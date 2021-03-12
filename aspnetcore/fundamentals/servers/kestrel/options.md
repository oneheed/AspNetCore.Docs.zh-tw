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
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>設定 ASP.NET Core Kestrel web 伺服器的選項

Kestrel 網頁伺服器所含的條件約束組態選項，在網際網路對應部署方面特別有用。

若要在呼叫 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> 之後提供額外的設定，請使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.ConfigureKestrel%2A>：

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

請在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 類別的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 屬性上設定條件約束。 `Limits` 屬性會保存 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 類別的執行個體。

下列範例會使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空間；

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

在本文稍後所示的範例中，會在 c # 程式碼中設定 Kestrel 選項。 您也可以使用設定 [提供者](xref:fundamentals/configuration/index)來設定 Kestrel 選項。 例如，檔案設定 [提供者](xref:fundamentals/configuration/index#file-configuration-provider) 可以從或檔案載入 Kestrel 設定 `appsettings.json` `appsettings.{Environment}.json` ：

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [端點](xref:fundamentals/servers/kestrel/endpoints) 設定可從設定提供者進行設定。 其餘的 Kestrel 設定必須在 c # 程式碼中設定。

使用下列 **其中一** 種方法：

* 設定 Kestrel in `Startup.ConfigureServices` ：

  1. 將的實例插入 `IConfiguration` 至 `Startup` 類別。 下列範例假設將插入的設定指派給 `Configuration` 屬性。
  2. 在中 `Startup.ConfigureServices` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：

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

* 設定 Kestrel 建立主機時：

  在中 `Program.cs` ，將 `Kestrel` configuration 區段載入 Kestrel 的設定：

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

上述兩種方法都可搭配任何設定 [提供者](xref:fundamentals/configuration/index)使用。

## <a name="general-limits"></a>一般限制

### <a name="keep-alive-timeout"></a>Keep-alive 逾時

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

取得或設定 [Keep-alive 逾時](https://tools.ietf.org/html/rfc7230#section-6.5) \(英文\)。 預設為 2 分鐘。

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>用戶端連線數目上限

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

可以使用下列程式碼，針對整個應用程式設定同時開啟的 TCP 連線數目上限：

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

已經從 HTTP 或 HTTPS 升級為另一個通訊協定 (例如，在 WebSocket 要求中) 的連線，有其個別限制。 升級連線之後，它不會納入 `MaxConcurrentConnections` 限制。

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

連線數目上限預設為無限制 (null)。

### <a name="maximum-request-body-size"></a>要求主體大小上限

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

預設的要求主體大小上限是 30,000,000 個位元組，大約 28.6 MB。

若要覆寫 ASP.NET Core MVC 應用程式中的限制，建議的方式是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>屬性：

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

下列範例顯示如何在每個要求上設定應用程式的條件約束：

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

覆寫中介軟體中特定要求的設定：

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

如果應用程式在應用程式開始讀取要求之後設定要求的限制，則會擲回例外狀況。 有一個 `IsReadOnly` 屬性會指出 `MaxRequestBodySize` 屬性處於唯讀狀態，這表示要設定限制已經太遲。

當應用程式在[ASP.NET 核心模組](xref:host-and-deploy/aspnet-core-module)後方執行[跨進程](xref:host-and-deploy/iis/index#out-of-process-hosting-model)時，會停用 Kestrel 的要求主體大小限制。 IIS 已設定限制。

### <a name="minimum-request-body-data-rate"></a>要求主體資料速率下限

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate><br>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

如果資料是以指定的速率 (位元組/秒) 傳入，Kestrel 會每秒檢查一次。 如果速率降到低於最小值，則連接會超時。寬限期是 Kestrel 允許用戶端增加其傳送速率的時間量（最小值）。 在這段時間內不會檢查速率。 寬限期有助於避免因為 TCP 速度太慢而中斷一開始傳送資料的速度變慢的連接。

預設的最小速率是每秒240個位元組，並有5秒的寬限期。

速率下限也適用於回應。 除了屬性中具有 `RequestBody` 或 `Response` 以及介面名稱之外，用來設定要求限制和回應限制的程式碼都相同。

以下範例示範如何設定中的最小資料速率 `Program.cs` ：

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

覆寫中介軟體中每個要求的最小速率限制：

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>先前範例中所參考的不存在 <xref:Microsoft.AspNetCore.Http.HttpContext.Features?displayProperty=nameWithType> 于 HTTP/2 要求中。 因為通訊協定對要求多工的支援，所以 HTTP/2 通常不支援修改依要求的速率限制。 不過，<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> 仍存在 HTTP/2 要求的 `HttpContext.Features`您仍能透過將 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature.MinDataRate?displayProperty=nameWithType> 設定為 `null` (即使是針對 HTTP/2 要求)，以個別要求基礎來「完全停用」讀取素率限制。 嘗試讀取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或嘗試將它設定為 `null` 以外的值將會導致擲回 <xref:System.NotSupportedException> (假設要求是 HTTP/2 要求)。

透過 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.Limits?displayProperty=nameWithType> 設定的全伺服器速率限制皆仍套用至 HTTP/1.x 及 HTTP/2 連線。

### <a name="request-headers-timeout"></a>要求標頭逾時

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

取得或設定伺服器花費在接收要求標頭的時間上限。 預設為 30 秒。

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>HTTP/2 限制

此區段中的限制設定為 on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.Http2?displayProperty=nameWithType> 。

### <a name="maximum-streams-per-connection"></a>每個連線的資料流數目上限

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>

限制每個 HTTP/2 連接的並行要求資料流程數目。 超出的資料流會被拒絕。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

預設值是 100。

### <a name="header-table-size"></a>標頭表格大小

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.HeaderTableSize>

HPACK 解碼器可解壓縮 HTTP/2 連線的 HTTP 標頭。 `HeaderTableSize` 會限制 HPACK 解碼器所使用的標頭壓縮表格大小。 這個值是以八位元提供，而且必須大於零 (0)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

預設值為 4096。

### <a name="maximum-frame-size"></a>框架大小上限

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxFrameSize>

指出伺服器接收或傳送之 HTTP/2 連接框架承載的允許大小上限。 這個值是以八位元提供，而且必須介於 2^14 (16,384) 到 2^24-1 (16,777,215) 之間。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

預設值為 2^14 (16,384)。

### <a name="maximum-request-header-size"></a>要求標頭大小上限

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxRequestHeaderFieldSize>

指出要求標頭值的最大允許大小（以八位）。 這項限制適用于其壓縮和未壓縮標記法中的名稱和值。 此值必須大於零 (0)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

預設值為 8,192。

### <a name="initial-connection-window-size"></a>初始連線視窗大小

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialConnectionWindowSize>

以位元組為單位，表示伺服器在所有要求之間匯總的最大要求主體資料（以位元組為單位），每個連接)  (資料流程。 要求也皆受 `Http2.InitialStreamWindowSize` 所限制。 此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

預設值為 128 KB (131,072)。

### <a name="initial-stream-window-size"></a>初始資料流視窗大小

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.InitialStreamWindowSize>

以位元組為單位，表示每個要求 (串流) 的最大要求主體資料（以位元組為單位）。 要求也受限於 [`InitialConnectionWindowSize`](#initial-connection-window-size) 。 此值必須大於或等於 65,535，且小於 2^31 (2,147,483,648)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

預設值為 96 KB (98,304)。

### <a name="http2-keep-alive-ping-configuration"></a>HTTP/2 保持運作 ping 設定

Kestrel 可以設定為將 HTTP/2 ping 傳送至連線的用戶端。 HTTP/2 ping 的服務有多個用途：

* 保持閒置連接保持運作。 某些用戶端和 proxy 伺服器會關閉閒置的連線。 HTTP/2 ping 會視為連線上的活動，並防止連接關閉為閒置。
* 關閉狀況不良的連接。 在設定的時間內，用戶端沒有回應「保持運作」偵測的連接會由伺服器關閉。

有兩個與 HTTP/2 保持運作 ping 相關的設定選項：

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingDelay> 是設定 <xref:System.TimeSpan> ping 間隔的。 如果伺服器在這段時間內未收到任何框架，則伺服器會將保持運作的偵測傳送至用戶端。 當此選項設定為時，會停用 [保持作用中的 ping] <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> 。 預設值是 <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType>。
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.KeepAlivePingTimeout> 是 <xref:System.TimeSpan> 設定 ping timeout 的。 如果伺服器未在此時間內接收任何框架，例如回應偵測，則連接會關閉。 當此選項設定為時，會停用 [保持作用中] 超時 <xref:System.TimeSpan.MaxValue?displayProperty=nameWithType> 。 預設值為 20 秒。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingDelay = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>其他選項

### <a name="synchronous-io"></a>同步 I/O

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制要求和回應是否允許同步 i/o。 預設值是 `false`。

> [!WARNING]
> 大量封鎖同步 i/o 作業可能會導致執行緒集區耗盡，而使應用程式無回應。 只有 `AllowSynchronousIO` 在使用不支援非同步 i/o 的程式庫時才啟用。

下列範例會啟用同步 i/o：

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

如需其他 Kestrel 選項和限制的資訊，請參閱：

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
