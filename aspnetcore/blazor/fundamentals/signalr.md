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
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279857"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="ea5ba-103">ASP.NET Core Blazor SignalR 指導方針</span><span class="sxs-lookup"><span data-stu-id="ea5ba-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="ea5ba-104">如需 ASP.NET Core 設定的一般指引 SignalR ，請參閱檔區域中的主題 <xref:signalr/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="ea5ba-105">若要設定 SignalR [加入至裝載的 Blazor WebAssembly 解決方案](xref:tutorials/signalr-blazor)，請參閱 <xref:signalr/configuration#configure-server-options> 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="ea5ba-106">線路處理常式選項</span><span class="sxs-lookup"><span data-stu-id="ea5ba-106">Circuit handler options</span></span>

<span data-ttu-id="ea5ba-107">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ea5ba-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ea5ba-108">Blazor Server使用 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 下表所示的來設定電路。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="ea5ba-109">選項</span><span class="sxs-lookup"><span data-stu-id="ea5ba-109">Option</span></span> | <span data-ttu-id="ea5ba-110">預設</span><span class="sxs-lookup"><span data-stu-id="ea5ba-110">Default</span></span> | <span data-ttu-id="ea5ba-111">描述</span><span class="sxs-lookup"><span data-stu-id="ea5ba-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="ea5ba-112">當線路上發生未處理的例外狀況，或透過 JS interop 的 .NET 方法調用導致例外狀況時，將詳細的例外狀況訊息傳送至 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="ea5ba-113">100</span><span class="sxs-lookup"><span data-stu-id="ea5ba-113">100</span></span> | <span data-ttu-id="ea5ba-114">指定伺服器一次保存在記憶體中的中斷連接電路數目上限。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="ea5ba-115">3 分鐘</span><span class="sxs-lookup"><span data-stu-id="ea5ba-115">3 minutes</span></span> | <span data-ttu-id="ea5ba-116">中斷連接的電路在中斷之前保留在記憶體中的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="ea5ba-117">1 分鐘</span><span class="sxs-lookup"><span data-stu-id="ea5ba-117">1 minute</span></span> | <span data-ttu-id="ea5ba-118">在將非同步 JavaScript 函式呼叫計時之前，伺服器所等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="ea5ba-119">10</span><span class="sxs-lookup"><span data-stu-id="ea5ba-119">10</span></span> | <span data-ttu-id="ea5ba-120">伺服器在指定時間為每個迴圈保留記憶體中的未認可轉譯批次數目上限，以支援健全的重新連接。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="ea5ba-121">達到此限制之後，伺服器會停止產生新的轉譯批次，直到用戶端認可一或多個批次為止。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="ea5ba-122">在中設定選項 `Startup.ConfigureServices` 委派的選項 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="ea5ba-123">下列範例會指派上表所示的預設選項值：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-123">The following example assigns the default option values shown in the preceding table:</span></span>

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

<span data-ttu-id="ea5ba-124">若要設定 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> ，請使用 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="ea5ba-125">如需選項描述，請參閱 <xref:signalr/configuration#configure-server-options> 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="ea5ba-126">下列範例會指派預設選項值：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-126">The following example assigns the default option values:</span></span>

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

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="ea5ba-127">SignalR 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="ea5ba-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="ea5ba-128">*本節適用于 Blazor WebAssembly 。*</span><span class="sxs-lookup"><span data-stu-id="ea5ba-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="ea5ba-129">設定 SignalR 的基礎用戶端以傳送認證，例如 cookie s 或 HTTP 驗證標頭：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="ea5ba-130">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> 跨原始來源的 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="ea5ba-131">將指派給 <xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 選項：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="ea5ba-132">如需詳細資訊，請參閱<xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="ea5ba-133">反映 UI 中的連接狀態</span><span class="sxs-lookup"><span data-stu-id="ea5ba-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="ea5ba-134">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ea5ba-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ea5ba-135">當用戶端偵測到連線已遺失時，當用戶端嘗試重新連接時，會對使用者顯示預設的 UI。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ea5ba-136">如果重新連接失敗，就會提供使用者重試的選項。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="ea5ba-137">若要自訂 UI，請在頁面的中，使用的來定義元素 `id` `components-reconnect-modal` `<body>` `_Host.cshtml` Razor ：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="ea5ba-138">將下列內容新增至應用程式的樣式表單 (`wwwroot/css/app.css` 或 `wwwroot/css/site.css`) ：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="ea5ba-139">下表描述套用至元素的 CSS 類別 `components-reconnect-modal` 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="ea5ba-140">CSS 類別</span><span class="sxs-lookup"><span data-stu-id="ea5ba-140">CSS class</span></span>                       | <span data-ttu-id="ea5ba-141">表示&hellip;</span><span class="sxs-lookup"><span data-stu-id="ea5ba-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="ea5ba-142">遺失的連接。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-142">A lost connection.</span></span> <span data-ttu-id="ea5ba-143">用戶端正在嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="ea5ba-144">顯示強制回應。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="ea5ba-145">系統會重新建立與伺服器之間的使用中連接。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="ea5ba-146">隱形模式。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="ea5ba-147">重新連接失敗，可能是因為網路失敗。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="ea5ba-148">若要嘗試重新連接，請呼叫 `window.Blazor.reconnect()` 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="ea5ba-149">重新連線遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-149">Reconnection rejected.</span></span> <span data-ttu-id="ea5ba-150">伺服器已達到但拒絕連線，而且伺服器上的使用者狀態遺失。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="ea5ba-151">若要重載應用程式，請呼叫 `location.reload()` 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="ea5ba-152">此連接狀態可能會在下列情況下產生：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="ea5ba-153">伺服器端線路發生損毀。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="ea5ba-154">用戶端已中斷連線到足夠的時間，讓伺服器卸載使用者的狀態。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="ea5ba-155">系統會處置使用者進行互動的元件實例。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="ea5ba-156">伺服器會重新開機，或回收應用程式的背景工作進程。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="ea5ba-157">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="ea5ba-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ea5ba-158">*本節適用于 hosted Blazor WebAssembly 和 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ea5ba-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="ea5ba-159">Blazor 依預設，應用程式會設定為伺服器上的可呈現的 UI。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="ea5ba-160">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ea5ba-161">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ea5ba-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ea5ba-162">Blazor Server 根據預設，應用程式會在伺服器上建立用戶端連接之前，先將伺服器上的 UI 設定為預先呈現。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ea5ba-163">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="ea5ba-164">初始化 Blazor 線路</span><span class="sxs-lookup"><span data-stu-id="ea5ba-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="ea5ba-165">*本節適用于 Blazor Server 。*</span><span class="sxs-lookup"><span data-stu-id="ea5ba-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ea5ba-166">Blazor Server在檔案中設定應用程式[ SignalR 線路](xref:blazor/hosting-models#circuits)的手動啟動 `Pages/_Host.cshtml` ：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="ea5ba-167">將 `autostart="false"` 屬性新增至 `<script>` 腳本的標記 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ea5ba-168">將在腳本標記之後呼叫的腳本放在 `Blazor.start` `blazor.server.js` 結尾 `</body>` 標記內。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="ea5ba-169">`autostart`停用時，不相依于電路的應用程式任何層面都能正常運作。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="ea5ba-170">例如，用戶端路由可運作。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="ea5ba-171">不過，與電路相依的任何層面在 `Blazor.start` 呼叫之前都不會運作。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="ea5ba-172">沒有已建立的線路，應用程式行為無法預期。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="ea5ba-173">例如，當線路中斷連線時，元件方法無法執行。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="ea5ba-174">在 Blazor 檔就緒時初始化</span><span class="sxs-lookup"><span data-stu-id="ea5ba-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="ea5ba-175">Blazor當檔準備就緒時，將應用程式初始化：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-175">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="ea5ba-176">`Promise`從手動開始到該結果的鏈</span><span class="sxs-lookup"><span data-stu-id="ea5ba-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="ea5ba-177">若要執行其他工作（例如 JS interop 初始化），請使用 `then` 來連結至 `Promise` 手動 Blazor 應用程式啟動的結果：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-signalr-client"></a><span data-ttu-id="ea5ba-178">設定 SignalR 用戶端</span><span class="sxs-lookup"><span data-stu-id="ea5ba-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="ea5ba-179">記錄</span><span class="sxs-lookup"><span data-stu-id="ea5ba-179">Logging</span></span>

<span data-ttu-id="ea5ba-180">若要設定 SignalR 用戶端記錄，請 `configureSignalR` `configureLogging` 使用用戶端產生器上的記錄層級 () 傳入設定物件：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="ea5ba-181">在上述範例中， `information` 相當於的記錄層級 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="ea5ba-182">修改重新連接處理常式</span><span class="sxs-lookup"><span data-stu-id="ea5ba-182">Modify the reconnection handler</span></span>

<span data-ttu-id="ea5ba-183">您可以針對自訂行為修改重新連接處理常式的線路線上活動，例如：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="ea5ba-184">以在中斷連接時通知使用者。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="ea5ba-185">若要從用戶端執行記錄 (線上路連線時) 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="ea5ba-186">若要修改連接事件，請註冊下列連線變更的回呼：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="ea5ba-187">中斷的連接使用 `onConnectionDown` 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="ea5ba-188">已建立/重新建立的連接使用 `onConnectionUp` 。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="ea5ba-189">**兩者** `onConnectionDown``onConnectionUp`必須指定和：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="ea5ba-190">調整重新連接重試計數和間隔</span><span class="sxs-lookup"><span data-stu-id="ea5ba-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="ea5ba-191">若要調整重新連接重試計數和間隔，請設定重試次數 (`maxRetries`) 和每次重試嘗試所允許的期間（以毫秒為單位） (`retryIntervalMilliseconds`) ：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="ea5ba-192">隱藏或取代重新連接顯示</span><span class="sxs-lookup"><span data-stu-id="ea5ba-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="ea5ba-193">若要隱藏重新連接顯示，請將重新連接處理常式設定 `_reconnectionDisplay` 為空白物件 (`{}` 或 `new Object()`) ：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="ea5ba-194">若要取代重新連接顯示，請 `_reconnectionDisplay` 在上述範例中設定為要顯示的元素：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="ea5ba-195">預留位置 `{ELEMENT ID}` 是要顯示之 HTML 元素的識別碼。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ea5ba-196">藉由在 `transition-delay` 應用程式的 CSS 中設定屬性（property），藉由在應用程式的)  (CSS 中設定屬性（property），藉以自訂延遲顯示之前 `wwwroot/css/site.css`</span><span class="sxs-lookup"><span data-stu-id="ea5ba-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="ea5ba-197">下列範例會將500毫秒 (預設) 的轉換延遲設定為1000毫秒 (1 秒) ：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="ea5ba-198">中斷與 Blazor 用戶端的線路連線</span><span class="sxs-lookup"><span data-stu-id="ea5ba-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="ea5ba-199">依預設， Blazor 當觸發[ `unload` 頁面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)時，電路會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="ea5ba-200">若要中斷用戶端上其他案例的線路連接，請 `Blazor.disconnect` 在適當的事件處理常式中叫用。</span><span class="sxs-lookup"><span data-stu-id="ea5ba-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="ea5ba-201">在下列範例中，當 ([ `pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) 隱藏頁面時，電路會中斷連線：</span><span class="sxs-lookup"><span data-stu-id="ea5ba-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ea5ba-202">其他資源</span><span class="sxs-lookup"><span data-stu-id="ea5ba-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="ea5ba-203">Blazor Server 重新連接事件和元件生命週期事件</span><span class="sxs-lookup"><span data-stu-id="ea5ba-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
