---
title: ASP.NET Core 中的 Kestrel 網頁伺服器實作
author: rick-anderson
description: 了解 Kestrel，這是 ASP.NET Core 的跨平台網頁伺服器。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: d53cafb605939fd85bdbb71b2fbf13e7bd7a9b7b
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570999"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="d4102-103">ASP.NET Core 中的 Kestrel 網頁伺服器實作</span><span class="sxs-lookup"><span data-stu-id="d4102-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="d4102-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher) 和 [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="d4102-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d4102-105">Kestrel 是 [ASP.NET Core 的跨平台網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="d4102-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d4102-106">Kestrel 是 ASP.NET Core 專案範本中預設包含和啟用的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="d4102-106">Kestrel is the web server that's included and enabled by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d4102-107">Kestrel 支援下列案例：</span><span class="sxs-lookup"><span data-stu-id="d4102-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d4102-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d4102-108">HTTPS</span></span>
* <span data-ttu-id="d4102-109">除了 macOS) 之外， [HTTP/2](xref:fundamentals/servers/kestrel/http2) (&dagger;</span><span class="sxs-lookup"><span data-stu-id="d4102-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (except on macOS&dagger;)</span></span>
* <span data-ttu-id="d4102-110">用來啟用 [WebSockets](xref:fundamentals/websockets) 的不透明升級</span><span class="sxs-lookup"><span data-stu-id="d4102-110">Opaque upgrade used to enable [WebSockets](xref:fundamentals/websockets)</span></span>
* <span data-ttu-id="d4102-111">Nginx 背後的高效能 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-111">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="d4102-112">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d4102-113">.NET Core 支援的所有平台和版本都支援 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d4102-114">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4102-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started"></a><span data-ttu-id="d4102-115">開始使用</span><span class="sxs-lookup"><span data-stu-id="d4102-115">Get started</span></span>

<span data-ttu-id="d4102-116">ASP.NET Core 專案範本預設會使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-116">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d4102-117">在 *Program.cs* 中，此 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> ：</span><span class="sxs-lookup"><span data-stu-id="d4102-117">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="d4102-118">如需建立主機的詳細資訊，請參閱的 *設定主* 控制項和 *預設* 建立器設定一節 <xref:fundamentals/host/generic-host#set-up-a-host> 。</span><span class="sxs-lookup"><span data-stu-id="d4102-118">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4102-119">其他資源</span><span class="sxs-lookup"><span data-stu-id="d4102-119">Additional resources</span></span>

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d4102-120">RFC 7230：訊息語法和路由 (第 5.4 節：主機)</span><span class="sxs-lookup"><span data-stu-id="d4102-120">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* <span data-ttu-id="d4102-121">在 Linux 上使用 UNIX 通訊端時，通訊端不會在應用程式關閉時自動刪除。</span><span class="sxs-lookup"><span data-stu-id="d4102-121">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d4102-122">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="d4102-122">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>

> [!NOTE]
> <span data-ttu-id="d4102-123">從 ASP.NET Core 5.0，Kestrel 的 libuv 傳輸已淘汰。</span><span class="sxs-lookup"><span data-stu-id="d4102-123">As of ASP.NET Core 5.0, Kestrel's libuv transport is obsolete.</span></span> <span data-ttu-id="d4102-124">Libuv 傳輸不會接收更新來支援新的 OS 平臺（例如 Windows ARM64），並將在未來的版本中移除。</span><span class="sxs-lookup"><span data-stu-id="d4102-124">The libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="d4102-125">移除任何對已淘汰方法的呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> ，並改為使用 Kestrel 的預設通訊端傳輸。</span><span class="sxs-lookup"><span data-stu-id="d4102-125">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d4102-126">Kestrel 是 [ASP.NET Core 的跨平台網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="d4102-126">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d4102-127">Kestrel 是 ASP.NET Core 專案範本中預設隨附的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="d4102-127">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d4102-128">Kestrel 支援下列案例：</span><span class="sxs-lookup"><span data-stu-id="d4102-128">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d4102-129">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d4102-129">HTTPS</span></span>
* <span data-ttu-id="d4102-130">用來啟用 [WebSockets](https://github.com/aspnet/websockets) 的不透明升級</span><span class="sxs-lookup"><span data-stu-id="d4102-130">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d4102-131">Nginx 背後的高效能 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-131">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="d4102-132">HTTP/2 (macOS 上除外&dagger;)</span><span class="sxs-lookup"><span data-stu-id="d4102-132">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="d4102-133">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-133">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d4102-134">.NET Core 支援的所有平台和版本都支援 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-134">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d4102-135">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4102-135">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="d4102-136">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="d4102-136">HTTP/2 support</span></span>

<span data-ttu-id="d4102-137">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式使用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="d4102-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d4102-138">作業系統&dagger;</span><span class="sxs-lookup"><span data-stu-id="d4102-138">Operating system&dagger;</span></span>
  * <span data-ttu-id="d4102-139">Windows Server 2016/Windows 10 或更新版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="d4102-139">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="d4102-140">Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="d4102-140">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="d4102-141">目標 Framework：.NET Core 2.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d4102-141">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="d4102-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="d4102-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d4102-143">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="d4102-143">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d4102-144">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-144">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="d4102-145">&Dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。</span><span class="sxs-lookup"><span data-stu-id="d4102-145">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d4102-146">支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。</span><span class="sxs-lookup"><span data-stu-id="d4102-146">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d4102-147">可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-147">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="d4102-148">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="d4102-148">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d4102-149">從 .NET Core 3.0 開始，預設會啟用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-149">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d4102-150">如需組態的詳細資訊，請參閱 [Kestrel 選項](#kestrel-options)和 [ListenOptions. 通訊協定](#listenoptionsprotocols)一節。</span><span class="sxs-lookup"><span data-stu-id="d4102-150">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d4102-151">何時搭配使用 Kestrel 與反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="d4102-151">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d4102-152">您可以單獨使用 Kestrel，或與 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 等「反向 Proxy 伺服器」搭配使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-152">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d4102-153">反向 Proxy 伺服器會從網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-153">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d4102-154">Kestrel 用作邊緣 (網際網路對應) 網頁伺服器：</span><span class="sxs-lookup"><span data-stu-id="d4102-154">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d4102-156">Kestrel 用於反向 Proxy 組態中：</span><span class="sxs-lookup"><span data-stu-id="d4102-156">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d4102-158">不論是否有反向 proxy 伺服器，設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-158">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d4102-159">Kestrel 用作不需要反向 Proxy 伺服器的 Edge Server 時，不支援在多個處理序之間共用相同的 IP 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-159">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d4102-160">當 Kestrel 設定為接聽連接埠時，Kestrel 會處理該連接埠的所有流量，而不論要求的 `Host` 標頭為何。</span><span class="sxs-lookup"><span data-stu-id="d4102-160">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d4102-161">可以共用連接埠的反向 Proxy 能夠在唯一的 IP 和連接埠上轉送要求給 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-161">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d4102-162">即使不需要反向 Proxy 伺服器，使用反向 Proxy 伺服器也是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="d4102-162">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d4102-163">反向 Proxy：</span><span class="sxs-lookup"><span data-stu-id="d4102-163">A reverse proxy:</span></span>

* <span data-ttu-id="d4102-164">可以限制它所主控之應用程式的公開介面區。</span><span class="sxs-lookup"><span data-stu-id="d4102-164">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d4102-165">提供額外的組態和防禦層。</span><span class="sxs-lookup"><span data-stu-id="d4102-165">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d4102-166">能夠與現有基礎結構更好地整合。</span><span class="sxs-lookup"><span data-stu-id="d4102-166">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d4102-167">簡化負載平衡和安全通訊 (HTTPS) 組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-167">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d4102-168">只有反向 proxy 伺服器需要 x.509 憑證，而且該伺服器可以使用一般 HTTP 與內部網路上的應用程式伺服器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="d4102-168">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d4102-169">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="d4102-169">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d4102-170">ASP.NET Core apps 中的 Kestrel</span><span class="sxs-lookup"><span data-stu-id="d4102-170">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d4102-171">ASP.NET Core 專案範本預設會使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-171">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d4102-172">在 *Program.cs* 中，此 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> ：</span><span class="sxs-lookup"><span data-stu-id="d4102-172">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="d4102-173">如需建立主機的詳細資訊，請參閱的 *設定主* 控制項和 *預設* 建立器設定一節 <xref:fundamentals/host/generic-host#set-up-a-host> 。</span><span class="sxs-lookup"><span data-stu-id="d4102-173">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="d4102-174">若要在呼叫 `ConfigureWebHostDefaults` 之後提供額外的設定，請使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="d4102-174">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="d4102-175">Kestrel 選項</span><span class="sxs-lookup"><span data-stu-id="d4102-175">Kestrel options</span></span>

<span data-ttu-id="d4102-176">Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。</span><span class="sxs-lookup"><span data-stu-id="d4102-176">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d4102-177">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。</span><span class="sxs-lookup"><span data-stu-id="d4102-177">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d4102-178">`Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4102-178">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d4102-179">下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；</span><span class="sxs-lookup"><span data-stu-id="d4102-179">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d4102-180">在本文稍後所示的範例中，會在 c # 程式碼中設定 Kestrel 選項。</span><span class="sxs-lookup"><span data-stu-id="d4102-180">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="d4102-181">您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項。</span><span class="sxs-lookup"><span data-stu-id="d4102-181">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d4102-182">例如，檔案設定 [提供者](xref:fundamentals/configuration/index#file-configuration-provider) 可以從或 Appsettings 載入 Kestrel 設定 *appsettings.json* *。環境}. json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="d4102-182">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="d4102-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [端點](#endpoint-configuration) 設定可從設定提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="d4102-184">其餘的 Kestrel 設定必須在 c # 程式碼中設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-184">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="d4102-185">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="d4102-185">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d4102-186">設定 Kestrel in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d4102-186">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d4102-187">將的實例插入 `IConfiguration` 至 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="d4102-187">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d4102-188">下列範例假設將插入的設定指派給 `Configuration` 屬性。</span><span class="sxs-lookup"><span data-stu-id="d4102-188">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d4102-189">在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-189">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="d4102-190">設定 Kestrel 建立主機時：</span><span class="sxs-lookup"><span data-stu-id="d4102-190">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d4102-191">在 *Program.cs* 中，將 `Kestrel` Configuration 區段載入 Kestrel 的設定中：</span><span class="sxs-lookup"><span data-stu-id="d4102-191">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="d4102-192">上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-192">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d4102-193">Keep-alive 逾時</span><span class="sxs-lookup"><span data-stu-id="d4102-193">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d4102-194">取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="d4102-194">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d4102-195">預設為 2 分鐘。</span><span class="sxs-lookup"><span data-stu-id="d4102-195">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d4102-196">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="d4102-196">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d4102-197">可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：</span><span class="sxs-lookup"><span data-stu-id="d4102-197">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d4102-198">已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-198">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d4102-199">升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-199">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d4102-200">連線數目上限預設為無限制 (null)。</span><span class="sxs-lookup"><span data-stu-id="d4102-200">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d4102-201">要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="d4102-201">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d4102-202">預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="d4102-202">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d4102-203">若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：</span><span class="sxs-lookup"><span data-stu-id="d4102-203">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d4102-204">以下範例會示範如何設定應用程式、每個要求的條件約束：</span><span class="sxs-lookup"><span data-stu-id="d4102-204">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d4102-205">覆寫中介軟體中特定要求的設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-205">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d4102-206">如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d4102-206">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d4102-207">有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="d4102-207">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d4102-208">當應用程式是在 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)後方於[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)執行時，Kestrel 的要求本文大小限制將會被停用，因為 IIS 已經設定限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-208">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d4102-209">要求主體資料速率下限</span><span class="sxs-lookup"><span data-stu-id="d4102-209">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d4102-210">如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。</span><span class="sxs-lookup"><span data-stu-id="d4102-210">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d4102-211">如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 讓用戶端提高其傳送速率的時間量（最小值）;在這段時間內不會檢查速率。</span><span class="sxs-lookup"><span data-stu-id="d4102-211">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d4102-212">寬限期可協助避免中斷連線，這是由於 TCP 緩慢啟動而一開始以低速傳送資料所造成。</span><span class="sxs-lookup"><span data-stu-id="d4102-212">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d4102-213">預設速率下限為 240 個位元組/秒，寬限期為 5 秒。</span><span class="sxs-lookup"><span data-stu-id="d4102-213">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d4102-214">速率下限也適用於回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-214">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d4102-215">除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。</span><span class="sxs-lookup"><span data-stu-id="d4102-215">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d4102-216">以下範例示範如何在 *Program.cs* 中設定資料速率下限：</span><span class="sxs-lookup"><span data-stu-id="d4102-216">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="d4102-217">覆寫中介軟體中每個要求的最小速率限制：</span><span class="sxs-lookup"><span data-stu-id="d4102-217">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d4102-218">因為通訊協定對要求多工的支援，所以 HTTP/2 一般不支援以每一要求基礎修改速率限制，進而使先前範例中所參考的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> 不會出現在 HTTP/2 要求的 `HttpContext.Features` 中。</span><span class="sxs-lookup"><span data-stu-id="d4102-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d4102-219">不過，<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> 仍存在 HTTP/2 要求的 `HttpContext.Features`您仍能透過將 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 設定為 `null` (即使是針對 HTTP/2 要求)，以個別要求基礎來「完全停用」讀取素率限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-219">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="d4102-220">嘗試讀取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或嘗試將它設定為 `null` 以外的值將會導致擲回 `NotSupportedException` (假設要求是 HTTP/2 要求)。</span><span class="sxs-lookup"><span data-stu-id="d4102-220">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="d4102-221">透過 `KestrelServerOptions.Limits` 設定的全伺服器速率限制皆仍套用至 HTTP/1.x 及 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-221">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d4102-222">要求標頭逾時</span><span class="sxs-lookup"><span data-stu-id="d4102-222">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d4102-223">取得或設定伺服器花費在接收要求標頭的時間上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-223">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d4102-224">預設為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4102-224">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d4102-225">每個連線的資料流數目上限</span><span class="sxs-lookup"><span data-stu-id="d4102-225">Maximum streams per connection</span></span>

<span data-ttu-id="d4102-226">`Http2.MaxStreamsPerConnection` 會限制每個 HTTP/2 連線的同時要求資料流數目。</span><span class="sxs-lookup"><span data-stu-id="d4102-226">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d4102-227">超出的資料流會被拒絕。</span><span class="sxs-lookup"><span data-stu-id="d4102-227">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="d4102-228">預設值是 100。</span><span class="sxs-lookup"><span data-stu-id="d4102-228">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d4102-229">標頭表格大小</span><span class="sxs-lookup"><span data-stu-id="d4102-229">Header table size</span></span>

<span data-ttu-id="d4102-230">HPACK 解碼器可解壓縮 HTTP/2 連線的 HTTP 標頭。</span><span class="sxs-lookup"><span data-stu-id="d4102-230">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d4102-231">`Http2.HeaderTableSize` 會限制 HPACK 解碼器所使用的標頭壓縮表格大小。</span><span class="sxs-lookup"><span data-stu-id="d4102-231">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d4102-232">這個值是以八位元提供，而且必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="d4102-232">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="d4102-233">預設值為 4096。</span><span class="sxs-lookup"><span data-stu-id="d4102-233">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d4102-234">框架大小上限</span><span class="sxs-lookup"><span data-stu-id="d4102-234">Maximum frame size</span></span>

<span data-ttu-id="d4102-235">`Http2.MaxFrameSize` 指出伺服器接收或傳送之 HTTP/2 連接框架承載的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-235">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="d4102-236">這個值是以八位元提供，而且必須介於 2^14 (16,384) 到 2^24-1 (16,777,215) 之間。</span><span class="sxs-lookup"><span data-stu-id="d4102-236">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="d4102-237">預設值為 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="d4102-237">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d4102-238">要求標頭大小上限</span><span class="sxs-lookup"><span data-stu-id="d4102-238">Maximum request header size</span></span>

<span data-ttu-id="d4102-239">`Http2.MaxRequestHeaderFieldSize` 以八位元表示要求標頭值的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-239">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d4102-240">這項限制適用于其壓縮和未壓縮標記法中的名稱和值。</span><span class="sxs-lookup"><span data-stu-id="d4102-240">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d4102-241">此值必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="d4102-241">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="d4102-242">預設值為 8,192。</span><span class="sxs-lookup"><span data-stu-id="d4102-242">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d4102-243">初始連線視窗大小</span><span class="sxs-lookup"><span data-stu-id="d4102-243">Initial connection window size</span></span>

<span data-ttu-id="d4102-244">`Http2.InitialConnectionWindowSize` 會以位元組表示伺服器緩衝每個連線之所有要求 (資料流) 單次彙總的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-244">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d4102-245">要求也皆受 `Http2.InitialStreamWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-245">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d4102-246">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="d4102-246">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="d4102-247">預設值為 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="d4102-247">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d4102-248">初始資料流視窗大小</span><span class="sxs-lookup"><span data-stu-id="d4102-248">Initial stream window size</span></span>

<span data-ttu-id="d4102-249">`Http2.InitialStreamWindowSize` 會以位元組表示每個要求 (資料流) 單次伺服器緩衝的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-249">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d4102-250">要求也皆受 `Http2.InitialConnectionWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-250">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="d4102-251">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="d4102-251">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="d4102-252">預設值為 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="d4102-252">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="d4102-253">預告片</span><span class="sxs-lookup"><span data-stu-id="d4102-253">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="d4102-254">重設</span><span class="sxs-lookup"><span data-stu-id="d4102-254">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="d4102-255">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="d4102-255">Synchronous I/O</span></span>

<span data-ttu-id="d4102-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="d4102-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d4102-257">預設值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="d4102-257">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="d4102-258">大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-258">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d4102-259">只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。</span><span class="sxs-lookup"><span data-stu-id="d4102-259">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d4102-260">下列範例會啟用同步 i/o：</span><span class="sxs-lookup"><span data-stu-id="d4102-260">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d4102-261">如需其他 Kestrel 選項和限制的資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="d4102-261">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d4102-262">端點組態</span><span class="sxs-lookup"><span data-stu-id="d4102-262">Endpoint configuration</span></span>

<span data-ttu-id="d4102-263">ASP.NET Core 預設會繫結至：</span><span class="sxs-lookup"><span data-stu-id="d4102-263">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d4102-264">`https://localhost:5001` (當有本機開發憑證存在時)</span><span class="sxs-lookup"><span data-stu-id="d4102-264">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d4102-265">使用以下各項指定 URL：</span><span class="sxs-lookup"><span data-stu-id="d4102-265">Specify URLs using the:</span></span>

* <span data-ttu-id="d4102-266">`ASPNETCORE_URLS` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="d4102-266">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d4102-267">`--urls` 命令列引數。</span><span class="sxs-lookup"><span data-stu-id="d4102-267">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d4102-268">`urls` 主機組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d4102-268">`urls` host configuration key.</span></span>
* <span data-ttu-id="d4102-269">`UseUrls` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="d4102-269">`UseUrls` extension method.</span></span>

<span data-ttu-id="d4102-270">使用這些方法提供的值可以是一或多個 HTTP 和 HTTPS 端點 (如果有預設憑證可用則為 HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="d4102-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d4102-271">將值設定為以分號分隔的清單 (例如，`"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="d4102-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d4102-272">如需有關這些方法的詳細資訊，請參閱[伺服器 URL](xref:fundamentals/host/web-host#server-urls) 和[覆寫設定](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="d4102-272">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d4102-273">開發憑證會建立於：</span><span class="sxs-lookup"><span data-stu-id="d4102-273">A development certificate is created:</span></span>

* <span data-ttu-id="d4102-274">已安裝 [.NET Core SDK](/dotnet/core/sdk) 時。</span><span class="sxs-lookup"><span data-stu-id="d4102-274">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d4102-275">[dev-certs 工具](xref:aspnetcore-2.1#https)用來建立憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-275">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d4102-276">某些瀏覽器需要授與明確的許可權，以信任本機開發憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-276">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d4102-277">專案範本預設會將應用程式設定為在 HTTPS 上執行，並包含 Https 重新導向 [和 HSTS 支援](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="d4102-277">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d4102-278">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上呼叫 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法，來為 Kestrel 設定 URL 首碼和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-278">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d4102-279">`UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵和 `ASPNETCORE_URLS` 環境變數同樣有效，但卻有本節稍後註明的限制 (針對 HTTPS 端點組態必須有預設憑證可用)。</span><span class="sxs-lookup"><span data-stu-id="d4102-279">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d4102-280">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="d4102-280">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d4102-281">ConfigureEndpointDefaults (動作 \<ListenOptions>) </span><span class="sxs-lookup"><span data-stu-id="d4102-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d4102-282">指定組態 `Action` 以針對每個指定端點執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-282">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d4102-283">呼叫 `ConfigureEndpointDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-283">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="d4102-284">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d4102-285">ConfigureHttpsDefaults (動作 \<HttpsConnectionAdapterOptions>) </span><span class="sxs-lookup"><span data-stu-id="d4102-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d4102-286">指定組態 `Action` 以針對每個 HTTPS 端點執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-286">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d4102-287">呼叫 `ConfigureHttpsDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-287">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="d4102-288">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-288">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="d4102-289">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="d4102-289">Configure(IConfiguration)</span></span>

<span data-ttu-id="d4102-290">建立設定載入器來設定以 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作為輸入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-290">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d4102-291">組態的範圍必須限於 Kestrel 的組態區段。</span><span class="sxs-lookup"><span data-stu-id="d4102-291">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d4102-292">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d4102-292">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d4102-293">設定 Kestrel 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-293">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d4102-294">`ListenOptions.UseHttps` 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="d4102-294">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d4102-295">`UseHttps`：設定 Kestrel 以搭配預設憑證使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-295">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d4102-296">如果未設定預設憑證，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d4102-296">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="d4102-297">`ListenOptions.UseHttps` 參數：</span><span class="sxs-lookup"><span data-stu-id="d4102-297">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d4102-298">`filename` 是憑證檔案的路徑和檔案名稱，它相對於包含應用程式內容檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="d4102-298">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d4102-299">`password` 是存取 X.509 憑證資料所需的密碼。</span><span class="sxs-lookup"><span data-stu-id="d4102-299">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d4102-300">`configureOptions` 是設定 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-300">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d4102-301">傳回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="d4102-301">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d4102-302">`storeName` 是要從中載入憑證的憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="d4102-302">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d4102-303">`subject` 是憑證的主體名稱。</span><span class="sxs-lookup"><span data-stu-id="d4102-303">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d4102-304">`allowInvalid` 表示是否應該考慮無效的憑證，例如自我簽署憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-304">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d4102-305">`location` 是要從中載入憑證的存放區位置。</span><span class="sxs-lookup"><span data-stu-id="d4102-305">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d4102-306">`serverCertificate` 是 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-306">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d4102-307">在生產環境中，必須明確設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-307">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d4102-308">至少必須提供預設憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-308">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d4102-309">支援的組態描述如下：</span><span class="sxs-lookup"><span data-stu-id="d4102-309">Supported configurations described next:</span></span>

* <span data-ttu-id="d4102-310">無組態</span><span class="sxs-lookup"><span data-stu-id="d4102-310">No configuration</span></span>
* <span data-ttu-id="d4102-311">從組態取代預設憑證</span><span class="sxs-lookup"><span data-stu-id="d4102-311">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d4102-312">變更程式碼中的預設值</span><span class="sxs-lookup"><span data-stu-id="d4102-312">Change the defaults in code</span></span>

<span data-ttu-id="d4102-313">*無組態*</span><span class="sxs-lookup"><span data-stu-id="d4102-313">*No configuration*</span></span>

<span data-ttu-id="d4102-314">Kestrel 會接聽 `http://localhost:5000` 和 `https://localhost:5001` (如果預設憑證可用的話)。</span><span class="sxs-lookup"><span data-stu-id="d4102-314">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d4102-315">*從組態取代預設憑證*</span><span class="sxs-lookup"><span data-stu-id="d4102-315">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d4102-316">`CreateDefaultBuilder` 預設會呼叫 `Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-316">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d4102-317">Kestrel 可以使用預設的 HTTPS 應用程式設定組態結構描述。</span><span class="sxs-lookup"><span data-stu-id="d4102-317">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d4102-318">設定多個端點，包括 URL 和要使用的憑證－從磁碟上的檔案，或是從憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="d4102-318">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d4102-319">在下列 *appsettings.json* 範例中：</span><span class="sxs-lookup"><span data-stu-id="d4102-319">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d4102-320">將 **AllowInvalid** 設定為 `true`，允許使用無效的憑證 (例如，自我簽署憑證)。</span><span class="sxs-lookup"><span data-stu-id="d4102-320">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d4102-321">任何未指定憑證 (接下來範例中的 **HttpsDefaultCert**) 的 HTTPS 端點會回復為 [憑證]**[預設]** >  下定義的憑證或開發憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-321">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="d4102-322">除了針對任何憑證節點使用 [路徑] 和 [密碼]，還可以使用憑證存放區欄位指定憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-322">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d4102-323">例如，您可以將 **憑證**  >  **預設** 憑證指定為：</span><span class="sxs-lookup"><span data-stu-id="d4102-323">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d4102-324">結構描述附註：</span><span class="sxs-lookup"><span data-stu-id="d4102-324">Schema notes:</span></span>

* <span data-ttu-id="d4102-325">端點名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="d4102-325">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d4102-326">例如，`HTTPS` 和 `Https` 都有效。</span><span class="sxs-lookup"><span data-stu-id="d4102-326">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d4102-327">`Url` 參數對每個端點而言都是必要的。</span><span class="sxs-lookup"><span data-stu-id="d4102-327">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d4102-328">此參數的格式等同於最上層 `Urls` 組態參數，但是它限制為單一值。</span><span class="sxs-lookup"><span data-stu-id="d4102-328">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d4102-329">這些端點會取代最上層 `Urls` 組態中定義的端點，而不是新增至其中。</span><span class="sxs-lookup"><span data-stu-id="d4102-329">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d4102-330">透過 `Listen` 在程式碼中定義的端點，會與組態區段中定義的端點累計。</span><span class="sxs-lookup"><span data-stu-id="d4102-330">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d4102-331">`Certificate` 區段是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="d4102-331">The `Certificate` section is optional.</span></span> <span data-ttu-id="d4102-332">如果未指定 `Certificate` 區段，則會使用先前案例中所定義的預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-332">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d4102-333">如果沒有預設值可供使用，伺服器就會擲回例外狀況，且無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4102-333">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d4102-334">`Certificate`區段同時支援 **路徑** &ndash; **密碼** 和 **主體** &ndash; **存放區** 憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-334">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d4102-335">可以用這種方式定義任何數目的端點，只要它們不會導致連接埠衝突即可。</span><span class="sxs-lookup"><span data-stu-id="d4102-335">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d4102-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 會傳回 `KestrelConfigurationLoader` 與 `.Endpoint(string name, listenOptions => { })` 方法，此方法可用來補充已設定的端點設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="d4102-337">`KestrelServerOptions.ConfigurationLoader` 可以直接存取，以繼續逐一查看現有的載入器，例如所提供的載入器 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 。</span><span class="sxs-lookup"><span data-stu-id="d4102-337">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d4102-338">您可以在方法的選項中使用每個端點的設定區段， `Endpoint` 以便讀取自訂設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-338">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d4102-339">可以藉由使用另一個區段再次呼叫 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 而載入多個組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-339">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d4102-340">只會使用最後一個組態，除非在先前的執行個體上已明確呼叫 `Load`。</span><span class="sxs-lookup"><span data-stu-id="d4102-340">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d4102-341">中繼套件不會呼叫 `Load`，如此可能會取代其預設組態區段。</span><span class="sxs-lookup"><span data-stu-id="d4102-341">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d4102-342">`KestrelConfigurationLoader` 會將來自 `KestrelServerOptions` 的 API 的 `Listen` 系列鏡像為 `Endpoint` 多載，所以可在相同的位置設定程式碼和設定端點。</span><span class="sxs-lookup"><span data-stu-id="d4102-342">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d4102-343">這些多載不使用名稱，並且只使用來自組態的預設組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-343">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d4102-344">*變更程式碼中的預設值*</span><span class="sxs-lookup"><span data-stu-id="d4102-344">*Change the defaults in code*</span></span>

<span data-ttu-id="d4102-345">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 可以用來變更 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的預設設定，包括覆寫先前案例中指定的預設憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-345">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d4102-346">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 應該在設定任何端點之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-346">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="d4102-347">*SNI 的 Kestrel 支援*</span><span class="sxs-lookup"><span data-stu-id="d4102-347">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d4102-348">[伺服器名稱指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可以用於在相同的 IP 位址和連接埠上裝載多個網域。</span><span class="sxs-lookup"><span data-stu-id="d4102-348">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d4102-349">SNI 若要運作，用戶端會在 TLS 信號交換期間傳送安全工作階段的主機名稱給伺服器，讓伺服器可以提供正確的憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-349">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d4102-350">用戶端在 TLS 信號交換之後的安全工作階段期間，會使用所提供的憑證與伺服器進行加密通訊。</span><span class="sxs-lookup"><span data-stu-id="d4102-350">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d4102-351">Kestrel 透過 `ServerCertificateSelector` 回呼來支援 SNI。</span><span class="sxs-lookup"><span data-stu-id="d4102-351">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d4102-352">回呼會針對每個連線叫用一次，允許應用程式檢查主機名稱並選取適當的憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-352">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d4102-353">SNI 支援需要：</span><span class="sxs-lookup"><span data-stu-id="d4102-353">SNI support requires:</span></span>

* <span data-ttu-id="d4102-354">在目標 framework `netcoreapp2.1` 或更新版本上執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-354">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d4102-355">在 `net461` 或更新版本中，會叫用回呼，但 `name` 一律為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="d4102-355">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d4102-356">如果用戶端不在 TLS 信號交換中提供主機名稱參數，則 `name` 也是 `null`。</span><span class="sxs-lookup"><span data-stu-id="d4102-356">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d4102-357">所有網站都在相同的 Kestrel 執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-357">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d4102-358">在不使用反向 Proxy 的情況下，Kestrel 不支援跨多個執行個體共用 IP 位址和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-358">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="d4102-359">連接記錄</span><span class="sxs-lookup"><span data-stu-id="d4102-359">Connection logging</span></span>

<span data-ttu-id="d4102-360">呼叫 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以發出連接上位元組層級通訊的 Debug 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="d4102-360">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d4102-361">連線記錄有助於疑難排解低層級通訊中的問題，例如在 TLS 加密期間和 proxy 後方。</span><span class="sxs-lookup"><span data-stu-id="d4102-361">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d4102-362">如果 `UseConnectionLogging` 之前放置 `UseHttps` ，則會記錄加密的流量。</span><span class="sxs-lookup"><span data-stu-id="d4102-362">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d4102-363">如果 `UseConnectionLogging` 放置在之後 `UseHttps` ，則會記錄解密的流量。</span><span class="sxs-lookup"><span data-stu-id="d4102-363">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d4102-364">繫結至 TCP 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-364">Bind to a TCP socket</span></span>

<span data-ttu-id="d4102-365"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法會繫結至 TCP 通訊端，而選項 Lambda 則會允許 X.509 憑證設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-365">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="d4102-366">此範例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>來為端點設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-366">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d4102-367">若要設定特定端點的其他 Kestrel 設定，請使用相同的 API。</span><span class="sxs-lookup"><span data-stu-id="d4102-367">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d4102-368">繫結至 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-368">Bind to a Unix socket</span></span>

<span data-ttu-id="d4102-369">請使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 在 Unix 通訊端上進行接聽以改善 Nginx 的效能，如此範例所示：</span><span class="sxs-lookup"><span data-stu-id="d4102-369">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="d4102-370">在 Nginx 設定檔中，將 `server`  >  `location`  >  `proxy_pass` 專案設定為 `http://unix:/tmp/{KESTREL SOCKET}:/;` 。</span><span class="sxs-lookup"><span data-stu-id="d4102-370">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d4102-371">`{KESTREL SOCKET}` 這是提供給 (的通訊端名稱 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ，例如 `kestrel-test.sock` 上述範例) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-371">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d4102-372">確定 Nginx (可寫入通訊端，例如 `chmod go+w /tmp/kestrel-test.sock`) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-372">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="d4102-373">連接埠 0</span><span class="sxs-lookup"><span data-stu-id="d4102-373">Port 0</span></span>

<span data-ttu-id="d4102-374">指定連接埠號碼 `0` 時，Kestrel 會動態繫結至可用的連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-374">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d4102-375">下列範例示範如何判斷 Kestrel 在執行階段實際上繫結至哪一個連接埠：</span><span class="sxs-lookup"><span data-stu-id="d4102-375">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d4102-376">當應用程式執行時，主控台視窗輸出會指出可以連線到應用程式的動態連接埠：</span><span class="sxs-lookup"><span data-stu-id="d4102-376">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d4102-377">限制</span><span class="sxs-lookup"><span data-stu-id="d4102-377">Limitations</span></span>

<span data-ttu-id="d4102-378">使用下列方法來設定端點：</span><span class="sxs-lookup"><span data-stu-id="d4102-378">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d4102-379">`--urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="d4102-379">`--urls` command-line argument</span></span>
* <span data-ttu-id="d4102-380">`urls` 主機組態索引鍵</span><span class="sxs-lookup"><span data-stu-id="d4102-380">`urls` host configuration key</span></span>
* <span data-ttu-id="d4102-381">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="d4102-381">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d4102-382">要讓程式碼使用 Kestrel 以外的伺服器，這些方法會很有用。</span><span class="sxs-lookup"><span data-stu-id="d4102-382">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d4102-383">不過，請注意下列限制：</span><span class="sxs-lookup"><span data-stu-id="d4102-383">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d4102-384">HTTPS 無法與這些方法搭配使用，除非在 HTTPS 端點設定中提供預設憑證 (例如，使用 `KestrelServerOptions` 設定或設定檔，如本主題稍早所示)。</span><span class="sxs-lookup"><span data-stu-id="d4102-384">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d4102-385">當同時使用 `Listen` 和 `UseUrls` 方法時，`Listen` 端點會覆寫 `UseUrls` 端點。</span><span class="sxs-lookup"><span data-stu-id="d4102-385">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d4102-386">IIS 端點設定</span><span class="sxs-lookup"><span data-stu-id="d4102-386">IIS endpoint configuration</span></span>

<span data-ttu-id="d4102-387">使用 IIS 時，IIS 覆寫繫結的 URL 繫結是由 `Listen` 或 `UseUrls` 設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-387">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d4102-388">如需詳細資訊，請參閱 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)主題。</span><span class="sxs-lookup"><span data-stu-id="d4102-388">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="d4102-389">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="d4102-389">ListenOptions.Protocols</span></span>

<span data-ttu-id="d4102-390">`Protocols` 屬性會建立在連線端點上或針對伺服器啟用的 HTTP 通訊協定 (`HttpProtocols`)。</span><span class="sxs-lookup"><span data-stu-id="d4102-390">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="d4102-391">從 `HttpProtocols` 列舉中指派一個值給 `Protocols` 屬性。</span><span class="sxs-lookup"><span data-stu-id="d4102-391">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="d4102-392">`HttpProtocols` 列舉值</span><span class="sxs-lookup"><span data-stu-id="d4102-392">`HttpProtocols` enum value</span></span> | <span data-ttu-id="d4102-393">允許的連線通訊協定</span><span class="sxs-lookup"><span data-stu-id="d4102-393">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="d4102-394">僅限 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="d4102-394">HTTP/1.1 only.</span></span> <span data-ttu-id="d4102-395">可在具有或沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-395">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="d4102-396">僅限 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-396">HTTP/2 only.</span></span> <span data-ttu-id="d4102-397">只有在用戶端支援[先備知識模式](https://tools.ietf.org/html/rfc7540#section-3.4)時，才可以在沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-397">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="d4102-398">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-398">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="d4102-399">HTTP/2 要求用戶端在 TLS [應用層通訊協定協商 ](https://tools.ietf.org/html/rfc7301#section-3) 中選取 HTTP/2， (ALPN) 交握;否則，連接預設為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="d4102-399">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="d4102-400">`ListenOptions.Protocols`任何端點的預設值為 `HttpProtocols.Http1AndHttp2` 。</span><span class="sxs-lookup"><span data-stu-id="d4102-400">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="d4102-401">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="d4102-401">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="d4102-402">TLS 1.2 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="d4102-402">TLS version 1.2 or later</span></span>
* <span data-ttu-id="d4102-403">已停用重新交涉</span><span class="sxs-lookup"><span data-stu-id="d4102-403">Renegotiation disabled</span></span>
* <span data-ttu-id="d4102-404">已停用壓縮</span><span class="sxs-lookup"><span data-stu-id="d4102-404">Compression disabled</span></span>
* <span data-ttu-id="d4102-405">暫時金鑰交換大小下限：</span><span class="sxs-lookup"><span data-stu-id="d4102-405">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="d4102-406">橢圓曲線 Diffie-Hellman (>ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; ：最少224位</span><span class="sxs-lookup"><span data-stu-id="d4102-406">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="d4102-407">有限的欄位 Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; ：最低2048位</span><span class="sxs-lookup"><span data-stu-id="d4102-407">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="d4102-408">不禁止加密套件。</span><span class="sxs-lookup"><span data-stu-id="d4102-408">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="d4102-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack;預設支援使用 P-256 橢圓曲線 &lbrack; `FIPS186` &rbrack; 。</span><span class="sxs-lookup"><span data-stu-id="d4102-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="d4102-410">下列範例會允許連接埠 8000 上的 HTTP/1.1 和 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-410">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="d4102-411">這些連線使用提供的憑證受到 TLS 保護：</span><span class="sxs-lookup"><span data-stu-id="d4102-411">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="d4102-412">若有需要，請使用連線中介軟體針對特定加密篩選每個連接的 TLS 交握。</span><span class="sxs-lookup"><span data-stu-id="d4102-412">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="d4102-413">下列範例 <xref:System.NotSupportedException> 會針對應用程式不支援的任何加密演算法擲回。</span><span class="sxs-lookup"><span data-stu-id="d4102-413">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="d4102-414">或者，將 [ITlsHandshakeFeature](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 定義和比較成可接受的加密套件清單。</span><span class="sxs-lookup"><span data-stu-id="d4102-414">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="d4102-415">CipherAlgorithmType 不會使用加密 [。 Null](xref:System.Security.Authentication.CipherAlgorithmType) 加密演算法。</span><span class="sxs-lookup"><span data-stu-id="d4102-415">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="d4102-416">您也可以透過 lambda 設定連接篩選 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="d4102-416">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="d4102-417">在 Linux 上， <xref:System.Net.Security.CipherSuitesPolicy> 可用來篩選每個連接的 TLS 交握：</span><span class="sxs-lookup"><span data-stu-id="d4102-417">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="d4102-418">從設定進行通訊協定設定</span><span class="sxs-lookup"><span data-stu-id="d4102-418">*Set the protocol from configuration*</span></span>

<span data-ttu-id="d4102-419">`CreateDefaultBuilder` 預設會呼叫 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-419">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="d4102-420">下列 *appsettings.json* 範例會建立 HTTP/1.1 作為所有端點的預設連接通訊協定：</span><span class="sxs-lookup"><span data-stu-id="d4102-420">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="d4102-421">下列 *appsettings.json* 範例會建立特定端點的 HTTP/1.1 連接通訊協定：</span><span class="sxs-lookup"><span data-stu-id="d4102-421">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="d4102-422">程式碼中指定的通訊協定會覆寫設定所設定的值。</span><span class="sxs-lookup"><span data-stu-id="d4102-422">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="d4102-423">URL 前置詞</span><span class="sxs-lookup"><span data-stu-id="d4102-423">URL prefixes</span></span>

<span data-ttu-id="d4102-424">使用 `UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵或 `ASPNETCORE_URLS` 環境變數時，URL 前置詞可以採用下列任一格式。</span><span class="sxs-lookup"><span data-stu-id="d4102-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d4102-425">只有 HTTP URL 前置詞有效。</span><span class="sxs-lookup"><span data-stu-id="d4102-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d4102-426">使用 `UseUrls` 來設定 URL 繫結時，Kestrel 不支援 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d4102-427">IPv4 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d4102-428">`0.0.0.0` 是繫結至所有 IPv4 位址的特殊情況。</span><span class="sxs-lookup"><span data-stu-id="d4102-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d4102-429">IPv6 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d4102-430">`[::]` 是相當於 IPv4 `0.0.0.0` 的 IPv6 對等項目。</span><span class="sxs-lookup"><span data-stu-id="d4102-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d4102-431">主機名稱與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d4102-432">主機名稱 `*` 和 `+` 並不特殊。</span><span class="sxs-lookup"><span data-stu-id="d4102-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d4102-433">無法辨識為有效 IP 位址或 `localhost` 的任何項目，都會繫結至所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="d4102-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d4102-434">若要在相同連接埠上將不同的主機名稱繫結至不同的 ASP.NET Core 應用程式，請使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向 Proxy 伺服器 (例如 IIS、Nginx 或 Apache)。</span><span class="sxs-lookup"><span data-stu-id="d4102-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d4102-435">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="d4102-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d4102-436">主機 `localhost` 名稱與連接埠號碼，或回送 IP 與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d4102-437">如果指定 `localhost`，Kestrel 會嘗試同時繫結至 IPv4 和 IPv6 回送介面。</span><span class="sxs-lookup"><span data-stu-id="d4102-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d4102-438">如果所要求的連接埠在任一個回送介面上由另一個服務使用，則 Kestrel 無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4102-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d4102-439">如果任一回送介面由於任何其他原因 (最常見的原因是不支援 IPv6) 無法使用，Kestrel 就會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="d4102-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d4102-440">主機篩選</span><span class="sxs-lookup"><span data-stu-id="d4102-440">Host filtering</span></span>

<span data-ttu-id="d4102-441">雖然 Kestrel 根據前置詞來支援組態，例如 `http://example.com:5000`，Kestrel 大多會忽略主機名稱。</span><span class="sxs-lookup"><span data-stu-id="d4102-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d4102-442">主機 `localhost` 是特殊情況，用來繫結到回送位址。</span><span class="sxs-lookup"><span data-stu-id="d4102-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d4102-443">任何非明確 IP 位址的主機，會繫結至所有公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="d4102-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d4102-444">`Host` 標頭未驗證。</span><span class="sxs-lookup"><span data-stu-id="d4102-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d4102-445">因應措施是使用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d4102-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d4102-446">主機篩選中介軟體是由 [AspNetCore HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 套件所提供，該套件是針對 ASP.NET Core 應用程式隱含提供的。</span><span class="sxs-lookup"><span data-stu-id="d4102-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="d4102-447">中介軟體是由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 所新增，它會呼叫 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>：</span><span class="sxs-lookup"><span data-stu-id="d4102-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d4102-448">預設停用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d4102-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d4102-449">若要啟用中介軟體，請 `AllowedHosts` 在 *appsettings.json* / *appsettings 中定義 \<EnvironmentName> 金鑰。json*。</span><span class="sxs-lookup"><span data-stu-id="d4102-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d4102-450">此值是以分號分隔的主機名稱清單，不含連接埠號碼：</span><span class="sxs-lookup"><span data-stu-id="d4102-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d4102-451">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d4102-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d4102-452">[轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)也有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 選項。</span><span class="sxs-lookup"><span data-stu-id="d4102-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d4102-453">在不同的案例中，轉送標頭中介軟體和主機篩選中介軟體有類似的功能。</span><span class="sxs-lookup"><span data-stu-id="d4102-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d4102-454">當不保留 `Host` 標頭，卻使用反向 Proxy 伺服器或負載平衡器轉送要求時，可使用轉送標頭中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="d4102-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d4102-455">當使用 Kestrel 作為公眾對應 Edge Server，或直接轉送 `Host` 標頭時，可使用主機篩選中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="d4102-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d4102-456">如需轉送標頭中介軟體的詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="d4102-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="d4102-457">Libuv 傳輸設定</span><span class="sxs-lookup"><span data-stu-id="d4102-457">Libuv transport configuration</span></span>

<span data-ttu-id="d4102-458">對於需要使用 Libuv () 的專案 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> ：</span><span class="sxs-lookup"><span data-stu-id="d4102-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="d4102-459">將套件的相依性新增 [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) 至應用程式的專案檔案：</span><span class="sxs-lookup"><span data-stu-id="d4102-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d4102-460"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>在上呼叫 `IWebHostBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="d4102-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

## <a name="http11-request-draining"></a><span data-ttu-id="d4102-461">HTTP/1.1 要求清空</span><span class="sxs-lookup"><span data-stu-id="d4102-461">HTTP/1.1 request draining</span></span>

<span data-ttu-id="d4102-462">開啟 HTTP 連接相當耗時。</span><span class="sxs-lookup"><span data-stu-id="d4102-462">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="d4102-463">若是 HTTPS，也需要大量資源。</span><span class="sxs-lookup"><span data-stu-id="d4102-463">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="d4102-464">因此，Kestrel 會嘗試針對每個 HTTP/1.1 通訊協定重複使用連接。</span><span class="sxs-lookup"><span data-stu-id="d4102-464">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="d4102-465">要求主體必須完全取用，才能重複使用連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-465">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="d4102-466">應用程式不一定會取用要求主體，例如伺服器傳回重新 `POST` 導向或404回應的要求。</span><span class="sxs-lookup"><span data-stu-id="d4102-466">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="d4102-467">在 `POST` -重新導向案例中：</span><span class="sxs-lookup"><span data-stu-id="d4102-467">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="d4102-468">用戶端可能已經傳送部分 `POST` 資料。</span><span class="sxs-lookup"><span data-stu-id="d4102-468">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="d4102-469">伺服器會寫入301回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-469">The server writes the 301 response.</span></span>
* <span data-ttu-id="d4102-470">連線無法用於新的要求，直到 `POST` 前一個要求主體的資料已完全讀取為止。</span><span class="sxs-lookup"><span data-stu-id="d4102-470">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="d4102-471">Kestrel 會嘗試清空要求主體。</span><span class="sxs-lookup"><span data-stu-id="d4102-471">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="d4102-472">清空要求主體表示在不處理資料的情況下讀取和捨棄資料。</span><span class="sxs-lookup"><span data-stu-id="d4102-472">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="d4102-473">清空程式可讓您在允許連線重複使用時，以及清空任何剩餘資料所需的時間之間進行 tradoff：</span><span class="sxs-lookup"><span data-stu-id="d4102-473">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="d4102-474">清空有五秒的時間，無法設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-474">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="d4102-475">如果或標頭所指定的所有資料在 `Content-Length` `Transfer-Encoding` 此時間之前未被讀取，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="d4102-475">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="d4102-476">有時您可能會想要在寫入回應之前或之後立即終止要求。</span><span class="sxs-lookup"><span data-stu-id="d4102-476">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="d4102-477">例如，用戶端可能會有限制性的資料上限，因此限制上傳的資料可能是優先考慮。</span><span class="sxs-lookup"><span data-stu-id="d4102-477">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="d4102-478">在這種情況下，若要終止要求，請從控制器、頁面或中介軟體呼叫[HttpCoNtext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) 。 Razor</span><span class="sxs-lookup"><span data-stu-id="d4102-478">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="d4102-479">呼叫時有一些注意事項 `Abort` ：</span><span class="sxs-lookup"><span data-stu-id="d4102-479">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="d4102-480">建立新的連接可能很慢且昂貴。</span><span class="sxs-lookup"><span data-stu-id="d4102-480">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="d4102-481">在連接關閉之前，不保證用戶端已讀取回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-481">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="d4102-482">呼叫 `Abort` 應該很罕見，而且會保留給嚴重的錯誤案例，而不是常見的錯誤。</span><span class="sxs-lookup"><span data-stu-id="d4102-482">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="d4102-483">只有 `Abort` 在需要解決特定問題時才呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-483">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="d4102-484">例如， `Abort` 如果惡意用戶端嘗試 `POST` 資料或用戶端程式代碼中有錯誤，而導致大量或許多要求，請呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-484">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="d4102-485">請勿呼叫 `Abort` 常見的錯誤狀況，例如 HTTP 404 (找不到) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-485">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="d4102-486">在呼叫之前呼叫 [HttpResponse](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) ， `Abort` 可確保伺服器已完成寫入回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-486">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="d4102-487">不過，用戶端行為不是可預測的，而且在中斷連接之前，可能不會讀取回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-487">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="d4102-488">HTTP/2 的這個程式不同，因為通訊協定支援中止個別要求資料流程，而不需要關閉連接。</span><span class="sxs-lookup"><span data-stu-id="d4102-488">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="d4102-489">五秒的清空超時時間不適用。</span><span class="sxs-lookup"><span data-stu-id="d4102-489">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="d4102-490">如果在完成回應之後有任何未讀取的要求主體資料，則伺服器會傳送 HTTP/2 RST 框架。</span><span class="sxs-lookup"><span data-stu-id="d4102-490">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="d4102-491">系統會忽略其他要求主體資料框架。</span><span class="sxs-lookup"><span data-stu-id="d4102-491">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="d4102-492">可能的話，最好是讓用戶端利用預期的 [： 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 要求標頭，並等待伺服器回應，然後再開始傳送要求本文。</span><span class="sxs-lookup"><span data-stu-id="d4102-492">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="d4102-493">這可讓用戶端有機會檢查回應，並在傳送不需要的資料之前中止。</span><span class="sxs-lookup"><span data-stu-id="d4102-493">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4102-494">其他資源</span><span class="sxs-lookup"><span data-stu-id="d4102-494">Additional resources</span></span>

* <span data-ttu-id="d4102-495">在 Linux 上使用 UNIX 通訊端時，通訊端不會在應用程式關閉時自動刪除。</span><span class="sxs-lookup"><span data-stu-id="d4102-495">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d4102-496">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="d4102-496">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d4102-497">RFC 7230：訊息語法和路由 (第 5.4 節：主機)</span><span class="sxs-lookup"><span data-stu-id="d4102-497">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d4102-498">Kestrel 是 [ASP.NET Core 的跨平台網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="d4102-498">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d4102-499">Kestrel 是 ASP.NET Core 專案範本中預設隨附的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="d4102-499">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d4102-500">Kestrel 支援下列案例：</span><span class="sxs-lookup"><span data-stu-id="d4102-500">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d4102-501">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d4102-501">HTTPS</span></span>
* <span data-ttu-id="d4102-502">用來啟用 [WebSockets](https://github.com/aspnet/websockets) 的不透明升級</span><span class="sxs-lookup"><span data-stu-id="d4102-502">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d4102-503">Nginx 背後的高效能 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-503">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="d4102-504">HTTP/2 (macOS 上除外&dagger;)</span><span class="sxs-lookup"><span data-stu-id="d4102-504">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="d4102-505">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-505">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d4102-506">.NET Core 支援的所有平台和版本都支援 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-506">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d4102-507">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4102-507">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="d4102-508">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="d4102-508">HTTP/2 support</span></span>

<span data-ttu-id="d4102-509">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式使用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="d4102-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d4102-510">作業系統&dagger;</span><span class="sxs-lookup"><span data-stu-id="d4102-510">Operating system&dagger;</span></span>
  * <span data-ttu-id="d4102-511">Windows Server 2016/Windows 10 或更新版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="d4102-511">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="d4102-512">Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="d4102-512">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="d4102-513">目標 Framework：.NET Core 2.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d4102-513">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="d4102-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="d4102-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d4102-515">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="d4102-515">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d4102-516">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-516">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="d4102-517">&Dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。</span><span class="sxs-lookup"><span data-stu-id="d4102-517">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d4102-518">支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。</span><span class="sxs-lookup"><span data-stu-id="d4102-518">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d4102-519">可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-519">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="d4102-520">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="d4102-520">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d4102-521">預設會停用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-521">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="d4102-522">如需組態的詳細資訊，請參閱 [Kestrel 選項](#kestrel-options)和 [ListenOptions. 通訊協定](#listenoptionsprotocols)一節。</span><span class="sxs-lookup"><span data-stu-id="d4102-522">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d4102-523">何時搭配使用 Kestrel 與反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="d4102-523">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d4102-524">您可以單獨使用 Kestrel，或與 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 等「反向 Proxy 伺服器」搭配使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-524">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d4102-525">反向 Proxy 伺服器會從網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-525">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d4102-526">Kestrel 用作邊緣 (網際網路對應) 網頁伺服器：</span><span class="sxs-lookup"><span data-stu-id="d4102-526">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d4102-528">Kestrel 用於反向 Proxy 組態中：</span><span class="sxs-lookup"><span data-stu-id="d4102-528">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d4102-530">不論是否有反向 proxy 伺服器，設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-530">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d4102-531">Kestrel 用作不需要反向 Proxy 伺服器的 Edge Server 時，不支援在多個處理序之間共用相同的 IP 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-531">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d4102-532">當 Kestrel 設定為接聽連接埠時，Kestrel 會處理該連接埠的所有流量，而不論要求的 `Host` 標頭為何。</span><span class="sxs-lookup"><span data-stu-id="d4102-532">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d4102-533">可以共用連接埠的反向 Proxy 能夠在唯一的 IP 和連接埠上轉送要求給 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-533">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d4102-534">即使不需要反向 Proxy 伺服器，使用反向 Proxy 伺服器也是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="d4102-534">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d4102-535">反向 Proxy：</span><span class="sxs-lookup"><span data-stu-id="d4102-535">A reverse proxy:</span></span>

* <span data-ttu-id="d4102-536">可以限制它所主控之應用程式的公開介面區。</span><span class="sxs-lookup"><span data-stu-id="d4102-536">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d4102-537">提供額外的組態和防禦層。</span><span class="sxs-lookup"><span data-stu-id="d4102-537">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d4102-538">能夠與現有基礎結構更好地整合。</span><span class="sxs-lookup"><span data-stu-id="d4102-538">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d4102-539">簡化負載平衡和安全通訊 (HTTPS) 組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-539">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d4102-540">只有反向 proxy 伺服器需要 x.509 憑證，而且該伺服器可以使用一般 HTTP 與內部網路上的應用程式伺服器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="d4102-540">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d4102-541">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="d4102-541">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d4102-542">如何在 ASP.NET Core 應用程式中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="d4102-542">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d4102-543">AspNetCore 會包含在[AspNetCore.. 中繼套件](xref:fundamentals/metapackage-app). [Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)套件中。</span><span class="sxs-lookup"><span data-stu-id="d4102-543">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d4102-544">ASP.NET Core 專案範本預設會使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-544">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d4102-545">在 *Program.cs* 中，範本程式碼會呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，而後者會在幕後呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="d4102-545">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="d4102-546">如需 `CreateDefaultBuilder` 和建立主機的詳細資訊，請參閱的 *設定主機* 一節 <xref:fundamentals/host/web-host#set-up-a-host> 。</span><span class="sxs-lookup"><span data-stu-id="d4102-546">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="d4102-547">若要在呼叫 `CreateDefaultBuilder` 之後提供額外的設定，請使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="d4102-547">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="d4102-548">如果應用程式未呼叫 `CreateDefaultBuilder` 來設定主機，請在呼叫 `ConfigureKestrel` **之前**，先呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="d4102-548">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="d4102-549">Kestrel 選項</span><span class="sxs-lookup"><span data-stu-id="d4102-549">Kestrel options</span></span>

<span data-ttu-id="d4102-550">Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。</span><span class="sxs-lookup"><span data-stu-id="d4102-550">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d4102-551">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。</span><span class="sxs-lookup"><span data-stu-id="d4102-551">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d4102-552">`Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4102-552">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d4102-553">下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；</span><span class="sxs-lookup"><span data-stu-id="d4102-553">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d4102-554">您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項（在下列範例中以 c # 程式碼設定）。</span><span class="sxs-lookup"><span data-stu-id="d4102-554">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d4102-555">例如，檔案設定提供者可以從或 appsettings 載入 Kestrel 設定 *appsettings.json* *。環境}. json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="d4102-555">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="d4102-556">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="d4102-556">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d4102-557">設定 Kestrel in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d4102-557">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d4102-558">將的實例插入 `IConfiguration` 至 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="d4102-558">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d4102-559">下列範例假設將插入的設定指派給 `Configuration` 屬性。</span><span class="sxs-lookup"><span data-stu-id="d4102-559">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d4102-560">在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-560">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="d4102-561">設定 Kestrel 建立主機時：</span><span class="sxs-lookup"><span data-stu-id="d4102-561">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d4102-562">在 *Program.cs* 中，將 `Kestrel` Configuration 區段載入 Kestrel 的設定中：</span><span class="sxs-lookup"><span data-stu-id="d4102-562">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="d4102-563">上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-563">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d4102-564">Keep-alive 逾時</span><span class="sxs-lookup"><span data-stu-id="d4102-564">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d4102-565">取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="d4102-565">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d4102-566">預設為 2 分鐘。</span><span class="sxs-lookup"><span data-stu-id="d4102-566">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d4102-567">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="d4102-567">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d4102-568">可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：</span><span class="sxs-lookup"><span data-stu-id="d4102-568">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d4102-569">已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-569">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d4102-570">升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-570">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d4102-571">連線數目上限預設為無限制 (null)。</span><span class="sxs-lookup"><span data-stu-id="d4102-571">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d4102-572">要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="d4102-572">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d4102-573">預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="d4102-573">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d4102-574">若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：</span><span class="sxs-lookup"><span data-stu-id="d4102-574">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d4102-575">以下範例會示範如何設定應用程式、每個要求的條件約束：</span><span class="sxs-lookup"><span data-stu-id="d4102-575">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d4102-576">覆寫中介軟體中特定要求的設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-576">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d4102-577">如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d4102-577">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d4102-578">有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="d4102-578">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d4102-579">當應用程式是在 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)後方於[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)執行時，Kestrel 的要求本文大小限制將會被停用，因為 IIS 已經設定限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-579">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d4102-580">要求主體資料速率下限</span><span class="sxs-lookup"><span data-stu-id="d4102-580">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d4102-581">如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。</span><span class="sxs-lookup"><span data-stu-id="d4102-581">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d4102-582">如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 讓用戶端提高其傳送速率的時間量（最小值）;在這段時間內不會檢查速率。</span><span class="sxs-lookup"><span data-stu-id="d4102-582">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d4102-583">寬限期可協助避免中斷連線，這是由於 TCP 緩慢啟動而一開始以低速傳送資料所造成。</span><span class="sxs-lookup"><span data-stu-id="d4102-583">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d4102-584">預設速率下限為 240 個位元組/秒，寬限期為 5 秒。</span><span class="sxs-lookup"><span data-stu-id="d4102-584">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d4102-585">速率下限也適用於回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-585">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d4102-586">除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。</span><span class="sxs-lookup"><span data-stu-id="d4102-586">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d4102-587">以下範例示範如何在 *Program.cs* 中設定資料速率下限：</span><span class="sxs-lookup"><span data-stu-id="d4102-587">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="d4102-588">覆寫中介軟體中每個要求的最小速率限制：</span><span class="sxs-lookup"><span data-stu-id="d4102-588">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d4102-589">因為通訊協定對要求多工的支援，所以 HTTP/2 不支援以每一要求基礎修改速率限制，進而使先前範例中所參考的所有速率功能都不會出現在 HTTP/2 要求的 `HttpContext.Features` 中。</span><span class="sxs-lookup"><span data-stu-id="d4102-589">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d4102-590">透過 `KestrelServerOptions.Limits` 設定的全伺服器速率限制皆仍套用至 HTTP/1.x 及 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-590">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d4102-591">要求標頭逾時</span><span class="sxs-lookup"><span data-stu-id="d4102-591">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d4102-592">取得或設定伺服器花費在接收要求標頭的時間上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-592">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d4102-593">預設為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4102-593">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d4102-594">每個連線的資料流數目上限</span><span class="sxs-lookup"><span data-stu-id="d4102-594">Maximum streams per connection</span></span>

<span data-ttu-id="d4102-595">`Http2.MaxStreamsPerConnection` 會限制每個 HTTP/2 連線的同時要求資料流數目。</span><span class="sxs-lookup"><span data-stu-id="d4102-595">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d4102-596">超出的資料流會被拒絕。</span><span class="sxs-lookup"><span data-stu-id="d4102-596">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="d4102-597">預設值是 100。</span><span class="sxs-lookup"><span data-stu-id="d4102-597">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d4102-598">標頭表格大小</span><span class="sxs-lookup"><span data-stu-id="d4102-598">Header table size</span></span>

<span data-ttu-id="d4102-599">HPACK 解碼器可解壓縮 HTTP/2 連線的 HTTP 標頭。</span><span class="sxs-lookup"><span data-stu-id="d4102-599">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d4102-600">`Http2.HeaderTableSize` 會限制 HPACK 解碼器所使用的標頭壓縮表格大小。</span><span class="sxs-lookup"><span data-stu-id="d4102-600">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d4102-601">這個值是以八位元提供，而且必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="d4102-601">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="d4102-602">預設值為 4096。</span><span class="sxs-lookup"><span data-stu-id="d4102-602">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d4102-603">框架大小上限</span><span class="sxs-lookup"><span data-stu-id="d4102-603">Maximum frame size</span></span>

<span data-ttu-id="d4102-604">`Http2.MaxFrameSize` 表示要接收的 HTTP/2 連線框架承載大小上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-604">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="d4102-605">這個值是以八位元提供，而且必須介於 2^14 (16,384) 到 2^24-1 (16,777,215) 之間。</span><span class="sxs-lookup"><span data-stu-id="d4102-605">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="d4102-606">預設值為 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="d4102-606">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d4102-607">要求標頭大小上限</span><span class="sxs-lookup"><span data-stu-id="d4102-607">Maximum request header size</span></span>

<span data-ttu-id="d4102-608">`Http2.MaxRequestHeaderFieldSize` 以八位元表示要求標頭值的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-608">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d4102-609">此限制皆共同套用至已壓縮及未壓縮代表中的名稱與值。</span><span class="sxs-lookup"><span data-stu-id="d4102-609">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d4102-610">此值必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="d4102-610">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="d4102-611">預設值為 8,192。</span><span class="sxs-lookup"><span data-stu-id="d4102-611">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d4102-612">初始連線視窗大小</span><span class="sxs-lookup"><span data-stu-id="d4102-612">Initial connection window size</span></span>

<span data-ttu-id="d4102-613">`Http2.InitialConnectionWindowSize` 會以位元組表示伺服器緩衝每個連線之所有要求 (資料流) 單次彙總的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-613">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d4102-614">要求也皆受 `Http2.InitialStreamWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-614">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d4102-615">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="d4102-615">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="d4102-616">預設值為 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="d4102-616">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d4102-617">初始資料流視窗大小</span><span class="sxs-lookup"><span data-stu-id="d4102-617">Initial stream window size</span></span>

<span data-ttu-id="d4102-618">`Http2.InitialStreamWindowSize` 會以位元組表示每個要求 (資料流) 單次伺服器緩衝的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-618">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d4102-619">要求也皆受 `Http2.InitialStreamWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-619">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d4102-620">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="d4102-620">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="d4102-621">預設值為 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="d4102-621">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="d4102-622">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="d4102-622">Synchronous I/O</span></span>

<span data-ttu-id="d4102-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="d4102-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d4102-624">預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="d4102-624">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d4102-625">大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-625">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d4102-626">只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。</span><span class="sxs-lookup"><span data-stu-id="d4102-626">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d4102-627">下列範例會啟用同步 i/o：</span><span class="sxs-lookup"><span data-stu-id="d4102-627">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d4102-628">如需其他 Kestrel 選項和限制的資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="d4102-628">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d4102-629">端點組態</span><span class="sxs-lookup"><span data-stu-id="d4102-629">Endpoint configuration</span></span>

<span data-ttu-id="d4102-630">ASP.NET Core 預設會繫結至：</span><span class="sxs-lookup"><span data-stu-id="d4102-630">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d4102-631">`https://localhost:5001` (當有本機開發憑證存在時)</span><span class="sxs-lookup"><span data-stu-id="d4102-631">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d4102-632">使用以下各項指定 URL：</span><span class="sxs-lookup"><span data-stu-id="d4102-632">Specify URLs using the:</span></span>

* <span data-ttu-id="d4102-633">`ASPNETCORE_URLS` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="d4102-633">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d4102-634">`--urls` 命令列引數。</span><span class="sxs-lookup"><span data-stu-id="d4102-634">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d4102-635">`urls` 主機組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d4102-635">`urls` host configuration key.</span></span>
* <span data-ttu-id="d4102-636">`UseUrls` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="d4102-636">`UseUrls` extension method.</span></span>

<span data-ttu-id="d4102-637">使用這些方法提供的值可以是一或多個 HTTP 和 HTTPS 端點 (如果有預設憑證可用則為 HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="d4102-637">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d4102-638">將值設定為以分號分隔的清單 (例如，`"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="d4102-638">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d4102-639">如需有關這些方法的詳細資訊，請參閱[伺服器 URL](xref:fundamentals/host/web-host#server-urls) 和[覆寫設定](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="d4102-639">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d4102-640">開發憑證會建立於：</span><span class="sxs-lookup"><span data-stu-id="d4102-640">A development certificate is created:</span></span>

* <span data-ttu-id="d4102-641">已安裝 [.NET Core SDK](/dotnet/core/sdk) 時。</span><span class="sxs-lookup"><span data-stu-id="d4102-641">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d4102-642">[dev-certs 工具](xref:aspnetcore-2.1#https)用來建立憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-642">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d4102-643">某些瀏覽器需要授與明確的許可權，以信任本機開發憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-643">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d4102-644">專案範本預設會將應用程式設定為在 HTTPS 上執行，並包含 Https 重新導向 [和 HSTS 支援](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="d4102-644">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d4102-645">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上呼叫 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法，來為 Kestrel 設定 URL 首碼和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-645">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d4102-646">`UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵和 `ASPNETCORE_URLS` 環境變數同樣有效，但卻有本節稍後註明的限制 (針對 HTTPS 端點組態必須有預設憑證可用)。</span><span class="sxs-lookup"><span data-stu-id="d4102-646">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d4102-647">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="d4102-647">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d4102-648">ConfigureEndpointDefaults (動作 \<ListenOptions>) </span><span class="sxs-lookup"><span data-stu-id="d4102-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d4102-649">指定組態 `Action` 以針對每個指定端點執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-649">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d4102-650">呼叫 `ConfigureEndpointDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-650">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="d4102-651">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-651">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d4102-652">ConfigureHttpsDefaults (動作 \<HttpsConnectionAdapterOptions>) </span><span class="sxs-lookup"><span data-stu-id="d4102-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d4102-653">指定組態 `Action` 以針對每個 HTTPS 端點執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-653">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d4102-654">呼叫 `ConfigureHttpsDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-654">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="d4102-655">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-655">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="d4102-656">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="d4102-656">Configure(IConfiguration)</span></span>

<span data-ttu-id="d4102-657">建立設定載入器來設定以 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作為輸入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-657">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d4102-658">組態的範圍必須限於 Kestrel 的組態區段。</span><span class="sxs-lookup"><span data-stu-id="d4102-658">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d4102-659">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d4102-659">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d4102-660">設定 Kestrel 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-660">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d4102-661">`ListenOptions.UseHttps` 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="d4102-661">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d4102-662">`UseHttps`：設定 Kestrel 以搭配預設憑證使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-662">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d4102-663">如果未設定預設憑證，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d4102-663">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="d4102-664">`ListenOptions.UseHttps` 參數：</span><span class="sxs-lookup"><span data-stu-id="d4102-664">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d4102-665">`filename` 是憑證檔案的路徑和檔案名稱，它相對於包含應用程式內容檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="d4102-665">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d4102-666">`password` 是存取 X.509 憑證資料所需的密碼。</span><span class="sxs-lookup"><span data-stu-id="d4102-666">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d4102-667">`configureOptions` 是設定 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-667">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d4102-668">傳回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="d4102-668">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d4102-669">`storeName` 是要從中載入憑證的憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="d4102-669">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d4102-670">`subject` 是憑證的主體名稱。</span><span class="sxs-lookup"><span data-stu-id="d4102-670">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d4102-671">`allowInvalid` 表示是否應該考慮無效的憑證，例如自我簽署憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-671">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d4102-672">`location` 是要從中載入憑證的存放區位置。</span><span class="sxs-lookup"><span data-stu-id="d4102-672">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d4102-673">`serverCertificate` 是 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-673">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d4102-674">在生產環境中，必須明確設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-674">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d4102-675">至少必須提供預設憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-675">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d4102-676">支援的組態描述如下：</span><span class="sxs-lookup"><span data-stu-id="d4102-676">Supported configurations described next:</span></span>

* <span data-ttu-id="d4102-677">無組態</span><span class="sxs-lookup"><span data-stu-id="d4102-677">No configuration</span></span>
* <span data-ttu-id="d4102-678">從組態取代預設憑證</span><span class="sxs-lookup"><span data-stu-id="d4102-678">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d4102-679">變更程式碼中的預設值</span><span class="sxs-lookup"><span data-stu-id="d4102-679">Change the defaults in code</span></span>

<span data-ttu-id="d4102-680">*無組態*</span><span class="sxs-lookup"><span data-stu-id="d4102-680">*No configuration*</span></span>

<span data-ttu-id="d4102-681">Kestrel 會接聽 `http://localhost:5000` 和 `https://localhost:5001` (如果預設憑證可用的話)。</span><span class="sxs-lookup"><span data-stu-id="d4102-681">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d4102-682">*從組態取代預設憑證*</span><span class="sxs-lookup"><span data-stu-id="d4102-682">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d4102-683">`CreateDefaultBuilder` 預設會呼叫 `Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-683">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d4102-684">Kestrel 可以使用預設的 HTTPS 應用程式設定組態結構描述。</span><span class="sxs-lookup"><span data-stu-id="d4102-684">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d4102-685">設定多個端點，包括 URL 和要使用的憑證－從磁碟上的檔案，或是從憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="d4102-685">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d4102-686">在下列 *appsettings.json* 範例中：</span><span class="sxs-lookup"><span data-stu-id="d4102-686">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d4102-687">將 **AllowInvalid** 設定為 `true`，允許使用無效的憑證 (例如，自我簽署憑證)。</span><span class="sxs-lookup"><span data-stu-id="d4102-687">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d4102-688">任何未指定憑證 (接下來範例中的 **HttpsDefaultCert**) 的 HTTPS 端點會回復為 [憑證]**[預設]** >  下定義的憑證或開發憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-688">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="d4102-689">除了針對任何憑證節點使用 [路徑] 和 [密碼]，還可以使用憑證存放區欄位指定憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-689">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d4102-690">例如，您可以將 **憑證**  >  **預設** 憑證指定為：</span><span class="sxs-lookup"><span data-stu-id="d4102-690">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d4102-691">結構描述附註：</span><span class="sxs-lookup"><span data-stu-id="d4102-691">Schema notes:</span></span>

* <span data-ttu-id="d4102-692">端點名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="d4102-692">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d4102-693">例如，`HTTPS` 和 `Https` 都有效。</span><span class="sxs-lookup"><span data-stu-id="d4102-693">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d4102-694">`Url` 參數對每個端點而言都是必要的。</span><span class="sxs-lookup"><span data-stu-id="d4102-694">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d4102-695">此參數的格式等同於最上層 `Urls` 組態參數，但是它限制為單一值。</span><span class="sxs-lookup"><span data-stu-id="d4102-695">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d4102-696">這些端點會取代最上層 `Urls` 組態中定義的端點，而不是新增至其中。</span><span class="sxs-lookup"><span data-stu-id="d4102-696">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d4102-697">透過 `Listen` 在程式碼中定義的端點，會與組態區段中定義的端點累計。</span><span class="sxs-lookup"><span data-stu-id="d4102-697">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d4102-698">`Certificate` 區段是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="d4102-698">The `Certificate` section is optional.</span></span> <span data-ttu-id="d4102-699">如果未指定 `Certificate` 區段，則會使用先前案例中所定義的預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-699">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d4102-700">如果沒有預設值可供使用，伺服器就會擲回例外狀況，且無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4102-700">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d4102-701">`Certificate`區段同時支援 **路徑** &ndash; **密碼** 和 **主體** &ndash; **存放區** 憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-701">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d4102-702">可以用這種方式定義任何數目的端點，只要它們不會導致連接埠衝突即可。</span><span class="sxs-lookup"><span data-stu-id="d4102-702">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d4102-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 會傳回 `KestrelConfigurationLoader` 與 `.Endpoint(string name, listenOptions => { })` 方法，此方法可用來補充已設定的端點設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="d4102-704">`KestrelServerOptions.ConfigurationLoader` 可以直接存取，以繼續逐一查看現有的載入器，例如所提供的載入器 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 。</span><span class="sxs-lookup"><span data-stu-id="d4102-704">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d4102-705">您可以在方法的選項中使用每個端點的設定區段， `Endpoint` 以便讀取自訂設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-705">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d4102-706">可以藉由使用另一個區段再次呼叫 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 而載入多個組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-706">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d4102-707">只會使用最後一個組態，除非在先前的執行個體上已明確呼叫 `Load`。</span><span class="sxs-lookup"><span data-stu-id="d4102-707">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d4102-708">中繼套件不會呼叫 `Load`，如此可能會取代其預設組態區段。</span><span class="sxs-lookup"><span data-stu-id="d4102-708">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d4102-709">`KestrelConfigurationLoader` 會將來自 `KestrelServerOptions` 的 API 的 `Listen` 系列鏡像為 `Endpoint` 多載，所以可在相同的位置設定程式碼和設定端點。</span><span class="sxs-lookup"><span data-stu-id="d4102-709">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d4102-710">這些多載不使用名稱，並且只使用來自組態的預設組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-710">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d4102-711">*變更程式碼中的預設值*</span><span class="sxs-lookup"><span data-stu-id="d4102-711">*Change the defaults in code*</span></span>

<span data-ttu-id="d4102-712">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 可以用來變更 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的預設設定，包括覆寫先前案例中指定的預設憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-712">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d4102-713">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 應該在設定任何端點之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-713">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="d4102-714">*SNI 的 Kestrel 支援*</span><span class="sxs-lookup"><span data-stu-id="d4102-714">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d4102-715">[伺服器名稱指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可以用於在相同的 IP 位址和連接埠上裝載多個網域。</span><span class="sxs-lookup"><span data-stu-id="d4102-715">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d4102-716">SNI 若要運作，用戶端會在 TLS 信號交換期間傳送安全工作階段的主機名稱給伺服器，讓伺服器可以提供正確的憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-716">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d4102-717">用戶端在 TLS 信號交換之後的安全工作階段期間，會使用所提供的憑證與伺服器進行加密通訊。</span><span class="sxs-lookup"><span data-stu-id="d4102-717">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d4102-718">Kestrel 透過 `ServerCertificateSelector` 回呼來支援 SNI。</span><span class="sxs-lookup"><span data-stu-id="d4102-718">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d4102-719">回呼會針對每個連線叫用一次，允許應用程式檢查主機名稱並選取適當的憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-719">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d4102-720">SNI 支援需要：</span><span class="sxs-lookup"><span data-stu-id="d4102-720">SNI support requires:</span></span>

* <span data-ttu-id="d4102-721">在目標 framework `netcoreapp2.1` 或更新版本上執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-721">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d4102-722">在 `net461` 或更新版本中，會叫用回呼，但 `name` 一律為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="d4102-722">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d4102-723">如果用戶端不在 TLS 信號交換中提供主機名稱參數，則 `name` 也是 `null`。</span><span class="sxs-lookup"><span data-stu-id="d4102-723">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d4102-724">所有網站都在相同的 Kestrel 執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-724">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d4102-725">在不使用反向 Proxy 的情況下，Kestrel 不支援跨多個執行個體共用 IP 位址和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-725">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="d4102-726">連接記錄</span><span class="sxs-lookup"><span data-stu-id="d4102-726">Connection logging</span></span>

<span data-ttu-id="d4102-727">呼叫 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以發出連接上位元組層級通訊的 Debug 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="d4102-727">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d4102-728">連線記錄有助於疑難排解低層級通訊中的問題，例如在 TLS 加密期間和 proxy 後方。</span><span class="sxs-lookup"><span data-stu-id="d4102-728">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d4102-729">如果 `UseConnectionLogging` 之前放置 `UseHttps` ，則會記錄加密的流量。</span><span class="sxs-lookup"><span data-stu-id="d4102-729">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d4102-730">如果 `UseConnectionLogging` 放置在之後 `UseHttps` ，則會記錄解密的流量。</span><span class="sxs-lookup"><span data-stu-id="d4102-730">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d4102-731">繫結至 TCP 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-731">Bind to a TCP socket</span></span>

<span data-ttu-id="d4102-732"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法會繫結至 TCP 通訊端，而選項 Lambda 則會允許 X.509 憑證設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-732">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="d4102-733">此範例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>來為端點設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-733">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d4102-734">若要設定特定端點的其他 Kestrel 設定，請使用相同的 API。</span><span class="sxs-lookup"><span data-stu-id="d4102-734">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d4102-735">繫結至 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-735">Bind to a Unix socket</span></span>

<span data-ttu-id="d4102-736">請使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 在 Unix 通訊端上進行接聽以改善 Nginx 的效能，如此範例所示：</span><span class="sxs-lookup"><span data-stu-id="d4102-736">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="d4102-737">在 Nginx confiuguration 檔案中，將 `server`  >  `location`  >  `proxy_pass` 專案設定為 `http://unix:/tmp/{KESTREL SOCKET}:/;` 。</span><span class="sxs-lookup"><span data-stu-id="d4102-737">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d4102-738">`{KESTREL SOCKET}` 這是提供給 (的通訊端名稱 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ，例如 `kestrel-test.sock` 上述範例) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-738">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d4102-739">確定 Nginx (可寫入通訊端，例如 `chmod go+w /tmp/kestrel-test.sock`) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-739">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="d4102-740">連接埠 0</span><span class="sxs-lookup"><span data-stu-id="d4102-740">Port 0</span></span>

<span data-ttu-id="d4102-741">指定連接埠號碼 `0` 時，Kestrel 會動態繫結至可用的連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-741">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d4102-742">下列範例示範如何判斷 Kestrel 在執行階段實際上繫結至哪一個連接埠：</span><span class="sxs-lookup"><span data-stu-id="d4102-742">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d4102-743">當應用程式執行時，主控台視窗輸出會指出可以連線到應用程式的動態連接埠：</span><span class="sxs-lookup"><span data-stu-id="d4102-743">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d4102-744">限制</span><span class="sxs-lookup"><span data-stu-id="d4102-744">Limitations</span></span>

<span data-ttu-id="d4102-745">使用下列方法來設定端點：</span><span class="sxs-lookup"><span data-stu-id="d4102-745">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d4102-746">`--urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="d4102-746">`--urls` command-line argument</span></span>
* <span data-ttu-id="d4102-747">`urls` 主機組態索引鍵</span><span class="sxs-lookup"><span data-stu-id="d4102-747">`urls` host configuration key</span></span>
* <span data-ttu-id="d4102-748">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="d4102-748">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d4102-749">要讓程式碼使用 Kestrel 以外的伺服器，這些方法會很有用。</span><span class="sxs-lookup"><span data-stu-id="d4102-749">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d4102-750">不過，請注意下列限制：</span><span class="sxs-lookup"><span data-stu-id="d4102-750">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d4102-751">HTTPS 無法與這些方法搭配使用，除非在 HTTPS 端點設定中提供預設憑證 (例如，使用 `KestrelServerOptions` 設定或設定檔，如本主題稍早所示)。</span><span class="sxs-lookup"><span data-stu-id="d4102-751">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d4102-752">當同時使用 `Listen` 和 `UseUrls` 方法時，`Listen` 端點會覆寫 `UseUrls` 端點。</span><span class="sxs-lookup"><span data-stu-id="d4102-752">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d4102-753">IIS 端點設定</span><span class="sxs-lookup"><span data-stu-id="d4102-753">IIS endpoint configuration</span></span>

<span data-ttu-id="d4102-754">使用 IIS 時，IIS 覆寫繫結的 URL 繫結是由 `Listen` 或 `UseUrls` 設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-754">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d4102-755">如需詳細資訊，請參閱 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)主題。</span><span class="sxs-lookup"><span data-stu-id="d4102-755">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="d4102-756">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="d4102-756">ListenOptions.Protocols</span></span>

<span data-ttu-id="d4102-757">`Protocols` 屬性會建立在連線端點上或針對伺服器啟用的 HTTP 通訊協定 (`HttpProtocols`)。</span><span class="sxs-lookup"><span data-stu-id="d4102-757">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="d4102-758">從 `HttpProtocols` 列舉中指派一個值給 `Protocols` 屬性。</span><span class="sxs-lookup"><span data-stu-id="d4102-758">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="d4102-759">`HttpProtocols` 列舉值</span><span class="sxs-lookup"><span data-stu-id="d4102-759">`HttpProtocols` enum value</span></span> | <span data-ttu-id="d4102-760">允許的連線通訊協定</span><span class="sxs-lookup"><span data-stu-id="d4102-760">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="d4102-761">僅限 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="d4102-761">HTTP/1.1 only.</span></span> <span data-ttu-id="d4102-762">可在具有或沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-762">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="d4102-763">僅限 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-763">HTTP/2 only.</span></span> <span data-ttu-id="d4102-764">只有在用戶端支援[先備知識模式](https://tools.ietf.org/html/rfc7540#section-3.4)時，才可以在沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-764">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="d4102-765">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d4102-765">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="d4102-766">HTTP/2 需要 TLS 和 [應用層通訊協定協商 (ALPN) ](https://tools.ietf.org/html/rfc7301#section-3) 連接;否則，連接預設為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="d4102-766">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="d4102-767">預設通訊協定為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="d4102-767">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="d4102-768">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="d4102-768">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="d4102-769">TLS 1.2 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="d4102-769">TLS version 1.2 or later</span></span>
* <span data-ttu-id="d4102-770">已停用重新交涉</span><span class="sxs-lookup"><span data-stu-id="d4102-770">Renegotiation disabled</span></span>
* <span data-ttu-id="d4102-771">已停用壓縮</span><span class="sxs-lookup"><span data-stu-id="d4102-771">Compression disabled</span></span>
* <span data-ttu-id="d4102-772">暫時金鑰交換大小下限：</span><span class="sxs-lookup"><span data-stu-id="d4102-772">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="d4102-773">橢圓曲線 Diffie-Hellman (>ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; ：最少224位</span><span class="sxs-lookup"><span data-stu-id="d4102-773">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="d4102-774">有限的欄位 Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; ：最低2048位</span><span class="sxs-lookup"><span data-stu-id="d4102-774">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="d4102-775">未封鎖加密套件</span><span class="sxs-lookup"><span data-stu-id="d4102-775">Cipher suite not blocked</span></span>

<span data-ttu-id="d4102-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack;預設支援使用 P-256 橢圓曲線 &lbrack; `FIPS186` &rbrack; 。</span><span class="sxs-lookup"><span data-stu-id="d4102-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="d4102-777">下列範例會允許連接埠 8000 上的 HTTP/1.1 和 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-777">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="d4102-778">這些連線使用提供的憑證受到 TLS 保護：</span><span class="sxs-lookup"><span data-stu-id="d4102-778">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="d4102-779">選擇性地建立 `IConnectionAdapter` 實作，針對每個連線來篩選特定加密的 TLS 交握：</span><span class="sxs-lookup"><span data-stu-id="d4102-779">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="d4102-780">從設定進行通訊協定設定</span><span class="sxs-lookup"><span data-stu-id="d4102-780">*Set the protocol from configuration*</span></span>

<span data-ttu-id="d4102-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 預設會呼叫 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="d4102-782">在下列 *appsettings.json* 範例中，會為 Kestrel 的所有端點建立預設的連接通訊協定， (HTTP/1.1 和 HTTP/2) ：</span><span class="sxs-lookup"><span data-stu-id="d4102-782">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="d4102-783">下列設定檔範例會為特定端點建立連線通訊協定：</span><span class="sxs-lookup"><span data-stu-id="d4102-783">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="d4102-784">程式碼中指定的通訊協定會覆寫設定所設定的值。</span><span class="sxs-lookup"><span data-stu-id="d4102-784">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="d4102-785">Libuv 傳輸設定</span><span class="sxs-lookup"><span data-stu-id="d4102-785">Libuv transport configuration</span></span>

<span data-ttu-id="d4102-786">隨著 ASP.NET Core 2.1 的發行，Kestrel 的預設傳輸不再根據 Libuv，而是改為根據受控通訊端。</span><span class="sxs-lookup"><span data-stu-id="d4102-786">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="d4102-787">對於升級到 2.1 且會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 並相依於下列任一套件的 ASP.NET Core 2.0 應用程式來說，這是一項中斷性變更：</span><span class="sxs-lookup"><span data-stu-id="d4102-787">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="d4102-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (直接套件參考)</span><span class="sxs-lookup"><span data-stu-id="d4102-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="d4102-789">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d4102-789">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="d4102-790">針對需要使用 Libuv 的專案：</span><span class="sxs-lookup"><span data-stu-id="d4102-790">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="d4102-791">將 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 套件的相依性新增至應用程式的專案檔中：</span><span class="sxs-lookup"><span data-stu-id="d4102-791">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d4102-792">呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="d4102-792">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="d4102-793">URL 前置詞</span><span class="sxs-lookup"><span data-stu-id="d4102-793">URL prefixes</span></span>

<span data-ttu-id="d4102-794">使用 `UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵或 `ASPNETCORE_URLS` 環境變數時，URL 前置詞可以採用下列任一格式。</span><span class="sxs-lookup"><span data-stu-id="d4102-794">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d4102-795">只有 HTTP URL 前置詞有效。</span><span class="sxs-lookup"><span data-stu-id="d4102-795">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d4102-796">使用 `UseUrls` 來設定 URL 繫結時，Kestrel 不支援 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-796">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d4102-797">IPv4 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-797">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d4102-798">`0.0.0.0` 是繫結至所有 IPv4 位址的特殊情況。</span><span class="sxs-lookup"><span data-stu-id="d4102-798">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d4102-799">IPv6 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-799">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d4102-800">`[::]` 是相當於 IPv4 `0.0.0.0` 的 IPv6 對等項目。</span><span class="sxs-lookup"><span data-stu-id="d4102-800">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d4102-801">主機名稱與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-801">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d4102-802">主機名稱 `*` 和 `+` 並不特殊。</span><span class="sxs-lookup"><span data-stu-id="d4102-802">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d4102-803">無法辨識為有效 IP 位址或 `localhost` 的任何項目，都會繫結至所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="d4102-803">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d4102-804">若要在相同連接埠上將不同的主機名稱繫結至不同的 ASP.NET Core 應用程式，請使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向 Proxy 伺服器 (例如 IIS、Nginx 或 Apache)。</span><span class="sxs-lookup"><span data-stu-id="d4102-804">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d4102-805">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="d4102-805">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d4102-806">主機 `localhost` 名稱與連接埠號碼，或回送 IP 與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-806">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d4102-807">如果指定 `localhost`，Kestrel 會嘗試同時繫結至 IPv4 和 IPv6 回送介面。</span><span class="sxs-lookup"><span data-stu-id="d4102-807">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d4102-808">如果所要求的連接埠在任一個回送介面上由另一個服務使用，則 Kestrel 無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4102-808">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d4102-809">如果任一回送介面由於任何其他原因 (最常見的原因是不支援 IPv6) 無法使用，Kestrel 就會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="d4102-809">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d4102-810">主機篩選</span><span class="sxs-lookup"><span data-stu-id="d4102-810">Host filtering</span></span>

<span data-ttu-id="d4102-811">雖然 Kestrel 根據前置詞來支援組態，例如 `http://example.com:5000`，Kestrel 大多會忽略主機名稱。</span><span class="sxs-lookup"><span data-stu-id="d4102-811">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d4102-812">主機 `localhost` 是特殊情況，用來繫結到回送位址。</span><span class="sxs-lookup"><span data-stu-id="d4102-812">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d4102-813">任何非明確 IP 位址的主機，會繫結至所有公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="d4102-813">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d4102-814">`Host` 標頭未驗證。</span><span class="sxs-lookup"><span data-stu-id="d4102-814">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d4102-815">因應措施是使用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d4102-815">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d4102-816">主機篩選中介軟體是由 [AspNetCore HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 套件所提供，此套件隨附于 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 或 2.2) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-816">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="d4102-817">中介軟體是由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 所新增，它會呼叫 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>：</span><span class="sxs-lookup"><span data-stu-id="d4102-817">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d4102-818">預設停用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d4102-818">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d4102-819">若要啟用中介軟體，請 `AllowedHosts` 在 *appsettings.json* / *appsettings 中定義 \<EnvironmentName> 金鑰。json*。</span><span class="sxs-lookup"><span data-stu-id="d4102-819">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d4102-820">此值是以分號分隔的主機名稱清單，不含連接埠號碼：</span><span class="sxs-lookup"><span data-stu-id="d4102-820">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d4102-821">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d4102-821">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d4102-822">[轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)也有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 選項。</span><span class="sxs-lookup"><span data-stu-id="d4102-822">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d4102-823">在不同的案例中，轉送標頭中介軟體和主機篩選中介軟體有類似的功能。</span><span class="sxs-lookup"><span data-stu-id="d4102-823">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d4102-824">當不保留 `Host` 標頭，卻使用反向 Proxy 伺服器或負載平衡器轉送要求時，可使用轉送標頭中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="d4102-824">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d4102-825">當使用 Kestrel 作為公眾對應 Edge Server，或直接轉送 `Host` 標頭時，可使用主機篩選中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="d4102-825">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d4102-826">如需轉送標頭中介軟體的詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="d4102-826">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="d4102-827">HTTP/1.1 要求清空</span><span class="sxs-lookup"><span data-stu-id="d4102-827">HTTP/1.1 request draining</span></span>

<span data-ttu-id="d4102-828">開啟 HTTP 連接相當耗時。</span><span class="sxs-lookup"><span data-stu-id="d4102-828">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="d4102-829">若是 HTTPS，也需要大量資源。</span><span class="sxs-lookup"><span data-stu-id="d4102-829">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="d4102-830">因此，Kestrel 會嘗試針對每個 HTTP/1.1 通訊協定重複使用連接。</span><span class="sxs-lookup"><span data-stu-id="d4102-830">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="d4102-831">要求主體必須完全取用，才能重複使用連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-831">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="d4102-832">應用程式不一定會取用要求主體，例如伺服器傳回重新 `POST` 導向或404回應的要求。</span><span class="sxs-lookup"><span data-stu-id="d4102-832">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="d4102-833">在 `POST` -重新導向案例中：</span><span class="sxs-lookup"><span data-stu-id="d4102-833">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="d4102-834">用戶端可能已經傳送部分 `POST` 資料。</span><span class="sxs-lookup"><span data-stu-id="d4102-834">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="d4102-835">伺服器會寫入301回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-835">The server writes the 301 response.</span></span>
* <span data-ttu-id="d4102-836">連線無法用於新的要求，直到 `POST` 前一個要求主體的資料已完全讀取為止。</span><span class="sxs-lookup"><span data-stu-id="d4102-836">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="d4102-837">Kestrel 會嘗試清空要求主體。</span><span class="sxs-lookup"><span data-stu-id="d4102-837">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="d4102-838">清空要求主體表示在不處理資料的情況下讀取和捨棄資料。</span><span class="sxs-lookup"><span data-stu-id="d4102-838">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="d4102-839">清空程式可讓您在允許連線重複使用時，以及清空任何剩餘資料所需的時間之間進行 tradoff：</span><span class="sxs-lookup"><span data-stu-id="d4102-839">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="d4102-840">清空有五秒的時間，無法設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-840">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="d4102-841">如果或標頭所指定的所有資料在 `Content-Length` `Transfer-Encoding` 此時間之前未被讀取，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="d4102-841">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="d4102-842">有時您可能會想要在寫入回應之前或之後立即終止要求。</span><span class="sxs-lookup"><span data-stu-id="d4102-842">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="d4102-843">例如，用戶端可能會有限制性的資料上限，因此限制上傳的資料可能是優先考慮。</span><span class="sxs-lookup"><span data-stu-id="d4102-843">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="d4102-844">在這種情況下，若要終止要求，請從控制器、頁面或中介軟體呼叫[HttpCoNtext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) 。 Razor</span><span class="sxs-lookup"><span data-stu-id="d4102-844">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="d4102-845">呼叫時有一些注意事項 `Abort` ：</span><span class="sxs-lookup"><span data-stu-id="d4102-845">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="d4102-846">建立新的連接可能很慢且昂貴。</span><span class="sxs-lookup"><span data-stu-id="d4102-846">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="d4102-847">在連接關閉之前，不保證用戶端已讀取回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-847">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="d4102-848">呼叫 `Abort` 應該很罕見，而且會保留給嚴重的錯誤案例，而不是常見的錯誤。</span><span class="sxs-lookup"><span data-stu-id="d4102-848">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="d4102-849">只有 `Abort` 在需要解決特定問題時才呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-849">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="d4102-850">例如， `Abort` 如果惡意用戶端嘗試 `POST` 資料或用戶端程式代碼中有錯誤，而導致大量或許多要求，請呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-850">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="d4102-851">請勿呼叫 `Abort` 常見的錯誤狀況，例如 HTTP 404 (找不到) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-851">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="d4102-852">在呼叫之前呼叫 [HttpResponse](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) ， `Abort` 可確保伺服器已完成寫入回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-852">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="d4102-853">不過，用戶端行為不是可預測的，而且在中斷連接之前，可能不會讀取回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-853">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="d4102-854">HTTP/2 的這個程式不同，因為通訊協定支援中止個別要求資料流程，而不需要關閉連接。</span><span class="sxs-lookup"><span data-stu-id="d4102-854">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="d4102-855">五秒的清空超時時間不適用。</span><span class="sxs-lookup"><span data-stu-id="d4102-855">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="d4102-856">如果在完成回應之後有任何未讀取的要求主體資料，則伺服器會傳送 HTTP/2 RST 框架。</span><span class="sxs-lookup"><span data-stu-id="d4102-856">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="d4102-857">系統會忽略其他要求主體資料框架。</span><span class="sxs-lookup"><span data-stu-id="d4102-857">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="d4102-858">可能的話，最好是讓用戶端利用預期的 [： 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 要求標頭，並等待伺服器回應，然後再開始傳送要求本文。</span><span class="sxs-lookup"><span data-stu-id="d4102-858">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="d4102-859">這可讓用戶端有機會檢查回應，並在傳送不需要的資料之前中止。</span><span class="sxs-lookup"><span data-stu-id="d4102-859">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4102-860">其他資源</span><span class="sxs-lookup"><span data-stu-id="d4102-860">Additional resources</span></span>

* <span data-ttu-id="d4102-861">在 Linux 上使用 UNIX 通訊端時，通訊端不會在應用程式關閉時自動刪除。</span><span class="sxs-lookup"><span data-stu-id="d4102-861">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d4102-862">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="d4102-862">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d4102-863">RFC 7230：訊息語法和路由 (第 5.4 節：主機)</span><span class="sxs-lookup"><span data-stu-id="d4102-863">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d4102-864">Kestrel 是 [ASP.NET Core 的跨平台網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="d4102-864">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d4102-865">Kestrel 是 ASP.NET Core 專案範本中預設隨附的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="d4102-865">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d4102-866">Kestrel 支援下列案例：</span><span class="sxs-lookup"><span data-stu-id="d4102-866">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d4102-867">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d4102-867">HTTPS</span></span>
* <span data-ttu-id="d4102-868">用來啟用 [WebSockets](https://github.com/aspnet/websockets) 的不透明升級</span><span class="sxs-lookup"><span data-stu-id="d4102-868">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d4102-869">Nginx 背後的高效能 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-869">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="d4102-870">.NET Core 支援的所有平台和版本都支援 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-870">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d4102-871">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4102-871">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d4102-872">何時搭配使用 Kestrel 與反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="d4102-872">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d4102-873">您可以單獨使用 Kestrel，或與 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 等「反向 Proxy 伺服器」搭配使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-873">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d4102-874">反向 Proxy 伺服器會從網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-874">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d4102-875">Kestrel 用作邊緣 (網際網路對應) 網頁伺服器：</span><span class="sxs-lookup"><span data-stu-id="d4102-875">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d4102-877">Kestrel 用於反向 Proxy 組態中：</span><span class="sxs-lookup"><span data-stu-id="d4102-877">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d4102-879">不論是否有反向 proxy 伺服器，設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-879">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d4102-880">Kestrel 用作不需要反向 Proxy 伺服器的 Edge Server 時，不支援在多個處理序之間共用相同的 IP 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-880">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d4102-881">當 Kestrel 設定為接聽連接埠時，Kestrel 會處理該連接埠的所有流量，而不論要求的 `Host` 標頭為何。</span><span class="sxs-lookup"><span data-stu-id="d4102-881">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d4102-882">可以共用連接埠的反向 Proxy 能夠在唯一的 IP 和連接埠上轉送要求給 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-882">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d4102-883">即使不需要反向 Proxy 伺服器，使用反向 Proxy 伺服器也是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="d4102-883">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d4102-884">反向 Proxy：</span><span class="sxs-lookup"><span data-stu-id="d4102-884">A reverse proxy:</span></span>

* <span data-ttu-id="d4102-885">可以限制它所主控之應用程式的公開介面區。</span><span class="sxs-lookup"><span data-stu-id="d4102-885">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d4102-886">提供額外的組態和防禦層。</span><span class="sxs-lookup"><span data-stu-id="d4102-886">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d4102-887">能夠與現有基礎結構更好地整合。</span><span class="sxs-lookup"><span data-stu-id="d4102-887">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d4102-888">簡化負載平衡和安全通訊 (HTTPS) 組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-888">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d4102-889">只有反向 proxy 伺服器需要 x.509 憑證，而且該伺服器可以使用一般 HTTP 與內部網路上的應用程式伺服器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="d4102-889">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d4102-890">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="d4102-890">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d4102-891">如何在 ASP.NET Core 應用程式中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="d4102-891">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d4102-892">AspNetCore 會包含在[AspNetCore.. 中繼套件](xref:fundamentals/metapackage-app). [Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)套件中。</span><span class="sxs-lookup"><span data-stu-id="d4102-892">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d4102-893">ASP.NET Core 專案範本預設會使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-893">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d4102-894">在 *Program.cs* 中，範本程式碼會呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，而後者會在幕後呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="d4102-894">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="d4102-895">若要在呼叫 `CreateDefaultBuilder` 之後提供額外的設定，請呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="d4102-895">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="d4102-896">如需 `CreateDefaultBuilder` 和建立主機的詳細資訊，請參閱的 *設定主機* 一節 <xref:fundamentals/host/web-host#set-up-a-host> 。</span><span class="sxs-lookup"><span data-stu-id="d4102-896">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="d4102-897">Kestrel 選項</span><span class="sxs-lookup"><span data-stu-id="d4102-897">Kestrel options</span></span>

<span data-ttu-id="d4102-898">Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。</span><span class="sxs-lookup"><span data-stu-id="d4102-898">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d4102-899">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。</span><span class="sxs-lookup"><span data-stu-id="d4102-899">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d4102-900">`Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4102-900">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d4102-901">下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；</span><span class="sxs-lookup"><span data-stu-id="d4102-901">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d4102-902">您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項（在下列範例中以 c # 程式碼設定）。</span><span class="sxs-lookup"><span data-stu-id="d4102-902">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d4102-903">例如，檔案設定提供者可以從或 appsettings 載入 Kestrel 設定 *appsettings.json* *。環境}. json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="d4102-903">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="d4102-904">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="d4102-904">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d4102-905">設定 Kestrel in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d4102-905">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d4102-906">將的實例插入 `IConfiguration` 至 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="d4102-906">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d4102-907">下列範例假設將插入的設定指派給 `Configuration` 屬性。</span><span class="sxs-lookup"><span data-stu-id="d4102-907">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d4102-908">在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-908">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="d4102-909">設定 Kestrel 建立主機時：</span><span class="sxs-lookup"><span data-stu-id="d4102-909">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d4102-910">在 *Program.cs* 中，將 `Kestrel` Configuration 區段載入 Kestrel 的設定中：</span><span class="sxs-lookup"><span data-stu-id="d4102-910">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="d4102-911">上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。</span><span class="sxs-lookup"><span data-stu-id="d4102-911">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d4102-912">Keep-alive 逾時</span><span class="sxs-lookup"><span data-stu-id="d4102-912">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d4102-913">取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="d4102-913">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d4102-914">預設為 2 分鐘。</span><span class="sxs-lookup"><span data-stu-id="d4102-914">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="d4102-915">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="d4102-915">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d4102-916">可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：</span><span class="sxs-lookup"><span data-stu-id="d4102-916">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="d4102-917">已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-917">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d4102-918">升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-918">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="d4102-919">連線數目上限預設為無限制 (null)。</span><span class="sxs-lookup"><span data-stu-id="d4102-919">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d4102-920">要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="d4102-920">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d4102-921">預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="d4102-921">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d4102-922">若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：</span><span class="sxs-lookup"><span data-stu-id="d4102-922">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d4102-923">以下範例會示範如何設定應用程式、每個要求的條件約束：</span><span class="sxs-lookup"><span data-stu-id="d4102-923">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="d4102-924">覆寫中介軟體中特定要求的設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-924">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d4102-925">如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d4102-925">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d4102-926">有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="d4102-926">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d4102-927">當應用程式是在 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)後方於[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)執行時，Kestrel 的要求本文大小限制將會被停用，因為 IIS 已經設定限制。</span><span class="sxs-lookup"><span data-stu-id="d4102-927">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d4102-928">要求主體資料速率下限</span><span class="sxs-lookup"><span data-stu-id="d4102-928">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d4102-929">如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。</span><span class="sxs-lookup"><span data-stu-id="d4102-929">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d4102-930">如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 讓用戶端提高其傳送速率的時間量（最小值）;在這段時間內不會檢查速率。</span><span class="sxs-lookup"><span data-stu-id="d4102-930">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d4102-931">寬限期可協助避免中斷連線，這是由於 TCP 緩慢啟動而一開始以低速傳送資料所造成。</span><span class="sxs-lookup"><span data-stu-id="d4102-931">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d4102-932">預設速率下限為 240 個位元組/秒，寬限期為 5 秒。</span><span class="sxs-lookup"><span data-stu-id="d4102-932">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d4102-933">速率下限也適用於回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-933">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d4102-934">除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。</span><span class="sxs-lookup"><span data-stu-id="d4102-934">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d4102-935">以下範例示範如何在 *Program.cs* 中設定資料速率下限：</span><span class="sxs-lookup"><span data-stu-id="d4102-935">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="d4102-936">要求標頭逾時</span><span class="sxs-lookup"><span data-stu-id="d4102-936">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d4102-937">取得或設定伺服器花費在接收要求標頭的時間上限。</span><span class="sxs-lookup"><span data-stu-id="d4102-937">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d4102-938">預設為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d4102-938">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="d4102-939">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="d4102-939">Synchronous I/O</span></span>

<span data-ttu-id="d4102-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="d4102-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d4102-941">預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="d4102-941">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d4102-942">大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-942">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d4102-943">只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。</span><span class="sxs-lookup"><span data-stu-id="d4102-943">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d4102-944">下列範例會停用同步 i/o：</span><span class="sxs-lookup"><span data-stu-id="d4102-944">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="d4102-945">如需其他 Kestrel 選項和限制的資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="d4102-945">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d4102-946">端點組態</span><span class="sxs-lookup"><span data-stu-id="d4102-946">Endpoint configuration</span></span>

<span data-ttu-id="d4102-947">ASP.NET Core 預設會繫結至：</span><span class="sxs-lookup"><span data-stu-id="d4102-947">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d4102-948">`https://localhost:5001` (當有本機開發憑證存在時)</span><span class="sxs-lookup"><span data-stu-id="d4102-948">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d4102-949">使用以下各項指定 URL：</span><span class="sxs-lookup"><span data-stu-id="d4102-949">Specify URLs using the:</span></span>

* <span data-ttu-id="d4102-950">`ASPNETCORE_URLS` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="d4102-950">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d4102-951">`--urls` 命令列引數。</span><span class="sxs-lookup"><span data-stu-id="d4102-951">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d4102-952">`urls` 主機組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d4102-952">`urls` host configuration key.</span></span>
* <span data-ttu-id="d4102-953">`UseUrls` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="d4102-953">`UseUrls` extension method.</span></span>

<span data-ttu-id="d4102-954">使用這些方法提供的值可以是一或多個 HTTP 和 HTTPS 端點 (如果有預設憑證可用則為 HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="d4102-954">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d4102-955">將值設定為以分號分隔的清單 (例如，`"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="d4102-955">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d4102-956">如需有關這些方法的詳細資訊，請參閱[伺服器 URL](xref:fundamentals/host/web-host#server-urls) 和[覆寫設定](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="d4102-956">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d4102-957">開發憑證會建立於：</span><span class="sxs-lookup"><span data-stu-id="d4102-957">A development certificate is created:</span></span>

* <span data-ttu-id="d4102-958">已安裝 [.NET Core SDK](/dotnet/core/sdk) 時。</span><span class="sxs-lookup"><span data-stu-id="d4102-958">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d4102-959">[dev-certs 工具](xref:aspnetcore-2.1#https)用來建立憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-959">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d4102-960">某些瀏覽器需要授與明確的許可權，以信任本機開發憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-960">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d4102-961">專案範本預設會將應用程式設定為在 HTTPS 上執行，並包含 Https 重新導向 [和 HSTS 支援](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="d4102-961">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d4102-962">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上呼叫 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法，來為 Kestrel 設定 URL 首碼和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-962">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d4102-963">`UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵和 `ASPNETCORE_URLS` 環境變數同樣有效，但卻有本節稍後註明的限制 (針對 HTTPS 端點組態必須有預設憑證可用)。</span><span class="sxs-lookup"><span data-stu-id="d4102-963">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d4102-964">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="d4102-964">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d4102-965">ConfigureEndpointDefaults (動作 \<ListenOptions>) </span><span class="sxs-lookup"><span data-stu-id="d4102-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d4102-966">指定組態 `Action` 以針對每個指定端點執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-966">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d4102-967">呼叫 `ConfigureEndpointDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-967">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="d4102-968">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-968">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d4102-969">ConfigureHttpsDefaults (動作 \<HttpsConnectionAdapterOptions>) </span><span class="sxs-lookup"><span data-stu-id="d4102-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d4102-970">指定組態 `Action` 以針對每個 HTTPS 端點執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-970">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d4102-971">呼叫 `ConfigureHttpsDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-971">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="d4102-972">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-972">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="d4102-973">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="d4102-973">Configure(IConfiguration)</span></span>

<span data-ttu-id="d4102-974">建立設定載入器來設定以 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作為輸入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d4102-974">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d4102-975">組態的範圍必須限於 Kestrel 的組態區段。</span><span class="sxs-lookup"><span data-stu-id="d4102-975">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d4102-976">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d4102-976">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d4102-977">設定 Kestrel 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-977">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d4102-978">`ListenOptions.UseHttps` 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="d4102-978">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d4102-979">`UseHttps`：設定 Kestrel 以搭配預設憑證使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-979">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d4102-980">如果未設定預設憑證，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="d4102-980">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="d4102-981">`ListenOptions.UseHttps` 參數：</span><span class="sxs-lookup"><span data-stu-id="d4102-981">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d4102-982">`filename` 是憑證檔案的路徑和檔案名稱，它相對於包含應用程式內容檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="d4102-982">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d4102-983">`password` 是存取 X.509 憑證資料所需的密碼。</span><span class="sxs-lookup"><span data-stu-id="d4102-983">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d4102-984">`configureOptions` 是設定 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="d4102-984">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d4102-985">傳回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="d4102-985">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d4102-986">`storeName` 是要從中載入憑證的憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="d4102-986">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d4102-987">`subject` 是憑證的主體名稱。</span><span class="sxs-lookup"><span data-stu-id="d4102-987">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d4102-988">`allowInvalid` 表示是否應該考慮無效的憑證，例如自我簽署憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-988">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d4102-989">`location` 是要從中載入憑證的存放區位置。</span><span class="sxs-lookup"><span data-stu-id="d4102-989">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d4102-990">`serverCertificate` 是 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-990">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d4102-991">在生產環境中，必須明確設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-991">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d4102-992">至少必須提供預設憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-992">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d4102-993">支援的組態描述如下：</span><span class="sxs-lookup"><span data-stu-id="d4102-993">Supported configurations described next:</span></span>

* <span data-ttu-id="d4102-994">無組態</span><span class="sxs-lookup"><span data-stu-id="d4102-994">No configuration</span></span>
* <span data-ttu-id="d4102-995">從組態取代預設憑證</span><span class="sxs-lookup"><span data-stu-id="d4102-995">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d4102-996">變更程式碼中的預設值</span><span class="sxs-lookup"><span data-stu-id="d4102-996">Change the defaults in code</span></span>

<span data-ttu-id="d4102-997">*無組態*</span><span class="sxs-lookup"><span data-stu-id="d4102-997">*No configuration*</span></span>

<span data-ttu-id="d4102-998">Kestrel 會接聽 `http://localhost:5000` 和 `https://localhost:5001` (如果預設憑證可用的話)。</span><span class="sxs-lookup"><span data-stu-id="d4102-998">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d4102-999">*從組態取代預設憑證*</span><span class="sxs-lookup"><span data-stu-id="d4102-999">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d4102-1000">`CreateDefaultBuilder` 預設會呼叫 `Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-1000">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d4102-1001">Kestrel 可以使用預設的 HTTPS 應用程式設定組態結構描述。</span><span class="sxs-lookup"><span data-stu-id="d4102-1001">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d4102-1002">設定多個端點，包括 URL 和要使用的憑證－從磁碟上的檔案，或是從憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="d4102-1002">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d4102-1003">在下列 *appsettings.json* 範例中：</span><span class="sxs-lookup"><span data-stu-id="d4102-1003">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d4102-1004">將 **AllowInvalid** 設定為 `true`，允許使用無效的憑證 (例如，自我簽署憑證)。</span><span class="sxs-lookup"><span data-stu-id="d4102-1004">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d4102-1005">任何未指定憑證 (接下來範例中的 **HttpsDefaultCert**) 的 HTTPS 端點會回復為 [憑證]**[預設]** >  下定義的憑證或開發憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-1005">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="d4102-1006">除了針對任何憑證節點使用 [路徑] 和 [密碼]，還可以使用憑證存放區欄位指定憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-1006">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d4102-1007">例如，您可以將 **憑證**  >  **預設** 憑證指定為：</span><span class="sxs-lookup"><span data-stu-id="d4102-1007">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d4102-1008">結構描述附註：</span><span class="sxs-lookup"><span data-stu-id="d4102-1008">Schema notes:</span></span>

* <span data-ttu-id="d4102-1009">端點名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="d4102-1009">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d4102-1010">例如，`HTTPS` 和 `Https` 都有效。</span><span class="sxs-lookup"><span data-stu-id="d4102-1010">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d4102-1011">`Url` 參數對每個端點而言都是必要的。</span><span class="sxs-lookup"><span data-stu-id="d4102-1011">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d4102-1012">此參數的格式等同於最上層 `Urls` 組態參數，但是它限制為單一值。</span><span class="sxs-lookup"><span data-stu-id="d4102-1012">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d4102-1013">這些端點會取代最上層 `Urls` 組態中定義的端點，而不是新增至其中。</span><span class="sxs-lookup"><span data-stu-id="d4102-1013">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d4102-1014">透過 `Listen` 在程式碼中定義的端點，會與組態區段中定義的端點累計。</span><span class="sxs-lookup"><span data-stu-id="d4102-1014">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d4102-1015">`Certificate` 區段是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="d4102-1015">The `Certificate` section is optional.</span></span> <span data-ttu-id="d4102-1016">如果未指定 `Certificate` 區段，則會使用先前案例中所定義的預設值。</span><span class="sxs-lookup"><span data-stu-id="d4102-1016">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d4102-1017">如果沒有預設值可供使用，伺服器就會擲回例外狀況，且無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4102-1017">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d4102-1018">`Certificate`區段同時支援 **路徑** &ndash; **密碼** 和 **主體** &ndash; **存放區** 憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-1018">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d4102-1019">可以用這種方式定義任何數目的端點，只要它們不會導致連接埠衝突即可。</span><span class="sxs-lookup"><span data-stu-id="d4102-1019">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d4102-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 會傳回 `KestrelConfigurationLoader` 與 `.Endpoint(string name, listenOptions => { })` 方法，此方法可用來補充已設定的端點設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="d4102-1021">`KestrelServerOptions.ConfigurationLoader` 可以直接存取，以繼續逐一查看現有的載入器，例如所提供的載入器 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 。</span><span class="sxs-lookup"><span data-stu-id="d4102-1021">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d4102-1022">您可以在方法的選項中使用每個端點的設定區段， `Endpoint` 以便讀取自訂設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-1022">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d4102-1023">可以藉由使用另一個區段再次呼叫 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 而載入多個組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-1023">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d4102-1024">只會使用最後一個組態，除非在先前的執行個體上已明確呼叫 `Load`。</span><span class="sxs-lookup"><span data-stu-id="d4102-1024">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d4102-1025">中繼套件不會呼叫 `Load`，如此可能會取代其預設組態區段。</span><span class="sxs-lookup"><span data-stu-id="d4102-1025">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d4102-1026">`KestrelConfigurationLoader` 會將來自 `KestrelServerOptions` 的 API 的 `Listen` 系列鏡像為 `Endpoint` 多載，所以可在相同的位置設定程式碼和設定端點。</span><span class="sxs-lookup"><span data-stu-id="d4102-1026">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d4102-1027">這些多載不使用名稱，並且只使用來自組態的預設組態。</span><span class="sxs-lookup"><span data-stu-id="d4102-1027">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d4102-1028">*變更程式碼中的預設值*</span><span class="sxs-lookup"><span data-stu-id="d4102-1028">*Change the defaults in code*</span></span>

<span data-ttu-id="d4102-1029">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 可以用來變更 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的預設設定，包括覆寫先前案例中指定的預設憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-1029">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d4102-1030">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 應該在設定任何端點之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-1030">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="d4102-1031">*SNI 的 Kestrel 支援*</span><span class="sxs-lookup"><span data-stu-id="d4102-1031">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d4102-1032">[伺服器名稱指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可以用於在相同的 IP 位址和連接埠上裝載多個網域。</span><span class="sxs-lookup"><span data-stu-id="d4102-1032">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d4102-1033">SNI 若要運作，用戶端會在 TLS 信號交換期間傳送安全工作階段的主機名稱給伺服器，讓伺服器可以提供正確的憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-1033">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d4102-1034">用戶端在 TLS 信號交換之後的安全工作階段期間，會使用所提供的憑證與伺服器進行加密通訊。</span><span class="sxs-lookup"><span data-stu-id="d4102-1034">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d4102-1035">Kestrel 透過 `ServerCertificateSelector` 回呼來支援 SNI。</span><span class="sxs-lookup"><span data-stu-id="d4102-1035">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d4102-1036">回呼會針對每個連線叫用一次，允許應用程式檢查主機名稱並選取適當的憑證。</span><span class="sxs-lookup"><span data-stu-id="d4102-1036">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d4102-1037">SNI 支援需要：</span><span class="sxs-lookup"><span data-stu-id="d4102-1037">SNI support requires:</span></span>

* <span data-ttu-id="d4102-1038">在目標 framework `netcoreapp2.1` 或更新版本上執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-1038">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d4102-1039">在 `net461` 或更新版本中，會叫用回呼，但 `name` 一律為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="d4102-1039">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d4102-1040">如果用戶端不在 TLS 信號交換中提供主機名稱參數，則 `name` 也是 `null`。</span><span class="sxs-lookup"><span data-stu-id="d4102-1040">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d4102-1041">所有網站都在相同的 Kestrel 執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="d4102-1041">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d4102-1042">在不使用反向 Proxy 的情況下，Kestrel 不支援跨多個執行個體共用 IP 位址和連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-1042">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="d4102-1043">連接記錄</span><span class="sxs-lookup"><span data-stu-id="d4102-1043">Connection logging</span></span>

<span data-ttu-id="d4102-1044">呼叫 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以發出連接上位元組層級通訊的 Debug 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="d4102-1044">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d4102-1045">連線記錄有助於疑難排解低層級通訊中的問題，例如在 TLS 加密期間和 proxy 後方。</span><span class="sxs-lookup"><span data-stu-id="d4102-1045">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d4102-1046">如果 `UseConnectionLogging` 之前放置 `UseHttps` ，則會記錄加密的流量。</span><span class="sxs-lookup"><span data-stu-id="d4102-1046">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d4102-1047">如果 `UseConnectionLogging` 放置在之後 `UseHttps` ，則會記錄解密的流量。</span><span class="sxs-lookup"><span data-stu-id="d4102-1047">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d4102-1048">繫結至 TCP 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-1048">Bind to a TCP socket</span></span>

<span data-ttu-id="d4102-1049"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法會繫結至 TCP 通訊端，而選項 Lambda 則會允許 X.509 憑證設定：</span><span class="sxs-lookup"><span data-stu-id="d4102-1049">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="d4102-1050">此範例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>來為端點設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-1050">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d4102-1051">若要設定特定端點的其他 Kestrel 設定，請使用相同的 API。</span><span class="sxs-lookup"><span data-stu-id="d4102-1051">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d4102-1052">繫結至 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="d4102-1052">Bind to a Unix socket</span></span>

<span data-ttu-id="d4102-1053">請使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 在 Unix 通訊端上進行接聽以改善 Nginx 的效能，如此範例所示：</span><span class="sxs-lookup"><span data-stu-id="d4102-1053">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="d4102-1054">在 Nginx confiuguration 檔案中，將 `server`  >  `location`  >  `proxy_pass` 專案設定為 `http://unix:/tmp/{KESTREL SOCKET}:/;` 。</span><span class="sxs-lookup"><span data-stu-id="d4102-1054">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d4102-1055">`{KESTREL SOCKET}` 這是提供給 (的通訊端名稱 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ，例如 `kestrel-test.sock` 上述範例) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-1055">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d4102-1056">確定 Nginx (可寫入通訊端，例如 `chmod go+w /tmp/kestrel-test.sock`) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-1056">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="d4102-1057">連接埠 0</span><span class="sxs-lookup"><span data-stu-id="d4102-1057">Port 0</span></span>

<span data-ttu-id="d4102-1058">指定連接埠號碼 `0` 時，Kestrel 會動態繫結至可用的連接埠。</span><span class="sxs-lookup"><span data-stu-id="d4102-1058">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d4102-1059">下列範例示範如何判斷 Kestrel 在執行階段實際上繫結至哪一個連接埠：</span><span class="sxs-lookup"><span data-stu-id="d4102-1059">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d4102-1060">當應用程式執行時，主控台視窗輸出會指出可以連線到應用程式的動態連接埠：</span><span class="sxs-lookup"><span data-stu-id="d4102-1060">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d4102-1061">限制</span><span class="sxs-lookup"><span data-stu-id="d4102-1061">Limitations</span></span>

<span data-ttu-id="d4102-1062">使用下列方法來設定端點：</span><span class="sxs-lookup"><span data-stu-id="d4102-1062">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d4102-1063">`--urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="d4102-1063">`--urls` command-line argument</span></span>
* <span data-ttu-id="d4102-1064">`urls` 主機組態索引鍵</span><span class="sxs-lookup"><span data-stu-id="d4102-1064">`urls` host configuration key</span></span>
* <span data-ttu-id="d4102-1065">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="d4102-1065">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d4102-1066">要讓程式碼使用 Kestrel 以外的伺服器，這些方法會很有用。</span><span class="sxs-lookup"><span data-stu-id="d4102-1066">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d4102-1067">不過，請注意下列限制：</span><span class="sxs-lookup"><span data-stu-id="d4102-1067">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d4102-1068">HTTPS 無法與這些方法搭配使用，除非在 HTTPS 端點設定中提供預設憑證 (例如，使用 `KestrelServerOptions` 設定或設定檔，如本主題稍早所示)。</span><span class="sxs-lookup"><span data-stu-id="d4102-1068">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d4102-1069">當同時使用 `Listen` 和 `UseUrls` 方法時，`Listen` 端點會覆寫 `UseUrls` 端點。</span><span class="sxs-lookup"><span data-stu-id="d4102-1069">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d4102-1070">IIS 端點設定</span><span class="sxs-lookup"><span data-stu-id="d4102-1070">IIS endpoint configuration</span></span>

<span data-ttu-id="d4102-1071">使用 IIS 時，IIS 覆寫繫結的 URL 繫結是由 `Listen` 或 `UseUrls` 設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-1071">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d4102-1072">如需詳細資訊，請參閱 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)主題。</span><span class="sxs-lookup"><span data-stu-id="d4102-1072">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="d4102-1073">Libuv 傳輸設定</span><span class="sxs-lookup"><span data-stu-id="d4102-1073">Libuv transport configuration</span></span>

<span data-ttu-id="d4102-1074">隨著 ASP.NET Core 2.1 的發行，Kestrel 的預設傳輸不再根據 Libuv，而是改為根據受控通訊端。</span><span class="sxs-lookup"><span data-stu-id="d4102-1074">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="d4102-1075">對於升級到 2.1 且會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 並相依於下列任一套件的 ASP.NET Core 2.0 應用程式來說，這是一項中斷性變更：</span><span class="sxs-lookup"><span data-stu-id="d4102-1075">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="d4102-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (直接套件參考)</span><span class="sxs-lookup"><span data-stu-id="d4102-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="d4102-1077">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d4102-1077">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="d4102-1078">針對需要使用 Libuv 的專案：</span><span class="sxs-lookup"><span data-stu-id="d4102-1078">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="d4102-1079">將 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 套件的相依性新增至應用程式的專案檔中：</span><span class="sxs-lookup"><span data-stu-id="d4102-1079">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d4102-1080">呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="d4102-1080">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="d4102-1081">URL 前置詞</span><span class="sxs-lookup"><span data-stu-id="d4102-1081">URL prefixes</span></span>

<span data-ttu-id="d4102-1082">使用 `UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵或 `ASPNETCORE_URLS` 環境變數時，URL 前置詞可以採用下列任一格式。</span><span class="sxs-lookup"><span data-stu-id="d4102-1082">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d4102-1083">只有 HTTP URL 前置詞有效。</span><span class="sxs-lookup"><span data-stu-id="d4102-1083">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d4102-1084">使用 `UseUrls` 來設定 URL 繫結時，Kestrel 不支援 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="d4102-1084">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d4102-1085">IPv4 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-1085">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d4102-1086">`0.0.0.0` 是繫結至所有 IPv4 位址的特殊情況。</span><span class="sxs-lookup"><span data-stu-id="d4102-1086">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d4102-1087">IPv6 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-1087">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d4102-1088">`[::]` 是相當於 IPv4 `0.0.0.0` 的 IPv6 對等項目。</span><span class="sxs-lookup"><span data-stu-id="d4102-1088">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d4102-1089">主機名稱與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-1089">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d4102-1090">主機名稱 `*` 和 `+` 並不特殊。</span><span class="sxs-lookup"><span data-stu-id="d4102-1090">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d4102-1091">無法辨識為有效 IP 位址或 `localhost` 的任何項目，都會繫結至所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="d4102-1091">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d4102-1092">若要在相同連接埠上將不同的主機名稱繫結至不同的 ASP.NET Core 應用程式，請使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向 Proxy 伺服器 (例如 IIS、Nginx 或 Apache)。</span><span class="sxs-lookup"><span data-stu-id="d4102-1092">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d4102-1093">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="d4102-1093">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d4102-1094">主機 `localhost` 名稱與連接埠號碼，或回送 IP 與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="d4102-1094">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d4102-1095">如果指定 `localhost`，Kestrel 會嘗試同時繫結至 IPv4 和 IPv6 回送介面。</span><span class="sxs-lookup"><span data-stu-id="d4102-1095">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d4102-1096">如果所要求的連接埠在任一個回送介面上由另一個服務使用，則 Kestrel 無法啟動。</span><span class="sxs-lookup"><span data-stu-id="d4102-1096">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d4102-1097">如果任一回送介面由於任何其他原因 (最常見的原因是不支援 IPv6) 無法使用，Kestrel 就會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="d4102-1097">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d4102-1098">主機篩選</span><span class="sxs-lookup"><span data-stu-id="d4102-1098">Host filtering</span></span>

<span data-ttu-id="d4102-1099">雖然 Kestrel 根據前置詞來支援組態，例如 `http://example.com:5000`，Kestrel 大多會忽略主機名稱。</span><span class="sxs-lookup"><span data-stu-id="d4102-1099">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d4102-1100">主機 `localhost` 是特殊情況，用來繫結到回送位址。</span><span class="sxs-lookup"><span data-stu-id="d4102-1100">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d4102-1101">任何非明確 IP 位址的主機，會繫結至所有公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="d4102-1101">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d4102-1102">`Host` 標頭未驗證。</span><span class="sxs-lookup"><span data-stu-id="d4102-1102">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d4102-1103">因應措施是使用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d4102-1103">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d4102-1104">主機篩選中介軟體是由 [AspNetCore HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 套件所提供，此套件隨附于 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 或 2.2) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-1104">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="d4102-1105">中介軟體是由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 所新增，它會呼叫 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>：</span><span class="sxs-lookup"><span data-stu-id="d4102-1105">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d4102-1106">預設停用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="d4102-1106">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d4102-1107">若要啟用中介軟體，請 `AllowedHosts` 在 *appsettings.json* / *appsettings 中定義 \<EnvironmentName> 金鑰。json*。</span><span class="sxs-lookup"><span data-stu-id="d4102-1107">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d4102-1108">此值是以分號分隔的主機名稱清單，不含連接埠號碼：</span><span class="sxs-lookup"><span data-stu-id="d4102-1108">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d4102-1109">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d4102-1109">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d4102-1110">[轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)也有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 選項。</span><span class="sxs-lookup"><span data-stu-id="d4102-1110">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d4102-1111">在不同的案例中，轉送標頭中介軟體和主機篩選中介軟體有類似的功能。</span><span class="sxs-lookup"><span data-stu-id="d4102-1111">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d4102-1112">當不保留 `Host` 標頭，卻使用反向 Proxy 伺服器或負載平衡器轉送要求時，可使用轉送標頭中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="d4102-1112">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d4102-1113">當使用 Kestrel 作為公眾對應 Edge Server，或直接轉送 `Host` 標頭時，可使用主機篩選中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="d4102-1113">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d4102-1114">如需轉送標頭中介軟體的詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="d4102-1114">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="d4102-1115">HTTP/1.1 要求清空</span><span class="sxs-lookup"><span data-stu-id="d4102-1115">HTTP/1.1 request draining</span></span>

<span data-ttu-id="d4102-1116">開啟 HTTP 連接相當耗時。</span><span class="sxs-lookup"><span data-stu-id="d4102-1116">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="d4102-1117">若是 HTTPS，也需要大量資源。</span><span class="sxs-lookup"><span data-stu-id="d4102-1117">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="d4102-1118">因此，Kestrel 會嘗試針對每個 HTTP/1.1 通訊協定重複使用連接。</span><span class="sxs-lookup"><span data-stu-id="d4102-1118">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="d4102-1119">要求主體必須完全取用，才能重複使用連線。</span><span class="sxs-lookup"><span data-stu-id="d4102-1119">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="d4102-1120">應用程式不一定會取用要求主體，例如伺服器傳回重新 `POST` 導向或404回應的要求。</span><span class="sxs-lookup"><span data-stu-id="d4102-1120">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="d4102-1121">在 `POST` -重新導向案例中：</span><span class="sxs-lookup"><span data-stu-id="d4102-1121">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="d4102-1122">用戶端可能已經傳送部分 `POST` 資料。</span><span class="sxs-lookup"><span data-stu-id="d4102-1122">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="d4102-1123">伺服器會寫入301回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-1123">The server writes the 301 response.</span></span>
* <span data-ttu-id="d4102-1124">連線無法用於新的要求，直到 `POST` 前一個要求主體的資料已完全讀取為止。</span><span class="sxs-lookup"><span data-stu-id="d4102-1124">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="d4102-1125">Kestrel 會嘗試清空要求主體。</span><span class="sxs-lookup"><span data-stu-id="d4102-1125">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="d4102-1126">清空要求主體表示在不處理資料的情況下讀取和捨棄資料。</span><span class="sxs-lookup"><span data-stu-id="d4102-1126">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="d4102-1127">清空程式可讓您在允許連線重複使用時，以及清空任何剩餘資料所需的時間之間進行 tradoff：</span><span class="sxs-lookup"><span data-stu-id="d4102-1127">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="d4102-1128">清空有五秒的時間，無法設定。</span><span class="sxs-lookup"><span data-stu-id="d4102-1128">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="d4102-1129">如果或標頭所指定的所有資料在 `Content-Length` `Transfer-Encoding` 此時間之前未被讀取，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="d4102-1129">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="d4102-1130">有時您可能會想要在寫入回應之前或之後立即終止要求。</span><span class="sxs-lookup"><span data-stu-id="d4102-1130">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="d4102-1131">例如，用戶端可能會有限制性的資料上限，因此限制上傳的資料可能是優先考慮。</span><span class="sxs-lookup"><span data-stu-id="d4102-1131">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="d4102-1132">在這種情況下，若要終止要求，請從控制器、頁面或中介軟體呼叫[HttpCoNtext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) 。 Razor</span><span class="sxs-lookup"><span data-stu-id="d4102-1132">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="d4102-1133">呼叫時有一些注意事項 `Abort` ：</span><span class="sxs-lookup"><span data-stu-id="d4102-1133">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="d4102-1134">建立新的連接可能很慢且昂貴。</span><span class="sxs-lookup"><span data-stu-id="d4102-1134">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="d4102-1135">在連接關閉之前，不保證用戶端已讀取回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-1135">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="d4102-1136">呼叫 `Abort` 應該很罕見，而且會保留給嚴重的錯誤案例，而不是常見的錯誤。</span><span class="sxs-lookup"><span data-stu-id="d4102-1136">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="d4102-1137">只有 `Abort` 在需要解決特定問題時才呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-1137">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="d4102-1138">例如， `Abort` 如果惡意用戶端嘗試 `POST` 資料或用戶端程式代碼中有錯誤，而導致大量或許多要求，請呼叫。</span><span class="sxs-lookup"><span data-stu-id="d4102-1138">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="d4102-1139">請勿呼叫 `Abort` 常見的錯誤狀況，例如 HTTP 404 (找不到) 。</span><span class="sxs-lookup"><span data-stu-id="d4102-1139">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="d4102-1140">在呼叫之前呼叫 [HttpResponse](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) ， `Abort` 可確保伺服器已完成寫入回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-1140">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="d4102-1141">不過，用戶端行為不是可預測的，而且在中斷連接之前，可能不會讀取回應。</span><span class="sxs-lookup"><span data-stu-id="d4102-1141">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="d4102-1142">HTTP/2 的這個程式不同，因為通訊協定支援中止個別要求資料流程，而不需要關閉連接。</span><span class="sxs-lookup"><span data-stu-id="d4102-1142">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="d4102-1143">五秒的清空超時時間不適用。</span><span class="sxs-lookup"><span data-stu-id="d4102-1143">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="d4102-1144">如果在完成回應之後有任何未讀取的要求主體資料，則伺服器會傳送 HTTP/2 RST 框架。</span><span class="sxs-lookup"><span data-stu-id="d4102-1144">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="d4102-1145">系統會忽略其他要求主體資料框架。</span><span class="sxs-lookup"><span data-stu-id="d4102-1145">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="d4102-1146">可能的話，最好是讓用戶端利用預期的 [： 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 要求標頭，並等待伺服器回應，然後再開始傳送要求本文。</span><span class="sxs-lookup"><span data-stu-id="d4102-1146">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="d4102-1147">這可讓用戶端有機會檢查回應，並在傳送不需要的資料之前中止。</span><span class="sxs-lookup"><span data-stu-id="d4102-1147">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4102-1148">其他資源</span><span class="sxs-lookup"><span data-stu-id="d4102-1148">Additional resources</span></span>

* <span data-ttu-id="d4102-1149">在 Linux 上使用 UNIX 通訊端時，通訊端不會在應用程式關閉時自動刪除。</span><span class="sxs-lookup"><span data-stu-id="d4102-1149">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d4102-1150">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="d4102-1150">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d4102-1151">RFC 7230：訊息語法和路由 (第 5.4 節：主機)</span><span class="sxs-lookup"><span data-stu-id="d4102-1151">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end
