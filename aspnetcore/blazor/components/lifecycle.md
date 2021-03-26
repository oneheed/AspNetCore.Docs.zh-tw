---
title: ASP.NET Core Razor 元件生命週期
author: guardrex
description: 瞭解 ASP.NET Core 元件的 Razor 生命週期，以及如何使用生命週期事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/25/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 529d9f6712e96eeea52cbf11efeb6116fdb34b75
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105554981"
---
# <a name="aspnet-core-razor-component-lifecycle"></a><span data-ttu-id="e762d-103">ASP.NET Core Razor 元件生命週期</span><span class="sxs-lookup"><span data-stu-id="e762d-103">ASP.NET Core Razor component lifecycle</span></span>

<span data-ttu-id="e762d-104">Razor元件會處理 Razor 一組同步和非同步生命週期方法中的元件生命週期事件。</span><span class="sxs-lookup"><span data-stu-id="e762d-104">The Razor component processes Razor component lifecycle events in a set of synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="e762d-105">您可以覆寫生命週期方法，以在元件初始化和轉譯期間于元件中執行其他作業。</span><span class="sxs-lookup"><span data-stu-id="e762d-105">The lifecycle methods can be overridden to perform additional operations in components during component initialization and rendering.</span></span>

## <a name="lifecycle-events"></a><span data-ttu-id="e762d-106">週期事件</span><span class="sxs-lookup"><span data-stu-id="e762d-106">Lifecycle events</span></span>

<span data-ttu-id="e762d-107">下圖說明 Razor 元件生命週期事件。</span><span class="sxs-lookup"><span data-stu-id="e762d-107">The following diagrams illustrate Razor component lifecycle events.</span></span> <span data-ttu-id="e762d-108">與生命週期事件相關聯的 c # 方法會使用本文的下列各節中的範例來定義。</span><span class="sxs-lookup"><span data-stu-id="e762d-108">The C# methods associated with the lifecycle events are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="e762d-109">元件生命週期事件：</span><span class="sxs-lookup"><span data-stu-id="e762d-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="e762d-110">如果第一次在要求時轉譯元件：</span><span class="sxs-lookup"><span data-stu-id="e762d-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="e762d-111">建立元件的實例。</span><span class="sxs-lookup"><span data-stu-id="e762d-111">Create the component's instance.</span></span>
   * <span data-ttu-id="e762d-112">執行屬性插入。</span><span class="sxs-lookup"><span data-stu-id="e762d-112">Perform property injection.</span></span> <span data-ttu-id="e762d-113">執行 [`SetParametersAsync`](#before-parameters-are-set-setparametersasync) 。</span><span class="sxs-lookup"><span data-stu-id="e762d-113">Run [`SetParametersAsync`](#before-parameters-are-set-setparametersasync).</span></span>
   * <span data-ttu-id="e762d-114">呼叫 [`OnInitialized{Async}`](#component-initialization-methods-oninitializedasync) 。</span><span class="sxs-lookup"><span data-stu-id="e762d-114">Call [`OnInitialized{Async}`](#component-initialization-methods-oninitializedasync).</span></span> <span data-ttu-id="e762d-115">如果傳回不完整 <xref:System.Threading.Tasks.Task> 的，則 <xref:System.Threading.Tasks.Task> 會等待，然後元件會保存。</span><span class="sxs-lookup"><span data-stu-id="e762d-115">If an incomplete <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>
1. <span data-ttu-id="e762d-116">呼叫 [`OnParametersSet{Async}`](#after-parameters-are-set-onparameterssetasync) 。</span><span class="sxs-lookup"><span data-stu-id="e762d-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set-onparameterssetasync).</span></span> <span data-ttu-id="e762d-117">如果傳回不完整 <xref:System.Threading.Tasks.Task> 的，則 <xref:System.Threading.Tasks.Task> 會等待，然後元件會保存。</span><span class="sxs-lookup"><span data-stu-id="e762d-117">If an incomplete <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>
1. <span data-ttu-id="e762d-118">針對所有同步工作和完整的呈現 <xref:System.Threading.Tasks.Task> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-118">Render for all synchronous work and complete <xref:System.Threading.Tasks.Task>s.</span></span>

![：：： No loc (Razor) ：：： component in：：： no-loc (Blazor) ：：：的元件生命週期事件](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="e762d-120">檔物件模型 (DOM) 事件處理：</span><span class="sxs-lookup"><span data-stu-id="e762d-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="e762d-121">執行事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="e762d-121">The event handler is run.</span></span>
1. <span data-ttu-id="e762d-122">如果傳回不完整 <xref:System.Threading.Tasks.Task> 的，則 <xref:System.Threading.Tasks.Task> 會等待，然後元件會保存。</span><span class="sxs-lookup"><span data-stu-id="e762d-122">If an incomplete <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>
1. <span data-ttu-id="e762d-123">針對所有同步工作和完整的呈現 <xref:System.Threading.Tasks.Task> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-123">Render for all synchronous work and complete <xref:System.Threading.Tasks.Task>s.</span></span>

![檔物件模型 (DOM) 事件處理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="e762d-125">`Render`生命週期：</span><span class="sxs-lookup"><span data-stu-id="e762d-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="e762d-126">避免元件上的進一步轉譯作業：</span><span class="sxs-lookup"><span data-stu-id="e762d-126">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="e762d-127">在第一次呈現之後。</span><span class="sxs-lookup"><span data-stu-id="e762d-127">After the first render.</span></span>
   * <span data-ttu-id="e762d-128">當 [`ShouldRender`](#suppress-ui-refreshing-shouldrender) 為時 `false` 。</span><span class="sxs-lookup"><span data-stu-id="e762d-128">When [`ShouldRender`](#suppress-ui-refreshing-shouldrender) is `false`.</span></span>
1. <span data-ttu-id="e762d-129">建立轉譯樹狀結構差異 (差異) 並呈現元件。</span><span class="sxs-lookup"><span data-stu-id="e762d-129">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="e762d-130">等待 DOM 進行更新。</span><span class="sxs-lookup"><span data-stu-id="e762d-130">Await the DOM to update.</span></span>
1. <span data-ttu-id="e762d-131">呼叫 [`OnAfterRender{Async}`](#after-component-render-onafterrenderasync) 。</span><span class="sxs-lookup"><span data-stu-id="e762d-131">Call [`OnAfterRender{Async}`](#after-component-render-onafterrenderasync).</span></span>

![轉譯生命週期](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="e762d-133">開發人員呼叫以 [`StateHasChanged`](#state-changes-statehaschanged) 產生轉譯。</span><span class="sxs-lookup"><span data-stu-id="e762d-133">Developer calls to [`StateHasChanged`](#state-changes-statehaschanged) result in a render.</span></span> <span data-ttu-id="e762d-134">如需詳細資訊，請參閱<xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="e762d-134">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="before-parameters-are-set-setparametersasync"></a><span data-ttu-id="e762d-135">設定參數之前 (`SetParametersAsync`) </span><span class="sxs-lookup"><span data-stu-id="e762d-135">Before parameters are set (`SetParametersAsync`)</span></span>

<span data-ttu-id="e762d-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 在轉譯樹狀結構中或從路由參數，設定元件的父系所提供的參數。</span><span class="sxs-lookup"><span data-stu-id="e762d-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span>

<span data-ttu-id="e762d-137">方法的 <xref:Microsoft.AspNetCore.Components.ParameterView> 參數會在每次呼叫時包含元件的一組 [元件參數](xref:blazor/components/index#parameters) 值 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-137">The method's <xref:Microsoft.AspNetCore.Components.ParameterView> parameter contains the set of [component parameter](xref:blazor/components/index#parameters) values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span> <span data-ttu-id="e762d-138">藉由覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 方法，開發人員程式碼就可以直接與 <xref:Microsoft.AspNetCore.Components.ParameterView> 的參數互動。</span><span class="sxs-lookup"><span data-stu-id="e762d-138">By overriding the <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method, developer code can interact directly with <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="e762d-139">的預設實 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 值會設定每個屬性的值，其具有的 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或[ `[CascadingParameter]` 屬性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)在中具有對應的值 <xref:Microsoft.AspNetCore.Components.ParameterView> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-139">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="e762d-140">在中沒有對應值的參數 <xref:Microsoft.AspNetCore.Components.ParameterView> 會保持不變。</span><span class="sxs-lookup"><span data-stu-id="e762d-140">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="e762d-141">如果 [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) 未叫用，開發人員程式碼就能以任何需要的方式解讀傳入參數的值。</span><span class="sxs-lookup"><span data-stu-id="e762d-141">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, developer code can interpret the incoming parameters' values in any way required.</span></span> <span data-ttu-id="e762d-142">例如，不需要將傳入參數指派給類別的屬性。</span><span class="sxs-lookup"><span data-stu-id="e762d-142">For example, there's no requirement to assign the incoming parameters to the properties of the class.</span></span>

<span data-ttu-id="e762d-143">如果在開發人員程式碼中提供事件處理常式，請在處置時解除其的掛接。</span><span class="sxs-lookup"><span data-stu-id="e762d-143">If event handlers are provided in developer code, unhook them on disposal.</span></span> <span data-ttu-id="e762d-144">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="e762d-144">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

<span data-ttu-id="e762d-145">在下列範例中， <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> 如果剖析的路由參數為成功，則將 `Param` 參數的值指派給 `value` `Param` 。</span><span class="sxs-lookup"><span data-stu-id="e762d-145">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="e762d-146">如果 `value` 沒有 `null` ，此值就會由元件顯示。</span><span class="sxs-lookup"><span data-stu-id="e762d-146">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="e762d-147">雖然 [路由參數比對不區分大小寫](xref:blazor/fundamentals/routing#route-parameters)，但 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 僅符合路由範本中區分大小寫的參數名稱。</span><span class="sxs-lookup"><span data-stu-id="e762d-147">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="e762d-148">下列範例需要在 `/{Param?}` 路由範本中使用，才能使用來取得值 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> ，而不是 `/{param?}` 。</span><span class="sxs-lookup"><span data-stu-id="e762d-148">The following example requires the use of `/{Param?}` in the route template in order to get the value with <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A>, not `/{param?}`.</span></span> <span data-ttu-id="e762d-149">如果 `/{param?}` 在此案例中使用，則會傳回， <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> `false` 而且 `message` 不會設定為任何一個 `message` 字串。</span><span class="sxs-lookup"><span data-stu-id="e762d-149">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either `message` string.</span></span>

<span data-ttu-id="e762d-150">`Pages/SetParamsAsync.razor`:</span><span class="sxs-lookup"><span data-stu-id="e762d-150">`Pages/SetParamsAsync.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/SetParamsAsync.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/SetParamsAsync.razor)]

::: moniker-end

## <a name="component-initialization-methods-oninitializedasync"></a><span data-ttu-id="e762d-151">元件初始化方法 (`OnInitialized{Async}`) </span><span class="sxs-lookup"><span data-stu-id="e762d-151">Component initialization methods (`OnInitialized{Async}`)</span></span>

<span data-ttu-id="e762d-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>當元件在中收到其初始參數之後初始化時，就會叫用和 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> are invoked when the component is initialized after having received its initial parameters in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span>

<span data-ttu-id="e762d-153">針對同步作業，覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ：</span><span class="sxs-lookup"><span data-stu-id="e762d-153">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

<span data-ttu-id="e762d-154">`Pages/OnInit.razor`:</span><span class="sxs-lookup"><span data-stu-id="e762d-154">`Pages/OnInit.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/OnInit.razor?highlight=8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/OnInit.razor?highlight=8)]

::: moniker-end

<span data-ttu-id="e762d-155">若要執行非同步作業，請覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 並使用 [`await`](/dotnet/csharp/language-reference/operators/await) 運算子：</span><span class="sxs-lookup"><span data-stu-id="e762d-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="e762d-156">Blazor 將 [其內容呼叫呈現](xref:blazor/fundamentals/signalr#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *兩次* 的應用程式：</span><span class="sxs-lookup"><span data-stu-id="e762d-156">Blazor apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="e762d-157">一開始是以靜態方式將元件轉譯為頁面的一部分。</span><span class="sxs-lookup"><span data-stu-id="e762d-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="e762d-158">第二次當瀏覽器呈現元件時。</span><span class="sxs-lookup"><span data-stu-id="e762d-158">A second time when the browser renders the component.</span></span>

<span data-ttu-id="e762d-159">若要防止開發人員 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 程式碼在應用程式中進行自呈現時執行兩次 Blazor Server ，請參閱 [自 [呈現後](#stateful-reconnection-after-prerendering) 的可設定狀態] 區段</span><span class="sxs-lookup"><span data-stu-id="e762d-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice when prerendering in Blazor Server apps, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span> <span data-ttu-id="e762d-160">雖然本節中的內容著重于和具狀態的重新連線 Blazor Server ，但在裝載 SignalR 的應用程式 () 的案例中，會 Blazor WebAssembly <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> 涉及類似的條件和方法，以避免執行開發人員程式碼兩次。</span><span class="sxs-lookup"><span data-stu-id="e762d-160">Although the content in the section focuses on Blazor Server and stateful SignalR *reconnection*, the scenario for prerendering in hosted Blazor WebAssembly apps (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) involves similar conditions and approaches to prevent executing developer code twice.</span></span> <span data-ttu-id="e762d-161">已針對 ASP.NET Core 6.0 版本規劃 *新的狀態保留功能* ，可改善在預進行期間的初始化程式碼執行管理。</span><span class="sxs-lookup"><span data-stu-id="e762d-161">A *new state preservation feature* is planned for the ASP.NET Core 6.0 release that will improve the management of initialization code execution during prerendering.</span></span>

<span data-ttu-id="e762d-162">當 Blazor 應用程式進行可呈現時，無法執行某些動作，例如呼叫 JavaScript (JS interop) 。</span><span class="sxs-lookup"><span data-stu-id="e762d-162">While a Blazor app is prerendering, certain actions, such as calling into JavaScript (JS interop), aren't possible.</span></span> <span data-ttu-id="e762d-163">元件在資源清單時可能需要以不同的方式呈現。</span><span class="sxs-lookup"><span data-stu-id="e762d-163">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="e762d-164">如需詳細資訊，請參閱在 [應用程式的 [已呈現時](#detect-when-the-app-is-prerendering) 偵測] 區段。</span><span class="sxs-lookup"><span data-stu-id="e762d-164">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="e762d-165">如果在開發人員程式碼中提供事件處理常式，請在處置時解除其的掛接。</span><span class="sxs-lookup"><span data-stu-id="e762d-165">If event handlers are provided in developer code, unhook them on disposal.</span></span> <span data-ttu-id="e762d-166">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="e762d-166">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

## <a name="after-parameters-are-set-onparameterssetasync"></a><span data-ttu-id="e762d-167"> (設定參數之後 `OnParametersSet{Async}`) </span><span class="sxs-lookup"><span data-stu-id="e762d-167">After parameters are set (`OnParametersSet{Async}`)</span></span>

<span data-ttu-id="e762d-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 稱為：</span><span class="sxs-lookup"><span data-stu-id="e762d-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> are called:</span></span>

* <span data-ttu-id="e762d-169">在或中初始化元件之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-169">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="e762d-170">當父元件轉譯中並提供時：</span><span class="sxs-lookup"><span data-stu-id="e762d-170">When the parent component rerenders and supplies:</span></span>
  * <span data-ttu-id="e762d-171">當至少一個參數已變更時，已知的基本不可變類型。</span><span class="sxs-lookup"><span data-stu-id="e762d-171">Known primitive immutable types when at least one parameter has changed.</span></span>
  * <span data-ttu-id="e762d-172">複雜類型參數。</span><span class="sxs-lookup"><span data-stu-id="e762d-172">Complex-typed parameters.</span></span> <span data-ttu-id="e762d-173">架構無法得知複雜型別參數的值是否在內部改變，因此當有一或多個複雜類型參數存在時，架構一律會將參數集視為變更。</span><span class="sxs-lookup"><span data-stu-id="e762d-173">The framework can't know whether the values of a complex-typed parameter have mutated internally, so the framework always treats the parameter set as changed when one or more complex-typed parameters are present.</span></span>

<span data-ttu-id="e762d-174">針對下列範例元件，請流覽至元件的頁面，網址為：</span><span class="sxs-lookup"><span data-stu-id="e762d-174">For the following example component, navigate to the component's page at a URL:</span></span>

* <span data-ttu-id="e762d-175">使用接收的開始日期 `StartDate` ： `/on-parameters-set/2021-03-19`</span><span class="sxs-lookup"><span data-stu-id="e762d-175">With a start date that's received by `StartDate`: `/on-parameters-set/2021-03-19`</span></span>
* <span data-ttu-id="e762d-176">如果沒有開始日期， `StartDate` 則會將目前當地時間的值指派給其中： `/on-parameters-set`</span><span class="sxs-lookup"><span data-stu-id="e762d-176">Without a start date, where `StartDate` is assigned a value of the current local time: `/on-parameters-set`</span></span>

<span data-ttu-id="e762d-177">`Pages/OnParamsSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="e762d-177">`Pages/OnParamsSet.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="e762d-178">在元件路由中，您無法同時 <xref:System.DateTime> 使用[路由條件約束 `datetime` ](xref:blazor/fundamentals/routing#route-constraints)來限制參數，並[將參數設為選擇性](xref:blazor/fundamentals/routing#route-parameters)。</span><span class="sxs-lookup"><span data-stu-id="e762d-178">In a component route, it isn't possible to both constrain a <xref:System.DateTime> parameter with the [route constraint `datetime`](xref:blazor/fundamentals/routing#route-constraints) and [make the parameter optional](xref:blazor/fundamentals/routing#route-parameters).</span></span> <span data-ttu-id="e762d-179">因此，下列 `OnParamsSet` 元件會使用兩個指示詞 [`@page`](xref:mvc/views/razor#page) 來處理路由，而不在 URL 中提供任何日期區段。</span><span class="sxs-lookup"><span data-stu-id="e762d-179">Therefore, the following `OnParamsSet` component uses two [`@page`](xref:mvc/views/razor#page) directives to handle routing with and without a supplied date segment in the URL.</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/OnParamsSet.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/OnParamsSet.razor)]

::: moniker-end

<span data-ttu-id="e762d-180">套用參數和屬性值的非同步工作必須在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命週期事件期間發生：</span><span class="sxs-lookup"><span data-stu-id="e762d-180">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

<span data-ttu-id="e762d-181">如果在開發人員程式碼中提供事件處理常式，請在處置時解除其的掛接。</span><span class="sxs-lookup"><span data-stu-id="e762d-181">If event handlers are provided in developer code, unhook them on disposal.</span></span> <span data-ttu-id="e762d-182">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="e762d-182">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

<span data-ttu-id="e762d-183">如需路由參數和條件約束的詳細資訊，請參閱 <xref:blazor/fundamentals/routing> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-183">For more information on route parameters and constraints, see <xref:blazor/fundamentals/routing>.</span></span>

## <a name="after-component-render-onafterrenderasync"></a><span data-ttu-id="e762d-184">元件轉譯 (之後 `OnAfterRender{Async}`) </span><span class="sxs-lookup"><span data-stu-id="e762d-184">After component render (`OnAfterRender{Async}`)</span></span>

<span data-ttu-id="e762d-185"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 元件完成轉譯之後，會呼叫和。</span><span class="sxs-lookup"><span data-stu-id="e762d-185"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="e762d-186">此時會填入元素和元件參考。</span><span class="sxs-lookup"><span data-stu-id="e762d-186">Element and component references are populated at this point.</span></span> <span data-ttu-id="e762d-187">您可以使用此階段，以轉譯的內容（例如與轉譯的 DOM 元素互動的 JS interop 呼叫）來執行其他初始化步驟。</span><span class="sxs-lookup"><span data-stu-id="e762d-187">Use this stage to perform additional initialization steps with the rendered content, such as JS interop calls that interact with the rendered DOM elements.</span></span>

<span data-ttu-id="e762d-188">`firstRender`And 的參數 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ：</span><span class="sxs-lookup"><span data-stu-id="e762d-188">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>:</span></span>

* <span data-ttu-id="e762d-189">會 `true` 在第一次轉譯元件實例時設定為。</span><span class="sxs-lookup"><span data-stu-id="e762d-189">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="e762d-190">可以用來確保只執行一次初始化工作。</span><span class="sxs-lookup"><span data-stu-id="e762d-190">Can be used to ensure that initialization work is only performed once.</span></span>

<span data-ttu-id="e762d-191">`Pages/AfterRender.razor`:</span><span class="sxs-lookup"><span data-stu-id="e762d-191">`Pages/AfterRender.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/AfterRender.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/AfterRender.razor)]

::: moniker-end

<span data-ttu-id="e762d-192">轉譯後立即的非同步工作必須在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命週期事件期間發生：</span><span class="sxs-lookup"><span data-stu-id="e762d-192">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event:</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

<span data-ttu-id="e762d-193">即使您 <xref:System.Threading.Tasks.Task> 從傳回 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ，架構也不會在該工作完成之後，為您的元件排程進一步的轉譯迴圈。</span><span class="sxs-lookup"><span data-stu-id="e762d-193">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="e762d-194">這是為了避免無限呈現迴圈。</span><span class="sxs-lookup"><span data-stu-id="e762d-194">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="e762d-195">這與其他生命週期方法不同，後者會在傳回完成時排程進一步的轉譯週期 <xref:System.Threading.Tasks.Task> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-195">This is different from the other lifecycle methods, which schedule a further render cycle once a returned <xref:System.Threading.Tasks.Task> completes.</span></span>

<span data-ttu-id="e762d-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 而且不會在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *伺服器上的未處理常式期間呼叫*。</span><span class="sxs-lookup"><span data-stu-id="e762d-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="e762d-197">當元件在預先轉譯後以互動方式轉譯時，會呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="e762d-197">The methods are called when the component is rendered interactively after prerendering.</span></span> <span data-ttu-id="e762d-198">當應用程式 prerenders 時：</span><span class="sxs-lookup"><span data-stu-id="e762d-198">When the app prerenders:</span></span>

1. <span data-ttu-id="e762d-199">元件會在伺服器上執行，以在 HTTP 回應中產生一些靜態 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="e762d-199">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="e762d-200">在這個階段中 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 不會呼叫。</span><span class="sxs-lookup"><span data-stu-id="e762d-200">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="e762d-201">當 Blazor 腳本 (`blazor.webassembly.js` 或 `blazor.server.js`) 在瀏覽器中啟動時，元件會以互動式轉譯模式重新開機。</span><span class="sxs-lookup"><span data-stu-id="e762d-201">When the Blazor script (`blazor.webassembly.js` or `blazor.server.js`) start in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="e762d-202">在重新開機元件之後， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **會** 呼叫，因為應用程式不會再使用已進行的重新呈現階段。</span><span class="sxs-lookup"><span data-stu-id="e762d-202">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't in the prerendering phase any longer.</span></span>

<span data-ttu-id="e762d-203">如果在開發人員程式碼中提供事件處理常式，請在處置時解除其的掛接。</span><span class="sxs-lookup"><span data-stu-id="e762d-203">If event handlers are provided in developer code, unhook them on disposal.</span></span> <span data-ttu-id="e762d-204">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="e762d-204">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

## <a name="suppress-ui-refreshing-shouldrender"></a><span data-ttu-id="e762d-205">隱藏 UI 重新整理 (`ShouldRender`) </span><span class="sxs-lookup"><span data-stu-id="e762d-205">Suppress UI refreshing (`ShouldRender`)</span></span>

<span data-ttu-id="e762d-206"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 每次轉譯元件時，就會呼叫。</span><span class="sxs-lookup"><span data-stu-id="e762d-206"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time a component is rendered.</span></span> <span data-ttu-id="e762d-207">覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以管理 UI 重新整理。</span><span class="sxs-lookup"><span data-stu-id="e762d-207">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to manage UI refreshing.</span></span> <span data-ttu-id="e762d-208">如果執行傳回 `true` ，就會重新整理 UI。</span><span class="sxs-lookup"><span data-stu-id="e762d-208">If the implementation returns `true`, the UI is refreshed.</span></span>

<span data-ttu-id="e762d-209">即使覆 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 寫，仍一律會呈現元件。</span><span class="sxs-lookup"><span data-stu-id="e762d-209">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="e762d-210">`Pages/ControlRender.razor`:</span><span class="sxs-lookup"><span data-stu-id="e762d-210">`Pages/ControlRender.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/ControlRender.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/ControlRender.razor)]

::: moniker-end

<span data-ttu-id="e762d-211">如需有關的效能最佳作法的詳細資訊 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> ，請參閱 <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-211">For more information on performance best practices pertaining to <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes-statehaschanged"></a><span data-ttu-id="e762d-212">狀態變更 (`StateHasChanged`) </span><span class="sxs-lookup"><span data-stu-id="e762d-212">State changes (`StateHasChanged`)</span></span>

<span data-ttu-id="e762d-213"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知元件其狀態已變更。</span><span class="sxs-lookup"><span data-stu-id="e762d-213"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="e762d-214">當適用時，呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會導致元件保存。</span><span class="sxs-lookup"><span data-stu-id="e762d-214">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="e762d-215"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動針對 <xref:Microsoft.AspNetCore.Components.EventCallback> 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="e762d-215"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="e762d-216">如需事件回呼的詳細資訊，請參閱 <xref:blazor/components/event-handling#eventcallback> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-216">For more information on event callbacks, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="e762d-217">如需元件轉譯和何時要呼叫的詳細資訊 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> ，請參閱 <xref:blazor/components/rendering> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-217">For more information on component rendering and when to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="e762d-218">在轉譯時處理未完成的非同步動作</span><span class="sxs-lookup"><span data-stu-id="e762d-218">Handle incomplete async actions at render</span></span>

<span data-ttu-id="e762d-219">在呈現元件之前，在生命週期事件中執行的非同步動作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="e762d-219">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="e762d-220">`null`當生命週期方法執行時，物件可能會或未完全填入資料。</span><span class="sxs-lookup"><span data-stu-id="e762d-220">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="e762d-221">提供轉譯邏輯來確認物件已初始化。</span><span class="sxs-lookup"><span data-stu-id="e762d-221">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="e762d-222">轉譯預留位置 UI 元素 (例如，在物件為時) 載入訊息 `null` 。</span><span class="sxs-lookup"><span data-stu-id="e762d-222">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="e762d-223">在 `FetchData` 範本的元件中 Blazor ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 會覆寫以非同步方式接收預測資料 (`forecasts`) 。</span><span class="sxs-lookup"><span data-stu-id="e762d-223">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="e762d-224">當 `forecasts` 為時 `null` ，就會向使用者顯示載入訊息。</span><span class="sxs-lookup"><span data-stu-id="e762d-224">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="e762d-225">在 `Task` 傳回之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ，元件會以更新狀態保存。</span><span class="sxs-lookup"><span data-stu-id="e762d-225">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="e762d-226">`Pages/FetchData.razor` 在 Blazor Server 範本中：</span><span class="sxs-lookup"><span data-stu-id="e762d-226">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a><span data-ttu-id="e762d-227">處理錯誤</span><span class="sxs-lookup"><span data-stu-id="e762d-227">Handle errors</span></span>

<span data-ttu-id="e762d-228">如需在生命週期方法執行期間處理錯誤的詳細資訊，請參閱 <xref:blazor/fundamentals/handle-errors> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-228">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="e762d-229">以具狀態重新連接後重新連線</span><span class="sxs-lookup"><span data-stu-id="e762d-229">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="e762d-230">在 Blazor Server 應用程式中 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，當為時 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> ，元件最初會以靜態方式轉譯為頁面的一部分。</span><span class="sxs-lookup"><span data-stu-id="e762d-230">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="e762d-231">當瀏覽器將 SignalR 連接重新建立回伺服器之後，就會 *再次* 轉譯該元件並進行互動。</span><span class="sxs-lookup"><span data-stu-id="e762d-231">Once the browser establishes a SignalR connection back to the server, the component is rendered *again* and interactive.</span></span> <span data-ttu-id="e762d-232">如果 [`OnInitialized{Async}`](#component-initialization-methods-oninitializedasync) 有初始化元件的生命週期方法，方法會執行 *兩次*：</span><span class="sxs-lookup"><span data-stu-id="e762d-232">If the [`OnInitialized{Async}`](#component-initialization-methods-oninitializedasync) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="e762d-233">以靜態方式資源清單元件時。</span><span class="sxs-lookup"><span data-stu-id="e762d-233">When the component is prerendered statically.</span></span>
* <span data-ttu-id="e762d-234">建立伺服器連接之後。</span><span class="sxs-lookup"><span data-stu-id="e762d-234">After the server connection has been established.</span></span>

<span data-ttu-id="e762d-235">這可能會導致在元件最後轉譯時，UI 中所顯示的資料有明顯的變更。</span><span class="sxs-lookup"><span data-stu-id="e762d-235">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span> <span data-ttu-id="e762d-236">若要避免在應用程式中使用這種雙重轉譯行為 Blazor Server ，請傳入識別碼以在預先轉譯期間快取狀態，並在預先轉譯之後取得狀態。</span><span class="sxs-lookup"><span data-stu-id="e762d-236">To avoid this double-rendering behavior in a Blazor Server app, pass in an identifier to cache the state during prerendering and to retrieve the state after prerendering.</span></span>

<span data-ttu-id="e762d-237">下列程式碼示範 `WeatherForecastService` 以範本為基礎的應用程式中已更新 Blazor Server ，可避免雙重轉譯。</span><span class="sxs-lookup"><span data-stu-id="e762d-237">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering.</span></span> <span data-ttu-id="e762d-238">在下列範例中，等候的 <xref:System.Threading.Tasks.Task.Delay%2A> (`await Task.Delay(...)`) 會模擬短暫延遲，然後再從方法傳回資料 `GetForecastAsync` 。</span><span class="sxs-lookup"><span data-stu-id="e762d-238">In the following example, the awaited <xref:System.Threading.Tasks.Task.Delay%2A> (`await Task.Delay(...)`) simulates a short delay before returning data from the `GetForecastAsync` method.</span></span>

<span data-ttu-id="e762d-239">`WeatherForecastService.cs`:</span><span class="sxs-lookup"><span data-stu-id="e762d-239">`WeatherForecastService.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/lifecycle/WeatherForecastService.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/lifecycle/WeatherForecastService.cs)]

::: moniker-end

<span data-ttu-id="e762d-240">如需的詳細資訊 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，請參閱 <xref:blazor/fundamentals/signalr#render-mode> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-240">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

<span data-ttu-id="e762d-241">雖然本節中的內容著重在和具狀態的重新連線 Blazor Server ，但在裝載 SignalR 的 Blazor WebAssembly 應用程式中 () 的案例 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> 也牽涉到類似的條件和方法，以避免執行開發人員程式碼兩次。</span><span class="sxs-lookup"><span data-stu-id="e762d-241">Although the content in this section focuses on Blazor Server and stateful SignalR *reconnection*, the scenario for prerendering in hosted Blazor WebAssembly apps (<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered>) involves similar conditions and approaches to prevent executing developer code twice.</span></span> <span data-ttu-id="e762d-242">已針對 ASP.NET Core 6.0 版本規劃 *新的狀態保留功能* ，可改善在預進行期間的初始化程式碼執行管理。</span><span class="sxs-lookup"><span data-stu-id="e762d-242">A *new state preservation feature* is planned for the ASP.NET Core 6.0 release that will improve the management of initialization code execution during prerendering.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="e762d-243">偵測應用程式何時進行呈現</span><span class="sxs-lookup"><span data-stu-id="e762d-243">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="e762d-244">元件處置方式 `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="e762d-244">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="e762d-245">如果元件已執行 <xref:System.IDisposable> ，則在從 UI 中移除元件時，架構會呼叫 [處置方法](/dotnet/standard/garbage-collection/implementing-dispose) ，而無法釋放非受控資源。</span><span class="sxs-lookup"><span data-stu-id="e762d-245">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="e762d-246">處置可以在任何時間進行，包括 [元件初始化](#component-initialization-methods-oninitializedasync)期間。</span><span class="sxs-lookup"><span data-stu-id="e762d-246">Disposal can occur at any time, including during [component initialization](#component-initialization-methods-oninitializedasync).</span></span> <span data-ttu-id="e762d-247">下列元件會使用指示詞來 <xref:System.IDisposable> [`@implements`](xref:mvc/views/razor#implements) Razor 執行：</span><span class="sxs-lookup"><span data-stu-id="e762d-247">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

<span data-ttu-id="e762d-248">如果物件需要處置，則在呼叫時，可以使用 lambda 來處置物件 <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-248">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called.</span></span> <span data-ttu-id="e762d-249">下列範例會出現在 <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> 文章中，並示範如何使用 lambda 運算式來處置 <xref:System.Timers.Timer> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-249">The following example appears in the <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> article and demonstrates the use of a lambda expression for the disposal of a <xref:System.Timers.Timer>.</span></span>

<span data-ttu-id="e762d-250">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="e762d-250">`Pages/CounterWithTimerDisposal.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/CounterWithTimerDisposal.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/CounterWithTimerDisposal.razor)]

::: moniker-end

<span data-ttu-id="e762d-251">針對非同步處置工作，請使用 `DisposeAsync` 取代 <xref:System.IDisposable.Dispose> ：</span><span class="sxs-lookup"><span data-stu-id="e762d-251">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="e762d-252"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>不支援在中呼叫 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="e762d-252">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="e762d-253"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能會在卸載轉譯器的過程中叫用，因此不支援在該時間點要求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="e762d-253"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="e762d-254">取消訂閱來自 .NET 事件的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="e762d-254">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="e762d-255">下列[ Blazor 表單](xref:blazor/forms-validation)範例示範如何在方法中取消訂閱事件處理常式 `Dispose` ：</span><span class="sxs-lookup"><span data-stu-id="e762d-255">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="e762d-256">私用欄位和 lambda 方法</span><span class="sxs-lookup"><span data-stu-id="e762d-256">Private field and lambda approach</span></span>

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

* <span data-ttu-id="e762d-257">私用方法方法</span><span class="sxs-lookup"><span data-stu-id="e762d-257">Private method approach</span></span>

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="e762d-258">私用欄位和 lambda 方法</span><span class="sxs-lookup"><span data-stu-id="e762d-258">Private field and lambda approach</span></span>

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

* <span data-ttu-id="e762d-259">私用方法方法</span><span class="sxs-lookup"><span data-stu-id="e762d-259">Private method approach</span></span>

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

::: moniker-end

<span data-ttu-id="e762d-260">使用 [匿名](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)函式、方法或運算式時，不需要執行 <xref:System.IDisposable> 和取消訂閱委派。</span><span class="sxs-lookup"><span data-stu-id="e762d-260">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods, or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="e762d-261">不過， **當公開事件的物件存留時間超過註冊委派之元件的存留期時**，無法取消訂閱委派會是一個問題。</span><span class="sxs-lookup"><span data-stu-id="e762d-261">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="e762d-262">發生這種情況時，會造成記憶體流失的結果，因為已註冊的委派會讓原始物件保持運作。</span><span class="sxs-lookup"><span data-stu-id="e762d-262">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="e762d-263">因此，當您知道事件委派會快速處置時，請使用下列方法。</span><span class="sxs-lookup"><span data-stu-id="e762d-263">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="e762d-264">如果不確定需要處置的物件存留期，請訂閱委派方法並適當地處置委派，如先前的範例所示。</span><span class="sxs-lookup"><span data-stu-id="e762d-264">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the earlier examples show.</span></span>

* <span data-ttu-id="e762d-265">匿名 lambda 方法方法 (不需要明確處置) ：</span><span class="sxs-lookup"><span data-stu-id="e762d-265">Anonymous lambda method approach (explicit disposal not required):</span></span>

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* <span data-ttu-id="e762d-266">匿名 lambda 運算式方法 (不需要明確處置) ：</span><span class="sxs-lookup"><span data-stu-id="e762d-266">Anonymous lambda expression approach (explicit disposal not required):</span></span>

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  <span data-ttu-id="e762d-267">本文中會顯示上述程式碼中具有匿名 lambda 運算式的完整範例 <xref:blazor/forms-validation#validator-components> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-267">The full example of the preceding code with anonymous lambda expressions appears in the <xref:blazor/forms-validation#validator-components> article.</span></span>

<span data-ttu-id="e762d-268">如需詳細資訊，請參閱在執行和方法時 [清除非受控資源](/dotnet/standard/garbage-collection/unmanaged) 和後續的主題 `Dispose` `DisposeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="e762d-268">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="e762d-269">可取消的背景工作</span><span class="sxs-lookup"><span data-stu-id="e762d-269">Cancelable background work</span></span>

<span data-ttu-id="e762d-270">元件通常會執行長時間執行的背景工作，例如 () 進行網路呼叫， <xref:System.Net.Http.HttpClient> 以及與資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="e762d-270">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="e762d-271">在幾種情況下，最好停止背景工作來節省系統資源。</span><span class="sxs-lookup"><span data-stu-id="e762d-271">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="e762d-272">例如，當使用者離開元件時，不會自動停止背景非同步作業。</span><span class="sxs-lookup"><span data-stu-id="e762d-272">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="e762d-273">背景工作專案可能需要取消的其他原因包括：</span><span class="sxs-lookup"><span data-stu-id="e762d-273">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="e762d-274">執行中的背景工作已啟動，但有錯誤的輸入資料或處理參數。</span><span class="sxs-lookup"><span data-stu-id="e762d-274">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="e762d-275">目前執行中的背景工作專案集必須取代為一組新的工作專案。</span><span class="sxs-lookup"><span data-stu-id="e762d-275">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="e762d-276">目前執行中工作的優先順序必須變更。</span><span class="sxs-lookup"><span data-stu-id="e762d-276">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="e762d-277">您必須關閉應用程式，才能重新部署伺服器。</span><span class="sxs-lookup"><span data-stu-id="e762d-277">The app must be shut down for server redeployment.</span></span>
* <span data-ttu-id="e762d-278">伺服器資源會受到限制，強制背景工作專案的重新排程。</span><span class="sxs-lookup"><span data-stu-id="e762d-278">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="e762d-279">若要在元件中執行可取消的背景工作模式：</span><span class="sxs-lookup"><span data-stu-id="e762d-279">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="e762d-280">使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-280">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="e762d-281">手動取消權杖時，若要處理 [元件](#component-disposal-with-idisposable) 和任何點取消，請呼叫 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示應取消背景工作。</span><span class="sxs-lookup"><span data-stu-id="e762d-281">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="e762d-282">在非同步呼叫傳回之後，請呼叫 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> 權杖上的。</span><span class="sxs-lookup"><span data-stu-id="e762d-282">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="e762d-283">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e762d-283">In the following example:</span></span>

* <span data-ttu-id="e762d-284">`await Task.Delay(5000, cts.Token);` 表示長時間執行的非同步背景工作。</span><span class="sxs-lookup"><span data-stu-id="e762d-284">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="e762d-285">`BackgroundResourceMethod` 表示長時間執行的背景方法，如果在 `Resource` 呼叫方法之前處置，則不應該啟動。</span><span class="sxs-lookup"><span data-stu-id="e762d-285">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

<span data-ttu-id="e762d-286">`Pages/BackgroundWork.razor`:</span><span class="sxs-lookup"><span data-stu-id="e762d-286">`Pages/BackgroundWork.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/BackgroundWork.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/BackgroundWork.razor)]

::: moniker-end

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="e762d-287">Blazor Server 重新連接事件</span><span class="sxs-lookup"><span data-stu-id="e762d-287">Blazor Server reconnection events</span></span>

<span data-ttu-id="e762d-288">本文所涵蓋的元件生命週期事件，與重新[ Blazor Server 連接事件處理常式](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui)分開運作。</span><span class="sxs-lookup"><span data-stu-id="e762d-288">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="e762d-289">當 Blazor Server 應用程式失去其 SignalR 與用戶端的連線時，只會中斷 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="e762d-289">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="e762d-290">重新建立連接時，會繼續 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="e762d-290">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="e762d-291">如需線路處理程式事件和設定的詳細資訊，請參閱 <xref:blazor/fundamentals/signalr> 。</span><span class="sxs-lookup"><span data-stu-id="e762d-291">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>
