---
title: ASP.NET Core Blazor 元件虛擬化
author: guardrex
description: 瞭解如何在 ASP.NET Core 應用程式中使用元件虛擬化 Blazor 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
ms.openlocfilehash: d9fc767a4b5160c616053b075ba92194bcffa275
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280019"
---
# <a name="aspnet-core-blazor-component-virtualization"></a><span data-ttu-id="e8ecc-103">ASP.NET Core Blazor 元件虛擬化</span><span class="sxs-lookup"><span data-stu-id="e8ecc-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="e8ecc-104">使用 Blazor 架構內建的虛擬化支援，改善元件轉譯的認知效能。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-104">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="e8ecc-105">虛擬化是一項技術，可將 UI 轉譯限制為只顯示目前可見的部分。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-105">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="e8ecc-106">例如，當應用程式必須轉譯長清單的專案，而且在任何指定的時間都只需要顯示專案的子集時，虛擬化就很有説明。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-106">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="e8ecc-107">Blazor提供可用於將虛擬化新增至應用程式元件的[ `Virtualize` 元件](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-107">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="e8ecc-108">`Virtualize`元件可用於下列情況：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-108">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="e8ecc-109">在迴圈中呈現一組資料項目。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-109">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="e8ecc-110">大部分的專案都因為滾動而無法顯示。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-110">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="e8ecc-111">轉譯的專案大小完全相同。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-111">The rendered items are exactly the same size.</span></span> <span data-ttu-id="e8ecc-112">當使用者滾動至任意點時，元件可以計算要顯示的可見專案。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-112">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="e8ecc-113">如果沒有虛擬化，一般清單可能會使用 c # [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 迴圈來轉譯清單中的每個專案：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-113">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="e8ecc-114">如果清單包含上千個專案，則轉譯清單可能需要很長的時間。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-114">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="e8ecc-115">使用者可能會遇到明顯的 UI 延遲。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-115">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="e8ecc-116">請將迴圈取代為 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) `Virtualize` 元件，並使用指定固定專案來源，而不是一次轉譯清單中的每個專案 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-116">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e8ecc-117">只有目前可見的專案會呈現：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-117">Only the items that are currently visible are rendered:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="e8ecc-118">如果未使用指定元件的 `Context` 內容，請使用 `context` 專案內容範本中的值：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-118">If not specifying a context to the component with `Context`, use the `context` value in the item content template:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="e8ecc-119">您可以使用指示詞屬性來控制模型物件到專案和元件的對應處理 [`@key`](xref:mvc/views/razor#key) 。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-119">The mapping process of model objects to elements and components can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="e8ecc-120">`@key` 使比較演算法保證根據索引鍵的值來保留元素或元件。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-120">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="e8ecc-121">如需詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-121">For more information, see the following articles:</span></span>
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [<span data-ttu-id="e8ecc-122">Razor ASP.NET Core 的語法參考</span><span class="sxs-lookup"><span data-stu-id="e8ecc-122">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor#key)

<span data-ttu-id="e8ecc-123">`Virtualize`元件：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="e8ecc-124">根據容器的高度和轉譯專案的大小，計算要轉譯的專案數目。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="e8ecc-125">在使用者滾動時重新計算和轉譯中專案。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="e8ecc-126">只會從對應到目前可見區域的外部 API 提取記錄的配量，而不是從集合中下載所有資料。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="e8ecc-127">元件的專案內容 `Virtualize` 可以包括：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="e8ecc-128">Razor如同上述範例所示的純 HTML 和程式碼。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="e8ecc-129">一或多個 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-129">One or more Razor components.</span></span>
* <span data-ttu-id="e8ecc-130">HTML/ Razor 和元件的混合 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="e8ecc-131">專案提供者委派</span><span class="sxs-lookup"><span data-stu-id="e8ecc-131">Item provider delegate</span></span>

<span data-ttu-id="e8ecc-132">如果您不想要將所有專案載入記憶體中，可以將專案提供者委派方法指定給元件的 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> 參數，以根據需求非同步地抓取要求的專案。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="e8ecc-133">在下列範例中，方法會將 `LoadEmployees` 專案提供給 `Virtualize` 元件：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-133">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="e8ecc-134">專案提供者會接收 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest> ，以指定從特定開始索引開始所需的專案數目。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-134">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="e8ecc-135">然後，專案提供者會從資料庫或其他服務中抓取要求的專案，並將它們 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> 連同總專案數一起傳回。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-135">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="e8ecc-136">專案提供者可以選擇使用每個要求抓取專案，或將它們快取，以便立即可用。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-136">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="e8ecc-137">`Virtualize`元件只能接受 **一個專案來源** 的參數，因此請勿嘗試同時使用專案提供者，並將集合指派給 `Items` 。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-137">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="e8ecc-138">如果兩者都被指派， <xref:System.InvalidOperationException> 當元件的參數在執行時間設定時，就會擲回。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-138">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="e8ecc-139">下列 `LoadEmployees` 方法範例 `EmployeeService` 會從未顯示)  (載入員工：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-139">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

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

<span data-ttu-id="e8ecc-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> 指示元件從其 rerequest 資料 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="e8ecc-141">當外部資料變更時，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-141">This is useful when external data changes.</span></span> <span data-ttu-id="e8ecc-142">使用時，不需要呼叫此 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-142">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="e8ecc-143">預留位置</span><span class="sxs-lookup"><span data-stu-id="e8ecc-143">Placeholder</span></span>

<span data-ttu-id="e8ecc-144">由於要求來自遠端資料源的專案可能需要一些時間，因此您可以選擇以專案內容呈現預留位置：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-144">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="e8ecc-145">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) 來顯示內容，直到專案資料可供使用為止。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-145">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="e8ecc-146">用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> 來設定清單的專案範本。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-146">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="e8ecc-147">項目大小</span><span class="sxs-lookup"><span data-stu-id="e8ecc-147">Item size</span></span>

<span data-ttu-id="e8ecc-148">每個專案的高度（以圖元為單位）可以設定為 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (預設值： 50) ：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-148">The height of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

<span data-ttu-id="e8ecc-149">根據預設， `Virtualize` 元件會在初始轉譯發生 *之後* 測量實際轉譯大小。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-149">By default, the `Virtualize` component measures the actual rendering size *after* the initial render occurs.</span></span> <span data-ttu-id="e8ecc-150">您 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 可以使用，事先提供確切的專案大小來協助精確的初始轉譯效能，以及確保頁面重載的正確滾動位置。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-150">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> to provide an exact item size in advance to assist with accurate initial render performance and to ensure the correct scroll position for page reloads.</span></span> <span data-ttu-id="e8ecc-151">如果預設值 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> 導致某些專案在目前可見的視圖之外轉譯，則會觸發第二次重呈現。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-151">If the default <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A> causes some items to render outside of the currently visible view, a second re-render is triggered.</span></span> <span data-ttu-id="e8ecc-152">若要在虛擬化清單中正確維護瀏覽器的滾動位置，初始轉譯必須是正確的。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-152">To correctly maintain the browser's scroll position in a virtualized list, the initial render must be correct.</span></span> <span data-ttu-id="e8ecc-153">如果不是，使用者可能會看到錯誤的專案。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-153">If not, users might view the wrong items.</span></span> 

## <a name="overscan-count"></a><span data-ttu-id="e8ecc-154">Overscan 計數</span><span class="sxs-lookup"><span data-stu-id="e8ecc-154">Overscan count</span></span>

<span data-ttu-id="e8ecc-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> 決定在可見區域之前和之後轉譯的額外專案數目。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-155"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="e8ecc-156">這項設定有助於減少滾動期間轉譯的頻率。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-156">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="e8ecc-157">不過，較高的值會導致在頁面中轉譯的元素越多 (預設值： 3) ：</span><span class="sxs-lookup"><span data-stu-id="e8ecc-157">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="e8ecc-158">狀態變更</span><span class="sxs-lookup"><span data-stu-id="e8ecc-158">State changes</span></span>

<span data-ttu-id="e8ecc-159">對元件所轉譯的專案進行變更時 `Virtualize` ，請呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以強制重新評估和轉譯資料流程元件。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-159">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="e8ecc-160">如需詳細資訊，請參閱<xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="e8ecc-160">For more information, see <xref:blazor/components/rendering>.</span></span>
