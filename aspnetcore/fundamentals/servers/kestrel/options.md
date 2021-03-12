---
title: 設定 ASP.NET Core Kestrel web 伺服器的選項
author: rick-anderson
description: 瞭解如何設定 Kestrel （適用于 ASP.NET Core 的跨平臺網頁伺服器）的選項。
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
ms.openlocfilehash: 48b4af2dfc925c4444c2bd0e43d04f2f0f3ddd17
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587004"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="2d503-103">設定 ASP.NET Core Kestrel web 伺服器的選項</span><span class="sxs-lookup"><span data-stu-id="2d503-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="2d503-104">Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。</span><span class="sxs-lookup"><span data-stu-id="2d503-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2d503-105">若要在呼叫 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> 之後提供額外的設定，請使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A>：</span><span class="sxs-lookup"><span data-stu-id="2d503-105">To provide additional configuration after calling <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A>, use <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A>:</span></span>

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

<span data-ttu-id="2d503-106">請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。</span><span class="sxs-lookup"><span data-stu-id="2d503-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2d503-107">`Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="2d503-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2d503-108">下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；</span><span class="sxs-lookup"><span data-stu-id="2d503-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2d503-109">在本文稍後所示的範例中，會在 c # 程式碼中設定 Kestrel 選項。</span><span class="sxs-lookup"><span data-stu-id="2d503-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="2d503-110">您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項。</span><span class="sxs-lookup"><span data-stu-id="2d503-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2d503-111">例如，檔案設定 [提供者](xref:fundamentals/configuration/index#file-configuration-provider) 可以從或檔案載入 Kestrel 設定 `appsettings.json` `appsettings.{Environment}.json` ：</span><span class="sxs-lookup"><span data-stu-id="2d503-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an `appsettings.json` or `appsettings.{Environment}.json` file:</span></span>

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
> <span data-ttu-id="2d503-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [端點](xref:fundamentals/servers/kestrel/endpoints) 設定可從設定提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="2d503-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="2d503-113">其餘的 Kestrel 設定必須在 c # 程式碼中設定。</span><span class="sxs-lookup"><span data-stu-id="2d503-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="2d503-114">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="2d503-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2d503-115">設定 Kestrel in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2d503-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2d503-116">將的實例插入 `IConfiguration` 至 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="2d503-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2d503-117">下列範例假設將插入的設定指派給 `Configuration` 屬性。</span><span class="sxs-lookup"><span data-stu-id="2d503-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2d503-118">在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="2d503-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2d503-119">設定 Kestrel 建立主機時：</span><span class="sxs-lookup"><span data-stu-id="2d503-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2d503-120">在中 `Program.cs` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：</span><span class="sxs-lookup"><span data-stu-id="2d503-120">In `Program.cs`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2d503-121">上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。</span><span class="sxs-lookup"><span data-stu-id="2d503-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="2d503-122">一般限制</span><span class="sxs-lookup"><span data-stu-id="2d503-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2d503-123">Keep-alive 逾時</span><span class="sxs-lookup"><span data-stu-id="2d503-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2d503-124">取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="2d503-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2d503-125">預設為 2 分鐘。</span><span class="sxs-lookup"><span data-stu-id="2d503-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2d503-126">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="2d503-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2d503-127">可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：</span><span class="sxs-lookup"><span data-stu-id="2d503-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2d503-128">已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。</span><span class="sxs-lookup"><span data-stu-id="2d503-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2d503-129">升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="2d503-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2d503-130">連線數目上限預設為無限制 (null)。</span><span class="sxs-lookup"><span data-stu-id="2d503-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2d503-131">要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="2d503-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2d503-132">預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="2d503-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2d503-133">若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：</span><span class="sxs-lookup"><span data-stu-id="2d503-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2d503-134">下列範例顯示如何在每個要求上設定應用程式的條件約束：</span><span class="sxs-lookup"><span data-stu-id="2d503-134">The following example shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2d503-135">覆寫中介軟體中特定要求的設定：</span><span class="sxs-lookup"><span data-stu-id="2d503-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2d503-136">如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2d503-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2d503-137">有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="2d503-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2d503-138">當應用程式在[ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module)後方執行[跨進程](xref:host-and-deploy/iis/index#out-of-process-hosting-model)時，會停用 Kestrel 的要求主體大小限制。</span><span class="sxs-lookup"><span data-stu-id="2d503-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="2d503-139">IIS 已設定限制。</span><span class="sxs-lookup"><span data-stu-id="2d503-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2d503-140">要求主體資料速率下限</span><span class="sxs-lookup"><span data-stu-id="2d503-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2d503-141">如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。</span><span class="sxs-lookup"><span data-stu-id="2d503-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2d503-142">如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 允許用戶端增加其傳送速率的時間量（最小值）。</span><span class="sxs-lookup"><span data-stu-id="2d503-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="2d503-143">在這段時間內不會檢查速率。</span><span class="sxs-lookup"><span data-stu-id="2d503-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="2d503-144">寬限期有助於避免因為 TCP 速度太慢而中斷一開始傳送資料的速度變慢的連接。</span><span class="sxs-lookup"><span data-stu-id="2d503-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="2d503-145">預設的最小速率是每秒240個位元組，並有5秒的寬限期。</span><span class="sxs-lookup"><span data-stu-id="2d503-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="2d503-146">速率下限也適用於回應。</span><span class="sxs-lookup"><span data-stu-id="2d503-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2d503-147">除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。</span><span class="sxs-lookup"><span data-stu-id="2d503-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2d503-148">以下範例示範如何設定中的最小資料速率 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="2d503-148">Here's an example that shows how to configure the minimum data rates in `Program.cs`:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="2d503-149">覆寫中介軟體中每個要求的最小速率限制：</span><span class="sxs-lookup"><span data-stu-id="2d503-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2d503-150"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>先前範例中所參考的不存在 <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> 于 HTTP/2 要求中。</span><span class="sxs-lookup"><span data-stu-id="2d503-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> for HTTP/2 requests.</span></span> <span data-ttu-id="2d503-151">因為通訊協定對要求多工的支援，所以 HTTP/2 通常不支援修改依要求的速率限制。</span><span class="sxs-lookup"><span data-stu-id="2d503-151">Modifying rate limits on a per-request basis isn't generally supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2d503-152">不過，<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> 仍存在 HTTP/2 要求的 `HttpContext.Features`您仍能透過將 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> 設定為 `null` (即使是針對 HTTP/2 要求)，以個別要求基礎來「完全停用」讀取素率限制。</span><span class="sxs-lookup"><span data-stu-id="2d503-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="2d503-153">嘗試讀取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或嘗試將它設定為 `null` 以外的值將會導致擲回 <xref:System.NotSupportedException> (假設要求是 HTTP/2 要求)。</span><span class="sxs-lookup"><span data-stu-id="2d503-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a <xref:System.NotSupportedException> being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="2d503-154">透過 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> 設定的全伺服器速率限制皆仍套用至 HTTP/1.x 及 HTTP/2 連線。</span><span class="sxs-lookup"><span data-stu-id="2d503-154">Server-wide rate limits configured via <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2d503-155">要求標頭逾時</span><span class="sxs-lookup"><span data-stu-id="2d503-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2d503-156">取得或設定伺服器花費在接收要求標頭的時間上限。</span><span class="sxs-lookup"><span data-stu-id="2d503-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2d503-157">預設為 30 秒。</span><span class="sxs-lookup"><span data-stu-id="2d503-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="2d503-158">HTTP/2 限制</span><span class="sxs-lookup"><span data-stu-id="2d503-158">HTTP/2 limits</span></span>

<span data-ttu-id="2d503-159">此區段中的限制設定為 on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="2d503-159">The limits in this section are set on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType>.</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2d503-160">每個連線的資料流數目上限</span><span class="sxs-lookup"><span data-stu-id="2d503-160">Maximum streams per connection</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>

<span data-ttu-id="2d503-161">限制每個 HTTP/2 連接的並行要求資料流程數目。</span><span class="sxs-lookup"><span data-stu-id="2d503-161">Limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2d503-162">超出的資料流會被拒絕。</span><span class="sxs-lookup"><span data-stu-id="2d503-162">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="2d503-163">預設值是 100。</span><span class="sxs-lookup"><span data-stu-id="2d503-163">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2d503-164">標頭表格大小</span><span class="sxs-lookup"><span data-stu-id="2d503-164">Header table size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.HeaderTableSize>

<span data-ttu-id="2d503-165">HPACK 解碼器可解壓縮 HTTP/2 連線的 HTTP 標頭。</span><span class="sxs-lookup"><span data-stu-id="2d503-165">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2d503-166">`HeaderTableSize` 會限制 HPACK 解碼器所使用的標頭壓縮表格大小。</span><span class="sxs-lookup"><span data-stu-id="2d503-166">`HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2d503-167">這個值是以八位元提供，而且必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="2d503-167">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="2d503-168">預設值為 4096。</span><span class="sxs-lookup"><span data-stu-id="2d503-168">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2d503-169">框架大小上限</span><span class="sxs-lookup"><span data-stu-id="2d503-169">Maximum frame size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxFrameSize>

<span data-ttu-id="2d503-170">指出伺服器接收或傳送之 HTTP/2 連接框架承載的允許大小上限。</span><span class="sxs-lookup"><span data-stu-id="2d503-170">Indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="2d503-171">這個值是以八位元提供，而且必須介於 2^14 (16,384) 到 2^24-1 (16,777,215) 之間。</span><span class="sxs-lookup"><span data-stu-id="2d503-171">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="2d503-172">預設值為 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="2d503-172">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2d503-173">要求標頭大小上限</span><span class="sxs-lookup"><span data-stu-id="2d503-173">Maximum request header size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxRequestHeaderFieldSize>

<span data-ttu-id="2d503-174">指出要求標頭值的最大允許大小（以八位）。</span><span class="sxs-lookup"><span data-stu-id="2d503-174">Indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2d503-175">這項限制適用于其壓縮和未壓縮標記法中的名稱和值。</span><span class="sxs-lookup"><span data-stu-id="2d503-175">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2d503-176">此值必須大於零 (0)。</span><span class="sxs-lookup"><span data-stu-id="2d503-176">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="2d503-177">預設值為 8,192。</span><span class="sxs-lookup"><span data-stu-id="2d503-177">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2d503-178">初始連線視窗大小</span><span class="sxs-lookup"><span data-stu-id="2d503-178">Initial connection window size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialConnectionWindowSize>

<span data-ttu-id="2d503-179">以位元組為單位，表示伺服器在所有要求之間匯總的最大要求主體資料（以位元組為單位），每個連接)  (資料流程。</span><span class="sxs-lookup"><span data-stu-id="2d503-179">Indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2d503-180">要求也皆受 `Http2.InitialStreamWindowSize` 所限制。</span><span class="sxs-lookup"><span data-stu-id="2d503-180">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2d503-181">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="2d503-181">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="2d503-182">預設值為 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="2d503-182">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2d503-183">初始資料流視窗大小</span><span class="sxs-lookup"><span data-stu-id="2d503-183">Initial stream window size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialStreamWindowSize>

<span data-ttu-id="2d503-184">以位元組為單位，表示每個要求 (串流) 的最大要求主體資料（以位元組為單位）。</span><span class="sxs-lookup"><span data-stu-id="2d503-184">Indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2d503-185">要求也受限於 [`InitialConnectionWindowSize`](#initial-connection-window-size) 。</span><span class="sxs-lookup"><span data-stu-id="2d503-185">Requests are also limited by [`InitialConnectionWindowSize`](#initial-connection-window-size).</span></span> <span data-ttu-id="2d503-186">此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="2d503-186">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="2d503-187">預設值為 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="2d503-187">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="2d503-188">HTTP/2 保持運作 ping 設定</span><span class="sxs-lookup"><span data-stu-id="2d503-188">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="2d503-189">Kestrel 可以設定為將 HTTP/2 ping 傳送至連線的用戶端。</span><span class="sxs-lookup"><span data-stu-id="2d503-189">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="2d503-190">HTTP/2 ping 的服務有多個用途：</span><span class="sxs-lookup"><span data-stu-id="2d503-190">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="2d503-191">保持閒置連接保持運作。</span><span class="sxs-lookup"><span data-stu-id="2d503-191">Keep idle connections alive.</span></span> <span data-ttu-id="2d503-192">某些用戶端和 proxy 伺服器會關閉閒置的連線。</span><span class="sxs-lookup"><span data-stu-id="2d503-192">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="2d503-193">HTTP/2 ping 會視為連線上的活動，並防止連接關閉為閒置。</span><span class="sxs-lookup"><span data-stu-id="2d503-193">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="2d503-194">關閉狀況不良的連接。</span><span class="sxs-lookup"><span data-stu-id="2d503-194">Close unhealthy connections.</span></span> <span data-ttu-id="2d503-195">在設定的時間內，用戶端沒有回應「保持運作」偵測的連接會由伺服器關閉。</span><span class="sxs-lookup"><span data-stu-id="2d503-195">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="2d503-196">有兩個與 HTTP/2 保持運作 ping 相關的設定選項：</span><span class="sxs-lookup"><span data-stu-id="2d503-196">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="2d503-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay> 是設定 <xref:System.TimeSpan> ping 間隔的。</span><span class="sxs-lookup"><span data-stu-id="2d503-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay> is a <xref:System.TimeSpan> that configures the ping interval.</span></span> <span data-ttu-id="2d503-198">如果伺服器在這段時間內未收到任何框架，則伺服器會將保持運作的偵測傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="2d503-198">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="2d503-199">當此選項設定為時，會停用 [保持作用中的 ping] <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="2d503-199">Keep alive pings are disabled when this option is set to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span> <span data-ttu-id="2d503-200">預設值是 <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="2d503-200">The default value is <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="2d503-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout> 是 <xref:System.TimeSpan> 設定 ping timeout 的。</span><span class="sxs-lookup"><span data-stu-id="2d503-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout> is a <xref:System.TimeSpan> that configures the ping timeout.</span></span> <span data-ttu-id="2d503-202">如果伺服器未在此時間內接收任何框架，例如回應偵測，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="2d503-202">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="2d503-203">當此選項設定為時，會停用 [保持作用中] 超時 <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="2d503-203">Keep alive timeout is disabled when this option is set to <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>.</span></span> <span data-ttu-id="2d503-204">預設值為 20 秒。</span><span class="sxs-lookup"><span data-stu-id="2d503-204">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingDelay = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="2d503-205">其他選項</span><span class="sxs-lookup"><span data-stu-id="2d503-205">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2d503-206">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="2d503-206">Synchronous I/O</span></span>

<span data-ttu-id="2d503-207"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="2d503-207"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2d503-208">預設值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="2d503-208">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="2d503-209">大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。</span><span class="sxs-lookup"><span data-stu-id="2d503-209">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2d503-210">只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。</span><span class="sxs-lookup"><span data-stu-id="2d503-210">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2d503-211">下列範例會啟用同步 i/o：</span><span class="sxs-lookup"><span data-stu-id="2d503-211">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2d503-212">如需其他 Kestrel 選項和限制的資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="2d503-212">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
