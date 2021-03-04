---
title: ASP.NET 核心 Blazor 生命週期
author: guardrex
description: 瞭解如何 Razor 在 ASP.NET Core 應用程式中使用元件生命週期方法 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
ms.openlocfilehash: 6e9d2c3180fb9e4c3e5ccc0b6d8e17183f78d698
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109841"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="64a2f-103">ASP.NET 核心 Blazor 生命週期</span><span class="sxs-lookup"><span data-stu-id="64a2f-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="64a2f-104">此 Blazor 架構包含同步和非同步生命週期方法。</span><span class="sxs-lookup"><span data-stu-id="64a2f-104">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="64a2f-105">覆寫生命週期方法，以在元件初始化和轉譯期間對元件執行額外的作業。</span><span class="sxs-lookup"><span data-stu-id="64a2f-105">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="64a2f-106">下圖說明 Blazor 生命週期。</span><span class="sxs-lookup"><span data-stu-id="64a2f-106">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="64a2f-107">生命週期方法是使用本文的下列各節中的範例所定義。</span><span class="sxs-lookup"><span data-stu-id="64a2f-107">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="64a2f-108">元件生命週期事件：</span><span class="sxs-lookup"><span data-stu-id="64a2f-108">Component lifecycle events:</span></span>

1. <span data-ttu-id="64a2f-109">如果第一次在要求時轉譯元件：</span><span class="sxs-lookup"><span data-stu-id="64a2f-109">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="64a2f-110">建立元件的實例。</span><span class="sxs-lookup"><span data-stu-id="64a2f-110">Create the component's instance.</span></span>
   * <span data-ttu-id="64a2f-111">執行屬性插入。</span><span class="sxs-lookup"><span data-stu-id="64a2f-111">Perform property injection.</span></span> <span data-ttu-id="64a2f-112">執行 [`SetParametersAsync`](#before-parameters-are-set) 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-112">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="64a2f-113">呼叫 [`OnInitialized{Async}`](#component-initialization-methods) 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-113">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="64a2f-114">如果 <xref:System.Threading.Tasks.Task> 傳回，則會等候， <xref:System.Threading.Tasks.Task> 並且呈現元件。</span><span class="sxs-lookup"><span data-stu-id="64a2f-114">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="64a2f-115">如果 <xref:System.Threading.Tasks.Task> 未傳回，則會轉譯元件。</span><span class="sxs-lookup"><span data-stu-id="64a2f-115">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="64a2f-116">呼叫 [`OnParametersSet{Async}`](#after-parameters-are-set) 並呈現元件。</span><span class="sxs-lookup"><span data-stu-id="64a2f-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="64a2f-117">如果 <xref:System.Threading.Tasks.Task> 從傳回，則 `OnParametersSetAsync` <xref:System.Threading.Tasks.Task> 會等待，然後元件會保存。</span><span class="sxs-lookup"><span data-stu-id="64a2f-117">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![：：： No loc (Razor) ：：： component in：：： no-loc (Blazor) ：：：的元件生命週期事件](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="64a2f-119">檔物件模型 (DOM) 事件處理：</span><span class="sxs-lookup"><span data-stu-id="64a2f-119">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="64a2f-120">執行事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="64a2f-120">The event handler is run.</span></span>
1. <span data-ttu-id="64a2f-121">如果 <xref:System.Threading.Tasks.Task> 傳回，則 <xref:System.Threading.Tasks.Task> 會等待，然後轉譯元件。</span><span class="sxs-lookup"><span data-stu-id="64a2f-121">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="64a2f-122">如果 <xref:System.Threading.Tasks.Task> 未傳回，則會轉譯元件。</span><span class="sxs-lookup"><span data-stu-id="64a2f-122">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![檔物件模型 (DOM) 事件處理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="64a2f-124">`Render`生命週期：</span><span class="sxs-lookup"><span data-stu-id="64a2f-124">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="64a2f-125">避免元件上的進一步轉譯作業：</span><span class="sxs-lookup"><span data-stu-id="64a2f-125">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="64a2f-126">在第一次呈現之後。</span><span class="sxs-lookup"><span data-stu-id="64a2f-126">After the first render.</span></span>
   * <span data-ttu-id="64a2f-127">當 [`ShouldRender`](#suppress-ui-refreshing) 為時 `false` 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-127">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="64a2f-128">建立轉譯樹狀結構差異 (差異) 並呈現元件。</span><span class="sxs-lookup"><span data-stu-id="64a2f-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="64a2f-129">等待 DOM 進行更新。</span><span class="sxs-lookup"><span data-stu-id="64a2f-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="64a2f-130">呼叫 [`OnAfterRender{Async}`](#after-component-render) 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![轉譯生命週期](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="64a2f-132">開發人員呼叫以 [`StateHasChanged`](#state-changes) 產生轉譯。</span><span class="sxs-lookup"><span data-stu-id="64a2f-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="64a2f-133">如需詳細資訊，請參閱<xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="64a2f-133">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="64a2f-134">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="64a2f-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="64a2f-135">設定參數之前</span><span class="sxs-lookup"><span data-stu-id="64a2f-135">Before parameters are set</span></span>

<span data-ttu-id="64a2f-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 在轉譯樹狀結構中或從路由參數，設定元件的父系所提供的參數。</span><span class="sxs-lookup"><span data-stu-id="64a2f-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="64a2f-137">藉由覆寫方法，開發人員程式碼就可以直接與 <xref:Microsoft.AspNetCore.Components.ParameterView> 的參數互動。</span><span class="sxs-lookup"><span data-stu-id="64a2f-137">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="64a2f-138">在下列範例中， <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> 如果剖析的路由參數為成功，則將 `Param` 參數的值指派給 `value` `Param` 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-138">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="64a2f-139">如果 `value` 沒有 `null` ，此值就會由元件顯示。</span><span class="sxs-lookup"><span data-stu-id="64a2f-139">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="64a2f-140">雖然 [路由參數比對不區分大小寫](xref:blazor/fundamentals/routing#route-parameters)，但 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 僅符合路由範本中區分大小寫的參數名稱。</span><span class="sxs-lookup"><span data-stu-id="64a2f-140">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="64a2f-141">下列範例需要使用 `/{Param?}` ，而不是以 `/{param?}` 取得值。</span><span class="sxs-lookup"><span data-stu-id="64a2f-141">The following example is required to use `/{Param?}`, not `/{param?}`, in order to get the value.</span></span> <span data-ttu-id="64a2f-142">如果 `/{param?}` 在此案例中使用，則會傳回， <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> `false` 而且 `message` 不會設定為任何一個字串。</span><span class="sxs-lookup"><span data-stu-id="64a2f-142">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either string.</span></span>

<span data-ttu-id="64a2f-143">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="64a2f-143">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="64a2f-144"><xref:Microsoft.AspNetCore.Components.ParameterView> 每次呼叫時，包含元件的參數值集 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-144"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="64a2f-145">的預設實 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 值會設定每個屬性的值，其具有的 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或[ `[CascadingParameter]` 屬性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)在中具有對應的值 <xref:Microsoft.AspNetCore.Components.ParameterView> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-145">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="64a2f-146">在中沒有對應值的參數 <xref:Microsoft.AspNetCore.Components.ParameterView> 會保持不變。</span><span class="sxs-lookup"><span data-stu-id="64a2f-146">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="64a2f-147">如果 [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) 未叫用，自訂程式碼就能以任何需要的方式解讀傳入的參數值。</span><span class="sxs-lookup"><span data-stu-id="64a2f-147">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="64a2f-148">例如，不需要將傳入參數指派給類別上的屬性。</span><span class="sxs-lookup"><span data-stu-id="64a2f-148">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="64a2f-149">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="64a2f-149">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="64a2f-150">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="64a2f-150">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="64a2f-151">元件初始化方法</span><span class="sxs-lookup"><span data-stu-id="64a2f-151">Component initialization methods</span></span>

<span data-ttu-id="64a2f-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>當元件在中收到其父元件的初始參數之後，就會叫用和 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="64a2f-153">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 元件執行非同步作業時使用，而且應該在作業完成時重新整理。</span><span class="sxs-lookup"><span data-stu-id="64a2f-153">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="64a2f-154">針對同步作業，覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ：</span><span class="sxs-lookup"><span data-stu-id="64a2f-154">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="64a2f-155">若要執行非同步作業，請 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 在作業上覆寫並使用 [`await`](/dotnet/csharp/language-reference/operators/await) 運算子：</span><span class="sxs-lookup"><span data-stu-id="64a2f-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="64a2f-156">Blazor Server 將 [其內容呼叫呈現](xref:blazor/fundamentals/signalr#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *兩次* 的應用程式：</span><span class="sxs-lookup"><span data-stu-id="64a2f-156">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="64a2f-157">一開始是以靜態方式將元件轉譯為頁面的一部分。</span><span class="sxs-lookup"><span data-stu-id="64a2f-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="64a2f-158">第二次當瀏覽器重新建立與伺服器的連接時。</span><span class="sxs-lookup"><span data-stu-id="64a2f-158">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="64a2f-159">若要防止開發人員的程式碼執行 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 兩次，請參閱 [自 [呈現後](#stateful-reconnection-after-prerendering) 的可設定狀態] 區段。</span><span class="sxs-lookup"><span data-stu-id="64a2f-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="64a2f-160">當 Blazor Server 應用程式進行預先處理時，無法執行某些動作（例如呼叫 JavaScript），因為尚未建立與瀏覽器的連接。</span><span class="sxs-lookup"><span data-stu-id="64a2f-160">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="64a2f-161">元件在資源清單時可能需要以不同的方式呈現。</span><span class="sxs-lookup"><span data-stu-id="64a2f-161">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="64a2f-162">如需詳細資訊，請參閱在 [應用程式的 [已呈現時](#detect-when-the-app-is-prerendering) 偵測] 區段。</span><span class="sxs-lookup"><span data-stu-id="64a2f-162">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="64a2f-163">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="64a2f-163">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="64a2f-164">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="64a2f-164">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="64a2f-165">設定參數之後</span><span class="sxs-lookup"><span data-stu-id="64a2f-165">After parameters are set</span></span>

<span data-ttu-id="64a2f-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 稱為：</span><span class="sxs-lookup"><span data-stu-id="64a2f-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="64a2f-167">在或中初始化元件之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-167">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="64a2f-168">當父元件重新呈現和提供時：</span><span class="sxs-lookup"><span data-stu-id="64a2f-168">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="64a2f-169">只有已知的基本不可變類型，其中至少有一個參數已變更。</span><span class="sxs-lookup"><span data-stu-id="64a2f-169">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="64a2f-170">任何複雜類型參數。</span><span class="sxs-lookup"><span data-stu-id="64a2f-170">Any complex-typed parameters.</span></span> <span data-ttu-id="64a2f-171">架構無法得知複雜型別參數的值是否在內部改變，因此它會將參數集視為已變更。</span><span class="sxs-lookup"><span data-stu-id="64a2f-171">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="64a2f-172">套用參數和屬性值的非同步工作必須在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命週期事件期間發生。</span><span class="sxs-lookup"><span data-stu-id="64a2f-172">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="64a2f-173">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="64a2f-173">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="64a2f-174">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="64a2f-174">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="64a2f-175">元件呈現之後</span><span class="sxs-lookup"><span data-stu-id="64a2f-175">After component render</span></span>

<span data-ttu-id="64a2f-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 元件完成轉譯之後，會呼叫和。</span><span class="sxs-lookup"><span data-stu-id="64a2f-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="64a2f-177">此時會填入元素和元件參考。</span><span class="sxs-lookup"><span data-stu-id="64a2f-177">Element and component references are populated at this point.</span></span> <span data-ttu-id="64a2f-178">您可以使用此階段，利用轉譯的內容來執行其他初始化步驟，例如啟用在轉譯的 DOM 專案上操作的協力廠商 JavaScript 程式庫。</span><span class="sxs-lookup"><span data-stu-id="64a2f-178">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="64a2f-179">`firstRender`And 的參數 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ：</span><span class="sxs-lookup"><span data-stu-id="64a2f-179">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="64a2f-180">會 `true` 在第一次轉譯元件實例時設定為。</span><span class="sxs-lookup"><span data-stu-id="64a2f-180">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="64a2f-181">可以用來確保只執行一次初始化工作。</span><span class="sxs-lookup"><span data-stu-id="64a2f-181">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="64a2f-182">轉譯後立即的非同步工作必須在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命週期事件期間發生。</span><span class="sxs-lookup"><span data-stu-id="64a2f-182">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="64a2f-183">即使您 <xref:System.Threading.Tasks.Task> 從傳回 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ，架構也不會在該工作完成之後，為您的元件排程進一步的轉譯迴圈。</span><span class="sxs-lookup"><span data-stu-id="64a2f-183">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="64a2f-184">這是為了避免無限呈現迴圈。</span><span class="sxs-lookup"><span data-stu-id="64a2f-184">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="64a2f-185">這與其他生命週期方法不同，後者會在傳回的工作完成時排程進一步的轉譯週期。</span><span class="sxs-lookup"><span data-stu-id="64a2f-185">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="64a2f-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 而且不會在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *伺服器上的未處理常式期間呼叫*。</span><span class="sxs-lookup"><span data-stu-id="64a2f-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="64a2f-187">當預先轉譯完成之後，以互動方式轉譯元件時，就會呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="64a2f-187">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="64a2f-188">當應用程式 prerenders 時：</span><span class="sxs-lookup"><span data-stu-id="64a2f-188">When the app prerenders:</span></span>

1. <span data-ttu-id="64a2f-189">元件會在伺服器上執行，以在 HTTP 回應中產生一些靜態 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="64a2f-189">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="64a2f-190">在這個階段中 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 不會呼叫。</span><span class="sxs-lookup"><span data-stu-id="64a2f-190">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="64a2f-191">當 `blazor.server.js` 您 `blazor.webassembly.js` 在瀏覽器中啟動或啟動時，元件會以互動式轉譯模式重新開機。</span><span class="sxs-lookup"><span data-stu-id="64a2f-191">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="64a2f-192">重新開機元件之後， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **會** 呼叫，因為應用程式不會再于未進入的階段內。</span><span class="sxs-lookup"><span data-stu-id="64a2f-192">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="64a2f-193">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="64a2f-193">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="64a2f-194">如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。</span><span class="sxs-lookup"><span data-stu-id="64a2f-194">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="64a2f-195">隱藏 UI 重新整理</span><span class="sxs-lookup"><span data-stu-id="64a2f-195">Suppress UI refreshing</span></span>

<span data-ttu-id="64a2f-196">覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以隱藏 UI 重新整理。</span><span class="sxs-lookup"><span data-stu-id="64a2f-196">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="64a2f-197">如果執行傳回 `true` ，則會重新整理 UI：</span><span class="sxs-lookup"><span data-stu-id="64a2f-197">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="64a2f-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 每次轉譯元件時，就會呼叫。</span><span class="sxs-lookup"><span data-stu-id="64a2f-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="64a2f-199">即使覆 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 寫，仍一律會呈現元件。</span><span class="sxs-lookup"><span data-stu-id="64a2f-199">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="64a2f-200">如需詳細資訊，請參閱<xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>。</span><span class="sxs-lookup"><span data-stu-id="64a2f-200">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="64a2f-201">狀態變更</span><span class="sxs-lookup"><span data-stu-id="64a2f-201">State changes</span></span>

<span data-ttu-id="64a2f-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知元件其狀態已變更。</span><span class="sxs-lookup"><span data-stu-id="64a2f-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="64a2f-203">當適用時，呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會導致元件保存。</span><span class="sxs-lookup"><span data-stu-id="64a2f-203">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="64a2f-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動針對 <xref:Microsoft.AspNetCore.Components.EventCallback> 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="64a2f-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="64a2f-205">如需詳細資訊，請參閱<xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="64a2f-205">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="64a2f-206">如需詳細資訊，請參閱<xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="64a2f-206">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="64a2f-207">在轉譯時處理未完成的非同步動作</span><span class="sxs-lookup"><span data-stu-id="64a2f-207">Handle incomplete async actions at render</span></span>

<span data-ttu-id="64a2f-208">在呈現元件之前，在生命週期事件中執行的非同步動作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="64a2f-208">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="64a2f-209">`null`當生命週期方法執行時，物件可能會或未完全填入資料。</span><span class="sxs-lookup"><span data-stu-id="64a2f-209">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="64a2f-210">提供轉譯邏輯來確認物件已初始化。</span><span class="sxs-lookup"><span data-stu-id="64a2f-210">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="64a2f-211">轉譯預留位置 UI 元素 (例如，在物件為時) 載入訊息 `null` 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-211">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="64a2f-212">在 `FetchData` 範本的元件中 Blazor ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 會覆寫以非同步方式接收預測資料 (`forecasts`) 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-212">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="64a2f-213">當 `forecasts` 為時 `null` ，就會向使用者顯示載入訊息。</span><span class="sxs-lookup"><span data-stu-id="64a2f-213">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="64a2f-214">在 `Task` 傳回之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ，元件會以更新狀態保存。</span><span class="sxs-lookup"><span data-stu-id="64a2f-214">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="64a2f-215">`Pages/FetchData.razor` 在 Blazor Server 範本中：</span><span class="sxs-lookup"><span data-stu-id="64a2f-215">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a><span data-ttu-id="64a2f-216">處理錯誤</span><span class="sxs-lookup"><span data-stu-id="64a2f-216">Handle errors</span></span>

<span data-ttu-id="64a2f-217">如需在生命週期方法執行期間處理錯誤的詳細資訊，請參閱 <xref:blazor/fundamentals/handle-errors> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-217">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="64a2f-218">以具狀態重新連接後重新連線</span><span class="sxs-lookup"><span data-stu-id="64a2f-218">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="64a2f-219">在 Blazor Server 應用程式中 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，當為時 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> ，元件最初會以靜態方式轉譯為頁面的一部分。</span><span class="sxs-lookup"><span data-stu-id="64a2f-219">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="64a2f-220">當瀏覽器將連接重新建立回伺服器之後，元件會 *再次* 轉譯，而元件現在是互動式的。</span><span class="sxs-lookup"><span data-stu-id="64a2f-220">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="64a2f-221">如果 [`OnInitialized{Async}`](#component-initialization-methods) 有初始化元件的生命週期方法，方法會執行 *兩次*：</span><span class="sxs-lookup"><span data-stu-id="64a2f-221">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="64a2f-222">以靜態方式資源清單元件時。</span><span class="sxs-lookup"><span data-stu-id="64a2f-222">When the component is prerendered statically.</span></span>
* <span data-ttu-id="64a2f-223">建立伺服器連接之後。</span><span class="sxs-lookup"><span data-stu-id="64a2f-223">After the server connection has been established.</span></span>

<span data-ttu-id="64a2f-224">這可能會導致在元件最後轉譯時，UI 中所顯示的資料有明顯的變更。</span><span class="sxs-lookup"><span data-stu-id="64a2f-224">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="64a2f-225">若要避免應用程式中的雙呈現案例 Blazor Server ：</span><span class="sxs-lookup"><span data-stu-id="64a2f-225">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="64a2f-226">傳入可用來在自動處理期間快取狀態的識別碼，並在應用程式重新開機之後取得狀態。</span><span class="sxs-lookup"><span data-stu-id="64a2f-226">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="64a2f-227">在預呈現期間使用識別碼，以儲存元件狀態。</span><span class="sxs-lookup"><span data-stu-id="64a2f-227">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="64a2f-228">在經過完成後，請使用識別碼來抓取快取的狀態。</span><span class="sxs-lookup"><span data-stu-id="64a2f-228">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="64a2f-229">下列程式碼示範 `WeatherForecastService` 以範本為基礎的應用程式中已更新 Blazor Server ，可避免雙重轉譯：</span><span class="sxs-lookup"><span data-stu-id="64a2f-229">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="64a2f-230">如需的詳細資訊 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，請參閱 <xref:blazor/fundamentals/signalr#render-mode> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-230">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="64a2f-231">偵測應用程式何時進行呈現</span><span class="sxs-lookup"><span data-stu-id="64a2f-231">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="64a2f-232">元件處置方式 `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="64a2f-232">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="64a2f-233">如果元件已執行 <xref:System.IDisposable> ，則在從 UI 中移除元件時，架構會呼叫 [處置方法](/dotnet/standard/garbage-collection/implementing-dispose) ，而無法釋放非受控資源。</span><span class="sxs-lookup"><span data-stu-id="64a2f-233">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="64a2f-234">處置可以在任何時間進行，包括 [元件初始化](#component-initialization-methods)期間。</span><span class="sxs-lookup"><span data-stu-id="64a2f-234">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="64a2f-235">下列元件會使用指示詞來 <xref:System.IDisposable> [`@implements`](xref:mvc/views/razor#implements) Razor 執行：</span><span class="sxs-lookup"><span data-stu-id="64a2f-235">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

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

<span data-ttu-id="64a2f-236">如果物件需要處置，當呼叫時，可以使用 lambda 來處置物件 <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> ：</span><span class="sxs-lookup"><span data-stu-id="64a2f-236">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called:</span></span>

<span data-ttu-id="64a2f-237">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="64a2f-237">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="64a2f-238">上述範例會出現在中 <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-238">The preceding example appears in <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>.</span></span>

<span data-ttu-id="64a2f-239">針對非同步處置工作，請使用 `DisposeAsync` 取代 <xref:System.IDisposable.Dispose> ：</span><span class="sxs-lookup"><span data-stu-id="64a2f-239">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="64a2f-240"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>不支援在中呼叫 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-240">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="64a2f-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能會在卸載轉譯器的過程中叫用，因此不支援在該時間點要求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="64a2f-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="64a2f-242">取消訂閱來自 .NET 事件的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="64a2f-242">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="64a2f-243">下列[ Blazor 表單](xref:blazor/forms-validation)範例示範如何在方法中取消訂閱事件處理常式 `Dispose` ：</span><span class="sxs-lookup"><span data-stu-id="64a2f-243">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="64a2f-244">私用欄位和 lambda 方法</span><span class="sxs-lookup"><span data-stu-id="64a2f-244">Private field and lambda approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

* <span data-ttu-id="64a2f-245">私用方法方法</span><span class="sxs-lookup"><span data-stu-id="64a2f-245">Private method approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

<span data-ttu-id="64a2f-246">使用 [匿名](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)函式、方法或運算式時，不需要執行 <xref:System.IDisposable> 和取消訂閱委派。</span><span class="sxs-lookup"><span data-stu-id="64a2f-246">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="64a2f-247">不過， **當公開事件的物件存留時間超過註冊委派之元件的存留期時**，無法取消訂閱委派會是一個問題。</span><span class="sxs-lookup"><span data-stu-id="64a2f-247">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="64a2f-248">發生這種情況時，會造成記憶體流失的結果，因為已註冊的委派會讓原始物件保持運作。</span><span class="sxs-lookup"><span data-stu-id="64a2f-248">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="64a2f-249">因此，當您知道事件委派會快速處置時，請使用下列方法。</span><span class="sxs-lookup"><span data-stu-id="64a2f-249">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="64a2f-250">如果不確定需要處置的物件存留期，請訂閱委派方法並適當地處置委派，如先前的範例所示。</span><span class="sxs-lookup"><span data-stu-id="64a2f-250">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the preceding examples show.</span></span>

* <span data-ttu-id="64a2f-251">匿名 lambda 方法方法 (不需要明確處置) </span><span class="sxs-lookup"><span data-stu-id="64a2f-251">Anonymous lambda method approach (explicit disposal not required)</span></span>

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

* <span data-ttu-id="64a2f-252">匿名 lambda 運算式方法 (不需要明確處置) </span><span class="sxs-lookup"><span data-stu-id="64a2f-252">Anonymous lambda expression approach (explicit disposal not required)</span></span>

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

  <span data-ttu-id="64a2f-253">上述具有匿名 lambda 運算式的程式碼完整範例會出現在中 <xref:blazor/forms-validation#validator-components> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-253">The full example of the preceding code with anonymous lambda expressions appears in <xref:blazor/forms-validation#validator-components>.</span></span>

<span data-ttu-id="64a2f-254">如需詳細資訊，請參閱在執行和方法時 [清除非受控資源](/dotnet/standard/garbage-collection/unmanaged) 和後續的主題 `Dispose` `DisposeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-254">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="64a2f-255">可取消的背景工作</span><span class="sxs-lookup"><span data-stu-id="64a2f-255">Cancelable background work</span></span>

<span data-ttu-id="64a2f-256">元件通常會執行長時間執行的背景工作，例如 () 進行網路呼叫， <xref:System.Net.Http.HttpClient> 以及與資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="64a2f-256">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="64a2f-257">在幾種情況下，最好停止背景工作來節省系統資源。</span><span class="sxs-lookup"><span data-stu-id="64a2f-257">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="64a2f-258">例如，當使用者離開元件時，不會自動停止背景非同步作業。</span><span class="sxs-lookup"><span data-stu-id="64a2f-258">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="64a2f-259">背景工作專案可能需要取消的其他原因包括：</span><span class="sxs-lookup"><span data-stu-id="64a2f-259">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="64a2f-260">執行中的背景工作已啟動，但有錯誤的輸入資料或處理參數。</span><span class="sxs-lookup"><span data-stu-id="64a2f-260">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="64a2f-261">目前執行中的背景工作專案集必須取代為一組新的工作專案。</span><span class="sxs-lookup"><span data-stu-id="64a2f-261">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="64a2f-262">目前執行中工作的優先順序必須變更。</span><span class="sxs-lookup"><span data-stu-id="64a2f-262">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="64a2f-263">您必須關閉應用程式，才能將它重新部署至伺服器。</span><span class="sxs-lookup"><span data-stu-id="64a2f-263">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="64a2f-264">伺服器資源會受到限制，強制背景工作專案的重新排程。</span><span class="sxs-lookup"><span data-stu-id="64a2f-264">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="64a2f-265">若要在元件中執行可取消的背景工作模式：</span><span class="sxs-lookup"><span data-stu-id="64a2f-265">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="64a2f-266">使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-266">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="64a2f-267">手動取消權杖時，若要處理 [元件](#component-disposal-with-idisposable) 和任何點取消，請呼叫 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示應取消背景工作。</span><span class="sxs-lookup"><span data-stu-id="64a2f-267">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="64a2f-268">在非同步呼叫傳回之後，請呼叫 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> 權杖上的。</span><span class="sxs-lookup"><span data-stu-id="64a2f-268">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="64a2f-269">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="64a2f-269">In the following example:</span></span>

* <span data-ttu-id="64a2f-270">`await Task.Delay(5000, cts.Token);` 表示長時間執行的非同步背景工作。</span><span class="sxs-lookup"><span data-stu-id="64a2f-270">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="64a2f-271">`BackgroundResourceMethod` 表示長時間執行的背景方法，如果在 `Resource` 呼叫方法之前處置，則不應該啟動。</span><span class="sxs-lookup"><span data-stu-id="64a2f-271">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="64a2f-272">Blazor Server 重新連接事件</span><span class="sxs-lookup"><span data-stu-id="64a2f-272">Blazor Server reconnection events</span></span>

<span data-ttu-id="64a2f-273">本文所涵蓋的元件生命週期事件，與重新[ Blazor Server 連接事件處理常式](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui)分開運作。</span><span class="sxs-lookup"><span data-stu-id="64a2f-273">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="64a2f-274">當 Blazor Server 應用程式失去其 SignalR 與用戶端的連線時，只會中斷 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="64a2f-274">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="64a2f-275">重新建立連接時，會繼續 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="64a2f-275">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="64a2f-276">如需線路處理程式事件和設定的詳細資訊，請參閱 <xref:blazor/fundamentals/signalr> 。</span><span class="sxs-lookup"><span data-stu-id="64a2f-276">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>
