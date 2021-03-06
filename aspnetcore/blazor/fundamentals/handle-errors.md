---
title: 處理 ASP.NET Core 應用程式中的錯誤 Blazor
author: guardrex
description: 探索 ASP.NET Core 如何 Blazor Blazor 管理未處理的例外狀況，以及如何開發偵測和處理錯誤的應用程式。
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
uid: blazor/fundamentals/handle-errors
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d4c8054afc3818d58bc2a047a0aa74613ae6047b
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395093"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="9fac3-103">處理 ASP.NET Core 應用程式中的錯誤 Blazor</span><span class="sxs-lookup"><span data-stu-id="9fac3-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="9fac3-104">本文說明如何 Blazor 管理未處理的例外狀況，以及如何開發偵測和處理錯誤的應用程式。</span><span class="sxs-lookup"><span data-stu-id="9fac3-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

::: zone pivot="webassembly"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="9fac3-105">開發期間的詳細錯誤</span><span class="sxs-lookup"><span data-stu-id="9fac3-105">Detailed errors during development</span></span>

<span data-ttu-id="9fac3-106">當 Blazor 應用程式在開發期間無法正常運作時，從應用程式接收詳細的錯誤資訊有助於疑難排解和修正問題。</span><span class="sxs-lookup"><span data-stu-id="9fac3-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="9fac3-107">發生錯誤時， Blazor 應用程式會在畫面底部顯示淺黃色的橫條：</span><span class="sxs-lookup"><span data-stu-id="9fac3-107">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="9fac3-108">在開發期間，橫條會將您導向瀏覽器主控台，您可以在其中查看例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-108">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="9fac3-109">在生產環境中，橫條會通知使用者發生錯誤，建議您重新整理瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9fac3-109">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="9fac3-110">此錯誤處理體驗的 UI 是[ Blazor 專案範本](xref:blazor/project-structure)的一部分。</span><span class="sxs-lookup"><span data-stu-id="9fac3-110">The UI for this error handling experience is part of the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="9fac3-111">在 Blazor WebAssembly 應用程式中，自訂檔案的體驗 `wwwroot/index.html` ：</span><span class="sxs-lookup"><span data-stu-id="9fac3-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="9fac3-112">`blazor-error-ui`元素通常是隱藏的，因為 `display: none` `blazor-error-ui` 應用程式樣式表單中 CSS 類別的樣式存在， (`wwwroot/css/app.css`) 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-112">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the app's stylesheet (`wwwroot/css/app.css`).</span></span> <span data-ttu-id="9fac3-113">當發生錯誤時，架構會套用 `display: block` 至元素。</span><span class="sxs-lookup"><span data-stu-id="9fac3-113">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="9fac3-114">管理開發人員程式碼中未處理的例外狀況</span><span class="sxs-lookup"><span data-stu-id="9fac3-114">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="9fac3-115">若要讓應用程式在發生錯誤之後繼續，應用程式必須有錯誤處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="9fac3-115">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="9fac3-116">本文稍後的章節將說明可能的未處理例外狀況來源。</span><span class="sxs-lookup"><span data-stu-id="9fac3-116">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="9fac3-117">在生產環境中，請勿在 UI 中呈現架構例外狀況訊息或堆疊追蹤。</span><span class="sxs-lookup"><span data-stu-id="9fac3-117">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="9fac3-118">轉譯例外狀況訊息或堆疊追蹤可能：</span><span class="sxs-lookup"><span data-stu-id="9fac3-118">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="9fac3-119">將機密資訊洩漏給終端使用者。</span><span class="sxs-lookup"><span data-stu-id="9fac3-119">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="9fac3-120">協助惡意使用者探索應用程式中可能會危害應用程式、伺服器或網路安全性的弱點。</span><span class="sxs-lookup"><span data-stu-id="9fac3-120">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="9fac3-121">全域例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="9fac3-121">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="9fac3-122">使用持續性提供者記錄錯誤</span><span class="sxs-lookup"><span data-stu-id="9fac3-122">Log errors with a persistent provider</span></span>

<span data-ttu-id="9fac3-123">如果發生未處理的例外狀況，則會將例外狀況記錄到 <xref:Microsoft.Extensions.Logging.ILogger> 服務容器中設定的實例。</span><span class="sxs-lookup"><span data-stu-id="9fac3-123">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="9fac3-124">根據預設， Blazor 應用程式會使用主控台記錄提供者來記錄主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="9fac3-124">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="9fac3-125">請考慮將錯誤資訊傳送至後端 web API，以使用記錄提供者搭配記錄檔大小管理和記錄檔輪替，以記錄到伺服器上的永久位置。</span><span class="sxs-lookup"><span data-stu-id="9fac3-125">Consider logging to a more permanent location on the server by sending error information to a backend web API that uses a logging provider with log size management and log rotation.</span></span> <span data-ttu-id="9fac3-126">此外，後端 web API 應用程式可以使用應用程式效能管理 (APM) 服務（例如[Azure Application Insights (Azure 監視器) &dagger; ](/azure/azure-monitor/app/app-insights-overview)）記錄從用戶端接收的錯誤資訊。</span><span class="sxs-lookup"><span data-stu-id="9fac3-126">Alternatively, the backend web API app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)&dagger;](/azure/azure-monitor/app/app-insights-overview), to record error information that it receives from clients.</span></span>

<span data-ttu-id="9fac3-127">您必須決定要記錄哪些事件，以及記錄事件的嚴重性層級。</span><span class="sxs-lookup"><span data-stu-id="9fac3-127">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="9fac3-128">惡意使用者可能可以刻意觸發錯誤。</span><span class="sxs-lookup"><span data-stu-id="9fac3-128">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="9fac3-129">例如，請勿從 `ProductId` 顯示產品詳細資料的元件 URL 中提供未知的錯誤記錄事件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-129">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="9fac3-130">並非所有錯誤都應該視為記錄的事件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-130">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="9fac3-131">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="9fac3-131">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&Dagger;
* <xref:web-api/index>

<span data-ttu-id="9fac3-132">&dagger;支援應用程式的原生 [Application Insights](/azure/azure-monitor/app/app-insights-overview) 功能 Blazor WebAssembly 和 Blazor [Google Analytics](https://analytics.google.com/analytics/web/) 的原生架構支援可能會在這些技術的未來版本中提供。</span><span class="sxs-lookup"><span data-stu-id="9fac3-132">&dagger;Native [Application Insights](/azure/azure-monitor/app/app-insights-overview) features to support Blazor WebAssembly apps and native Blazor framework support for [Google Analytics](https://analytics.google.com/analytics/web/) might become available in future releases of these technologies.</span></span> <span data-ttu-id="9fac3-133">如需詳細資訊，請參閱 [在 WASM 用戶端中支援 App Insights Blazor (Microsoft/ApplicationInsights-dotnet #2143) ](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) 和 [Web 分析和診斷 (包含)  (dotnet/aspnetcore #5461 ](https://github.com/dotnet/aspnetcore/issues/5461)) 的連結。</span><span class="sxs-lookup"><span data-stu-id="9fac3-133">For more information, see [Support App Insights in Blazor WASM Client Side (microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) and [Web analytics and diagnostics (includes links to community implementations) (dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461).</span></span> <span data-ttu-id="9fac3-134">在此同時，用戶端應用程式 Blazor WebAssembly 可以使用具有[JS Interop](xref:blazor/call-javascript-from-dotnet)的[application insights JavaScript SDK](/azure/azure-monitor/app/javascript) ，直接將錯誤記錄到來自用戶端應用程式的 application insights。</span><span class="sxs-lookup"><span data-stu-id="9fac3-134">In the meantime, a client-side Blazor WebAssembly app can use the [Application Insights JavaScript SDK](/azure/azure-monitor/app/javascript) with [JS interop](xref:blazor/call-javascript-from-dotnet) to log errors directly to Application Insights from a client-side app.</span></span>

<span data-ttu-id="9fac3-135">&Dagger;適用于應用程式的 web API 後端應用程式的伺服器端 ASP.NET Core 應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-135">&Dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span> <span data-ttu-id="9fac3-136">用戶端應用程式會將錯誤資訊設陷並傳送至 web API，而該 API 會將錯誤資訊記錄至持續性記錄提供者。</span><span class="sxs-lookup"><span data-stu-id="9fac3-136">Client-side apps trap and send error information to a web API, which logs the error information to a persistent logging provider.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="9fac3-137">可能發生錯誤的位置</span><span class="sxs-lookup"><span data-stu-id="9fac3-137">Places where errors may occur</span></span>

<span data-ttu-id="9fac3-138">架構和應用程式程式碼可能會在下列任何位置中觸發未處理的例外狀況，這會在本文的下列各節中進一步說明：</span><span class="sxs-lookup"><span data-stu-id="9fac3-138">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="9fac3-139">元件具現化</span><span class="sxs-lookup"><span data-stu-id="9fac3-139">Component instantiation</span></span>](#component-instantiation-webassembly)
* [<span data-ttu-id="9fac3-140">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="9fac3-140">Lifecycle methods</span></span>](#lifecycle-methods-webassembly)
* [<span data-ttu-id="9fac3-141">轉譯邏輯</span><span class="sxs-lookup"><span data-stu-id="9fac3-141">Rendering logic</span></span>](#rendering-logic-webassembly)
* [<span data-ttu-id="9fac3-142">事件處理常式</span><span class="sxs-lookup"><span data-stu-id="9fac3-142">Event handlers</span></span>](#event-handlers-webassembly)
* [<span data-ttu-id="9fac3-143">元件處置</span><span class="sxs-lookup"><span data-stu-id="9fac3-143">Component disposal</span></span>](#component-disposal-webassembly)
* [<span data-ttu-id="9fac3-144">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="9fac3-144">JavaScript interop</span></span>](#javascript-interop-webassembly)

<h3 id="component-instantiation-webassembly"><span data-ttu-id="9fac3-145">元件具現化</span><span class="sxs-lookup"><span data-stu-id="9fac3-145">Component instantiation</span></span></h3>

<span data-ttu-id="9fac3-146">當 Blazor 建立元件的實例時：</span><span class="sxs-lookup"><span data-stu-id="9fac3-146">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="9fac3-147">會叫用元件的函式。</span><span class="sxs-lookup"><span data-stu-id="9fac3-147">The component's constructor is invoked.</span></span>
* <span data-ttu-id="9fac3-148">系統會叫用透過指示詞 [`@inject`](xref:mvc/views/razor#inject) 或[ `[Inject]` 屬性](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)提供給元件之函式之任何非 singleton DI 服務的函式。</span><span class="sxs-lookup"><span data-stu-id="9fac3-148">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="9fac3-149">在執行的函式或任何屬性的 setter 中發生錯誤 `[Inject]` ，會導致未處理的例外狀況，並阻止架構將元件具現化。</span><span class="sxs-lookup"><span data-stu-id="9fac3-149">An error in an executed constructor or a setter for any `[Inject]` property results in an unhandled exception and stops the framework from instantiating the component.</span></span> <span data-ttu-id="9fac3-150">如果函式邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-150">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-webassembly"><span data-ttu-id="9fac3-151">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="9fac3-151">Lifecycle methods</span></span></h3>

<span data-ttu-id="9fac3-152">在元件的存留期內，會叫用 Blazor [生命週期方法](xref:blazor/components/lifecycle#lifecycle-methods)。</span><span class="sxs-lookup"><span data-stu-id="9fac3-152">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="9fac3-153">若要讓元件處理生命週期方法中的錯誤，請新增錯誤處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="9fac3-153">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="9fac3-154">在下列範例中，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 呼叫方法來取得產品：</span><span class="sxs-lookup"><span data-stu-id="9fac3-154">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="9fac3-155">方法中擲回的例外狀況 `ProductRepository.GetProductByIdAsync` 是由 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句處理。</span><span class="sxs-lookup"><span data-stu-id="9fac3-155">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="9fac3-156">`catch`執行區塊時：</span><span class="sxs-lookup"><span data-stu-id="9fac3-156">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="9fac3-157">`loadFailed` 設定為 `true` ，用來向使用者顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9fac3-157">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="9fac3-158">會記錄錯誤。</span><span class="sxs-lookup"><span data-stu-id="9fac3-158">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-webassembly"><span data-ttu-id="9fac3-159">轉譯邏輯</span><span class="sxs-lookup"><span data-stu-id="9fac3-159">Rendering logic</span></span></h3>

<span data-ttu-id="9fac3-160">元件檔 () 中的宣告式標記 Razor `.razor` 會編譯成名為的 c # 方法 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-160">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="9fac3-161">當元件呈現時，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 執行並建立描述所轉譯元件之元素、文字和子元件的資料結構。</span><span class="sxs-lookup"><span data-stu-id="9fac3-161">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="9fac3-162">轉譯邏輯可能會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-162">Rendering logic can throw an exception.</span></span> <span data-ttu-id="9fac3-163">當評估但為時，就會發生這種情況的範例 `@someObject.PropertyName` `@someObject` `null` 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-163">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span>

<span data-ttu-id="9fac3-164">若要避免轉譯 <xref:System.NullReferenceException> 邏輯中的，請 `null` 先檢查物件，再存取其成員。</span><span class="sxs-lookup"><span data-stu-id="9fac3-164">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="9fac3-165">在下列範例中， `person.Address` 如果是，則不會存取屬性 `person.Address` `null` ：</span><span class="sxs-lookup"><span data-stu-id="9fac3-165">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="9fac3-166">上述程式碼假設 `person` 不是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-166">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="9fac3-167">程式碼的結構通常會保證物件存在於轉譯元件時。</span><span class="sxs-lookup"><span data-stu-id="9fac3-167">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="9fac3-168">在這些情況下，不需要檢查轉譯 `null` 邏輯。</span><span class="sxs-lookup"><span data-stu-id="9fac3-168">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="9fac3-169">在先前的範例中， `person` 可能保證存在，因為 `person` 會在元件具現化時建立，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="9fac3-169">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-webassembly"><span data-ttu-id="9fac3-170">事件處理常式</span><span class="sxs-lookup"><span data-stu-id="9fac3-170">Event handlers</span></span></h3>

<span data-ttu-id="9fac3-171">使用下列專案建立事件處理常式時，用戶端程式代碼會觸發 c # 程式碼的調用：</span><span class="sxs-lookup"><span data-stu-id="9fac3-171">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="9fac3-172">其他 `@on...` 屬性</span><span class="sxs-lookup"><span data-stu-id="9fac3-172">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="9fac3-173">在這些案例中，事件處理常式程式碼可能會擲回未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-173">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="9fac3-174">如果應用程式呼叫可能因為外部原因而失敗的程式碼，請使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-174">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="9fac3-175">如果使用者程式碼未攔截並處理例外狀況，則架構會記錄例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-175">If user code doesn't trap and handle the exception, the framework logs the exception.</span></span>

<h3 id="component-disposal-webassembly"><span data-ttu-id="9fac3-176">元件處置</span><span class="sxs-lookup"><span data-stu-id="9fac3-176">Component disposal</span></span></h3>

<span data-ttu-id="9fac3-177">您可以從 UI 中移除元件，例如，因為使用者已流覽至另一個頁面。</span><span class="sxs-lookup"><span data-stu-id="9fac3-177">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="9fac3-178">從 UI 中移除實作為的元件時 <xref:System.IDisposable?displayProperty=fullName> ，架構會呼叫元件的 <xref:System.IDisposable.Dispose%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="9fac3-178">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="9fac3-179">如果處置邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-179">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="9fac3-180">如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-180">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-webassembly"><span data-ttu-id="9fac3-181">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="9fac3-181">JavaScript interop</span></span></h3>

<span data-ttu-id="9fac3-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允許 .NET 程式碼在使用者的瀏覽器中對 JavaScript 執行時間進行非同步呼叫。</span><span class="sxs-lookup"><span data-stu-id="9fac3-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="9fac3-183">下列條件適用于的錯誤處理 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="9fac3-183">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="9fac3-184">如果同步的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 失敗，就會發生 .net 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-184">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="9fac3-185"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為無法序列化提供的引數。</span><span class="sxs-lookup"><span data-stu-id="9fac3-185">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="9fac3-186">開發人員程式碼必須攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-186">Developer code must catch the exception.</span></span>
* <span data-ttu-id="9fac3-187">如果 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 非同步呼叫失敗，則 .net <xref:System.Threading.Tasks.Task> 會失敗。</span><span class="sxs-lookup"><span data-stu-id="9fac3-187">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="9fac3-188"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為 JavaScript 端程式碼會擲回例外狀況，或傳回 `Promise` 做為完成的 `rejected` 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-188">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="9fac3-189">開發人員程式碼必須攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-189">Developer code must catch the exception.</span></span> <span data-ttu-id="9fac3-190">如果使用 [`await`](/dotnet/csharp/language-reference/keywords/await) 運算子，請考慮使用錯誤處理和記錄，將方法呼叫包裝在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句中。</span><span class="sxs-lookup"><span data-stu-id="9fac3-190">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>
* <span data-ttu-id="9fac3-191">依預設，對的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 必須在特定期間內完成，否則會呼叫超時。預設的超時時間是一分鐘。</span><span class="sxs-lookup"><span data-stu-id="9fac3-191">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="9fac3-192">此超時會防止程式碼遺失網路連線或永遠不會傳回完成訊息的 JavaScript 程式碼。</span><span class="sxs-lookup"><span data-stu-id="9fac3-192">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="9fac3-193">如果呼叫超時，則產生的 <xref:System.Threading.Tasks> 會失敗，並出現 <xref:System.OperationCanceledException> 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-193">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="9fac3-194">使用記錄來攔截和處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-194">Trap and process the exception with logging.</span></span>

<span data-ttu-id="9fac3-195">同樣地，JavaScript 程式碼可能會起始對[ `[JSInvokable]` 屬性](xref:blazor/call-dotnet-from-javascript)所表示之 .net 方法的呼叫。</span><span class="sxs-lookup"><span data-stu-id="9fac3-195">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="9fac3-196">如果這些 .NET 方法擲回未處理的例外狀況，則 `Promise` 會拒絕 JavaScript 端。</span><span class="sxs-lookup"><span data-stu-id="9fac3-196">If these .NET methods throw an unhandled exception, the JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="9fac3-197">您可以選擇在 .NET 端或方法呼叫的 JavaScript 端使用錯誤處理常式代碼。</span><span class="sxs-lookup"><span data-stu-id="9fac3-197">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="9fac3-198">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="9fac3-198">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="advanced-scenarios"></a><span data-ttu-id="9fac3-199">進階案例</span><span class="sxs-lookup"><span data-stu-id="9fac3-199">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="9fac3-200">遞迴轉譯</span><span class="sxs-lookup"><span data-stu-id="9fac3-200">Recursive rendering</span></span>

<span data-ttu-id="9fac3-201">元件可以用遞迴方式進行嵌套。</span><span class="sxs-lookup"><span data-stu-id="9fac3-201">Components can be nested recursively.</span></span> <span data-ttu-id="9fac3-202">這適用于表示遞迴資料結構。</span><span class="sxs-lookup"><span data-stu-id="9fac3-202">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="9fac3-203">例如， `TreeNode` 元件可以 `TreeNode` 針對每個節點的子系呈現更多元件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-203">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="9fac3-204">以遞迴方式呈現時，請避免產生無限遞迴的程式碼模式：</span><span class="sxs-lookup"><span data-stu-id="9fac3-204">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="9fac3-205">不要以遞迴方式呈現包含迴圈的資料結構。</span><span class="sxs-lookup"><span data-stu-id="9fac3-205">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="9fac3-206">例如，不要轉譯其子系本身包含的樹狀節點。</span><span class="sxs-lookup"><span data-stu-id="9fac3-206">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="9fac3-207">請勿建立包含迴圈的版面配置鏈。</span><span class="sxs-lookup"><span data-stu-id="9fac3-207">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="9fac3-208">例如，請勿建立版面配置本身的配置。</span><span class="sxs-lookup"><span data-stu-id="9fac3-208">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="9fac3-209">不允許終端使用者違反遞迴不變數 (規則) 透過惡意資料輸入或 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="9fac3-209">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="9fac3-210">轉譯期間的無限迴圈：</span><span class="sxs-lookup"><span data-stu-id="9fac3-210">Infinite loops during rendering:</span></span>

* <span data-ttu-id="9fac3-211">導致轉譯程式永遠繼續。</span><span class="sxs-lookup"><span data-stu-id="9fac3-211">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="9fac3-212">相當於建立未結束的迴圈。</span><span class="sxs-lookup"><span data-stu-id="9fac3-212">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="9fac3-213">在這些情況下，執行緒通常會嘗試：</span><span class="sxs-lookup"><span data-stu-id="9fac3-213">In these scenarios, the thread usually attempts to:</span></span>

* <span data-ttu-id="9fac3-214">無限期耗用作業系統所允許的 CPU 時間。</span><span class="sxs-lookup"><span data-stu-id="9fac3-214">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="9fac3-215">耗用無限數量的用戶端記憶體。</span><span class="sxs-lookup"><span data-stu-id="9fac3-215">Consume an unlimited amount of client memory.</span></span> <span data-ttu-id="9fac3-216">使用無限制的記憶體相當於未結束的迴圈在每次反復專案中將專案新增至集合的案例。</span><span class="sxs-lookup"><span data-stu-id="9fac3-216">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="9fac3-217">若要避免無限遞迴模式，請確定遞迴轉譯程式碼包含適當的停止條件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-217">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="9fac3-218">自訂呈現樹狀結構邏輯</span><span class="sxs-lookup"><span data-stu-id="9fac3-218">Custom render tree logic</span></span>

<span data-ttu-id="9fac3-219">大部分的 Blazor 元件會實作為 Razor 元件檔 (`.razor`) ，並由架構編譯以產生可在上操作的邏輯， <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 以呈現其輸出。</span><span class="sxs-lookup"><span data-stu-id="9fac3-219">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="9fac3-220">不過，開發人員可以使用程式化 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> c # 程式碼手動執行邏輯。</span><span class="sxs-lookup"><span data-stu-id="9fac3-220">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="9fac3-221">如需詳細資訊，請參閱<xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。</span><span class="sxs-lookup"><span data-stu-id="9fac3-221">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="9fac3-222">手動轉譯樹狀結構產生器邏輯的使用會被視為 advanced 和 unsafe 案例，不建議用於一般元件開發。</span><span class="sxs-lookup"><span data-stu-id="9fac3-222">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="9fac3-223"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>撰寫程式碼時，開發人員必須保證程式碼的正確性。</span><span class="sxs-lookup"><span data-stu-id="9fac3-223">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="9fac3-224">例如，開發人員必須確保：</span><span class="sxs-lookup"><span data-stu-id="9fac3-224">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="9fac3-225">對 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的呼叫會正確平衡。</span><span class="sxs-lookup"><span data-stu-id="9fac3-225">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="9fac3-226">只會將屬性新增至正確的位置。</span><span class="sxs-lookup"><span data-stu-id="9fac3-226">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="9fac3-227">不正確的手動轉譯樹狀 builder 邏輯可能會造成任意未定義的行為，包括損毀、應用程式停止回應和安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="9fac3-227">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, app hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="9fac3-228">請考慮以相同的複雜度層級手動呈現樹狀檢視器邏輯，並以與撰寫元件程式碼或 [Microsoft 中繼語言 (MSIL](/dotnet/standard/managed-code)相同的 *危險* 層級，手動) 的指示。</span><span class="sxs-lookup"><span data-stu-id="9fac3-228">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9fac3-229">其他資源</span><span class="sxs-lookup"><span data-stu-id="9fac3-229">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;
* <xref:web-api/index>

<span data-ttu-id="9fac3-230">&dagger;適用于用戶端應用程式用於記錄的後端 ASP.NET Core web API 應用程式 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-230">&dagger;Applies to backend ASP.NET Core web API apps that client-side Blazor WebAssembly apps use for logging.</span></span>

::: zone-end

::: zone pivot="server"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="9fac3-231">開發期間的詳細錯誤</span><span class="sxs-lookup"><span data-stu-id="9fac3-231">Detailed errors during development</span></span>

<span data-ttu-id="9fac3-232">當 Blazor 應用程式在開發期間無法正常運作時，從應用程式接收詳細的錯誤資訊有助於疑難排解和修正問題。</span><span class="sxs-lookup"><span data-stu-id="9fac3-232">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="9fac3-233">發生錯誤時， Blazor 應用程式會在畫面底部顯示淺黃色的橫條：</span><span class="sxs-lookup"><span data-stu-id="9fac3-233">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="9fac3-234">在開發期間，橫條會將您導向瀏覽器主控台，您可以在其中查看例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-234">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="9fac3-235">在生產環境中，橫條會通知使用者發生錯誤，建議您重新整理瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9fac3-235">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="9fac3-236">此錯誤處理體驗的 UI 是[ Blazor 專案範本](xref:blazor/project-structure)的一部分。</span><span class="sxs-lookup"><span data-stu-id="9fac3-236">The UI for this error handling experience is part of the [Blazor project templates](xref:blazor/project-structure).</span></span>

<span data-ttu-id="9fac3-237">在 Blazor Server 應用程式中，自訂檔案的體驗 `Pages/_Host.cshtml` ：</span><span class="sxs-lookup"><span data-stu-id="9fac3-237">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="9fac3-238">專案 `blazor-error-ui` 通常是隱藏的，因為 `display: none` `blazor-error-ui` 網站的樣式表單中 CSS 類別的樣式存在， (`wwwroot/css/site.css`) 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-238">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the site's stylesheet (`wwwroot/css/site.css`).</span></span> <span data-ttu-id="9fac3-239">當發生錯誤時，架構會套用 `display: block` 至元素。</span><span class="sxs-lookup"><span data-stu-id="9fac3-239">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="9fac3-240">Blazor Server 詳細線路錯誤</span><span class="sxs-lookup"><span data-stu-id="9fac3-240">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="9fac3-241">用戶端錯誤不包含呼叫堆疊，也不會提供錯誤原因的詳細資料，但伺服器記錄檔會包含這類資訊。</span><span class="sxs-lookup"><span data-stu-id="9fac3-241">Client-side errors don't include the call stack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="9fac3-242">基於開發目的，可透過啟用詳細錯誤，讓用戶端可以使用敏感性電路錯誤資訊。</span><span class="sxs-lookup"><span data-stu-id="9fac3-242">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="9fac3-243">將 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> 設定為 `true`。</span><span class="sxs-lookup"><span data-stu-id="9fac3-243">Set <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> to `true`.</span></span> <span data-ttu-id="9fac3-244">如需詳細資訊和範例，請參閱 <xref:blazor/fundamentals/signalr#circuit-handler-options>。</span><span class="sxs-lookup"><span data-stu-id="9fac3-244">For more information and an example, see <xref:blazor/fundamentals/signalr#circuit-handler-options>.</span></span>

<span data-ttu-id="9fac3-245">設定的替代 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> `DetailedErrors` `true` 方式是在應用程式的開發環境設定檔中，將設定金鑰設定為 (`appsettings.Development.json`) 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-245">An alternative to setting <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> is to set the `DetailedErrors` configuration key to `true` in the app's Development environment settings file (`appsettings.Development.json`).</span></span>  <span data-ttu-id="9fac3-246">此外，設定[ SignalR 伺服器端記錄](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) ，以進行詳細記錄的[Debug](xref:Microsoft.Extensions.Logging.LogLevel)或[Trace](xref:Microsoft.Extensions.Logging.LogLevel) SignalR 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-246">Additionally, set [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="9fac3-247">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="9fac3-247">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

<span data-ttu-id="9fac3-248"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors>設定金鑰也可以設定為 `true` 使用 `ASPNETCORE_DETAILEDERRORS` 環境變數，其值為 `true` 在開發/預備環境伺服器上或在您的本機系統上。</span><span class="sxs-lookup"><span data-stu-id="9fac3-248">The <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> configuration key can also be set to `true` using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true` on Development/Staging environment servers or on your local system.</span></span>

> [!WARNING]
> <span data-ttu-id="9fac3-249">一律避免將錯誤資訊公開給網際網路上的用戶端，這會造成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="9fac3-249">Always avoid exposing error information to clients on the Internet, which is a security risk.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="9fac3-250">Blazor Server應用程式如何回應未處理的例外狀況</span><span class="sxs-lookup"><span data-stu-id="9fac3-250">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="9fac3-251">Blazor Server 是具狀態的架構。</span><span class="sxs-lookup"><span data-stu-id="9fac3-251">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="9fac3-252">當使用者與應用程式互動時，它們會維持與伺服器的連線，稱為 *線路*。</span><span class="sxs-lookup"><span data-stu-id="9fac3-252">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="9fac3-253">線路會保存作用中的元件實例，再加上許多其他狀態的層面，例如：</span><span class="sxs-lookup"><span data-stu-id="9fac3-253">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="9fac3-254">元件的最新轉譯輸出。</span><span class="sxs-lookup"><span data-stu-id="9fac3-254">The most recent rendered output of components.</span></span>
* <span data-ttu-id="9fac3-255">可由用戶端事件觸發的目前事件處理委派集。</span><span class="sxs-lookup"><span data-stu-id="9fac3-255">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="9fac3-256">如果使用者在多個瀏覽器索引標籤中開啟應用程式，使用者會建立多個獨立線路。</span><span class="sxs-lookup"><span data-stu-id="9fac3-256">If a user opens the app in multiple browser tabs, the user creates multiple independent circuits.</span></span>

<span data-ttu-id="9fac3-257">Blazor 將大部分未處理的例外狀況視為嚴重的重大例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-257">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="9fac3-258">如果線路因為未處理的例外狀況而終止，則使用者只能透過重載頁面來建立新的線路，以繼續與應用程式互動。</span><span class="sxs-lookup"><span data-stu-id="9fac3-258">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="9fac3-259">終止的線路以外的線路（其他使用者或其他瀏覽器索引標籤的線路）不受影響。</span><span class="sxs-lookup"><span data-stu-id="9fac3-259">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="9fac3-260">此案例類似于損毀的桌面應用程式。</span><span class="sxs-lookup"><span data-stu-id="9fac3-260">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="9fac3-261">損毀的應用程式必須重新開機，但不會影響其他應用程式。</span><span class="sxs-lookup"><span data-stu-id="9fac3-261">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="9fac3-262">當發生未處理的例外狀況時，架構會終止電路，原因如下：</span><span class="sxs-lookup"><span data-stu-id="9fac3-262">The framework terminates a circuit when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="9fac3-263">未處理的例外狀況通常會讓電路保持未定義狀態。</span><span class="sxs-lookup"><span data-stu-id="9fac3-263">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="9fac3-264">無法在未處理的例外狀況之後保證應用程式的正常操作。</span><span class="sxs-lookup"><span data-stu-id="9fac3-264">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="9fac3-265">如果線路繼續處於未定義狀態，則應用程式中可能會出現安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="9fac3-265">Security vulnerabilities may appear in the app if the circuit continues in an undefined state.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="9fac3-266">管理開發人員程式碼中未處理的例外狀況</span><span class="sxs-lookup"><span data-stu-id="9fac3-266">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="9fac3-267">若要讓應用程式在發生錯誤之後繼續，應用程式必須有錯誤處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="9fac3-267">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="9fac3-268">本文稍後的章節將說明可能的未處理例外狀況來源。</span><span class="sxs-lookup"><span data-stu-id="9fac3-268">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="9fac3-269">在生產環境中，請勿在 UI 中呈現架構例外狀況訊息或堆疊追蹤。</span><span class="sxs-lookup"><span data-stu-id="9fac3-269">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="9fac3-270">轉譯例外狀況訊息或堆疊追蹤可能：</span><span class="sxs-lookup"><span data-stu-id="9fac3-270">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="9fac3-271">將機密資訊洩漏給終端使用者。</span><span class="sxs-lookup"><span data-stu-id="9fac3-271">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="9fac3-272">協助惡意使用者探索應用程式中可能會危害應用程式、伺服器或網路安全性的弱點。</span><span class="sxs-lookup"><span data-stu-id="9fac3-272">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="9fac3-273">全域例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="9fac3-273">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

<span data-ttu-id="9fac3-274">因為本節中的方法會使用語句來處理錯誤 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ，所以 SignalR 用戶端與伺服器之間的連接不會在發生錯誤且電路維持運作時中斷。</span><span class="sxs-lookup"><span data-stu-id="9fac3-274">Because the approaches in this section handle errors with a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, the SignalR connection between the client and server isn't broken when an error occurs and the circuit remains alive.</span></span> <span data-ttu-id="9fac3-275">任何未處理的例外狀況對電路都是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="9fac3-275">Any unhandled exception is fatal to a circuit.</span></span> <span data-ttu-id="9fac3-276">如需詳細資訊，請參閱上一節，以瞭解 [ Blazor Server 應用程式如何回應未處理的例外](#how-a-blazor-server-app-reacts-to-unhandled-exceptions)狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-276">For more information, see the preceding section on [how a Blazor Server app reacts to unhandled exceptions](#how-a-blazor-server-app-reacts-to-unhandled-exceptions).</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="9fac3-277">使用持續性提供者記錄錯誤</span><span class="sxs-lookup"><span data-stu-id="9fac3-277">Log errors with a persistent provider</span></span>

<span data-ttu-id="9fac3-278">如果發生未處理的例外狀況，則會將例外狀況記錄到 <xref:Microsoft.Extensions.Logging.ILogger> 服務容器中設定的實例。</span><span class="sxs-lookup"><span data-stu-id="9fac3-278">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="9fac3-279">根據預設， Blazor 應用程式會使用主控台記錄提供者來記錄主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="9fac3-279">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="9fac3-280">請考慮使用記錄管理檔大小和記錄檔的提供者，在伺服器上記錄更永久的位置。</span><span class="sxs-lookup"><span data-stu-id="9fac3-280">Consider logging to a more permanent location on the server with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="9fac3-281">或者，應用程式可以使用應用程式效能管理 (APM) 服務，例如 [Azure Application Insights (Azure 監視器) ](/azure/azure-monitor/app/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="9fac3-281">Alternatively, the app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)](/azure/azure-monitor/app/app-insights-overview).</span></span>

<span data-ttu-id="9fac3-282">在開發期間， Blazor Server 應用程式通常會將例外狀況的完整詳細資料傳送至瀏覽器的主控台，以協助進行偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="9fac3-282">During development, a Blazor Server app usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="9fac3-283">在生產環境中，不會將詳細錯誤傳送給用戶端，但會在伺服器上記錄例外狀況的完整詳細資料。</span><span class="sxs-lookup"><span data-stu-id="9fac3-283">In production, detailed errors aren't sent to clients, but an exception's full details are logged on the server.</span></span>

<span data-ttu-id="9fac3-284">您必須決定要記錄哪些事件，以及記錄事件的嚴重性層級。</span><span class="sxs-lookup"><span data-stu-id="9fac3-284">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="9fac3-285">惡意使用者可能可以刻意觸發錯誤。</span><span class="sxs-lookup"><span data-stu-id="9fac3-285">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="9fac3-286">例如，請勿從 `ProductId` 顯示產品詳細資料的元件 URL 中提供未知的錯誤記錄事件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-286">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="9fac3-287">並非所有錯誤都應該視為記錄的事件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-287">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="9fac3-288">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="9fac3-288">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="9fac3-289">&dagger;適用于應用程式的 web API 後端應用程式的伺服器端 ASP.NET Core 應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-289">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="9fac3-290">可能發生錯誤的位置</span><span class="sxs-lookup"><span data-stu-id="9fac3-290">Places where errors may occur</span></span>

<span data-ttu-id="9fac3-291">架構和應用程式程式碼可能會在下列任何位置中觸發未處理的例外狀況，這會在本文的下列各節中進一步說明：</span><span class="sxs-lookup"><span data-stu-id="9fac3-291">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="9fac3-292">元件具現化</span><span class="sxs-lookup"><span data-stu-id="9fac3-292">Component instantiation</span></span>](#component-instantiation-server)
* [<span data-ttu-id="9fac3-293">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="9fac3-293">Lifecycle methods</span></span>](#lifecycle-methods-server)
* [<span data-ttu-id="9fac3-294">轉譯邏輯</span><span class="sxs-lookup"><span data-stu-id="9fac3-294">Rendering logic</span></span>](#rendering-logic-server)
* [<span data-ttu-id="9fac3-295">事件處理常式</span><span class="sxs-lookup"><span data-stu-id="9fac3-295">Event handlers</span></span>](#event-handlers-server)
* [<span data-ttu-id="9fac3-296">元件處置</span><span class="sxs-lookup"><span data-stu-id="9fac3-296">Component disposal</span></span>](#component-disposal-server)
* [<span data-ttu-id="9fac3-297">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="9fac3-297">JavaScript interop</span></span>](#javascript-interop-server)
* [<span data-ttu-id="9fac3-298">Blazor Server 轉譯資料流程</span><span class="sxs-lookup"><span data-stu-id="9fac3-298">Blazor Server rerendering</span></span>](#blazor-server-prerendering-server)

<h3 id="component-instantiation-server"><span data-ttu-id="9fac3-299">元件具現化</span><span class="sxs-lookup"><span data-stu-id="9fac3-299">Component instantiation</span></span></h3>

<span data-ttu-id="9fac3-300">當 Blazor 建立元件的實例時：</span><span class="sxs-lookup"><span data-stu-id="9fac3-300">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="9fac3-301">會叫用元件的函式。</span><span class="sxs-lookup"><span data-stu-id="9fac3-301">The component's constructor is invoked.</span></span>
* <span data-ttu-id="9fac3-302">系統會叫用透過指示詞 [`@inject`](xref:mvc/views/razor#inject) 或[ `[Inject]` 屬性](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component)提供給元件之函式之任何非 singleton DI 服務的函式。</span><span class="sxs-lookup"><span data-stu-id="9fac3-302">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="9fac3-303">Blazor Server當任何已執行的函式或任何屬性的 setter 擲回 `[Inject]` 未處理的例外狀況時，電路會失敗。</span><span class="sxs-lookup"><span data-stu-id="9fac3-303">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="9fac3-304">例外狀況是嚴重的，因為架構無法將元件具現化。</span><span class="sxs-lookup"><span data-stu-id="9fac3-304">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="9fac3-305">如果函式邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-305">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-server"><span data-ttu-id="9fac3-306">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="9fac3-306">Lifecycle methods</span></span></h3>

<span data-ttu-id="9fac3-307">在元件的存留期內，會叫用 Blazor [生命週期方法](xref:blazor/components/lifecycle#lifecycle-methods)。</span><span class="sxs-lookup"><span data-stu-id="9fac3-307">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="9fac3-308">如果任何生命週期方法會以同步或非同步方式擲回例外狀況，則例外狀況對電路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-308">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="9fac3-309">若要讓元件處理生命週期方法中的錯誤，請新增錯誤處理邏輯。</span><span class="sxs-lookup"><span data-stu-id="9fac3-309">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="9fac3-310">在下列範例中，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 呼叫方法來取得產品：</span><span class="sxs-lookup"><span data-stu-id="9fac3-310">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="9fac3-311">方法中擲回的例外狀況 `ProductRepository.GetProductByIdAsync` 是由 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句處理。</span><span class="sxs-lookup"><span data-stu-id="9fac3-311">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="9fac3-312">`catch`執行區塊時：</span><span class="sxs-lookup"><span data-stu-id="9fac3-312">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="9fac3-313">`loadFailed` 設定為 `true` ，用來向使用者顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9fac3-313">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="9fac3-314">會記錄錯誤。</span><span class="sxs-lookup"><span data-stu-id="9fac3-314">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-server"><span data-ttu-id="9fac3-315">轉譯邏輯</span><span class="sxs-lookup"><span data-stu-id="9fac3-315">Rendering logic</span></span></h3>

<span data-ttu-id="9fac3-316">元件檔 () 中的宣告式標記 Razor `.razor` 會編譯成名為的 c # 方法 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-316">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="9fac3-317">當元件呈現時，會 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 執行並建立描述所轉譯元件之元素、文字和子元件的資料結構。</span><span class="sxs-lookup"><span data-stu-id="9fac3-317">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="9fac3-318">轉譯邏輯可能會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-318">Rendering logic can throw an exception.</span></span> <span data-ttu-id="9fac3-319">當評估但為時，就會發生這種情況的範例 `@someObject.PropertyName` `@someObject` `null` 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-319">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="9fac3-320">轉譯邏輯擲回的未處理例外狀況對電路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-320">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="9fac3-321">若要避免轉譯 <xref:System.NullReferenceException> 邏輯中的，請 `null` 先檢查物件，再存取其成員。</span><span class="sxs-lookup"><span data-stu-id="9fac3-321">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="9fac3-322">在下列範例中， `person.Address` 如果是，則不會存取屬性 `person.Address` `null` ：</span><span class="sxs-lookup"><span data-stu-id="9fac3-322">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="9fac3-323">上述程式碼假設 `person` 不是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-323">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="9fac3-324">程式碼的結構通常會保證物件存在於轉譯元件時。</span><span class="sxs-lookup"><span data-stu-id="9fac3-324">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="9fac3-325">在這些情況下，不需要檢查轉譯 `null` 邏輯。</span><span class="sxs-lookup"><span data-stu-id="9fac3-325">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="9fac3-326">在先前的範例中， `person` 可能保證存在，因為 `person` 會在元件具現化時建立，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="9fac3-326">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-server"><span data-ttu-id="9fac3-327">事件處理常式</span><span class="sxs-lookup"><span data-stu-id="9fac3-327">Event handlers</span></span></h3>

<span data-ttu-id="9fac3-328">使用下列專案建立事件處理常式時，用戶端程式代碼會觸發 c # 程式碼的調用：</span><span class="sxs-lookup"><span data-stu-id="9fac3-328">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="9fac3-329">其他 `@on...` 屬性</span><span class="sxs-lookup"><span data-stu-id="9fac3-329">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="9fac3-330">在這些案例中，事件處理常式程式碼可能會擲回未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-330">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="9fac3-331">如果事件處理常式擲回未處理的例外狀況 (例如，資料庫查詢) 失敗，則例外狀況對電路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-331">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="9fac3-332">如果應用程式呼叫可能因為外部原因而失敗的程式碼，請使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-332">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="9fac3-333">如果使用者程式碼未攔截並處理例外狀況，則架構會記錄例外狀況並終止電路。</span><span class="sxs-lookup"><span data-stu-id="9fac3-333">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

<h3 id="component-disposal-server"><span data-ttu-id="9fac3-334">元件處置</span><span class="sxs-lookup"><span data-stu-id="9fac3-334">Component disposal</span></span></h3>

<span data-ttu-id="9fac3-335">您可以從 UI 中移除元件，例如，因為使用者已流覽至另一個頁面。</span><span class="sxs-lookup"><span data-stu-id="9fac3-335">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="9fac3-336">從 UI 中移除實作為的元件時 <xref:System.IDisposable?displayProperty=fullName> ，架構會呼叫元件的 <xref:System.IDisposable.Dispose%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="9fac3-336">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="9fac3-337">如果元件的方法擲回 `Dispose` 未處理的例外狀況，則例外狀況對線路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-337">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="9fac3-338">如果處置邏輯可能會擲回例外狀況，則應用程式應該使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 具有錯誤處理和記錄的語句來攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-338">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="9fac3-339">如需元件處置的詳細資訊，請參閱 <xref:blazor/components/lifecycle#component-disposal-with-idisposable> 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-339">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-server"><span data-ttu-id="9fac3-340">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="9fac3-340">JavaScript interop</span></span></h3>

<span data-ttu-id="9fac3-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允許 .NET 程式碼在使用者的瀏覽器中對 JavaScript 執行時間進行非同步呼叫。</span><span class="sxs-lookup"><span data-stu-id="9fac3-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="9fac3-342">下列條件適用于的錯誤處理 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="9fac3-342">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="9fac3-343">如果同步的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 失敗，就會發生 .net 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-343">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="9fac3-344"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為無法序列化提供的引數。</span><span class="sxs-lookup"><span data-stu-id="9fac3-344">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="9fac3-345">開發人員程式碼必須攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-345">Developer code must catch the exception.</span></span> <span data-ttu-id="9fac3-346">如果事件處理常式或元件生命週期方法中的應用程式程式碼未處理例外狀況，則產生的例外狀況對線路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-346">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="9fac3-347">如果 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 非同步呼叫失敗，則 .net <xref:System.Threading.Tasks.Task> 會失敗。</span><span class="sxs-lookup"><span data-stu-id="9fac3-347">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="9fac3-348"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>例如，的呼叫可能會失敗，因為 JavaScript 端程式碼會擲回例外狀況，或傳回 `Promise` 做為完成的 `rejected` 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-348">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="9fac3-349">開發人員程式碼必須攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-349">Developer code must catch the exception.</span></span> <span data-ttu-id="9fac3-350">如果使用 [`await`](/dotnet/csharp/language-reference/keywords/await) 運算子，請考慮使用錯誤處理和記錄，將方法呼叫包裝在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句中。</span><span class="sxs-lookup"><span data-stu-id="9fac3-350">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="9fac3-351">否則，失敗的程式碼會導致未處理的例外狀況對線路而言是嚴重的 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-351">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="9fac3-352">依預設，對的呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 必須在特定期間內完成，否則會呼叫超時。預設的超時時間是一分鐘。</span><span class="sxs-lookup"><span data-stu-id="9fac3-352">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="9fac3-353">此超時會防止程式碼遺失網路連線或永遠不會傳回完成訊息的 JavaScript 程式碼。</span><span class="sxs-lookup"><span data-stu-id="9fac3-353">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="9fac3-354">如果呼叫超時，則產生的 <xref:System.Threading.Tasks> 會失敗，並出現 <xref:System.OperationCanceledException> 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-354">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="9fac3-355">使用記錄來攔截和處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-355">Trap and process the exception with logging.</span></span>

<span data-ttu-id="9fac3-356">同樣地，JavaScript 程式碼可能會起始對[ `[JSInvokable]` 屬性](xref:blazor/call-dotnet-from-javascript)所表示之 .net 方法的呼叫。</span><span class="sxs-lookup"><span data-stu-id="9fac3-356">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="9fac3-357">如果這些 .NET 方法擲回未處理的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="9fac3-357">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="9fac3-358">例外狀況不會被視為線路的嚴重例外狀況 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-358">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="9fac3-359">JavaScript 端 `Promise` 遭到拒絕。</span><span class="sxs-lookup"><span data-stu-id="9fac3-359">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="9fac3-360">您可以選擇在 .NET 端或方法呼叫的 JavaScript 端使用錯誤處理常式代碼。</span><span class="sxs-lookup"><span data-stu-id="9fac3-360">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="9fac3-361">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="9fac3-361">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

<h3 id="blazor-server-prerendering-server"><span data-ttu-id="9fac3-362">Blazor Server 預</span><span class="sxs-lookup"><span data-stu-id="9fac3-362">Blazor Server prerendering</span></span></h3>

<span data-ttu-id="9fac3-363">Blazor 您可以使用 [元件標記](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) 協助程式來資源清單元件，以便在使用者的初始 HTTP 要求中傳回其轉譯的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="9fac3-363">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="9fac3-364">其運作方式如下：</span><span class="sxs-lookup"><span data-stu-id="9fac3-364">This works by:</span></span>

* <span data-ttu-id="9fac3-365">針對屬於相同頁面的所有資源清單元件建立新的線路。</span><span class="sxs-lookup"><span data-stu-id="9fac3-365">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="9fac3-366">產生初始 HTML。</span><span class="sxs-lookup"><span data-stu-id="9fac3-366">Generating the initial HTML.</span></span>
* <span data-ttu-id="9fac3-367">將線路視為 `disconnected` 使用者的瀏覽器會將 SignalR 連接重新建立回相同的伺服器。</span><span class="sxs-lookup"><span data-stu-id="9fac3-367">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="9fac3-368">建立連線時，會繼續執行線路的互動，並且更新元件的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="9fac3-368">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="9fac3-369">如果任何元件在轉譯期間擲回未處理的例外狀況，例如在生命週期方法或轉譯邏輯中：</span><span class="sxs-lookup"><span data-stu-id="9fac3-369">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="9fac3-370">線路的例外狀況是嚴重的。</span><span class="sxs-lookup"><span data-stu-id="9fac3-370">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="9fac3-371">例外狀況是從標記協助程式的呼叫堆疊中擲回 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-371">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="9fac3-372">因此，整個 HTTP 要求都會失敗，除非開發人員程式碼明確攔截到例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9fac3-372">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="9fac3-373">在正常情況下，預先轉譯失敗時，繼續建立和轉譯元件並不合理，因為無法轉譯工作元件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-373">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="9fac3-374">若要容忍在預進行期間可能發生的錯誤，必須將錯誤處理邏輯放在可能會擲回例外狀況的元件內部。</span><span class="sxs-lookup"><span data-stu-id="9fac3-374">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="9fac3-375">使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 語句搭配錯誤處理和記錄。</span><span class="sxs-lookup"><span data-stu-id="9fac3-375">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="9fac3-376">您 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 可以將錯誤處理邏輯放在標記協助程式所呈現的元件中，而不是在語句中包裝標記協助程式 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-376">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="9fac3-377">進階案例</span><span class="sxs-lookup"><span data-stu-id="9fac3-377">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="9fac3-378">遞迴轉譯</span><span class="sxs-lookup"><span data-stu-id="9fac3-378">Recursive rendering</span></span>

<span data-ttu-id="9fac3-379">元件可以用遞迴方式進行嵌套。</span><span class="sxs-lookup"><span data-stu-id="9fac3-379">Components can be nested recursively.</span></span> <span data-ttu-id="9fac3-380">這適用于表示遞迴資料結構。</span><span class="sxs-lookup"><span data-stu-id="9fac3-380">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="9fac3-381">例如， `TreeNode` 元件可以 `TreeNode` 針對每個節點的子系呈現更多元件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-381">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="9fac3-382">以遞迴方式呈現時，請避免產生無限遞迴的程式碼模式：</span><span class="sxs-lookup"><span data-stu-id="9fac3-382">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="9fac3-383">不要以遞迴方式呈現包含迴圈的資料結構。</span><span class="sxs-lookup"><span data-stu-id="9fac3-383">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="9fac3-384">例如，不要轉譯其子系本身包含的樹狀節點。</span><span class="sxs-lookup"><span data-stu-id="9fac3-384">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="9fac3-385">請勿建立包含迴圈的版面配置鏈。</span><span class="sxs-lookup"><span data-stu-id="9fac3-385">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="9fac3-386">例如，請勿建立版面配置本身的配置。</span><span class="sxs-lookup"><span data-stu-id="9fac3-386">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="9fac3-387">不允許終端使用者違反遞迴不變數 (規則) 透過惡意資料輸入或 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="9fac3-387">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="9fac3-388">轉譯期間的無限迴圈：</span><span class="sxs-lookup"><span data-stu-id="9fac3-388">Infinite loops during rendering:</span></span>

* <span data-ttu-id="9fac3-389">導致轉譯程式永遠繼續。</span><span class="sxs-lookup"><span data-stu-id="9fac3-389">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="9fac3-390">相當於建立未結束的迴圈。</span><span class="sxs-lookup"><span data-stu-id="9fac3-390">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="9fac3-391">在這些情況下，受影響的 Blazor Server 線路會失敗，而且執行緒通常會嘗試：</span><span class="sxs-lookup"><span data-stu-id="9fac3-391">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="9fac3-392">無限期耗用作業系統所允許的 CPU 時間。</span><span class="sxs-lookup"><span data-stu-id="9fac3-392">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="9fac3-393">耗用無限數量的伺服器記憶體。</span><span class="sxs-lookup"><span data-stu-id="9fac3-393">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="9fac3-394">使用無限制的記憶體相當於未結束的迴圈在每次反復專案中將專案新增至集合的案例。</span><span class="sxs-lookup"><span data-stu-id="9fac3-394">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="9fac3-395">若要避免無限遞迴模式，請確定遞迴轉譯程式碼包含適當的停止條件。</span><span class="sxs-lookup"><span data-stu-id="9fac3-395">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="9fac3-396">自訂呈現樹狀結構邏輯</span><span class="sxs-lookup"><span data-stu-id="9fac3-396">Custom render tree logic</span></span>

<span data-ttu-id="9fac3-397">大部分的 Blazor 元件會實作為 Razor 元件檔 (`.razor`) ，並由架構編譯以產生可在上操作的邏輯， <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 以呈現其輸出。</span><span class="sxs-lookup"><span data-stu-id="9fac3-397">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="9fac3-398">不過，開發人員可以使用程式化 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> c # 程式碼手動執行邏輯。</span><span class="sxs-lookup"><span data-stu-id="9fac3-398">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="9fac3-399">如需詳細資訊，請參閱<xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。</span><span class="sxs-lookup"><span data-stu-id="9fac3-399">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="9fac3-400">手動轉譯樹狀結構產生器邏輯的使用會被視為 advanced 和 unsafe 案例，不建議用於一般元件開發。</span><span class="sxs-lookup"><span data-stu-id="9fac3-400">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="9fac3-401"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>撰寫程式碼時，開發人員必須保證程式碼的正確性。</span><span class="sxs-lookup"><span data-stu-id="9fac3-401">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="9fac3-402">例如，開發人員必須確保：</span><span class="sxs-lookup"><span data-stu-id="9fac3-402">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="9fac3-403">對 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的呼叫會正確平衡。</span><span class="sxs-lookup"><span data-stu-id="9fac3-403">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="9fac3-404">只會將屬性新增至正確的位置。</span><span class="sxs-lookup"><span data-stu-id="9fac3-404">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="9fac3-405">不正確的手動轉譯樹狀結構產生器邏輯可能會造成任何未定義的行為，包括損毀、伺服器當機和安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="9fac3-405">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="9fac3-406">請考慮以相同的複雜度層級手動呈現樹狀檢視器邏輯，並以與撰寫元件程式碼或 [Microsoft 中繼語言 (MSIL](/dotnet/standard/managed-code)相同的 *危險* 層級，手動) 的指示。</span><span class="sxs-lookup"><span data-stu-id="9fac3-406">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9fac3-407">其他資源</span><span class="sxs-lookup"><span data-stu-id="9fac3-407">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="9fac3-408">&dagger;適用于應用程式的 web API 後端應用程式的伺服器端 ASP.NET Core 應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="9fac3-408">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

::: zone-end
