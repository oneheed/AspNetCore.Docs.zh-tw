---
title: 'ASP.NET Core Blazor 生命週期'
author: guardrex
description: '瞭解如何 Razor 在 ASP.NET Core apps 中使用元件生命週期方法 Blazor 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/lifecycle
ms.openlocfilehash: 08fc393160e0a7396963901e2add3b44fc7b02b9
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507997"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="e6e09-103">ASP.NET Core Blazor 生命週期</span><span class="sxs-lookup"><span data-stu-id="e6e09-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="e6e09-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e6e09-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e6e09-105">此 Blazor 架構包含同步和非同步生命週期方法。</span><span class="sxs-lookup"><span data-stu-id="e6e09-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="e6e09-106">覆寫生命週期方法，以在元件初始化和轉譯期間對元件執行額外的作業。</span><span class="sxs-lookup"><span data-stu-id="e6e09-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="e6e09-107">下圖說明 Blazor 生命週期。</span><span class="sxs-lookup"><span data-stu-id="e6e09-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="e6e09-108">生命週期方法是使用本文的下列各節中的範例所定義。</span><span class="sxs-lookup"><span data-stu-id="e6e09-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="e6e09-109">元件生命週期事件：</span><span class="sxs-lookup"><span data-stu-id="e6e09-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="e6e09-110">如果第一次在要求時轉譯元件：</span><span class="sxs-lookup"><span data-stu-id="e6e09-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="e6e09-111">建立元件的實例。</span><span class="sxs-lookup"><span data-stu-id="e6e09-111">Create the component's instance.</span></span>
   * <span data-ttu-id="e6e09-112">執行屬性插入。</span><span class="sxs-lookup"><span data-stu-id="e6e09-112">Perform property injection.</span></span> <span data-ttu-id="e6e09-113">執行 [`SetParametersAsync`](#before-parameters-are-set) 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="e6e09-114">呼叫 [`OnInitialized{Async}`](#component-initialization-methods) 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="e6e09-115">如果 <xref:System.Threading.Tasks.Task> 傳回，則 <xref:System.Threading.Tasks.Task> 會等待，然後轉譯元件。</span><span class="sxs-lookup"><span data-stu-id="e6e09-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="e6e09-116">如果 <xref:System.Threading.Tasks.Task> 未傳回，則會轉譯元件。</span><span class="sxs-lookup"><span data-stu-id="e6e09-116">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>
1. <span data-ttu-id="e6e09-117">呼叫 [`OnParametersSet{Async}`](#after-parameters-are-set) 並呈現元件。</span><span class="sxs-lookup"><span data-stu-id="e6e09-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="e6e09-118">如果 <xref:System.Threading.Tasks.Task> 從傳回，則 `OnParametersSetAsync` <xref:System.Threading.Tasks.Task> 會等待，然後元件會保存。</span><span class="sxs-lookup"><span data-stu-id="e6e09-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![：：： No loc (Razor) ：：： component in：：： no-loc (Blazor) ：：：的元件生命週期事件](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="e6e09-120">檔物件模型 (DOM) 事件處理：</span><span class="sxs-lookup"><span data-stu-id="e6e09-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="e6e09-121">執行事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="e6e09-121">The event handler is run.</span></span>
1. <span data-ttu-id="e6e09-122">如果 <xref:System.Threading.Tasks.Task> 傳回，則 <xref:System.Threading.Tasks.Task> 會等待，然後轉譯元件。</span><span class="sxs-lookup"><span data-stu-id="e6e09-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="e6e09-123">如果 <xref:System.Threading.Tasks.Task> 未傳回，則會轉譯元件。</span><span class="sxs-lookup"><span data-stu-id="e6e09-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![檔物件模型 (DOM) 事件處理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="e6e09-125">`Render`生命週期：</span><span class="sxs-lookup"><span data-stu-id="e6e09-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="e6e09-126">如果這不是元件的第一次轉譯或 [`ShouldRender`](#suppress-ui-refreshing) 評估為 `false` ，請勿在元件上執行進一步的作業。</span><span class="sxs-lookup"><span data-stu-id="e6e09-126">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="e6e09-127">建立轉譯樹狀結構差異 (差異) 並呈現元件。</span><span class="sxs-lookup"><span data-stu-id="e6e09-127">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="e6e09-128">等待 DOM 進行更新。</span><span class="sxs-lookup"><span data-stu-id="e6e09-128">Await the DOM to update.</span></span>
1. <span data-ttu-id="e6e09-129">呼叫 [`OnAfterRender{Async}`](#after-component-render) 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-129">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![轉譯生命週期](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="e6e09-131">開發人員呼叫以 [`StateHasChanged`](#state-changes) 產生轉譯。</span><span class="sxs-lookup"><span data-stu-id="e6e09-131">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="e6e09-132">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="e6e09-132">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="e6e09-133">設定參數之前</span><span class="sxs-lookup"><span data-stu-id="e6e09-133">Before parameters are set</span></span>

<span data-ttu-id="e6e09-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 在轉譯樹狀結構中設定元件父系所提供的參數：</span><span class="sxs-lookup"><span data-stu-id="e6e09-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="e6e09-135"><xref:Microsoft.AspNetCore.Components.ParameterView> 每次呼叫時，包含元件的參數值集 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-135"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="e6e09-136">的預設實 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 值會設定每個屬性的值，其具有的 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 屬性在中具有對應的值 <xref:Microsoft.AspNetCore.Components.ParameterView> 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-136">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="e6e09-137">在中沒有對應值的參數 <xref:Microsoft.AspNetCore.Components.ParameterView> 會保持不變。</span><span class="sxs-lookup"><span data-stu-id="e6e09-137">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="e6e09-138">如果 [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) 未叫用，自訂程式碼就能以任何需要的方式解讀傳入的參數值。</span><span class="sxs-lookup"><span data-stu-id="e6e09-138">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="e6e09-139">例如，不需要將傳入參數指派給類別上的屬性。</span><span class="sxs-lookup"><span data-stu-id="e6e09-139">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="e6e09-140">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="e6e09-140">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="e6e09-141">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="e6e09-141">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="e6e09-142">元件初始化方法</span><span class="sxs-lookup"><span data-stu-id="e6e09-142">Component initialization methods</span></span>

<span data-ttu-id="e6e09-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>當元件在中收到其父元件的初始參數之後，就會叫用和 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="e6e09-144">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 元件執行非同步作業時使用，而且應該在作業完成時重新整理。</span><span class="sxs-lookup"><span data-stu-id="e6e09-144">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="e6e09-145">針對同步作業，覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ：</span><span class="sxs-lookup"><span data-stu-id="e6e09-145">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="e6e09-146">若要執行非同步作業，請 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 在作業上覆寫並使用 [`await`](/dotnet/csharp/language-reference/operators/await) 運算子：</span><span class="sxs-lookup"><span data-stu-id="e6e09-146">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="e6e09-147">Blazor Server 將 [其內容呼叫呈現](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_兩次_** 的應用程式：</span><span class="sxs-lookup"><span data-stu-id="e6e09-147">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_** :</span></span>

* <span data-ttu-id="e6e09-148">一開始是以靜態方式將元件轉譯為頁面的一部分。</span><span class="sxs-lookup"><span data-stu-id="e6e09-148">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="e6e09-149">第二次當瀏覽器重新建立與伺服器的連接時。</span><span class="sxs-lookup"><span data-stu-id="e6e09-149">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="e6e09-150">若要防止開發人員的程式碼執行 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 兩次，請參閱 [自 [呈現後](#stateful-reconnection-after-prerendering) 的可設定狀態] 區段。</span><span class="sxs-lookup"><span data-stu-id="e6e09-150">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="e6e09-151">當 Blazor Server 應用程式進行預先處理時，無法執行某些動作（例如呼叫 JavaScript），因為尚未建立與瀏覽器的連接。</span><span class="sxs-lookup"><span data-stu-id="e6e09-151">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="e6e09-152">元件在資源清單時可能需要以不同的方式呈現。</span><span class="sxs-lookup"><span data-stu-id="e6e09-152">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="e6e09-153">如需詳細資訊，請參閱在 [應用程式的 [已呈現時](#detect-when-the-app-is-prerendering) 偵測] 區段。</span><span class="sxs-lookup"><span data-stu-id="e6e09-153">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="e6e09-154">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="e6e09-154">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="e6e09-155">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="e6e09-155">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="e6e09-156">設定參數之後</span><span class="sxs-lookup"><span data-stu-id="e6e09-156">After parameters are set</span></span>

<span data-ttu-id="e6e09-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 稱為：</span><span class="sxs-lookup"><span data-stu-id="e6e09-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="e6e09-158">在或中初始化元件之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-158">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="e6e09-159">當父元件重新呈現和提供時：</span><span class="sxs-lookup"><span data-stu-id="e6e09-159">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="e6e09-160">只有已知的基本不可變類型，其中至少有一個參數已變更。</span><span class="sxs-lookup"><span data-stu-id="e6e09-160">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="e6e09-161">任何複雜類型參數。</span><span class="sxs-lookup"><span data-stu-id="e6e09-161">Any complex-typed parameters.</span></span> <span data-ttu-id="e6e09-162">架構無法得知複雜型別參數的值是否在內部改變，因此它會將參數集視為已變更。</span><span class="sxs-lookup"><span data-stu-id="e6e09-162">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="e6e09-163">套用參數和屬性值的非同步工作必須在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命週期事件期間發生。</span><span class="sxs-lookup"><span data-stu-id="e6e09-163">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="e6e09-164">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="e6e09-164">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="e6e09-165">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="e6e09-165">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="e6e09-166">元件呈現之後</span><span class="sxs-lookup"><span data-stu-id="e6e09-166">After component render</span></span>

<span data-ttu-id="e6e09-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 元件完成轉譯之後，會呼叫和。</span><span class="sxs-lookup"><span data-stu-id="e6e09-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="e6e09-168">此時會填入元素和元件參考。</span><span class="sxs-lookup"><span data-stu-id="e6e09-168">Element and component references are populated at this point.</span></span> <span data-ttu-id="e6e09-169">您可以使用此階段，利用轉譯的內容來執行其他初始化步驟，例如啟用在轉譯的 DOM 專案上操作的協力廠商 JavaScript 程式庫。</span><span class="sxs-lookup"><span data-stu-id="e6e09-169">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="e6e09-170">`firstRender`And 的參數 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ：</span><span class="sxs-lookup"><span data-stu-id="e6e09-170">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="e6e09-171">會 `true` 在第一次轉譯元件實例時設定為。</span><span class="sxs-lookup"><span data-stu-id="e6e09-171">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="e6e09-172">可以用來確保只執行一次初始化工作。</span><span class="sxs-lookup"><span data-stu-id="e6e09-172">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="e6e09-173">轉譯後立即的非同步工作必須在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命週期事件期間發生。</span><span class="sxs-lookup"><span data-stu-id="e6e09-173">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="e6e09-174">即使您 <xref:System.Threading.Tasks.Task> 從傳回 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ，架構也不會在該工作完成之後，為您的元件排程進一步的轉譯迴圈。</span><span class="sxs-lookup"><span data-stu-id="e6e09-174">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="e6e09-175">這是為了避免無限呈現迴圈。</span><span class="sxs-lookup"><span data-stu-id="e6e09-175">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="e6e09-176">這與其他生命週期方法不同，後者會在傳回的工作完成時排程進一步的轉譯週期。</span><span class="sxs-lookup"><span data-stu-id="e6e09-176">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="e6e09-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 而且不會在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *伺服器上的未處理常式期間呼叫* 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="e6e09-178">當預先轉譯完成之後，以互動方式轉譯元件時，就會呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="e6e09-178">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="e6e09-179">當應用程式 prerenders 時：</span><span class="sxs-lookup"><span data-stu-id="e6e09-179">When the app prerenders:</span></span>

1. <span data-ttu-id="e6e09-180">元件會在伺服器上執行，以在 HTTP 回應中產生一些靜態 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="e6e09-180">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="e6e09-181">在這個階段中 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 不會呼叫。</span><span class="sxs-lookup"><span data-stu-id="e6e09-181">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="e6e09-182">當 `blazor.server.js` 您 `blazor.webassembly.js` 在瀏覽器中啟動或啟動時，元件會以互動式轉譯模式重新開機。</span><span class="sxs-lookup"><span data-stu-id="e6e09-182">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="e6e09-183">重新開機元件之後， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **會** 呼叫，因為應用程式不會再于未進入的階段內。</span><span class="sxs-lookup"><span data-stu-id="e6e09-183">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="e6e09-184">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="e6e09-184">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="e6e09-185">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="e6e09-185">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="e6e09-186">隱藏 UI 重新整理</span><span class="sxs-lookup"><span data-stu-id="e6e09-186">Suppress UI refreshing</span></span>

<span data-ttu-id="e6e09-187">覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以隱藏 UI 重新整理。</span><span class="sxs-lookup"><span data-stu-id="e6e09-187">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="e6e09-188">如果執行傳回 `true` ，則會重新整理 UI：</span><span class="sxs-lookup"><span data-stu-id="e6e09-188">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="e6e09-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 每次轉譯元件時，就會呼叫。</span><span class="sxs-lookup"><span data-stu-id="e6e09-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="e6e09-190">即使覆 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 寫，仍一律會呈現元件。</span><span class="sxs-lookup"><span data-stu-id="e6e09-190">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="e6e09-191">如需詳細資訊，請參閱<xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>。</span><span class="sxs-lookup"><span data-stu-id="e6e09-191">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="e6e09-192">狀態變更</span><span class="sxs-lookup"><span data-stu-id="e6e09-192">State changes</span></span>

<span data-ttu-id="e6e09-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知元件其狀態已變更。</span><span class="sxs-lookup"><span data-stu-id="e6e09-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="e6e09-194">當適用時，呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會導致元件保存。</span><span class="sxs-lookup"><span data-stu-id="e6e09-194">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="e6e09-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動針對 <xref:Microsoft.AspNetCore.Components.EventCallback> 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="e6e09-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="e6e09-196">如需詳細資訊，請參閱<xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="e6e09-196">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="e6e09-197">在轉譯時處理未完成的非同步動作</span><span class="sxs-lookup"><span data-stu-id="e6e09-197">Handle incomplete async actions at render</span></span>

<span data-ttu-id="e6e09-198">在呈現元件之前，在生命週期事件中執行的非同步動作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="e6e09-198">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="e6e09-199">`null`當生命週期方法執行時，物件可能會或未完全填入資料。</span><span class="sxs-lookup"><span data-stu-id="e6e09-199">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="e6e09-200">提供轉譯邏輯來確認物件已初始化。</span><span class="sxs-lookup"><span data-stu-id="e6e09-200">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="e6e09-201">轉譯預留位置 UI 元素 (例如，在物件為時) 載入訊息 `null` 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-201">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="e6e09-202">在 `FetchData` 範本的元件中 Blazor ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 會覆寫以 asychronously 接收預測資料 (`forecasts`) 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-202">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="e6e09-203">當 `forecasts` 為時 `null` ，就會向使用者顯示載入訊息。</span><span class="sxs-lookup"><span data-stu-id="e6e09-203">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="e6e09-204">在 `Task` 傳回之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ，元件會以更新狀態保存。</span><span class="sxs-lookup"><span data-stu-id="e6e09-204">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="e6e09-205">`Pages/FetchData.razor` 在 Blazor Server 範本中：</span><span class="sxs-lookup"><span data-stu-id="e6e09-205">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="e6e09-206">處理錯誤</span><span class="sxs-lookup"><span data-stu-id="e6e09-206">Handle errors</span></span>

<span data-ttu-id="e6e09-207">如需在生命週期方法執行期間處理錯誤的詳細資訊，請參閱 <xref:blazor/fundamentals/handle-errors#lifecycle-methods> 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-207">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="e6e09-208">以具狀態重新連接後重新連線</span><span class="sxs-lookup"><span data-stu-id="e6e09-208">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="e6e09-209">在 Blazor Server 應用程式中 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，當為時 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> ，元件最初會以靜態方式轉譯為頁面的一部分。</span><span class="sxs-lookup"><span data-stu-id="e6e09-209">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="e6e09-210">當瀏覽器將連接重新建立回伺服器之後，元件會 *再次* 轉譯，而元件現在是互動式的。</span><span class="sxs-lookup"><span data-stu-id="e6e09-210">Once the browser establishes a connection back to the server, the component is rendered *again* , and the component is now interactive.</span></span> <span data-ttu-id="e6e09-211">如果 [`OnInitialized{Async}`](#component-initialization-methods) 有初始化元件的生命週期方法，方法會執行 *兩次* ：</span><span class="sxs-lookup"><span data-stu-id="e6e09-211">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice* :</span></span>

* <span data-ttu-id="e6e09-212">以靜態方式資源清單元件時。</span><span class="sxs-lookup"><span data-stu-id="e6e09-212">When the component is prerendered statically.</span></span>
* <span data-ttu-id="e6e09-213">建立伺服器連接之後。</span><span class="sxs-lookup"><span data-stu-id="e6e09-213">After the server connection has been established.</span></span>

<span data-ttu-id="e6e09-214">這可能會導致在元件最後轉譯時，UI 中所顯示的資料有明顯的變更。</span><span class="sxs-lookup"><span data-stu-id="e6e09-214">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="e6e09-215">若要避免應用程式中的雙呈現案例 Blazor Server ：</span><span class="sxs-lookup"><span data-stu-id="e6e09-215">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="e6e09-216">傳入可用來在自動處理期間快取狀態的識別碼，並在應用程式重新開機之後取得狀態。</span><span class="sxs-lookup"><span data-stu-id="e6e09-216">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="e6e09-217">在預呈現期間使用識別碼，以儲存元件狀態。</span><span class="sxs-lookup"><span data-stu-id="e6e09-217">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="e6e09-218">在經過完成後，請使用識別碼來抓取快取的狀態。</span><span class="sxs-lookup"><span data-stu-id="e6e09-218">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="e6e09-219">下列程式碼示範 `WeatherForecastService` 以範本為基礎的應用程式中已更新 Blazor Server ，可避免雙重轉譯：</span><span class="sxs-lookup"><span data-stu-id="e6e09-219">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="e6e09-220">如需的詳細資訊 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，請參閱 <xref:blazor/fundamentals/additional-scenarios#render-mode> 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-220">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="e6e09-221">偵測應用程式何時進行呈現</span><span class="sxs-lookup"><span data-stu-id="e6e09-221">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="e6e09-222">使用 IDisposable 的元件處置</span><span class="sxs-lookup"><span data-stu-id="e6e09-222">Component disposal with IDisposable</span></span>

<span data-ttu-id="e6e09-223">如果元件已 <xref:System.IDisposable> 完成，則會在從 UI 中移除元件時呼叫此[ `Dispose` 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="e6e09-223">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="e6e09-224">處置可以在任何時間進行，包括 [元件初始化](#component-initialization-methods)期間。</span><span class="sxs-lookup"><span data-stu-id="e6e09-224">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="e6e09-225">下列元件會使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="e6e09-225">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

> [!NOTE]
> <span data-ttu-id="e6e09-226"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>不支援在中呼叫 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-226">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="e6e09-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能會在卸載轉譯器的過程中叫用，因此不支援在該時間點要求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="e6e09-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="e6e09-228">取消訂閱來自 .NET 事件的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="e6e09-228">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="e6e09-229">下列[ Blazor 表單](xref:blazor/forms-validation)範例示範如何在方法中解除程式事件處理常式的掛鉤 `Dispose` ：</span><span class="sxs-lookup"><span data-stu-id="e6e09-229">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="e6e09-230">私用欄位和 lambda 方法</span><span class="sxs-lookup"><span data-stu-id="e6e09-230">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="e6e09-231">私用方法方法</span><span class="sxs-lookup"><span data-stu-id="e6e09-231">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="e6e09-232">可取消的背景工作</span><span class="sxs-lookup"><span data-stu-id="e6e09-232">Cancelable background work</span></span>

<span data-ttu-id="e6e09-233">元件通常會執行長時間執行的背景工作，例如 () 進行網路呼叫， <xref:System.Net.Http.HttpClient> 以及與資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="e6e09-233">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="e6e09-234">在幾種情況下，最好停止背景工作來節省系統資源。</span><span class="sxs-lookup"><span data-stu-id="e6e09-234">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="e6e09-235">例如，當使用者離開元件時，不會自動停止背景非同步作業。</span><span class="sxs-lookup"><span data-stu-id="e6e09-235">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="e6e09-236">背景工作專案可能需要取消的其他原因包括：</span><span class="sxs-lookup"><span data-stu-id="e6e09-236">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="e6e09-237">執行中的背景工作已啟動，但有錯誤的輸入資料或處理參數。</span><span class="sxs-lookup"><span data-stu-id="e6e09-237">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="e6e09-238">目前執行中的背景工作專案集必須取代為一組新的工作專案。</span><span class="sxs-lookup"><span data-stu-id="e6e09-238">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="e6e09-239">目前執行中工作的優先順序必須變更。</span><span class="sxs-lookup"><span data-stu-id="e6e09-239">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="e6e09-240">您必須關閉應用程式，才能將它重新部署至伺服器。</span><span class="sxs-lookup"><span data-stu-id="e6e09-240">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="e6e09-241">伺服器資源會受到限制，強制背景工作專案的重新排程。</span><span class="sxs-lookup"><span data-stu-id="e6e09-241">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="e6e09-242">若要在元件中執行可取消的背景工作模式：</span><span class="sxs-lookup"><span data-stu-id="e6e09-242">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="e6e09-243">使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken> 。</span><span class="sxs-lookup"><span data-stu-id="e6e09-243">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="e6e09-244">手動取消權杖時，若要處理 [元件](#component-disposal-with-idisposable) 和任何點取消，請呼叫 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示應取消背景工作。</span><span class="sxs-lookup"><span data-stu-id="e6e09-244">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="e6e09-245">在非同步呼叫傳回之後，請呼叫 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> 權杖上的。</span><span class="sxs-lookup"><span data-stu-id="e6e09-245">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="e6e09-246">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e6e09-246">In the following example:</span></span>

* <span data-ttu-id="e6e09-247">`await Task.Delay(5000, cts.Token);` 表示長時間執行的非同步背景工作。</span><span class="sxs-lookup"><span data-stu-id="e6e09-247">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="e6e09-248">`BackgroundResourceMethod` 表示長時間執行的背景方法，如果在 `Resource` 呼叫方法之前處置，則不應該啟動。</span><span class="sxs-lookup"><span data-stu-id="e6e09-248">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
