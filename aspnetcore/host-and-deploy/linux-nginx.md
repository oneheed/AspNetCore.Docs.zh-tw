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
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="4dbe2-103">在 Linux 上使用 Nginx 裝載 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4dbe2-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="4dbe2-104">由 [Sourabh Shirhatti](https://twitter.com/sshirhatti) 提供</span><span class="sxs-lookup"><span data-stu-id="4dbe2-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="4dbe2-105">本指南說明在 Ubuntu 16.04 伺服器上設定生產環境就緒的 ASP.NET Core 環境。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="4dbe2-106">這些指示可能使用較新版本的 Ubuntu，但未經測試。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="4dbe2-107">如需有關 ASP.NET Core 支援之其他 Linux 發行版本的資訊，請參閱 [Linux 上 .NET Core 的必要條件](/dotnet/core/linux-prerequisites)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="4dbe2-108">針對 Ubuntu 14.04， `supervisord` 建議使用此解決方案來監視 Kestrel 流程。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-108">For Ubuntu 14.04, `supervisord` is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="4dbe2-109">`systemd` 在 Ubuntu 14.04 上無法使用。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-109">`systemd` isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="4dbe2-110">如需 Ubuntu 14.04 指示，請參閱[本主題前一版本](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="4dbe2-111">本指南：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-111">This guide:</span></span>

* <span data-ttu-id="4dbe2-112">將現有的 ASP.NET Core 應用程式放在反向 Proxy 伺服器後方。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="4dbe2-113">設定反向 Proxy 伺服器以將要求轉送至 Kestrel 網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="4dbe2-114">確保 Web 應用程式在啟動時以精靈的形式執行。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="4dbe2-115">設定程序管理工具以協助重新啟動 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4dbe2-116">必要條件</span><span class="sxs-lookup"><span data-stu-id="4dbe2-116">Prerequisites</span></span>

* <span data-ttu-id="4dbe2-117">以 sudo 權限使用標準使用者帳戶存取 Ubuntu 16.04 伺服器。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="4dbe2-118">安裝在伺服器上的最新非預覽版 [.net 運行](/dotnet/core/install/linux) 時間。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-118">The latest non-preview [.NET runtime installed](/dotnet/core/install/linux) on the server.</span></span>
* <span data-ttu-id="4dbe2-119">現有的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-119">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="4dbe2-120">在升級共用架構之後的任何時間點，請重新開機伺服器所裝載的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-120">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="4dbe2-121">跨應用程式發佈與複製</span><span class="sxs-lookup"><span data-stu-id="4dbe2-121">Publish and copy over the app</span></span>

<span data-ttu-id="4dbe2-122">為[架構相依部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)設定應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-122">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="4dbe2-123">如果應用程式在本機執行且未設定為進行安全連線 (HTTPS)，請採用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-123">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="4dbe2-124">設定應用程式以處理安全的本機連線。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-124">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="4dbe2-125">如需詳細資訊，請參閱 [HTTPS 組態](#https-configuration)一節。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-125">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="4dbe2-126">`https://localhost:5001`從檔案的屬性中移除 (（如果有) 的話） `applicationUrl` `Properties/launchSettings.json` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-126">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the `Properties/launchSettings.json` file.</span></span>

<span data-ttu-id="4dbe2-127">執行 [dotnet](/dotnet/core/tools/dotnet-publish) 從開發環境發佈，將應用程式封裝至目錄 (例如， `bin/Release/{TARGET FRAMEWORK MONIKER}/publish` 其中預留位置 `{TARGET FRAMEWORK MONIKER}` 是可在伺服器上執行的目標 Framework 標記/TFM) ：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-127">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, where the placeholder `{TARGET FRAMEWORK MONIKER}` is the Target Framework Moniker/TFM) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="4dbe2-128">如果您不想在伺服器上維護 .NET Core 執行階段，應用程式也可以發佈為[獨立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-128">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="4dbe2-129">使用整合至組織工作流程 (的工具，將 ASP.NET Core 應用程式複製到伺服器，例如 `SCP` `SFTP`) 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-129">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, `SCP`, `SFTP`).</span></span> <span data-ttu-id="4dbe2-130">通常會在目錄底下尋找 web 應用程式 `var` (例如 `var/www/helloapp`) 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-130">It's common to locate web apps under the `var` directory (for example, `var/www/helloapp`).</span></span>

> [!NOTE]
> <span data-ttu-id="4dbe2-131">在生產環境部署案例中，持續整合工作流程會執行發佈應用程式並將資產複製到伺服器的工作。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-131">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="4dbe2-132">測試應用程式：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-132">Test the app:</span></span>

1. <span data-ttu-id="4dbe2-133">從命令列執行應用程式：`dotnet <app_assembly>.dll`。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-133">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="4dbe2-134">在瀏覽器中，巡覽至 `http://<serveraddress>:<port>` 以確認應用程式可在 Linux 本機上正常運作。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-134">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="4dbe2-135">設定反向 Proxy 伺服器</span><span class="sxs-lookup"><span data-stu-id="4dbe2-135">Configure a reverse proxy server</span></span>

<span data-ttu-id="4dbe2-136">反向 Proxy 是為動態 Web 應用程式提供服務的常見設定。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-136">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="4dbe2-137">反向 Proxy 會終止 HTTP 要求，並將它轉送至 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-137">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="4dbe2-138">使用反向 Proxy 伺服器</span><span class="sxs-lookup"><span data-stu-id="4dbe2-138">Use a reverse proxy server</span></span>

<span data-ttu-id="4dbe2-139">Kestrel 非常適用於從 ASP.NET Core 提供動態內容。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-139">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="4dbe2-140">不過，Web 服務功能不像 IIS、Apache 或 Nginx 這類伺服器那樣豐富。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-140">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="4dbe2-141">反向 Proxy 伺服器可以讓 HTTP 伺服器卸下提供靜態內容、快取要求、壓縮要求及終止 HTTPS 等工作的負擔。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-141">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="4dbe2-142">反向 Proxy 伺服器可能位在專用電腦上，或可能與 HTTP 伺服器一起部署。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-142">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="4dbe2-143">為達到本指南的目的，使用 Nginx 的單一執行個體。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-143">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="4dbe2-144">它會在相同的伺服器上和 HTTP 伺服器一起執行。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-144">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="4dbe2-145">您可以根據需求，選擇不同的設定。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-145">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="4dbe2-146">因為反向 proxy 會轉送要求，所以請使用封裝中 [轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer) [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-146">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) package.</span></span> <span data-ttu-id="4dbe2-147">此中介軟體會使用 `X-Forwarded-Proto` 標頭來更新 `Request.Scheme`，以便讓重新導向 URI 及其他安全性原則正確運作。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-147">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="4dbe2-148"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A>在 `Startup.Configure` 呼叫其他中介軟體之前，先在頂端叫用方法。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-148">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="4dbe2-149">請設定中介軟體來轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-149">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="4dbe2-150">如果未將任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 指定給中介軟體，則要轉送的預設標頭會是 `None`。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-150">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="4dbe2-151">預設會信任在回送位址上執行的 proxy (`127.0.0.0/8` 、 `[::1]`) （包括標準 localhost 位址 (`127.0.0.1`) ）。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-151">Proxies running on loopback addresses (`127.0.0.0/8`, `[::1]`), including the standard localhost address (`127.0.0.1`), are trusted by default.</span></span> <span data-ttu-id="4dbe2-152">如果組織內有其他受信任的 Proxy 或網路處理網際網路與網頁伺服器之間的要求，請使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，將其新增至 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> 或 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> 清單。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-152">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="4dbe2-153">下列範例會將 IP 位址 10.0.0.100 的受信任 Proxy 伺服器新增至 `Startup.ConfigureServices` 中「轉送的標頭中介軟體」的 `KnownProxies`：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-153">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="4dbe2-154">如需詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-154">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="4dbe2-155">安裝 Nginx</span><span class="sxs-lookup"><span data-stu-id="4dbe2-155">Install Nginx</span></span>

<span data-ttu-id="4dbe2-156">使用 `apt-get` 來安裝 Nginx。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-156">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="4dbe2-157">安裝程式會建立在 `systemd` 系統啟動時執行 Nginx 做為 daemon 的 init 腳本。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-157">The installer creates a `systemd` init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="4dbe2-158">請遵循 [Nginx：Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx：官方 Debian/Ubuntu 套件) 中適用於 Ubuntu 的安裝指示。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-158">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="4dbe2-159">如果需要選用的 Nginx 模組，可能要從來源建置 Nginx。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-159">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="4dbe2-160">因為 Nginx 是第一次安裝，請透過執行明確啟動它：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-160">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="4dbe2-161">確認瀏覽器會顯示 Nginx 的預設登陸頁面。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-161">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="4dbe2-162">登陸頁面位於 `http://<server_IP_address>/index.nginx-debian.html`。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-162">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="4dbe2-163">設定 Nginx</span><span class="sxs-lookup"><span data-stu-id="4dbe2-163">Configure Nginx</span></span>

<span data-ttu-id="4dbe2-164">若要將 Nginx 設定為反向 proxy，以將 HTTP 要求轉送至您的 ASP.NET Core 應用程式，請修改 `/etc/nginx/sites-available/default` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-164">To configure Nginx as a reverse proxy to forward HTTP requests to your ASP.NET Core app, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="4dbe2-165">在文字編輯器中開啟它，並將內容取代為下列程式碼片段：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-165">Open it in a text editor, and replace the contents with the following snippet:</span></span>

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

<span data-ttu-id="4dbe2-166">如果應用程式是 SignalR 或 Blazor Server 應用程式，請 <xref:signalr/scale#linux-with-nginx> 分別參閱和， <xref:blazor/host-and-deploy/server#linux-with-nginx> 以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-166">If the app is a SignalR or Blazor Server app, see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="4dbe2-167">當沒有任何與 `server_name` 相符的項目時，Nginx 會使用預設伺服器。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-167">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="4dbe2-168">如果未定義任何預設伺服器，則設定檔中的第一個伺服器就是預設伺服器。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-168">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="4dbe2-169">最佳做法是在您的設定檔中新增會傳回狀態碼444的特定預設伺服器。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-169">As a best practice, add a specific default server that returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="4dbe2-170">預設伺服器設定範例如下：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-170">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4dbe2-171">使用上述設定檔和預設伺服器時，Nginx 會在連接埠 80 接受主機標頭為 `example.com` 或 `*.example.com` 的公用流量。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-171">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="4dbe2-172">不符合這些主機的要求將不會轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-172">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="4dbe2-173">Nginx 會將相符的要求轉送至位於 `http://localhost:5000` 的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-173">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="4dbe2-174">如需詳細資訊，請參閱 [nginx 處理要求的方式](https://nginx.org/docs/http/request_processing.html)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-174">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="4dbe2-175">若要變更 Kestrel 的 IP/連接埠，請參閱 [Kestrel：端點組態](xref:fundamentals/servers/kestrel/endpoints)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-175">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4dbe2-176">使用上述設定檔和預設伺服器時，Nginx 會在連接埠 80 接受主機標頭為 `example.com` 或 `*.example.com` 的公用流量。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-176">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="4dbe2-177">不符合這些主機的要求將不會轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-177">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="4dbe2-178">Nginx 會將相符的要求轉送至位於 `http://localhost:5000` 的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-178">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="4dbe2-179">如需詳細資訊，請參閱 [nginx 處理要求的方式](https://nginx.org/docs/http/request_processing.html)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-179">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="4dbe2-180">若要變更 Kestrel 的 IP/連接埠，請參閱 [Kestrel：端點組態](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-180">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="4dbe2-181">如果無法指定適當的 [server_name 指示詞](https://nginx.org/docs/http/server_names.html)，就會讓應用程式暴露在安全性弱點的風險下。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-181">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="4dbe2-182">若您擁有整個父網域 (相對於易受攻擊的 `*.com`) 的控制權，子網域萬用字元繫結 (例如 `*.example.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-182">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4dbe2-183">如需詳細資訊，請參閱 [rfc7230 章節-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-183">For more information, see [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

<span data-ttu-id="4dbe2-184">建立 Nginx 設定之後，請執行 `sudo nginx -t` 來確認設定檔的語法。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-184">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="4dbe2-185">如果設定檔測試成功，請執行 `sudo nginx -s reload` 來強制 Nginx 套用這些變更。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-185">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="4dbe2-186">直接在伺服器上執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-186">To directly run the app on the server:</span></span>

1. <span data-ttu-id="4dbe2-187">巡覽至應用程式目錄。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-187">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="4dbe2-188">執行應用程式：`dotnet <app_assembly.dll>`，其中 `app_assembly.dll` 是應用程式的組件檔名稱。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-188">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="4dbe2-189">如果應用程式在伺服器上執行，但無法透過網際網路回應，請檢查伺服器的防火牆，並確認埠80已開啟。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-189">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm port 80 is open.</span></span> <span data-ttu-id="4dbe2-190">如果使用的是 Azure Ubuntu VM，請新增啓用輸入連接埠 80 流量的網路安全性群組 (NSG) 規則。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-190">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="4dbe2-191">沒有必要啟用輸出連接埠 80 規則，因為啓用輸入規則時會自動授與輸出流量。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-191">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="4dbe2-192">完成測試應用程式時，請在命令提示字元下使用<kbd>Ctrl +</kbd>關閉應用程式  +  <kbd></kbd> 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-192">When done testing the app, shut down the app with <kbd>Ctrl</kbd> + <kbd>C</kbd> at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="4dbe2-193">監視應用程式</span><span class="sxs-lookup"><span data-stu-id="4dbe2-193">Monitor the app</span></span>

<span data-ttu-id="4dbe2-194">伺服器會設定為將對開啟的要求轉寄至在 Kestrel 上執行 `http://<serveraddress>:80` 的 ASP.NET Core 應用程式 `http://127.0.0.1:5000` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-194">The server is set up to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="4dbe2-195">不過，並未設定 Nginx 來管理 Kestrel 處理序。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-195">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="4dbe2-196">`systemd` 可以用來建立服務檔案，以啟動並監視基礎 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-196">`systemd` can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="4dbe2-197">`systemd` 是 init 系統，提供許多強大的功能來啟動、停止及管理進程。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-197">`systemd` is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="4dbe2-198">建立服務檔</span><span class="sxs-lookup"><span data-stu-id="4dbe2-198">Create the service file</span></span>

<span data-ttu-id="4dbe2-199">建立服務定義檔：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-199">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="4dbe2-200">下列範例是應用程式的服務檔：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-200">The following example is a service file for the app:</span></span>

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

<span data-ttu-id="4dbe2-201">在上述範例中，管理服務的使用者是由選項所指定 `User` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-201">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="4dbe2-202">使用者 (`www-data`) 必須存在，且擁有應用程式檔案的適當擁有權。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-202">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="4dbe2-203">使用 `TimeoutStopSec` 可設定應用程式收到初始中斷訊號之後等待關閉的時間。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-203">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="4dbe2-204">如果應用程式在此期間後未關閉，則會發出 SIGKILL 來終止應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-204">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="4dbe2-205">提供不具單位的秒值 (例如 `150`)、時間範圍值 (例如 `2min 30s`) 或 `infinity` (表示停用逾時)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-205">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="4dbe2-206">`TimeoutStopSec` 預設為 `DefaultTimeoutStopSec` manager 設定檔中的值 `systemd-system.conf` ， (、 `system.conf.d` 、 `systemd-user.conf` `user.conf.d`) 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-206">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span></span> <span data-ttu-id="4dbe2-207">大多數發行版本的預設逾時為 90 秒。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-207">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="4dbe2-208">Linux 的檔案系統會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-208">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="4dbe2-209">將設定 `ASPNETCORE_ENVIRONMENT` 為 `Production` 會導致搜尋設定檔 `appsettings.Production.json` ，而不是 `appsettings.production.json` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-209">Setting `ASPNETCORE_ENVIRONMENT` to `Production` results in searching for the configuration file `appsettings.Production.json`, not `appsettings.production.json`.</span></span>

<span data-ttu-id="4dbe2-210">有些值 (例如 SQL 連接字串) 必須以逸出方式處理，設定提供者才能讀取環境變數。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-210">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="4dbe2-211">請使用下列命令來產生要在設定檔中使用並已適當逸出的值：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-211">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4dbe2-212">環境變數名稱不支援冒號 (`:`) 分隔符號。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-212">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="4dbe2-213">請使用雙底線 (`__`) 來取代冒號。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-213">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="4dbe2-214">[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables)會在將環境變數讀入組態時，將雙底線轉換為冒號。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-214">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="4dbe2-215">在下列範例中，連接字串索引鍵 `ConnectionStrings:DefaultConnection` 會設定為服務定義檔中的 `ConnectionStrings__DefaultConnection`：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-215">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4dbe2-216">環境變數名稱不支援冒號 (`:`) 分隔符號。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-216">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="4dbe2-217">請使用雙底線 (`__`) 來取代冒號。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-217">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="4dbe2-218">[環境變數組態提供者](xref:fundamentals/configuration/index#environment-variables-configuration-provider)會在將環境變數讀入組態時，將雙底線轉換為冒號。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-218">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="4dbe2-219">在下列範例中，連接字串索引鍵 `ConnectionStrings:DefaultConnection` 會設定為服務定義檔中的 `ConnectionStrings__DefaultConnection`：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-219">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="4dbe2-220">儲存檔案並啟用服務。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-220">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="4dbe2-221">啟動服務並確認它正在執行。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-221">Start the service and verify that it's running.</span></span>

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

<span data-ttu-id="4dbe2-222">透過設定反向 proxy 並透過管理 Kestrel `systemd` ，web 應用程式就會完整設定，並可在本機電腦上的瀏覽器中存取 `http://localhost` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-222">With the reverse proxy configured and Kestrel managed through `systemd`, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="4dbe2-223">此外，也可以從遠端電腦存取它，除非遭到任何防火牆封鎖。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-223">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="4dbe2-224">檢查回應標頭時，`Server` 標頭會顯示是由 Kestrel 為 ASP.NET Core 應用程式提供服務。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-224">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="4dbe2-225">檢視記錄</span><span class="sxs-lookup"><span data-stu-id="4dbe2-225">View logs</span></span>

<span data-ttu-id="4dbe2-226">由於是使用 `systemd` 來管理使用 Kestrel 的 Web 應用程式，因此會將所有事件和處理序都記錄在集中式日誌中。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-226">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="4dbe2-227">不過，此日誌包含 `systemd` 管理的所有服務和處理程序的所有項目。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-227">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="4dbe2-228">若要檢視 `kestrel-helloapp.service` 的特定項目，請使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-228">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="4dbe2-229">如需進一步的篩選，時間選項（例如 `--since today` 、 `--until 1 hour ago` 或）的組合可以減少傳回的專案數。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-229">For further filtering, time options such as `--since today`, `--until 1 hour ago`, or a combination of these can reduce the number of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="4dbe2-230">資料保護</span><span class="sxs-lookup"><span data-stu-id="4dbe2-230">Data protection</span></span>

<span data-ttu-id="4dbe2-231">[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core[中介軟體](xref:fundamentals/middleware/index)所使用，包括驗證中介軟體 (例如， cookie 中介軟體) 和跨網站要求偽造 (CSRF) 保護。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-231">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="4dbe2-232">即使資料保護 API 並非由使用者程式碼呼叫，仍應設定資料保護，以建立持續密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-232">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="4dbe2-233">如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-233">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="4dbe2-234">如果 Keyring 儲存在記憶體中，則當應用程式重新啟動時：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-234">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="4dbe2-235">所有 cookie 的驗證權杖都會失效。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-235">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="4dbe2-236">當使用者提出下一個要求時，需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-236">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="4dbe2-237">所有以 Keyring 保護的資料都無法再解密。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-237">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="4dbe2-238">這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie ](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-238">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="4dbe2-239">若要設定資料保護來保存及加密金鑰環，請參閱：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-239">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="4dbe2-240">要求標頭欄位太長</span><span class="sxs-lookup"><span data-stu-id="4dbe2-240">Long request header fields</span></span>

<span data-ttu-id="4dbe2-241">Proxy 伺服器預設設定通常會將要求標頭欄位限制為 4 K 或 8 K （視平臺而定）。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-241">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="4dbe2-242">應用程式可能需要比預設 (更長的欄位，例如使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)) 的應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-242">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="4dbe2-243">如果需要較長的欄位，proxy 伺服器的預設設定需要進行調整。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-243">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="4dbe2-244">要套用的值取決於案例。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-244">The values to apply depend on the scenario.</span></span> <span data-ttu-id="4dbe2-245">如需詳細資訊，請參閱您的伺服器文件。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-245">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="4dbe2-246">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="4dbe2-246">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="4dbe2-247">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="4dbe2-247">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="4dbe2-248">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="4dbe2-248">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="4dbe2-249">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="4dbe2-249">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="4dbe2-250">除非必要，否則請勿增加 Proxy 緩衝區的預設值。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-250">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="4dbe2-251">增加這些值會提高緩衝區溢位及惡意使用者發動拒絕服務 (DoS) 攻擊的風險。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-251">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="4dbe2-252">保護應用程式</span><span class="sxs-lookup"><span data-stu-id="4dbe2-252">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="4dbe2-253">啟用 AppArmor</span><span class="sxs-lookup"><span data-stu-id="4dbe2-253">Enable AppArmor</span></span>

<span data-ttu-id="4dbe2-254">Linux 安全性模組 (LSM) 是 Linux 2.6 之後 Linux 核心所包含的一個架構。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-254">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="4dbe2-255">LSM 支援不同的安全性模組實作。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-255">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="4dbe2-256">[AppArmor](https://wiki.ubuntu.com/AppArmor) 是一個 LSM，可執行強制存取控制系統，讓您將程式將到一組有限的資源。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-256">[AppArmor](https://wiki.ubuntu.com/AppArmor) is an LSM that implements a Mandatory Access Control system, which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="4dbe2-257">確定已啟用並正確設定 AppArmor。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-257">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="4dbe2-258">設定防火牆</span><span class="sxs-lookup"><span data-stu-id="4dbe2-258">Configure the firewall</span></span>

<span data-ttu-id="4dbe2-259">關閉所有未使用的外部埠。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-259">Close off all external ports that aren't in use.</span></span> <span data-ttu-id="4dbe2-260">簡單的防火牆 (ufw) 提供可設定防火牆的 CLI 來提供前端 `iptables` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-260">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="4dbe2-261">如未正確設定，防火牆會禁止存取整個系統。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-261">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="4dbe2-262">未指定正確的 SSH 連接埠，將會導致您無法存取系統 (若您使用 SSH 連線至該連接埠)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-262">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="4dbe2-263">預設連接埠為 22。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-263">The default port is 22.</span></span> <span data-ttu-id="4dbe2-264">如需詳細資訊，請參閱 [ufw 簡介](https://help.ubuntu.com/community/UFW)與[手冊](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-264">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="4dbe2-265">安裝 `ufw` 並將其設定為允許任何所需連接埠上的流量。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-265">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="4dbe2-266">保護 Nginx</span><span class="sxs-lookup"><span data-stu-id="4dbe2-266">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="4dbe2-267">變更 Nginx 回應名稱</span><span class="sxs-lookup"><span data-stu-id="4dbe2-267">Change the Nginx response name</span></span>

<span data-ttu-id="4dbe2-268">編輯 `src/http/ngx_http_header_filter_module.c`：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-268">Edit `src/http/ngx_http_header_filter_module.c`:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="4dbe2-269">設定選項</span><span class="sxs-lookup"><span data-stu-id="4dbe2-269">Configure options</span></span>

<span data-ttu-id="4dbe2-270">設定伺服器的其他所需模組。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-270">Configure the server with additional required modules.</span></span> <span data-ttu-id="4dbe2-271">請考慮使用 [ModSecurity](https://www.modsecurity.org/) 等 Web 應用程式防火牆來強化應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-271">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="4dbe2-272">HTTPS 設定</span><span class="sxs-lookup"><span data-stu-id="4dbe2-272">HTTPS configuration</span></span>

<span data-ttu-id="4dbe2-273">**設定應用程式以進行安全的本機連線 (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="4dbe2-273">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="4dbe2-274">[Dotnet run](/dotnet/core/tools/dotnet-run)命令使用應用程式的 *Properties/launchSettings.json* 檔案，其會將應用程式設定為在屬性提供的 url 上接聽 `applicationUrl` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-274">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property.</span></span> <span data-ttu-id="4dbe2-275">例如： `https://localhost:5001;http://localhost:5000` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-275">For example, `https://localhost:5001;http://localhost:5000`.</span></span>

<span data-ttu-id="4dbe2-276">您可以使用下列其中一種方法，將應用程式設定為在命令或開發環境中使用憑證進行開發 `dotnet run` (<kbd>F5</kbd>或<kbd>Ctrl</kbd> + <kbd>F5</kbd>) ：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-276">Configure the app to use a certificate in development for the `dotnet run` command or development environment (<kbd>F5</kbd> or <kbd>Ctrl</kbd>+<kbd>F5</kbd> in Visual Studio Code) using one of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="4dbe2-277">[取代組態中的預設憑證](xref:fundamentals/servers/kestrel/endpoints#configuration) (建議使用)</span><span class="sxs-lookup"><span data-stu-id="4dbe2-277">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="4dbe2-278">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="4dbe2-278">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="4dbe2-279">[取代組態中的預設憑證](xref:fundamentals/servers/kestrel#configuration) (建議使用)</span><span class="sxs-lookup"><span data-stu-id="4dbe2-279">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="4dbe2-280">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="4dbe2-280">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

<span data-ttu-id="4dbe2-281">**設定反向 Prooxy 以進行安全的用戶端連線 (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="4dbe2-281">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="4dbe2-282">藉由指定由受信任的憑證授權單位單位所簽發的有效憑證 (CA) ，將伺服器設定為接聽埠443上的 HTTPS 流量。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-282">Configure the server to listen to HTTPS traffic on port 443 by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="4dbe2-283">採用以下 */etc/nginx/nginx.conf* 檔案所述的一些做法來強化安全性。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-283">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="4dbe2-284">範例包括選擇更強的加密，重新導向 HTTPS 到 HTTP 的所有流量。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-284">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

  > [!NOTE]
  > <span data-ttu-id="4dbe2-285">針對開發環境，我們建議使用暫時性重新導向 (302) 而不是永久重新導向 (301) 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-285">For development environments, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="4dbe2-286">連結快取可能會在開發環境中造成不穩定的行為。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-286">Link caching can cause unstable behavior in development environments.</span></span>

* <span data-ttu-id="4dbe2-287">新增 `HTTP Strict-Transport-Security` (HSTS) 標頭可確保用戶端提出的所有後續要求都會透過 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-287">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

  <span data-ttu-id="4dbe2-288">如需 HSTS 的重要指導方針，請參閱 <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts> 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-288">For important guidance on HSTS, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

* <span data-ttu-id="4dbe2-289">如果未來將停用 HTTPS，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-289">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="4dbe2-290">請勿新增 HSTS 標頭。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-290">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="4dbe2-291">選擇簡短 `max-age` 值。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-291">Choose a short `max-age` value.</span></span>

<span data-ttu-id="4dbe2-292">新增 */etc/nginx/Proxy.conf* 組態檔：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-292">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="4dbe2-293">將 */etc/nginx/nginx.conf* 設定檔的內容 **取代** 為下列檔案。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-293">**Replace** the contents of the */etc/nginx/nginx.conf* configuration file with the following file.</span></span> <span data-ttu-id="4dbe2-294">此範例在一個組態檔中同時包含 `http` 和 `server` 區段。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-294">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="4dbe2-295">Blazor WebAssembly 應用程式需要較大的 `burst` 參數值，以容納應用程式所提出的大量要求。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-295">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="4dbe2-296">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/webassembly#nginx>。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-296">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="4dbe2-297">保護 Nginx 免於點閱綁架</span><span class="sxs-lookup"><span data-stu-id="4dbe2-297">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="4dbe2-298">[點閱綁架](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)(也稱為「UI 偽裝攻擊」) 是一種惡意攻擊，會誘騙網站訪客點選與其目前所瀏覽頁面不同的頁面上連結或按鈕。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-298">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="4dbe2-299">請使用 `X-FRAME-OPTIONS` 來保護網站安全。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-299">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="4dbe2-300">減輕點擊劫持攻擊：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-300">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="4dbe2-301">編輯 *nginx.conf* 檔案：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-301">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="4dbe2-302">新增行： `add_header X-Frame-Options "SAMEORIGIN";`</span><span class="sxs-lookup"><span data-stu-id="4dbe2-302">Add the line: `add_header X-Frame-Options "SAMEORIGIN";`</span></span>

1. <span data-ttu-id="4dbe2-303">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-303">Save the file.</span></span>
1. <span data-ttu-id="4dbe2-304">重新啟動 Nginx。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-304">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="4dbe2-305">MIME 類型探查</span><span class="sxs-lookup"><span data-stu-id="4dbe2-305">MIME-type sniffing</span></span>

<span data-ttu-id="4dbe2-306">此標頭會防止大部分的瀏覽器對遠離宣告內容類型的回應進行 MIME 探查，因為標頭會指示瀏覽器不要覆寫回應內容類型。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-306">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="4dbe2-307">使用 `nosniff` 選項時，如果伺服器顯示的內容是 `text/html` ，則瀏覽器會將它轉譯為 `text/html` 。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-307">With the `nosniff` option, if the server says the content is `text/html`, the browser renders it as `text/html`.</span></span>

1. <span data-ttu-id="4dbe2-308">編輯 *nginx.conf* 檔案：</span><span class="sxs-lookup"><span data-stu-id="4dbe2-308">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="4dbe2-309">新增行： `add_header X-Content-Type-Options "nosniff";`</span><span class="sxs-lookup"><span data-stu-id="4dbe2-309">Add the line: `add_header X-Content-Type-Options "nosniff";`</span></span>

1. <span data-ttu-id="4dbe2-310">儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-310">Save the file.</span></span>
1. <span data-ttu-id="4dbe2-311">重新啟動 Nginx。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-311">Restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="4dbe2-312">其他 Nginx 建議</span><span class="sxs-lookup"><span data-stu-id="4dbe2-312">Additional Nginx suggestions</span></span>

<span data-ttu-id="4dbe2-313">在伺服器上升級共用架構之後，請重新開機伺服器所裝載的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4dbe2-313">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4dbe2-314">其他資源</span><span class="sxs-lookup"><span data-stu-id="4dbe2-314">Additional resources</span></span>

* [<span data-ttu-id="4dbe2-315">Linux 上 .NET Core 的必要條件</span><span class="sxs-lookup"><span data-stu-id="4dbe2-315">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <span data-ttu-id="4dbe2-316">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx：二進位版本：正式的 Debian Ubuntu 套件)</span><span class="sxs-lookup"><span data-stu-id="4dbe2-316">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)</span></span>
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="4dbe2-317">NGINX：使用轉送的標頭</span><span class="sxs-lookup"><span data-stu-id="4dbe2-317">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
