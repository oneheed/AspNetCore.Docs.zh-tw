---
title: 在 Linux 上使用 Nginx 裝載 ASP.NET Core
author: rick-anderson
description: 瞭解如何在 Ubuntu 16.04 上將 Nginx 設定為反向 proxy，以將 HTTP 流量轉送至 Kestrel 上執行的 ASP.NET Core web 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 8a593654fa31e643e7c239f361f035589c75ce98
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395249"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>在 Linux 上使用 Nginx 裝載 ASP.NET Core

由 [Sourabh Shirhatti](https://twitter.com/sshirhatti) 提供

本指南說明在 Ubuntu 16.04 伺服器上設定生產環境就緒的 ASP.NET Core 環境。 這些指示可能使用較新版本的 Ubuntu，但未經測試。

如需有關 ASP.NET Core 支援之其他 Linux 發行版本的資訊，請參閱 [Linux 上 .NET Core 的必要條件](/dotnet/core/linux-prerequisites)。

> [!NOTE]
> 針對 Ubuntu 14.04， `supervisord` 建議使用此解決方案來監視 Kestrel 流程。 `systemd` 在 Ubuntu 14.04 上無法使用。 如需 Ubuntu 14.04 指示，請參閱[本主題前一版本](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)。

本指南：

* 將現有的 ASP.NET Core 應用程式放在反向 Proxy 伺服器後方。
* 設定反向 Proxy 伺服器以將要求轉送至 Kestrel 網頁伺服器。
* 確保 Web 應用程式在啟動時以精靈的形式執行。
* 設定程序管理工具以協助重新啟動 Web 應用程式。

## <a name="prerequisites"></a>必要條件

* 以 sudo 權限使用標準使用者帳戶存取 Ubuntu 16.04 伺服器。
* 安裝在伺服器上的最新非預覽版 [.net 運行](/dotnet/core/install/linux) 時間。
* 現有的 ASP.NET Core 應用程式。

在升級共用架構之後的任何時間點，請重新開機伺服器所裝載的 ASP.NET Core 應用程式。

## <a name="publish-and-copy-over-the-app"></a>跨應用程式發佈與複製

為[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)設定應用程式。

如果應用程式在本機執行且未設定為進行安全連線 (HTTPS)，請採用下列任一方法：

* 設定應用程式以處理安全的本機連線。 如需詳細資訊，請參閱 [HTTPS 組態](#https-configuration)一節。
* `https://localhost:5001`從檔案的屬性中移除 (（如果有) 的話） `applicationUrl` `Properties/launchSettings.json` 。

執行 [dotnet](/dotnet/core/tools/dotnet-publish) 從開發環境發佈，將應用程式封裝至目錄 (例如， `bin/Release/{TARGET FRAMEWORK MONIKER}/publish` 其中預留位置 `{TARGET FRAMEWORK MONIKER}` 是可在伺服器上執行的目標 Framework 標記/TFM) ：

```dotnetcli
dotnet publish --configuration Release
```

如果您不想在伺服器上維護 .NET Core 執行階段，應用程式也可以發佈為[獨立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)。

使用整合至組織工作流程 (的工具，將 ASP.NET Core 應用程式複製到伺服器，例如 `SCP` `SFTP`) 。 通常會在目錄底下尋找 web 應用程式 `var` (例如 `var/www/helloapp`) 。

> [!NOTE]
> 在生產環境部署案例中，持續整合工作流程會執行發佈應用程式並將資產複製到伺服器的工作。

測試應用程式：

1. 從命令列執行應用程式：`dotnet <app_assembly>.dll`。
1. 在瀏覽器中，巡覽至 `http://<serveraddress>:<port>` 以確認應用程式可在 Linux 本機上正常運作。

## <a name="configure-a-reverse-proxy-server"></a>設定反向 Proxy 伺服器

反向 Proxy 是為動態 Web 應用程式提供服務的常見設定。 反向 Proxy 會終止 HTTP 要求，並將它轉送至 ASP.NET Core 應用程式。

### <a name="use-a-reverse-proxy-server"></a>使用反向 Proxy 伺服器

Kestrel 非常適用於從 ASP.NET Core 提供動態內容。 不過，Web 服務功能不像 IIS、Apache 或 Nginx 這類伺服器那樣豐富。 反向 Proxy 伺服器可以讓 HTTP 伺服器卸下提供靜態內容、快取要求、壓縮要求及終止 HTTPS 等工作的負擔。 反向 Proxy 伺服器可能位在專用電腦上，或可能與 HTTP 伺服器一起部署。

為達到本指南的目的，使用 Nginx 的單一執行個體。 它會在相同的伺服器上和 HTTP 伺服器一起執行。 您可以根據需求，選擇不同的設定。

因為反向 proxy 會轉送要求，所以請使用封裝中 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer) [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) 。 此中介軟體會使用 `X-Forwarded-Proto` 標頭來更新 `Request.Scheme`，以便讓重新導向 URI 及其他安全性原則正確運作。

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A>在 `Startup.Configure` 呼叫其他中介軟體之前，先在頂端叫用方法。 請設定中介軟體來轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭：

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

如果未將任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 指定給中介軟體，則要轉送的預設標頭會是 `None`。

預設會信任在回送位址上執行的 proxy (`127.0.0.0/8` 、 `[::1]`) （包括標準 localhost 位址 (`127.0.0.1`) ）。 如果組織內有其他受信任的 Proxy 或網路處理網際網路與網頁伺服器之間的要求，請使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，將其新增至 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> 或 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> 清單。 下列範例會將 IP 位址 10.0.0.100 的受信任 Proxy 伺服器新增至 `Startup.ConfigureServices` 中「轉送的標頭中介軟體」的 `KnownProxies`：

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。

### <a name="install-nginx"></a>安裝 Nginx

使用 `apt-get` 來安裝 Nginx。 安裝程式會建立在 `systemd` 系統啟動時執行 Nginx 做為 daemon 的 init 腳本。 請遵循 [Nginx：Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx：官方 Debian/Ubuntu 套件) 中適用於 Ubuntu 的安裝指示。

> [!NOTE]
> 如果需要選用的 Nginx 模組，可能要從來源建置 Nginx。

因為 Nginx 是第一次安裝，請透過執行明確啟動它：

```bash
sudo service nginx start
```

確認瀏覽器會顯示 Nginx 的預設登陸頁面。 登陸頁面位於 `http://<server_IP_address>/index.nginx-debian.html`。

### <a name="configure-nginx"></a>設定 Nginx

若要將 Nginx 設定為反向 proxy，以將 HTTP 要求轉送至您的 ASP.NET Core 應用程式，請修改 `/etc/nginx/sites-available/default` 。 在文字編輯器中開啟它，並將內容取代為下列程式碼片段：

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

如果應用程式是 SignalR 或 Blazor Server 應用程式，請 <xref:signalr/scale#linux-with-nginx> 分別參閱和， <xref:blazor/host-and-deploy/server#linux-with-nginx> 以取得詳細資訊。

當沒有任何與 `server_name` 相符的項目時，Nginx 會使用預設伺服器。 如果未定義任何預設伺服器，則設定檔中的第一個伺服器就是預設伺服器。 最佳做法是在您的設定檔中新增會傳回狀態碼444的特定預設伺服器。 預設伺服器設定範例如下：

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

::: moniker range=">= aspnetcore-5.0"

使用上述設定檔和預設伺服器時，Nginx 會在連接埠 80 接受主機標頭為 `example.com` 或 `*.example.com` 的公用流量。 不符合這些主機的要求將不會轉送至 Kestrel。 Nginx 會將相符的要求轉送至位於 `http://localhost:5000` 的 Kestrel。 如需詳細資訊，請參閱 [nginx 處理要求的方式](https://nginx.org/docs/http/request_processing.html)。 若要變更 Kestrel 的 IP/連接埠，請參閱 [Kestrel：端點組態](xref:fundamentals/servers/kestrel/endpoints)。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

使用上述設定檔和預設伺服器時，Nginx 會在連接埠 80 接受主機標頭為 `example.com` 或 `*.example.com` 的公用流量。 不符合這些主機的要求將不會轉送至 Kestrel。 Nginx 會將相符的要求轉送至位於 `http://localhost:5000` 的 Kestrel。 如需詳細資訊，請參閱 [nginx 處理要求的方式](https://nginx.org/docs/http/request_processing.html)。 若要變更 Kestrel 的 IP/連接埠，請參閱 [Kestrel：端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration)。

::: moniker-end

> [!WARNING]
> 如果無法指定適當的 [server_name 指示詞](https://nginx.org/docs/http/server_names.html)，就會讓應用程式暴露在安全性弱點的風險下。 若您擁有整個父網域 (相對於易受攻擊的 `*.com`) 的控制權，子網域萬用字元繫結 (例如 `*.example.com`) 就沒有此安全性風險。 如需詳細資訊，請參閱 [rfc7230 章節-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)。

建立 Nginx 設定之後，請執行 `sudo nginx -t` 來確認設定檔的語法。 如果設定檔測試成功，請執行 `sudo nginx -s reload` 來強制 Nginx 套用這些變更。

直接在伺服器上執行應用程式：

1. 巡覽至應用程式目錄。
1. 執行應用程式：`dotnet <app_assembly.dll>`，其中 `app_assembly.dll` 是應用程式的組件檔名稱。

如果應用程式在伺服器上執行，但無法透過網際網路回應，請檢查伺服器的防火牆，並確認埠80已開啟。 如果使用的是 Azure Ubuntu VM，請新增啓用輸入連接埠 80 流量的網路安全性群組 (NSG) 規則。 沒有必要啟用輸出連接埠 80 規則，因為啓用輸入規則時會自動授與輸出流量。

完成測試應用程式時，請在命令提示字元下使用<kbd>Ctrl +</kbd>關閉應用程式  +  <kbd></kbd> 。

## <a name="monitor-the-app"></a>監視應用程式

伺服器會設定為將對開啟的要求轉寄至在 Kestrel 上執行 `http://<serveraddress>:80` 的 ASP.NET Core 應用程式 `http://127.0.0.1:5000` 。 不過，並未設定 Nginx 來管理 Kestrel 處理序。 `systemd` 可以用來建立服務檔案，以啟動並監視基礎 web 應用程式。 `systemd` 是 init 系統，提供許多強大的功能來啟動、停止及管理進程。 

### <a name="create-the-service-file"></a>建立服務檔

建立服務定義檔：

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

下列範例是應用程式的服務檔：

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

在上述範例中，管理服務的使用者是由選項所指定 `User` 。 使用者 (`www-data`) 必須存在，且擁有應用程式檔案的適當擁有權。

使用 `TimeoutStopSec` 可設定應用程式收到初始中斷訊號之後等待關閉的時間。 如果應用程式在此期間後未關閉，則會發出 SIGKILL 來終止應用程式。 提供不具單位的秒值 (例如 `150`)、時間範圍值 (例如 `2min 30s`) 或 `infinity` (表示停用逾時)。 `TimeoutStopSec` 預設為 `DefaultTimeoutStopSec` manager 設定檔中的值 `systemd-system.conf` ， (、 `system.conf.d` 、 `systemd-user.conf` `user.conf.d`) 。 大多數發行版本的預設逾時為 90 秒。

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux 的檔案系統會區分大小寫。 將設定 `ASPNETCORE_ENVIRONMENT` 為 `Production` 會導致搜尋設定檔 `appsettings.Production.json` ，而不是 `appsettings.production.json` 。

有些值 (例如 SQL 連接字串) 必須以逸出方式處理，設定提供者才能讀取環境變數。 請使用下列命令來產生要在設定檔中使用並已適當逸出的值：

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

環境變數名稱不支援冒號 (`:`) 分隔符號。 請使用雙底線 (`__`) 來取代冒號。 [環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables)會在將環境變數讀入組態時，將雙底線轉換為冒號。 在下列範例中，連接字串索引鍵 `ConnectionStrings:DefaultConnection` 會設定為服務定義檔中的 `ConnectionStrings__DefaultConnection`：

::: moniker-end
::: moniker range="< aspnetcore-3.0"

環境變數名稱不支援冒號 (`:`) 分隔符號。 請使用雙底線 (`__`) 來取代冒號。 [環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables-configuration-provider)會在將環境變數讀入組態時，將雙底線轉換為冒號。 在下列範例中，連接字串索引鍵 `ConnectionStrings:DefaultConnection` 會設定為服務定義檔中的 `ConnectionStrings__DefaultConnection`：

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

儲存檔案並啟用服務。

```bash
sudo systemctl enable kestrel-helloapp.service
```

啟動服務並確認它正在執行。

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

透過設定反向 proxy 並透過管理 Kestrel `systemd` ，web 應用程式就會完整設定，並可在本機電腦上的瀏覽器中存取 `http://localhost` 。 此外，也可以從遠端電腦存取它，除非遭到任何防火牆封鎖。 檢查回應標頭時，`Server` 標頭會顯示是由 Kestrel 為 ASP.NET Core 應用程式提供服務。

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>檢視記錄

由於是使用 `systemd` 來管理使用 Kestrel 的 Web 應用程式，因此會將所有事件和處理序都記錄在集中式日誌中。 不過，此日誌包含 `systemd` 管理的所有服務和處理程序的所有項目。 若要檢視 `kestrel-helloapp.service` 的特定項目，請使用下列命令：

```bash
sudo journalctl -fu kestrel-helloapp.service
```

如需進一步的篩選，時間選項（例如 `--since today` 、 `--until 1 hour ago` 或）的組合可以減少傳回的專案數。

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>資料保護

[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core[中介軟體](xref:fundamentals/middleware/index)所使用，包括驗證中介軟體 (例如， cookie 中介軟體) 和跨網站要求偽造 (CSRF) 保護。 即使資料保護 API 並非由使用者程式碼呼叫，仍應設定資料保護，以建立持續密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。 如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。

如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：

* 所有 cookie 的驗證權杖都會失效。
* 當使用者提出下一個要求時，需要再次登入。
* 所有以 Keyring 保護的資料都無法再解密。 這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie ](xref:fundamentals/app-state#tempdata)。

若要設定資料保護來保存及加密金鑰環，請參閱：

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>要求標頭欄位太長

Proxy 伺服器預設設定通常會將要求標頭欄位限制為 4 K 或 8 K （視平臺而定）。 應用程式可能需要比預設 (更長的欄位，例如使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)) 的應用程式。 如果需要較長的欄位，proxy 伺服器的預設設定需要進行調整。 要套用的值取決於案例。 如需詳細資訊，請參閱您的伺服器文件。

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> 除非必要，否則請勿增加 Proxy 緩衝區的預設值。 增加這些值會提高緩衝區溢位及惡意使用者發動拒絕服務 (DoS) 攻擊的風險。

## <a name="secure-the-app"></a>保護應用程式

### <a name="enable-apparmor"></a>啟用 AppArmor

Linux 安全性模組 (LSM) 是 Linux 2.6 之後 Linux 核心所包含的一個架構。 LSM 支援不同的安全性模組實作。 [AppArmor](https://wiki.ubuntu.com/AppArmor) 是一個 LSM，可執行強制存取控制系統，讓您將程式將到一組有限的資源。 確定已啟用並正確設定 AppArmor。

### <a name="configure-the-firewall"></a>設定防火牆

關閉所有未使用的外部埠。 簡單的防火牆 (ufw) 提供可設定防火牆的 CLI 來提供前端 `iptables` 。

> [!WARNING]
> 如未正確設定，防火牆會禁止存取整個系統。 未指定正確的 SSH 連接埠，將會導致您無法存取系統 (若您使用 SSH 連線至該連接埠)。 預設連接埠為 22。 如需詳細資訊，請參閱 [ufw 簡介](https://help.ubuntu.com/community/UFW)與[手冊](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)。

安裝 `ufw` 並將其設定為允許任何所需連接埠上的流量。

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>保護 Nginx

#### <a name="change-the-nginx-response-name"></a>變更 Nginx 回應名稱

編輯 `src/http/ngx_http_header_filter_module.c`：

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>設定選項

設定伺服器的其他所需模組。 請考慮使用 [ModSecurity](https://www.modsecurity.org/) 等 Web 應用程式防火牆來強化應用程式。

#### <a name="https-configuration"></a>HTTPS 設定

**設定應用程式以進行安全的本機連線 (HTTPS)**

[Dotnet run](/dotnet/core/tools/dotnet-run)命令使用應用程式的 *Properties/launchSettings.json* 檔案，其會將應用程式設定為在屬性提供的 url 上接聽 `applicationUrl` 。 例如： `https://localhost:5001;http://localhost:5000` 。

您可以使用下列其中一種方法，將應用程式設定為在命令或開發環境中使用憑證進行開發 `dotnet run` (<kbd>F5</kbd>或<kbd>Ctrl</kbd> + <kbd>F5</kbd>) ：

::: moniker range=">= aspnetcore-5.0"

* [取代組態中的預設憑證](xref:fundamentals/servers/kestrel/endpoints#configuration) (建議使用)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* [取代組態中的預設憑證](xref:fundamentals/servers/kestrel#configuration) (建議使用)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

**設定反向 Prooxy 以進行安全的用戶端連線 (HTTPS)**

* 藉由指定由受信任的憑證授權單位單位所簽發的有效憑證 (CA) ，將伺服器設定為接聽埠443上的 HTTPS 流量。

* 採用以下 */etc/nginx/nginx.conf* 檔案所述的一些做法來強化安全性。 範例包括選擇更強的加密，重新導向 HTTPS 到 HTTP 的所有流量。

  > [!NOTE]
  > 針對開發環境，我們建議使用暫時性重新導向 (302) 而不是永久重新導向 (301) 。 連結快取可能會在開發環境中造成不穩定的行為。

* 新增 `HTTP Strict-Transport-Security` (HSTS) 標頭可確保用戶端提出的所有後續要求都會透過 HTTPS。

  如需 HSTS 的重要指導方針，請參閱 <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts> 。

* 如果未來將停用 HTTPS，請使用下列其中一種方法：

  * 請勿新增 HSTS 標頭。
  * 選擇簡短 `max-age` 值。

新增 */etc/nginx/Proxy.conf* 組態檔：

[!code-nginx[](linux-nginx/proxy.conf)]

將 */etc/nginx/nginx.conf* 設定檔的內容 **取代** 為下列檔案。 此範例在一個組態檔中同時包含 `http` 和 `server` 區段。

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> Blazor WebAssembly 應用程式需要較大的 `burst` 參數值，以容納應用程式所提出的大量要求。 如需詳細資訊，請參閱<xref:blazor/host-and-deploy/webassembly#nginx>。

#### <a name="secure-nginx-from-clickjacking"></a>保護 Nginx 免於點閱綁架

[點閱綁架](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)(也稱為「UI 偽裝攻擊」) 是一種惡意攻擊，會誘騙網站訪客點選與其目前所瀏覽頁面不同的頁面上連結或按鈕。 請使用 `X-FRAME-OPTIONS` 來保護網站安全。

減輕點擊劫持攻擊：

1. 編輯 *nginx.conf* 檔案：

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   新增行： `add_header X-Frame-Options "SAMEORIGIN";`

1. 儲存檔案。
1. 重新啟動 Nginx。

#### <a name="mime-type-sniffing"></a>MIME 類型探查

此標頭會防止大部分的瀏覽器對遠離宣告內容類型的回應進行 MIME 探查，因為標頭會指示瀏覽器不要覆寫回應內容類型。 使用 `nosniff` 選項時，如果伺服器顯示的內容是 `text/html` ，則瀏覽器會將它轉譯為 `text/html` 。

1. 編輯 *nginx.conf* 檔案：

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   新增行： `add_header X-Content-Type-Options "nosniff";`

1. 儲存檔案。
1. 重新啟動 Nginx。

## <a name="additional-nginx-suggestions"></a>其他 Nginx 建議

在伺服器上升級共用架構之後，請重新開機伺服器所裝載的 ASP.NET Core 應用程式。

## <a name="additional-resources"></a>其他資源

* [Linux 上 .NET Core 的必要條件](/dotnet/core/linux-prerequisites)
* [Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx：二進位版本：正式的 Debian Ubuntu 套件)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX：使用轉送的標頭](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
