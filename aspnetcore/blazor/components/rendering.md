---
title: ASP.NET Core Blazor 元件轉譯
author: guardrex
description: 瞭解 Razor ASP.NET Core apps 中的元件轉譯 Blazor ，包括何時呼叫 StateHasChanged。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
ms.openlocfilehash: 27701d175c86cdf4b74a9e6332b30b4d55806650
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107034"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="1e0bb-103">ASP.NET Core Blazor 元件轉譯</span><span class="sxs-lookup"><span data-stu-id="1e0bb-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="1e0bb-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="1e0bb-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="1e0bb-105">元件第一次依其父元件新增至元件階層時， *必須* 呈現。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-105">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="1e0bb-106">這是元件嚴格必須呈現的唯一時間。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-106">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="1e0bb-107">元件 *可能會* 選擇根據自己的邏輯和慣例，在任何其他時間進行轉譯。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-107">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="1e0bb-108">的慣例 `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="1e0bb-108">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="1e0bb-109">根據預設， Razor 元件 (`.razor`) 會繼承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 基類，此類別包含在下列時間觸發轉譯資料流程的邏輯：</span><span class="sxs-lookup"><span data-stu-id="1e0bb-109">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="1e0bb-110">從父元件套用一組更新的參數之後。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-110">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="1e0bb-111">套用串聯參數的更新值之後。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-111">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="1e0bb-112">在事件的通知之後，叫用它自己的其中一個事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-112">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="1e0bb-113">呼叫它自己的方法之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-113">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="1e0bb-114"><xref:Microsoft.AspNetCore.Components.ComponentBase>如果下列任一條件成立，則繼承自 skip 轉譯中的元件，因為參數更新：</span><span class="sxs-lookup"><span data-stu-id="1e0bb-114">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="1e0bb-115">所有的參數值都是已知的不可變基本類型 (例如， `int` 、 `string` 、 `DateTime`) ，而且自先前設定的參數集以來尚未變更。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-115">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="1e0bb-116">元件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 方法會傳回 `false` 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-116">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="1e0bb-117">如需 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的詳細資訊，請參閱 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-117">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="1e0bb-118">控制轉譯流程</span><span class="sxs-lookup"><span data-stu-id="1e0bb-118">Control the rendering flow</span></span>

<span data-ttu-id="1e0bb-119">在大部分的情況下， <xref:Microsoft.AspNetCore.Components.ComponentBase> 慣例會在事件發生後產生正確的元件轉譯中子集。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-119">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="1e0bb-120">開發人員通常不需要提供手動邏輯來告訴架構要 rerender 的元件，以及 rerender 它們的時機。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-120">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="1e0bb-121">架構慣例的整體效果是元件接收事件轉譯中本身，以遞迴方式觸發轉譯資料流程其子元件的參數值可能已變更。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-121">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="1e0bb-122">如需有關架構慣例的效能含意，以及如何將應用程式的元件階層優化的詳細資訊，請參閱 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-122">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="1e0bb-123">呼叫時機 `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="1e0bb-123">When to call `StateHasChanged`</span></span>

<span data-ttu-id="1e0bb-124"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType>方法可讓您在任何時間觸發轉譯。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-124">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="1e0bb-125">不過，請小心不要呼叫不 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 必要的，這是常見的錯誤，因為它會產生不必要的轉譯成本。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-125">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="1e0bb-126">在下列情況中，您應該 *不* 需要呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> ：</span><span class="sxs-lookup"><span data-stu-id="1e0bb-126">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="1e0bb-127">定期處理事件，無論是同步或非同步，因為都會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 觸發大部分例行事件處理常式的轉譯。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-127">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="1e0bb-128">執行一般的生命週期邏輯（例如 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 或），而 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) 不論是 synchonrously 還是非同步，因為會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 觸發一般生命週期事件的轉譯。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-128">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="1e0bb-129">不過， <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在下列各節所述的案例中呼叫可能很合理：</span><span class="sxs-lookup"><span data-stu-id="1e0bb-129">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="1e0bb-130">非同步處理常式牽涉到多個非同步階段</span><span class="sxs-lookup"><span data-stu-id="1e0bb-130">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="1e0bb-131">從轉譯 Blazor 和事件處理系統外部的某個專案接收呼叫</span><span class="sxs-lookup"><span data-stu-id="1e0bb-131">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="1e0bb-132">在特定事件所保存的子樹狀結構外轉譯元件</span><span class="sxs-lookup"><span data-stu-id="1e0bb-132">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="1e0bb-133">非同步處理常式牽涉到多個非同步階段</span><span class="sxs-lookup"><span data-stu-id="1e0bb-133">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="1e0bb-134">請考慮下列 `Counter` 元件，這會在每次按一下時更新計數四次。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-134">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="1e0bb-135">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="1e0bb-135">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="1e0bb-136">由於在 .NET 中定義工作的方式，的接收者 <xref:System.Threading.Tasks.Task> 只能觀察其最終完成，而非中繼非同步狀態。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-136">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="1e0bb-137">因此， <xref:Microsoft.AspNetCore.Components.ComponentBase> 只有在 <xref:System.Threading.Tasks.Task> 第一次傳回時以及最後完成時，才可以觸發轉譯資料流程 <xref:System.Threading.Tasks.Task> 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-137">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="1e0bb-138">它無法知道要在其他中繼點 rerender。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-138">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="1e0bb-139">如果您想要在中繼點 rerender，請使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-139">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="1e0bb-140">從轉譯 Blazor 和事件處理系統外部的某個專案接收呼叫</span><span class="sxs-lookup"><span data-stu-id="1e0bb-140">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="1e0bb-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> 只知道自己的生命週期方法和 Blazor 觸發的事件。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="1e0bb-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> 不知道程式碼中可能發生的其他事件。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="1e0bb-143">例如，自訂資料存放區所引發的任何 c # 事件都是未知的 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-143">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="1e0bb-144">為了讓這類事件觸發轉譯資料流程，以在 UI 中顯示更新的值，請使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-144">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="1e0bb-145">在另一個使用案例中，請考慮使用的下列 `Counter` 元件， <xref:System.Timers.Timer?displayProperty=fullName> 以定期更新計數和呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 來更新 UI。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-145">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="1e0bb-146">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="1e0bb-146">`Pages/CounterWithTimerDisposal.razor`:</span></span>

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

<span data-ttu-id="1e0bb-147">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="1e0bb-147">In the preceding example:</span></span>

* <span data-ttu-id="1e0bb-148">`OnTimerCallback` 必須呼叫， <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 因為 Blazor 不知道 `currentCount` 回呼中的變更。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-148">`OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="1e0bb-149">`OnTimerCallback` 在任何 managed 轉譯 Blazor 流程或事件通知之外執行。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-149">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>
* <span data-ttu-id="1e0bb-150">元件會執行 <xref:System.IDisposable> ，其中 <xref:System.Timers.Timer> 會在架構呼叫方法時處置 `Dispose` 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-150">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="1e0bb-151">如需詳細資訊，請參閱<xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-151">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="1e0bb-152">同樣地，因為回呼是 Blazor 在同步處理內容之外叫用，所以必須將邏輯包裝在中， <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 才能將它移到轉譯器的同步處理內容。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-152">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="1e0bb-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 只能從轉譯器的同步處理內容呼叫，否則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="1e0bb-154">這相當於將其他 UI 架構中的 UI 執行緒封送處理。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-154">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="1e0bb-155">若要在特定事件所保存的子樹狀結構外呈現元件</span><span class="sxs-lookup"><span data-stu-id="1e0bb-155">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="1e0bb-156">您的 UI 可能牽涉到將事件分派至一個元件、變更某些狀態，以及需要 rerender 完全不同的元件，而不是接收事件的子系。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-156">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="1e0bb-157">處理此案例的其中一種方式是將 *狀態管理* 類別（可能是 DI 服務）插入多個元件中。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-157">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="1e0bb-158">當某個元件在狀態管理員上呼叫方法時，狀態管理員可以引發 c # 事件，然後由獨立元件接收該事件。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-158">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="1e0bb-159">因為這些 c # 事件是在轉譯 Blazor 管線之外，所以請 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在您想要轉譯的其他元件上呼叫，以回應狀態管理員的事件。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-159">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="1e0bb-160">這與上一節的先前案例類似 <xref:System.Timers.Timer?displayProperty=fullName> 。 [](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)</span><span class="sxs-lookup"><span data-stu-id="1e0bb-160">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="1e0bb-161">由於執行呼叫堆疊通常會保留在轉譯器的同步處理內容上，因此 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> 通常不需要。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-161">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="1e0bb-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> 只有當邏輯將同步處理內容（例如 <xref:System.Threading.Tasks.Task.ContinueWith%2A> 在上呼叫 <xref:System.Threading.Tasks.Task> 或等候進行呼叫）時，才需要 <xref:System.Threading.Tasks.Task> [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) 。</span><span class="sxs-lookup"><span data-stu-id="1e0bb-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
