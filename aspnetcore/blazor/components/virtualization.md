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
ms.openlocfilehash: 920a23aee0d0555e93c829142700709d5881afd2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753086"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="065c9-103">ASP.NET Core Blazor 元件虛擬化</span><span class="sxs-lookup"><span data-stu-id="065c9-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="065c9-104">依 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="065c9-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="065c9-105">使用 Blazor 架構內建的虛擬化支援，改善元件轉譯的認知效能。</span><span class="sxs-lookup"><span data-stu-id="065c9-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="065c9-106">虛擬化是一項技術，可將 UI 轉譯限制為只顯示目前可見的部分。</span><span class="sxs-lookup"><span data-stu-id="065c9-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="065c9-107">例如，當應用程式必須轉譯長清單的專案，而且在任何指定的時間都只需要顯示專案的子集時，虛擬化就很有説明。</span><span class="sxs-lookup"><span data-stu-id="065c9-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="065c9-108">Blazor 提供 `Virtualize` 可用於將虛擬化新增至應用程式元件的元件。</span><span class="sxs-lookup"><span data-stu-id="065c9-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="065c9-109">如果沒有虛擬化，一般清單可能會使用 c # [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 迴圈來轉譯清單中的每個專案：</span><span class="sxs-lookup"><span data-stu-id="065c9-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="065c9-110">如果清單包含上千個專案，則轉譯清單可能需要很長的時間。</span><span class="sxs-lookup"><span data-stu-id="065c9-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="065c9-111">使用者可能會遇到明顯的 UI 延遲。</span><span class="sxs-lookup"><span data-stu-id="065c9-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="065c9-112">請將迴圈取代為 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) `Virtualize` 元件，並使用指定固定專案來源，而不是一次轉譯清單中的每個專案 `Items` 。</span><span class="sxs-lookup"><span data-stu-id="065c9-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="065c9-113">只有目前可見的專案會呈現：</span><span class="sxs-lookup"><span data-stu-id="065c9-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="065c9-114">如果未使用來指定元件的 `Context` 內容，請使用 `context` `@context.{PROPERTY}` 專案內容範本中 () 值：</span><span class="sxs-lookup"><span data-stu-id="065c9-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="065c9-115">`Virtualize`元件會根據容器的高度和轉譯專案的大小，計算要轉譯的專案數目。</span><span class="sxs-lookup"><span data-stu-id="065c9-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="065c9-116">元件的專案內容 `Virtualize` 可以包括：</span><span class="sxs-lookup"><span data-stu-id="065c9-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="065c9-117">Razor如同上述範例所示的純 HTML 和程式碼。</span><span class="sxs-lookup"><span data-stu-id="065c9-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="065c9-118">一或多個 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="065c9-118">One or more Razor components.</span></span>
* <span data-ttu-id="065c9-119">HTML/ Razor 和元件的混合 Razor 。</span><span class="sxs-lookup"><span data-stu-id="065c9-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="065c9-120">專案提供者委派</span><span class="sxs-lookup"><span data-stu-id="065c9-120">Item provider delegate</span></span>

<span data-ttu-id="065c9-121">如果您不想要將所有專案載入記憶體中，可以將專案提供者委派方法指定給元件的 `ItemsProvider` 參數，以根據需求非同步地抓取要求的專案：</span><span class="sxs-lookup"><span data-stu-id="065c9-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="065c9-122">專案提供者會接收 `ItemsProviderRequest` ，以指定從特定開始索引開始所需的專案數目。</span><span class="sxs-lookup"><span data-stu-id="065c9-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="065c9-123">然後，專案提供者會從資料庫或其他服務中抓取要求的專案，並將它們 `ItemsProviderResult<TItem>` 連同總專案數一起傳回。</span><span class="sxs-lookup"><span data-stu-id="065c9-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="065c9-124">專案提供者可以選擇使用每個要求抓取專案，或將它們快取，以便立即可用。</span><span class="sxs-lookup"><span data-stu-id="065c9-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="065c9-125">`Virtualize`元件只能接受 **一個專案來源** 的參數，因此請勿嘗試同時使用專案提供者，並將集合指派給 `Items` 。</span><span class="sxs-lookup"><span data-stu-id="065c9-125">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="065c9-126">如果兩者都被指派， <xref:System.InvalidOperationException> 當元件的參數在執行時間設定時，就會擲回。</span><span class="sxs-lookup"><span data-stu-id="065c9-126">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="065c9-127">下列範例會從載入員工 `EmployeeService` ：</span><span class="sxs-lookup"><span data-stu-id="065c9-127">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="065c9-128">預留位置</span><span class="sxs-lookup"><span data-stu-id="065c9-128">Placeholder</span></span>

<span data-ttu-id="065c9-129">由於要求來自遠端資料源的專案可能需要一些時間，因此您可以選擇 () 呈現預留位置， `<Placeholder>...</Placeholder>` 直到專案資料可用為止：</span><span class="sxs-lookup"><span data-stu-id="065c9-129">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="065c9-130">項目大小</span><span class="sxs-lookup"><span data-stu-id="065c9-130">Item size</span></span>

<span data-ttu-id="065c9-131">您可以使用 (預設值來設定每個專案的大小（以圖元為單位）： `ItemSize` 50px) ：</span><span class="sxs-lookup"><span data-stu-id="065c9-131">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="065c9-132">Overscan 計數</span><span class="sxs-lookup"><span data-stu-id="065c9-132">Overscan count</span></span>

<span data-ttu-id="065c9-133">`OverscanCount` 決定在可見區域之前和之後轉譯的額外專案數目。</span><span class="sxs-lookup"><span data-stu-id="065c9-133">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="065c9-134">這項設定有助於減少滾動期間轉譯的頻率。</span><span class="sxs-lookup"><span data-stu-id="065c9-134">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="065c9-135">不過，較高的值會導致在頁面中轉譯的元素越多 (預設值： 3) ：</span><span class="sxs-lookup"><span data-stu-id="065c9-135">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="065c9-136">狀態變更</span><span class="sxs-lookup"><span data-stu-id="065c9-136">State changes</span></span>

<span data-ttu-id="065c9-137">對元件所轉譯的專案進行變更時 `Virtualize` ，請呼叫 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以強制重新評估和轉譯資料流程元件。</span><span class="sxs-lookup"><span data-stu-id="065c9-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
