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
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253927"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a>ASP.NET Core Blazor 元件轉譯

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

元件第一次依其父元件新增至元件階層時， *必須* 呈現。 這是元件嚴格必須呈現的唯一時間。

元件 *可能會* 選擇根據自己的邏輯和慣例，在任何其他時間進行轉譯。

## <a name="conventions-for-componentbase"></a>的慣例 `ComponentBase`

根據預設， Razor 元件 (`.razor`) 會繼承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 基類，此類別包含在下列時間觸發轉譯資料流程的邏輯：

* 從父元件套用一組更新的參數之後。
* 套用串聯參數的更新值之後。
* 在事件的通知之後，叫用它自己的其中一個事件處理常式。
* 呼叫它自己的方法之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 。

<xref:Microsoft.AspNetCore.Components.ComponentBase>如果下列任一條件成立，則繼承自 skip 轉譯中的元件，因為參數更新：

* 所有的參數值都是已知的不可變基本類型 (例如， `int` 、 `string` 、 `DateTime`) ，而且自先前設定的參數集以來尚未變更。
* 元件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 方法會傳回 `false` 。

如需 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的詳細資訊，請參閱 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>。

## <a name="control-the-rendering-flow"></a>控制轉譯流程

在大部分的情況下， <xref:Microsoft.AspNetCore.Components.ComponentBase> 慣例會在事件發生後產生正確的元件轉譯中子集。 開發人員通常不需要提供手動邏輯來告訴架構要 rerender 的元件，以及 rerender 它們的時機。 架構慣例的整體效果是元件接收事件轉譯中本身，以遞迴方式觸發轉譯資料流程其子元件的參數值可能已變更。

如需有關架構慣例的效能含意，以及如何將應用程式的元件階層優化的詳細資訊，請參閱 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> 。

## <a name="when-to-call-statehaschanged"></a>呼叫時機 `StateHasChanged`

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType>方法可讓您在任何時間觸發轉譯。 不過，請小心不要呼叫不 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 必要的，這是常見的錯誤，因為它會產生不必要的轉譯成本。

在下列情況中，您應該 *不* 需要呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> ：

* 定期處理事件，無論是同步或非同步，因為都會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 觸發大部分例行事件處理常式的轉譯。
* 執行一般的生命週期邏輯（例如 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 或），而 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) 不論是 synchonrously 還是非同步，因為會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 觸發一般生命週期事件的轉譯。

不過， <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在下列各節所述的案例中呼叫可能很合理：

* [非同步處理常式牽涉到多個非同步階段](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [從轉譯 Blazor 和事件處理系統外部的某個專案接收呼叫](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [在特定事件所保存的子樹狀結構外轉譯元件](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>非同步處理常式牽涉到多個非同步階段

請考慮下列 `Counter` 元件，這會在每次按一下時更新計數四次。

`Pages/Counter.razor`:

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

由於在 .NET 中定義工作的方式，的接收者 <xref:System.Threading.Tasks.Task> 只能觀察其最終完成，而非中繼非同步狀態。 因此， <xref:Microsoft.AspNetCore.Components.ComponentBase> 只有在 <xref:System.Threading.Tasks.Task> 第一次傳回時以及最後完成時，才可以觸發轉譯資料流程 <xref:System.Threading.Tasks.Task> 。 它無法知道要在其他中繼點 rerender。 如果您想要在中繼點 rerender，請使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 。

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a>從轉譯 Blazor 和事件處理系統外部的某個專案接收呼叫

<xref:Microsoft.AspNetCore.Components.ComponentBase> 只知道自己的生命週期方法和 Blazor 觸發的事件。 <xref:Microsoft.AspNetCore.Components.ComponentBase> 不知道程式碼中可能發生的其他事件。 例如，自訂資料存放區所引發的任何 c # 事件都是未知的 Blazor 。 為了讓這類事件觸發轉譯資料流程，以在 UI 中顯示更新的值，請使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 。

在另一個使用案例中，請考慮使用的下列 `Counter` 元件， <xref:System.Timers.Timer?displayProperty=fullName> 以定期更新計數和呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 來更新 UI。

`Pages/Counter.razor`:

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

在上述範例中， `OnTimerCallback` 必須呼叫， <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 因為 Blazor 不知道 `currentCount` 回呼中的變更。 `OnTimerCallback` 在任何 managed 轉譯 Blazor 流程或事件通知之外執行。

同樣地，因為回呼是 Blazor 在同步處理內容之外叫用，所以必須將邏輯包裝在中， <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 才能將它移到轉譯器的同步處理內容。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 只能從轉譯器的同步處理內容呼叫，否則會擲回例外狀況。 這相當於將其他 UI 架構中的 UI 執行緒封送處理。

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>若要在特定事件所保存的子樹狀結構外呈現元件

您的 UI 可能牽涉到將事件分派至一個元件、變更某些狀態，以及需要 rerender 完全不同的元件，而不是接收事件的子系。

處理此案例的其中一種方式是將 *狀態管理* 類別（可能是 DI 服務）插入多個元件中。 當某個元件在狀態管理員上呼叫方法時，狀態管理員可以引發 c # 事件，然後由獨立元件接收該事件。

因為這些 c # 事件是在轉譯 Blazor 管線之外，所以請 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在您想要轉譯的其他元件上呼叫，以回應狀態管理員的事件。

這與上一節的先前案例類似 <xref:System.Timers.Timer?displayProperty=fullName> 。 [](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) 由於執行呼叫堆疊通常會保留在轉譯器的同步處理內容上，因此 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> 通常不需要。 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> 只有當邏輯將同步處理內容（例如 <xref:System.Threading.Tasks.Task.ContinueWith%2A> 在上呼叫 <xref:System.Threading.Tasks.Task> 或等候進行呼叫）時，才需要 <xref:System.Threading.Tasks.Task> [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) 。
