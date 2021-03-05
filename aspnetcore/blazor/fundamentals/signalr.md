---
title: ASP.NET 核心 Blazor SignalR 指引
author: guardrex
description: 瞭解如何設定和管理 Blazor SignalR 連接。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 63dfd93fbc42a869211bc5cd481a8dbee6eb6c91
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118911"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="d5121-103">ASP.NET 核心 Blazor SignalR 指引</span><span class="sxs-lookup"><span data-stu-id="d5121-103">ASP.NET Core Blazor SignalR guidance</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="d5121-104">本文說明如何 SignalR 在應用程式中設定及管理連線 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="d5121-104">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="d5121-105">如需有關 ASP.NET Core 設定的一般指引 SignalR ，請參閱檔區域中的主題 <xref:signalr/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="d5121-105">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="d5121-106">若要設定 SignalR [加入至裝載的 Blazor WebAssembly 解決方案](xref:tutorials/signalr-blazor)，請參閱 <xref:signalr/configuration#configure-server-options> 。</span><span class="sxs-lookup"><span data-stu-id="d5121-106">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="d5121-107">SignalR 驗證的跨原始來源協商</span><span class="sxs-lookup"><span data-stu-id="d5121-107">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="d5121-108">設定 SignalR 的基礎用戶端以傳送認證，例如 cookie s 或 HTTP 驗證標頭：</span><span class="sxs-lookup"><span data-stu-id="d5121-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="d5121-109">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> 跨原始來源 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 要求。</span><span class="sxs-lookup"><span data-stu-id="d5121-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests.</span></span>

  <span data-ttu-id="d5121-110">`IncludeRequestCredentialsMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="d5121-110">`IncludeRequestCredentialsMessageHandler.cs`:</span></span>

  ```csharp
  using System.Net.Http;
  using System.Threading;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Components.WebAssembly.Http;

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

* <span data-ttu-id="d5121-111">建立中樞連線時，請將指派給 <xref:System.Net.Http.HttpMessageHandler> <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 選項：</span><span class="sxs-lookup"><span data-stu-id="d5121-111">Where a hub connection is built, assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  HubConnectionBuilder hubConnecton;

  ...

  hubConnecton = new HubConnectionBuilder()
      .WithUrl(new Uri(NavigationManager.ToAbsoluteUri("/chathub")), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

  <span data-ttu-id="d5121-112">上述範例會將中樞連線 URL 設定為的絕對 URI 位址 `/chathub` ，也就是元件 () 的[ SignalR with Blazor 教學](xref:tutorials/signalr-blazor)課程中所使用的 url `Index` `Pages/Index.razor` 。</span><span class="sxs-lookup"><span data-stu-id="d5121-112">The preceding example configures the hub connection URL to the absolute URI address at `/chathub`, which is the URL used in the [SignalR with Blazor tutorial](xref:tutorials/signalr-blazor) in the `Index` component (`Pages/Index.razor`).</span></span> <span data-ttu-id="d5121-113">URI 也可以透過字串（例如，或透過設定）來設定 `https://signalr.example.com` 。 [](xref:blazor/fundamentals/configuration)</span><span class="sxs-lookup"><span data-stu-id="d5121-113">The URI can also be set via a string, for example `https://signalr.example.com`, or via [configuration](xref:blazor/fundamentals/configuration).</span></span>

<span data-ttu-id="d5121-114">如需詳細資訊，請參閱<xref:signalr/configuration#configure-additional-options>。</span><span class="sxs-lookup"><span data-stu-id="d5121-114">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="render-mode"></a><span data-ttu-id="d5121-115">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="d5121-115">Render mode</span></span>

<span data-ttu-id="d5121-116">如果 Blazor WebAssembly 使用的應用程式 SignalR 已設定為在伺服器上預先建立，則會在建立伺服器的用戶端連接之前發生預先呈現。</span><span class="sxs-lookup"><span data-stu-id="d5121-116">If a Blazor WebAssembly app that uses SignalR is configured to prerender on the server, prerendering occurs before the client connection to the server is established.</span></span> <span data-ttu-id="d5121-117">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="d5121-117">For more information, see the following articles:</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d5121-118">其他資源</span><span class="sxs-lookup"><span data-stu-id="d5121-118">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="d5121-119">本文說明如何 SignalR 在應用程式中設定及管理連線 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="d5121-119">This article explains how to configure and manage SignalR connections in Blazor apps.</span></span>

<span data-ttu-id="d5121-120">如需有關 ASP.NET Core 設定的一般指引 SignalR ，請參閱檔區域中的主題 <xref:signalr/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="d5121-120">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="d5121-121">若要設定 SignalR [加入至裝載的 Blazor WebAssembly 解決方案](xref:tutorials/signalr-blazor)，請參閱 <xref:signalr/configuration#configure-server-options> 。</span><span class="sxs-lookup"><span data-stu-id="d5121-121">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="d5121-122">線路處理常式選項</span><span class="sxs-lookup"><span data-stu-id="d5121-122">Circuit handler options</span></span>

<span data-ttu-id="d5121-123">Blazor Server使用 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 下表所示的來設定電路。</span><span class="sxs-lookup"><span data-stu-id="d5121-123">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="d5121-124">選項</span><span class="sxs-lookup"><span data-stu-id="d5121-124">Option</span></span> | <span data-ttu-id="d5121-125">預設</span><span class="sxs-lookup"><span data-stu-id="d5121-125">Default</span></span> | <span data-ttu-id="d5121-126">描述</span><span class="sxs-lookup"><span data-stu-id="d5121-126">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="d5121-127">當線路上發生未處理的例外狀況，或透過 JS interop 的 .NET 方法叫用導致例外狀況時，將詳細的例外狀況訊息傳送至 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="d5121-127">Send detailed exception messages to JavaScript when an unhandled exception occurs on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="d5121-128">100</span><span class="sxs-lookup"><span data-stu-id="d5121-128">100</span></span> | <span data-ttu-id="d5121-129">伺服器一次保存在記憶體中的中斷連接電路數目上限。</span><span class="sxs-lookup"><span data-stu-id="d5121-129">Maximum number of disconnected circuits that the server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="d5121-130">3 分鐘</span><span class="sxs-lookup"><span data-stu-id="d5121-130">3 minutes</span></span> | <span data-ttu-id="d5121-131">中斷連接的電路在中斷之前保留在記憶體中的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="d5121-131">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="d5121-132">1 分鐘</span><span class="sxs-lookup"><span data-stu-id="d5121-132">1 minute</span></span> | <span data-ttu-id="d5121-133">在將非同步 JavaScript 函式呼叫計時之前，伺服器所等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="d5121-133">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="d5121-134">10</span><span class="sxs-lookup"><span data-stu-id="d5121-134">10</span></span> | <span data-ttu-id="d5121-135">伺服器在指定時間為每個迴圈保留記憶體中的未認可轉譯批次數目上限，以支援健全的重新連接。</span><span class="sxs-lookup"><span data-stu-id="d5121-135">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="d5121-136">達到此限制之後，伺服器會停止產生新的轉譯批次，直到用戶端認可一或多個批次為止。</span><span class="sxs-lookup"><span data-stu-id="d5121-136">After reaching the limit, the server stops producing new render batches until one or more batches are acknowledged by the client.</span></span> |

<span data-ttu-id="d5121-137">在中設定選項 `Startup.ConfigureServices` 委派的選項 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 。</span><span class="sxs-lookup"><span data-stu-id="d5121-137">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="d5121-138">下列範例會指派上表所示的預設選項值。</span><span class="sxs-lookup"><span data-stu-id="d5121-138">The following example assigns the default option values shown in the preceding table.</span></span> <span data-ttu-id="d5121-139">確認 `Startup.cs` 使用 <xref:System> 命名空間 (`using System;`) 。</span><span class="sxs-lookup"><span data-stu-id="d5121-139">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="d5121-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d5121-140">`Startup.ConfigureServices`:</span></span>

```csharp
services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="d5121-141">若要設定 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> ，請使用 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A> 。</span><span class="sxs-lookup"><span data-stu-id="d5121-141">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="d5121-142">如需選項描述，請參閱 <xref:signalr/configuration#configure-server-options> 。</span><span class="sxs-lookup"><span data-stu-id="d5121-142">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="d5121-143">下列範例會指派預設選項值。</span><span class="sxs-lookup"><span data-stu-id="d5121-143">The following example assigns the default option values.</span></span> <span data-ttu-id="d5121-144">確認 `Startup.cs` 使用 <xref:System> 命名空間 (`using System;`) 。</span><span class="sxs-lookup"><span data-stu-id="d5121-144">Confirm that `Startup.cs` uses the <xref:System> namespace (`using System;`).</span></span>

<span data-ttu-id="d5121-145">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d5121-145">`Startup.ConfigureServices`:</span></span>

```csharp
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

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="d5121-146">反映 UI 中的連接狀態</span><span class="sxs-lookup"><span data-stu-id="d5121-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="d5121-147">當用戶端偵測到連線已遺失時，當用戶端嘗試重新連接時，會對使用者顯示預設的 UI。</span><span class="sxs-lookup"><span data-stu-id="d5121-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="d5121-148">如果重新連接失敗，就會提供使用者重試的選項。</span><span class="sxs-lookup"><span data-stu-id="d5121-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="d5121-149">若要自訂 UI，請在頁面的中，使用的來定義專案 `id` `components-reconnect-modal` `<body>` `_Host.cshtml` Razor 。</span><span class="sxs-lookup"><span data-stu-id="d5121-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page.</span></span>

<span data-ttu-id="d5121-150">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d5121-150">`Pages/_Host.cshtml`:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="d5121-151">將下列 CSS 樣式新增至網站的樣式表單。</span><span class="sxs-lookup"><span data-stu-id="d5121-151">Add the following CSS styles to the site's stylesheet.</span></span>

<span data-ttu-id="d5121-152">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="d5121-152">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="d5121-153">下表描述架構套用至元素的 CSS 類別 `components-reconnect-modal` Blazor 。</span><span class="sxs-lookup"><span data-stu-id="d5121-153">The following table describes the CSS classes applied to the `components-reconnect-modal` element by the Blazor framework.</span></span>

| <span data-ttu-id="d5121-154">CSS 類別</span><span class="sxs-lookup"><span data-stu-id="d5121-154">CSS class</span></span>                       | <span data-ttu-id="d5121-155">表示&hellip;</span><span class="sxs-lookup"><span data-stu-id="d5121-155">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="d5121-156">遺失的連接。</span><span class="sxs-lookup"><span data-stu-id="d5121-156">A lost connection.</span></span> <span data-ttu-id="d5121-157">用戶端正在嘗試重新連接。</span><span class="sxs-lookup"><span data-stu-id="d5121-157">The client is attempting to reconnect.</span></span> <span data-ttu-id="d5121-158">顯示強制回應。</span><span class="sxs-lookup"><span data-stu-id="d5121-158">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="d5121-159">系統會重新建立與伺服器之間的使用中連接。</span><span class="sxs-lookup"><span data-stu-id="d5121-159">An active connection is re-established to the server.</span></span> <span data-ttu-id="d5121-160">隱形模式。</span><span class="sxs-lookup"><span data-stu-id="d5121-160">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="d5121-161">重新連接失敗，可能是因為網路失敗。</span><span class="sxs-lookup"><span data-stu-id="d5121-161">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="d5121-162">若要嘗試重新連接，請 `window.Blazor.reconnect()` 在 JavaScript 中呼叫。</span><span class="sxs-lookup"><span data-stu-id="d5121-162">To attempt reconnection, call `window.Blazor.reconnect()` in JavaScript.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="d5121-163">重新連線遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="d5121-163">Reconnection rejected.</span></span> <span data-ttu-id="d5121-164">伺服器已達到但拒絕連線，而且伺服器上的使用者狀態遺失。</span><span class="sxs-lookup"><span data-stu-id="d5121-164">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="d5121-165">若要重載應用程式，請 `location.reload()` 在 JavaScript 中呼叫。</span><span class="sxs-lookup"><span data-stu-id="d5121-165">To reload the app, call `location.reload()` in JavaScript.</span></span> <span data-ttu-id="d5121-166">此連接狀態可能會在下列情況下產生：</span><span class="sxs-lookup"><span data-stu-id="d5121-166">This connection state may result when:</span></span><ul><li><span data-ttu-id="d5121-167">伺服器端線路發生損毀。</span><span class="sxs-lookup"><span data-stu-id="d5121-167">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="d5121-168">用戶端已中斷連線到足夠的時間，讓伺服器卸載使用者的狀態。</span><span class="sxs-lookup"><span data-stu-id="d5121-168">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="d5121-169">系統會處置使用者元件的實例。</span><span class="sxs-lookup"><span data-stu-id="d5121-169">Instances of the user's components are disposed.</span></span></li><li><span data-ttu-id="d5121-170">伺服器會重新開機，或回收應用程式的背景工作進程。</span><span class="sxs-lookup"><span data-stu-id="d5121-170">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="d5121-171">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="d5121-171">Render mode</span></span>

<span data-ttu-id="d5121-172">根據預設， Blazor Server 應用程式會先在伺服器上預先呈現 UI，然後再建立與伺服器的用戶端連接。</span><span class="sxs-lookup"><span data-stu-id="d5121-172">By default, Blazor Server apps prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="d5121-173">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="d5121-173">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="d5121-174">初始化 Blazor 線路</span><span class="sxs-lookup"><span data-stu-id="d5121-174">Initialize the Blazor circuit</span></span>

<span data-ttu-id="d5121-175">Blazor Server在檔案中設定應用程式[ SignalR 線路](xref:blazor/hosting-models#circuits)的手動啟動 `Pages/_Host.cshtml` ：</span><span class="sxs-lookup"><span data-stu-id="d5121-175">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="d5121-176">將 `autostart="false"` 屬性新增至 `<script>` 腳本的標記 `blazor.server.js` 。</span><span class="sxs-lookup"><span data-stu-id="d5121-176">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="d5121-177">將在腳本標記之後呼叫的腳本放在 `Blazor.start` `blazor.server.js` 結尾 `</body>` 標記內。</span><span class="sxs-lookup"><span data-stu-id="d5121-177">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="d5121-178">`autostart`停用時，不相依于電路的應用程式任何層面都能正常運作。</span><span class="sxs-lookup"><span data-stu-id="d5121-178">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="d5121-179">例如，用戶端路由可運作。</span><span class="sxs-lookup"><span data-stu-id="d5121-179">For example, client-side routing is operational.</span></span> <span data-ttu-id="d5121-180">不過，與電路相依的任何層面在 `Blazor.start` 呼叫之前都不會運作。</span><span class="sxs-lookup"><span data-stu-id="d5121-180">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="d5121-181">沒有已建立的線路，應用程式行為無法預期。</span><span class="sxs-lookup"><span data-stu-id="d5121-181">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="d5121-182">例如，當線路中斷連線時，元件方法無法執行。</span><span class="sxs-lookup"><span data-stu-id="d5121-182">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="d5121-183">在 Blazor 檔就緒時初始化</span><span class="sxs-lookup"><span data-stu-id="d5121-183">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="d5121-184">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d5121-184">`Pages/_Host.cshtml`:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="d5121-185">`Promise`從手動開始到該結果的鏈</span><span class="sxs-lookup"><span data-stu-id="d5121-185">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="d5121-186">若要執行其他工作（例如 JS interop 初始化），請使用 [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) 來與 [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 手動 Blazor 應用程式啟動的結果連結。</span><span class="sxs-lookup"><span data-stu-id="d5121-186">To perform additional tasks, such as JS interop initialization, use [`then`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) to chain to the [`Promise`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) that results from a manual Blazor app start.</span></span>

<span data-ttu-id="d5121-187">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d5121-187">`Pages/_Host.cshtml`:</span></span>

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

### <a name="configure-signalr-client-logging"></a><span data-ttu-id="d5121-188">設定 SignalR 用戶端記錄</span><span class="sxs-lookup"><span data-stu-id="d5121-188">Configure SignalR client logging</span></span>

<span data-ttu-id="d5121-189">在用戶端產生器上，傳入 `configureSignalR` `configureLogging` 以記錄層級呼叫的設定物件。</span><span class="sxs-lookup"><span data-stu-id="d5121-189">On the client builder, pass in the `configureSignalR` configuration object that calls `configureLogging` with the log level.</span></span>

<span data-ttu-id="d5121-190">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d5121-190">`Pages/_Host.cshtml`:</span></span>

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

<span data-ttu-id="d5121-191">在上述範例中， `information` 相當於的記錄層級 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="d5121-191">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="d5121-192">修改重新連接處理常式</span><span class="sxs-lookup"><span data-stu-id="d5121-192">Modify the reconnection handler</span></span>

<span data-ttu-id="d5121-193">您可以針對自訂行為修改重新連接處理常式的線路線上活動，例如：</span><span class="sxs-lookup"><span data-stu-id="d5121-193">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="d5121-194">以在中斷連接時通知使用者。</span><span class="sxs-lookup"><span data-stu-id="d5121-194">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="d5121-195">若要從用戶端執行記錄 (線上路連線時) 。</span><span class="sxs-lookup"><span data-stu-id="d5121-195">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="d5121-196">若要修改連接事件，請註冊下列連線變更的回呼：</span><span class="sxs-lookup"><span data-stu-id="d5121-196">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="d5121-197">中斷的連接使用 `onConnectionDown` 。</span><span class="sxs-lookup"><span data-stu-id="d5121-197">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="d5121-198">已建立/重新建立的連接使用 `onConnectionUp` 。</span><span class="sxs-lookup"><span data-stu-id="d5121-198">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="d5121-199">**`onConnectionDown`和都 `onConnectionUp` 必須指定。**</span><span class="sxs-lookup"><span data-stu-id="d5121-199">**Both `onConnectionDown` and `onConnectionUp` must be specified.**</span></span>

<span data-ttu-id="d5121-200">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d5121-200">`Pages/_Host.cshtml`:</span></span>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="d5121-201">調整重新連接重試計數和間隔</span><span class="sxs-lookup"><span data-stu-id="d5121-201">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="d5121-202">若要調整重新連接重試計數和間隔，請將重試次數 (`maxRetries`) 和每次重試嘗試所允許的期間（以毫秒為單位） (`retryIntervalMilliseconds`) 。</span><span class="sxs-lookup"><span data-stu-id="d5121-202">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

<span data-ttu-id="d5121-203">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d5121-203">`Pages/_Host.cshtml`:</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="d5121-204">隱藏或取代重新連接顯示</span><span class="sxs-lookup"><span data-stu-id="d5121-204">Hide or replace the reconnection display</span></span>

<span data-ttu-id="d5121-205">若要隱藏重新連接顯示，請將重新連接處理常式設定 `_reconnectionDisplay` 為空的物件 (`{}` 或 `new Object()`) 。</span><span class="sxs-lookup"><span data-stu-id="d5121-205">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

<span data-ttu-id="d5121-206">`Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="d5121-206">`Pages/_Host.cshtml`:</span></span>

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

<span data-ttu-id="d5121-207">若要取代重新連接顯示，請 `_reconnectionDisplay` 在上述範例中設定為要顯示的元素：</span><span class="sxs-lookup"><span data-stu-id="d5121-207">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="d5121-208">預留位置 `{ELEMENT ID}` 是要顯示之 HTML 元素的識別碼。</span><span class="sxs-lookup"><span data-stu-id="d5121-208">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d5121-209">在網站 CSS 中為強制回應專案設定屬性，以自訂重新連接顯示之前的延遲 `transition-delay` 。</span><span class="sxs-lookup"><span data-stu-id="d5121-209">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the site's CSS for the modal element.</span></span> <span data-ttu-id="d5121-210">下列範例會將500毫秒 (預設) 的轉換延遲設定為1000毫秒 (1 秒) 。</span><span class="sxs-lookup"><span data-stu-id="d5121-210">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second).</span></span>

<span data-ttu-id="d5121-211">`wwwroot/css/site.css`:</span><span class="sxs-lookup"><span data-stu-id="d5121-211">`wwwroot/css/site.css`:</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="d5121-212">中斷與 Blazor 用戶端的線路連線</span><span class="sxs-lookup"><span data-stu-id="d5121-212">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="d5121-213">依預設， Blazor 當觸發[ `unload` 頁面事件](https://developer.mozilla.org/docs/Web/API/Window/unload_event)時，電路會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="d5121-213">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="d5121-214">若要中斷用戶端上其他案例的線路連接，請 `Blazor.disconnect` 在適當的事件處理常式中叫用。</span><span class="sxs-lookup"><span data-stu-id="d5121-214">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="d5121-215">在下列範例中，當 ([ `pagehide` 事件](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)) 隱藏頁面時，電路會中斷連線：</span><span class="sxs-lookup"><span data-stu-id="d5121-215">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d5121-216">其他資源</span><span class="sxs-lookup"><span data-stu-id="d5121-216">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="d5121-217">Blazor Server 重新連接事件和元件生命週期事件</span><span class="sxs-lookup"><span data-stu-id="d5121-217">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)

::: zone-end
