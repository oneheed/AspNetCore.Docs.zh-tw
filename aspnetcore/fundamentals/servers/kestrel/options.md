---
title: 設定 ASP.NET Core Kestrel web 伺服器的選項
author: rick-anderson
description: 瞭解如何設定 Kestrel 的選項，這是適用于 ASP.NET Core 的跨平臺網頁伺服器。
monikerRange: '>= aspnetcore-5.0'
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253930"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="2d38d-103">設定 ASP.NET Core Kestrel web 伺服器的選項</span><span class="sxs-lookup"><span data-stu-id="2d38d-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="2d38d-104">Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。</span><span class="sxs-lookup"><span data-stu-id="2d38d-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2d38d-105">若要在呼叫 `ConfigureWebHostDefaults` 之後提供額外的設定，請使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="2d38d-105">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

<span data-ttu-id="2d38d-106">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。</span><span class="sxs-lookup"><span data-stu-id="2d38d-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2d38d-107">`Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="2d38d-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2d38d-108">下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；</span><span class="sxs-lookup"><span data-stu-id="2d38d-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2d38d-109">在本文稍後所示的範例中，會在 c # 程式碼中設定 Kestrel 選項。</span><span class="sxs-lookup"><span data-stu-id="2d38d-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="2d38d-110">您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項。</span><span class="sxs-lookup"><span data-stu-id="2d38d-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2d38d-111">例如，檔案設定 [提供者](xref:fundamentals/configuration/index#file-configuration-provider) 可以從或 Appsettings 載入 Kestrel 設定 *appsettings.json* *。環境}. json* 檔案：</span><span class="sxs-lookup"><span data-stu-id="2d38d-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="2d38d-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [端點](xref:fundamentals/servers/kestrel/endpoints) 設定可從設定提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="2d38d-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="2d38d-113">其餘的 Kestrel 設定必須在 c # 程式碼中設定。</span><span class="sxs-lookup"><span data-stu-id="2d38d-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="2d38d-114">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="2d38d-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2d38d-115">設定 Kestrel in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2d38d-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2d38d-116">將的實例插入 `IConfiguration` 至 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="2d38d-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2d38d-117">下列範例假設將插入的設定指派給 `Configuration` 屬性。</span><span class="sxs-lookup"><span data-stu-id="2d38d-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2d38d-118">在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="2d38d-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2d38d-119">設定 Kestrel 建立主機時：</span><span class="sxs-lookup"><span data-stu-id="2d38d-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2d38d-120">在 *Program.cs* 中，將 `Kestrel` Configuration 區段載入 Kestrel 的設定中：</span><span class="sxs-lookup"><span data-stu-id="2d38d-120">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2d38d-121">上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。</span><span class="sxs-lookup"><span data-stu-id="2d38d-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="2d38d-122">一般限制</span><span class="sxs-lookup"><span data-stu-id="2d38d-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2d38d-123">Keep-alive 逾時</span><span class="sxs-lookup"><span data-stu-id="2d38d-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2d38d-124">取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2d38d-125">預設為 2 分鐘。</span><span class="sxs-lookup"><span data-stu-id="2d38d-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2d38d-126">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="2d38d-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2d38d-127">可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：</span><span class="sxs-lookup"><span data-stu-id="2d38d-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2d38d-128">已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。</span><span class="sxs-lookup"><span data-stu-id="2d38d-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2d38d-129">升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="2d38d-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2d38d-130">連線數目上限預設為無限制 (null)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2d38d-131">要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="2d38d-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2d38d-132">預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="2d38d-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2d38d-133">若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：</span><span class="sxs-lookup"><span data-stu-id="2d38d-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2d38d-134">以下範例會示範如何設定應用程式、每個要求的條件約束：</span><span class="sxs-lookup"><span data-stu-id="2d38d-134">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2d38d-135">覆寫中介軟體中特定要求的設定：</span><span class="sxs-lookup"><span data-stu-id="2d38d-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2d38d-136">如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2d38d-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2d38d-137">有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="2d38d-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2d38d-138">當應用程式在[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)後方執行[跨進程](xref:host-and-deploy/iis/index#out-of-process-hosting-model)時，會停用 Kestrel 的要求主體大小限制。</span><span class="sxs-lookup"><span data-stu-id="2d38d-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="2d38d-139">IIS 已設定限制。</span><span class="sxs-lookup"><span data-stu-id="2d38d-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2d38d-140">要求主體資料速率下限</span><span class="sxs-lookup"><span data-stu-id="2d38d-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2d38d-141">如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。</span><span class="sxs-lookup"><span data-stu-id="2d38d-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2d38d-142">如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 允許用戶端增加其傳送速率的時間量（最小值）。</span><span class="sxs-lookup"><span data-stu-id="2d38d-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="2d38d-143">在這段時間內不會檢查速率。</span><span class="sxs-lookup"><span data-stu-id="2d38d-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="2d38d-144">寬限期有助於避免因為 TCP 速度太慢而中斷一開始傳送資料的速度變慢的連接。</span><span class="sxs-lookup"><span data-stu-id="2d38d-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="2d38d-145">預設的最小速率是每秒240個位元組，並有5秒的寬限期。</span><span class="sxs-lookup"><span data-stu-id="2d38d-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="2d38d-146">速率下限也適用於回應。</span><span class="sxs-lookup"><span data-stu-id="2d38d-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2d38d-147">除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。</span><span class="sxs-lookup"><span data-stu-id="2d38d-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2d38d-148">以下範例示範如何在 *Program.cs* 中設定資料速率下限：</span><span class="sxs-lookup"><span data-stu-id="2d38d-148">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="2d38d-149">覆寫中介軟體中每個要求的最小速率限制：</span><span class="sxs-lookup"><span data-stu-id="2d38d-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2d38d-150"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>先前範例中所參考的不存在 `HttpContext.Features` 于 HTTP/2 要求中。</span><span class="sxs-lookup"><span data-stu-id="2d38d-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in `HttpContext.Features` for HTTP/2 requests.</span></span> <span data-ttu-id="2d38d-151">因為通訊協定對要求多工的支援，所以 HTTP/2 通常不支援修改依要求的速率限制。</span><span class="sxs-lookup"><span data-stu-id="2d38d-151">Modifying rate limits on a per-request basis is generally not supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2d38d-152">不過，<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> 仍存在 HTTP/2 要求的 `HttpContext.Features`您仍能透過將 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 設定為 `null` (即使是針對 HTTP/2 要求)，以個別要求基礎來「完全停用」讀取素率限制。</span><span class="sxs-lookup"><span data-stu-id="2d38d-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="2d38d-153">嘗試讀取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或嘗試將它設定為 `null` 以外的值將會導致擲回 `NotSupportedException` (假設要求是 HTTP/2 要求)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="2d38d-154">透過 `KestrelServerOptions.Limits` 設定的全伺服器速率限制皆仍套用至 HTTP/1.x 及 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="2d38d-154">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2d38d-155">要求標頭逾時</span><span class="sxs-lookup"><span data-stu-id="2d38d-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2d38d-156">取得或設定伺服器花費在接收要求標頭的時間上限。</span><span class="sxs-lookup"><span data-stu-id="2d38d-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2d38d-157">預設為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="2d38d-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="2d38d-158">HTTP/2 限制</span><span class="sxs-lookup"><span data-stu-id="2d38d-158">HTTP/2 limits</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2d38d-159">每個連線的資料流數目上限</span><span class="sxs-lookup"><span data-stu-id="2d38d-159">Maximum streams per connection</span></span>

<span data-ttu-id="2d38d-160">`Http2.MaxStreamsPerConnection` 會限制每個 HTTP/2 連線的同時要求資料流數目。</span><span class="sxs-lookup"><span data-stu-id="2d38d-160">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2d38d-161">超出的資料流會被拒絕。</span><span class="sxs-lookup"><span data-stu-id="2d38d-161">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="2d38d-162">預設值是 100。</span><span class="sxs-lookup"><span data-stu-id="2d38d-162">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2d38d-163">標頭表格大小</span><span class="sxs-lookup"><span data-stu-id="2d38d-163">Header table size</span></span>

<span data-ttu-id="2d38d-164">HPACK 解碼器可解壓縮 HTTP/2 連線的 HTTP 標頭。</span><span class="sxs-lookup"><span data-stu-id="2d38d-164">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2d38d-165">`Http2.HeaderTableSize` 會限制 HPACK 解碼器所使用的標頭壓縮表格大小。</span><span class="sxs-lookup"><span data-stu-id="2d38d-165">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2d38d-166">這個值是以八位元提供，而且必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-166">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="2d38d-167">預設值為 4096。</span><span class="sxs-lookup"><span data-stu-id="2d38d-167">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2d38d-168">框架大小上限</span><span class="sxs-lookup"><span data-stu-id="2d38d-168">Maximum frame size</span></span>

<span data-ttu-id="2d38d-169">`Http2.MaxFrameSize` 指出伺服器接收或傳送之 HTTP/2 連接框架承載的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="2d38d-169">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="2d38d-170">這個值是以八位元提供，而且必須介於 2^14 (16,384) 到 2^24-1 (16,777,215) 之間。</span><span class="sxs-lookup"><span data-stu-id="2d38d-170">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="2d38d-171">預設值為 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-171">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2d38d-172">要求標頭大小上限</span><span class="sxs-lookup"><span data-stu-id="2d38d-172">Maximum request header size</span></span>

<span data-ttu-id="2d38d-173">`Http2.MaxRequestHeaderFieldSize` 以八位元表示要求標頭值的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="2d38d-173">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2d38d-174">這項限制適用于其壓縮和未壓縮標記法中的名稱和值。</span><span class="sxs-lookup"><span data-stu-id="2d38d-174">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2d38d-175">此值必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-175">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="2d38d-176">預設值為 8,192。</span><span class="sxs-lookup"><span data-stu-id="2d38d-176">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2d38d-177">初始連線視窗大小</span><span class="sxs-lookup"><span data-stu-id="2d38d-177">Initial connection window size</span></span>

<span data-ttu-id="2d38d-178">`Http2.InitialConnectionWindowSize` 會以位元組表示伺服器緩衝每個連線之所有要求 (資料流) 單次彙總的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="2d38d-178">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2d38d-179">要求也皆受 `Http2.InitialStreamWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="2d38d-179">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2d38d-180">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-180">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="2d38d-181">預設值為 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-181">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2d38d-182">初始資料流視窗大小</span><span class="sxs-lookup"><span data-stu-id="2d38d-182">Initial stream window size</span></span>

<span data-ttu-id="2d38d-183">`Http2.InitialStreamWindowSize` 會以位元組表示每個要求 (資料流) 單次伺服器緩衝的要求內容資料上限。</span><span class="sxs-lookup"><span data-stu-id="2d38d-183">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2d38d-184">要求也皆受 `Http2.InitialConnectionWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="2d38d-184">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="2d38d-185">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-185">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="2d38d-186">預設值為 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="2d38d-186">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="2d38d-187">HTTP/2 保持運作 ping 設定</span><span class="sxs-lookup"><span data-stu-id="2d38d-187">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="2d38d-188">Kestrel 可以設定為將 HTTP/2 ping 傳送至連線的用戶端。</span><span class="sxs-lookup"><span data-stu-id="2d38d-188">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="2d38d-189">HTTP/2 ping 的服務有多個用途：</span><span class="sxs-lookup"><span data-stu-id="2d38d-189">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="2d38d-190">保持閒置連接保持運作。</span><span class="sxs-lookup"><span data-stu-id="2d38d-190">Keep idle connections alive.</span></span> <span data-ttu-id="2d38d-191">某些用戶端和 proxy 伺服器會關閉閒置的連線。</span><span class="sxs-lookup"><span data-stu-id="2d38d-191">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="2d38d-192">HTTP/2 ping 會視為連線上的活動，並防止連接關閉為閒置。</span><span class="sxs-lookup"><span data-stu-id="2d38d-192">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="2d38d-193">關閉狀況不良的連接。</span><span class="sxs-lookup"><span data-stu-id="2d38d-193">Close unhealthy connections.</span></span> <span data-ttu-id="2d38d-194">在設定的時間內，用戶端沒有回應「保持運作」偵測的連接會由伺服器關閉。</span><span class="sxs-lookup"><span data-stu-id="2d38d-194">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="2d38d-195">有兩個與 HTTP/2 保持運作 ping 相關的設定選項：</span><span class="sxs-lookup"><span data-stu-id="2d38d-195">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="2d38d-196">`Http2.KeepAlivePingInterval` 是設定 `TimeSpan` ping 間隔的。</span><span class="sxs-lookup"><span data-stu-id="2d38d-196">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping interval.</span></span> <span data-ttu-id="2d38d-197">如果伺服器在這段時間內未收到任何框架，則伺服器會將保持運作的偵測傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="2d38d-197">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="2d38d-198">當此選項設定為時，會停用 [保持作用中的 ping] `TimeSpan.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="2d38d-198">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="2d38d-199">預設值是 `TimeSpan.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="2d38d-199">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="2d38d-200">`Http2.KeepAlivePingTimeout` 是 `TimeSpan` 設定 ping timeout 的。</span><span class="sxs-lookup"><span data-stu-id="2d38d-200">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="2d38d-201">如果伺服器未在此時間內接收任何框架，例如回應偵測，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="2d38d-201">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="2d38d-202">當此選項設定為時，會停用 [保持作用中] 超時 `TimeSpan.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="2d38d-202">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="2d38d-203">預設值為 20 秒。</span><span class="sxs-lookup"><span data-stu-id="2d38d-203">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="2d38d-204">其他選項</span><span class="sxs-lookup"><span data-stu-id="2d38d-204">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2d38d-205">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="2d38d-205">Synchronous I/O</span></span>

<span data-ttu-id="2d38d-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="2d38d-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2d38d-207">預設值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="2d38d-207">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="2d38d-208">大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。</span><span class="sxs-lookup"><span data-stu-id="2d38d-208">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2d38d-209">只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。</span><span class="sxs-lookup"><span data-stu-id="2d38d-209">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2d38d-210">下列範例會啟用同步 i/o：</span><span class="sxs-lookup"><span data-stu-id="2d38d-210">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2d38d-211">如需其他 Kestrel 選項和限制的資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="2d38d-211">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
