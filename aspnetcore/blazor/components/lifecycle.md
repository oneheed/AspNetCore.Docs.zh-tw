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
# <a name="aspnet-core-razor-component-lifecycle"></a>ASP.NET Core Razor 元件生命週期

Razor元件會處理 Razor 一組同步和非同步生命週期方法中的元件生命週期事件。 您可以覆寫生命週期方法，以在元件初始化和轉譯期間于元件中執行其他作業。

## <a name="lifecycle-events"></a>週期事件

下圖說明 Razor 元件生命週期事件。 與生命週期事件相關聯的 c # 方法會使用本文的下列各節中的範例來定義。

元件生命週期事件：

1. 如果第一次在要求時轉譯元件：
   * 建立元件的實例。
   * 執行屬性插入。 執行 [`SetParametersAsync`](#before-parameters-are-set-setparametersasync) 。
   * 呼叫 [`OnInitialized{Async}`](#component-initialization-methods-oninitializedasync) 。 如果傳回不完整 <xref:System.Threading.Tasks.Task> 的，則 <xref:System.Threading.Tasks.Task> 會等待，然後元件會保存。
1. 呼叫 [`OnParametersSet{Async}`](#after-parameters-are-set-onparameterssetasync) 。 如果傳回不完整 <xref:System.Threading.Tasks.Task> 的，則 <xref:System.Threading.Tasks.Task> 會等待，然後元件會保存。
1. 針對所有同步工作和完整的呈現 <xref:System.Threading.Tasks.Task> 。

![：：： No loc (Razor) ：：： component in：：： no-loc (Blazor) ：：：的元件生命週期事件](lifecycle/_static/lifecycle1.png)

檔物件模型 (DOM) 事件處理：

1. 執行事件處理常式。
1. 如果傳回不完整 <xref:System.Threading.Tasks.Task> 的，則 <xref:System.Threading.Tasks.Task> 會等待，然後元件會保存。
1. 針對所有同步工作和完整的呈現 <xref:System.Threading.Tasks.Task> 。

![檔物件模型 (DOM) 事件處理](lifecycle/_static/lifecycle2.png)

`Render`生命週期：

1. 避免元件上的進一步轉譯作業：
   * 在第一次呈現之後。
   * 當 [`ShouldRender`](#suppress-ui-refreshing-shouldrender) 為時 `false` 。
1. 建立轉譯樹狀結構差異 (差異) 並呈現元件。
1. 等待 DOM 進行更新。
1. 呼叫 [`OnAfterRender{Async}`](#after-component-render-onafterrenderasync) 。

![轉譯生命週期](lifecycle/_static/lifecycle3.png)

開發人員呼叫以 [`StateHasChanged`](#state-changes-statehaschanged) 產生轉譯。 如需詳細資訊，請參閱<xref:blazor/components/rendering>。

## <a name="before-parameters-are-set-setparametersasync"></a>設定參數之前 (`SetParametersAsync`) 

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 在轉譯樹狀結構中或從路由參數，設定元件的父系所提供的參數。

方法的 <xref:Microsoft.AspNetCore.Components.ParameterView> 參數會在每次呼叫時包含元件的一組 [元件參數](xref:blazor/components/index#parameters) 值 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。 藉由覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 方法，開發人員程式碼就可以直接與 <xref:Microsoft.AspNetCore.Components.ParameterView> 的參數互動。

的預設實 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 值會設定每個屬性的值，其具有的 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或[ `[CascadingParameter]` 屬性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)在中具有對應的值 <xref:Microsoft.AspNetCore.Components.ParameterView> 。 在中沒有對應值的參數 <xref:Microsoft.AspNetCore.Components.ParameterView> 會保持不變。

如果 [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) 未叫用，開發人員程式碼就能以任何需要的方式解讀傳入參數的值。 例如，不需要將傳入參數指派給類別的屬性。

如果在開發人員程式碼中提供事件處理常式，請在處置時解除其的掛接。 如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。

在下列範例中， <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> 如果剖析的路由參數為成功，則將 `Param` 參數的值指派給 `value` `Param` 。 如果 `value` 沒有 `null` ，此值就會由元件顯示。

雖然 [路由參數比對不區分大小寫](xref:blazor/fundamentals/routing#route-parameters)，但 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 僅符合路由範本中區分大小寫的參數名稱。 下列範例需要在 `/{Param?}` 路由範本中使用，才能使用來取得值 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> ，而不是 `/{param?}` 。 如果 `/{param?}` 在此案例中使用，則會傳回， <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> `false` 而且 `message` 不會設定為任何一個 `message` 字串。

`Pages/SetParamsAsync.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/SetParamsAsync.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/SetParamsAsync.razor)]

::: moniker-end

## <a name="component-initialization-methods-oninitializedasync"></a>元件初始化方法 (`OnInitialized{Async}`) 

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>當元件在中收到其初始參數之後初始化時，就會叫用和 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 。

針對同步作業，覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> ：

`Pages/OnInit.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/OnInit.razor?highlight=8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/OnInit.razor?highlight=8)]

::: moniker-end

若要執行非同步作業，請覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 並使用 [`await`](/dotnet/csharp/language-reference/operators/await) 運算子：

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor 將 [其內容呼叫呈現](xref:blazor/fundamentals/signalr#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *兩次* 的應用程式：

* 一開始是以靜態方式將元件轉譯為頁面的一部分。
* 第二次當瀏覽器呈現元件時。

若要防止開發人員 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 程式碼在應用程式中進行自呈現時執行兩次 Blazor Server ，請參閱 [自 [呈現後](#stateful-reconnection-after-prerendering) 的可設定狀態] 區段 雖然本節中的內容著重于和具狀態的重新連線 Blazor Server ，但在裝載 SignalR 的應用程式 () 的案例中，會 Blazor WebAssembly <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> 涉及類似的條件和方法，以避免執行開發人員程式碼兩次。 已針對 ASP.NET Core 6.0 版本規劃 *新的狀態保留功能* ，可改善在預進行期間的初始化程式碼執行管理。

當 Blazor 應用程式進行可呈現時，無法執行某些動作，例如呼叫 JavaScript (JS interop) 。 元件在資源清單時可能需要以不同的方式呈現。 如需詳細資訊，請參閱在 [應用程式的 [已呈現時](#detect-when-the-app-is-prerendering) 偵測] 區段。

如果在開發人員程式碼中提供事件處理常式，請在處置時解除其的掛接。 如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。

## <a name="after-parameters-are-set-onparameterssetasync"></a> (設定參數之後 `OnParametersSet{Async}`) 

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 稱為：

* 在或中初始化元件之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 。
* 當父元件轉譯中並提供時：
  * 當至少一個參數已變更時，已知的基本不可變類型。
  * 複雜類型參數。 架構無法得知複雜型別參數的值是否在內部改變，因此當有一或多個複雜類型參數存在時，架構一律會將參數集視為變更。

針對下列範例元件，請流覽至元件的頁面，網址為：

* 使用接收的開始日期 `StartDate` ： `/on-parameters-set/2021-03-19`
* 如果沒有開始日期， `StartDate` 則會將目前當地時間的值指派給其中： `/on-parameters-set`

`Pages/OnParamsSet.razor`:

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 在元件路由中，您無法同時 <xref:System.DateTime> 使用[路由條件約束 `datetime` ](xref:blazor/fundamentals/routing#route-constraints)來限制參數，並[將參數設為選擇性](xref:blazor/fundamentals/routing#route-parameters)。 因此，下列 `OnParamsSet` 元件會使用兩個指示詞 [`@page`](xref:mvc/views/razor#page) 來處理路由，而不在 URL 中提供任何日期區段。

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/OnParamsSet.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/OnParamsSet.razor)]

::: moniker-end

套用參數和屬性值的非同步工作必須在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命週期事件期間發生：

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

如果在開發人員程式碼中提供事件處理常式，請在處置時解除其的掛接。 如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。

如需路由參數和條件約束的詳細資訊，請參閱 <xref:blazor/fundamentals/routing> 。

## <a name="after-component-render-onafterrenderasync"></a>元件轉譯 (之後 `OnAfterRender{Async}`) 

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 元件完成轉譯之後，會呼叫和。 此時會填入元素和元件參考。 您可以使用此階段，以轉譯的內容（例如與轉譯的 DOM 元素互動的 JS interop 呼叫）來執行其他初始化步驟。

`firstRender`And 的參數 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ：

* 會 `true` 在第一次轉譯元件實例時設定為。
* 可以用來確保只執行一次初始化工作。

`Pages/AfterRender.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/AfterRender.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/AfterRender.razor)]

::: moniker-end

轉譯後立即的非同步工作必須在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命週期事件期間發生：

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

即使您 <xref:System.Threading.Tasks.Task> 從傳回 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ，架構也不會在該工作完成之後，為您的元件排程進一步的轉譯迴圈。 這是為了避免無限呈現迴圈。 這與其他生命週期方法不同，後者會在傳回完成時排程進一步的轉譯週期 <xref:System.Threading.Tasks.Task> 。

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 而且不會在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *伺服器上的未處理常式期間呼叫*。 當元件在預先轉譯後以互動方式轉譯時，會呼叫方法。 當應用程式 prerenders 時：

1. 元件會在伺服器上執行，以在 HTTP 回應中產生一些靜態 HTML 標籤。 在這個階段中 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 不會呼叫。
1. 當 Blazor 腳本 (`blazor.webassembly.js` 或 `blazor.server.js`) 在瀏覽器中啟動時，元件會以互動式轉譯模式重新開機。 在重新開機元件之後， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **會** 呼叫，因為應用程式不會再使用已進行的重新呈現階段。

如果在開發人員程式碼中提供事件處理常式，請在處置時解除其的掛接。 如需詳細資訊，請參閱「[元件 `IDisposable` 處置方式](#component-disposal-with-idisposable)」一節。

## <a name="suppress-ui-refreshing-shouldrender"></a>隱藏 UI 重新整理 (`ShouldRender`) 

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 每次轉譯元件時，就會呼叫。 覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以管理 UI 重新整理。 如果執行傳回 `true` ，就會重新整理 UI。

即使覆 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 寫，仍一律會呈現元件。

`Pages/ControlRender.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/ControlRender.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/ControlRender.razor)]

::: moniker-end

如需有關的效能最佳作法的詳細資訊 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> ，請參閱 <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees> 。

## <a name="state-changes-statehaschanged"></a>狀態變更 (`StateHasChanged`) 

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知元件其狀態已變更。 當適用時，呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會導致元件保存。

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動針對 <xref:Microsoft.AspNetCore.Components.EventCallback> 方法呼叫。 如需事件回呼的詳細資訊，請參閱 <xref:blazor/components/event-handling#eventcallback> 。

如需元件轉譯和何時要呼叫的詳細資訊 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> ，請參閱 <xref:blazor/components/rendering> 。

## <a name="handle-incomplete-async-actions-at-render"></a>在轉譯時處理未完成的非同步動作

在呈現元件之前，在生命週期事件中執行的非同步動作可能尚未完成。 `null`當生命週期方法執行時，物件可能會或未完全填入資料。 提供轉譯邏輯來確認物件已初始化。 轉譯預留位置 UI 元素 (例如，在物件為時) 載入訊息 `null` 。

在 `FetchData` 範本的元件中 Blazor ， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 會覆寫以非同步方式接收預測資料 (`forecasts`) 。 當 `forecasts` 為時 `null` ，就會向使用者顯示載入訊息。 在 `Task` 傳回之後 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ，元件會以更新狀態保存。

`Pages/FetchData.razor` 在 Blazor Server 範本中：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a>處理錯誤

如需在生命週期方法執行期間處理錯誤的詳細資訊，請參閱 <xref:blazor/fundamentals/handle-errors> 。

## <a name="stateful-reconnection-after-prerendering"></a>以具狀態重新連接後重新連線

在 Blazor Server 應用程式中 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，當為時 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> ，元件最初會以靜態方式轉譯為頁面的一部分。 當瀏覽器將 SignalR 連接重新建立回伺服器之後，就會 *再次* 轉譯該元件並進行互動。 如果 [`OnInitialized{Async}`](#component-initialization-methods-oninitializedasync) 有初始化元件的生命週期方法，方法會執行 *兩次*：

* 以靜態方式資源清單元件時。
* 建立伺服器連接之後。

這可能會導致在元件最後轉譯時，UI 中所顯示的資料有明顯的變更。 若要避免在應用程式中使用這種雙重轉譯行為 Blazor Server ，請傳入識別碼以在預先轉譯期間快取狀態，並在預先轉譯之後取得狀態。

下列程式碼示範 `WeatherForecastService` 以範本為基礎的應用程式中已更新 Blazor Server ，可避免雙重轉譯。 在下列範例中，等候的 <xref:System.Threading.Tasks.Task.Delay%2A> (`await Task.Delay(...)`) 會模擬短暫延遲，然後再從方法傳回資料 `GetForecastAsync` 。

`WeatherForecastService.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/lifecycle/WeatherForecastService.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/lifecycle/WeatherForecastService.cs)]

::: moniker-end

如需的詳細資訊 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> ，請參閱 <xref:blazor/fundamentals/signalr#render-mode> 。

雖然本節中的內容著重在和具狀態的重新連線 Blazor Server ，但在裝載 SignalR 的 Blazor WebAssembly 應用程式中 () 的案例 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.WebAssemblyPrerendered> 也牽涉到類似的條件和方法，以避免執行開發人員程式碼兩次。 已針對 ASP.NET Core 6.0 版本規劃 *新的狀態保留功能* ，可改善在預進行期間的初始化程式碼執行管理。

## <a name="detect-when-the-app-is-prerendering"></a>偵測應用程式何時進行呈現

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a>元件處置方式 `IDisposable`

如果元件已執行 <xref:System.IDisposable> ，則在從 UI 中移除元件時，架構會呼叫 [處置方法](/dotnet/standard/garbage-collection/implementing-dispose) ，而無法釋放非受控資源。 處置可以在任何時間進行，包括 [元件初始化](#component-initialization-methods-oninitializedasync)期間。 下列元件會使用指示詞來 <xref:System.IDisposable> [`@implements`](xref:mvc/views/razor#implements) Razor 執行：

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

如果物件需要處置，則在呼叫時，可以使用 lambda 來處置物件 <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> 。 下列範例會出現在 <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> 文章中，並示範如何使用 lambda 運算式來處置 <xref:System.Timers.Timer> 。

`Pages/CounterWithTimerDisposal.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/CounterWithTimerDisposal.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/CounterWithTimerDisposal.razor)]

::: moniker-end

針對非同步處置工作，請使用 `DisposeAsync` 取代 <xref:System.IDisposable.Dispose> ：

```csharp
public async ValueTask DisposeAsync()
{
    await ...
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>不支援在中呼叫 `Dispose` 。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能會在卸載轉譯器的過程中叫用，因此不支援在該時間點要求 UI 更新。

取消訂閱來自 .NET 事件的事件處理常式。 下列[ Blazor 表單](xref:blazor/forms-validation)範例示範如何在方法中取消訂閱事件處理常式 `Dispose` ：

::: moniker range=">= aspnetcore-5.0"

* 私用欄位和 lambda 方法

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

* 私用方法方法

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* 私用欄位和 lambda 方法

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

* 私用方法方法

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

::: moniker-end

使用 [匿名](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)函式、方法或運算式時，不需要執行 <xref:System.IDisposable> 和取消訂閱委派。 不過， **當公開事件的物件存留時間超過註冊委派之元件的存留期時**，無法取消訂閱委派會是一個問題。 發生這種情況時，會造成記憶體流失的結果，因為已註冊的委派會讓原始物件保持運作。 因此，當您知道事件委派會快速處置時，請使用下列方法。 如果不確定需要處置的物件存留期，請訂閱委派方法並適當地處置委派，如先前的範例所示。

* 匿名 lambda 方法方法 (不需要明確處置) ：

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

* 匿名 lambda 運算式方法 (不需要明確處置) ：

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

  本文中會顯示上述程式碼中具有匿名 lambda 運算式的完整範例 <xref:blazor/forms-validation#validator-components> 。

如需詳細資訊，請參閱在執行和方法時 [清除非受控資源](/dotnet/standard/garbage-collection/unmanaged) 和後續的主題 `Dispose` `DisposeAsync` 。

## <a name="cancelable-background-work"></a>可取消的背景工作

元件通常會執行長時間執行的背景工作，例如 () 進行網路呼叫， <xref:System.Net.Http.HttpClient> 以及與資料庫互動。 在幾種情況下，最好停止背景工作來節省系統資源。 例如，當使用者離開元件時，不會自動停止背景非同步作業。

背景工作專案可能需要取消的其他原因包括：

* 執行中的背景工作已啟動，但有錯誤的輸入資料或處理參數。
* 目前執行中的背景工作專案集必須取代為一組新的工作專案。
* 目前執行中工作的優先順序必須變更。
* 您必須關閉應用程式，才能重新部署伺服器。
* 伺服器資源會受到限制，強制背景工作專案的重新排程。

若要在元件中執行可取消的背景工作模式：

* 使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken> 。
* 手動取消權杖時，若要處理 [元件](#component-disposal-with-idisposable) 和任何點取消，請呼叫 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示應取消背景工作。
* 在非同步呼叫傳回之後，請呼叫 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> 權杖上的。

在下例中︰

* `await Task.Delay(5000, cts.Token);` 表示長時間執行的非同步背景工作。
* `BackgroundResourceMethod` 表示長時間執行的背景方法，如果在 `Resource` 呼叫方法之前處置，則不應該啟動。

`Pages/BackgroundWork.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/lifecycle/BackgroundWork.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/lifecycle/BackgroundWork.razor)]

::: moniker-end

## <a name="blazor-server-reconnection-events"></a>Blazor Server 重新連接事件

本文所涵蓋的元件生命週期事件，與重新[ Blazor Server 連接事件處理常式](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui)分開運作。 當 Blazor Server 應用程式失去其 SignalR 與用戶端的連線時，只會中斷 UI 更新。 重新建立連接時，會繼續 UI 更新。 如需線路處理程式事件和設定的詳細資訊，請參閱 <xref:blazor/fundamentals/signalr> 。
