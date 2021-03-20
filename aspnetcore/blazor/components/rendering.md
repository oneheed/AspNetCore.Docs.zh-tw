---
title: ASP.NET Core Blazor 元件轉譯
author: guardrex
description: 瞭解 Razor ASP.NET Core apps 中的元件轉譯 Blazor ，包括何時呼叫 StateHasChanged。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: 1d244434cd3aa7e1a49839cc0c6cecb61abbcdff
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711330"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="10a06-103">ASP.NET Core Blazor 元件轉譯</span><span class="sxs-lookup"><span data-stu-id="10a06-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="10a06-104">當元件第一次由父元件新增至元件階層時， *必須* 呈現這些元件。</span><span class="sxs-lookup"><span data-stu-id="10a06-104">Components *must* render when they're first added to the component hierarchy by a parent component.</span></span> <span data-ttu-id="10a06-105">這是元件必須轉譯的唯一時間。</span><span class="sxs-lookup"><span data-stu-id="10a06-105">This is the only time that a component must render.</span></span> <span data-ttu-id="10a06-106">元件 *可能會* 根據自己的邏輯和慣例，在其他時間轉譯。</span><span class="sxs-lookup"><span data-stu-id="10a06-106">Components *may* render at other times according to their own logic and conventions.</span></span>

## <a name="rendering-conventions-for-componentbase"></a><span data-ttu-id="10a06-107">的呈現慣例 `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="10a06-107">Rendering conventions for `ComponentBase`</span></span>

<span data-ttu-id="10a06-108">根據預設， Razor 元件會繼承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 基類，此類別包含在下列時間觸發轉譯資料流程的邏輯：</span><span class="sxs-lookup"><span data-stu-id="10a06-108">By default, Razor components inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="10a06-109">從父元件套用一組更新的 [參數](xref:blazor/components/data-binding#binding-with-component-parameters) 之後。</span><span class="sxs-lookup"><span data-stu-id="10a06-109">After applying an updated set of [parameters](xref:blazor/components/data-binding#binding-with-component-parameters) from a parent component.</span></span>
* <span data-ttu-id="10a06-110">套用串聯 [參數](xref:blazor/components/cascading-values-and-parameters)的更新值之後。</span><span class="sxs-lookup"><span data-stu-id="10a06-110">After applying an updated value for a [cascading parameter](xref:blazor/components/cascading-values-and-parameters).</span></span>
* <span data-ttu-id="10a06-111">在事件的通知之後，叫用它自己的其中一個 [事件處理常式](xref:blazor/components/event-handling)。</span><span class="sxs-lookup"><span data-stu-id="10a06-111">After notification of an event and invoking one of its own [event handlers](xref:blazor/components/event-handling).</span></span>
* <span data-ttu-id="10a06-112">呼叫自己的 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 方法之後 (請參閱[ Blazor 生命週期：狀態變更](xref:blazor/components/lifecycle#state-changes)) 。</span><span class="sxs-lookup"><span data-stu-id="10a06-112">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method (see [Blazor lifecycle: State changes](xref:blazor/components/lifecycle#state-changes)).</span></span>

<span data-ttu-id="10a06-113"><xref:Microsoft.AspNetCore.Components.ComponentBase>如果下列任一條件成立，則繼承自 skip 轉譯中的元件，因為參數更新：</span><span class="sxs-lookup"><span data-stu-id="10a06-113">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="10a06-114">所有的參數值都是已知的不可變基本型別，例如 `int` ，、、 `string` `DateTime` 和在設定前一組參數之後尚未變更。</span><span class="sxs-lookup"><span data-stu-id="10a06-114">All of the parameter values are of known immutable primitive types, such as `int`, `string`, `DateTime`, and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="10a06-115">元件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 方法會傳回 `false` 。</span><span class="sxs-lookup"><span data-stu-id="10a06-115">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="10a06-116">如需 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的詳細資訊，請參閱 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>。</span><span class="sxs-lookup"><span data-stu-id="10a06-116">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="10a06-117">控制轉譯流程</span><span class="sxs-lookup"><span data-stu-id="10a06-117">Control the rendering flow</span></span>

<span data-ttu-id="10a06-118">在大部分的情況下， <xref:Microsoft.AspNetCore.Components.ComponentBase> 慣例會在事件發生後產生正確的元件轉譯中子集。</span><span class="sxs-lookup"><span data-stu-id="10a06-118">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="10a06-119">開發人員通常不需要提供手動邏輯來告訴架構要 rerender 的元件，以及 rerender 它們的時機。</span><span class="sxs-lookup"><span data-stu-id="10a06-119">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="10a06-120">架構慣例的整體效果是元件接收事件轉譯中本身，以遞迴方式觸發轉譯資料流程其子元件的參數值可能已變更。</span><span class="sxs-lookup"><span data-stu-id="10a06-120">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="10a06-121">如需架構慣例的效能含意，以及如何將應用程式的元件階層優化以進行轉譯的詳細資訊，請參閱 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> 。</span><span class="sxs-lookup"><span data-stu-id="10a06-121">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy for rendering, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="10a06-122">呼叫時機 `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="10a06-122">When to call `StateHasChanged`</span></span>

<span data-ttu-id="10a06-123">呼叫可 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 讓您隨時觸發轉譯。</span><span class="sxs-lookup"><span data-stu-id="10a06-123">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> allows you to trigger a render at any time.</span></span> <span data-ttu-id="10a06-124">不過，請小心不要呼叫不 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 必要的，這是造成不必要轉譯成本的常見錯誤。</span><span class="sxs-lookup"><span data-stu-id="10a06-124">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake that imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="10a06-125">程式碼不應該 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在下列情況呼叫：</span><span class="sxs-lookup"><span data-stu-id="10a06-125">Code shouldn't need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="10a06-126">定期處理事件，無論是同步或非同步，因為都會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 觸發大部分例行事件處理常式的轉譯。</span><span class="sxs-lookup"><span data-stu-id="10a06-126">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="10a06-127">執行一般的生命週期邏輯（例如 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 或），而 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) 不論是 synchonrously 還是非同步，因為會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 觸發一般生命週期事件的轉譯。</span><span class="sxs-lookup"><span data-stu-id="10a06-127">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="10a06-128">不過，在本文的 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 下列各節所述的案例中，可能有意義：</span><span class="sxs-lookup"><span data-stu-id="10a06-128">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described in the following sections of this article:</span></span>

* [<span data-ttu-id="10a06-129">非同步處理常式牽涉到多個非同步階段</span><span class="sxs-lookup"><span data-stu-id="10a06-129">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="10a06-130">從轉譯 Blazor 和事件處理系統外部的某個專案接收呼叫</span><span class="sxs-lookup"><span data-stu-id="10a06-130">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="10a06-131">在特定事件所保存的子樹狀結構外轉譯元件</span><span class="sxs-lookup"><span data-stu-id="10a06-131">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="10a06-132">非同步處理常式牽涉到多個非同步階段</span><span class="sxs-lookup"><span data-stu-id="10a06-132">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="10a06-133">由於在 .NET 中定義工作的方式，的接收者 <xref:System.Threading.Tasks.Task> 只能觀察其最終完成，而非中繼非同步狀態。</span><span class="sxs-lookup"><span data-stu-id="10a06-133">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="10a06-134">因此， <xref:Microsoft.AspNetCore.Components.ComponentBase> 只有在 <xref:System.Threading.Tasks.Task> 第一次傳回時以及最後完成時，才可以觸發轉譯資料流程 <xref:System.Threading.Tasks.Task> 。</span><span class="sxs-lookup"><span data-stu-id="10a06-134">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="10a06-135">架構不知道要在其他中繼點 rerender 元件。</span><span class="sxs-lookup"><span data-stu-id="10a06-135">The framework can't know to rerender a component at other intermediate points.</span></span> <span data-ttu-id="10a06-136">如果您想要在中繼點上 rerender，請 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在這些點呼叫。</span><span class="sxs-lookup"><span data-stu-id="10a06-136">If you want to rerender at intermediate points, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> at those points.</span></span>

<span data-ttu-id="10a06-137">請考慮下列 `CounterState1` 元件，這會在每次按一下時更新計數四次：</span><span class="sxs-lookup"><span data-stu-id="10a06-137">Consider the following `CounterState1` component, which updates the count four times on each click:</span></span>

* <span data-ttu-id="10a06-138">自動轉譯會在的第一個和最後一個增量之後發生 `currentCount` 。</span><span class="sxs-lookup"><span data-stu-id="10a06-138">Automatic renders occur after the first and last increments of `currentCount`.</span></span>
* <span data-ttu-id="10a06-139"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>當架構未在遞增的中繼處理點上自動觸發轉譯中時，呼叫會觸發手動呈現 `currentCount` 。</span><span class="sxs-lookup"><span data-stu-id="10a06-139">Manual renders are triggered by calls to <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when the framework doesn't automatically trigger rerenders at intermediate processing points where `currentCount` is incremented.</span></span>

<span data-ttu-id="10a06-140">`Pages/CounterState1.razor`:</span><span class="sxs-lookup"><span data-stu-id="10a06-140">`Pages/CounterState1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/rendering/CounterState1.razor?highlight=17,21,25,29)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/rendering/CounterState1.razor?highlight=17,21,25,29)]

::: moniker-end

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="10a06-141">從轉譯 Blazor 和事件處理系統外部的某個專案接收呼叫</span><span class="sxs-lookup"><span data-stu-id="10a06-141">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="10a06-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> 只知道自己的生命週期方法和 Blazor 觸發的事件。</span><span class="sxs-lookup"><span data-stu-id="10a06-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="10a06-143"><xref:Microsoft.AspNetCore.Components.ComponentBase> 不知道程式碼中可能發生的其他事件。</span><span class="sxs-lookup"><span data-stu-id="10a06-143"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in code.</span></span> <span data-ttu-id="10a06-144">例如，自訂資料存放區所引發的任何 c # 事件都是未知的 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="10a06-144">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="10a06-145">為了讓這類事件觸發轉譯資料流程，以在 UI 中顯示更新的值，請呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 。</span><span class="sxs-lookup"><span data-stu-id="10a06-145">In order for such events to trigger rerendering to display updated values in the UI, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="10a06-146">請考慮 `CounterState2` 使用下列元件， <xref:System.Timers.Timer?displayProperty=fullName> 以定期更新計數和呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 來更新 UI：</span><span class="sxs-lookup"><span data-stu-id="10a06-146">Consider the following `CounterState2` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update a count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI:</span></span>

* <span data-ttu-id="10a06-147">`OnTimerCallback` 在任何 managed 轉譯 Blazor 流程或事件通知之外執行。</span><span class="sxs-lookup"><span data-stu-id="10a06-147">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span> <span data-ttu-id="10a06-148">因此， `OnTimerCallback` 必須呼叫， <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 因為 Blazor 不知道 `currentCount` 回呼中的變更。</span><span class="sxs-lookup"><span data-stu-id="10a06-148">Therefore, `OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span>
* <span data-ttu-id="10a06-149">元件會執行 <xref:System.IDisposable> ，其中 <xref:System.Timers.Timer> 會在架構呼叫方法時處置 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="10a06-149">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="10a06-150">如需詳細資訊，請參閱<xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="10a06-150">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="10a06-151">由於回呼是在的同步處理 Blazor 內容之外叫用，因此元件必須包裝中的邏輯， `OnTimerCallback` <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 才能將它移到轉譯器的同步處理內容。</span><span class="sxs-lookup"><span data-stu-id="10a06-151">Because the callback is invoked outside of Blazor's synchronization context, the component must wrap the logic of `OnTimerCallback` in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="10a06-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 只能從轉譯器的同步處理內容呼叫，否則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="10a06-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="10a06-153">這相當於將其他 UI 架構中的 UI 執行緒封送處理。</span><span class="sxs-lookup"><span data-stu-id="10a06-153">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

<span data-ttu-id="10a06-154">`Pages/CounterState2.razor`:</span><span class="sxs-lookup"><span data-stu-id="10a06-154">`Pages/CounterState2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/rendering/CounterState2.razor?highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/rendering/CounterState2.razor?highlight=26)]

::: moniker-end

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="10a06-155">若要在特定事件所保存的子樹狀結構外呈現元件</span><span class="sxs-lookup"><span data-stu-id="10a06-155">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="10a06-156">UI 可能包括：</span><span class="sxs-lookup"><span data-stu-id="10a06-156">The UI might involve:</span></span>

1. <span data-ttu-id="10a06-157">將事件分派至一個元件。</span><span class="sxs-lookup"><span data-stu-id="10a06-157">Dispatching an event to one component.</span></span>
1. <span data-ttu-id="10a06-158">變更某些狀態。</span><span class="sxs-lookup"><span data-stu-id="10a06-158">Changing some state.</span></span>
1. <span data-ttu-id="10a06-159">轉譯資料流程完全不同的元件，而不是接收事件的元件的子系。</span><span class="sxs-lookup"><span data-stu-id="10a06-159">Rerendering a completely different component that isn't a descendant of the component receiving the event.</span></span>

<span data-ttu-id="10a06-160">處理此案例的其中一種方式，是提供 *狀態管理* 類別，通常是在插入多個元件 (DI) 服務的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="10a06-160">One way to deal with this scenario is to provide a *state management* class, often as a dependency injection (DI) service, injected into multiple components.</span></span> <span data-ttu-id="10a06-161">當某個元件在狀態管理員上呼叫方法時，狀態管理員會引發 c # 事件，然後由獨立元件接收該事件。</span><span class="sxs-lookup"><span data-stu-id="10a06-161">When one component calls a method on the state manager, the state manager raises a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="10a06-162">因為這些 c # 事件是在轉譯 Blazor 管線之外，所以請 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在您想要轉譯的其他元件上呼叫，以回應狀態管理員的事件。</span><span class="sxs-lookup"><span data-stu-id="10a06-162">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="10a06-163">這與 <xref:System.Timers.Timer?displayProperty=fullName> [上一節](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)中的稍早案例類似。</span><span class="sxs-lookup"><span data-stu-id="10a06-163">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system).</span></span> <span data-ttu-id="10a06-164">由於執行呼叫堆疊通常會保留在轉譯器的同步處理內容中，因此 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> 通常不需要呼叫。</span><span class="sxs-lookup"><span data-stu-id="10a06-164">Since the execution call stack typically remains on the renderer's synchronization context, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="10a06-165"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>只有當邏輯將同步處理內容（例如 <xref:System.Threading.Tasks.Task.ContinueWith%2A> 在上呼叫 <xref:System.Threading.Tasks.Task> 或等候進行呼叫）時，才需要呼叫 <xref:System.Threading.Tasks.Task> [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) 。</span><span class="sxs-lookup"><span data-stu-id="10a06-165">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
