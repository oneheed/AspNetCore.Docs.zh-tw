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
# <a name="aspnet-core-blazor-component-rendering"></a>ASP.NET Core Blazor 元件轉譯

當元件第一次由父元件新增至元件階層時， *必須* 呈現這些元件。 這是元件必須轉譯的唯一時間。 元件 *可能會* 根據自己的邏輯和慣例，在其他時間轉譯。

## <a name="rendering-conventions-for-componentbase"></a>的呈現慣例 `ComponentBase`

根據預設， Razor 元件會繼承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 基類，此類別包含在下列時間觸發轉譯資料流程的邏輯：

* 從父元件套用一組更新的 [參數](xref:blazor/components/data-binding#binding-with-component-parameters) 之後。
* 套用串聯 [參數](xref:blazor/components/cascading-values-and-parameters)的更新值之後。
* 在事件的通知之後，叫用它自己的其中一個 [事件處理常式](xref:blazor/components/event-handling)。
* 呼叫自己的 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 方法之後 (請參閱[ Blazor 生命週期：狀態變更](xref:blazor/components/lifecycle#state-changes)) 。

<xref:Microsoft.AspNetCore.Components.ComponentBase>如果下列任一條件成立，則繼承自 skip 轉譯中的元件，因為參數更新：

* 所有的參數值都是已知的不可變基本型別，例如 `int` ，、、 `string` `DateTime` 和在設定前一組參數之後尚未變更。
* 元件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 方法會傳回 `false` 。

如需 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的詳細資訊，請參閱 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>。

## <a name="control-the-rendering-flow"></a>控制轉譯流程

在大部分的情況下， <xref:Microsoft.AspNetCore.Components.ComponentBase> 慣例會在事件發生後產生正確的元件轉譯中子集。 開發人員通常不需要提供手動邏輯來告訴架構要 rerender 的元件，以及 rerender 它們的時機。 架構慣例的整體效果是元件接收事件轉譯中本身，以遞迴方式觸發轉譯資料流程其子元件的參數值可能已變更。

如需架構慣例的效能含意，以及如何將應用程式的元件階層優化以進行轉譯的詳細資訊，請參閱 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed> 。

## <a name="when-to-call-statehaschanged"></a>呼叫時機 `StateHasChanged`

呼叫可 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 讓您隨時觸發轉譯。 不過，請小心不要呼叫不 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 必要的，這是造成不必要轉譯成本的常見錯誤。

程式碼不應該 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在下列情況呼叫：

* 定期處理事件，無論是同步或非同步，因為都會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 觸發大部分例行事件處理常式的轉譯。
* 執行一般的生命週期邏輯（例如 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 或），而 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) 不論是 synchonrously 還是非同步，因為會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 觸發一般生命週期事件的轉譯。

不過，在本文的 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 下列各節所述的案例中，可能有意義：

* [非同步處理常式牽涉到多個非同步階段](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [從轉譯 Blazor 和事件處理系統外部的某個專案接收呼叫](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [在特定事件所保存的子樹狀結構外轉譯元件](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a>非同步處理常式牽涉到多個非同步階段

由於在 .NET 中定義工作的方式，的接收者 <xref:System.Threading.Tasks.Task> 只能觀察其最終完成，而非中繼非同步狀態。 因此， <xref:Microsoft.AspNetCore.Components.ComponentBase> 只有在 <xref:System.Threading.Tasks.Task> 第一次傳回時以及最後完成時，才可以觸發轉譯資料流程 <xref:System.Threading.Tasks.Task> 。 架構不知道要在其他中繼點 rerender 元件。 如果您想要在中繼點上 rerender，請 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在這些點呼叫。

請考慮下列 `CounterState1` 元件，這會在每次按一下時更新計數四次：

* 自動轉譯會在的第一個和最後一個增量之後發生 `currentCount` 。
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>當架構未在遞增的中繼處理點上自動觸發轉譯中時，呼叫會觸發手動呈現 `currentCount` 。

`Pages/CounterState1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/rendering/CounterState1.razor?highlight=17,21,25,29)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/rendering/CounterState1.razor?highlight=17,21,25,29)]

::: moniker-end

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a>從轉譯 Blazor 和事件處理系統外部的某個專案接收呼叫

<xref:Microsoft.AspNetCore.Components.ComponentBase> 只知道自己的生命週期方法和 Blazor 觸發的事件。 <xref:Microsoft.AspNetCore.Components.ComponentBase> 不知道程式碼中可能發生的其他事件。 例如，自訂資料存放區所引發的任何 c # 事件都是未知的 Blazor 。 為了讓這類事件觸發轉譯資料流程，以在 UI 中顯示更新的值，請呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 。

請考慮 `CounterState2` 使用下列元件， <xref:System.Timers.Timer?displayProperty=fullName> 以定期更新計數和呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 來更新 UI：

* `OnTimerCallback` 在任何 managed 轉譯 Blazor 流程或事件通知之外執行。 因此， `OnTimerCallback` 必須呼叫， <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 因為 Blazor 不知道 `currentCount` 回呼中的變更。
* 元件會執行 <xref:System.IDisposable> ，其中 <xref:System.Timers.Timer> 會在架構呼叫方法時處置 `Dispose` 。 如需詳細資訊，請參閱<xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

由於回呼是在的同步處理 Blazor 內容之外叫用，因此元件必須包裝中的邏輯， `OnTimerCallback` <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 才能將它移到轉譯器的同步處理內容。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 只能從轉譯器的同步處理內容呼叫，否則會擲回例外狀況。 這相當於將其他 UI 架構中的 UI 執行緒封送處理。

`Pages/CounterState2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/rendering/CounterState2.razor?highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/rendering/CounterState2.razor?highlight=26)]

::: moniker-end

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a>若要在特定事件所保存的子樹狀結構外呈現元件

UI 可能包括：

1. 將事件分派至一個元件。
1. 變更某些狀態。
1. 轉譯資料流程完全不同的元件，而不是接收事件的元件的子系。

處理此案例的其中一種方式，是提供 *狀態管理* 類別，通常是在插入多個元件 (DI) 服務的相依性插入。 當某個元件在狀態管理員上呼叫方法時，狀態管理員會引發 c # 事件，然後由獨立元件接收該事件。

因為這些 c # 事件是在轉譯 Blazor 管線之外，所以請 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在您想要轉譯的其他元件上呼叫，以回應狀態管理員的事件。

這與 <xref:System.Timers.Timer?displayProperty=fullName> [上一節](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)中的稍早案例類似。 由於執行呼叫堆疊通常會保留在轉譯器的同步處理內容中，因此 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> 通常不需要呼叫。 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>只有當邏輯將同步處理內容（例如 <xref:System.Threading.Tasks.Task.ContinueWith%2A> 在上呼叫 <xref:System.Threading.Tasks.Task> 或等候進行呼叫）時，才需要呼叫 <xref:System.Threading.Tasks.Task> [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) 。
