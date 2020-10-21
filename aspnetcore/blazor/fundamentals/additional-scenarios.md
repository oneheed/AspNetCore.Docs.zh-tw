---
title: ASP.NET Core Blazor 裝載模型設定
author: guardrex
description: 瞭解 ASP.NET Core 裝載模型設定的其他案例 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 075bcc68fd2dff0ebf2cfceacec24fde8c818603
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326532"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="ee4db-103">ASP.NET Core Blazor 裝載模型設定</span><span class="sxs-lookup"><span data-stu-id="ee4db-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="ee4db-104">[Daniel Roth](https://github.com/danroth27)、 [Mackinnon Buck](https://github.com/MackinnonBuck)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ee4db-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ee4db-105">本文涵蓋裝載模型設定。</span><span class="sxs-lookup"><span data-stu-id="ee4db-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="ee4db-106">SignalR 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="ee4db-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="ee4db-107">*本節適用于 Blazor WebAssembly 。*</span><span class="sxs-lookup"><span data-stu-id="ee4db-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="ee4db-108">設定 SignalR 的基礎用戶端以傳送認證，例如 cookie s 或 HTTP 驗證標頭：</span><span class="sxs-lookup"><span data-stu-id="ee4db-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="ee4db-109">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> 跨原始來源的 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求：</span><span class="sxs-lookup"><span data-stu-id="ee4db-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="ee4db-110">將指派給 <xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 選項：</span><span class="sxs-lookup"><span data-stu-id="ee4db-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="ee4db-111">如需詳細資訊，請參閱<xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="ee4db-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="ee4db-112">反映 UI 中的連接狀態</span><span class="sxs-lookup"><span data-stu-id="ee4db-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="ee4db-113">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ee4db-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ee4db-114">當用戶端偵測到連線已遺失時，當用戶端嘗試重新連接時，會對使用者顯示預設的 UI。</span><span class="sxs-lookup"><span data-stu-id="ee4db-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ee4db-115">如果重新連接失敗，就會提供使用者重試的選項。</span><span class="sxs-lookup"><span data-stu-id="ee4db-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="ee4db-116">若要自訂 UI，請在頁面的中，使用的來定義元素 `id` `components-reconnect-modal` `<body>` `_Host.cshtml` Razor ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="ee4db-117">將下列內容新增至應用程式的樣式表單 (`wwwroot/css/app.css` 或 `wwwroot/css/site.css`) ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="ee4db-118">下表描述套用至元素的 CSS 類別 `components-reconnect-modal` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="ee4db-119">CSS 類別</span><span class="sxs-lookup"><span data-stu-id="ee4db-119">CSS class</span></span>                       | <span data-ttu-id="ee4db-120">表示&hellip;</span><span class="sxs-lookup"><span data-stu-id="ee4db-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="ee4db-121">遺失的連接。</span><span class="sxs-lookup"><span data-stu-id="ee4db-121">A lost connection.</span></span> <span data-ttu-id="ee4db-122">用戶端正在嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="ee4db-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="ee4db-123">顯示強制回應。</span><span class="sxs-lookup"><span data-stu-id="ee4db-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="ee4db-124">系統會重新建立與伺服器之間的使用中連接。</span><span class="sxs-lookup"><span data-stu-id="ee4db-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="ee4db-125">隱形模式。</span><span class="sxs-lookup"><span data-stu-id="ee4db-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="ee4db-126">重新連接失敗，可能是因為網路失敗。</span><span class="sxs-lookup"><span data-stu-id="ee4db-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="ee4db-127">若要嘗試重新連接，請呼叫 `window.Blazor.reconnect()` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="ee4db-128">重新連線遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="ee4db-128">Reconnection rejected.</span></span> <span data-ttu-id="ee4db-129">伺服器已達到但拒絕連線，而且伺服器上的使用者狀態遺失。</span><span class="sxs-lookup"><span data-stu-id="ee4db-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="ee4db-130">若要重載應用程式，請呼叫 `location.reload()` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="ee4db-131">此連接狀態可能會在下列情況下產生：</span><span class="sxs-lookup"><span data-stu-id="ee4db-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="ee4db-132">伺服器端線路發生損毀。</span><span class="sxs-lookup"><span data-stu-id="ee4db-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="ee4db-133">用戶端已中斷連線到足夠的時間，讓伺服器卸載使用者的狀態。</span><span class="sxs-lookup"><span data-stu-id="ee4db-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="ee4db-134">系統會處置使用者進行互動的元件實例。</span><span class="sxs-lookup"><span data-stu-id="ee4db-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="ee4db-135">伺服器會重新開機，或回收應用程式的背景工作進程。</span><span class="sxs-lookup"><span data-stu-id="ee4db-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="ee4db-136">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="ee4db-136">Render mode</span></span>

<span data-ttu-id="ee4db-137">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ee4db-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ee4db-138">Blazor Server 根據預設，應用程式會在伺服器上建立用戶端連接之前，先將伺服器上的 UI 設定為預先呈現。</span><span class="sxs-lookup"><span data-stu-id="ee4db-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ee4db-139">這會在頁面中設定 `_Host.cshtml` Razor ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="ee4db-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 設定元件是否為：</span><span class="sxs-lookup"><span data-stu-id="ee4db-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="ee4db-141">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="ee4db-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="ee4db-142">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="ee4db-143">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="ee4db-143">Render mode</span></span> | <span data-ttu-id="ee4db-144">描述</span><span class="sxs-lookup"><span data-stu-id="ee4db-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ee4db-145">將元件轉譯為靜態 HTML，並包含 Blazor Server 應用程式的標記。</span><span class="sxs-lookup"><span data-stu-id="ee4db-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ee4db-146">當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ee4db-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ee4db-147">轉譯應用程式的標記 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ee4db-148">不包含元件的輸出。</span><span class="sxs-lookup"><span data-stu-id="ee4db-148">Output from the component isn't included.</span></span> <span data-ttu-id="ee4db-149">當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ee4db-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ee4db-150">將元件轉譯為靜態 HTML。</span><span class="sxs-lookup"><span data-stu-id="ee4db-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="ee4db-151">不支援從靜態 HTML 網頁轉譯伺服器元件。</span><span class="sxs-lookup"><span data-stu-id="ee4db-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="ee4db-152">初始化 Blazor 線路</span><span class="sxs-lookup"><span data-stu-id="ee4db-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="ee4db-153">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ee4db-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ee4db-154">Blazor Server在檔案中設定應用程式[ SignalR 線路](xref:blazor/hosting-models#circuits)的手動啟動 `Pages/_Host.cshtml` ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="ee4db-155">將 `autostart="false"` 屬性新增至 `<script>` 腳本的標記 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ee4db-156">將在腳本標記之後呼叫的腳本放在 `Blazor.start` `blazor.server.js` 結尾 `</body>` 標記內。</span><span class="sxs-lookup"><span data-stu-id="ee4db-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="ee4db-157">`autostart`停用時，不相依于電路的應用程式任何層面都能正常運作。</span><span class="sxs-lookup"><span data-stu-id="ee4db-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="ee4db-158">例如，用戶端路由可運作。</span><span class="sxs-lookup"><span data-stu-id="ee4db-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="ee4db-159">不過，與電路相依的任何層面在 `Blazor.start` 呼叫之前都不會運作。</span><span class="sxs-lookup"><span data-stu-id="ee4db-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="ee4db-160">沒有已建立的線路，應用程式行為無法預期。</span><span class="sxs-lookup"><span data-stu-id="ee4db-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="ee4db-161">例如，當線路中斷連線時，元件方法無法執行。</span><span class="sxs-lookup"><span data-stu-id="ee4db-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="ee4db-162">在 Blazor 檔就緒時初始化</span><span class="sxs-lookup"><span data-stu-id="ee4db-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="ee4db-163">Blazor當檔準備就緒時，將應用程式初始化：</span><span class="sxs-lookup"><span data-stu-id="ee4db-163">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="ee4db-164">`Promise`從手動開始到該結果的鏈</span><span class="sxs-lookup"><span data-stu-id="ee4db-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="ee4db-165">若要執行其他工作（例如 JS interop 初始化），請使用 `then` 來連結至 `Promise` 手動 Blazor 應用程式啟動的結果：</span><span class="sxs-lookup"><span data-stu-id="ee4db-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="ee4db-166">設定 SignalR 用戶端</span><span class="sxs-lookup"><span data-stu-id="ee4db-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="ee4db-167">記錄</span><span class="sxs-lookup"><span data-stu-id="ee4db-167">Logging</span></span>

<span data-ttu-id="ee4db-168">若要設定 SignalR 用戶端記錄，請 `configureSignalR` `configureLogging` 使用用戶端產生器上的記錄層級 () 傳入設定物件：</span><span class="sxs-lookup"><span data-stu-id="ee4db-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="ee4db-169">在上述範例中， `information` 相當於的記錄層級 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="ee4db-170">修改重新連接處理常式</span><span class="sxs-lookup"><span data-stu-id="ee4db-170">Modify the reconnection handler</span></span>

<span data-ttu-id="ee4db-171">您可以針對自訂行為修改重新連接處理常式的線路線上活動，例如：</span><span class="sxs-lookup"><span data-stu-id="ee4db-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="ee4db-172">以在中斷連接時通知使用者。</span><span class="sxs-lookup"><span data-stu-id="ee4db-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="ee4db-173">若要從用戶端執行記錄 (線上路連線時) 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="ee4db-174">若要修改連接事件，請註冊下列連線變更的回呼：</span><span class="sxs-lookup"><span data-stu-id="ee4db-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="ee4db-175">中斷的連接使用 `onConnectionDown` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="ee4db-176">已建立/重新建立的連接使用 `onConnectionUp` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="ee4db-177">**兩者** `onConnectionDown``onConnectionUp`必須指定和：</span><span class="sxs-lookup"><span data-stu-id="ee4db-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="ee4db-178">調整重新連接重試計數和間隔</span><span class="sxs-lookup"><span data-stu-id="ee4db-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="ee4db-179">若要調整重新連接重試計數和間隔，請設定重試次數 (`maxRetries`) 和每次重試嘗試所允許的期間（以毫秒為單位） (`retryIntervalMilliseconds`) ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="ee4db-180">隱藏或取代重新連接顯示</span><span class="sxs-lookup"><span data-stu-id="ee4db-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="ee4db-181">若要隱藏重新連接顯示，請將重新連接處理常式設定 `_reconnectionDisplay` 為空白物件 (`{}` 或 `new Object()`) ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="ee4db-182">若要取代重新連接顯示，請 `_reconnectionDisplay` 在上述範例中設定為要顯示的元素：</span><span class="sxs-lookup"><span data-stu-id="ee4db-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="ee4db-183">預留位置 `{ELEMENT ID}` 是要顯示之 HTML 元素的識別碼。</span><span class="sxs-lookup"><span data-stu-id="ee4db-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ee4db-184">藉由在 `transition-delay` 應用程式的 CSS 中設定屬性（property），藉由在應用程式的)  (CSS 中設定屬性（property），藉以自訂延遲顯示之前 `wwwroot/css/site.css`</span><span class="sxs-lookup"><span data-stu-id="ee4db-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="ee4db-185">下列範例會將500毫秒 (預設) 的轉換延遲設定為1000毫秒 (1 秒) ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="ee4db-186">中斷與 Blazor 用戶端的線路連線</span><span class="sxs-lookup"><span data-stu-id="ee4db-186">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="ee4db-187">依預設， Blazor 當觸發[ `unload` 頁面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)時，電路會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="ee4db-187">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="ee4db-188">若要中斷用戶端上其他案例的線路連接，請 `Blazor.disconnect` 在適當的事件處理常式中叫用。</span><span class="sxs-lookup"><span data-stu-id="ee4db-188">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="ee4db-189">在下列範例中，當 ([ `pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) 隱藏頁面時，電路會中斷連線：</span><span class="sxs-lookup"><span data-stu-id="ee4db-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="ee4db-190">影響 HTML `<head>` 標記元素</span><span class="sxs-lookup"><span data-stu-id="ee4db-190">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="ee4db-191">*本節適用于和的即將推出的 ASP.NET Core 5.0 Blazor WebAssembly 版本 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ee4db-191">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="ee4db-192">轉譯時， `Title` 、 `Link` 和元件會 `Meta` 新增或更新 HTML 標籤元素中的資料 `<head>` ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-192">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="ee4db-193">在上述範例中， `{TITLE}` 、和的預留位置 `{URL}` `{DESCRIPTION}` 是字串值、 Razor 變數或 Razor 運算式。</span><span class="sxs-lookup"><span data-stu-id="ee4db-193">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="ee4db-194">適用下列特性：</span><span class="sxs-lookup"><span data-stu-id="ee4db-194">The following characteristics apply:</span></span>

* <span data-ttu-id="ee4db-195">支援伺服器端預先呈現。</span><span class="sxs-lookup"><span data-stu-id="ee4db-195">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="ee4db-196">`Value`參數是唯一有效的 `Title` 元件參數。</span><span class="sxs-lookup"><span data-stu-id="ee4db-196">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="ee4db-197">提供給和元件的 HTML 屬性 `Meta` `Link` 會在 [其他屬性](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) 中捕捉，並傳遞至轉譯的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="ee4db-197">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="ee4db-198">針對多個 `Title` 元件，頁面的標題會反映最後轉譯的 `Value` `Title` 元件的。</span><span class="sxs-lookup"><span data-stu-id="ee4db-198">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="ee4db-199">如果有多個 `Meta` 或 `Link` 元件包含在相同的屬性中，每個或元件只會轉譯一個 HTML 標籤 `Meta` `Link` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-199">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="ee4db-200">兩個 `Meta` 或 `Link` 元件無法參考相同的呈現 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="ee4db-200">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="ee4db-201">現有或元件的參數變更 `Meta` `Link` 會反映在其呈現的 HTML 標籤中。</span><span class="sxs-lookup"><span data-stu-id="ee4db-201">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="ee4db-202">當 `Link` 或 `Meta` 元件不再呈現，且由架構處置時，會移除其轉譯的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="ee4db-202">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="ee4db-203">在子元件中使用其中一個架構元件時，只要轉譯包含 framework 元件的子元件，轉譯的 HTML 標籤就會影響父元件的任何其他子元件。</span><span class="sxs-lookup"><span data-stu-id="ee4db-203">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="ee4db-204">在子元件中使用其中一個架構元件，並在或中放置 HTML 標籤的差異在於 `wwwroot/index.html` `Pages/_Host.cshtml` 架構元件的轉譯 html 標記：</span><span class="sxs-lookup"><span data-stu-id="ee4db-204">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="ee4db-205">可依應用程式狀態修改。</span><span class="sxs-lookup"><span data-stu-id="ee4db-205">Can be modified by application state.</span></span> <span data-ttu-id="ee4db-206">應用程式狀態無法修改硬式編碼的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="ee4db-206">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="ee4db-207">當父元件不再呈現時，會從 HTML 中移除 `<head>` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-207">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="ee4db-208">靜態檔案</span><span class="sxs-lookup"><span data-stu-id="ee4db-208">Static files</span></span>

<span data-ttu-id="ee4db-209">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ee4db-209">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ee4db-210">若要使用或其他設定來建立其他檔案對應 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> ，請使用下列 **其中一** 種方法。</span><span class="sxs-lookup"><span data-stu-id="ee4db-210">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="ee4db-211">在下列範例中， `{EXTENSION}` 預留位置是副檔名，而 `{CONTENT TYPE}` 預留位置是內容類型。</span><span class="sxs-lookup"><span data-stu-id="ee4db-211">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="ee4db-212">使用下列方法，透過 () 的相依性 [插入 (DI) ](xref:blazor/fundamentals/dependency-injection) 設定選項 `Startup.ConfigureServices` `Startup.cs` <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-212">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="ee4db-213">因為此方法會設定用來提供服務的相同檔案提供者 `blazor.server.js` ，請確定您的自訂設定不會干擾服務 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-213">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="ee4db-214">例如，請不要藉由設定提供者來移除 JavaScript 檔案的對應 `provider.Mappings.Remove(".js")` 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-214">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="ee4db-215"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>在 () 中使用兩個呼叫 `Startup.Configure` `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="ee4db-215">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="ee4db-216">在第一次呼叫時設定自訂檔案提供者 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-216">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="ee4db-217">第二個中間件會 `blazor.server.js` 提供，其使用架構所提供的預設靜態檔案設定 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="ee4db-217">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="ee4db-218">您可以 `_framework/blazor.server.js` 使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 來執行自訂靜態檔案中介軟體，以避免干擾服務：</span><span class="sxs-lookup"><span data-stu-id="ee4db-218">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="ee4db-219">其他資源</span><span class="sxs-lookup"><span data-stu-id="ee4db-219">Additional resources</span></span>

* <xref:fundamentals/logging/index>
