---
title: ASP.NET Core Blazor 裝載模型設定
author: guardrex
description: 深入瞭解 ASP.NET Core 裝載模型設定的其他案例 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b32710e515d111b7dd6556f1db55082cd56a82b5
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818998"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="c610b-103">ASP.NET Core Blazor 裝載模型設定</span><span class="sxs-lookup"><span data-stu-id="c610b-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="c610b-104">By [Daniel Roth](https://github.com/danroth27)、 [Mackinnon Buck](https://github.com/MackinnonBuck)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c610b-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c610b-105">本文涵蓋主控模型設定。</span><span class="sxs-lookup"><span data-stu-id="c610b-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="c610b-106">SignalR用於驗證的跨原始來源協調</span><span class="sxs-lookup"><span data-stu-id="c610b-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="c610b-107">*本節適用于 Blazor WebAssembly 。*</span><span class="sxs-lookup"><span data-stu-id="c610b-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="c610b-108">若要設定 SignalR 的基礎用戶端來傳送認證，例如 cookie 或 HTTP 驗證標頭：</span><span class="sxs-lookup"><span data-stu-id="c610b-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="c610b-109">用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 來設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> 跨原始來源 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求：</span><span class="sxs-lookup"><span data-stu-id="c610b-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="c610b-110">將指派給 <xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 選項：</span><span class="sxs-lookup"><span data-stu-id="c610b-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="c610b-111">如需詳細資訊，請參閱<xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="c610b-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="c610b-112">反映 UI 中的連接狀態</span><span class="sxs-lookup"><span data-stu-id="c610b-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="c610b-113">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="c610b-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c610b-114">當用戶端偵測到連線已遺失時，會在用戶端嘗試重新連線時，向使用者顯示預設的 UI。</span><span class="sxs-lookup"><span data-stu-id="c610b-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="c610b-115">如果重新連線失敗，則會提供使用者重試的選項。</span><span class="sxs-lookup"><span data-stu-id="c610b-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="c610b-116">若要自訂 UI，請 `id` 在頁面的中定義具有之的元素 `components-reconnect-modal` `<body>` `_Host.cshtml` Razor ：</span><span class="sxs-lookup"><span data-stu-id="c610b-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="c610b-117">將下列內容新增至應用程式的樣式表單 (`wwwroot/css/app.css` 或 `wwwroot/css/site.css`) ：</span><span class="sxs-lookup"><span data-stu-id="c610b-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="c610b-118">下表描述套用至元素的 CSS 類別 `components-reconnect-modal` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="c610b-119">CSS 類別</span><span class="sxs-lookup"><span data-stu-id="c610b-119">CSS class</span></span>                       | <span data-ttu-id="c610b-120">表示&hellip;</span><span class="sxs-lookup"><span data-stu-id="c610b-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="c610b-121">遺失的連接。</span><span class="sxs-lookup"><span data-stu-id="c610b-121">A lost connection.</span></span> <span data-ttu-id="c610b-122">用戶端正在嘗試重新連線。</span><span class="sxs-lookup"><span data-stu-id="c610b-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="c610b-123">顯示強制回應。</span><span class="sxs-lookup"><span data-stu-id="c610b-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="c610b-124">系統會將使用中的連接重新建立至伺服器。</span><span class="sxs-lookup"><span data-stu-id="c610b-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="c610b-125">隱藏強制回應。</span><span class="sxs-lookup"><span data-stu-id="c610b-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="c610b-126">重新連接失敗，可能是因為網路失敗。</span><span class="sxs-lookup"><span data-stu-id="c610b-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="c610b-127">若要嘗試重新連接，請呼叫 `window.Blazor.reconnect()` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="c610b-128">已拒絕重新連接。</span><span class="sxs-lookup"><span data-stu-id="c610b-128">Reconnection rejected.</span></span> <span data-ttu-id="c610b-129">已達到伺服器但拒絕連線，而且伺服器上的使用者狀態遺失。</span><span class="sxs-lookup"><span data-stu-id="c610b-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="c610b-130">若要重載應用程式，請呼叫 `location.reload()` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="c610b-131">當下列情況發生時，可能會產生此連接狀態：</span><span class="sxs-lookup"><span data-stu-id="c610b-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="c610b-132">伺服器端線路發生損毀。</span><span class="sxs-lookup"><span data-stu-id="c610b-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="c610b-133">用戶端中斷連線的時間夠長，伺服器才會捨棄使用者的狀態。</span><span class="sxs-lookup"><span data-stu-id="c610b-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="c610b-134">系統會處置與使用者互動之元件的實例。</span><span class="sxs-lookup"><span data-stu-id="c610b-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="c610b-135">伺服器會重新開機，或回收應用程式的背景工作進程。</span><span class="sxs-lookup"><span data-stu-id="c610b-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="c610b-136">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="c610b-136">Render mode</span></span>

<span data-ttu-id="c610b-137">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="c610b-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c610b-138">Blazor Server在建立伺服器的用戶端連接之前，預設會將應用程式設定為在伺服器上預先呈現該 UI。</span><span class="sxs-lookup"><span data-stu-id="c610b-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="c610b-139">這會在頁面中設定 `_Host.cshtml` Razor ：</span><span class="sxs-lookup"><span data-stu-id="c610b-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="c610b-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>設定元件是否：</span><span class="sxs-lookup"><span data-stu-id="c610b-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="c610b-141">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="c610b-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="c610b-142">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="c610b-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="c610b-143">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="c610b-143">Render mode</span></span> | <span data-ttu-id="c610b-144">描述</span><span class="sxs-lookup"><span data-stu-id="c610b-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="c610b-145">將元件轉譯為靜態 HTML，並包含 Blazor Server 應用程式的標記。</span><span class="sxs-lookup"><span data-stu-id="c610b-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c610b-146">當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="c610b-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="c610b-147">呈現應用程式的標記 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="c610b-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c610b-148">不包含來自元件的輸出。</span><span class="sxs-lookup"><span data-stu-id="c610b-148">Output from the component isn't included.</span></span> <span data-ttu-id="c610b-149">當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="c610b-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="c610b-150">將元件轉譯為靜態 HTML。</span><span class="sxs-lookup"><span data-stu-id="c610b-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="c610b-151">不支援從靜態 HTML 網頁轉譯伺服器元件。</span><span class="sxs-lookup"><span data-stu-id="c610b-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="c610b-152">設定 SignalR 應用程式的用戶端 Blazor Server</span><span class="sxs-lookup"><span data-stu-id="c610b-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="c610b-153">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="c610b-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c610b-154">設定 SignalR 應用程式在檔案中使用的用戶端 Blazor Server `Pages/_Host.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="c610b-155">將呼叫的腳本放在 `Blazor.start` `_framework/blazor.server.js` 腳本之後和標記內 `</body>` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="c610b-156">記錄</span><span class="sxs-lookup"><span data-stu-id="c610b-156">Logging</span></span>

<span data-ttu-id="c610b-157">若要設定 SignalR 用戶端記錄：</span><span class="sxs-lookup"><span data-stu-id="c610b-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="c610b-158">將 `autostart="false"` 屬性加入至 `<script>` 腳本的標記 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c610b-159">傳入設定物件 (在 `configureSignalR` 用戶端產生器 `configureLogging` 上通話記錄層級的) 。</span><span class="sxs-lookup"><span data-stu-id="c610b-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
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

<span data-ttu-id="c610b-160">在上述範例中， `information` 相當於的記錄層級 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="c610b-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="c610b-161">修改重新連接處理常式</span><span class="sxs-lookup"><span data-stu-id="c610b-161">Modify the reconnection handler</span></span>

<span data-ttu-id="c610b-162">重新連線處理常式的線路連接事件可以針對自訂行為進行修改，例如：</span><span class="sxs-lookup"><span data-stu-id="c610b-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="c610b-163">表示在連接中斷時通知使用者。</span><span class="sxs-lookup"><span data-stu-id="c610b-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="c610b-164">當線路連線時，從用戶端) 執行記錄 (。</span><span class="sxs-lookup"><span data-stu-id="c610b-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="c610b-165">若要修改連接事件：</span><span class="sxs-lookup"><span data-stu-id="c610b-165">To modify the connection events:</span></span>

* <span data-ttu-id="c610b-166">將 `autostart="false"` 屬性加入至 `<script>` 腳本的標記 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c610b-167">針對已卸載連接的連線變更註冊回呼， (`onConnectionDown`) ，並 () 建立/重新建立的連接 `onConnectionUp` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="c610b-168">**兩者** `onConnectionDown``onConnectionUp`必須指定和。</span><span class="sxs-lookup"><span data-stu-id="c610b-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="c610b-169">調整重新連接重試計數和間隔</span><span class="sxs-lookup"><span data-stu-id="c610b-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="c610b-170">若要調整重新連接重試計數和間隔：</span><span class="sxs-lookup"><span data-stu-id="c610b-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="c610b-171">將 `autostart="false"` 屬性加入至 `<script>` 腳本的標記 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c610b-172">設定 `maxRetries` 每次重試嘗試 () 的重試次數 () 和允許的期間（以毫秒為單位） `retryIntervalMilliseconds` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="c610b-173">隱藏或取代重新連接顯示</span><span class="sxs-lookup"><span data-stu-id="c610b-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="c610b-174">若要隱藏重新連接顯示：</span><span class="sxs-lookup"><span data-stu-id="c610b-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="c610b-175">將 `autostart="false"` 屬性加入至 `<script>` 腳本的標記 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c610b-176">將重新連接處理常式的 `_reconnectionDisplay` (或) 設定為空的物件 `{}` `new Object()` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="c610b-177">若要取代重新連接顯示，請將 `_reconnectionDisplay` 前述範例中的設定為顯示的元素：</span><span class="sxs-lookup"><span data-stu-id="c610b-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="c610b-178">預留位置 `{ELEMENT ID}` 是要顯示之 HTML 專案的識別碼。</span><span class="sxs-lookup"><span data-stu-id="c610b-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="c610b-179">影響 HTML `<head>` 標記元素</span><span class="sxs-lookup"><span data-stu-id="c610b-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="c610b-180">*本節適用于 Blazor WebAssembly 和 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="c610b-180">*This section applies to Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="c610b-181">轉譯時，、 `Title` `Link` 和元件會 `Meta` 在 HTML 標籤元素中新增或更新資料 `<head>` ：</span><span class="sxs-lookup"><span data-stu-id="c610b-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="c610b-182">在上述範例中，、和的預留位置為 `{TITLE}` `{URL}` `{DESCRIPTION}` 字串值、 Razor 變數或 Razor 運算式。</span><span class="sxs-lookup"><span data-stu-id="c610b-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="c610b-183">適用下列特性：</span><span class="sxs-lookup"><span data-stu-id="c610b-183">The following characteristics apply:</span></span>

* <span data-ttu-id="c610b-184">支援伺服器端預先呈現。</span><span class="sxs-lookup"><span data-stu-id="c610b-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="c610b-185">`Value`參數是元件唯一有效的參數 `Title` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="c610b-186">提供給和元件的 HTML 屬性 `Meta` `Link` 會在[其他屬性](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters)中捕捉，並傳遞至轉譯的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="c610b-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="c610b-187">若為多個 `Title` 元件，頁面的標題會反映 `Value` 最後轉譯之 `Title` 元件的。</span><span class="sxs-lookup"><span data-stu-id="c610b-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="c610b-188">如果 `Meta` 包含相同屬性的多個或 `Link` 元件，則每個或元件只會呈現一個 HTML 標籤 `Meta` `Link` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="c610b-189">兩個 `Meta` 或 `Link` 元件不能參考相同的呈現 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="c610b-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="c610b-190">對現有或元件的參數所做的變更 `Meta` `Link` 會反映在其轉譯的 HTML 標籤中。</span><span class="sxs-lookup"><span data-stu-id="c610b-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="c610b-191">當 `Link` 或 `Meta` 元件不再呈現，因而由架構處置時，會移除其呈現的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="c610b-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="c610b-192">在子元件中使用其中一個 framework 元件時，只要呈現包含架構元件的子元件，轉譯的 HTML 標籤就會影響父元件的任何其他子元件。</span><span class="sxs-lookup"><span data-stu-id="c610b-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="c610b-193">在子元件中使用其中一個架構元件，並將 HTML 標籤放在或中的差異， `wwwroot/index.html` 在於 `Pages/_Host.cshtml` 架構元件的轉譯 HTML 標籤：</span><span class="sxs-lookup"><span data-stu-id="c610b-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="c610b-194">可由應用程式狀態修改。</span><span class="sxs-lookup"><span data-stu-id="c610b-194">Can be modified by application state.</span></span> <span data-ttu-id="c610b-195">應用程式狀態無法修改硬式編碼的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="c610b-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="c610b-196">當父元件不再呈現時，會從 HTML 中移除 `<head>` 。</span><span class="sxs-lookup"><span data-stu-id="c610b-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c610b-197">其他資源</span><span class="sxs-lookup"><span data-stu-id="c610b-197">Additional resources</span></span>

* <xref:fundamentals/logging/index>
