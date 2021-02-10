---
title: ASP.NET Core Blazor SignalR 指導方針
author: guardrex
description: 瞭解如何設定和管理 Blazor SignalR 連接。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/signalr
ms.openlocfilehash: 31cd68baa0fbc773f09fe3a1b37091b2dfc0b0a0
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100110009"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a>ASP.NET Core Blazor SignalR 指導方針

[Daniel Roth](https://github.com/danroth27)、 [Mackinnon Buck](https://github.com/MackinnonBuck)和[Luke Latham](https://github.com/guardrex)

如需 ASP.NET Core 設定的一般指引 SignalR ，請參閱檔區域中的主題 <xref:signalr/introduction> 。 若要設定 SignalR [加入至裝載的 Blazor WebAssembly 解決方案](xref:tutorials/signalr-blazor)，請參閱 <xref:signalr/configuration#configure-server-options> 。

## <a name="circuit-handler-options"></a>線路處理常式選項

*本節適用于 Blazor Server 。*

Blazor Server使用 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 下表所示的來設定電路。

| 選項 | 預設 | 描述 |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | 當線路上發生未處理的例外狀況，或透過 JS interop 的 .NET 方法調用導致例外狀況時，將詳細的例外狀況訊息傳送至 JavaScript。 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 100 | 指定伺服器一次保存在記憶體中的中斷連接電路數目上限。 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 3 分鐘 | 中斷連接的電路在中斷之前保留在記憶體中的最大時間量。 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 1 分鐘 | 在將非同步 JavaScript 函式呼叫計時之前，伺服器所等待的最大時間量。 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 10 | 伺服器在指定時間為每個迴圈保留記憶體中的未認可轉譯批次數目上限，以支援健全的重新連接。 達到此限制之後，伺服器會停止產生新的轉譯批次，直到用戶端認可一或多個批次為止。 |

在中設定選項 `Startup.ConfigureServices` 委派的選項 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 。 下列範例會指派上表所示的預設選項值：

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

若要設定 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> ，請使用 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> 。 如需選項描述，請參閱 <xref:signalr/configuration#configure-server-options> 。 下列範例會指派預設選項值：

```csharp
using System;

...

services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR 驗證的跨原始來源協商

*本節適用于 Blazor WebAssembly 。*

設定 SignalR 的基礎用戶端以傳送認證，例如 cookie s 或 HTTP 驗證標頭：

* 使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> 跨原始來源的 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求：

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* 將指派給 <xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 選項：

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

如需詳細資訊，請參閱<xref:signalr/configuration#configure-additional-options>。

## <a name="reflect-the-connection-state-in-the-ui"></a>反映 UI 中的連接狀態

*本節適用于 Blazor Server 。*

當用戶端偵測到連線已遺失時，當用戶端嘗試重新連接時，會對使用者顯示預設的 UI。 如果重新連接失敗，就會提供使用者重試的選項。

若要自訂 UI，請在頁面的中，使用的來定義元素 `id` `components-reconnect-modal` `<body>` `_Host.cshtml` Razor ：

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

將下列內容新增至應用程式的樣式表單 (`wwwroot/css/app.css` 或 `wwwroot/css/site.css`) ：

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

下表描述套用至元素的 CSS 類別 `components-reconnect-modal` 。

| CSS 類別                       | 表示&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 遺失的連接。 用戶端正在嘗試重新連接。 顯示強制回應。 |
| `components-reconnect-hide`     | 系統會重新建立與伺服器之間的使用中連接。 隱形模式。 |
| `components-reconnect-failed`   | 重新連接失敗，可能是因為網路失敗。 若要嘗試重新連接，請呼叫 `window.Blazor.reconnect()` 。 |
| `components-reconnect-rejected` | 重新連線遭到拒絕。 伺服器已達到但拒絕連線，而且伺服器上的使用者狀態遺失。 若要重載應用程式，請呼叫 `location.reload()` 。 此連接狀態可能會在下列情況下產生：<ul><li>伺服器端線路發生損毀。</li><li>用戶端已中斷連線到足夠的時間，讓伺服器卸載使用者的狀態。 系統會處置使用者進行互動的元件實例。</li><li>伺服器會重新開機，或回收應用程式的背景工作進程。</li></ul> |

## <a name="render-mode"></a>轉譯模式

::: moniker range=">= aspnetcore-5.0"

*本節適用于 hosted Blazor WebAssembly 和 Blazor Server 。*

Blazor 依預設，應用程式會設定為伺服器上的可呈現的 UI。 如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

*本節適用于 Blazor Server 。*

Blazor Server 根據預設，應用程式會在伺服器上建立用戶端連接之前，先將伺服器上的 UI 設定為預先呈現。 如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a>初始化 Blazor 線路

*本節適用于 Blazor Server 。*

Blazor Server在檔案中設定應用程式[ SignalR 線路](xref:blazor/hosting-models#circuits)的手動啟動 `Pages/_Host.cshtml` ：

* 將 `autostart="false"` 屬性新增至 `<script>` 腳本的標記 `blazor.server.js` 。
* 將在腳本標記之後呼叫的腳本放在 `Blazor.start` `blazor.server.js` 結尾 `</body>` 標記內。

`autostart`停用時，不相依于電路的應用程式任何層面都能正常運作。 例如，用戶端路由可運作。 不過，與電路相依的任何層面在 `Blazor.start` 呼叫之前都不會運作。 沒有已建立的線路，應用程式行為無法預期。 例如，當線路中斷連線時，元件方法無法執行。

### <a name="initialize-blazor-when-the-document-is-ready"></a>在 Blazor 檔就緒時初始化

Blazor當檔準備就緒時，將應用程式初始化：

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>`Promise`從手動開始到該結果的鏈

若要執行其他工作（例如 JS interop 初始化），請使用 `then` 來連結至 `Promise` 手動 Blazor 應用程式啟動的結果：

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-signalr-client"></a>設定 SignalR 用戶端

#### <a name="logging"></a>記錄

若要設定 SignalR 用戶端記錄，請 `configureSignalR` `configureLogging` 使用用戶端產生器上的記錄層級 () 傳入設定物件：

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

在上述範例中， `information` 相當於的記錄層級 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> 。

### <a name="modify-the-reconnection-handler"></a>修改重新連接處理常式

您可以針對自訂行為修改重新連接處理常式的線路線上活動，例如：

* 以在中斷連接時通知使用者。
* 若要從用戶端執行記錄 (線上路連線時) 。

若要修改連接事件，請註冊下列連線變更的回呼：

* 中斷的連接使用 `onConnectionDown` 。
* 已建立/重新建立的連接使用 `onConnectionUp` 。

**兩者** `onConnectionDown``onConnectionUp`必須指定和：

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>調整重新連接重試計數和間隔

若要調整重新連接重試計數和間隔，請設定重試次數 (`maxRetries`) 和每次重試嘗試所允許的期間（以毫秒為單位） (`retryIntervalMilliseconds`) ：

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a>隱藏或取代重新連接顯示

若要隱藏重新連接顯示，請將重新連接處理常式設定 `_reconnectionDisplay` 為空白物件 (`{}` 或 `new Object()`) ：

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

若要取代重新連接顯示，請 `_reconnectionDisplay` 在上述範例中設定為要顯示的元素：

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

預留位置 `{ELEMENT ID}` 是要顯示之 HTML 元素的識別碼。

::: moniker range=">= aspnetcore-5.0"

藉由在 `transition-delay` 應用程式的 CSS 中設定屬性（property），藉由在應用程式的)  (CSS 中設定屬性（property），藉以自訂延遲顯示之前 `wwwroot/css/site.css` 下列範例會將500毫秒 (預設) 的轉換延遲設定為1000毫秒 (1 秒) ：

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a>中斷與 Blazor 用戶端的線路連線

依預設， Blazor 當觸發[ `unload` 頁面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)時，電路會中斷連線。 若要中斷用戶端上其他案例的線路連接，請 `Blazor.disconnect` 在適當的事件處理常式中叫用。 在下列範例中，當 ([ `pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) 隱藏頁面時，電路會中斷連線：

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a>其他資源

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [Blazor Server 重新連接事件和元件生命週期事件](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
