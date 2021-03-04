---
title: ASP.NET 核心 Blazor 元件虛擬化
author: guardrex
description: 瞭解如何在 ASP.NET Core 應用程式中使用元件虛擬化 Blazor 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2021
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
uid: blazor/components/virtualization
ms.openlocfilehash: c81732c29b262e9134a4ff7dab077a4f31db96af
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109815"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="a6b14-103">ASP.NET 核心 Blazor 元件虛擬化</span><span class="sxs-lookup"><span data-stu-id="a6b14-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="a6b14-104">使用此 Blazor [ `Virtualize` 元件](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)的架構內建虛擬化支援，改善元件轉譯的認知效能。</span><span class="sxs-lookup"><span data-stu-id="a6b14-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support with the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601).</span></span> <span data-ttu-id="a6b14-105">虛擬化是一項技術，可將 UI 轉譯限制為只顯示目前可見的部分。</span><span class="sxs-lookup"><span data-stu-id="a6b14-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="a6b14-106">例如，當應用程式必須轉譯長清單的專案，而且在任何指定的時間都只需要顯示專案的子集時，虛擬化就很有説明。</span><span class="sxs-lookup"><span data-stu-id="a6b14-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span>

<span data-ttu-id="a6b14-107">使用元件的時機 `Virtualize` ：</span><span class="sxs-lookup"><span data-stu-id="a6b14-107">Use the `Virtualize` component when:</span></span>

* <span data-ttu-id="a6b14-108">在迴圈中呈現一組資料項目。</span><span class="sxs-lookup"><span data-stu-id="a6b14-108">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="a6b14-109">大部分的專案都因為滾動而無法顯示。</span><span class="sxs-lookup"><span data-stu-id="a6b14-109">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="a6b14-110">轉譯的專案大小相同。</span><span class="sxs-lookup"><span data-stu-id="a6b14-110">The rendered items are the same size.</span></span>

<span data-ttu-id="a6b14-111">當使用者滾動至元件的專案清單中的任意點時 `Virtualize` ，該元件會計算要顯示的可見專案。</span><span class="sxs-lookup"><span data-stu-id="a6b14-111">When the user scrolls to an arbitrary point in the `Virtualize` component's list of items, the component calculates the visible items to show.</span></span> <span data-ttu-id="a6b14-112">未顯示的專案則不會呈現。</span><span class="sxs-lookup"><span data-stu-id="a6b14-112">Unseen items aren't rendered.</span></span>

<span data-ttu-id="a6b14-113">如果沒有虛擬化，一般清單可能會使用 c # [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 迴圈來轉譯清單中的每個專案。</span><span class="sxs-lookup"><span data-stu-id="a6b14-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in a list.</span></span> <span data-ttu-id="a6b14-114">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="a6b14-114">In the following example:</span></span>

* <span data-ttu-id="a6b14-115">`allFlights` 是飛機航班的集合。</span><span class="sxs-lookup"><span data-stu-id="a6b14-115">`allFlights` is a collection of airplane flights.</span></span>
* <span data-ttu-id="a6b14-116">`FlightSummary`元件會顯示每個航班的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="a6b14-116">The `FlightSummary` component displays details about each flight.</span></span>
* <span data-ttu-id="a6b14-117">指示詞[ `@key` 屬性](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)會將每個元件的關聯性保留 `FlightSummary` 給航班的轉譯飛行 `FlightId` 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-117">The [`@key` directive attribute](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components) preserves the relationship of each `FlightSummary` component to its rendered flight by the flight's `FlightId`.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="a6b14-118">如果集合包含上千個航班，則轉譯航班需要很長的時間，而且使用者會遇到明顯的 UI 延遲。</span><span class="sxs-lookup"><span data-stu-id="a6b14-118">If the collection contains thousands of flights, rendering the flights takes a long time and users experience a noticeable UI lag.</span></span> <span data-ttu-id="a6b14-119">大部分航班都不會轉譯，因為它們落在元素的高度之外 `<div>` 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-119">Most of the flights aren't rendered because they fall outside of the height of the `<div>` element.</span></span>

<span data-ttu-id="a6b14-120">請將上述範例中的迴圈取代為元件，而不是一次轉譯整個航班清單 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) `Virtualize` ：</span><span class="sxs-lookup"><span data-stu-id="a6b14-120">Instead of rendering the entire list of flights at once, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop in the preceding example with the `Virtualize` component:</span></span>

* <span data-ttu-id="a6b14-121">`allFlights`將指定為的固定專案來源 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-121">Specify `allFlights` as a fixed item source to <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a6b14-122">只有目前可見的航班會由元件呈現 `Virtualize` 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-122">Only the currently visible flights are rendered by the `Virtualize` component.</span></span>
* <span data-ttu-id="a6b14-123">使用參數指定每個航班的內容 `Context` 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-123">Specify a context for each flight with the `Context` parameter.</span></span> <span data-ttu-id="a6b14-124">在下列範例中， `flight` 會使用做為內容，以提供每個航班成員的存取權。</span><span class="sxs-lookup"><span data-stu-id="a6b14-124">In the following example, `flight` is used as the context, which provides access to each flight's members.</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="a6b14-125">如果未使用參數來指定內容 `Context` ，請使用 `context` 專案內容範本中的值來存取每個航班的成員：</span><span class="sxs-lookup"><span data-stu-id="a6b14-125">If a context isn't specified with the `Context` parameter, use the value of `context` in the item content template to access each flight's members:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="a6b14-126">`Virtualize`元件：</span><span class="sxs-lookup"><span data-stu-id="a6b14-126">The `Virtualize` component:</span></span>

* <span data-ttu-id="a6b14-127">根據容器的高度和轉譯專案的大小，計算要呈現的專案數目。</span><span class="sxs-lookup"><span data-stu-id="a6b14-127">Calculates the number of items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="a6b14-128">在使用者滾動時重新計算和轉譯中專案。</span><span class="sxs-lookup"><span data-stu-id="a6b14-128">Recalculates and rerenders the items as the user scrolls.</span></span>
* <span data-ttu-id="a6b14-129">只會從對應到目前可見區域的外部 API 提取記錄的配量，而不是從集合中下載所有資料。</span><span class="sxs-lookup"><span data-stu-id="a6b14-129">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="a6b14-130">元件的專案內容 `Virtualize` 可以包括：</span><span class="sxs-lookup"><span data-stu-id="a6b14-130">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="a6b14-131">Razor如同上述範例所示的純 HTML 和程式碼。</span><span class="sxs-lookup"><span data-stu-id="a6b14-131">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="a6b14-132">一或多個 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="a6b14-132">One or more Razor components.</span></span>
* <span data-ttu-id="a6b14-133">HTML/ Razor 和元件的混合 Razor 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-133">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="a6b14-134">專案提供者委派</span><span class="sxs-lookup"><span data-stu-id="a6b14-134">Item provider delegate</span></span>

<span data-ttu-id="a6b14-135">如果您不想要將所有專案載入記憶體中，可以將專案提供者委派方法指定給元件的 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> 參數，以根據需求非同步地抓取要求的專案。</span><span class="sxs-lookup"><span data-stu-id="a6b14-135">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="a6b14-136">在下列範例中，方法會將 `LoadEmployees` 專案提供給 `Virtualize` 元件：</span><span class="sxs-lookup"><span data-stu-id="a6b14-136">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="a6b14-137">專案提供者會接收 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> ，以指定從特定開始索引開始所需的專案數目。</span><span class="sxs-lookup"><span data-stu-id="a6b14-137">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="a6b14-138">然後，專案提供者會從資料庫或其他服務中抓取要求的專案，並將它們 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> 連同總專案數一起傳回。</span><span class="sxs-lookup"><span data-stu-id="a6b14-138">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="a6b14-139">專案提供者可以選擇使用每個要求抓取專案，或將它們快取，以便立即可用。</span><span class="sxs-lookup"><span data-stu-id="a6b14-139">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="a6b14-140">`Virtualize`元件只能接受 **一個專案來源** 的參數，因此請勿嘗試同時使用專案提供者，並將集合指派給 `Items` 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-140">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="a6b14-141">如果兩者都被指派， <xref:System.InvalidOperationException> 當元件的參數在執行時間設定時，就會擲回。</span><span class="sxs-lookup"><span data-stu-id="a6b14-141">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="a6b14-142">下列 `LoadEmployees` 方法範例 `EmployeeService` 會從未顯示)  (載入員工：</span><span class="sxs-lookup"><span data-stu-id="a6b14-142">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<span data-ttu-id="a6b14-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> 指示元件從其 rerequest 資料 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-143"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="a6b14-144">當外部資料變更時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="a6b14-144">This is useful when external data changes.</span></span> <span data-ttu-id="a6b14-145">使用時，不需要呼叫 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-145">There's no need to call <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A> when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="a6b14-146">預留位置</span><span class="sxs-lookup"><span data-stu-id="a6b14-146">Placeholder</span></span>

<span data-ttu-id="a6b14-147">由於要求來自遠端資料源的專案可能需要一些時間，因此您可以選擇以專案內容呈現預留位置：</span><span class="sxs-lookup"><span data-stu-id="a6b14-147">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="a6b14-148">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) 來顯示內容，直到專案資料可供使用為止。</span><span class="sxs-lookup"><span data-stu-id="a6b14-148">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="a6b14-149">用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> 來設定清單的專案範本。</span><span class="sxs-lookup"><span data-stu-id="a6b14-149">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="a6b14-150">項目大小</span><span class="sxs-lookup"><span data-stu-id="a6b14-150">Item size</span></span>

<span data-ttu-id="a6b14-151">每個專案的高度（以圖元為單位）可以使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (預設值： 50) 來設定。</span><span class="sxs-lookup"><span data-stu-id="a6b14-151">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50).</span></span> <span data-ttu-id="a6b14-152">下列範例會將每個專案的高度從預設的50圖元變更為25圖元：</span><span class="sxs-lookup"><span data-stu-id="a6b14-152">The following example changes the height of each item from the default of 50 pixels to 25 pixels:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="a6b14-153">根據預設，此 `Virtualize` 元件會測量轉譯大小 (在初始轉譯 *之後* ，個別專案的高度) 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-153">By default, the `Virtualize` component measures the rendering size (height) of individual items *after* the initial render occurs.</span></span> <span data-ttu-id="a6b14-154">您 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 可以使用，事先提供確切的專案大小來協助精確的初始轉譯效能，以及確保頁面重載的正確滾動位置。</span><span class="sxs-lookup"><span data-stu-id="a6b14-154">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="a6b14-155">如果預設值 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 導致某些專案在目前可見的視圖之外轉譯，則會觸發第二次重呈現。</span><span class="sxs-lookup"><span data-stu-id="a6b14-155">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="a6b14-156">若要在虛擬化清單中正確維護瀏覽器的滾動位置，初始轉譯必須是正確的。</span><span class="sxs-lookup"><span data-stu-id="a6b14-156">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="a6b14-157">如果不是，使用者可能會看到錯誤的專案。</span><span class="sxs-lookup"><span data-stu-id="a6b14-157">If not, users might view the wrong items.</span></span>

## <a name="overscan-count"></a><span data-ttu-id="a6b14-158">Overscan 計數</span><span class="sxs-lookup"><span data-stu-id="a6b14-158">Overscan count</span></span>

<span data-ttu-id="a6b14-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> 決定在可見區域之前和之後轉譯的額外專案數目。</span><span class="sxs-lookup"><span data-stu-id="a6b14-159"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="a6b14-160">這項設定有助於減少滾動期間轉譯的頻率。</span><span class="sxs-lookup"><span data-stu-id="a6b14-160">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="a6b14-161">不過，較高的值會導致在頁面中轉譯的元素越多 (預設值： 3) 。</span><span class="sxs-lookup"><span data-stu-id="a6b14-161">However, higher values result in more elements rendered in the page (default: 3).</span></span> <span data-ttu-id="a6b14-162">下列範例會將 overscan 計數從預設的三個專案變更為四個專案：</span><span class="sxs-lookup"><span data-stu-id="a6b14-162">The following example changes the overscan count from the default of three items to four items:</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="a6b14-163">狀態變更</span><span class="sxs-lookup"><span data-stu-id="a6b14-163">State changes</span></span>

<span data-ttu-id="a6b14-164">對元件所轉譯的專案進行變更時 `Virtualize` ，請呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以強制重新評估和轉譯資料流程元件。</span><span class="sxs-lookup"><span data-stu-id="a6b14-164">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="a6b14-165">如需詳細資訊，請參閱<xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="a6b14-165">For more information, see <xref:blazor/components/rendering>.</span></span>
