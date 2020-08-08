---
title: ASP.NET Core Blazor 生命週期
author: guardrex
description: 瞭解如何 Razor 在 ASP.NET Core 應用程式中使用元件生命週期方法 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
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
ms.openlocfilehash: 601bb218df989c15c631633be26501ae426fab52
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014564"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="5cc96-103">ASP.NET Core Blazor 生命週期</span><span class="sxs-lookup"><span data-stu-id="5cc96-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="5cc96-104">By [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5cc96-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5cc96-105">此 Blazor 架構包含同步和非同步生命週期方法。</span><span class="sxs-lookup"><span data-stu-id="5cc96-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="5cc96-106">覆寫生命週期方法，在元件初始化和轉譯期間，對元件執行其他作業。</span><span class="sxs-lookup"><span data-stu-id="5cc96-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="5cc96-107">生命週期方法</span><span class="sxs-lookup"><span data-stu-id="5cc96-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="5cc96-108">設定參數之前</span><span class="sxs-lookup"><span data-stu-id="5cc96-108">Before parameters are set</span></span>

<span data-ttu-id="5cc96-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>在轉譯樹狀結構中設定元件的父系所提供的參數：</span><span class="sxs-lookup"><span data-stu-id="5cc96-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="5cc96-110"><xref:Microsoft.AspNetCore.Components.ParameterView>每次呼叫時，包含一組完整的參數值 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="5cc96-111">的預設執行 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 會使用在中具有對應值的或屬性，來設定每個屬性的值 <xref:Microsoft.AspNetCore.Components.ParameterView> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="5cc96-112">在中沒有對應值的參數 <xref:Microsoft.AspNetCore.Components.ParameterView> 會保持不變。</span><span class="sxs-lookup"><span data-stu-id="5cc96-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="5cc96-113">如果 [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) 未叫用，自訂程式碼就可以任何需要的方式解讀傳入的參數值。</span><span class="sxs-lookup"><span data-stu-id="5cc96-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="5cc96-114">例如，不需要將傳入的參數指派給類別的屬性。</span><span class="sxs-lookup"><span data-stu-id="5cc96-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="5cc96-115">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="5cc96-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="5cc96-116">如需詳細資訊，請參閱[ `IDisposable` 使用元件處置](#component-disposal-with-idisposable)一節。</span><span class="sxs-lookup"><span data-stu-id="5cc96-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="5cc96-117">元件初始化方法</span><span class="sxs-lookup"><span data-stu-id="5cc96-117">Component initialization methods</span></span>

<span data-ttu-id="5cc96-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>當元件在中從其父元件收到其初始參數之後，就會叫用和 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="5cc96-119"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>當元件執行非同步作業時使用，而且應該在作業完成時重新整理。</span><span class="sxs-lookup"><span data-stu-id="5cc96-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="5cc96-120">針對同步作業，覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ：</span><span class="sxs-lookup"><span data-stu-id="5cc96-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="5cc96-121">若要執行非同步作業，請覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 並 [`await`](/dotnet/csharp/language-reference/operators/await) 在作業上使用運算子：</span><span class="sxs-lookup"><span data-stu-id="5cc96-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="5cc96-122">Blazor Server將[其內容呼叫呈現](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_兩次_** 的應用程式：</span><span class="sxs-lookup"><span data-stu-id="5cc96-122">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="5cc96-123">當元件一開始以靜態方式轉譯為頁面的一部分時。</span><span class="sxs-lookup"><span data-stu-id="5cc96-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="5cc96-124">第二次當瀏覽器建立與伺服器的連接時。</span><span class="sxs-lookup"><span data-stu-id="5cc96-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="5cc96-125">若要防止中的開發人員程式碼執行 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 兩次，請參閱在預做[後](#stateful-reconnection-after-prerendering)重新設定狀態一節。</span><span class="sxs-lookup"><span data-stu-id="5cc96-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="5cc96-126">當 Blazor Server 應用程式進行預先處理時，某些動作（例如呼叫 JavaScript）並不可能，因為尚未建立與瀏覽器的連接。</span><span class="sxs-lookup"><span data-stu-id="5cc96-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="5cc96-127">元件可能需要在資源清單時以不同的方式呈現。</span><span class="sxs-lookup"><span data-stu-id="5cc96-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="5cc96-128">如需詳細資訊，請參閱偵測[應用程式何時進行預呈現](#detect-when-the-app-is-prerendering)一節。</span><span class="sxs-lookup"><span data-stu-id="5cc96-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="5cc96-129">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="5cc96-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="5cc96-130">如需詳細資訊，請參閱[ `IDisposable` 使用元件處置](#component-disposal-with-idisposable)一節。</span><span class="sxs-lookup"><span data-stu-id="5cc96-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="5cc96-131">設定參數之後</span><span class="sxs-lookup"><span data-stu-id="5cc96-131">After parameters are set</span></span>

<span data-ttu-id="5cc96-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 會呼叫：</span><span class="sxs-lookup"><span data-stu-id="5cc96-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="5cc96-133">在或中初始化元件之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="5cc96-134">當父元件重新呈現和提供時：</span><span class="sxs-lookup"><span data-stu-id="5cc96-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="5cc96-135">只有已知的基本不可變類型，其中至少有一個參數已變更。</span><span class="sxs-lookup"><span data-stu-id="5cc96-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="5cc96-136">任何複雜類型的參數。</span><span class="sxs-lookup"><span data-stu-id="5cc96-136">Any complex-typed parameters.</span></span> <span data-ttu-id="5cc96-137">架構無法得知複雜型別參數的值是否在內部變動，因此它會將參數集視為已變更。</span><span class="sxs-lookup"><span data-stu-id="5cc96-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="5cc96-138">當套用參數和屬性值時，必須在生命週期事件期間進行非同步工作 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="5cc96-139">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="5cc96-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="5cc96-140">如需詳細資訊，請參閱[ `IDisposable` 使用元件處置](#component-disposal-with-idisposable)一節。</span><span class="sxs-lookup"><span data-stu-id="5cc96-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="5cc96-141">元件呈現之後</span><span class="sxs-lookup"><span data-stu-id="5cc96-141">After component render</span></span>

<span data-ttu-id="5cc96-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 元件完成呈現之後，會呼叫和。</span><span class="sxs-lookup"><span data-stu-id="5cc96-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="5cc96-143">此時會填入元素和元件參考。</span><span class="sxs-lookup"><span data-stu-id="5cc96-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="5cc96-144">使用此階段來執行使用轉譯內容的其他初始化步驟，例如啟用在轉譯的 DOM 元素上操作的協力廠商 JavaScript 程式庫。</span><span class="sxs-lookup"><span data-stu-id="5cc96-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="5cc96-145">`firstRender`和的參數 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ：</span><span class="sxs-lookup"><span data-stu-id="5cc96-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="5cc96-146">會 `true` 在第一次呈現元件實例時設定為。</span><span class="sxs-lookup"><span data-stu-id="5cc96-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="5cc96-147">可以用來確保初始化工作只會執行一次。</span><span class="sxs-lookup"><span data-stu-id="5cc96-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="5cc96-148">在生命週期事件期間，必須立即執行非同步工作 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="5cc96-149">即使您 <xref:System.Threading.Tasks.Task> 從傳回 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ，架構也不會在該工作完成後，為您的元件排程進一步的轉譯週期。</span><span class="sxs-lookup"><span data-stu-id="5cc96-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="5cc96-150">這是為了避免無限的呈現迴圈。</span><span class="sxs-lookup"><span data-stu-id="5cc96-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="5cc96-151">它與其他生命週期方法不同，後者會在傳回的工作完成後，排程進一步的轉譯週期。</span><span class="sxs-lookup"><span data-stu-id="5cc96-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="5cc96-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *伺服器上進行預呈現時，不會呼叫和。*</span><span class="sxs-lookup"><span data-stu-id="5cc96-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="5cc96-153">如果已設定任何事件處理常式，請將它們解除鎖定以供處置。</span><span class="sxs-lookup"><span data-stu-id="5cc96-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="5cc96-154">如需詳細資訊，請參閱[ `IDisposable` 使用元件處置](#component-disposal-with-idisposable)一節。</span><span class="sxs-lookup"><span data-stu-id="5cc96-154">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="5cc96-155">隱藏 UI 重新整理</span><span class="sxs-lookup"><span data-stu-id="5cc96-155">Suppress UI refreshing</span></span>

<span data-ttu-id="5cc96-156">覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以隱藏 UI 重新整理。</span><span class="sxs-lookup"><span data-stu-id="5cc96-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="5cc96-157">如果執行傳回 `true` ，則會重新整理 UI：</span><span class="sxs-lookup"><span data-stu-id="5cc96-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="5cc96-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>每次呈現元件時，都會呼叫。</span><span class="sxs-lookup"><span data-stu-id="5cc96-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="5cc96-159">即使 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 已覆寫，元件一律會一開始呈現。</span><span class="sxs-lookup"><span data-stu-id="5cc96-159">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="5cc96-160">如需詳細資訊，請參閱<xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>。</span><span class="sxs-lookup"><span data-stu-id="5cc96-160">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="5cc96-161">狀態變更</span><span class="sxs-lookup"><span data-stu-id="5cc96-161">State changes</span></span>

<span data-ttu-id="5cc96-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>通知元件其狀態已變更。</span><span class="sxs-lookup"><span data-stu-id="5cc96-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="5cc96-163">當適用時，呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會導致元件重新顯示。</span><span class="sxs-lookup"><span data-stu-id="5cc96-163">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="5cc96-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>會針對方法自動呼叫 <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="5cc96-165">如需詳細資訊，請參閱<xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="5cc96-165">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="5cc96-166">處理轉譯時的未完成非同步動作</span><span class="sxs-lookup"><span data-stu-id="5cc96-166">Handle incomplete async actions at render</span></span>

<span data-ttu-id="5cc96-167">在呈現元件之前，在生命週期事件中執行的非同步動作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="5cc96-167">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="5cc96-168">`null`當生命週期方法正在執行時，物件可能會或未完全填入資料。</span><span class="sxs-lookup"><span data-stu-id="5cc96-168">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="5cc96-169">提供轉譯邏輯，以確認物件已初始化。</span><span class="sxs-lookup"><span data-stu-id="5cc96-169">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="5cc96-170">轉譯預留位置 UI 元素 (例如，當物件為時，載入訊息) `null` 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-170">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="5cc96-171">在 `FetchData` 範本的元件中 Blazor ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 會覆寫為 asychronously 接收預測資料 (`forecasts`) 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-171">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="5cc96-172">當 `forecasts` 為時 `null` ，會向使用者顯示載入訊息。</span><span class="sxs-lookup"><span data-stu-id="5cc96-172">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="5cc96-173">在所 `Task` 傳回的 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 完成之後，元件會以更新的狀態重新顯示。</span><span class="sxs-lookup"><span data-stu-id="5cc96-173">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="5cc96-174">`Pages/FetchData.razor`在 Blazor Server 範本中：</span><span class="sxs-lookup"><span data-stu-id="5cc96-174">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="5cc96-175">處理錯誤</span><span class="sxs-lookup"><span data-stu-id="5cc96-175">Handle errors</span></span>

<span data-ttu-id="5cc96-176">如需在生命週期方法執行期間處理錯誤的詳細資訊，請參閱 <xref:blazor/fundamentals/handle-errors#lifecycle-methods> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-176">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="5cc96-177">預呈現後的具狀態重新連接</span><span class="sxs-lookup"><span data-stu-id="5cc96-177">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="5cc96-178">在 Blazor Server 應用程式中 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，當為時 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> ，元件一開始會以靜態方式呈現為頁面的一部分。</span><span class="sxs-lookup"><span data-stu-id="5cc96-178">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="5cc96-179">當瀏覽器建立回到伺服器的連接後，就會*再次*轉譯該元件，而且該元件現在是互動式的。</span><span class="sxs-lookup"><span data-stu-id="5cc96-179">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="5cc96-180">如果 [`OnInitialized{Async}`](#component-initialization-methods) 有用來初始化元件的生命週期方法，則會執行*兩次*方法：</span><span class="sxs-lookup"><span data-stu-id="5cc96-180">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="5cc96-181">當元件以靜態方式資源清單時。</span><span class="sxs-lookup"><span data-stu-id="5cc96-181">When the component is prerendered statically.</span></span>
* <span data-ttu-id="5cc96-182">建立伺服器連接之後。</span><span class="sxs-lookup"><span data-stu-id="5cc96-182">After the server connection has been established.</span></span>

<span data-ttu-id="5cc96-183">這可能會導致在最後呈現元件時，UI 中顯示的資料有明顯的變更。</span><span class="sxs-lookup"><span data-stu-id="5cc96-183">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="5cc96-184">若要避免應用程式中的雙呈現案例 Blazor Server ：</span><span class="sxs-lookup"><span data-stu-id="5cc96-184">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="5cc96-185">傳入識別碼，可在自動處理期間用來快取狀態，並在應用程式重新開機之後，取得狀態。</span><span class="sxs-lookup"><span data-stu-id="5cc96-185">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="5cc96-186">在預入期間使用識別碼來儲存元件狀態。</span><span class="sxs-lookup"><span data-stu-id="5cc96-186">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="5cc96-187">在可呈現後使用識別碼，以取得快取的狀態。</span><span class="sxs-lookup"><span data-stu-id="5cc96-187">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="5cc96-188">下列程式碼示範 `WeatherForecastService` 在以範本為基礎的 Blazor Server 應用程式中，已更新，可避免雙重呈現：</span><span class="sxs-lookup"><span data-stu-id="5cc96-188">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="5cc96-189">如需的詳細資訊 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，請參閱 <xref:blazor/fundamentals/additional-scenarios#render-mode> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-189">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="5cc96-190">偵測應用程式何時已進行預呈現</span><span class="sxs-lookup"><span data-stu-id="5cc96-190">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="5cc96-191">使用 IDisposable 的元件處置</span><span class="sxs-lookup"><span data-stu-id="5cc96-191">Component disposal with IDisposable</span></span>

<span data-ttu-id="5cc96-192">如果元件會執行 <xref:System.IDisposable> ，則會在從 UI 中移除元件時呼叫[ `Dispose` 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="5cc96-192">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="5cc96-193">可隨時進行處置，包括[元件初始化](#component-initialization-methods)期間。</span><span class="sxs-lookup"><span data-stu-id="5cc96-193">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="5cc96-194">下列元件會使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="5cc96-194">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="5cc96-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>不支援在中呼叫 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-195">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="5cc96-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>可能會在卸載轉譯器的過程中叫用，因此不支援在該時間點要求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="5cc96-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="5cc96-197">取消訂閱來自 .NET 事件的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="5cc96-197">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="5cc96-198">下列[ Blazor 表單](xref:blazor/forms-validation)範例示範如何在方法中解除掛接事件處理常式 `Dispose` ：</span><span class="sxs-lookup"><span data-stu-id="5cc96-198">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="5cc96-199">私用欄位和 lambda 方法</span><span class="sxs-lookup"><span data-stu-id="5cc96-199">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="5cc96-200">私用方法方法</span><span class="sxs-lookup"><span data-stu-id="5cc96-200">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="5cc96-201">可取消的背景工作</span><span class="sxs-lookup"><span data-stu-id="5cc96-201">Cancelable background work</span></span>

<span data-ttu-id="5cc96-202">元件通常會執行長時間執行的背景工作，例如進行網路呼叫 (<xref:System.Net.Http.HttpClient>) 並與資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="5cc96-202">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="5cc96-203">在幾種情況下，最好停止背景工作以節省系統資源。</span><span class="sxs-lookup"><span data-stu-id="5cc96-203">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="5cc96-204">例如，當使用者離開元件時，背景非同步作業不會自動停止。</span><span class="sxs-lookup"><span data-stu-id="5cc96-204">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="5cc96-205">背景工作專案可能需要取消的其他原因包括：</span><span class="sxs-lookup"><span data-stu-id="5cc96-205">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="5cc96-206">執行中的背景工作使用了錯誤的輸入資料或處理參數來啟動。</span><span class="sxs-lookup"><span data-stu-id="5cc96-206">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="5cc96-207">目前執行中的背景工作專案集必須以一組新的工作專案取代。</span><span class="sxs-lookup"><span data-stu-id="5cc96-207">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="5cc96-208">必須變更目前正在執行之工作的優先順序。</span><span class="sxs-lookup"><span data-stu-id="5cc96-208">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="5cc96-209">必須關閉應用程式，才能將它重新部署到伺服器。</span><span class="sxs-lookup"><span data-stu-id="5cc96-209">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="5cc96-210">伺服器資源會受到限制，請強制背景工作專案的重新排定。</span><span class="sxs-lookup"><span data-stu-id="5cc96-210">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="5cc96-211">若要在元件中執行可取消的背景工作模式：</span><span class="sxs-lookup"><span data-stu-id="5cc96-211">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="5cc96-212">使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken> 。</span><span class="sxs-lookup"><span data-stu-id="5cc96-212">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="5cc96-213">在處理[元件](#component-disposal-with-idisposable)時，以及在任何點取消作業需要手動取消權杖時，請呼叫 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示應該取消背景工作。</span><span class="sxs-lookup"><span data-stu-id="5cc96-213">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="5cc96-214">在非同步呼叫傳回之後，呼叫 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> 權杖上的。</span><span class="sxs-lookup"><span data-stu-id="5cc96-214">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="5cc96-215">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="5cc96-215">In the following example:</span></span>

* <span data-ttu-id="5cc96-216">`await Task.Delay(5000, cts.Token);`代表長時間執行的非同步背景工作。</span><span class="sxs-lookup"><span data-stu-id="5cc96-216">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="5cc96-217">`BackgroundResourceMethod`代表長時間執行的背景方法，如果在 `Resource` 呼叫方法之前處置，則不應該啟動。</span><span class="sxs-lookup"><span data-stu-id="5cc96-217">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
