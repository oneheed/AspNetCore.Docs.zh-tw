---
title: ASP.NET Core 中的 Kestrel 網頁伺服器實作
author: rick-anderson
description: 了解 Kestrel，這是 ASP.NET Core 的跨平台網頁伺服器。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
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
ms.openlocfilehash: 50bf2a60f14238c9b71fe90a64c284da202bff59
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491596"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="acb26-103">ASP.NET Core 中的 Kestrel 網頁伺服器實作</span><span class="sxs-lookup"><span data-stu-id="acb26-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="acb26-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher) 和 [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="acb26-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="acb26-105">Kestrel 是 [ASP.NET Core 的跨平台網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="acb26-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="acb26-106">Kestrel 是 ASP.NET Core 專案範本中預設隨附的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="acb26-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="acb26-107">Kestrel 支援下列案例：</span><span class="sxs-lookup"><span data-stu-id="acb26-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="acb26-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="acb26-108">HTTPS</span></span>
* <span data-ttu-id="acb26-109">用來啟用 [WebSockets](https://github.com/aspnet/websockets) 的不透明升級</span><span class="sxs-lookup"><span data-stu-id="acb26-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="acb26-110">Nginx 背後的高效能 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="acb26-111">HTTP/2 (macOS 上除外&dagger;)</span><span class="sxs-lookup"><span data-stu-id="acb26-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="acb26-112">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="acb26-113">.NET Core 支援的所有平台和版本都支援 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="acb26-114">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="acb26-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="acb26-115">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="acb26-115">HTTP/2 support</span></span>

<span data-ttu-id="acb26-116">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式使用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="acb26-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="acb26-117">作業系統&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb26-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="acb26-118">Windows Server 2016/Windows 10 或更新版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="acb26-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="acb26-119">Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="acb26-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="acb26-120">目標 Framework：.NET Core 2.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="acb26-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="acb26-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="acb26-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="acb26-122">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="acb26-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="acb26-123">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="acb26-124">&Dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。</span><span class="sxs-lookup"><span data-stu-id="acb26-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="acb26-125">支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。</span><span class="sxs-lookup"><span data-stu-id="acb26-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="acb26-126">可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。</span><span class="sxs-lookup"><span data-stu-id="acb26-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="acb26-127">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="acb26-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="acb26-128">預設會停用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="acb26-129">如需組態的詳細資訊，請參閱 [Kestrel 選項](#kestrel-options)和 [ListenOptions. 通訊協定](#listenoptionsprotocols)一節。</span><span class="sxs-lookup"><span data-stu-id="acb26-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="acb26-130">何時搭配使用 Kestrel 與反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="acb26-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="acb26-131">您可以單獨使用 Kestrel，或與 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 等「反向 Proxy 伺服器」\*\* 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="acb26-132">反向 Proxy 伺服器會從網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="acb26-133">Kestrel 用作邊緣 (網際網路對應) 網頁伺服器：</span><span class="sxs-lookup"><span data-stu-id="acb26-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="acb26-135">Kestrel 用於反向 Proxy 組態中：</span><span class="sxs-lookup"><span data-stu-id="acb26-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="acb26-137">不論是否有反向 proxy 伺服器，設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="acb26-138">Kestrel 用作不需要反向 Proxy 伺服器的 Edge Server 時，不支援在多個處理序之間共用相同的 IP 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="acb26-139">當 Kestrel 設定為接聽連接埠時，Kestrel 會處理該連接埠的所有流量，而不論要求的 `Host` 標頭為何。</span><span class="sxs-lookup"><span data-stu-id="acb26-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="acb26-140">可以共用連接埠的反向 Proxy 能夠在唯一的 IP 和連接埠上轉送要求給 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="acb26-141">即使不需要反向 Proxy 伺服器，使用反向 Proxy 伺服器也是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="acb26-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="acb26-142">反向 Proxy：</span><span class="sxs-lookup"><span data-stu-id="acb26-142">A reverse proxy:</span></span>

* <span data-ttu-id="acb26-143">可以限制它所主控之應用程式的公開介面區。</span><span class="sxs-lookup"><span data-stu-id="acb26-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="acb26-144">提供額外的組態和防禦層。</span><span class="sxs-lookup"><span data-stu-id="acb26-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="acb26-145">能夠與現有基礎結構更好地整合。</span><span class="sxs-lookup"><span data-stu-id="acb26-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="acb26-146">簡化負載平衡和安全通訊 (HTTPS) 組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="acb26-147">只有反向 proxy 伺服器需要 x.509 憑證，而且該伺服器可以使用一般 HTTP 與內部網路上的應用程式伺服器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="acb26-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="acb26-148">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acb26-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="acb26-149">ASP.NET Core apps 中的 Kestrel</span><span class="sxs-lookup"><span data-stu-id="acb26-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="acb26-150">ASP.NET Core 專案範本預設會使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="acb26-151">在 *Program.cs*中，此 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> ：</span><span class="sxs-lookup"><span data-stu-id="acb26-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="acb26-152">如需建立主機的詳細資訊，請參閱的 *設定主* 控制項和 *預設* 建立器設定一節 <xref:fundamentals/host/generic-host#set-up-a-host> 。</span><span class="sxs-lookup"><span data-stu-id="acb26-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="acb26-153">若要在呼叫 `ConfigureWebHostDefaults` 之後提供額外的設定，請使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="acb26-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="acb26-154">Kestrel 選項</span><span class="sxs-lookup"><span data-stu-id="acb26-154">Kestrel options</span></span>

<span data-ttu-id="acb26-155">Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。</span><span class="sxs-lookup"><span data-stu-id="acb26-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="acb26-156">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。</span><span class="sxs-lookup"><span data-stu-id="acb26-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="acb26-157">`Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="acb26-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="acb26-158">下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；</span><span class="sxs-lookup"><span data-stu-id="acb26-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="acb26-159">在本文稍後所示的範例中，會在 c # 程式碼中設定 Kestrel 選項。</span><span class="sxs-lookup"><span data-stu-id="acb26-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="acb26-160">您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項。</span><span class="sxs-lookup"><span data-stu-id="acb26-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="acb26-161">例如，檔案設定 [提供者](xref:fundamentals/configuration/index#file-configuration-provider) 可以從 *appsettings.json* 或 appsettings 載入 Kestrel 設定 *。 {環境}. json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="acb26-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="acb26-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [端點](#endpoint-configuration) 設定可從設定提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="acb26-163">其餘的 Kestrel 設定必須在 c # 程式碼中設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="acb26-164">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="acb26-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="acb26-165">設定 Kestrel in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="acb26-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="acb26-166">將的實例插入 `IConfiguration` 至 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="acb26-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="acb26-167">下列範例假設將插入的設定指派給 `Configuration` 屬性。</span><span class="sxs-lookup"><span data-stu-id="acb26-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="acb26-168">在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="acb26-169">設定 Kestrel 建立主機時：</span><span class="sxs-lookup"><span data-stu-id="acb26-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="acb26-170">在 *Program.cs*中，將 `Kestrel` Configuration 區段載入 Kestrel 的設定中：</span><span class="sxs-lookup"><span data-stu-id="acb26-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="acb26-171">上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="acb26-172">Keep-alive 逾時</span><span class="sxs-lookup"><span data-stu-id="acb26-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="acb26-173">取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="acb26-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="acb26-174">預設為 2 分鐘。</span><span class="sxs-lookup"><span data-stu-id="acb26-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="acb26-175">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="acb26-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="acb26-176">可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：</span><span class="sxs-lookup"><span data-stu-id="acb26-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="acb26-177">已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="acb26-178">升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="acb26-179">連線數目上限預設為無限制 (null)。</span><span class="sxs-lookup"><span data-stu-id="acb26-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="acb26-180">要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="acb26-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="acb26-181">預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="acb26-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="acb26-182">若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：</span><span class="sxs-lookup"><span data-stu-id="acb26-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acb26-183">以下範例會示範如何設定應用程式、每個要求的條件約束：</span><span class="sxs-lookup"><span data-stu-id="acb26-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="acb26-184">覆寫中介軟體中特定要求的設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="acb26-185">如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="acb26-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="acb26-186">有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="acb26-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acb26-187">當應用程式是在 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)後方於[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)執行時，Kestrel 的要求本文大小限制將會被停用，因為 IIS 已經設定限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="acb26-188">要求主體資料速率下限</span><span class="sxs-lookup"><span data-stu-id="acb26-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="acb26-189">如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。</span><span class="sxs-lookup"><span data-stu-id="acb26-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="acb26-190">如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 讓用戶端提高其傳送速率的時間量（最小值）;在這段時間內不會檢查速率。</span><span class="sxs-lookup"><span data-stu-id="acb26-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="acb26-191">寬限期可協助避免中斷連線，這是由於 TCP 緩慢啟動而一開始以低速傳送資料所造成。</span><span class="sxs-lookup"><span data-stu-id="acb26-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="acb26-192">預設速率下限為 240 個位元組/秒，寬限期為 5 秒。</span><span class="sxs-lookup"><span data-stu-id="acb26-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="acb26-193">速率下限也適用於回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="acb26-194">除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。</span><span class="sxs-lookup"><span data-stu-id="acb26-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="acb26-195">以下範例示範如何在 *Program.cs* 中設定資料速率下限：</span><span class="sxs-lookup"><span data-stu-id="acb26-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="acb26-196">覆寫中介軟體中每個要求的最小速率限制：</span><span class="sxs-lookup"><span data-stu-id="acb26-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="acb26-197">因為通訊協定對要求多工的支援，所以 HTTP/2 一般不支援以每一要求基礎修改速率限制，進而使先前範例中所參考的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> 不會出現在 HTTP/2 要求的 `HttpContext.Features` 中。</span><span class="sxs-lookup"><span data-stu-id="acb26-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="acb26-198">不過，<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> 仍存在 HTTP/2 要求的 `HttpContext.Features`您仍能透過將 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 設定為 `null` (即使是針對 HTTP/2 要求)，以個別要求基礎來「完全停用」\*\* 讀取素率限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="acb26-199">嘗試讀取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或嘗試將它設定為 `null` 以外的值將會導致擲回 `NotSupportedException` (假設要求是 HTTP/2 要求)。</span><span class="sxs-lookup"><span data-stu-id="acb26-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="acb26-200">透過 `KestrelServerOptions.Limits` 設定的全伺服器速率限制皆仍套用至 HTTP/1.x 及 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="acb26-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="acb26-201">要求標頭逾時</span><span class="sxs-lookup"><span data-stu-id="acb26-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="acb26-202">取得或設定伺服器花費在接收要求標頭的時間上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="acb26-203">預設為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="acb26-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="acb26-204">每個連線的資料流數目上限</span><span class="sxs-lookup"><span data-stu-id="acb26-204">Maximum streams per connection</span></span>

<span data-ttu-id="acb26-205">`Http2.MaxStreamsPerConnection` 會限制每個 HTTP/2 連線的同時要求資料流數目。</span><span class="sxs-lookup"><span data-stu-id="acb26-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="acb26-206">超出的資料流會被拒絕。</span><span class="sxs-lookup"><span data-stu-id="acb26-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="acb26-207">預設值是 100。</span><span class="sxs-lookup"><span data-stu-id="acb26-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="acb26-208">標頭表格大小</span><span class="sxs-lookup"><span data-stu-id="acb26-208">Header table size</span></span>

<span data-ttu-id="acb26-209">HPACK 解碼器可解壓縮 HTTP/2 連線的 HTTP 標頭。</span><span class="sxs-lookup"><span data-stu-id="acb26-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="acb26-210">`Http2.HeaderTableSize` 會限制 HPACK 解碼器所使用的標頭壓縮表格大小。</span><span class="sxs-lookup"><span data-stu-id="acb26-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="acb26-211">這個值是以八位元提供，而且必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="acb26-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="acb26-212">預設值為 4096。</span><span class="sxs-lookup"><span data-stu-id="acb26-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="acb26-213">框架大小上限</span><span class="sxs-lookup"><span data-stu-id="acb26-213">Maximum frame size</span></span>

<span data-ttu-id="acb26-214">`Http2.MaxFrameSize` 指出伺服器接收或傳送之 HTTP/2 連接框架承載的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="acb26-215">這個值是以八位元提供，而且必須介於 2^14 (16,384) 到 2^24-1 (16,777,215) 之間。</span><span class="sxs-lookup"><span data-stu-id="acb26-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="acb26-216">預設值為 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="acb26-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="acb26-217">要求標頭大小上限</span><span class="sxs-lookup"><span data-stu-id="acb26-217">Maximum request header size</span></span>

<span data-ttu-id="acb26-218">`Http2.MaxRequestHeaderFieldSize` 以八位元表示要求標頭值的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="acb26-219">這項限制適用于其壓縮和未壓縮標記法中的名稱和值。</span><span class="sxs-lookup"><span data-stu-id="acb26-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="acb26-220">此值必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="acb26-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="acb26-221">預設值為 8,192。</span><span class="sxs-lookup"><span data-stu-id="acb26-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="acb26-222">初始連線視窗大小</span><span class="sxs-lookup"><span data-stu-id="acb26-222">Initial connection window size</span></span>

<span data-ttu-id="acb26-223">`Http2.InitialConnectionWindowSize` 會以位元組表示伺服器緩衝每個連線之所有要求 (資料流) 單次彙總的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="acb26-224">要求也皆受 `Http2.InitialStreamWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="acb26-225">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="acb26-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="acb26-226">預設值為 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="acb26-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="acb26-227">初始資料流視窗大小</span><span class="sxs-lookup"><span data-stu-id="acb26-227">Initial stream window size</span></span>

<span data-ttu-id="acb26-228">`Http2.InitialStreamWindowSize` 會以位元組表示每個要求 (資料流) 單次伺服器緩衝的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="acb26-229">要求也皆受 `Http2.InitialConnectionWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="acb26-230">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="acb26-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="acb26-231">預設值為 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="acb26-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="acb26-232">HTTP/2 保持運作 ping 設定</span><span class="sxs-lookup"><span data-stu-id="acb26-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="acb26-233">Kestrel 可以設定為將 HTTP/2 ping 傳送至連線的用戶端。</span><span class="sxs-lookup"><span data-stu-id="acb26-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="acb26-234">HTTP/2 ping 的服務有多個用途：</span><span class="sxs-lookup"><span data-stu-id="acb26-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="acb26-235">保持閒置連接保持運作。</span><span class="sxs-lookup"><span data-stu-id="acb26-235">Keep idle connections alive.</span></span> <span data-ttu-id="acb26-236">某些用戶端和 proxy 伺服器會關閉閒置的連線。</span><span class="sxs-lookup"><span data-stu-id="acb26-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="acb26-237">HTTP/2 ping 會視為連線上的活動，並防止連接關閉為閒置。</span><span class="sxs-lookup"><span data-stu-id="acb26-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="acb26-238">關閉狀況不良的連接。</span><span class="sxs-lookup"><span data-stu-id="acb26-238">Close unhealthy connections.</span></span> <span data-ttu-id="acb26-239">在設定的時間內，用戶端沒有回應「保持運作」偵測的連接會由伺服器關閉。</span><span class="sxs-lookup"><span data-stu-id="acb26-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="acb26-240">有兩個與 HTTP/2 保持運作 ping 相關的設定選項：</span><span class="sxs-lookup"><span data-stu-id="acb26-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="acb26-241">`Http2.KeepAlivePingInterval` 是設定 `TimeSpan` 偵測內部的。</span><span class="sxs-lookup"><span data-stu-id="acb26-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="acb26-242">如果伺服器在這段時間內未收到任何框架，則伺服器會將保持運作的偵測傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="acb26-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="acb26-243">當此選項設定為時，會停用 [保持作用中的 ping] `TimeSpan.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="acb26-244">預設值是 `TimeSpan.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="acb26-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="acb26-245">`Http2.KeepAlivePingTimeout` 是 `TimeSpan` 設定 ping timeout 的。</span><span class="sxs-lookup"><span data-stu-id="acb26-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="acb26-246">如果伺服器未在此時間內接收任何框架，例如回應偵測，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="acb26-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="acb26-247">當此選項設定為時，會停用 [保持作用中] 超時 `TimeSpan.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="acb26-248">預設值為 20 秒。</span><span class="sxs-lookup"><span data-stu-id="acb26-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="acb26-249">預告片</span><span class="sxs-lookup"><span data-stu-id="acb26-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="acb26-250">重設</span><span class="sxs-lookup"><span data-stu-id="acb26-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="acb26-251">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="acb26-251">Synchronous I/O</span></span>

<span data-ttu-id="acb26-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="acb26-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="acb26-253">預設值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="acb26-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="acb26-254">大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="acb26-255">只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。</span><span class="sxs-lookup"><span data-stu-id="acb26-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="acb26-256">下列範例會啟用同步 i/o：</span><span class="sxs-lookup"><span data-stu-id="acb26-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="acb26-257">如需其他 Kestrel 選項和限制的資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="acb26-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="acb26-258">端點組態</span><span class="sxs-lookup"><span data-stu-id="acb26-258">Endpoint configuration</span></span>

<span data-ttu-id="acb26-259">ASP.NET Core 預設會繫結至：</span><span class="sxs-lookup"><span data-stu-id="acb26-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="acb26-260">`https://localhost:5001` (當有本機開發憑證存在時)</span><span class="sxs-lookup"><span data-stu-id="acb26-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="acb26-261">使用以下各項指定 URL：</span><span class="sxs-lookup"><span data-stu-id="acb26-261">Specify URLs using the:</span></span>

* <span data-ttu-id="acb26-262">`ASPNETCORE_URLS` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="acb26-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="acb26-263">`--urls` 命令列引數。</span><span class="sxs-lookup"><span data-stu-id="acb26-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="acb26-264">`urls` 主機組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="acb26-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="acb26-265">`UseUrls` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="acb26-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="acb26-266">使用這些方法提供的值可以是一或多個 HTTP 和 HTTPS 端點 (如果有預設憑證可用則為 HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="acb26-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="acb26-267">將值設定為以分號分隔的清單 (例如，`"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="acb26-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="acb26-268">如需有關這些方法的詳細資訊，請參閱[伺服器 URL](xref:fundamentals/host/web-host#server-urls) 和[覆寫設定](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="acb26-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="acb26-269">開發憑證會建立於：</span><span class="sxs-lookup"><span data-stu-id="acb26-269">A development certificate is created:</span></span>

* <span data-ttu-id="acb26-270">已安裝 [.NET Core SDK](/dotnet/core/sdk) 時。</span><span class="sxs-lookup"><span data-stu-id="acb26-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="acb26-271">[dev-certs 工具](xref:aspnetcore-2.1#https)用來建立憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="acb26-272">某些瀏覽器需要授與明確的許可權，以信任本機開發憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="acb26-273">專案範本預設會將應用程式設定為在 HTTPS 上執行，並包含 Https 重新導向 [和 HSTS 支援](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="acb26-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="acb26-274">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上呼叫 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法，來為 Kestrel 設定 URL 首碼和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="acb26-275">`UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵和 `ASPNETCORE_URLS` 環境變數同樣有效，但卻有本節稍後註明的限制 (針對 HTTPS 端點組態必須有預設憑證可用)。</span><span class="sxs-lookup"><span data-stu-id="acb26-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="acb26-276">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="acb26-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="acb26-277">ConfigureEndpointDefaults (動作 \<ListenOptions>) </span><span class="sxs-lookup"><span data-stu-id="acb26-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="acb26-278">指定組態 `Action` 以針對每個指定端點執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="acb26-279">呼叫 `ConfigureEndpointDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acb26-280">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="acb26-281">ConfigureHttpsDefaults (動作 \<HttpsConnectionAdapterOptions>) </span><span class="sxs-lookup"><span data-stu-id="acb26-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="acb26-282">指定組態 `Action` 以針對每個 HTTPS 端點執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="acb26-283">呼叫 `ConfigureHttpsDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acb26-284">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="acb26-285">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="acb26-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="acb26-286">建立設定載入器來設定以 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作為輸入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="acb26-287">組態的範圍必須限於 Kestrel 的組態區段。</span><span class="sxs-lookup"><span data-stu-id="acb26-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="acb26-288">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="acb26-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="acb26-289">設定 Kestrel 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="acb26-290">`ListenOptions.UseHttps` 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="acb26-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="acb26-291">`UseHttps`：設定 Kestrel 以搭配預設憑證使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="acb26-292">如果未設定預設憑證，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="acb26-292">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="acb26-293">`ListenOptions.UseHttps` 參數：</span><span class="sxs-lookup"><span data-stu-id="acb26-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="acb26-294">`filename` 是憑證檔案的路徑和檔案名稱，它相對於包含應用程式內容檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="acb26-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="acb26-295">`password` 是存取 X.509 憑證資料所需的密碼。</span><span class="sxs-lookup"><span data-stu-id="acb26-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="acb26-296">`configureOptions` 是設定 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="acb26-297">傳回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="acb26-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="acb26-298">`storeName` 是要從中載入憑證的憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="acb26-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="acb26-299">`subject` 是憑證的主體名稱。</span><span class="sxs-lookup"><span data-stu-id="acb26-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="acb26-300">`allowInvalid` 表示是否應該考慮無效的憑證，例如自我簽署憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="acb26-301">`location` 是要從中載入憑證的存放區位置。</span><span class="sxs-lookup"><span data-stu-id="acb26-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="acb26-302">`serverCertificate` 是 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="acb26-303">在生產環境中，必須明確設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="acb26-304">至少必須提供預設憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="acb26-305">支援的組態描述如下：</span><span class="sxs-lookup"><span data-stu-id="acb26-305">Supported configurations described next:</span></span>

* <span data-ttu-id="acb26-306">無組態</span><span class="sxs-lookup"><span data-stu-id="acb26-306">No configuration</span></span>
* <span data-ttu-id="acb26-307">從組態取代預設憑證</span><span class="sxs-lookup"><span data-stu-id="acb26-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="acb26-308">變更程式碼中的預設值</span><span class="sxs-lookup"><span data-stu-id="acb26-308">Change the defaults in code</span></span>

<span data-ttu-id="acb26-309">*無組態*</span><span class="sxs-lookup"><span data-stu-id="acb26-309">*No configuration*</span></span>

<span data-ttu-id="acb26-310">Kestrel 會接聽 `http://localhost:5000` 和 `https://localhost:5001` (如果預設憑證可用的話)。</span><span class="sxs-lookup"><span data-stu-id="acb26-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="acb26-311">*從組態取代預設憑證*</span><span class="sxs-lookup"><span data-stu-id="acb26-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="acb26-312">`CreateDefaultBuilder` 預設會呼叫 `Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="acb26-313">Kestrel 可以使用預設的 HTTPS 應用程式設定組態結構描述。</span><span class="sxs-lookup"><span data-stu-id="acb26-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="acb26-314">設定多個端點，包括 URL 和要使用的憑證－從磁碟上的檔案，或是從憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="acb26-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="acb26-315">在下列 *appsettings.json* 範例中：</span><span class="sxs-lookup"><span data-stu-id="acb26-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="acb26-316">將 **AllowInvalid** 設定為 `true`，允許使用無效的憑證 (例如，自我簽署憑證)。</span><span class="sxs-lookup"><span data-stu-id="acb26-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="acb26-317">任何未指定憑證 (接下來範例中的 **HttpsDefaultCert**) 的 HTTPS 端點會回復為 [憑證]**[預設]** > \*\*\*\* 下定義的憑證或開發憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="acb26-318">除了針對任何憑證節點使用 [路徑]\*\*\*\* 和 [密碼]\*\*\*\*，還可以使用憑證存放區欄位指定憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="acb26-319">例如，您可以將**憑證**  >  **預設**憑證指定為：</span><span class="sxs-lookup"><span data-stu-id="acb26-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="acb26-320">結構描述附註：</span><span class="sxs-lookup"><span data-stu-id="acb26-320">Schema notes:</span></span>

* <span data-ttu-id="acb26-321">端點名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="acb26-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="acb26-322">例如，`HTTPS` 和 `Https` 都有效。</span><span class="sxs-lookup"><span data-stu-id="acb26-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="acb26-323">`Url` 參數對每個端點而言都是必要的。</span><span class="sxs-lookup"><span data-stu-id="acb26-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="acb26-324">此參數的格式等同於最上層 `Urls` 組態參數，但是它限制為單一值。</span><span class="sxs-lookup"><span data-stu-id="acb26-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="acb26-325">這些端點會取代最上層 `Urls` 組態中定義的端點，而不是新增至其中。</span><span class="sxs-lookup"><span data-stu-id="acb26-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="acb26-326">透過 `Listen` 在程式碼中定義的端點，會與組態區段中定義的端點累計。</span><span class="sxs-lookup"><span data-stu-id="acb26-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="acb26-327">`Certificate` 區段是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="acb26-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="acb26-328">如果未指定 `Certificate` 區段，則會使用先前案例中所定義的預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="acb26-329">如果沒有預設值可供使用，伺服器就會擲回例外狀況，且無法啟動。</span><span class="sxs-lookup"><span data-stu-id="acb26-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="acb26-330">`Certificate`區段同時支援**路徑** &ndash; **密碼**和**主體** &ndash; **存放區**憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="acb26-331">可以用這種方式定義任何數目的端點，只要它們不會導致連接埠衝突即可。</span><span class="sxs-lookup"><span data-stu-id="acb26-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="acb26-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 會傳回 `KestrelConfigurationLoader` 與 `.Endpoint(string name, listenOptions => { })` 方法，此方法可用來補充已設定的端點設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="acb26-333">`KestrelServerOptions.ConfigurationLoader` 可以直接存取，以繼續逐一查看現有的載入器，例如所提供的載入器 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 。</span><span class="sxs-lookup"><span data-stu-id="acb26-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="acb26-334">您可以在方法的選項中使用每個端點的設定區段， `Endpoint` 以便讀取自訂設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="acb26-335">可以藉由使用另一個區段再次呼叫 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 而載入多個組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="acb26-336">只會使用最後一個組態，除非在先前的執行個體上已明確呼叫 `Load`。</span><span class="sxs-lookup"><span data-stu-id="acb26-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="acb26-337">中繼套件不會呼叫 `Load`，如此可能會取代其預設組態區段。</span><span class="sxs-lookup"><span data-stu-id="acb26-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="acb26-338">`KestrelConfigurationLoader` 會將來自 `KestrelServerOptions` 的 API 的 `Listen` 系列鏡像為 `Endpoint` 多載，所以可在相同的位置設定程式碼和設定端點。</span><span class="sxs-lookup"><span data-stu-id="acb26-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="acb26-339">這些多載不使用名稱，並且只使用來自組態的預設組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="acb26-340">*變更程式碼中的預設值*</span><span class="sxs-lookup"><span data-stu-id="acb26-340">*Change the defaults in code*</span></span>

<span data-ttu-id="acb26-341">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 可以用來變更 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的預設設定，包括覆寫先前案例中指定的預設憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="acb26-342">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 應該在設定任何端點之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="acb26-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="acb26-343">*SNI 的 Kestrel 支援*</span><span class="sxs-lookup"><span data-stu-id="acb26-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="acb26-344">[伺服器名稱指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可以用於在相同的 IP 位址和連接埠上裝載多個網域。</span><span class="sxs-lookup"><span data-stu-id="acb26-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="acb26-345">SNI 若要運作，用戶端會在 TLS 信號交換期間傳送安全工作階段的主機名稱給伺服器，讓伺服器可以提供正確的憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="acb26-346">用戶端在 TLS 信號交換之後的安全工作階段期間，會使用所提供的憑證與伺服器進行加密通訊。</span><span class="sxs-lookup"><span data-stu-id="acb26-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="acb26-347">Kestrel 透過 `ServerCertificateSelector` 回呼來支援 SNI。</span><span class="sxs-lookup"><span data-stu-id="acb26-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="acb26-348">回呼會針對每個連線叫用一次，允許應用程式檢查主機名稱並選取適當的憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="acb26-349">SNI 支援需要：</span><span class="sxs-lookup"><span data-stu-id="acb26-349">SNI support requires:</span></span>

* <span data-ttu-id="acb26-350">在目標 framework `netcoreapp2.1` 或更新版本上執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="acb26-351">在 `net461` 或更新版本中，會叫用回呼，但 `name` 一律為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="acb26-352">如果用戶端不在 TLS 信號交換中提供主機名稱參數，則 `name` 也是 `null`。</span><span class="sxs-lookup"><span data-stu-id="acb26-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="acb26-353">所有網站都在相同的 Kestrel 執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="acb26-354">在不使用反向 Proxy 的情況下，Kestrel 不支援跨多個執行個體共用 IP 位址和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="acb26-355">連接記錄</span><span class="sxs-lookup"><span data-stu-id="acb26-355">Connection logging</span></span>

<span data-ttu-id="acb26-356">呼叫 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以發出連接上位元組層級通訊的 Debug 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="acb26-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="acb26-357">連線記錄有助於疑難排解低層級通訊中的問題，例如在 TLS 加密期間和 proxy 後方。</span><span class="sxs-lookup"><span data-stu-id="acb26-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="acb26-358">如果 `UseConnectionLogging` 之前放置 `UseHttps` ，則會記錄加密的流量。</span><span class="sxs-lookup"><span data-stu-id="acb26-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="acb26-359">如果 `UseConnectionLogging` 放置在之後 `UseHttps` ，則會記錄解密的流量。</span><span class="sxs-lookup"><span data-stu-id="acb26-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="acb26-360">繫結至 TCP 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-360">Bind to a TCP socket</span></span>

<span data-ttu-id="acb26-361"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法會繫結至 TCP 通訊端，而選項 Lambda 則會允許 X.509 憑證設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="acb26-362">此範例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>來為端點設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="acb26-363">若要設定特定端點的其他 Kestrel 設定，請使用相同的 API。</span><span class="sxs-lookup"><span data-stu-id="acb26-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="acb26-364">繫結至 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-364">Bind to a Unix socket</span></span>

<span data-ttu-id="acb26-365">請使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 在 Unix 通訊端上進行接聽以改善 Nginx 的效能，如此範例所示：</span><span class="sxs-lookup"><span data-stu-id="acb26-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="acb26-366">在 Nginx 設定檔中，將 `server`  >  `location`  >  `proxy_pass` 專案設定為 `http://unix:/tmp/{KESTREL SOCKET}:/;` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="acb26-367">`{KESTREL SOCKET}` 這是提供給 (的通訊端名稱 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ，例如 `kestrel-test.sock` 上述範例) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="acb26-368">確定 Nginx (可寫入通訊端，例如 `chmod go+w /tmp/kestrel-test.sock`) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="acb26-369">連接埠 0</span><span class="sxs-lookup"><span data-stu-id="acb26-369">Port 0</span></span>

<span data-ttu-id="acb26-370">指定連接埠號碼 `0` 時，Kestrel 會動態繫結至可用的連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="acb26-371">下列範例示範如何判斷 Kestrel 在執行階段實際上繫結至哪一個連接埠：</span><span class="sxs-lookup"><span data-stu-id="acb26-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="acb26-372">當應用程式執行時，主控台視窗輸出會指出可以連線到應用程式的動態連接埠：</span><span class="sxs-lookup"><span data-stu-id="acb26-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="acb26-373">限制</span><span class="sxs-lookup"><span data-stu-id="acb26-373">Limitations</span></span>

<span data-ttu-id="acb26-374">使用下列方法來設定端點：</span><span class="sxs-lookup"><span data-stu-id="acb26-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="acb26-375">`--urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="acb26-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="acb26-376">`urls` 主機組態索引鍵</span><span class="sxs-lookup"><span data-stu-id="acb26-376">`urls` host configuration key</span></span>
* <span data-ttu-id="acb26-377">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="acb26-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="acb26-378">要讓程式碼使用 Kestrel 以外的伺服器，這些方法會很有用。</span><span class="sxs-lookup"><span data-stu-id="acb26-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="acb26-379">不過，請注意下列限制：</span><span class="sxs-lookup"><span data-stu-id="acb26-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="acb26-380">HTTPS 無法與這些方法搭配使用，除非在 HTTPS 端點設定中提供預設憑證 (例如，使用 `KestrelServerOptions` 設定或設定檔，如本主題稍早所示)。</span><span class="sxs-lookup"><span data-stu-id="acb26-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="acb26-381">當同時使用 `Listen` 和 `UseUrls` 方法時，`Listen` 端點會覆寫 `UseUrls` 端點。</span><span class="sxs-lookup"><span data-stu-id="acb26-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="acb26-382">IIS 端點設定</span><span class="sxs-lookup"><span data-stu-id="acb26-382">IIS endpoint configuration</span></span>

<span data-ttu-id="acb26-383">使用 IIS 時，IIS 覆寫繫結的 URL 繫結是由 `Listen` 或 `UseUrls` 設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="acb26-384">如需詳細資訊，請參閱 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)主題。</span><span class="sxs-lookup"><span data-stu-id="acb26-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="acb26-385">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="acb26-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="acb26-386">`Protocols` 屬性會建立在連線端點上或針對伺服器啟用的 HTTP 通訊協定 (`HttpProtocols`)。</span><span class="sxs-lookup"><span data-stu-id="acb26-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="acb26-387">從 `HttpProtocols` 列舉中指派一個值給 `Protocols` 屬性。</span><span class="sxs-lookup"><span data-stu-id="acb26-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="acb26-388">`HttpProtocols` 列舉值</span><span class="sxs-lookup"><span data-stu-id="acb26-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="acb26-389">允許的連線通訊協定</span><span class="sxs-lookup"><span data-stu-id="acb26-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="acb26-390">僅限 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acb26-390">HTTP/1.1 only.</span></span> <span data-ttu-id="acb26-391">可在具有或沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="acb26-392">僅限 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-392">HTTP/2 only.</span></span> <span data-ttu-id="acb26-393">只有在用戶端支援[先備知識模式](https://tools.ietf.org/html/rfc7540#section-3.4)時，才可以在沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="acb26-394">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="acb26-395">HTTP/2 要求用戶端在 TLS [應用層通訊協定協商 ](https://tools.ietf.org/html/rfc7301#section-3) 中選取 HTTP/2， (ALPN) 交握;否則，連接預設為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acb26-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="acb26-396">`ListenOptions.Protocols`任何端點的預設值為 `HttpProtocols.Http1AndHttp2` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="acb26-397">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="acb26-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="acb26-398">TLS 1.2 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="acb26-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="acb26-399">已停用重新交涉</span><span class="sxs-lookup"><span data-stu-id="acb26-399">Renegotiation disabled</span></span>
* <span data-ttu-id="acb26-400">已停用壓縮</span><span class="sxs-lookup"><span data-stu-id="acb26-400">Compression disabled</span></span>
* <span data-ttu-id="acb26-401">暫時金鑰交換大小下限：</span><span class="sxs-lookup"><span data-stu-id="acb26-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="acb26-402">橢圓曲線 Diffie-Hellman (>ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; ：最少224位</span><span class="sxs-lookup"><span data-stu-id="acb26-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="acb26-403">有限的欄位 Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; ：最低2048位</span><span class="sxs-lookup"><span data-stu-id="acb26-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="acb26-404">不禁止加密套件。</span><span class="sxs-lookup"><span data-stu-id="acb26-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="acb26-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack;預設支援使用 P-256 橢圓曲線 &lbrack; `FIPS186` &rbrack; 。</span><span class="sxs-lookup"><span data-stu-id="acb26-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="acb26-406">下列範例會允許連接埠 8000 上的 HTTP/1.1 和 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="acb26-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="acb26-407">這些連線使用提供的憑證受到 TLS 保護：</span><span class="sxs-lookup"><span data-stu-id="acb26-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="acb26-408">若有需要，請使用連線中介軟體針對特定加密篩選每個連接的 TLS 交握。</span><span class="sxs-lookup"><span data-stu-id="acb26-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="acb26-409">下列範例 <xref:System.NotSupportedException> 會針對應用程式不支援的任何加密演算法擲回。</span><span class="sxs-lookup"><span data-stu-id="acb26-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="acb26-410">或者，將 [ITlsHandshakeFeature](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 定義和比較成可接受的加密套件清單。</span><span class="sxs-lookup"><span data-stu-id="acb26-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="acb26-411">CipherAlgorithmType 不會使用加密 [。 Null](xref:System.Security.Authentication.CipherAlgorithmType) 加密演算法。</span><span class="sxs-lookup"><span data-stu-id="acb26-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="acb26-412">您也可以透過 lambda 設定連接篩選 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="acb26-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="acb26-413">在 Linux 上， <xref:System.Net.Security.CipherSuitesPolicy> 可用來篩選每個連接的 TLS 交握：</span><span class="sxs-lookup"><span data-stu-id="acb26-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="acb26-414">從設定進行通訊協定設定\*\*</span><span class="sxs-lookup"><span data-stu-id="acb26-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="acb26-415">`CreateDefaultBuilder` 預設會呼叫 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="acb26-416">下列 *appsettings.js* 範例會建立 HTTP/1.1 作為所有端點的預設連接通訊協定：</span><span class="sxs-lookup"><span data-stu-id="acb26-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="acb26-417">下列 *appsettings.js* 範例會建立特定端點的 HTTP/1.1 連接通訊協定：</span><span class="sxs-lookup"><span data-stu-id="acb26-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="acb26-418">程式碼中指定的通訊協定會覆寫設定所設定的值。</span><span class="sxs-lookup"><span data-stu-id="acb26-418">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="acb26-419">傳輸組態</span><span class="sxs-lookup"><span data-stu-id="acb26-419">Transport configuration</span></span>

<span data-ttu-id="acb26-420">對於需要使用 Libuv () 的專案 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ：</span><span class="sxs-lookup"><span data-stu-id="acb26-420">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="acb26-421">將 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 套件的相依性新增至應用程式的專案檔中：</span><span class="sxs-lookup"><span data-stu-id="acb26-421">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="acb26-422"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>在上呼叫 `IWebHostBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="acb26-422">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="acb26-423">URL 前置詞</span><span class="sxs-lookup"><span data-stu-id="acb26-423">URL prefixes</span></span>

<span data-ttu-id="acb26-424">使用 `UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵或 `ASPNETCORE_URLS` 環境變數時，URL 前置詞可以採用下列任一格式。</span><span class="sxs-lookup"><span data-stu-id="acb26-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="acb26-425">只有 HTTP URL 前置詞有效。</span><span class="sxs-lookup"><span data-stu-id="acb26-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="acb26-426">使用 `UseUrls` 來設定 URL 繫結時，Kestrel 不支援 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="acb26-427">IPv4 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="acb26-428">`0.0.0.0` 是繫結至所有 IPv4 位址的特殊情況。</span><span class="sxs-lookup"><span data-stu-id="acb26-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="acb26-429">IPv6 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="acb26-430">`[::]` 是相當於 IPv4 `0.0.0.0` 的 IPv6 對等項目。</span><span class="sxs-lookup"><span data-stu-id="acb26-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="acb26-431">主機名稱與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="acb26-432">主機名稱 `*` 和 `+` 並不特殊。</span><span class="sxs-lookup"><span data-stu-id="acb26-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="acb26-433">無法辨識為有效 IP 位址或 `localhost` 的任何項目，都會繫結至所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="acb26-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="acb26-434">若要在相同連接埠上將不同的主機名稱繫結至不同的 ASP.NET Core 應用程式，請使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向 Proxy 伺服器 (例如 IIS、Nginx 或 Apache)。</span><span class="sxs-lookup"><span data-stu-id="acb26-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="acb26-435">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acb26-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="acb26-436">主機 `localhost` 名稱與連接埠號碼，或回送 IP 與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="acb26-437">如果指定 `localhost`，Kestrel 會嘗試同時繫結至 IPv4 和 IPv6 回送介面。</span><span class="sxs-lookup"><span data-stu-id="acb26-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="acb26-438">如果所要求的連接埠在任一個回送介面上由另一個服務使用，則 Kestrel 無法啟動。</span><span class="sxs-lookup"><span data-stu-id="acb26-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="acb26-439">如果任一回送介面由於任何其他原因 (最常見的原因是不支援 IPv6) 無法使用，Kestrel 就會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="acb26-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="acb26-440">主機篩選</span><span class="sxs-lookup"><span data-stu-id="acb26-440">Host filtering</span></span>

<span data-ttu-id="acb26-441">雖然 Kestrel 根據前置詞來支援組態，例如 `http://example.com:5000`，Kestrel 大多會忽略主機名稱。</span><span class="sxs-lookup"><span data-stu-id="acb26-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="acb26-442">主機 `localhost` 是特殊情況，用來繫結到回送位址。</span><span class="sxs-lookup"><span data-stu-id="acb26-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="acb26-443">任何非明確 IP 位址的主機，會繫結至所有公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="acb26-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="acb26-444">`Host` 標頭未驗證。</span><span class="sxs-lookup"><span data-stu-id="acb26-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="acb26-445">因應措施是使用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="acb26-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="acb26-446">主機篩選中介軟體是由 [AspNetCore HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 套件所提供，該套件是針對 ASP.NET Core 應用程式隱含提供的。</span><span class="sxs-lookup"><span data-stu-id="acb26-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="acb26-447">中介軟體是由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 所新增，它會呼叫 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>：</span><span class="sxs-lookup"><span data-stu-id="acb26-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="acb26-448">預設停用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="acb26-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="acb26-449">若要啟用中介軟體，請 `AllowedHosts` 在 appsettings*上的appsettings.js*中定義金鑰 / \* \<EnvironmentName> 。json\*。</span><span class="sxs-lookup"><span data-stu-id="acb26-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="acb26-450">此值是以分號分隔的主機名稱清單，不含連接埠號碼：</span><span class="sxs-lookup"><span data-stu-id="acb26-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="acb26-451">*appsettings.js*：</span><span class="sxs-lookup"><span data-stu-id="acb26-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="acb26-452">[轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)也有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 選項。</span><span class="sxs-lookup"><span data-stu-id="acb26-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="acb26-453">在不同的案例中，轉送標頭中介軟體和主機篩選中介軟體有類似的功能。</span><span class="sxs-lookup"><span data-stu-id="acb26-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="acb26-454">當不保留 `Host` 標頭，卻使用反向 Proxy 伺服器或負載平衡器轉送要求時，可使用轉送標頭中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="acb26-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="acb26-455">當使用 Kestrel 作為公眾對應 Edge Server，或直接轉送 `Host` 標頭時，可使用主機篩選中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="acb26-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="acb26-456">如需轉送標頭中介軟體的詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="acb26-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="acb26-457">Kestrel 是 [ASP.NET Core 的跨平台網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="acb26-457">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="acb26-458">Kestrel 是 ASP.NET Core 專案範本中預設隨附的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="acb26-458">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="acb26-459">Kestrel 支援下列案例：</span><span class="sxs-lookup"><span data-stu-id="acb26-459">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="acb26-460">HTTPS</span><span class="sxs-lookup"><span data-stu-id="acb26-460">HTTPS</span></span>
* <span data-ttu-id="acb26-461">用來啟用 [WebSockets](https://github.com/aspnet/websockets) 的不透明升級</span><span class="sxs-lookup"><span data-stu-id="acb26-461">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="acb26-462">Nginx 背後的高效能 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-462">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="acb26-463">HTTP/2 (macOS 上除外&dagger;)</span><span class="sxs-lookup"><span data-stu-id="acb26-463">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="acb26-464">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-464">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="acb26-465">.NET Core 支援的所有平台和版本都支援 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-465">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="acb26-466">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="acb26-466">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="acb26-467">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="acb26-467">HTTP/2 support</span></span>

<span data-ttu-id="acb26-468">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式使用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="acb26-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="acb26-469">作業系統&dagger;</span><span class="sxs-lookup"><span data-stu-id="acb26-469">Operating system&dagger;</span></span>
  * <span data-ttu-id="acb26-470">Windows Server 2016/Windows 10 或更新版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="acb26-470">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="acb26-471">Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="acb26-471">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="acb26-472">目標 Framework：.NET Core 2.2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="acb26-472">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="acb26-473">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="acb26-473">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="acb26-474">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="acb26-474">TLS 1.2 or later connection</span></span>

<span data-ttu-id="acb26-475">&dagger;未來版本的 macOS 上將會支援 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-475">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="acb26-476">&Dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。</span><span class="sxs-lookup"><span data-stu-id="acb26-476">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="acb26-477">支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。</span><span class="sxs-lookup"><span data-stu-id="acb26-477">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="acb26-478">可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。</span><span class="sxs-lookup"><span data-stu-id="acb26-478">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="acb26-479">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="acb26-479">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="acb26-480">預設會停用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-480">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="acb26-481">如需組態的詳細資訊，請參閱 [Kestrel 選項](#kestrel-options)和 [ListenOptions. 通訊協定](#listenoptionsprotocols)一節。</span><span class="sxs-lookup"><span data-stu-id="acb26-481">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="acb26-482">何時搭配使用 Kestrel 與反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="acb26-482">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="acb26-483">您可以單獨使用 Kestrel，或與 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 等「反向 Proxy 伺服器」\*\* 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-483">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="acb26-484">反向 Proxy 伺服器會從網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-484">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="acb26-485">Kestrel 用作邊緣 (網際網路對應) 網頁伺服器：</span><span class="sxs-lookup"><span data-stu-id="acb26-485">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="acb26-487">Kestrel 用於反向 Proxy 組態中：</span><span class="sxs-lookup"><span data-stu-id="acb26-487">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="acb26-489">不論是否有反向 proxy 伺服器，設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-489">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="acb26-490">Kestrel 用作不需要反向 Proxy 伺服器的 Edge Server 時，不支援在多個處理序之間共用相同的 IP 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-490">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="acb26-491">當 Kestrel 設定為接聽連接埠時，Kestrel 會處理該連接埠的所有流量，而不論要求的 `Host` 標頭為何。</span><span class="sxs-lookup"><span data-stu-id="acb26-491">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="acb26-492">可以共用連接埠的反向 Proxy 能夠在唯一的 IP 和連接埠上轉送要求給 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-492">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="acb26-493">即使不需要反向 Proxy 伺服器，使用反向 Proxy 伺服器也是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="acb26-493">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="acb26-494">反向 Proxy：</span><span class="sxs-lookup"><span data-stu-id="acb26-494">A reverse proxy:</span></span>

* <span data-ttu-id="acb26-495">可以限制它所主控之應用程式的公開介面區。</span><span class="sxs-lookup"><span data-stu-id="acb26-495">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="acb26-496">提供額外的組態和防禦層。</span><span class="sxs-lookup"><span data-stu-id="acb26-496">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="acb26-497">能夠與現有基礎結構更好地整合。</span><span class="sxs-lookup"><span data-stu-id="acb26-497">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="acb26-498">簡化負載平衡和安全通訊 (HTTPS) 組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-498">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="acb26-499">只有反向 proxy 伺服器需要 x.509 憑證，而且該伺服器可以使用一般 HTTP 與內部網路上的應用程式伺服器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="acb26-499">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="acb26-500">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acb26-500">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="acb26-501">如何在 ASP.NET Core 應用程式中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="acb26-501">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="acb26-502">AspNetCore 會包含在[AspNetCore.. 中繼套件](xref:fundamentals/metapackage-app). [Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)套件中。</span><span class="sxs-lookup"><span data-stu-id="acb26-502">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="acb26-503">ASP.NET Core 專案範本預設會使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-503">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="acb26-504">在 *Program.cs* 中，範本程式碼會呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，而後者會在幕後呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="acb26-504">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="acb26-505">如需 `CreateDefaultBuilder` 和建立主機的詳細資訊，請參閱的 *設定主機* 一節 <xref:fundamentals/host/web-host#set-up-a-host> 。</span><span class="sxs-lookup"><span data-stu-id="acb26-505">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="acb26-506">若要在呼叫 `CreateDefaultBuilder` 之後提供額外的設定，請使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="acb26-506">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="acb26-507">如果應用程式未呼叫 `CreateDefaultBuilder` 來設定主機，請在呼叫 `ConfigureKestrel` **之前**，先呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="acb26-507">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="acb26-508">Kestrel 選項</span><span class="sxs-lookup"><span data-stu-id="acb26-508">Kestrel options</span></span>

<span data-ttu-id="acb26-509">Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。</span><span class="sxs-lookup"><span data-stu-id="acb26-509">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="acb26-510">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。</span><span class="sxs-lookup"><span data-stu-id="acb26-510">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="acb26-511">`Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="acb26-511">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="acb26-512">下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；</span><span class="sxs-lookup"><span data-stu-id="acb26-512">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="acb26-513">您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項（在下列範例中以 c # 程式碼設定）。</span><span class="sxs-lookup"><span data-stu-id="acb26-513">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="acb26-514">例如，檔案設定提供者可以從 *appsettings.json* 或 Appsettings 載入 Kestrel 設定 *。 {環境}. json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="acb26-514">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="acb26-515">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="acb26-515">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="acb26-516">設定 Kestrel in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="acb26-516">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="acb26-517">將的實例插入 `IConfiguration` 至 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="acb26-517">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="acb26-518">下列範例假設將插入的設定指派給 `Configuration` 屬性。</span><span class="sxs-lookup"><span data-stu-id="acb26-518">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="acb26-519">在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-519">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="acb26-520">設定 Kestrel 建立主機時：</span><span class="sxs-lookup"><span data-stu-id="acb26-520">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="acb26-521">在 *Program.cs*中，將 `Kestrel` Configuration 區段載入 Kestrel 的設定中：</span><span class="sxs-lookup"><span data-stu-id="acb26-521">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="acb26-522">上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-522">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="acb26-523">Keep-alive 逾時</span><span class="sxs-lookup"><span data-stu-id="acb26-523">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="acb26-524">取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="acb26-524">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="acb26-525">預設為 2 分鐘。</span><span class="sxs-lookup"><span data-stu-id="acb26-525">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="acb26-526">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="acb26-526">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="acb26-527">可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：</span><span class="sxs-lookup"><span data-stu-id="acb26-527">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="acb26-528">已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-528">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="acb26-529">升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-529">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="acb26-530">連線數目上限預設為無限制 (null)。</span><span class="sxs-lookup"><span data-stu-id="acb26-530">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="acb26-531">要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="acb26-531">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="acb26-532">預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="acb26-532">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="acb26-533">若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：</span><span class="sxs-lookup"><span data-stu-id="acb26-533">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acb26-534">以下範例會示範如何設定應用程式、每個要求的條件約束：</span><span class="sxs-lookup"><span data-stu-id="acb26-534">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="acb26-535">覆寫中介軟體中特定要求的設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-535">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="acb26-536">如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="acb26-536">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="acb26-537">有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="acb26-537">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acb26-538">當應用程式是在 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)後方於[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)執行時，Kestrel 的要求本文大小限制將會被停用，因為 IIS 已經設定限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-538">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="acb26-539">要求主體資料速率下限</span><span class="sxs-lookup"><span data-stu-id="acb26-539">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="acb26-540">如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。</span><span class="sxs-lookup"><span data-stu-id="acb26-540">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="acb26-541">如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 讓用戶端提高其傳送速率的時間量（最小值）;在這段時間內不會檢查速率。</span><span class="sxs-lookup"><span data-stu-id="acb26-541">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="acb26-542">寬限期可協助避免中斷連線，這是由於 TCP 緩慢啟動而一開始以低速傳送資料所造成。</span><span class="sxs-lookup"><span data-stu-id="acb26-542">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="acb26-543">預設速率下限為 240 個位元組/秒，寬限期為 5 秒。</span><span class="sxs-lookup"><span data-stu-id="acb26-543">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="acb26-544">速率下限也適用於回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-544">A minimum rate also applies to the response.</span></span> <span data-ttu-id="acb26-545">除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。</span><span class="sxs-lookup"><span data-stu-id="acb26-545">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="acb26-546">以下範例示範如何在 *Program.cs* 中設定資料速率下限：</span><span class="sxs-lookup"><span data-stu-id="acb26-546">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="acb26-547">覆寫中介軟體中每個要求的最小速率限制：</span><span class="sxs-lookup"><span data-stu-id="acb26-547">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="acb26-548">因為通訊協定對要求多工的支援，所以 HTTP/2 不支援以每一要求基礎修改速率限制，進而使先前範例中所參考的所有速率功能都不會出現在 HTTP/2 要求的 `HttpContext.Features` 中。</span><span class="sxs-lookup"><span data-stu-id="acb26-548">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="acb26-549">透過 `KestrelServerOptions.Limits` 設定的全伺服器速率限制皆仍套用至 HTTP/1.x 及 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="acb26-549">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="acb26-550">要求標頭逾時</span><span class="sxs-lookup"><span data-stu-id="acb26-550">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="acb26-551">取得或設定伺服器花費在接收要求標頭的時間上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-551">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="acb26-552">預設為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="acb26-552">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="acb26-553">每個連線的資料流數目上限</span><span class="sxs-lookup"><span data-stu-id="acb26-553">Maximum streams per connection</span></span>

<span data-ttu-id="acb26-554">`Http2.MaxStreamsPerConnection` 會限制每個 HTTP/2 連線的同時要求資料流數目。</span><span class="sxs-lookup"><span data-stu-id="acb26-554">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="acb26-555">超出的資料流會被拒絕。</span><span class="sxs-lookup"><span data-stu-id="acb26-555">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="acb26-556">預設值是 100。</span><span class="sxs-lookup"><span data-stu-id="acb26-556">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="acb26-557">標頭表格大小</span><span class="sxs-lookup"><span data-stu-id="acb26-557">Header table size</span></span>

<span data-ttu-id="acb26-558">HPACK 解碼器可解壓縮 HTTP/2 連線的 HTTP 標頭。</span><span class="sxs-lookup"><span data-stu-id="acb26-558">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="acb26-559">`Http2.HeaderTableSize` 會限制 HPACK 解碼器所使用的標頭壓縮表格大小。</span><span class="sxs-lookup"><span data-stu-id="acb26-559">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="acb26-560">這個值是以八位元提供，而且必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="acb26-560">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="acb26-561">預設值為 4096。</span><span class="sxs-lookup"><span data-stu-id="acb26-561">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="acb26-562">框架大小上限</span><span class="sxs-lookup"><span data-stu-id="acb26-562">Maximum frame size</span></span>

<span data-ttu-id="acb26-563">`Http2.MaxFrameSize` 表示要接收的 HTTP/2 連線框架承載大小上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-563">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="acb26-564">這個值是以八位元提供，而且必須介於 2^14 (16,384) 到 2^24-1 (16,777,215) 之間。</span><span class="sxs-lookup"><span data-stu-id="acb26-564">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="acb26-565">預設值為 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="acb26-565">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="acb26-566">要求標頭大小上限</span><span class="sxs-lookup"><span data-stu-id="acb26-566">Maximum request header size</span></span>

<span data-ttu-id="acb26-567">`Http2.MaxRequestHeaderFieldSize` 以八位元表示要求標頭值的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-567">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="acb26-568">此限制皆共同套用至已壓縮及未壓縮代表中的名稱與值。</span><span class="sxs-lookup"><span data-stu-id="acb26-568">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="acb26-569">此值必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="acb26-569">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="acb26-570">預設值為 8,192。</span><span class="sxs-lookup"><span data-stu-id="acb26-570">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="acb26-571">初始連線視窗大小</span><span class="sxs-lookup"><span data-stu-id="acb26-571">Initial connection window size</span></span>

<span data-ttu-id="acb26-572">`Http2.InitialConnectionWindowSize` 會以位元組表示伺服器緩衝每個連線之所有要求 (資料流) 單次彙總的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-572">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="acb26-573">要求也皆受 `Http2.InitialStreamWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-573">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="acb26-574">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="acb26-574">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="acb26-575">預設值為 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="acb26-575">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="acb26-576">初始資料流視窗大小</span><span class="sxs-lookup"><span data-stu-id="acb26-576">Initial stream window size</span></span>

<span data-ttu-id="acb26-577">`Http2.InitialStreamWindowSize` 會以位元組表示每個要求 (資料流) 單次伺服器緩衝的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-577">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="acb26-578">要求也皆受 `Http2.InitialStreamWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-578">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="acb26-579">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="acb26-579">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="acb26-580">預設值為 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="acb26-580">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="acb26-581">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="acb26-581">Synchronous I/O</span></span>

<span data-ttu-id="acb26-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="acb26-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="acb26-583">預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="acb26-583">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="acb26-584">大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-584">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="acb26-585">只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。</span><span class="sxs-lookup"><span data-stu-id="acb26-585">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="acb26-586">下列範例會啟用同步 i/o：</span><span class="sxs-lookup"><span data-stu-id="acb26-586">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="acb26-587">如需其他 Kestrel 選項和限制的資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="acb26-587">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="acb26-588">端點組態</span><span class="sxs-lookup"><span data-stu-id="acb26-588">Endpoint configuration</span></span>

<span data-ttu-id="acb26-589">ASP.NET Core 預設會繫結至：</span><span class="sxs-lookup"><span data-stu-id="acb26-589">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="acb26-590">`https://localhost:5001` (當有本機開發憑證存在時)</span><span class="sxs-lookup"><span data-stu-id="acb26-590">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="acb26-591">使用以下各項指定 URL：</span><span class="sxs-lookup"><span data-stu-id="acb26-591">Specify URLs using the:</span></span>

* <span data-ttu-id="acb26-592">`ASPNETCORE_URLS` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="acb26-592">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="acb26-593">`--urls` 命令列引數。</span><span class="sxs-lookup"><span data-stu-id="acb26-593">`--urls` command-line argument.</span></span>
* <span data-ttu-id="acb26-594">`urls` 主機組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="acb26-594">`urls` host configuration key.</span></span>
* <span data-ttu-id="acb26-595">`UseUrls` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="acb26-595">`UseUrls` extension method.</span></span>

<span data-ttu-id="acb26-596">使用這些方法提供的值可以是一或多個 HTTP 和 HTTPS 端點 (如果有預設憑證可用則為 HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="acb26-596">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="acb26-597">將值設定為以分號分隔的清單 (例如，`"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="acb26-597">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="acb26-598">如需有關這些方法的詳細資訊，請參閱[伺服器 URL](xref:fundamentals/host/web-host#server-urls) 和[覆寫設定](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="acb26-598">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="acb26-599">開發憑證會建立於：</span><span class="sxs-lookup"><span data-stu-id="acb26-599">A development certificate is created:</span></span>

* <span data-ttu-id="acb26-600">已安裝 [.NET Core SDK](/dotnet/core/sdk) 時。</span><span class="sxs-lookup"><span data-stu-id="acb26-600">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="acb26-601">[dev-certs 工具](xref:aspnetcore-2.1#https)用來建立憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-601">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="acb26-602">某些瀏覽器需要授與明確的許可權，以信任本機開發憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-602">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="acb26-603">專案範本預設會將應用程式設定為在 HTTPS 上執行，並包含 Https 重新導向 [和 HSTS 支援](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="acb26-603">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="acb26-604">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上呼叫 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法，來為 Kestrel 設定 URL 首碼和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-604">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="acb26-605">`UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵和 `ASPNETCORE_URLS` 環境變數同樣有效，但卻有本節稍後註明的限制 (針對 HTTPS 端點組態必須有預設憑證可用)。</span><span class="sxs-lookup"><span data-stu-id="acb26-605">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="acb26-606">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="acb26-606">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="acb26-607">ConfigureEndpointDefaults (動作 \<ListenOptions>) </span><span class="sxs-lookup"><span data-stu-id="acb26-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="acb26-608">指定組態 `Action` 以針對每個指定端點執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-608">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="acb26-609">呼叫 `ConfigureEndpointDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-609">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acb26-610">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-610">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="acb26-611">ConfigureHttpsDefaults (動作 \<HttpsConnectionAdapterOptions>) </span><span class="sxs-lookup"><span data-stu-id="acb26-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="acb26-612">指定組態 `Action` 以針對每個 HTTPS 端點執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-612">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="acb26-613">呼叫 `ConfigureHttpsDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-613">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acb26-614">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="acb26-615">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="acb26-615">Configure(IConfiguration)</span></span>

<span data-ttu-id="acb26-616">建立設定載入器來設定以 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作為輸入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-616">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="acb26-617">組態的範圍必須限於 Kestrel 的組態區段。</span><span class="sxs-lookup"><span data-stu-id="acb26-617">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="acb26-618">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="acb26-618">ListenOptions.UseHttps</span></span>

<span data-ttu-id="acb26-619">設定 Kestrel 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-619">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="acb26-620">`ListenOptions.UseHttps` 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="acb26-620">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="acb26-621">`UseHttps`：設定 Kestrel 以搭配預設憑證使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-621">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="acb26-622">如果未設定預設憑證，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="acb26-622">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="acb26-623">`ListenOptions.UseHttps` 參數：</span><span class="sxs-lookup"><span data-stu-id="acb26-623">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="acb26-624">`filename` 是憑證檔案的路徑和檔案名稱，它相對於包含應用程式內容檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="acb26-624">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="acb26-625">`password` 是存取 X.509 憑證資料所需的密碼。</span><span class="sxs-lookup"><span data-stu-id="acb26-625">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="acb26-626">`configureOptions` 是設定 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-626">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="acb26-627">傳回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="acb26-627">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="acb26-628">`storeName` 是要從中載入憑證的憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="acb26-628">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="acb26-629">`subject` 是憑證的主體名稱。</span><span class="sxs-lookup"><span data-stu-id="acb26-629">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="acb26-630">`allowInvalid` 表示是否應該考慮無效的憑證，例如自我簽署憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-630">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="acb26-631">`location` 是要從中載入憑證的存放區位置。</span><span class="sxs-lookup"><span data-stu-id="acb26-631">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="acb26-632">`serverCertificate` 是 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-632">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="acb26-633">在生產環境中，必須明確設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-633">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="acb26-634">至少必須提供預設憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-634">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="acb26-635">支援的組態描述如下：</span><span class="sxs-lookup"><span data-stu-id="acb26-635">Supported configurations described next:</span></span>

* <span data-ttu-id="acb26-636">無組態</span><span class="sxs-lookup"><span data-stu-id="acb26-636">No configuration</span></span>
* <span data-ttu-id="acb26-637">從組態取代預設憑證</span><span class="sxs-lookup"><span data-stu-id="acb26-637">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="acb26-638">變更程式碼中的預設值</span><span class="sxs-lookup"><span data-stu-id="acb26-638">Change the defaults in code</span></span>

<span data-ttu-id="acb26-639">*無組態*</span><span class="sxs-lookup"><span data-stu-id="acb26-639">*No configuration*</span></span>

<span data-ttu-id="acb26-640">Kestrel 會接聽 `http://localhost:5000` 和 `https://localhost:5001` (如果預設憑證可用的話)。</span><span class="sxs-lookup"><span data-stu-id="acb26-640">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="acb26-641">*從組態取代預設憑證*</span><span class="sxs-lookup"><span data-stu-id="acb26-641">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="acb26-642">`CreateDefaultBuilder` 預設會呼叫 `Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-642">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="acb26-643">Kestrel 可以使用預設的 HTTPS 應用程式設定組態結構描述。</span><span class="sxs-lookup"><span data-stu-id="acb26-643">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="acb26-644">設定多個端點，包括 URL 和要使用的憑證－從磁碟上的檔案，或是從憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="acb26-644">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="acb26-645">在下列 *appsettings.json* 範例中：</span><span class="sxs-lookup"><span data-stu-id="acb26-645">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="acb26-646">將 **AllowInvalid** 設定為 `true`，允許使用無效的憑證 (例如，自我簽署憑證)。</span><span class="sxs-lookup"><span data-stu-id="acb26-646">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="acb26-647">任何未指定憑證 (接下來範例中的 **HttpsDefaultCert**) 的 HTTPS 端點會回復為 [憑證]**[預設]** > \*\*\*\* 下定義的憑證或開發憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-647">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="acb26-648">除了針對任何憑證節點使用 [路徑]\*\*\*\* 和 [密碼]\*\*\*\*，還可以使用憑證存放區欄位指定憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-648">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="acb26-649">例如，您可以將**憑證**  >  **預設**憑證指定為：</span><span class="sxs-lookup"><span data-stu-id="acb26-649">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="acb26-650">結構描述附註：</span><span class="sxs-lookup"><span data-stu-id="acb26-650">Schema notes:</span></span>

* <span data-ttu-id="acb26-651">端點名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="acb26-651">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="acb26-652">例如，`HTTPS` 和 `Https` 都有效。</span><span class="sxs-lookup"><span data-stu-id="acb26-652">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="acb26-653">`Url` 參數對每個端點而言都是必要的。</span><span class="sxs-lookup"><span data-stu-id="acb26-653">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="acb26-654">此參數的格式等同於最上層 `Urls` 組態參數，但是它限制為單一值。</span><span class="sxs-lookup"><span data-stu-id="acb26-654">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="acb26-655">這些端點會取代最上層 `Urls` 組態中定義的端點，而不是新增至其中。</span><span class="sxs-lookup"><span data-stu-id="acb26-655">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="acb26-656">透過 `Listen` 在程式碼中定義的端點，會與組態區段中定義的端點累計。</span><span class="sxs-lookup"><span data-stu-id="acb26-656">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="acb26-657">`Certificate` 區段是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="acb26-657">The `Certificate` section is optional.</span></span> <span data-ttu-id="acb26-658">如果未指定 `Certificate` 區段，則會使用先前案例中所定義的預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-658">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="acb26-659">如果沒有預設值可供使用，伺服器就會擲回例外狀況，且無法啟動。</span><span class="sxs-lookup"><span data-stu-id="acb26-659">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="acb26-660">`Certificate`區段同時支援**路徑** &ndash; **密碼**和**主體** &ndash; **存放區**憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-660">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="acb26-661">可以用這種方式定義任何數目的端點，只要它們不會導致連接埠衝突即可。</span><span class="sxs-lookup"><span data-stu-id="acb26-661">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="acb26-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 會傳回 `KestrelConfigurationLoader` 與 `.Endpoint(string name, listenOptions => { })` 方法，此方法可用來補充已設定的端點設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="acb26-663">`KestrelServerOptions.ConfigurationLoader` 可以直接存取，以繼續逐一查看現有的載入器，例如所提供的載入器 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 。</span><span class="sxs-lookup"><span data-stu-id="acb26-663">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="acb26-664">您可以在方法的選項中使用每個端點的設定區段， `Endpoint` 以便讀取自訂設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-664">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="acb26-665">可以藉由使用另一個區段再次呼叫 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 而載入多個組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-665">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="acb26-666">只會使用最後一個組態，除非在先前的執行個體上已明確呼叫 `Load`。</span><span class="sxs-lookup"><span data-stu-id="acb26-666">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="acb26-667">中繼套件不會呼叫 `Load`，如此可能會取代其預設組態區段。</span><span class="sxs-lookup"><span data-stu-id="acb26-667">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="acb26-668">`KestrelConfigurationLoader` 會將來自 `KestrelServerOptions` 的 API 的 `Listen` 系列鏡像為 `Endpoint` 多載，所以可在相同的位置設定程式碼和設定端點。</span><span class="sxs-lookup"><span data-stu-id="acb26-668">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="acb26-669">這些多載不使用名稱，並且只使用來自組態的預設組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-669">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="acb26-670">*變更程式碼中的預設值*</span><span class="sxs-lookup"><span data-stu-id="acb26-670">*Change the defaults in code*</span></span>

<span data-ttu-id="acb26-671">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 可以用來變更 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的預設設定，包括覆寫先前案例中指定的預設憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-671">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="acb26-672">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 應該在設定任何端點之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="acb26-672">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="acb26-673">*SNI 的 Kestrel 支援*</span><span class="sxs-lookup"><span data-stu-id="acb26-673">*Kestrel support for SNI*</span></span>

<span data-ttu-id="acb26-674">[伺服器名稱指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可以用於在相同的 IP 位址和連接埠上裝載多個網域。</span><span class="sxs-lookup"><span data-stu-id="acb26-674">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="acb26-675">SNI 若要運作，用戶端會在 TLS 信號交換期間傳送安全工作階段的主機名稱給伺服器，讓伺服器可以提供正確的憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-675">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="acb26-676">用戶端在 TLS 信號交換之後的安全工作階段期間，會使用所提供的憑證與伺服器進行加密通訊。</span><span class="sxs-lookup"><span data-stu-id="acb26-676">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="acb26-677">Kestrel 透過 `ServerCertificateSelector` 回呼來支援 SNI。</span><span class="sxs-lookup"><span data-stu-id="acb26-677">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="acb26-678">回呼會針對每個連線叫用一次，允許應用程式檢查主機名稱並選取適當的憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-678">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="acb26-679">SNI 支援需要：</span><span class="sxs-lookup"><span data-stu-id="acb26-679">SNI support requires:</span></span>

* <span data-ttu-id="acb26-680">在目標 framework `netcoreapp2.1` 或更新版本上執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-680">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="acb26-681">在 `net461` 或更新版本中，會叫用回呼，但 `name` 一律為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-681">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="acb26-682">如果用戶端不在 TLS 信號交換中提供主機名稱參數，則 `name` 也是 `null`。</span><span class="sxs-lookup"><span data-stu-id="acb26-682">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="acb26-683">所有網站都在相同的 Kestrel 執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-683">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="acb26-684">在不使用反向 Proxy 的情況下，Kestrel 不支援跨多個執行個體共用 IP 位址和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-684">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="acb26-685">連接記錄</span><span class="sxs-lookup"><span data-stu-id="acb26-685">Connection logging</span></span>

<span data-ttu-id="acb26-686">呼叫 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以發出連接上位元組層級通訊的 Debug 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="acb26-686">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="acb26-687">連線記錄有助於疑難排解低層級通訊中的問題，例如在 TLS 加密期間和 proxy 後方。</span><span class="sxs-lookup"><span data-stu-id="acb26-687">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="acb26-688">如果 `UseConnectionLogging` 之前放置 `UseHttps` ，則會記錄加密的流量。</span><span class="sxs-lookup"><span data-stu-id="acb26-688">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="acb26-689">如果 `UseConnectionLogging` 放置在之後 `UseHttps` ，則會記錄解密的流量。</span><span class="sxs-lookup"><span data-stu-id="acb26-689">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="acb26-690">繫結至 TCP 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-690">Bind to a TCP socket</span></span>

<span data-ttu-id="acb26-691"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法會繫結至 TCP 通訊端，而選項 Lambda 則會允許 X.509 憑證設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-691">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="acb26-692">此範例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>來為端點設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-692">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="acb26-693">若要設定特定端點的其他 Kestrel 設定，請使用相同的 API。</span><span class="sxs-lookup"><span data-stu-id="acb26-693">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="acb26-694">繫結至 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-694">Bind to a Unix socket</span></span>

<span data-ttu-id="acb26-695">請使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 在 Unix 通訊端上進行接聽以改善 Nginx 的效能，如此範例所示：</span><span class="sxs-lookup"><span data-stu-id="acb26-695">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="acb26-696">在 Nginx confiuguration 檔案中，將 `server`  >  `location`  >  `proxy_pass` 專案設定為 `http://unix:/tmp/{KESTREL SOCKET}:/;` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-696">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="acb26-697">`{KESTREL SOCKET}` 這是提供給 (的通訊端名稱 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ，例如 `kestrel-test.sock` 上述範例) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-697">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="acb26-698">確定 Nginx (可寫入通訊端，例如 `chmod go+w /tmp/kestrel-test.sock`) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-698">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="acb26-699">連接埠 0</span><span class="sxs-lookup"><span data-stu-id="acb26-699">Port 0</span></span>

<span data-ttu-id="acb26-700">指定連接埠號碼 `0` 時，Kestrel 會動態繫結至可用的連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-700">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="acb26-701">下列範例示範如何判斷 Kestrel 在執行階段實際上繫結至哪一個連接埠：</span><span class="sxs-lookup"><span data-stu-id="acb26-701">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="acb26-702">當應用程式執行時，主控台視窗輸出會指出可以連線到應用程式的動態連接埠：</span><span class="sxs-lookup"><span data-stu-id="acb26-702">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="acb26-703">限制</span><span class="sxs-lookup"><span data-stu-id="acb26-703">Limitations</span></span>

<span data-ttu-id="acb26-704">使用下列方法來設定端點：</span><span class="sxs-lookup"><span data-stu-id="acb26-704">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="acb26-705">`--urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="acb26-705">`--urls` command-line argument</span></span>
* <span data-ttu-id="acb26-706">`urls` 主機組態索引鍵</span><span class="sxs-lookup"><span data-stu-id="acb26-706">`urls` host configuration key</span></span>
* <span data-ttu-id="acb26-707">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="acb26-707">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="acb26-708">要讓程式碼使用 Kestrel 以外的伺服器，這些方法會很有用。</span><span class="sxs-lookup"><span data-stu-id="acb26-708">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="acb26-709">不過，請注意下列限制：</span><span class="sxs-lookup"><span data-stu-id="acb26-709">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="acb26-710">HTTPS 無法與這些方法搭配使用，除非在 HTTPS 端點設定中提供預設憑證 (例如，使用 `KestrelServerOptions` 設定或設定檔，如本主題稍早所示)。</span><span class="sxs-lookup"><span data-stu-id="acb26-710">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="acb26-711">當同時使用 `Listen` 和 `UseUrls` 方法時，`Listen` 端點會覆寫 `UseUrls` 端點。</span><span class="sxs-lookup"><span data-stu-id="acb26-711">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="acb26-712">IIS 端點設定</span><span class="sxs-lookup"><span data-stu-id="acb26-712">IIS endpoint configuration</span></span>

<span data-ttu-id="acb26-713">使用 IIS 時，IIS 覆寫繫結的 URL 繫結是由 `Listen` 或 `UseUrls` 設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-713">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="acb26-714">如需詳細資訊，請參閱 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)主題。</span><span class="sxs-lookup"><span data-stu-id="acb26-714">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="acb26-715">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="acb26-715">ListenOptions.Protocols</span></span>

<span data-ttu-id="acb26-716">`Protocols` 屬性會建立在連線端點上或針對伺服器啟用的 HTTP 通訊協定 (`HttpProtocols`)。</span><span class="sxs-lookup"><span data-stu-id="acb26-716">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="acb26-717">從 `HttpProtocols` 列舉中指派一個值給 `Protocols` 屬性。</span><span class="sxs-lookup"><span data-stu-id="acb26-717">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="acb26-718">`HttpProtocols` 列舉值</span><span class="sxs-lookup"><span data-stu-id="acb26-718">`HttpProtocols` enum value</span></span> | <span data-ttu-id="acb26-719">允許的連線通訊協定</span><span class="sxs-lookup"><span data-stu-id="acb26-719">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="acb26-720">僅限 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acb26-720">HTTP/1.1 only.</span></span> <span data-ttu-id="acb26-721">可在具有或沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-721">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="acb26-722">僅限 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-722">HTTP/2 only.</span></span> <span data-ttu-id="acb26-723">只有在用戶端支援[先備知識模式](https://tools.ietf.org/html/rfc7540#section-3.4)時，才可以在沒有 TLS 的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-723">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="acb26-724">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acb26-724">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="acb26-725">HTTP/2 需要 TLS 和 [應用層通訊協定協商 (ALPN) ](https://tools.ietf.org/html/rfc7301#section-3) 連接;否則，連接預設為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acb26-725">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="acb26-726">預設通訊協定為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acb26-726">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="acb26-727">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="acb26-727">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="acb26-728">TLS 1.2 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="acb26-728">TLS version 1.2 or later</span></span>
* <span data-ttu-id="acb26-729">已停用重新交涉</span><span class="sxs-lookup"><span data-stu-id="acb26-729">Renegotiation disabled</span></span>
* <span data-ttu-id="acb26-730">已停用壓縮</span><span class="sxs-lookup"><span data-stu-id="acb26-730">Compression disabled</span></span>
* <span data-ttu-id="acb26-731">暫時金鑰交換大小下限：</span><span class="sxs-lookup"><span data-stu-id="acb26-731">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="acb26-732">橢圓曲線 Diffie-Hellman (>ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; ：最少224位</span><span class="sxs-lookup"><span data-stu-id="acb26-732">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="acb26-733">有限的欄位 Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; ：最低2048位</span><span class="sxs-lookup"><span data-stu-id="acb26-733">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="acb26-734">未封鎖加密套件</span><span class="sxs-lookup"><span data-stu-id="acb26-734">Cipher suite not blocked</span></span>

<span data-ttu-id="acb26-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack;預設支援使用 P-256 橢圓曲線 &lbrack; `FIPS186` &rbrack; 。</span><span class="sxs-lookup"><span data-stu-id="acb26-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="acb26-736">下列範例會允許連接埠 8000 上的 HTTP/1.1 和 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="acb26-736">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="acb26-737">這些連線使用提供的憑證受到 TLS 保護：</span><span class="sxs-lookup"><span data-stu-id="acb26-737">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="acb26-738">選擇性地建立 `IConnectionAdapter` 實作，針對每個連線來篩選特定加密的 TLS 交握：</span><span class="sxs-lookup"><span data-stu-id="acb26-738">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="acb26-739">從設定進行通訊協定設定\*\*</span><span class="sxs-lookup"><span data-stu-id="acb26-739">*Set the protocol from configuration*</span></span>

<span data-ttu-id="acb26-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 預設會呼叫 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="acb26-741">在下列 *appsettings.json* 範例中，會為 Kestrel 的所有端點建立預設連線通訊協定 (HTTP/1.1 和 HTTP/2)：</span><span class="sxs-lookup"><span data-stu-id="acb26-741">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="acb26-742">下列設定檔範例會為特定端點建立連線通訊協定：</span><span class="sxs-lookup"><span data-stu-id="acb26-742">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="acb26-743">程式碼中指定的通訊協定會覆寫設定所設定的值。</span><span class="sxs-lookup"><span data-stu-id="acb26-743">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="acb26-744">傳輸組態</span><span class="sxs-lookup"><span data-stu-id="acb26-744">Transport configuration</span></span>

<span data-ttu-id="acb26-745">隨著 ASP.NET Core 2.1 的發行，Kestrel 的預設傳輸不再根據 Libuv，而是改為根據受控通訊端。</span><span class="sxs-lookup"><span data-stu-id="acb26-745">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="acb26-746">對於升級到 2.1 且會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 並相依於下列任一套件的 ASP.NET Core 2.0 應用程式來說，這是一項中斷性變更：</span><span class="sxs-lookup"><span data-stu-id="acb26-746">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="acb26-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (直接套件參考)</span><span class="sxs-lookup"><span data-stu-id="acb26-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="acb26-748">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="acb26-748">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="acb26-749">針對需要使用 Libuv 的專案：</span><span class="sxs-lookup"><span data-stu-id="acb26-749">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="acb26-750">將 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 套件的相依性新增至應用程式的專案檔中：</span><span class="sxs-lookup"><span data-stu-id="acb26-750">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="acb26-751">呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="acb26-751">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="acb26-752">URL 前置詞</span><span class="sxs-lookup"><span data-stu-id="acb26-752">URL prefixes</span></span>

<span data-ttu-id="acb26-753">使用 `UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵或 `ASPNETCORE_URLS` 環境變數時，URL 前置詞可以採用下列任一格式。</span><span class="sxs-lookup"><span data-stu-id="acb26-753">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="acb26-754">只有 HTTP URL 前置詞有效。</span><span class="sxs-lookup"><span data-stu-id="acb26-754">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="acb26-755">使用 `UseUrls` 來設定 URL 繫結時，Kestrel 不支援 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-755">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="acb26-756">IPv4 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-756">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="acb26-757">`0.0.0.0` 是繫結至所有 IPv4 位址的特殊情況。</span><span class="sxs-lookup"><span data-stu-id="acb26-757">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="acb26-758">IPv6 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-758">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="acb26-759">`[::]` 是相當於 IPv4 `0.0.0.0` 的 IPv6 對等項目。</span><span class="sxs-lookup"><span data-stu-id="acb26-759">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="acb26-760">主機名稱與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-760">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="acb26-761">主機名稱 `*` 和 `+` 並不特殊。</span><span class="sxs-lookup"><span data-stu-id="acb26-761">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="acb26-762">無法辨識為有效 IP 位址或 `localhost` 的任何項目，都會繫結至所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="acb26-762">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="acb26-763">若要在相同連接埠上將不同的主機名稱繫結至不同的 ASP.NET Core 應用程式，請使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向 Proxy 伺服器 (例如 IIS、Nginx 或 Apache)。</span><span class="sxs-lookup"><span data-stu-id="acb26-763">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="acb26-764">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acb26-764">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="acb26-765">主機 `localhost` 名稱與連接埠號碼，或回送 IP 與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-765">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="acb26-766">如果指定 `localhost`，Kestrel 會嘗試同時繫結至 IPv4 和 IPv6 回送介面。</span><span class="sxs-lookup"><span data-stu-id="acb26-766">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="acb26-767">如果所要求的連接埠在任一個回送介面上由另一個服務使用，則 Kestrel 無法啟動。</span><span class="sxs-lookup"><span data-stu-id="acb26-767">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="acb26-768">如果任一回送介面由於任何其他原因 (最常見的原因是不支援 IPv6) 無法使用，Kestrel 就會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="acb26-768">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="acb26-769">主機篩選</span><span class="sxs-lookup"><span data-stu-id="acb26-769">Host filtering</span></span>

<span data-ttu-id="acb26-770">雖然 Kestrel 根據前置詞來支援組態，例如 `http://example.com:5000`，Kestrel 大多會忽略主機名稱。</span><span class="sxs-lookup"><span data-stu-id="acb26-770">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="acb26-771">主機 `localhost` 是特殊情況，用來繫結到回送位址。</span><span class="sxs-lookup"><span data-stu-id="acb26-771">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="acb26-772">任何非明確 IP 位址的主機，會繫結至所有公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="acb26-772">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="acb26-773">`Host` 標頭未驗證。</span><span class="sxs-lookup"><span data-stu-id="acb26-773">`Host` headers aren't validated.</span></span>

<span data-ttu-id="acb26-774">因應措施是使用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="acb26-774">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="acb26-775">主機篩選中介軟體是由 [AspNetCore HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 套件所提供，此套件隨附于 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 或 2.2) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-775">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="acb26-776">中介軟體是由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 所新增，它會呼叫 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>：</span><span class="sxs-lookup"><span data-stu-id="acb26-776">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="acb26-777">預設停用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="acb26-777">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="acb26-778">若要啟用中介軟體，請 `AllowedHosts` 在 appsettings*上的appsettings.js*中定義金鑰 / \* \<EnvironmentName> 。json\*。</span><span class="sxs-lookup"><span data-stu-id="acb26-778">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="acb26-779">此值是以分號分隔的主機名稱清單，不含連接埠號碼：</span><span class="sxs-lookup"><span data-stu-id="acb26-779">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="acb26-780">*appsettings.js*：</span><span class="sxs-lookup"><span data-stu-id="acb26-780">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="acb26-781">[轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)也有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 選項。</span><span class="sxs-lookup"><span data-stu-id="acb26-781">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="acb26-782">在不同的案例中，轉送標頭中介軟體和主機篩選中介軟體有類似的功能。</span><span class="sxs-lookup"><span data-stu-id="acb26-782">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="acb26-783">當不保留 `Host` 標頭，卻使用反向 Proxy 伺服器或負載平衡器轉送要求時，可使用轉送標頭中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="acb26-783">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="acb26-784">當使用 Kestrel 作為公眾對應 Edge Server，或直接轉送 `Host` 標頭時，可使用主機篩選中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="acb26-784">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="acb26-785">如需轉送標頭中介軟體的詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="acb26-785">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="acb26-786">Kestrel 是 [ASP.NET Core 的跨平台網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="acb26-786">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="acb26-787">Kestrel 是 ASP.NET Core 專案範本中預設隨附的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="acb26-787">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="acb26-788">Kestrel 支援下列案例：</span><span class="sxs-lookup"><span data-stu-id="acb26-788">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="acb26-789">HTTPS</span><span class="sxs-lookup"><span data-stu-id="acb26-789">HTTPS</span></span>
* <span data-ttu-id="acb26-790">用來啟用 [WebSockets](https://github.com/aspnet/websockets) 的不透明升級</span><span class="sxs-lookup"><span data-stu-id="acb26-790">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="acb26-791">Nginx 背後的高效能 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-791">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="acb26-792">.NET Core 支援的所有平台和版本都支援 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-792">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="acb26-793">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="acb26-793">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="acb26-794">何時搭配使用 Kestrel 與反向 Proxy</span><span class="sxs-lookup"><span data-stu-id="acb26-794">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="acb26-795">您可以單獨使用 Kestrel，或與 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 等「反向 Proxy 伺服器」\*\* 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-795">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="acb26-796">反向 Proxy 伺服器會從網路接收 HTTP 要求，然後轉送到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-796">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="acb26-797">Kestrel 用作邊緣 (網際網路對應) 網頁伺服器：</span><span class="sxs-lookup"><span data-stu-id="acb26-797">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="acb26-799">Kestrel 用於反向 Proxy 組態中：</span><span class="sxs-lookup"><span data-stu-id="acb26-799">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="acb26-801">不論是否有反向 proxy 伺服器，設定都是支援的裝載設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-801">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="acb26-802">Kestrel 用作不需要反向 Proxy 伺服器的 Edge Server 時，不支援在多個處理序之間共用相同的 IP 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-802">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="acb26-803">當 Kestrel 設定為接聽連接埠時，Kestrel 會處理該連接埠的所有流量，而不論要求的 `Host` 標頭為何。</span><span class="sxs-lookup"><span data-stu-id="acb26-803">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="acb26-804">可以共用連接埠的反向 Proxy 能夠在唯一的 IP 和連接埠上轉送要求給 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-804">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="acb26-805">即使不需要反向 Proxy 伺服器，使用反向 Proxy 伺服器也是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="acb26-805">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="acb26-806">反向 Proxy：</span><span class="sxs-lookup"><span data-stu-id="acb26-806">A reverse proxy:</span></span>

* <span data-ttu-id="acb26-807">可以限制它所主控之應用程式的公開介面區。</span><span class="sxs-lookup"><span data-stu-id="acb26-807">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="acb26-808">提供額外的組態和防禦層。</span><span class="sxs-lookup"><span data-stu-id="acb26-808">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="acb26-809">能夠與現有基礎結構更好地整合。</span><span class="sxs-lookup"><span data-stu-id="acb26-809">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="acb26-810">簡化負載平衡和安全通訊 (HTTPS) 組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-810">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="acb26-811">只有反向 proxy 伺服器需要 x.509 憑證，而且該伺服器可以使用一般 HTTP 與內部網路上的應用程式伺服器進行通訊。</span><span class="sxs-lookup"><span data-stu-id="acb26-811">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="acb26-812">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acb26-812">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="acb26-813">如何在 ASP.NET Core 應用程式中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="acb26-813">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="acb26-814">AspNetCore 會包含在[AspNetCore.. 中繼套件](xref:fundamentals/metapackage-app). [Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)套件中。</span><span class="sxs-lookup"><span data-stu-id="acb26-814">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="acb26-815">ASP.NET Core 專案範本預設會使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-815">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="acb26-816">在 *Program.cs* 中，範本程式碼會呼叫 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，而後者會在幕後呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="acb26-816">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="acb26-817">若要在呼叫 `CreateDefaultBuilder` 之後提供額外的設定，請呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="acb26-817">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="acb26-818">如需 `CreateDefaultBuilder` 和建立主機的詳細資訊，請參閱的 *設定主機* 一節 <xref:fundamentals/host/web-host#set-up-a-host> 。</span><span class="sxs-lookup"><span data-stu-id="acb26-818">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="acb26-819">Kestrel 選項</span><span class="sxs-lookup"><span data-stu-id="acb26-819">Kestrel options</span></span>

<span data-ttu-id="acb26-820">Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。</span><span class="sxs-lookup"><span data-stu-id="acb26-820">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="acb26-821">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。</span><span class="sxs-lookup"><span data-stu-id="acb26-821">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="acb26-822">`Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="acb26-822">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="acb26-823">下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；</span><span class="sxs-lookup"><span data-stu-id="acb26-823">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="acb26-824">您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項（在下列範例中以 c # 程式碼設定）。</span><span class="sxs-lookup"><span data-stu-id="acb26-824">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="acb26-825">例如，檔案設定提供者可以從 *appsettings.json* 或 Appsettings 載入 Kestrel 設定 *。 {環境}. json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="acb26-825">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="acb26-826">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="acb26-826">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="acb26-827">設定 Kestrel in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="acb26-827">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="acb26-828">將的實例插入 `IConfiguration` 至 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="acb26-828">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="acb26-829">下列範例假設將插入的設定指派給 `Configuration` 屬性。</span><span class="sxs-lookup"><span data-stu-id="acb26-829">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="acb26-830">在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-830">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="acb26-831">設定 Kestrel 建立主機時：</span><span class="sxs-lookup"><span data-stu-id="acb26-831">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="acb26-832">在 *Program.cs*中，將 `Kestrel` Configuration 區段載入 Kestrel 的設定中：</span><span class="sxs-lookup"><span data-stu-id="acb26-832">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="acb26-833">上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。</span><span class="sxs-lookup"><span data-stu-id="acb26-833">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="acb26-834">Keep-alive 逾時</span><span class="sxs-lookup"><span data-stu-id="acb26-834">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="acb26-835">取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="acb26-835">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="acb26-836">預設為 2 分鐘。</span><span class="sxs-lookup"><span data-stu-id="acb26-836">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="acb26-837">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="acb26-837">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="acb26-838">可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：</span><span class="sxs-lookup"><span data-stu-id="acb26-838">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="acb26-839">已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-839">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="acb26-840">升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-840">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="acb26-841">連線數目上限預設為無限制 (null)。</span><span class="sxs-lookup"><span data-stu-id="acb26-841">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="acb26-842">要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="acb26-842">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="acb26-843">預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="acb26-843">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="acb26-844">若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：</span><span class="sxs-lookup"><span data-stu-id="acb26-844">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acb26-845">以下範例會示範如何設定應用程式、每個要求的條件約束：</span><span class="sxs-lookup"><span data-stu-id="acb26-845">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="acb26-846">覆寫中介軟體中特定要求的設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-846">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="acb26-847">如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="acb26-847">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="acb26-848">有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="acb26-848">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acb26-849">當應用程式是在 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)後方於[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)執行時，Kestrel 的要求本文大小限制將會被停用，因為 IIS 已經設定限制。</span><span class="sxs-lookup"><span data-stu-id="acb26-849">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="acb26-850">要求主體資料速率下限</span><span class="sxs-lookup"><span data-stu-id="acb26-850">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="acb26-851">如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。</span><span class="sxs-lookup"><span data-stu-id="acb26-851">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="acb26-852">如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 讓用戶端提高其傳送速率的時間量（最小值）;在這段時間內不會檢查速率。</span><span class="sxs-lookup"><span data-stu-id="acb26-852">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="acb26-853">寬限期可協助避免中斷連線，這是由於 TCP 緩慢啟動而一開始以低速傳送資料所造成。</span><span class="sxs-lookup"><span data-stu-id="acb26-853">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="acb26-854">預設速率下限為 240 個位元組/秒，寬限期為 5 秒。</span><span class="sxs-lookup"><span data-stu-id="acb26-854">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="acb26-855">速率下限也適用於回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-855">A minimum rate also applies to the response.</span></span> <span data-ttu-id="acb26-856">除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。</span><span class="sxs-lookup"><span data-stu-id="acb26-856">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="acb26-857">以下範例示範如何在 *Program.cs* 中設定資料速率下限：</span><span class="sxs-lookup"><span data-stu-id="acb26-857">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="acb26-858">要求標頭逾時</span><span class="sxs-lookup"><span data-stu-id="acb26-858">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="acb26-859">取得或設定伺服器花費在接收要求標頭的時間上限。</span><span class="sxs-lookup"><span data-stu-id="acb26-859">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="acb26-860">預設為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="acb26-860">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="acb26-861">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="acb26-861">Synchronous I/O</span></span>

<span data-ttu-id="acb26-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="acb26-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="acb26-863">預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="acb26-863">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="acb26-864">大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-864">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="acb26-865">只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。</span><span class="sxs-lookup"><span data-stu-id="acb26-865">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="acb26-866">下列範例會停用同步 i/o：</span><span class="sxs-lookup"><span data-stu-id="acb26-866">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="acb26-867">如需其他 Kestrel 選項和限制的資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="acb26-867">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="acb26-868">端點組態</span><span class="sxs-lookup"><span data-stu-id="acb26-868">Endpoint configuration</span></span>

<span data-ttu-id="acb26-869">ASP.NET Core 預設會繫結至：</span><span class="sxs-lookup"><span data-stu-id="acb26-869">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="acb26-870">`https://localhost:5001` (當有本機開發憑證存在時)</span><span class="sxs-lookup"><span data-stu-id="acb26-870">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="acb26-871">使用以下各項指定 URL：</span><span class="sxs-lookup"><span data-stu-id="acb26-871">Specify URLs using the:</span></span>

* <span data-ttu-id="acb26-872">`ASPNETCORE_URLS` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="acb26-872">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="acb26-873">`--urls` 命令列引數。</span><span class="sxs-lookup"><span data-stu-id="acb26-873">`--urls` command-line argument.</span></span>
* <span data-ttu-id="acb26-874">`urls` 主機組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="acb26-874">`urls` host configuration key.</span></span>
* <span data-ttu-id="acb26-875">`UseUrls` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="acb26-875">`UseUrls` extension method.</span></span>

<span data-ttu-id="acb26-876">使用這些方法提供的值可以是一或多個 HTTP 和 HTTPS 端點 (如果有預設憑證可用則為 HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="acb26-876">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="acb26-877">將值設定為以分號分隔的清單 (例如，`"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="acb26-877">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="acb26-878">如需有關這些方法的詳細資訊，請參閱[伺服器 URL](xref:fundamentals/host/web-host#server-urls) 和[覆寫設定](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="acb26-878">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="acb26-879">開發憑證會建立於：</span><span class="sxs-lookup"><span data-stu-id="acb26-879">A development certificate is created:</span></span>

* <span data-ttu-id="acb26-880">已安裝 [.NET Core SDK](/dotnet/core/sdk) 時。</span><span class="sxs-lookup"><span data-stu-id="acb26-880">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="acb26-881">[dev-certs 工具](xref:aspnetcore-2.1#https)用來建立憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-881">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="acb26-882">某些瀏覽器需要授與明確的許可權，以信任本機開發憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-882">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="acb26-883">專案範本預設會將應用程式設定為在 HTTPS 上執行，並包含 Https 重新導向 [和 HSTS 支援](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="acb26-883">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="acb26-884">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上呼叫 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法，來為 Kestrel 設定 URL 首碼和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-884">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="acb26-885">`UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵和 `ASPNETCORE_URLS` 環境變數同樣有效，但卻有本節稍後註明的限制 (針對 HTTPS 端點組態必須有預設憑證可用)。</span><span class="sxs-lookup"><span data-stu-id="acb26-885">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="acb26-886">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="acb26-886">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="acb26-887">ConfigureEndpointDefaults (動作 \<ListenOptions>) </span><span class="sxs-lookup"><span data-stu-id="acb26-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="acb26-888">指定組態 `Action` 以針對每個指定端點執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-888">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="acb26-889">呼叫 `ConfigureEndpointDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-889">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acb26-890">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-890">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="acb26-891">ConfigureHttpsDefaults (動作 \<HttpsConnectionAdapterOptions>) </span><span class="sxs-lookup"><span data-stu-id="acb26-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="acb26-892">指定組態 `Action` 以針對每個 HTTPS 端點執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-892">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="acb26-893">呼叫 `ConfigureHttpsDefaults` 多次會以最後一個指定的 `Action` 取代之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-893">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acb26-894">在呼叫之前呼叫所建立的端點 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> ，將不會套用預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="acb26-895">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="acb26-895">Configure(IConfiguration)</span></span>

<span data-ttu-id="acb26-896">建立設定載入器來設定以 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作為輸入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acb26-896">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="acb26-897">組態的範圍必須限於 Kestrel 的組態區段。</span><span class="sxs-lookup"><span data-stu-id="acb26-897">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="acb26-898">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="acb26-898">ListenOptions.UseHttps</span></span>

<span data-ttu-id="acb26-899">設定 Kestrel 使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-899">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="acb26-900">`ListenOptions.UseHttps` 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="acb26-900">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="acb26-901">`UseHttps`：設定 Kestrel 以搭配預設憑證使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-901">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="acb26-902">如果未設定預設憑證，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="acb26-902">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="acb26-903">`ListenOptions.UseHttps` 參數：</span><span class="sxs-lookup"><span data-stu-id="acb26-903">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="acb26-904">`filename` 是憑證檔案的路徑和檔案名稱，它相對於包含應用程式內容檔案的目錄。</span><span class="sxs-lookup"><span data-stu-id="acb26-904">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="acb26-905">`password` 是存取 X.509 憑證資料所需的密碼。</span><span class="sxs-lookup"><span data-stu-id="acb26-905">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="acb26-906">`configureOptions` 是設定 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acb26-906">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="acb26-907">傳回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="acb26-907">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="acb26-908">`storeName` 是要從中載入憑證的憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="acb26-908">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="acb26-909">`subject` 是憑證的主體名稱。</span><span class="sxs-lookup"><span data-stu-id="acb26-909">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="acb26-910">`allowInvalid` 表示是否應該考慮無效的憑證，例如自我簽署憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-910">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="acb26-911">`location` 是要從中載入憑證的存放區位置。</span><span class="sxs-lookup"><span data-stu-id="acb26-911">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="acb26-912">`serverCertificate` 是 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-912">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="acb26-913">在生產環境中，必須明確設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-913">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="acb26-914">至少必須提供預設憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-914">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="acb26-915">支援的組態描述如下：</span><span class="sxs-lookup"><span data-stu-id="acb26-915">Supported configurations described next:</span></span>

* <span data-ttu-id="acb26-916">無組態</span><span class="sxs-lookup"><span data-stu-id="acb26-916">No configuration</span></span>
* <span data-ttu-id="acb26-917">從組態取代預設憑證</span><span class="sxs-lookup"><span data-stu-id="acb26-917">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="acb26-918">變更程式碼中的預設值</span><span class="sxs-lookup"><span data-stu-id="acb26-918">Change the defaults in code</span></span>

<span data-ttu-id="acb26-919">*無組態*</span><span class="sxs-lookup"><span data-stu-id="acb26-919">*No configuration*</span></span>

<span data-ttu-id="acb26-920">Kestrel 會接聽 `http://localhost:5000` 和 `https://localhost:5001` (如果預設憑證可用的話)。</span><span class="sxs-lookup"><span data-stu-id="acb26-920">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="acb26-921">*從組態取代預設憑證*</span><span class="sxs-lookup"><span data-stu-id="acb26-921">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="acb26-922">`CreateDefaultBuilder` 預設會呼叫 `Configure(context.Configuration.GetSection("Kestrel"))` 以載入 Kestrel 設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-922">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="acb26-923">Kestrel 可以使用預設的 HTTPS 應用程式設定組態結構描述。</span><span class="sxs-lookup"><span data-stu-id="acb26-923">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="acb26-924">設定多個端點，包括 URL 和要使用的憑證－從磁碟上的檔案，或是從憑證存放區。</span><span class="sxs-lookup"><span data-stu-id="acb26-924">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="acb26-925">在下列 *appsettings.json* 範例中：</span><span class="sxs-lookup"><span data-stu-id="acb26-925">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="acb26-926">將 **AllowInvalid** 設定為 `true`，允許使用無效的憑證 (例如，自我簽署憑證)。</span><span class="sxs-lookup"><span data-stu-id="acb26-926">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="acb26-927">任何未指定憑證 (接下來範例中的 **HttpsDefaultCert**) 的 HTTPS 端點會回復為 [憑證]**[預設]** > \*\*\*\* 下定義的憑證或開發憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-927">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="acb26-928">除了針對任何憑證節點使用 [路徑]\*\*\*\* 和 [密碼]\*\*\*\*，還可以使用憑證存放區欄位指定憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-928">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="acb26-929">例如，您可以將**憑證**  >  **預設**憑證指定為：</span><span class="sxs-lookup"><span data-stu-id="acb26-929">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="acb26-930">結構描述附註：</span><span class="sxs-lookup"><span data-stu-id="acb26-930">Schema notes:</span></span>

* <span data-ttu-id="acb26-931">端點名稱不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="acb26-931">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="acb26-932">例如，`HTTPS` 和 `Https` 都有效。</span><span class="sxs-lookup"><span data-stu-id="acb26-932">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="acb26-933">`Url` 參數對每個端點而言都是必要的。</span><span class="sxs-lookup"><span data-stu-id="acb26-933">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="acb26-934">此參數的格式等同於最上層 `Urls` 組態參數，但是它限制為單一值。</span><span class="sxs-lookup"><span data-stu-id="acb26-934">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="acb26-935">這些端點會取代最上層 `Urls` 組態中定義的端點，而不是新增至其中。</span><span class="sxs-lookup"><span data-stu-id="acb26-935">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="acb26-936">透過 `Listen` 在程式碼中定義的端點，會與組態區段中定義的端點累計。</span><span class="sxs-lookup"><span data-stu-id="acb26-936">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="acb26-937">`Certificate` 區段是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="acb26-937">The `Certificate` section is optional.</span></span> <span data-ttu-id="acb26-938">如果未指定 `Certificate` 區段，則會使用先前案例中所定義的預設值。</span><span class="sxs-lookup"><span data-stu-id="acb26-938">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="acb26-939">如果沒有預設值可供使用，伺服器就會擲回例外狀況，且無法啟動。</span><span class="sxs-lookup"><span data-stu-id="acb26-939">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="acb26-940">`Certificate`區段同時支援**路徑** &ndash; **密碼**和**主體** &ndash; **存放區**憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-940">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="acb26-941">可以用這種方式定義任何數目的端點，只要它們不會導致連接埠衝突即可。</span><span class="sxs-lookup"><span data-stu-id="acb26-941">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="acb26-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 會傳回 `KestrelConfigurationLoader` 與 `.Endpoint(string name, listenOptions => { })` 方法，此方法可用來補充已設定的端點設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="acb26-943">`KestrelServerOptions.ConfigurationLoader` 可以直接存取，以繼續逐一查看現有的載入器，例如所提供的載入器 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 。</span><span class="sxs-lookup"><span data-stu-id="acb26-943">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="acb26-944">您可以在方法的選項中使用每個端點的設定區段， `Endpoint` 以便讀取自訂設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-944">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="acb26-945">可以藉由使用另一個區段再次呼叫 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 而載入多個組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-945">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="acb26-946">只會使用最後一個組態，除非在先前的執行個體上已明確呼叫 `Load`。</span><span class="sxs-lookup"><span data-stu-id="acb26-946">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="acb26-947">中繼套件不會呼叫 `Load`，如此可能會取代其預設組態區段。</span><span class="sxs-lookup"><span data-stu-id="acb26-947">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="acb26-948">`KestrelConfigurationLoader` 會將來自 `KestrelServerOptions` 的 API 的 `Listen` 系列鏡像為 `Endpoint` 多載，所以可在相同的位置設定程式碼和設定端點。</span><span class="sxs-lookup"><span data-stu-id="acb26-948">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="acb26-949">這些多載不使用名稱，並且只使用來自組態的預設組態。</span><span class="sxs-lookup"><span data-stu-id="acb26-949">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="acb26-950">*變更程式碼中的預設值*</span><span class="sxs-lookup"><span data-stu-id="acb26-950">*Change the defaults in code*</span></span>

<span data-ttu-id="acb26-951">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 可以用來變更 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的預設設定，包括覆寫先前案例中指定的預設憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-951">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="acb26-952">`ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 應該在設定任何端點之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="acb26-952">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="acb26-953">*SNI 的 Kestrel 支援*</span><span class="sxs-lookup"><span data-stu-id="acb26-953">*Kestrel support for SNI*</span></span>

<span data-ttu-id="acb26-954">[伺服器名稱指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可以用於在相同的 IP 位址和連接埠上裝載多個網域。</span><span class="sxs-lookup"><span data-stu-id="acb26-954">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="acb26-955">SNI 若要運作，用戶端會在 TLS 信號交換期間傳送安全工作階段的主機名稱給伺服器，讓伺服器可以提供正確的憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-955">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="acb26-956">用戶端在 TLS 信號交換之後的安全工作階段期間，會使用所提供的憑證與伺服器進行加密通訊。</span><span class="sxs-lookup"><span data-stu-id="acb26-956">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="acb26-957">Kestrel 透過 `ServerCertificateSelector` 回呼來支援 SNI。</span><span class="sxs-lookup"><span data-stu-id="acb26-957">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="acb26-958">回呼會針對每個連線叫用一次，允許應用程式檢查主機名稱並選取適當的憑證。</span><span class="sxs-lookup"><span data-stu-id="acb26-958">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="acb26-959">SNI 支援需要：</span><span class="sxs-lookup"><span data-stu-id="acb26-959">SNI support requires:</span></span>

* <span data-ttu-id="acb26-960">在目標 framework `netcoreapp2.1` 或更新版本上執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-960">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="acb26-961">在 `net461` 或更新版本中，會叫用回呼，但 `name` 一律為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-961">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="acb26-962">如果用戶端不在 TLS 信號交換中提供主機名稱參數，則 `name` 也是 `null`。</span><span class="sxs-lookup"><span data-stu-id="acb26-962">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="acb26-963">所有網站都在相同的 Kestrel 執行個體上執行。</span><span class="sxs-lookup"><span data-stu-id="acb26-963">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="acb26-964">在不使用反向 Proxy 的情況下，Kestrel 不支援跨多個執行個體共用 IP 位址和連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-964">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="acb26-965">連接記錄</span><span class="sxs-lookup"><span data-stu-id="acb26-965">Connection logging</span></span>

<span data-ttu-id="acb26-966">呼叫 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以發出連接上位元組層級通訊的 Debug 層級記錄。</span><span class="sxs-lookup"><span data-stu-id="acb26-966">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="acb26-967">連線記錄有助於疑難排解低層級通訊中的問題，例如在 TLS 加密期間和 proxy 後方。</span><span class="sxs-lookup"><span data-stu-id="acb26-967">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="acb26-968">如果 `UseConnectionLogging` 之前放置 `UseHttps` ，則會記錄加密的流量。</span><span class="sxs-lookup"><span data-stu-id="acb26-968">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="acb26-969">如果 `UseConnectionLogging` 放置在之後 `UseHttps` ，則會記錄解密的流量。</span><span class="sxs-lookup"><span data-stu-id="acb26-969">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="acb26-970">繫結至 TCP 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-970">Bind to a TCP socket</span></span>

<span data-ttu-id="acb26-971"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法會繫結至 TCP 通訊端，而選項 Lambda 則會允許 X.509 憑證設定：</span><span class="sxs-lookup"><span data-stu-id="acb26-971">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="acb26-972">此範例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>來為端點設定 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-972">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="acb26-973">若要設定特定端點的其他 Kestrel 設定，請使用相同的 API。</span><span class="sxs-lookup"><span data-stu-id="acb26-973">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="acb26-974">繫結至 Unix 通訊端</span><span class="sxs-lookup"><span data-stu-id="acb26-974">Bind to a Unix socket</span></span>

<span data-ttu-id="acb26-975">請使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 在 Unix 通訊端上進行接聽以改善 Nginx 的效能，如此範例所示：</span><span class="sxs-lookup"><span data-stu-id="acb26-975">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="acb26-976">在 Nginx confiuguration 檔案中，將 `server`  >  `location`  >  `proxy_pass` 專案設定為 `http://unix:/tmp/{KESTREL SOCKET}:/;` 。</span><span class="sxs-lookup"><span data-stu-id="acb26-976">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="acb26-977">`{KESTREL SOCKET}` 這是提供給 (的通訊端名稱 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ，例如 `kestrel-test.sock` 上述範例) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-977">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="acb26-978">確定 Nginx (可寫入通訊端，例如 `chmod go+w /tmp/kestrel-test.sock`) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-978">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="acb26-979">連接埠 0</span><span class="sxs-lookup"><span data-stu-id="acb26-979">Port 0</span></span>

<span data-ttu-id="acb26-980">指定連接埠號碼 `0` 時，Kestrel 會動態繫結至可用的連接埠。</span><span class="sxs-lookup"><span data-stu-id="acb26-980">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="acb26-981">下列範例示範如何判斷 Kestrel 在執行階段實際上繫結至哪一個連接埠：</span><span class="sxs-lookup"><span data-stu-id="acb26-981">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="acb26-982">當應用程式執行時，主控台視窗輸出會指出可以連線到應用程式的動態連接埠：</span><span class="sxs-lookup"><span data-stu-id="acb26-982">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="acb26-983">限制</span><span class="sxs-lookup"><span data-stu-id="acb26-983">Limitations</span></span>

<span data-ttu-id="acb26-984">使用下列方法來設定端點：</span><span class="sxs-lookup"><span data-stu-id="acb26-984">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="acb26-985">`--urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="acb26-985">`--urls` command-line argument</span></span>
* <span data-ttu-id="acb26-986">`urls` 主機組態索引鍵</span><span class="sxs-lookup"><span data-stu-id="acb26-986">`urls` host configuration key</span></span>
* <span data-ttu-id="acb26-987">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="acb26-987">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="acb26-988">要讓程式碼使用 Kestrel 以外的伺服器，這些方法會很有用。</span><span class="sxs-lookup"><span data-stu-id="acb26-988">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="acb26-989">不過，請注意下列限制：</span><span class="sxs-lookup"><span data-stu-id="acb26-989">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="acb26-990">HTTPS 無法與這些方法搭配使用，除非在 HTTPS 端點設定中提供預設憑證 (例如，使用 `KestrelServerOptions` 設定或設定檔，如本主題稍早所示)。</span><span class="sxs-lookup"><span data-stu-id="acb26-990">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="acb26-991">當同時使用 `Listen` 和 `UseUrls` 方法時，`Listen` 端點會覆寫 `UseUrls` 端點。</span><span class="sxs-lookup"><span data-stu-id="acb26-991">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="acb26-992">IIS 端點設定</span><span class="sxs-lookup"><span data-stu-id="acb26-992">IIS endpoint configuration</span></span>

<span data-ttu-id="acb26-993">使用 IIS 時，IIS 覆寫繫結的 URL 繫結是由 `Listen` 或 `UseUrls` 設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-993">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="acb26-994">如需詳細資訊，請參閱 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)主題。</span><span class="sxs-lookup"><span data-stu-id="acb26-994">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="acb26-995">傳輸組態</span><span class="sxs-lookup"><span data-stu-id="acb26-995">Transport configuration</span></span>

<span data-ttu-id="acb26-996">隨著 ASP.NET Core 2.1 的發行，Kestrel 的預設傳輸不再根據 Libuv，而是改為根據受控通訊端。</span><span class="sxs-lookup"><span data-stu-id="acb26-996">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="acb26-997">對於升級到 2.1 且會呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 並相依於下列任一套件的 ASP.NET Core 2.0 應用程式來說，這是一項中斷性變更：</span><span class="sxs-lookup"><span data-stu-id="acb26-997">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="acb26-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (直接套件參考)</span><span class="sxs-lookup"><span data-stu-id="acb26-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="acb26-999">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="acb26-999">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="acb26-1000">針對需要使用 Libuv 的專案：</span><span class="sxs-lookup"><span data-stu-id="acb26-1000">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="acb26-1001">將 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 套件的相依性新增至應用程式的專案檔中：</span><span class="sxs-lookup"><span data-stu-id="acb26-1001">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="acb26-1002">呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="acb26-1002">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="acb26-1003">URL 前置詞</span><span class="sxs-lookup"><span data-stu-id="acb26-1003">URL prefixes</span></span>

<span data-ttu-id="acb26-1004">使用 `UseUrls`、`--urls` 命令列引數、`urls` 主機組態索引鍵或 `ASPNETCORE_URLS` 環境變數時，URL 前置詞可以採用下列任一格式。</span><span class="sxs-lookup"><span data-stu-id="acb26-1004">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="acb26-1005">只有 HTTP URL 前置詞有效。</span><span class="sxs-lookup"><span data-stu-id="acb26-1005">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="acb26-1006">使用 `UseUrls` 來設定 URL 繫結時，Kestrel 不支援 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acb26-1006">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="acb26-1007">IPv4 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-1007">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="acb26-1008">`0.0.0.0` 是繫結至所有 IPv4 位址的特殊情況。</span><span class="sxs-lookup"><span data-stu-id="acb26-1008">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="acb26-1009">IPv6 位址與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-1009">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="acb26-1010">`[::]` 是相當於 IPv4 `0.0.0.0` 的 IPv6 對等項目。</span><span class="sxs-lookup"><span data-stu-id="acb26-1010">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="acb26-1011">主機名稱與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-1011">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="acb26-1012">主機名稱 `*` 和 `+` 並不特殊。</span><span class="sxs-lookup"><span data-stu-id="acb26-1012">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="acb26-1013">無法辨識為有效 IP 位址或 `localhost` 的任何項目，都會繫結至所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="acb26-1013">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="acb26-1014">若要在相同連接埠上將不同的主機名稱繫結至不同的 ASP.NET Core 應用程式，請使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或反向 Proxy 伺服器 (例如 IIS、Nginx 或 Apache)。</span><span class="sxs-lookup"><span data-stu-id="acb26-1014">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="acb26-1015">裝載於反向 Proxy 組態需要[主機篩選](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acb26-1015">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="acb26-1016">主機 `localhost` 名稱與連接埠號碼，或回送 IP 與連接埠號碼</span><span class="sxs-lookup"><span data-stu-id="acb26-1016">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="acb26-1017">如果指定 `localhost`，Kestrel 會嘗試同時繫結至 IPv4 和 IPv6 回送介面。</span><span class="sxs-lookup"><span data-stu-id="acb26-1017">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="acb26-1018">如果所要求的連接埠在任一個回送介面上由另一個服務使用，則 Kestrel 無法啟動。</span><span class="sxs-lookup"><span data-stu-id="acb26-1018">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="acb26-1019">如果任一回送介面由於任何其他原因 (最常見的原因是不支援 IPv6) 無法使用，Kestrel 就會記錄警告。</span><span class="sxs-lookup"><span data-stu-id="acb26-1019">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="acb26-1020">主機篩選</span><span class="sxs-lookup"><span data-stu-id="acb26-1020">Host filtering</span></span>

<span data-ttu-id="acb26-1021">雖然 Kestrel 根據前置詞來支援組態，例如 `http://example.com:5000`，Kestrel 大多會忽略主機名稱。</span><span class="sxs-lookup"><span data-stu-id="acb26-1021">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="acb26-1022">主機 `localhost` 是特殊情況，用來繫結到回送位址。</span><span class="sxs-lookup"><span data-stu-id="acb26-1022">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="acb26-1023">任何非明確 IP 位址的主機，會繫結至所有公用 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="acb26-1023">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="acb26-1024">`Host` 標頭未驗證。</span><span class="sxs-lookup"><span data-stu-id="acb26-1024">`Host` headers aren't validated.</span></span>

<span data-ttu-id="acb26-1025">因應措施是使用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="acb26-1025">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="acb26-1026">主機篩選中介軟體是由 [AspNetCore HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 套件所提供，此套件隨附于 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 或 2.2) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-1026">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="acb26-1027">中介軟體是由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 所新增，它會呼叫 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>：</span><span class="sxs-lookup"><span data-stu-id="acb26-1027">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="acb26-1028">預設停用主機篩選中介軟體。</span><span class="sxs-lookup"><span data-stu-id="acb26-1028">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="acb26-1029">若要啟用中介軟體，請 `AllowedHosts` 在 appsettings*上的appsettings.js*中定義金鑰 / \* \<EnvironmentName> 。json\*。</span><span class="sxs-lookup"><span data-stu-id="acb26-1029">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="acb26-1030">此值是以分號分隔的主機名稱清單，不含連接埠號碼：</span><span class="sxs-lookup"><span data-stu-id="acb26-1030">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="acb26-1031">*appsettings.js*：</span><span class="sxs-lookup"><span data-stu-id="acb26-1031">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="acb26-1032">[轉送的標頭中介軟體](xref:host-and-deploy/proxy-load-balancer)也有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 選項。</span><span class="sxs-lookup"><span data-stu-id="acb26-1032">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="acb26-1033">在不同的案例中，轉送標頭中介軟體和主機篩選中介軟體有類似的功能。</span><span class="sxs-lookup"><span data-stu-id="acb26-1033">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="acb26-1034">當不保留 `Host` 標頭，卻使用反向 Proxy 伺服器或負載平衡器轉送要求時，可使用轉送標頭中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="acb26-1034">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="acb26-1035">當使用 Kestrel 作為公眾對應 Edge Server，或直接轉送 `Host` 標頭時，可使用主機篩選中介軟體設定 `AllowedHosts`。</span><span class="sxs-lookup"><span data-stu-id="acb26-1035">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="acb26-1036">如需轉送標頭中介軟體的詳細資訊，請參閱<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="acb26-1036">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="acb26-1037">HTTP/1.1 要求清空</span><span class="sxs-lookup"><span data-stu-id="acb26-1037">HTTP/1.1 request draining</span></span>

<span data-ttu-id="acb26-1038">開啟 HTTP 連接相當耗時。</span><span class="sxs-lookup"><span data-stu-id="acb26-1038">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="acb26-1039">若是 HTTPS，也需要大量資源。</span><span class="sxs-lookup"><span data-stu-id="acb26-1039">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="acb26-1040">因此，Kestrel 會嘗試針對每個 HTTP/1.1 通訊協定重複使用連接。</span><span class="sxs-lookup"><span data-stu-id="acb26-1040">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="acb26-1041">要求主體必須完全取用，才能重複使用連線。</span><span class="sxs-lookup"><span data-stu-id="acb26-1041">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="acb26-1042">應用程式不一定會取用要求主體，例如伺服器傳回重新 `POST` 導向或404回應的要求。</span><span class="sxs-lookup"><span data-stu-id="acb26-1042">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="acb26-1043">在 `POST` -重新導向案例中：</span><span class="sxs-lookup"><span data-stu-id="acb26-1043">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="acb26-1044">用戶端可能已經傳送部分 `POST` 資料。</span><span class="sxs-lookup"><span data-stu-id="acb26-1044">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="acb26-1045">伺服器會寫入301回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-1045">The server writes the 301 response.</span></span>
* <span data-ttu-id="acb26-1046">連線無法用於新的要求，直到 `POST` 前一個要求主體的資料已完全讀取為止。</span><span class="sxs-lookup"><span data-stu-id="acb26-1046">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="acb26-1047">Kestrel 會嘗試清空要求主體。</span><span class="sxs-lookup"><span data-stu-id="acb26-1047">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="acb26-1048">清空要求主體表示在不處理資料的情況下讀取和捨棄資料。</span><span class="sxs-lookup"><span data-stu-id="acb26-1048">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="acb26-1049">清空程式可讓您在允許連線重複使用時，以及清空任何剩餘資料所需的時間之間進行 tradoff：</span><span class="sxs-lookup"><span data-stu-id="acb26-1049">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="acb26-1050">清空有五秒的時間，無法設定。</span><span class="sxs-lookup"><span data-stu-id="acb26-1050">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="acb26-1051">如果或標頭所指定的所有資料在 `Content-Length` `Transfer-Encoding` 此時間之前未被讀取，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="acb26-1051">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="acb26-1052">有時您可能會想要在寫入回應之前或之後立即終止要求。</span><span class="sxs-lookup"><span data-stu-id="acb26-1052">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="acb26-1053">例如，用戶端可能會有限制性的資料上限，因此限制上傳的資料可能是優先考慮。</span><span class="sxs-lookup"><span data-stu-id="acb26-1053">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="acb26-1054">在這種情況下，若要終止要求，請從控制器、頁面或中介軟體呼叫[HttpCoNtext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) 。 Razor</span><span class="sxs-lookup"><span data-stu-id="acb26-1054">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="acb26-1055">呼叫時有一些注意事項 `Abort` ：</span><span class="sxs-lookup"><span data-stu-id="acb26-1055">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="acb26-1056">建立新的連接可能很慢且昂貴。</span><span class="sxs-lookup"><span data-stu-id="acb26-1056">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="acb26-1057">在連接關閉之前，不保證用戶端已讀取回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-1057">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="acb26-1058">呼叫 `Abort` 應該很罕見，而且會保留給嚴重的錯誤案例，而不是常見的錯誤。</span><span class="sxs-lookup"><span data-stu-id="acb26-1058">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="acb26-1059">只有 `Abort` 在需要解決特定問題時才呼叫。</span><span class="sxs-lookup"><span data-stu-id="acb26-1059">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="acb26-1060">例如， `Abort` 如果惡意用戶端嘗試 `POST` 資料或用戶端程式代碼中有錯誤，而導致大量或許多要求，請呼叫。</span><span class="sxs-lookup"><span data-stu-id="acb26-1060">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="acb26-1061">請勿呼叫 `Abort` 常見的錯誤狀況，例如 HTTP 404 (找不到) 。</span><span class="sxs-lookup"><span data-stu-id="acb26-1061">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="acb26-1062">在呼叫之前呼叫 [HttpResponse](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) ， `Abort` 可確保伺服器已完成寫入回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-1062">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="acb26-1063">不過，用戶端行為不是可預測的，而且在中斷連接之前，可能不會讀取回應。</span><span class="sxs-lookup"><span data-stu-id="acb26-1063">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="acb26-1064">HTTP/2 的這個程式不同，因為通訊協定支援中止個別要求資料流程，而不需要關閉連接。</span><span class="sxs-lookup"><span data-stu-id="acb26-1064">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="acb26-1065">五秒的清空超時時間不適用。</span><span class="sxs-lookup"><span data-stu-id="acb26-1065">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="acb26-1066">如果在完成回應之後有任何未讀取的要求主體資料，則伺服器會傳送 HTTP/2 RST 框架。</span><span class="sxs-lookup"><span data-stu-id="acb26-1066">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="acb26-1067">系統會忽略其他要求主體資料框架。</span><span class="sxs-lookup"><span data-stu-id="acb26-1067">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="acb26-1068">可能的話，最好是讓用戶端利用預期的 [： 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 要求標頭，並等待伺服器回應，然後再開始傳送要求本文。</span><span class="sxs-lookup"><span data-stu-id="acb26-1068">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="acb26-1069">這可讓用戶端有機會檢查回應，並在傳送不需要的資料之前中止。</span><span class="sxs-lookup"><span data-stu-id="acb26-1069">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acb26-1070">其他資源</span><span class="sxs-lookup"><span data-stu-id="acb26-1070">Additional resources</span></span>

* <span data-ttu-id="acb26-1071">在 Linux 上使用 UNIX 通訊端時，通訊端不會在應用程式關閉時自動刪除。</span><span class="sxs-lookup"><span data-stu-id="acb26-1071">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="acb26-1072">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="acb26-1072">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="acb26-1073">RFC 7230：訊息語法和路由 (第 5.4 節：主機)</span><span class="sxs-lookup"><span data-stu-id="acb26-1073">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
