---
title: ASP.NET Core Blazor 元件虛擬化
author: guardrex
description: 瞭解如何在 ASP.NET Core 應用程式中使用元件虛擬化 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/16/2020
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
ms.openlocfilehash: c904a3f330531f80d1dc69bf63621c59790e1cb0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722975"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="8868d-103">ASP.NET Core Blazor 元件虛擬化</span><span class="sxs-lookup"><span data-stu-id="8868d-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="8868d-104">依 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8868d-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8868d-105">使用 Blazor 架構內建的虛擬化支援，改善元件轉譯的認知效能。</span><span class="sxs-lookup"><span data-stu-id="8868d-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="8868d-106">虛擬化是一項技術，可將 UI 轉譯限制為只顯示目前可見的部分。</span><span class="sxs-lookup"><span data-stu-id="8868d-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="8868d-107">例如，當應用程式必須轉譯長清單或具有許多資料列的資料表，而且在任何指定的時間都只需要顯示專案的子集時，虛擬化就很有説明。</span><span class="sxs-lookup"><span data-stu-id="8868d-107">For example, virtualization is helpful when the app must render a long list or a table with many rows and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="8868d-108">Blazor 提供 `Virtualize` 可用於將虛擬化新增至應用程式元件的元件。</span><span class="sxs-lookup"><span data-stu-id="8868d-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8868d-109">如果沒有虛擬化，一般清單或資料表元件可能會使用 c # [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 迴圈來轉譯清單中的每個專案，或資料表中的每個資料列：</span><span class="sxs-lookup"><span data-stu-id="8868d-109">Without virtualization, a typical list or table-based component might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list or each row in the table:</span></span>

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

<span data-ttu-id="8868d-110">如果清單包含上千個專案，則轉譯清單可能需要很長的時間。</span><span class="sxs-lookup"><span data-stu-id="8868d-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="8868d-111">使用者可能會遇到明顯的 UI 延遲。</span><span class="sxs-lookup"><span data-stu-id="8868d-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="8868d-112">請將迴圈取代為 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) `Virtualize` 元件，並使用指定固定專案來源，而不是一次轉譯清單中的每個專案 `Items` 。</span><span class="sxs-lookup"><span data-stu-id="8868d-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="8868d-113">只有目前可見的專案會呈現：</span><span class="sxs-lookup"><span data-stu-id="8868d-113">Only the items that are currently visible are rendered:</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="8868d-114">如果未使用來指定元件的 `Context` 內容，請使用 `context` `@context.{PROPERTY}` 專案內容範本中 () 值：</span><span class="sxs-lookup"><span data-stu-id="8868d-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="8868d-115">`Virtualize`元件會根據容器的高度和轉譯專案的大小，計算要轉譯的專案數目。</span><span class="sxs-lookup"><span data-stu-id="8868d-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="8868d-116">專案提供者委派</span><span class="sxs-lookup"><span data-stu-id="8868d-116">Item provider delegate</span></span>

<span data-ttu-id="8868d-117">如果您不想要將所有專案載入記憶體中，可以將專案提供者委派方法指定給元件的 `ItemsProvider` 參數，以根據需求非同步地抓取要求的專案：</span><span class="sxs-lookup"><span data-stu-id="8868d-117">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="8868d-118">專案提供者會接收 `ItemsProviderRequest` ，以指定從特定開始索引開始所需的專案數目。</span><span class="sxs-lookup"><span data-stu-id="8868d-118">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="8868d-119">然後，專案提供者會從資料庫或其他服務中抓取要求的專案，並將它們 `ItemsProviderResult<TItem>` 連同總專案數一起傳回。</span><span class="sxs-lookup"><span data-stu-id="8868d-119">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="8868d-120">專案提供者可以選擇使用每個要求抓取專案，或將它們快取，以便立即可用。</span><span class="sxs-lookup"><span data-stu-id="8868d-120">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="8868d-121">請勿嘗試使用專案提供者，並將集合指派給 `Items` 相同的 `Virtualize` 元件。</span><span class="sxs-lookup"><span data-stu-id="8868d-121">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="8868d-122">下列範例會從載入員工 `EmployeeService` ：</span><span class="sxs-lookup"><span data-stu-id="8868d-122">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="8868d-123">預留位置</span><span class="sxs-lookup"><span data-stu-id="8868d-123">Placeholder</span></span>

<span data-ttu-id="8868d-124">由於要求來自遠端資料源的專案可能需要一些時間，因此您可以選擇 () 呈現預留位置， `<Placeholder>...</Placeholder>` 直到專案資料可用為止：</span><span class="sxs-lookup"><span data-stu-id="8868d-124">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a><span data-ttu-id="8868d-125">項目大小</span><span class="sxs-lookup"><span data-stu-id="8868d-125">Item size</span></span>

<span data-ttu-id="8868d-126">您可以使用 (預設值來設定每個專案的大小（以圖元為單位）： `ItemSize` 50px) ：</span><span class="sxs-lookup"><span data-stu-id="8868d-126">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a><span data-ttu-id="8868d-127">Overscan 計數</span><span class="sxs-lookup"><span data-stu-id="8868d-127">Overscan count</span></span>

<span data-ttu-id="8868d-128">`OverscanCount` 決定在可見區域之前和之後轉譯的額外專案數目。</span><span class="sxs-lookup"><span data-stu-id="8868d-128">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="8868d-129">這項設定有助於減少滾動期間轉譯的頻率。</span><span class="sxs-lookup"><span data-stu-id="8868d-129">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="8868d-130">不過，較高的值會導致在頁面中轉譯的元素越多 (預設值： 3) ：</span><span class="sxs-lookup"><span data-stu-id="8868d-130">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8868d-131">例如，呈現數百個包含元件之資料列的方格或清單，需要處理器密集的轉譯。</span><span class="sxs-lookup"><span data-stu-id="8868d-131">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="8868d-132">請考慮將方格或清單版面配置虛擬化，如此一來，就只會在任何指定時間轉譯元件的子集。</span><span class="sxs-lookup"><span data-stu-id="8868d-132">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="8868d-133">如需元件子集轉譯的範例，請參閱[ `Virtualization` (aspnet/samples GitHub 存放庫) 範例應用程式](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)中的下列元件：</span><span class="sxs-lookup"><span data-stu-id="8868d-133">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="8868d-134">`Virtualize` 元件 ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)) ：以 c # 撰寫的元件，它會 <xref:Microsoft.AspNetCore.Components.ComponentBase> 根據使用者的滾動方式來呈現一組氣象資料列。</span><span class="sxs-lookup"><span data-stu-id="8868d-134">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="8868d-135">`FetchData` 元件 ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)) ：使用 `Virtualize` 元件一次顯示25個數據列的氣象資料。</span><span class="sxs-lookup"><span data-stu-id="8868d-135">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end
