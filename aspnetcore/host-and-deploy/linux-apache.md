---
title: 在 Linux 上使用 Apache 裝載 ASP.NET Core
author: rick-anderson
description: 了解如何在 CentOS 上將 Apache 設定為反向 Proxy 伺服器，以將 HTTP 流量重新導向至在 Kestrel 上執行的 ASP.NET Core Web 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: f7d47e26b429f31817b5e04f3104449c9748d94f
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711278"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="aad11-103">在 Linux 上使用 Apache 裝載 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aad11-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="aad11-104">作者：[Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="aad11-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="aad11-105">使用本指南來了解如何在 [CentOS 7](https://www.centos.org/) 上將 [Apache](https://httpd.apache.org/) 設定為反向 Proxy 伺服器，以將 HTTP 流量重新導向至在 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器上執行的 ASP.NET Core Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="aad11-106">[mod_proxy 延伸模組](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html)和相關的模組會建立伺服器的反向 Proxy。</span><span class="sxs-lookup"><span data-stu-id="aad11-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aad11-107">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="aad11-107">Prerequisites</span></span>

* <span data-ttu-id="aad11-108">執行 CentOS 7 的伺服器搭配具有 sudo 權限的標準使用者帳戶。</span><span class="sxs-lookup"><span data-stu-id="aad11-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="aad11-109">在伺服器上安裝 .NET Core 執行階段。</span><span class="sxs-lookup"><span data-stu-id="aad11-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="aad11-110">流覽 [ [下載 .Net Core] 頁面](https://dotnet.microsoft.com/download/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="aad11-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="aad11-111">選取最新的非預覽 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="aad11-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="aad11-112">在 [ **執行應用程式-運行** 時間] 下的表格中下載最新的非預覽執行時間。</span><span class="sxs-lookup"><span data-stu-id="aad11-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="aad11-113">選取 [Linux **套件管理員指示** ] 連結，並遵循 CentOS 指示。</span><span class="sxs-lookup"><span data-stu-id="aad11-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="aad11-114">現有的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="aad11-115">在升級共用架構之後的任何時間點，重新開機伺服器所裝載的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="aad11-116">跨應用程式發佈與複製</span><span class="sxs-lookup"><span data-stu-id="aad11-116">Publish and copy over the app</span></span>

<span data-ttu-id="aad11-117">為[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)設定應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="aad11-118">如果應用程式在本機執行且未設定為進行安全連線 (HTTPS)，請採用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="aad11-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="aad11-119">設定應用程式以處理安全的本機連線。</span><span class="sxs-lookup"><span data-stu-id="aad11-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="aad11-120">如需詳細資訊，請參閱 [HTTPS 組態](#https-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="aad11-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="aad11-121">從 *Properties/launchSettings.json* 檔案中的 `applicationUrl` 屬性移除 `https://localhost:5001` (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="aad11-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="aad11-122">從開發環境執行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 將應用程式封裝到可在伺服器上執行的目錄 (例如，*bin/Release/&lt;target_framework_moniker&gt;/publish*)：</span><span class="sxs-lookup"><span data-stu-id="aad11-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="aad11-123">如果您不想在伺服器上維護 .NET Core 執行階段，應用程式也可以發佈為[獨立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)。</span><span class="sxs-lookup"><span data-stu-id="aad11-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="aad11-124">使用整合至組織工作流程的工具 (SCP、SFTP 等等) 將 ASP.NET Core 應用程式複製到伺服器。</span><span class="sxs-lookup"><span data-stu-id="aad11-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="aad11-125">Web 應用程式通常可在 *var* 目錄下找到 (例如 *var/www/helloapp*)。</span><span class="sxs-lookup"><span data-stu-id="aad11-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="aad11-126">在生產環境部署案例中，持續整合工作流程會執行發佈應用程式並將資產複製到伺服器的工作。</span><span class="sxs-lookup"><span data-stu-id="aad11-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="aad11-127">設定 Proxy 伺服器</span><span class="sxs-lookup"><span data-stu-id="aad11-127">Configure a proxy server</span></span>

<span data-ttu-id="aad11-128">反向 Proxy 是為動態 Web 應用程式提供服務的常見設定。</span><span class="sxs-lookup"><span data-stu-id="aad11-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="aad11-129">反向 Proxy 會終止 HTTP 要求，並將它轉送至 ASP.NET 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="aad11-130">Proxy 伺服器會將用戶端要求轉送至另一部伺服器，而不是自行履行要求。</span><span class="sxs-lookup"><span data-stu-id="aad11-130">A proxy server forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="aad11-131">反向 Proxy 會轉送至固定目的地，通常代表任意的用戶端。</span><span class="sxs-lookup"><span data-stu-id="aad11-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="aad11-132">在本指南中，是將 Apache 設定成反向 Proxy，且執行所在的伺服器與 Kestrel 為 ASP.NET Core 應用程式提供服務的伺服器相同。</span><span class="sxs-lookup"><span data-stu-id="aad11-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="aad11-133">因為反向 proxy 會轉送要求，所以請使用來自[AspNetCore. >microsoft.aspnetcore.HTTPoverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/)封裝的[轉送標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="aad11-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="aad11-134">此中介軟體會使用 `X-Forwarded-Proto` 標頭來更新 `Request.Scheme`，以便讓重新導向 URI 及其他安全性原則正確運作。</span><span class="sxs-lookup"><span data-stu-id="aad11-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="aad11-135">任何依賴配置的元件，例如驗證、連結產生、重新導向和地理位置，都必須在叫用轉送的標頭中介軟體後放置。</span><span class="sxs-lookup"><span data-stu-id="aad11-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="aad11-136"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A>在 `Startup.Configure` 呼叫其他中介軟體之前，先在頂端叫用方法。</span><span class="sxs-lookup"><span data-stu-id="aad11-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="aad11-137">請設定中介軟體來轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭：</span><span class="sxs-lookup"><span data-stu-id="aad11-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="aad11-138">如果未將任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 指定給中介軟體，則要轉送的預設標頭會是 `None`。</span><span class="sxs-lookup"><span data-stu-id="aad11-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="aad11-139">在回送位址 () 上執行的 proxy `127.0.0.0/8, [::1]` （包括標準 localhost 位址 (127.0.0.1) ）預設為受信任。</span><span class="sxs-lookup"><span data-stu-id="aad11-139">Proxies running on loopback addresses (`127.0.0.0/8, [::1]`), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="aad11-140">如果組織內有其他受信任的 Proxy 或網路處理網際網路與網頁伺服器之間的要求，請使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，將其新增至 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> 或 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> 清單。</span><span class="sxs-lookup"><span data-stu-id="aad11-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="aad11-141">下列範例會將 IP 位址 10.0.0.100 的受信任 Proxy 伺服器新增至 `Startup.ConfigureServices` 中「轉送的標頭中介軟體」的 `KnownProxies`：</span><span class="sxs-lookup"><span data-stu-id="aad11-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="aad11-142">如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="aad11-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="aad11-143">安裝 Apache</span><span class="sxs-lookup"><span data-stu-id="aad11-143">Install Apache</span></span>

<span data-ttu-id="aad11-144">將 CentOS 套件更新至其最新穩定版本：</span><span class="sxs-lookup"><span data-stu-id="aad11-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="aad11-145">使用單一 `yum` 命令在 CentOS 上安裝 Apache 網頁伺服器：</span><span class="sxs-lookup"><span data-stu-id="aad11-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="aad11-146">執行命令之後的範例輸出：</span><span class="sxs-lookup"><span data-stu-id="aad11-146">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="aad11-147">在此範例中，輸出會反映 httpd.86_64，因為 CentOS 第 7 版是 64 位元。</span><span class="sxs-lookup"><span data-stu-id="aad11-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="aad11-148">若要確認 Apache 的安裝位置，請從命令提示字元執行 `whereis httpd`。</span><span class="sxs-lookup"><span data-stu-id="aad11-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="aad11-149">設定 Apache</span><span class="sxs-lookup"><span data-stu-id="aad11-149">Configure Apache</span></span>

<span data-ttu-id="aad11-150">Apache 的組態檔是位於 `/etc/httpd/conf.d/` 目錄內。</span><span class="sxs-lookup"><span data-stu-id="aad11-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="aad11-151">除了 `/etc/httpd/conf.modules.d/` (包含載入模組所需的所有設定檔) 中的模組設定檔之外，任何副檔名為 *.conf* 的檔案也會依字母順序處理。</span><span class="sxs-lookup"><span data-stu-id="aad11-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="aad11-152">為應用程式建立名為 *helloapp.conf* 的設定檔：</span><span class="sxs-lookup"><span data-stu-id="aad11-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="aad11-153">`VirtualHost` 區塊可以在伺服器上的一或多個檔案中出現多次。</span><span class="sxs-lookup"><span data-stu-id="aad11-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="aad11-154">在上述設定檔中，Apache 會在連接埠 80 接受公用流量。</span><span class="sxs-lookup"><span data-stu-id="aad11-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="aad11-155">所服務的網域是 `www.example.com`，而 `*.example.com` 別名則會解析成同一個網站。</span><span class="sxs-lookup"><span data-stu-id="aad11-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="aad11-156">如需詳細資訊，請參閱以 [名稱為基礎的虛擬主機支援](https://httpd.apache.org/docs/current/vhosts/name-based.html)。</span><span class="sxs-lookup"><span data-stu-id="aad11-156">For more information, see [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span></span> <span data-ttu-id="aad11-157">要求會在根目錄透過 Proxy 傳送至位於 127.0.0.1 之伺服器的連接埠 5000。</span><span class="sxs-lookup"><span data-stu-id="aad11-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="aad11-158">如需進行雙向通訊，則必須要有 `ProxyPass` 和 `ProxyPassReverse`。</span><span class="sxs-lookup"><span data-stu-id="aad11-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="aad11-159">若要變更 Kestrel 的 IP/連接埠，請參閱 [Kestrel：端點組態](xref:fundamentals/servers/kestrel/endpoints)。</span><span class="sxs-lookup"><span data-stu-id="aad11-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="aad11-160">`VirtualHost` 區塊可以在伺服器上的一或多個檔案中出現多次。</span><span class="sxs-lookup"><span data-stu-id="aad11-160">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="aad11-161">在上述設定檔中，Apache 會在連接埠 80 接受公用流量。</span><span class="sxs-lookup"><span data-stu-id="aad11-161">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="aad11-162">所服務的網域是 `www.example.com`，而 `*.example.com` 別名則會解析成同一個網站。</span><span class="sxs-lookup"><span data-stu-id="aad11-162">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="aad11-163">如需詳細資訊，請參閱以 [名稱為基礎的虛擬主機支援](https://httpd.apache.org/docs/current/vhosts/name-based.html)。</span><span class="sxs-lookup"><span data-stu-id="aad11-163">For more information, see [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span></span> <span data-ttu-id="aad11-164">要求會在根目錄透過 Proxy 傳送至位於 127.0.0.1 之伺服器的連接埠 5000。</span><span class="sxs-lookup"><span data-stu-id="aad11-164">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="aad11-165">如需進行雙向通訊，則必須要有 `ProxyPass` 和 `ProxyPassReverse`。</span><span class="sxs-lookup"><span data-stu-id="aad11-165">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="aad11-166">若要變更 Kestrel 的 IP/連接埠，請參閱 [Kestrel：端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="aad11-166">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="aad11-167">如果無法在 **VirtualHost** 區塊中指定適當的 [ServerName 指示詞](https://httpd.apache.org/docs/current/mod/core.html#servername)，就會讓應用程式暴露在安全性弱點的風險下。</span><span class="sxs-lookup"><span data-stu-id="aad11-167">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="aad11-168">若您擁有整個父網域 (相對於易受攻擊的 `*.com`) 的控制權，子網域萬用字元繫結 (例如 `*.example.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="aad11-168">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="aad11-169">如需詳細資訊，請參閱 [rfc7230 章節-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="aad11-169">For more information, see [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

<span data-ttu-id="aad11-170">您可以使用 `ErrorLog` 和 `CustomLog` 指示詞來依 `VirtualHost` 設定記錄功能。</span><span class="sxs-lookup"><span data-stu-id="aad11-170">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="aad11-171">`ErrorLog` 是伺服器記錄錯誤的位置，而 `CustomLog` 則會設定記錄檔的檔案名稱和格式。</span><span class="sxs-lookup"><span data-stu-id="aad11-171">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="aad11-172">在此案例中，這就是記錄要求資訊的位置。</span><span class="sxs-lookup"><span data-stu-id="aad11-172">In this case, this is where request information is logged.</span></span> <span data-ttu-id="aad11-173">每個要求都會有一行。</span><span class="sxs-lookup"><span data-stu-id="aad11-173">There's one line for each request.</span></span>

<span data-ttu-id="aad11-174">請儲存檔案並測試設定。</span><span class="sxs-lookup"><span data-stu-id="aad11-174">Save the file and test the configuration.</span></span> <span data-ttu-id="aad11-175">如果所有項目都通過，回應應該是 `Syntax [OK]`。</span><span class="sxs-lookup"><span data-stu-id="aad11-175">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="aad11-176">重新啟動 Apache：</span><span class="sxs-lookup"><span data-stu-id="aad11-176">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="aad11-177">監視應用程式</span><span class="sxs-lookup"><span data-stu-id="aad11-177">Monitor the app</span></span>

<span data-ttu-id="aad11-178">Apache 現在已設定為將要求轉送至在 `http://localhost:80` Kestrel 上執行的 ASP.NET Core 應用程式 `http://127.0.0.1:5000` 。</span><span class="sxs-lookup"><span data-stu-id="aad11-178">Apache is now set up to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="aad11-179">不過，並未設定 Apache 來管理 Kestrel 處理序。</span><span class="sxs-lookup"><span data-stu-id="aad11-179">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="aad11-180">請使用 *systemd* 並建立服務檔案，以啟動並監視基礎 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-180">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="aad11-181">*systemd* 是一種 init 系統，提供許多強大的功能來啟動、停止及管理進程。</span><span class="sxs-lookup"><span data-stu-id="aad11-181">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="aad11-182">建立服務檔</span><span class="sxs-lookup"><span data-stu-id="aad11-182">Create the service file</span></span>

<span data-ttu-id="aad11-183">建立服務定義檔：</span><span class="sxs-lookup"><span data-stu-id="aad11-183">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="aad11-184">應用程式的範例服務檔：</span><span class="sxs-lookup"><span data-stu-id="aad11-184">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="aad11-185">在上述範例中，管理服務的使用者是由選項所指定 `User` 。</span><span class="sxs-lookup"><span data-stu-id="aad11-185">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="aad11-186">使用者 (`apache`) 必須存在，且擁有應用程式檔案的適當擁有權。</span><span class="sxs-lookup"><span data-stu-id="aad11-186">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="aad11-187">使用 `TimeoutStopSec` 可設定應用程式收到初始中斷訊號之後等待關閉的時間。</span><span class="sxs-lookup"><span data-stu-id="aad11-187">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="aad11-188">如果應用程式在此期間後未關閉，則會發出 SIGKILL 來終止應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-188">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="aad11-189">提供不具單位的秒值 (例如 `150`)、時間範圍值 (例如 `2min 30s`) 或 `infinity` (表示停用逾時)。</span><span class="sxs-lookup"><span data-stu-id="aad11-189">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="aad11-190">`TimeoutStopSec` 在管理員設定檔 (*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*) 的預設值為 `DefaultTimeoutStopSec`。</span><span class="sxs-lookup"><span data-stu-id="aad11-190">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="aad11-191">大多數發行版本的預設逾時為 90 秒。</span><span class="sxs-lookup"><span data-stu-id="aad11-191">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="aad11-192">有些值 (例如 SQL 連接字串) 必須以逸出方式處理，設定提供者才能讀取環境變數。</span><span class="sxs-lookup"><span data-stu-id="aad11-192">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="aad11-193">請使用下列命令來產生要在設定檔中使用並已適當逸出的值：</span><span class="sxs-lookup"><span data-stu-id="aad11-193">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aad11-194">環境變數名稱不支援冒號 (`:`) 分隔符號。</span><span class="sxs-lookup"><span data-stu-id="aad11-194">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="aad11-195">請使用雙底線 (`__`) 來取代冒號。</span><span class="sxs-lookup"><span data-stu-id="aad11-195">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="aad11-196">[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables-configuration-provider)會在將環境變數讀入組態時，將雙底線轉換為冒號。</span><span class="sxs-lookup"><span data-stu-id="aad11-196">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="aad11-197">在下列範例中，連接字串索引鍵 `ConnectionStrings:DefaultConnection` 會設定為服務定義檔中的 `ConnectionStrings__DefaultConnection`：</span><span class="sxs-lookup"><span data-stu-id="aad11-197">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aad11-198">環境變數名稱不支援冒號 (`:`) 分隔符號。</span><span class="sxs-lookup"><span data-stu-id="aad11-198">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="aad11-199">請使用雙底線 (`__`) 來取代冒號。</span><span class="sxs-lookup"><span data-stu-id="aad11-199">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="aad11-200">[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables)會在將環境變數讀入組態時，將雙底線轉換為冒號。</span><span class="sxs-lookup"><span data-stu-id="aad11-200">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="aad11-201">在下列範例中，連接字串索引鍵 `ConnectionStrings:DefaultConnection` 會設定為服務定義檔中的 `ConnectionStrings__DefaultConnection`：</span><span class="sxs-lookup"><span data-stu-id="aad11-201">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="aad11-202">儲存檔案並啟用服務：</span><span class="sxs-lookup"><span data-stu-id="aad11-202">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="aad11-203">啟動服務並確認它正在執行：</span><span class="sxs-lookup"><span data-stu-id="aad11-203">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="aad11-204">設定好反向 Proxy 並透過 *systemd* 管理 Kestrel 之後，Web 應用程式便已完全設定妥當，而從本機電腦瀏覽器透過 `http://localhost` 即可存取它。</span><span class="sxs-lookup"><span data-stu-id="aad11-204">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="aad11-205">檢查回應標頭時，**Server** 標頭會指出是由 Kestrel 為 ASP.NET Core 應用程式提供服務：</span><span class="sxs-lookup"><span data-stu-id="aad11-205">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="aad11-206">檢視記錄</span><span class="sxs-lookup"><span data-stu-id="aad11-206">View logs</span></span>

<span data-ttu-id="aad11-207">由於是使用 *systemd* 來管理使用 Kestrel 的 Web 應用程式，因此會將事件和處理序都記錄在集中式日誌中。</span><span class="sxs-lookup"><span data-stu-id="aad11-207">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="aad11-208">不過，此日誌包含 *systemd* 所管理全部服務和處理序的項目。</span><span class="sxs-lookup"><span data-stu-id="aad11-208">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="aad11-209">若要檢視 `kestrel-helloapp.service` 的特定項目，請使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="aad11-209">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="aad11-210">如需進行時間篩選，請搭配命令指定時間選項。</span><span class="sxs-lookup"><span data-stu-id="aad11-210">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="aad11-211">例如，使用 `--since today` 來針對今天進行篩選，或使用 `--until 1 hour ago` 來查看前一個小時的項目。</span><span class="sxs-lookup"><span data-stu-id="aad11-211">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="aad11-212">如需詳細資訊，請參閱 [journalctl 的手冊頁面](https://www.unix.com/man-page/centos/1/journalctl/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="aad11-212">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="aad11-213">資料保護</span><span class="sxs-lookup"><span data-stu-id="aad11-213">Data protection</span></span>

<span data-ttu-id="aad11-214">[ASP.NET Core 的資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core[中介軟體](xref:fundamentals/middleware/index)所使用，包括驗證中介軟體 (例如， cookie 中介軟體) 和跨網站偽造要求 (CSRF) 保護。</span><span class="sxs-lookup"><span data-stu-id="aad11-214">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="aad11-215">即使資料保護 API 並非由使用者程式碼呼叫，仍應設定資料保護，以建立持續密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="aad11-215">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="aad11-216">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="aad11-216">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="aad11-217">如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：</span><span class="sxs-lookup"><span data-stu-id="aad11-217">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="aad11-218">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="aad11-218">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="aad11-219">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="aad11-219">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="aad11-220">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="aad11-220">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="aad11-221">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="aad11-221">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="aad11-222">若要設定資料保護來保存及加密金鑰環，請參閱：</span><span class="sxs-lookup"><span data-stu-id="aad11-222">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="aad11-223">保護應用程式</span><span class="sxs-lookup"><span data-stu-id="aad11-223">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="aad11-224">設定防火牆</span><span class="sxs-lookup"><span data-stu-id="aad11-224">Configure firewall</span></span>

<span data-ttu-id="aad11-225">*Firewalld* 是一個可管理防火牆並支援網路區域的動態精靈。</span><span class="sxs-lookup"><span data-stu-id="aad11-225">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="aad11-226">您仍然可以使用 iptables 來管理連接埠和封包篩選。</span><span class="sxs-lookup"><span data-stu-id="aad11-226">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="aad11-227">預設應該安裝 *Firewalld*。</span><span class="sxs-lookup"><span data-stu-id="aad11-227">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="aad11-228">您可以使用 `yum` 來安裝套件或確認是否已安裝套件。</span><span class="sxs-lookup"><span data-stu-id="aad11-228">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="aad11-229">請使用 `firewalld` 來僅開啟應用程式所需的連接埠。</span><span class="sxs-lookup"><span data-stu-id="aad11-229">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="aad11-230">在此情況下，會使用埠80和443。</span><span class="sxs-lookup"><span data-stu-id="aad11-230">In this case, ports 80 and 443 are used.</span></span> <span data-ttu-id="aad11-231">下列命令會將連接埠 80 和 443 永久設定為開啟：</span><span class="sxs-lookup"><span data-stu-id="aad11-231">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="aad11-232">重新載入防火牆設定。</span><span class="sxs-lookup"><span data-stu-id="aad11-232">Reload the firewall settings.</span></span> <span data-ttu-id="aad11-233">檢查預設區域中可用的服務和連接埠。</span><span class="sxs-lookup"><span data-stu-id="aad11-233">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="aad11-234">您可以檢查 `firewall-cmd -h` 來取得選項。</span><span class="sxs-lookup"><span data-stu-id="aad11-234">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="aad11-235">HTTPS 設定</span><span class="sxs-lookup"><span data-stu-id="aad11-235">HTTPS configuration</span></span>

<span data-ttu-id="aad11-236">**設定應用程式以進行安全的本機連線 (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="aad11-236">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="aad11-237">[dotnet run](/dotnet/core/tools/dotnet-run) 命令使用應用程式的 *Properties/launchSettings.json* 檔案，其設定應用程式在 `applicationUrl` 屬性所提供的 URL 上接聽 (例如 `https://localhost:5001;http://localhost:5000`)。</span><span class="sxs-lookup"><span data-stu-id="aad11-237">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="aad11-238">使用下列其中一種方法，設定應用程式將憑證用在針對 `dotnet run` 命令的開發，或用在開發環境 (F5，若在 Visual Studio Code 中則為 Ctrl+F5)：</span><span class="sxs-lookup"><span data-stu-id="aad11-238">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="aad11-239">[取代組態中的預設憑證](xref:fundamentals/servers/kestrel/endpoints#configuration) (建議使用)</span><span class="sxs-lookup"><span data-stu-id="aad11-239">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="aad11-240">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="aad11-240">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="aad11-241">[取代組態中的預設憑證](xref:fundamentals/servers/kestrel#configuration) (建議使用)</span><span class="sxs-lookup"><span data-stu-id="aad11-241">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="aad11-242">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="aad11-242">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

<span data-ttu-id="aad11-243">**設定反向 Prooxy 以進行安全的用戶端連線 (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="aad11-243">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

> [!WARNING]
> <span data-ttu-id="aad11-244">本節中的安全性設定是一般設定，可作為進一步自訂的起點。</span><span class="sxs-lookup"><span data-stu-id="aad11-244">The security configuration in this section is a general configuration to be used as a starting point for further customization.</span></span> <span data-ttu-id="aad11-245">我們無法提供協力廠商工具、伺服器和作業系統的支援。</span><span class="sxs-lookup"><span data-stu-id="aad11-245">We're unable to provide support for third-party tooling, servers, and operating systems.</span></span> <span data-ttu-id="aad11-246">*使用本節中的設定，以您自己的風險。*</span><span class="sxs-lookup"><span data-stu-id="aad11-246">*Use the configuration in this section at your own risk.*</span></span> <span data-ttu-id="aad11-247">如需詳細資訊，請存取下列資源：</span><span class="sxs-lookup"><span data-stu-id="aad11-247">For more information, access the following resources:</span></span>
>
* <span data-ttu-id="aad11-248">Apache 檔) 的[APACHE SSL/TLS 加密](https://httpd.apache.org/docs/trunk/ssl/) (</span><span class="sxs-lookup"><span data-stu-id="aad11-248">[Apache SSL/TLS Encryption](https://httpd.apache.org/docs/trunk/ssl/) (Apache documentation)</span></span>
* [<span data-ttu-id="aad11-249">mozilla.org SSL 設定產生器</span><span class="sxs-lookup"><span data-stu-id="aad11-249">mozilla.org SSL Configuration Generator</span></span>](https://ssl-config.mozilla.org/#server=apache)

<span data-ttu-id="aad11-250">為了設定適用於 HTTPS 的 Apache，會使用 *mod_ssl* 模組。</span><span class="sxs-lookup"><span data-stu-id="aad11-250">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="aad11-251">安裝 *httpd* 模組時，已一併安裝 *mod_ssl* 模組。</span><span class="sxs-lookup"><span data-stu-id="aad11-251">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="aad11-252">如果未安裝該模組，請使用 `yum` 將它新增到設定中。</span><span class="sxs-lookup"><span data-stu-id="aad11-252">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="aad11-253">若要強制執行 HTTPS，請安裝 `mod_rewrite` 模組來啟用 URL 重寫：</span><span class="sxs-lookup"><span data-stu-id="aad11-253">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="aad11-254">修改 *helloapp* 檔案，以在埠443上啟用安全通訊。</span><span class="sxs-lookup"><span data-stu-id="aad11-254">Modify the *helloapp.conf* file to enable secure communication on port 443.</span></span>

<span data-ttu-id="aad11-255">下列範例不會將伺服器設定為重新導向不安全的要求。</span><span class="sxs-lookup"><span data-stu-id="aad11-255">The following example doesn't configure the server to redirect insecure requests.</span></span> <span data-ttu-id="aad11-256">建議使用 HTTPS 重新導向中介軟體。</span><span class="sxs-lookup"><span data-stu-id="aad11-256">We recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="aad11-257">如需詳細資訊，請參閱<xref:security/enforcing-ssl>。</span><span class="sxs-lookup"><span data-stu-id="aad11-257">For more information, see <xref:security/enforcing-ssl>.</span></span>

> [!NOTE]
> <span data-ttu-id="aad11-258">針對伺服器設定處理安全重新導向而非 HTTPS 重新導向中介軟體的開發環境，我們建議使用暫時性重新導向 (302) ，而不是永久重新導向 (301) 。</span><span class="sxs-lookup"><span data-stu-id="aad11-258">For development environments where the server configuration handles secure redirection instead of HTTPS Redirection Middleware, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="aad11-259">連結快取可能會在開發環境中造成不穩定的行為。</span><span class="sxs-lookup"><span data-stu-id="aad11-259">Link caching can cause unstable behavior in development environments.</span></span>

<span data-ttu-id="aad11-260">新增 `Strict-Transport-Security` (HSTS) 標頭可確保用戶端提出的所有後續要求都是透過 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="aad11-260">Adding a `Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span> <span data-ttu-id="aad11-261">如需設定 `Strict-Transport-Security` 標頭的指引，請參閱 <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts> 。</span><span class="sxs-lookup"><span data-stu-id="aad11-261">For guidance on setting the `Strict-Transport-Security` header, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:443>
    Protocols             h2 http/1.1
    ProxyPreserveHost     On
    ProxyPass             / http://127.0.0.1:5000/
    ProxyPassReverse      / http://127.0.0.1:5000/
    ErrorLog              /var/log/httpd/helloapp-error.log
    CustomLog             /var/log/httpd/helloapp-access.log common
    SSLEngine             on
    SSLProtocol           all -SSLv3 -TLSv1 -TLSv1.1
    SSLHonorCipherOrder   off
    SSLCompression        off
    SSLSessionTickets     on
    SSLUseStapling        off
    SSLCertificateFile    /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    SSLCipherSuite        ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="aad11-262">此範例使用本機產生的憑證。</span><span class="sxs-lookup"><span data-stu-id="aad11-262">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="aad11-263">**SSLCertificateFile** 應該是網域名稱的主要憑證檔案。</span><span class="sxs-lookup"><span data-stu-id="aad11-263">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="aad11-264">**SSLCertificateKeyFile** 應該是建立 CSR 時產生的金鑰檔。</span><span class="sxs-lookup"><span data-stu-id="aad11-264">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="aad11-265">**SSLCertificateChainFile** 應該是憑證授權單位所提供的中繼憑證檔案 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="aad11-265">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>
>
> <span data-ttu-id="aad11-266">需要有 Apache HTTP 伺服器版本2.4.43 或更新版本，才能使用 OpenSSL 1.1.1 來操作 TLS 1.3 web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="aad11-266">Apache HTTP Server version 2.4.43 or newer is required in order to operate a TLS 1.3 web server with OpenSSL 1.1.1.</span></span>

> <span data-ttu-id="aad11-267">記上述範例會停用 (OCSP) 裝訂的線上憑證狀態通訊協定。</span><span class="sxs-lookup"><span data-stu-id="aad11-267">[NOTE] The preceding example disables Online Certificate Status Protocol (OCSP) Stapling.</span></span> <span data-ttu-id="aad11-268">如需啟用 OCSP 的詳細資訊和指引，請參閱 [ (Apache 檔) 的 Ocsp 裝訂 ](https://httpd.apache.org/docs/trunk/ssl/ssl_howto.html#ocspstapling)。</span><span class="sxs-lookup"><span data-stu-id="aad11-268">For more information and guidance on enabling OCSP, see [OCSP Stapling (Apache documentation)](https://httpd.apache.org/docs/trunk/ssl/ssl_howto.html#ocspstapling).</span></span>

<span data-ttu-id="aad11-269">儲存檔案並測試設定：</span><span class="sxs-lookup"><span data-stu-id="aad11-269">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="aad11-270">重新啟動 Apache：</span><span class="sxs-lookup"><span data-stu-id="aad11-270">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="aad11-271">Apache 的其他建議</span><span class="sxs-lookup"><span data-stu-id="aad11-271">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="aad11-272">使用共用架構更新重新開機應用程式</span><span class="sxs-lookup"><span data-stu-id="aad11-272">Restart apps with shared framework updates</span></span>

<span data-ttu-id="aad11-273">在伺服器上升級共用架構之後，請重新開機伺服器所裝載的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-273">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="aad11-274">其他標頭</span><span class="sxs-lookup"><span data-stu-id="aad11-274">Additional headers</span></span>

<span data-ttu-id="aad11-275">為了保護免于惡意攻擊，有幾個標頭應該修改或新增。</span><span class="sxs-lookup"><span data-stu-id="aad11-275">To secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="aad11-276">請確認已安裝 `mod_headers` 模組：</span><span class="sxs-lookup"><span data-stu-id="aad11-276">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="aad11-277">保護 Apache 免於點閱綁架攻擊</span><span class="sxs-lookup"><span data-stu-id="aad11-277">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="aad11-278">[點閱綁架](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)(也稱為「UI 偽裝攻擊」) 是一種惡意攻擊，會誘騙網站訪客點選與其目前所瀏覽頁面不同的頁面上連結或按鈕。</span><span class="sxs-lookup"><span data-stu-id="aad11-278">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="aad11-279">請使用 `X-FRAME-OPTIONS` 來保護網站安全。</span><span class="sxs-lookup"><span data-stu-id="aad11-279">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="aad11-280">減輕點擊劫持攻擊：</span><span class="sxs-lookup"><span data-stu-id="aad11-280">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="aad11-281">編輯 *httpd.conf* 檔案：</span><span class="sxs-lookup"><span data-stu-id="aad11-281">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="aad11-282">新增 `Header append X-FRAME-OPTIONS "SAMEORIGIN"` 行。</span><span class="sxs-lookup"><span data-stu-id="aad11-282">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="aad11-283">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="aad11-283">Save the file.</span></span>
1. <span data-ttu-id="aad11-284">重新啟動 Apache。</span><span class="sxs-lookup"><span data-stu-id="aad11-284">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="aad11-285">MIME 類型探查</span><span class="sxs-lookup"><span data-stu-id="aad11-285">MIME-type sniffing</span></span>

<span data-ttu-id="aad11-286">`X-Content-Type-Options` 標頭可防止 Internet Explorer 執行「MIME 探查」 (從檔案的內容判斷檔案的 `Content-Type`)。</span><span class="sxs-lookup"><span data-stu-id="aad11-286">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="aad11-287">如果伺服器將 `Content-Type` 標頭設定為 `text/html` 並搭配設定 `nosniff` 選項，則不論檔案內容為何，Internet Explorer 都會將該內容轉譯為 `text/html`。</span><span class="sxs-lookup"><span data-stu-id="aad11-287">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="aad11-288">編輯 *httpd.conf* 檔案：</span><span class="sxs-lookup"><span data-stu-id="aad11-288">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="aad11-289">新增 `Header set X-Content-Type-Options "nosniff"` 行。</span><span class="sxs-lookup"><span data-stu-id="aad11-289">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="aad11-290">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="aad11-290">Save the file.</span></span> <span data-ttu-id="aad11-291">重新啟動 Apache。</span><span class="sxs-lookup"><span data-stu-id="aad11-291">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="aad11-292">負載平衡</span><span class="sxs-lookup"><span data-stu-id="aad11-292">Load Balancing</span></span>

<span data-ttu-id="aad11-293">這個範例示範如何在 CentOS 7 上安裝和設定 Apache，以及如何在相同的執行個體電腦上安裝和設定 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="aad11-293">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="aad11-294">不會有單一失敗點;使用 *mod_proxy_balancer* 和修改 **VirtualHost** ，可讓您管理 Apache proxy 伺服器後方的多個 web 應用程式實例。</span><span class="sxs-lookup"><span data-stu-id="aad11-294">To not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="aad11-295">在以下所示的設定檔中，已將一個額外的 `helloapp` 執行個體設定在連接埠 5001 上執行。</span><span class="sxs-lookup"><span data-stu-id="aad11-295">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="aad11-296">*Proxy* 區段中設定了平衡器設定，其中有兩個成員為 *byrequests* 進行負載平衡。</span><span class="sxs-lookup"><span data-stu-id="aad11-296">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="aad11-297">速率限制</span><span class="sxs-lookup"><span data-stu-id="aad11-297">Rate Limits</span></span>

<span data-ttu-id="aad11-298">使用 *mod_ratelimit* (包含在 *httpd* 模組中) 時，可以限制用戶端的頻寬：</span><span class="sxs-lookup"><span data-stu-id="aad11-298">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="aad11-299">此範例檔案將根目錄位置下的頻寬限制為 600 KB/秒：</span><span class="sxs-lookup"><span data-stu-id="aad11-299">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="aad11-300">要求標頭欄位太長</span><span class="sxs-lookup"><span data-stu-id="aad11-300">Long request header fields</span></span>

<span data-ttu-id="aad11-301">Proxy 伺服器預設設定通常會將要求標頭欄位限制為8190個位元組。</span><span class="sxs-lookup"><span data-stu-id="aad11-301">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="aad11-302">應用程式可能需要比預設 (更長的欄位，例如使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)) 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="aad11-302">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="aad11-303">如果需要較長的欄位，proxy 伺服器的 [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) 指示詞需要調整。</span><span class="sxs-lookup"><span data-stu-id="aad11-303">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="aad11-304">要套用的值取決於案例。</span><span class="sxs-lookup"><span data-stu-id="aad11-304">The value to apply depends on the scenario.</span></span> <span data-ttu-id="aad11-305">如需詳細資訊，請參閱您的伺服器文件。</span><span class="sxs-lookup"><span data-stu-id="aad11-305">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="aad11-306">除非必要，否則請勿增加 `LimitRequestFieldSize` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="aad11-306">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="aad11-307">增加此值會提高緩衝區溢位及惡意使用者發動拒絕服務 (DoS) 攻擊的風險。</span><span class="sxs-lookup"><span data-stu-id="aad11-307">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aad11-308">其他資源</span><span class="sxs-lookup"><span data-stu-id="aad11-308">Additional resources</span></span>

* [<span data-ttu-id="aad11-309">Linux 上 .NET Core 的必要條件</span><span class="sxs-lookup"><span data-stu-id="aad11-309">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
