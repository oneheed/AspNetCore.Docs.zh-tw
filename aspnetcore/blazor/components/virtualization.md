---
title: ASP.NET Core Blazor 元件虛擬化
author: guardrex
description: 瞭解如何在 ASP.NET Core 應用程式中使用元件虛擬化 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
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
ms.openlocfilehash: 2ba698eaba23fd67617375e5186d40d45d9772fd
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653876"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="369a6-103">ASP.NET Core Blazor 元件虛擬化</span><span class="sxs-lookup"><span data-stu-id="369a6-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="369a6-104">依 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="369a6-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="369a6-105">使用 Blazor 架構內建的虛擬化支援，改善元件轉譯的認知效能。</span><span class="sxs-lookup"><span data-stu-id="369a6-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="369a6-106">虛擬化是一項技術，可將 UI 轉譯限制為只顯示目前可見的部分。</span><span class="sxs-lookup"><span data-stu-id="369a6-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="369a6-107">例如，當應用程式必須轉譯長清單的專案，而且在任何指定的時間都只需要顯示專案的子集時，虛擬化就很有説明。</span><span class="sxs-lookup"><span data-stu-id="369a6-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="369a6-108">Blazor 提供 `Virtualize` 可用於將虛擬化新增至應用程式元件的元件。</span><span class="sxs-lookup"><span data-stu-id="369a6-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="369a6-109">如果沒有虛擬化，一般清單可能會使用 c # [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 迴圈來轉譯清單中的每個專案：</span><span class="sxs-lookup"><span data-stu-id="369a6-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="369a6-110">如果清單包含上千個專案，則轉譯清單可能需要很長的時間。</span><span class="sxs-lookup"><span data-stu-id="369a6-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="369a6-111">使用者可能會遇到明顯的 UI 延遲。</span><span class="sxs-lookup"><span data-stu-id="369a6-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="369a6-112">請將迴圈取代為 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) `Virtualize` 元件，並使用指定固定專案來源，而不是一次轉譯清單中的每個專案 `Items` 。</span><span class="sxs-lookup"><span data-stu-id="369a6-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="369a6-113">只有目前可見的專案會呈現：</span><span class="sxs-lookup"><span data-stu-id="369a6-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="369a6-114">如果未使用來指定元件的 `Context` 內容，請使用 `context` `@context.{PROPERTY}` 專案內容範本中 () 值：</span><span class="sxs-lookup"><span data-stu-id="369a6-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="369a6-115">`Virtualize`元件會根據容器的高度和轉譯專案的大小，計算要轉譯的專案數目。</span><span class="sxs-lookup"><span data-stu-id="369a6-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="369a6-116">元件的專案內容 `Virtualize` 可以包括：</span><span class="sxs-lookup"><span data-stu-id="369a6-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="369a6-117">Razor如同上述範例所示的純 HTML 和程式碼。</span><span class="sxs-lookup"><span data-stu-id="369a6-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="369a6-118">一或多個 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="369a6-118">One or more Razor components.</span></span>
* <span data-ttu-id="369a6-119">HTML/ Razor 和元件的混合 Razor 。</span><span class="sxs-lookup"><span data-stu-id="369a6-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="369a6-120">專案提供者委派</span><span class="sxs-lookup"><span data-stu-id="369a6-120">Item provider delegate</span></span>

<span data-ttu-id="369a6-121">如果您不想要將所有專案載入記憶體中，可以將專案提供者委派方法指定給元件的 `ItemsProvider` 參數，以根據需求非同步地抓取要求的專案：</span><span class="sxs-lookup"><span data-stu-id="369a6-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="369a6-122">專案提供者會接收 `ItemsProviderRequest` ，以指定從特定開始索引開始所需的專案數目。</span><span class="sxs-lookup"><span data-stu-id="369a6-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="369a6-123">然後，專案提供者會從資料庫或其他服務中抓取要求的專案，並將它們 `ItemsProviderResult<TItem>` 連同總專案數一起傳回。</span><span class="sxs-lookup"><span data-stu-id="369a6-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="369a6-124">專案提供者可以選擇使用每個要求抓取專案，或將它們快取，以便立即可用。</span><span class="sxs-lookup"><span data-stu-id="369a6-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="369a6-125">請勿嘗試使用專案提供者，並將集合指派給 `Items` 相同的 `Virtualize` 元件。</span><span class="sxs-lookup"><span data-stu-id="369a6-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="369a6-126">下列範例會從載入員工 `EmployeeService` ：</span><span class="sxs-lookup"><span data-stu-id="369a6-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="369a6-127">預留位置</span><span class="sxs-lookup"><span data-stu-id="369a6-127">Placeholder</span></span>

<span data-ttu-id="369a6-128">由於要求來自遠端資料源的專案可能需要一些時間，因此您可以選擇 () 呈現預留位置， `<Placeholder>...</Placeholder>` 直到專案資料可用為止：</span><span class="sxs-lookup"><span data-stu-id="369a6-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="369a6-129">項目大小</span><span class="sxs-lookup"><span data-stu-id="369a6-129">Item size</span></span>

<span data-ttu-id="369a6-130">您可以使用 (預設值來設定每個專案的大小（以圖元為單位）： `ItemSize` 50px) ：</span><span class="sxs-lookup"><span data-stu-id="369a6-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="369a6-131">Overscan 計數</span><span class="sxs-lookup"><span data-stu-id="369a6-131">Overscan count</span></span>

<span data-ttu-id="369a6-132">`OverscanCount` 決定在可見區域之前和之後轉譯的額外專案數目。</span><span class="sxs-lookup"><span data-stu-id="369a6-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="369a6-133">這項設定有助於減少滾動期間轉譯的頻率。</span><span class="sxs-lookup"><span data-stu-id="369a6-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="369a6-134">不過，較高的值會導致在頁面中轉譯的元素越多 (預設值： 3) ：</span><span class="sxs-lookup"><span data-stu-id="369a6-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="369a6-135">例如，呈現數百個包含元件之資料列的方格或清單，需要處理器密集的轉譯。</span><span class="sxs-lookup"><span data-stu-id="369a6-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="369a6-136">請考慮將方格或清單版面配置虛擬化，如此一來，就只會在任何指定時間轉譯元件的子集。</span><span class="sxs-lookup"><span data-stu-id="369a6-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span>

<span data-ttu-id="369a6-137">下列 `Virtualize` 元件 (`Virtualize.cs`) 會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 根據使用者的滾動來呈現子內容：</span><span class="sxs-lookup"><span data-stu-id="369a6-137">The following `Virtualize` component (`Virtualize.cs`) implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render child content based on user scrolling:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Rendering;
using Microsoft.JSInterop;

public class Virtualize<TItem> : ComponentBase
{
    [Parameter]
    public string TagName { get; set; } = "div";

    [Parameter]
    public RenderFragment<TItem> ChildContent { get; set; }

    [Parameter]
    public ICollection<TItem> Items { get; set; }

    [Parameter]
    public double ItemHeight { get; set; }

    [Parameter(CaptureUnmatchedValues = true)] 
    public Dictionary<string, object> Attributes { get; set; }

    [Inject]
    IJSRuntime JS { get; set; }

    ElementReference contentElement;
    int numItemsToSkipBefore;
    int numItemsToShow;

    protected override void BuildRenderTree(RenderTreeBuilder builder)
    {
        // Render actual content
        builder.OpenElement(0, TagName);
        builder.AddMultipleAttributes(1, Attributes);

        var translateY = numItemsToSkipBefore * ItemHeight;
        builder.AddAttribute(2, "style", $"transform: translateY({ translateY }px);");
        builder.AddAttribute(2, "data-translateY", translateY);
        builder.AddElementReferenceCapture(3, @ref => { contentElement = @ref; });

        // As an important optimization, *don't* use builder.AddContent(seq, ChildContent, item)
        // because that implicitly wraps a new region around each item, which in turn means that 
        // @key does nothing (because keys are scoped to regions). Instead, create a single 
        // container region and then invoke the fragments directly.

        builder.OpenRegion(4);

        foreach (var item in Items.Skip(numItemsToSkipBefore).Take(numItemsToShow))
        {
            ChildContent(item)(builder);
        }

        builder.CloseRegion();

        builder.CloseElement();

        // Also emit a spacer that causes the total vertical height to add up to 
        // Items.Count()*numItems

        builder.OpenElement(5, "div");
        var numHiddenItems = Items.Count - numItemsToShow;
        builder.AddAttribute(6, "style", 
            $"width: 1px; height: { numHiddenItems * ItemHeight }px;");
        builder.CloseElement();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            var objectRef = DotNetObjectReference.Create(this);
            var initResult = await JS.InvokeAsync<ScrollEventArgs>(
                "VirtualizedComponent._initialize", objectRef, contentElement);
            OnScroll(initResult);
        }
    }

    [JSInvokable]
    public void OnScroll(ScrollEventArgs args)
    {
        var relativeTop = args.ContainerRect.Top - args.ContentRect.Top;
        numItemsToSkipBefore = Math.Max(0, (int)(relativeTop / ItemHeight));

        var visibleHeight = args.ContainerRect.Bottom - 
            (args.ContentRect.Top + numItemsToSkipBefore * ItemHeight);
        numItemsToShow = (int)Math.Ceiling(visibleHeight / ItemHeight) + 3;

        StateHasChanged();
    }

    public class ScrollEventArgs
    {
        public DOMRect ContainerRect { get; set; }
        public DOMRect ContentRect { get; set; }
    }

    public class DOMRect
    {
        public double Top { get; set; }
        public double Bottom { get; set; }
        public double Left { get; set; } 
        public double Right { get; set; }
        public double Width { get; set; }
        public double Height { get; set; }
    }
}
```

<span data-ttu-id="369a6-138">下列 `FetchData` 元件 (`FetchData.razor`) 會使用上述 `Virtualize` 元件一次顯示25個數據列的氣象資料：</span><span class="sxs-lookup"><span data-stu-id="369a6-138">The following `FetchData` component (`FetchData.razor`) uses the preceding `Virtualize` component to display 25 rows of weather data at a time:</span></span>

```razor
@page "/"
@page "/fetchdata"
@inject HttpClient Http

<h1>Weather forecast</h1>

<p>This component demonstrates fetching data from a service.</p>

@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h2>Using <code>table-layout: fixed</code></h2>
    <div style="height:300px; overflow-y: auto;">
        <Virtualize ItemHeight="25" Items="@forecasts">
            <tr @key="@context.GetHashCode()" 
                    style="display: table; table-layout: fixed; width: 100%;">
                <td>@context.Date.ToShortDateString()</td>
                <td>@context.TemperatureC</td>
                <td>@context.TemperatureF</td>
                <td>@context.Summary</td>
            </tr>
        </Virtualize>
    </div>

    <h2>Using <code>position: sticky</code></h2>
    <div style="height: 300px; overflow-y: auto; position: relative;">
        <table>
            <thead class="sticky">
                <tr>
                    <th>Date</th>
                    <th>Temperature (C)</th>
                    <th>Temperature (F)</th>
                    <th>Summary</th>
                </tr>
            </thead>

            <Virtualize TagName="tbody" ItemHeight="25" Items="@forecasts">
                <tr @key="@context.GetHashCode()">
                    <td>@context.Date.ToShortDateString()</td>
                    <td>@context.TemperatureC</td>
                    <td>@context.TemperatureF</td>
                    <td>@context.Summary</td>
                </tr>
            </Virtualize>
        </table>
    </div>

    <style type="text/css">
        thead.sticky th {
            position: sticky;
            top: 0;
        }
        tr td {
            height: 25px;
        }
    </style>
}

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "sample-data/weather.json");
    }

    public class WeatherForecast
    {
        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public string Summary { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
    }
}
```

<span data-ttu-id="369a6-139">在上述範例中，方法是避免每個個別專案的絕對位置。</span><span class="sxs-lookup"><span data-stu-id="369a6-139">In the preceding example, the approach is about avoiding absolute positioning for each individual item.</span></span> <span data-ttu-id="369a6-140">絕對位置會有一些優點 (我們可以確保專案會佔用指定的 Y 空間量) 但會造成您遺失正常寬度的不良缺點，而且當根據內容調整大小時，無法讓資料表資料行對齊資料列/標頭。</span><span class="sxs-lookup"><span data-stu-id="369a6-140">Absolute positioning would have some advantages (we can be sure the items do take up the specified amount of Y space) but has the bad disadvantage that you lose the normal widths and can't get table columns to line up across rows/header when based on content sizing.</span></span>

<span data-ttu-id="369a6-141">元件設計背後的概念 `Virtualize` 是元件不會變更在 DOM 內設定項目的方式。</span><span class="sxs-lookup"><span data-stu-id="369a6-141">The concept behind the design of the `Virtualize` component is that the component doesn't change how the items are laid out within the DOM.</span></span> <span data-ttu-id="369a6-142">除了您指定的包裝函式專案以外，沒有任何額外的包裝函式元素 `TagName` 。</span><span class="sxs-lookup"><span data-stu-id="369a6-142">There are no added wrapper elements, besides the single one whose `TagName` you specify.</span></span>

<span data-ttu-id="369a6-143">最好的方法是避免甚至是包裝函式 `TagName` 元素。</span><span class="sxs-lookup"><span data-stu-id="369a6-143">The best approach is to avoid even the `TagName` wrapper element.</span></span> <span data-ttu-id="369a6-144">讓 `Virtualize` 元件不會發出自己的任何元素。</span><span class="sxs-lookup"><span data-stu-id="369a6-144">Have the `Virtualize` component emit no elements of its own.</span></span> <span data-ttu-id="369a6-145">所有的元件都是轉譯的，不過有許多範本實例都需要填滿最接近的可滾動上階中任何剩餘的可見空間，加上下列的分隔元素，讓可滾動的上階具有右滾動範圍。</span><span class="sxs-lookup"><span data-stu-id="369a6-145">All the component does is render however many of the template instances are required to fill up any remaining visible space in the closest scrollable ancestor, plus add a following spacer element that makes the scrollable ancestor have the right scrolling range.</span></span> <span data-ttu-id="369a6-146">至於版面配置，就如同在 DOM 中實際的子系完全範圍一樣。</span><span class="sxs-lookup"><span data-stu-id="369a6-146">As far as the layout is concerned, it's the same as if the full range of children were physically in the DOM.</span></span> <span data-ttu-id="369a6-147">不過，您必須指定正確的 `ItemHeight` 。</span><span class="sxs-lookup"><span data-stu-id="369a6-147">It does require you to specify an accurate `ItemHeight` though.</span></span> <span data-ttu-id="369a6-148">如果您遇到錯誤 (例如，因為您感到混淆，並認為它是有效的，以便 `style.height` 在) 上指定 `<tr>` ，則此元件最後會轉譯錯誤的範本實例數目，而不是填滿 UI 或太多的轉譯。</span><span class="sxs-lookup"><span data-stu-id="369a6-148">If you get it wrong (for example, because you're confused and think it's valid to specify `style.height` on a `<tr>`), then the component ends up rendering the wrong number of template instances and either not fill up the UI or inefficiently render too many.</span></span> <span data-ttu-id="369a6-149">此外，父系的捲軸範圍也不正確。</span><span class="sxs-lookup"><span data-stu-id="369a6-149">Additionally, the scroll range on the parent won't be correct.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="369a6-150">狀態變更</span><span class="sxs-lookup"><span data-stu-id="369a6-150">State changes</span></span>

<span data-ttu-id="369a6-151">對元件所轉譯的專案進行變更時 `Virtualize` ，請呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以強制重新評估和轉譯資料流程元件。</span><span class="sxs-lookup"><span data-stu-id="369a6-151">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
