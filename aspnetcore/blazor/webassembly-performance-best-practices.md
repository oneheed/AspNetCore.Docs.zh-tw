---
title: 'ASP.NET Core :::no-loc(Blazor WebAssembly)::: 效能最佳做法'
author: pranavkm
description: '提升 ASP.NET Core :::no-loc(Blazor WebAssembly)::: 應用程式效能，並避免常見效能問題的秘訣。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 0e827680e7024eabed09b989466476a3a80eb225
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690272"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="82501-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: 效能最佳做法</span><span class="sxs-lookup"><span data-stu-id="82501-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: performance best practices</span></span>

<span data-ttu-id="82501-104">[Pranav Krishnamoorthy](https://github.com/pranavkm)和[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="82501-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="82501-105">:::no-loc(Blazor WebAssembly)::: 經過仔細設計和優化，可在最實際的應用程式 UI 案例中啟用高效能。</span><span class="sxs-lookup"><span data-stu-id="82501-105">:::no-loc(Blazor WebAssembly)::: is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="82501-106">不過，產生最佳結果取決於使用正確模式和功能的開發人員。</span><span class="sxs-lookup"><span data-stu-id="82501-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="82501-107">請考慮下列層面：</span><span class="sxs-lookup"><span data-stu-id="82501-107">Consider the following aspects:</span></span>

* <span data-ttu-id="82501-108">**執行時間輸送量** ： .net 程式碼會在 WebAssembly 執行時間內的解譯器上執行，因此 CPU 輸送量受限。</span><span class="sxs-lookup"><span data-stu-id="82501-108">**Runtime throughput** : The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="82501-109">在要求的案例中，應用程式受益于 [優化轉譯速度](#optimize-rendering-speed)。</span><span class="sxs-lookup"><span data-stu-id="82501-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="82501-110">**啟動時間** ：應用程式將 .net 執行時間傳送到瀏覽器，因此請務必使用 [最小化應用程式下載大小](#minimize-app-download-size)的功能。</span><span class="sxs-lookup"><span data-stu-id="82501-110">**Startup time** : The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="82501-111">優化轉譯速度</span><span class="sxs-lookup"><span data-stu-id="82501-111">Optimize rendering speed</span></span>

<span data-ttu-id="82501-112">下列各節提供建議來將轉譯工作負載降到最低，並改善 UI 回應性。</span><span class="sxs-lookup"><span data-stu-id="82501-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="82501-113">遵循這項建議，可輕鬆地在 UI 轉譯速度中進行 *十折迭或更高的改進* 。</span><span class="sxs-lookup"><span data-stu-id="82501-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="82501-114">避免不必要的元件子樹呈現</span><span class="sxs-lookup"><span data-stu-id="82501-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="82501-115">在執行時間，元件會以階層形式存在。</span><span class="sxs-lookup"><span data-stu-id="82501-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="82501-116">根元件具有子元件。</span><span class="sxs-lookup"><span data-stu-id="82501-116">A root component has child components.</span></span> <span data-ttu-id="82501-117">接著，根的子系有自己的子元件，依此類推。</span><span class="sxs-lookup"><span data-stu-id="82501-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="82501-118">當事件發生時（例如使用者選取按鈕），這會 :::no-loc(Blazor)::: 決定要 rerender 的元件：</span><span class="sxs-lookup"><span data-stu-id="82501-118">When an event occurs, such as a user selecting a button, this is how :::no-loc(Blazor)::: decides which components to rerender:</span></span>

 1. <span data-ttu-id="82501-119">事件本身會分派到轉譯事件處理常式的任何元件。</span><span class="sxs-lookup"><span data-stu-id="82501-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="82501-120">執行事件處理常式之後，該元件就會保存。</span><span class="sxs-lookup"><span data-stu-id="82501-120">After executing the event handler, that component is rerendered.</span></span>
 1. <span data-ttu-id="82501-121">每當保存任何元件時，它就會將參數值的新複本提供給其子元件。</span><span class="sxs-lookup"><span data-stu-id="82501-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
 1. <span data-ttu-id="82501-122">當接收一組新的參數值時，每個元件都會選擇是否要 rerender。</span><span class="sxs-lookup"><span data-stu-id="82501-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="82501-123">根據預設，元件 rerender 如果參數值可能已變更 (例如，如果它們是可變動的物件) 。</span><span class="sxs-lookup"><span data-stu-id="82501-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="82501-124">此序列的最後兩個步驟會以遞迴方式從元件階層中繼續進行。</span><span class="sxs-lookup"><span data-stu-id="82501-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="82501-125">在許多情況下，會保存整個子樹。</span><span class="sxs-lookup"><span data-stu-id="82501-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="82501-126">這表示以高階元件為目標的事件可能會造成昂貴的轉譯資料流程程式，因為該點下的所有內容都必須保存。</span><span class="sxs-lookup"><span data-stu-id="82501-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="82501-127">如果您想要中斷此程式，並防止將遞迴轉譯成特定的子樹狀結構，您可以執行下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="82501-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

 * <span data-ttu-id="82501-128">確定特定元件的所有參數都是基本的不可變類型 (例如、、 `string` 、 `int` `bool` `DateTime` 和其他) 。</span><span class="sxs-lookup"><span data-stu-id="82501-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="82501-129">如果未變更這些參數值，則偵測變更的內建邏輯會自動略過轉譯資料流程。</span><span class="sxs-lookup"><span data-stu-id="82501-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="82501-130">如果您以轉譯子元件 `<Customer CustomerId="@item.CustomerId" />` ，其中 `CustomerId` 是一個 `int` 值，則除非變更，否則不會保存 `item.CustomerId` 。</span><span class="sxs-lookup"><span data-stu-id="82501-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
 * <span data-ttu-id="82501-131">如果您需要接受 nonprimitive 參數值（例如自訂模型類型、事件回呼或 <xref:Microsoft.AspNetCore.Components.RenderFragment> 值），則可以覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 來控制是否要轉譯的決策，這在[ `ShouldRender` 使用](#use-of-shouldrender)一節中有說明。</span><span class="sxs-lookup"><span data-stu-id="82501-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="82501-132">藉由略過整個子樹的轉譯資料流程，您可以在事件發生時移除大部分的轉譯成本。</span><span class="sxs-lookup"><span data-stu-id="82501-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="82501-133">您可能想要特別考慮子元件，讓您可以略過該部分 UI 的轉譯資料流程。</span><span class="sxs-lookup"><span data-stu-id="82501-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="82501-134">這是減少父元件之轉譯成本的有效方式。</span><span class="sxs-lookup"><span data-stu-id="82501-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="82501-135">使用 `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="82501-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="82501-136">如果撰寫的僅限 UI 元件永遠不會在初始轉譯之後變更 (不論是否有任何參數值) ，請將設定 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 為 return `false` ：</span><span class="sxs-lookup"><span data-stu-id="82501-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="82501-137">如果元件的參數值以特定方式改變時，只需要轉譯資料流程，您就可以使用私用欄位來追蹤所需的資訊，以偵測變更。</span><span class="sxs-lookup"><span data-stu-id="82501-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="82501-138">在下列範例中， `shouldRender` 是以檢查任何種類的變更或變動來檢查是否會提示 rerender。</span><span class="sxs-lookup"><span data-stu-id="82501-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="82501-139">`prevOutboundFlightId` 和 `prevInboundFlightId` 追蹤下一次可能更新的資訊：</span><span class="sxs-lookup"><span data-stu-id="82501-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="82501-140">在上述程式碼中，事件處理常式也可以設定 `shouldRender` 為， `true` 以便在事件之後保存元件。</span><span class="sxs-lookup"><span data-stu-id="82501-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="82501-141">大部分的元件都不需要這種手動控制層級。</span><span class="sxs-lookup"><span data-stu-id="82501-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="82501-142">如果這些子樹在轉譯時特別耗費資源，而且會造成 UI 延隔，您應該只在意略過轉譯子樹。</span><span class="sxs-lookup"><span data-stu-id="82501-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="82501-143">如需詳細資訊，請參閱<xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="82501-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="82501-144">虛擬化</span><span class="sxs-lookup"><span data-stu-id="82501-144">Virtualization</span></span>

<span data-ttu-id="82501-145">在迴圈內轉譯大量的 UI （例如具有上千個專案的清單或方格）時，大量的轉譯作業可能會導致 UI 轉譯延遲，因而導致使用者體驗不佳。</span><span class="sxs-lookup"><span data-stu-id="82501-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="82501-146">假設使用者一次只能看到少量的元素，而不需要滾動，那麼花太多時間轉譯專案目前看起來很浪費。</span><span class="sxs-lookup"><span data-stu-id="82501-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="82501-147">為了解決這個問題， :::no-loc(Blazor)::: 提供了內建[ `<Virtualize>` 元件](xref:blazor/components/virtualization)，可建立任意大型清單的外觀和滾動行為，但實際上只會轉譯目前滾動塊區內的清單專案。</span><span class="sxs-lookup"><span data-stu-id="82501-147">To address this, :::no-loc(Blazor)::: provides a built-in [`<Virtualize>` component](xref:blazor/components/virtualization) that creates the appearance and scroll behaviors of an arbitrarily-large list but actually only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="82501-148">例如，這表示應用程式可以有一份包含100000個專案的清單，但只需支付20個專案的轉譯成本。</span><span class="sxs-lookup"><span data-stu-id="82501-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="82501-149">使用 `<Virtualize>` 元件可以依大小的順序擴大 UI 效能。</span><span class="sxs-lookup"><span data-stu-id="82501-149">Use of the `<Virtualize>` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="82501-150">`<Virtualize>` 可以在下列情況使用：</span><span class="sxs-lookup"><span data-stu-id="82501-150">`<Virtualize>` can be used when:</span></span>

 * <span data-ttu-id="82501-151">在迴圈中呈現一組資料項目。</span><span class="sxs-lookup"><span data-stu-id="82501-151">Rendering a set of data items in a loop.</span></span>
 * <span data-ttu-id="82501-152">大部分的專案都因為滾動而無法顯示。</span><span class="sxs-lookup"><span data-stu-id="82501-152">Most of the items aren't visible due to scrolling.</span></span>
 * <span data-ttu-id="82501-153">轉譯的專案大小完全相同。</span><span class="sxs-lookup"><span data-stu-id="82501-153">The rendered items are exactly the same size.</span></span> <span data-ttu-id="82501-154">當使用者滾動至任意點時，元件可以計算要顯示的可見專案。</span><span class="sxs-lookup"><span data-stu-id="82501-154">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="82501-155">以下顯示非虛擬化清單的範例：</span><span class="sxs-lookup"><span data-stu-id="82501-155">The following shows an example of a non-virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="82501-156">如果 `allFlights` 集合包含10000個專案，它會具現化並轉譯 10000 `<FlightSummary>` 元件實例。</span><span class="sxs-lookup"><span data-stu-id="82501-156">If the `allFlights` collection holds 10,000 items, it instantiates and renders 10,000 `<FlightSummary>` component instances.</span></span> <span data-ttu-id="82501-157">相較之下，下列顯示虛擬化清單的範例：</span><span class="sxs-lookup"><span data-stu-id="82501-157">In comparison, the following shows an example of a virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

<span data-ttu-id="82501-158">雖然產生的 UI 看起來與使用者相同，但是幕後的元件只會具現化，並轉譯為 `<FlightSummary>` 填滿可捲動區域所需的實例數量。</span><span class="sxs-lookup"><span data-stu-id="82501-158">Even though the resulting UI looks the same to a user, behind the scenes the component only instantiates and renders as many `<FlightSummary>` instances as are required to fill the scrollable region.</span></span> <span data-ttu-id="82501-159">`<FlightSummary>`當使用者滾動時，會重新計算和轉譯所顯示的實例集合。</span><span class="sxs-lookup"><span data-stu-id="82501-159">The set of `<FlightSummary>` instances displayed is recalculated and rendered as the user scrolls.</span></span>

<span data-ttu-id="82501-160">`<Virtualize>` 也有其他優點。</span><span class="sxs-lookup"><span data-stu-id="82501-160">`<Virtualize>` has other benefits, too.</span></span> <span data-ttu-id="82501-161">例如，當元件從外部 API 要求資料時， `<Virtualize>` 會允許元件只提取對應到目前可見區域之記錄的配量，而不是從集合中下載所有資料。</span><span class="sxs-lookup"><span data-stu-id="82501-161">For example when a component requests data from an external API, `<Virtualize>` permits the component to only fetch the slice of records that correspond to the current visible region, instead of downloading all the data from the collection.</span></span>

<span data-ttu-id="82501-162">如需詳細資訊，請參閱<xref:blazor/components/virtualization>。</span><span class="sxs-lookup"><span data-stu-id="82501-162">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="82501-163">建立輕量、優化的元件</span><span class="sxs-lookup"><span data-stu-id="82501-163">Create lightweight, optimized components</span></span>

<span data-ttu-id="82501-164">大部分 :::no-loc(Blazor)::: 的元件都不需要積極的優化工作。</span><span class="sxs-lookup"><span data-stu-id="82501-164">Most :::no-loc(Blazor)::: components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="82501-165">這是因為大部分的元件通常不會在 UI 中重複，且不會以高頻率 rerender。</span><span class="sxs-lookup"><span data-stu-id="82501-165">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="82501-166">例如， `@page` 代表高階 UI 元件（例如對話或表單）的元件和元件，最有可能只會出現一次，而且只會 rerender 以回應使用者手勢。</span><span class="sxs-lookup"><span data-stu-id="82501-166">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="82501-167">這些元件不會建立高轉譯工作負載，因此您可以自由地使用任何您想要的架構功能組合，而不需要擔心轉譯效能。</span><span class="sxs-lookup"><span data-stu-id="82501-167">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="82501-168">不過，在某些情況下，您也會建立需要大規模重複的元件。</span><span class="sxs-lookup"><span data-stu-id="82501-168">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="82501-169">例如：</span><span class="sxs-lookup"><span data-stu-id="82501-169">For example:</span></span>

 * <span data-ttu-id="82501-170">大型的嵌套表單可能會有數百個個別的輸入、標籤和其他元素。</span><span class="sxs-lookup"><span data-stu-id="82501-170">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
 * <span data-ttu-id="82501-171">方格可能有數千個數據格。</span><span class="sxs-lookup"><span data-stu-id="82501-171">Grids may have thousands of cells.</span></span>
 * <span data-ttu-id="82501-172">散佈圖可能有數百萬個資料點。</span><span class="sxs-lookup"><span data-stu-id="82501-172">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="82501-173">如果以個別的元件實例為每個單位進行模型化，則它們的轉譯效能會很重要。</span><span class="sxs-lookup"><span data-stu-id="82501-173">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="82501-174">本節提供讓這類元件變得輕量的建議，讓 UI 保持快速且迅速回應。</span><span class="sxs-lookup"><span data-stu-id="82501-174">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="82501-175">避免數以千計的元件實例</span><span class="sxs-lookup"><span data-stu-id="82501-175">Avoid thousands of component instances</span></span>

<span data-ttu-id="82501-176">每個元件都是個別的島，可獨立于其父代和子系之外轉譯。</span><span class="sxs-lookup"><span data-stu-id="82501-176">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="82501-177">藉由選擇如何將 UI 分割為元件的階層，您可以控制 UI 轉譯的資料細微性。</span><span class="sxs-lookup"><span data-stu-id="82501-177">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="82501-178">這可能是良好或不良的效能。</span><span class="sxs-lookup"><span data-stu-id="82501-178">This can be either good or bad for performance.</span></span>

 * <span data-ttu-id="82501-179">藉由將 UI 分割成更多元件，當事件發生時，您可以在 UI rerender 中有較小的部分。</span><span class="sxs-lookup"><span data-stu-id="82501-179">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="82501-180">例如，當使用者按一下資料表資料列中的按鈕時，您可能只會有該單一資料列 rerender，而不是整個頁面或資料表。</span><span class="sxs-lookup"><span data-stu-id="82501-180">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
 * <span data-ttu-id="82501-181">不過，每個額外的元件都牽涉到額外的記憶體和 CPU 額外負荷，以處理其獨立的狀態和轉譯生命週期。</span><span class="sxs-lookup"><span data-stu-id="82501-181">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="82501-182">在調整 .NET 5 的效能時 :::no-loc(Blazor WebAssembly)::: ，我們測量出每個元件實例大約0.06 毫秒的轉譯負擔。</span><span class="sxs-lookup"><span data-stu-id="82501-182">When tuning the performance of :::no-loc(Blazor WebAssembly)::: on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="82501-183">這是以簡單的元件為基礎，接受一般膝上型電腦上執行的三個參數。</span><span class="sxs-lookup"><span data-stu-id="82501-183">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="82501-184">就內部而言，此額外負荷主要是因為從字典中取出每個元件的狀態，以及傳遞和接收參數。</span><span class="sxs-lookup"><span data-stu-id="82501-184">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="82501-185">藉由乘法，您可以看到加入2000額外元件實例會在轉譯時間增加0.12 秒，而 UI 會開始感覺到使用者的速度變慢。</span><span class="sxs-lookup"><span data-stu-id="82501-185">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="82501-186">您可以讓元件更輕量，讓您可以有更多的元件，但通常更強大的技巧就是不會有這麼多的元件。</span><span class="sxs-lookup"><span data-stu-id="82501-186">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="82501-187">下列各節描述兩種方法。</span><span class="sxs-lookup"><span data-stu-id="82501-187">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="82501-188">內嵌子元件至其父代</span><span class="sxs-lookup"><span data-stu-id="82501-188">Inline child components into their parents</span></span>

<span data-ttu-id="82501-189">請考慮下列可呈現子元件序列的元件：</span><span class="sxs-lookup"><span data-stu-id="82501-189">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="82501-190">在上述的範例程式碼 `<ChatMessageDisplay>` 中，元件定義于 `ChatMessageDisplay.razor` 包含下列內容的檔案中：</span><span class="sxs-lookup"><span data-stu-id="82501-190">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="82501-191">上述範例會正常運作，而且只要上千個訊息不會顯示一次，就能順利執行。</span><span class="sxs-lookup"><span data-stu-id="82501-191">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="82501-192">若要一次顯示數以千計的訊息，請考慮 *不要* 分解個別的 `ChatMessageDisplay` 元件。</span><span class="sxs-lookup"><span data-stu-id="82501-192">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="82501-193">相反地，直接將轉譯內嵌至父系：</span><span class="sxs-lookup"><span data-stu-id="82501-193">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="82501-194">這可避免轉譯的每個元件的額外負荷，因為無法個別 rerender 每個元件的成本。</span><span class="sxs-lookup"><span data-stu-id="82501-194">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="82501-195">在程式碼中定義可重複使用 `RenderFragments`</span><span class="sxs-lookup"><span data-stu-id="82501-195">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="82501-196">您可以單純地將子元件分解為重複使用轉譯邏輯的方法。</span><span class="sxs-lookup"><span data-stu-id="82501-196">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="82501-197">如果是這種情況，您仍然可以重複使用轉譯邏輯，而不需要宣告實際元件。</span><span class="sxs-lookup"><span data-stu-id="82501-197">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="82501-198">在任何元件的 `@code` 區塊中，您可以定義 <xref:Microsoft.AspNetCore.Components.RenderFragment> 發出 UI 的，並可從任何位置呼叫：</span><span class="sxs-lookup"><span data-stu-id="82501-198">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is :::no-loc(Blazor)::: working for you?" />
        </div>
    };
}
```

<span data-ttu-id="82501-199">如同上述範例中的 demonstated，元件可以從區塊內的程式碼 `@code` 和外部發出標記。</span><span class="sxs-lookup"><span data-stu-id="82501-199">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="82501-200">這種方法會定義一個委派，可 <xref:Microsoft.AspNetCore.Components.RenderFragment> 讓您在元件的正常轉譯輸出內轉譯（選擇性地在多個位置）。</span><span class="sxs-lookup"><span data-stu-id="82501-200">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="82501-201">委派必須接受型別為的參數， `__builder` <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 以便 :::no-loc(Razor)::: 編譯器可以產生其轉譯指令。</span><span class="sxs-lookup"><span data-stu-id="82501-201">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the :::no-loc(Razor)::: compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="82501-202">如果您想要在多個元件之間重複使用，請考慮將它宣告為 `public static` 成員：</span><span class="sxs-lookup"><span data-stu-id="82501-202">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="82501-203">這現在可以從不相關的元件叫用。</span><span class="sxs-lookup"><span data-stu-id="82501-203">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="82501-204">這項技術適用于建立可重複使用之標記程式碼片段的程式庫，而不需要每個元件的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="82501-204">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="82501-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> 委派也可以接受參數。</span><span class="sxs-lookup"><span data-stu-id="82501-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="82501-206">若要建立 `ChatMessageDisplay` 先前範例中元件的對等專案：</span><span class="sxs-lookup"><span data-stu-id="82501-206">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="82501-207">這種方法可讓您在不需要每個元件的額外負荷下重複使用轉譯邏輯。</span><span class="sxs-lookup"><span data-stu-id="82501-207">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="82501-208">不過，它無法獨立重新整理其 UI 的子樹，也無法在其父代呈現時略過呈現 UI 的子樹，因為沒有元件界限。</span><span class="sxs-lookup"><span data-stu-id="82501-208">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="82501-209">不要收到太多參數</span><span class="sxs-lookup"><span data-stu-id="82501-209">Don't receive too many parameters</span></span>

<span data-ttu-id="82501-210">如果元件經常重複（例如數百次或數千次），請記住，傳遞和接收每個參數的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="82501-210">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="82501-211">太多參數很少會嚴重限制效能，但它可能是一個因素。</span><span class="sxs-lookup"><span data-stu-id="82501-211">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="82501-212">針對在 `<TableCell>` 方格內轉譯1000時間的元件，傳遞給它的每個額外參數可能會在總轉譯成本周圍加上15毫秒。</span><span class="sxs-lookup"><span data-stu-id="82501-212">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="82501-213">如果每個資料格都接受10個參數，則每個元件轉譯的參數傳遞大約是150毫秒，因此可能是150000毫秒 (150 秒) ，而且本身會造成遲鈍的 UI。</span><span class="sxs-lookup"><span data-stu-id="82501-213">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="82501-214">若要減少這項負載，您可以透過自訂類別組合多個參數。</span><span class="sxs-lookup"><span data-stu-id="82501-214">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="82501-215">例如， `<TableCell>` 元件可能會接受：</span><span class="sxs-lookup"><span data-stu-id="82501-215">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="82501-216">在上述範例中， `Data` 每個資料格都是不同的，但在所有資料格 `Options` 之間是共通的。</span><span class="sxs-lookup"><span data-stu-id="82501-216">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="82501-217">當然，這可能是因為不具有 `<TableCell>` 元件，而是將其邏輯內嵌至父元件的改進。</span><span class="sxs-lookup"><span data-stu-id="82501-217">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="82501-218">確定已修正串聯參數</span><span class="sxs-lookup"><span data-stu-id="82501-218">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="82501-219">`<CascadingValue>`元件有一個稱為的選擇性參數 `IsFixed` 。</span><span class="sxs-lookup"><span data-stu-id="82501-219">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

 * <span data-ttu-id="82501-220">如果 `IsFixed` 值 `false` (預設的) ，則串聯值的每個收件者都會設定要接收變更通知的訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="82501-220">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="82501-221">在此情況下，因為訂用帳戶追蹤的緣故，每個都 `[CascadingParameter]` 有 **相當高的成本** `[Parameter]` 。</span><span class="sxs-lookup"><span data-stu-id="82501-221">In this case, each each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
 * <span data-ttu-id="82501-222">如果 `IsFixed` 值是 `true` (例如 `<CascadingValue Value="@someValue" IsFixed="true">`) ，則收件者會收到初始值，但 *不* 會設定任何訂用帳戶來接收更新。</span><span class="sxs-lookup"><span data-stu-id="82501-222">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="82501-223">在此情況下，每個 `[CascadingParameter]` 都是輕量的，而且不會比一般標準 **更昂貴** `[Parameter]` 。</span><span class="sxs-lookup"><span data-stu-id="82501-223">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="82501-224">因此，您應該盡可能 `IsFixed="true"` 在串聯的值上使用。</span><span class="sxs-lookup"><span data-stu-id="82501-224">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="82501-225">只要所提供的值不會隨著時間而變更，您就可以這麼做。</span><span class="sxs-lookup"><span data-stu-id="82501-225">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="82501-226">在元件以串聯值形式傳遞的一般模式 `this` 下，您應該使用 `IsFixed="true"` ：</span><span class="sxs-lookup"><span data-stu-id="82501-226">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="82501-227">如果有大量的其他元件會收到串聯的值，這就會有很大的差異。</span><span class="sxs-lookup"><span data-stu-id="82501-227">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="82501-228">如需詳細資訊，請參閱<xref:blazor/components/cascading-values-and-parameters>。</span><span class="sxs-lookup"><span data-stu-id="82501-228">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="82501-229">避免使用屬性展開 `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="82501-229">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="82501-230">元件可以選擇使用旗標接收「不相符的」參數值 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> ：</span><span class="sxs-lookup"><span data-stu-id="82501-230">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="82501-231">這種方法可讓您將任意額外的屬性傳遞至元素。</span><span class="sxs-lookup"><span data-stu-id="82501-231">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="82501-232">不過，它也相當昂貴，因為轉譯器必須：</span><span class="sxs-lookup"><span data-stu-id="82501-232">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="82501-233">將所有提供的參數與已知參數的集合進行比對，以建立字典。</span><span class="sxs-lookup"><span data-stu-id="82501-233">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="82501-234">追蹤相同屬性的多個複本如何覆寫彼此。</span><span class="sxs-lookup"><span data-stu-id="82501-234">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="82501-235">您可以隨意使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 非效能關鍵的元件，例如不會經常重複的元件。</span><span class="sxs-lookup"><span data-stu-id="82501-235">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="82501-236">但是針對大規模轉譯的元件（例如大型清單中的每個專案或方格中的資料格），請嘗試避免屬性展開。</span><span class="sxs-lookup"><span data-stu-id="82501-236">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="82501-237">如需詳細資訊，請參閱<xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>。</span><span class="sxs-lookup"><span data-stu-id="82501-237">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="82501-238">`SetParametersAsync`手動執行</span><span class="sxs-lookup"><span data-stu-id="82501-238">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="82501-239">每一元件轉譯負荷的主要部分是將傳入的參數值寫入 `[Parameter]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="82501-239">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="82501-240">轉譯器必須使用反映來執行此作業。</span><span class="sxs-lookup"><span data-stu-id="82501-240">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="82501-241">雖然這是經過優化的，但在 WebAssembly 執行時間上缺少 JIT 支援會造成限制。</span><span class="sxs-lookup"><span data-stu-id="82501-241">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="82501-242">在某些極端的情況下，您可能會想要避免反映並手動執行您自己的參數設定邏輯。</span><span class="sxs-lookup"><span data-stu-id="82501-242">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="82501-243">這可能適用于：</span><span class="sxs-lookup"><span data-stu-id="82501-243">This may be applicable when:</span></span>

 * <span data-ttu-id="82501-244">您的元件通常會轉譯 (例如，UI) 中有數百個或數千個複本。</span><span class="sxs-lookup"><span data-stu-id="82501-244">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
 * <span data-ttu-id="82501-245">它接受許多參數。</span><span class="sxs-lookup"><span data-stu-id="82501-245">It accepts many parameters.</span></span>
 * <span data-ttu-id="82501-246">您發現接收參數的額外負荷會對 UI 回應性產生明顯的影響。</span><span class="sxs-lookup"><span data-stu-id="82501-246">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="82501-247">在這些情況下，您可以覆寫元件的虛擬 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 方法，並執行您自己的元件特定邏輯。</span><span class="sxs-lookup"><span data-stu-id="82501-247">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="82501-248">下列範例故意避免任何字典查閱：</span><span class="sxs-lookup"><span data-stu-id="82501-248">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="82501-249">在上述程式碼中，傳回基類 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 會執行一般生命週期方法，而不會再次指派參數。</span><span class="sxs-lookup"><span data-stu-id="82501-249">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="82501-250">如您在上述程式碼中所見，覆寫 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 和提供自訂邏輯既複雜又繁瑣，因此我們通常不建議採用這種方法。</span><span class="sxs-lookup"><span data-stu-id="82501-250">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="82501-251">在極端情況下，它可以改善20-25% 的轉譯效能，但您應該只在先前所列的案例中考慮這種方法。</span><span class="sxs-lookup"><span data-stu-id="82501-251">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="82501-252">不要太快觸發事件</span><span class="sxs-lookup"><span data-stu-id="82501-252">Don't trigger events too rapidly</span></span>

<span data-ttu-id="82501-253">有些瀏覽器事件非常頻繁地引發，例如 `onmousemove` 和 `onscroll` ，可能會每秒引發數十或數百次。</span><span class="sxs-lookup"><span data-stu-id="82501-253">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="82501-254">在大多數情況下，您不需要經常執行 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="82501-254">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="82501-255">如果您嘗試這麼做，可能會傷害 UI 回應性或耗用過多的 CPU 時間。</span><span class="sxs-lookup"><span data-stu-id="82501-255">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="82501-256">`@onmousemove` `@onscroll` 您可能會想要使用 JS interop 來註冊較不常引發的回呼，而不是使用原生或事件。</span><span class="sxs-lookup"><span data-stu-id="82501-256">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="82501-257">例如，下列元件 () 會 `MyComponent.razor` 顯示滑鼠的位置，但每隔500毫秒最多隻會更新一次：</span><span class="sxs-lookup"><span data-stu-id="82501-257">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="82501-258">對應的 JavaScript 程式碼（可以放在頁面中， `index.html` 或以 ES6 模組的形式載入）會註冊實際的 DOM 事件接聽程式。</span><span class="sxs-lookup"><span data-stu-id="82501-258">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="82501-259">在此範例中，事件接聽程式會使用 [lodash 所的 `throttle` 函數](https://lodash.com/docs/4.17.15#throttle) 來限制調用的速率：</span><span class="sxs-lookup"><span data-stu-id="82501-259">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="82501-260">這項技術對於而言更為重要 :::no-loc(Blazor Server)::: ，因為每個事件調用都牽涉到透過網路傳遞訊息。</span><span class="sxs-lookup"><span data-stu-id="82501-260">This technique can be even more important for :::no-loc(Blazor Server):::, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="82501-261">這是很重要的， :::no-loc(Blazor WebAssembly)::: 因為它可以大幅減少轉譯工作的數量。</span><span class="sxs-lookup"><span data-stu-id="82501-261">It's valuable for :::no-loc(Blazor WebAssembly)::: because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="82501-262">優化 JavaScript interop 速度</span><span class="sxs-lookup"><span data-stu-id="82501-262">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="82501-263">.NET 和 JavaScript 之間的呼叫牽涉到一些額外的額外負荷，因為：</span><span class="sxs-lookup"><span data-stu-id="82501-263">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

 * <span data-ttu-id="82501-264">依預設，呼叫是非同步。</span><span class="sxs-lookup"><span data-stu-id="82501-264">By default, calls are asynchronous.</span></span>
 * <span data-ttu-id="82501-265">根據預設，參數和傳回值為 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="82501-265">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="82501-266">這是為了在 .NET 與 JavaScript 類型之間提供容易瞭解的轉換機制。</span><span class="sxs-lookup"><span data-stu-id="82501-266">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="82501-267">此外 :::no-loc(Blazor Server)::: ，這些呼叫會跨網路傳遞。</span><span class="sxs-lookup"><span data-stu-id="82501-267">Additionally on :::no-loc(Blazor Server):::, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="82501-268">避免過度精細的呼叫</span><span class="sxs-lookup"><span data-stu-id="82501-268">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="82501-269">因為每個呼叫都牽涉到一些額外負荷，所以減少呼叫次數可能很有價值。</span><span class="sxs-lookup"><span data-stu-id="82501-269">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="82501-270">請考慮下列程式碼，它會在瀏覽器的存放區中儲存專案的集合 `localStorage` ：</span><span class="sxs-lookup"><span data-stu-id="82501-270">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="82501-271">上述範例會針對每個專案進行個別的 JS interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="82501-271">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="82501-272">相反地，下列方法會將 JS interop 縮減為單一呼叫：</span><span class="sxs-lookup"><span data-stu-id="82501-272">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="82501-273">對應的 JavaScript 函式定義如下：</span><span class="sxs-lookup"><span data-stu-id="82501-273">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="82501-274">對於 :::no-loc(Blazor WebAssembly)::: ，這通常只有在您正在進行大量 JS interop 呼叫時才會有問題。</span><span class="sxs-lookup"><span data-stu-id="82501-274">For :::no-loc(Blazor WebAssembly):::, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="82501-275">請考慮進行同步呼叫</span><span class="sxs-lookup"><span data-stu-id="82501-275">Consider making synchronous calls</span></span>

<span data-ttu-id="82501-276">根據預設，JavaScript interop 呼叫是非同步，不論呼叫的程式碼是同步或非同步。</span><span class="sxs-lookup"><span data-stu-id="82501-276">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="82501-277">這是為了確保元件與和都相容 :::no-loc(Blazor WebAssembly)::: :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="82501-277">This is to ensure components are compatible with both :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="82501-278">在上 :::no-loc(Blazor Server)::: ，所有 JavaScript interop 呼叫都必須是非同步，因為它們是透過網路連接來傳送。</span><span class="sxs-lookup"><span data-stu-id="82501-278">On :::no-loc(Blazor Server):::, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="82501-279">如果您知道您的應用程式只會在上執行 :::no-loc(Blazor WebAssembly)::: ，您可以選擇進行同步的 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="82501-279">If you know for certain that your app only ever runs on :::no-loc(Blazor WebAssembly):::, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="82501-280">這比進行非同步呼叫的額外負荷稍微低，而且可能會產生較少的轉譯迴圈，因為等待結果時沒有中繼狀態。</span><span class="sxs-lookup"><span data-stu-id="82501-280">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="82501-281">若要從 .NET 進行同步呼叫至 JavaScript，請轉換 <xref:Microsoft.JSInterop.IJSRuntime> 成 <xref:Microsoft.JSInterop.IJSInProcessRuntime> ：</span><span class="sxs-lookup"><span data-stu-id="82501-281">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="82501-282">使用時 `IJSObjectReference` ，您可以藉由轉換成來進行同步呼叫 `IJSInProcessObjectReference` 。</span><span class="sxs-lookup"><span data-stu-id="82501-282">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="82501-283">若要從 JavaScript 對 .NET 進行同步呼叫，請使用 `DotNet.invokeMethod` 而不是 `DotNet.invokeMethodAsync` 。</span><span class="sxs-lookup"><span data-stu-id="82501-283">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="82501-284">同步呼叫會在下列情況中運作：</span><span class="sxs-lookup"><span data-stu-id="82501-284">Synchronous calls work if:</span></span>

* <span data-ttu-id="82501-285">應用程式正在執行 :::no-loc(Blazor WebAssembly)::: ，而不是 :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="82501-285">The app is running on :::no-loc(Blazor WebAssembly):::, not :::no-loc(Blazor Server):::.</span></span>
* <span data-ttu-id="82501-286">呼叫的函式會以同步方式傳回值 (它不是 `async` 方法，也不會傳回 .net <xref:System.Threading.Tasks.Task> 或 JavaScript `Promise`) 。</span><span class="sxs-lookup"><span data-stu-id="82501-286">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="82501-287">如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="82501-287">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="82501-288">考慮進行 unmarshalled 呼叫</span><span class="sxs-lookup"><span data-stu-id="82501-288">Consider making unmarshalled calls</span></span>

<span data-ttu-id="82501-289">在上執行時，您可以 :::no-loc(Blazor WebAssembly)::: 從 .net 對 JavaScript 進行 unmarshalled 呼叫。</span><span class="sxs-lookup"><span data-stu-id="82501-289">When running on :::no-loc(Blazor WebAssembly):::, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="82501-290">這些是未執行引數或傳回值之 JSON 序列化的同步呼叫。</span><span class="sxs-lookup"><span data-stu-id="82501-290">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="82501-291">.NET 和 JavaScript 標記法中的記憶體管理和翻譯的所有層面，都是由開發人員所組成。</span><span class="sxs-lookup"><span data-stu-id="82501-291">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="82501-292">雖然使用 `IJSUnmarshalledRuntime` 具有最少的 JS interop 方法額外負荷，但與這些 api 互動所需的 JavaScript api 目前尚未記載，並且在未來版本中可能會有重大變更。</span><span class="sxs-lookup"><span data-stu-id="82501-292">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="82501-293">最小化應用程式下載大小</span><span class="sxs-lookup"><span data-stu-id="82501-293">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="82501-294">中繼語言 (IL) 修剪</span><span class="sxs-lookup"><span data-stu-id="82501-294">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="82501-295">[從 :::no-loc(Blazor WebAssembly)::: 應用程式修剪未使用的元件](xref:blazor/host-and-deploy/configure-trimmer) ，會移除應用程式二進位檔中未使用的程式碼，以減少應用程式的大小。</span><span class="sxs-lookup"><span data-stu-id="82501-295">[Trimming unused assemblies from a :::no-loc(Blazor WebAssembly)::: app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="82501-296">預設會在發行應用程式時執行修剪器。</span><span class="sxs-lookup"><span data-stu-id="82501-296">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="82501-297">若要從修剪中獲益，請使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令並將 [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) 選項設為，以發佈應用程式進行部署 `Release` ：</span><span class="sxs-lookup"><span data-stu-id="82501-297">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="82501-298"> (IL) 連結的中繼語言</span><span class="sxs-lookup"><span data-stu-id="82501-298">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="82501-299">[連結 :::no-loc(Blazor WebAssembly)::: 應用程式](xref:blazor/host-and-deploy/configure-linker) 會藉由在應用程式的二進位檔中修剪未使用的程式碼，來減少應用程式的大小。</span><span class="sxs-lookup"><span data-stu-id="82501-299">[Linking a :::no-loc(Blazor WebAssembly)::: app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="82501-300">依預設，只有在設定中建立時，才會啟用中繼語言 (IL) 連結器 `Release` 。</span><span class="sxs-lookup"><span data-stu-id="82501-300">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="82501-301">若要從中獲益，請使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令並將 [-c |--configuration](/dotnet/core/tools/dotnet-publish#options) 選項設為，以發行應用程式進行部署 `Release` ：</span><span class="sxs-lookup"><span data-stu-id="82501-301">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="82501-302">使用 System.Text.Js于</span><span class="sxs-lookup"><span data-stu-id="82501-302">Use System.Text.Json</span></span>

<span data-ttu-id="82501-303">:::no-loc(Blazor):::的 JS interop 實行相依于 <xref:System.Text.Json> ，這是具有低記憶體配置的高效能 JSON 序列化程式庫。</span><span class="sxs-lookup"><span data-stu-id="82501-303">:::no-loc(Blazor):::'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="82501-304">使用 <xref:System.Text.Json> 不會導致額外的應用程式承載大小超過新增一或多個替代的 JSON 程式庫。</span><span class="sxs-lookup"><span data-stu-id="82501-304">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="82501-305">如需遷移指引，請參閱[如何從 `Newtonsoft.Json` 遷移 `System.Text.Json` 至](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)。</span><span class="sxs-lookup"><span data-stu-id="82501-305">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="82501-306">延遲載入元件</span><span class="sxs-lookup"><span data-stu-id="82501-306">Lazy load assemblies</span></span>

<span data-ttu-id="82501-307">當路由需要元件時，于執行時間載入元件。</span><span class="sxs-lookup"><span data-stu-id="82501-307">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="82501-308">如需詳細資訊，請參閱<xref:blazor/webassembly-lazy-load-assemblies>。</span><span class="sxs-lookup"><span data-stu-id="82501-308">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="82501-309">壓縮</span><span class="sxs-lookup"><span data-stu-id="82501-309">Compression</span></span>

<span data-ttu-id="82501-310">:::no-loc(Blazor WebAssembly):::發佈應用程式時，會在發行期間以靜態方式壓縮輸出，以減少應用程式的大小，並移除執行時間壓縮的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="82501-310">When a :::no-loc(Blazor WebAssembly)::: app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="82501-311">:::no-loc(Blazor)::: 依賴伺服器來執行內容 negotation，並提供靜態壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="82501-311">:::no-loc(Blazor)::: relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="82501-312">部署應用程式之後，請確認應用程式會提供壓縮檔案。</span><span class="sxs-lookup"><span data-stu-id="82501-312">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="82501-313">檢查瀏覽器開發人員工具中的 [網路] 索引標籤，並確認檔案是由或提供服務 `Content-Encoding: br` `Content-Encoding: gz` 。</span><span class="sxs-lookup"><span data-stu-id="82501-313">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="82501-314">如果主機未提供壓縮檔案，請依照中的指示進行 <xref:blazor/host-and-deploy/webassembly#compression> 。</span><span class="sxs-lookup"><span data-stu-id="82501-314">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="82501-315">停用未使用的功能</span><span class="sxs-lookup"><span data-stu-id="82501-315">Disable unused features</span></span>

<span data-ttu-id="82501-316">:::no-loc(Blazor WebAssembly):::的執行時間包含下列 .NET 功能，如果應用程式不需要它們來取得較小的承載大小，則可以停用這些功能：</span><span class="sxs-lookup"><span data-stu-id="82501-316">:::no-loc(Blazor WebAssembly):::'s runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="82501-317">包含資料檔案，以使時區資訊正確無誤。</span><span class="sxs-lookup"><span data-stu-id="82501-317">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="82501-318">如果應用程式不需要這項功能，請考慮將 `:::no-loc(Blazor):::EnableTimeZoneSupport` 應用程式專案檔中的 MSBuild 屬性設為，以停用它 `false` ：</span><span class="sxs-lookup"><span data-stu-id="82501-318">If the app doesn't require this feature, consider disabling it by setting the `:::no-loc(Blazor):::EnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <:::no-loc(Blazor):::EnableTimeZoneSupport>false</:::no-loc(Blazor):::EnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="82501-319">依預設，會 :::no-loc(Blazor WebAssembly)::: 在使用者的文化特性中攜帶顯示值所需的全球化資源，例如日期和貨幣。</span><span class="sxs-lookup"><span data-stu-id="82501-319">By default, :::no-loc(Blazor WebAssembly)::: carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="82501-320">如果應用程式不需要當地語系化，您可以 [設定應用程式以支援以](xref:blazor/globalization-localization)文化特性為基礎的非變異文化特性 `en-US` ：</span><span class="sxs-lookup"><span data-stu-id="82501-320">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="82501-321">包含了定序資訊，以使 Api <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 正常運作。</span><span class="sxs-lookup"><span data-stu-id="82501-321">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="82501-322">如果您確定應用程式不需要定序資料，請考慮將 `:::no-loc(Blazor):::WebAssemblyPreserveCollationData` 應用程式專案檔中的 MSBuild 屬性設為，以停用它 `false` ：</span><span class="sxs-lookup"><span data-stu-id="82501-322">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `:::no-loc(Blazor):::WebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <:::no-loc(Blazor):::WebAssemblyPreserveCollationData>false</:::no-loc(Blazor):::WebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
