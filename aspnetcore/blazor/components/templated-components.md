---
title: ASP.NET Core 樣板 Blazor 化元件
author: guardrex
description: 瞭解樣板化元件如何接受一或多個 UI 範本作為參數，然後將其當做元件轉譯邏輯的一部分來使用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/04/2021
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
uid: blazor/components/templated-components
ms.openlocfilehash: d58bd87ba2b39ad7e6d57560a200c69e7937ec0d
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957726"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="7839b-103">ASP.NET Core 樣板 Blazor 化元件</span><span class="sxs-lookup"><span data-stu-id="7839b-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="7839b-104">樣板化元件是接受一或多個 UI 範本作為參數的元件，可接著用來作為元件轉譯邏輯的一部分。</span><span class="sxs-lookup"><span data-stu-id="7839b-104">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="7839b-105">樣板化元件可讓您撰寫比一般元件更容易重複使用的較高層級元件。</span><span class="sxs-lookup"><span data-stu-id="7839b-105">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="7839b-106">其中一些範例包括：</span><span class="sxs-lookup"><span data-stu-id="7839b-106">A couple of examples include:</span></span>

* <span data-ttu-id="7839b-107">資料表元件，可讓使用者指定資料表標頭、資料列和頁尾的範本。</span><span class="sxs-lookup"><span data-stu-id="7839b-107">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="7839b-108">清單元件，可讓使用者指定用於轉譯清單中專案的範本。</span><span class="sxs-lookup"><span data-stu-id="7839b-108">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="7839b-109">樣板化元件是藉由指定一或多個類型或的元件參數來定義 <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。</span><span class="sxs-lookup"><span data-stu-id="7839b-109">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="7839b-110">轉譯片段表示要呈現的 UI 區段。</span><span class="sxs-lookup"><span data-stu-id="7839b-110">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="7839b-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> 採用可在叫用轉譯片段時指定的型別參數。</span><span class="sxs-lookup"><span data-stu-id="7839b-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="7839b-112">樣板化元件通常會以一般類型，如下列 `TableTemplate` 元件所示。</span><span class="sxs-lookup"><span data-stu-id="7839b-112">Often, templated components are generically typed, as the following `TableTemplate` component demonstrates.</span></span> <span data-ttu-id="7839b-113">`<T>`此範例中的泛型型別是用來轉譯 `IReadOnlyList<T>` 值，在此案例中是在顯示寵物資料表的元件中的一系列寵物資料列。</span><span class="sxs-lookup"><span data-stu-id="7839b-113">The generic type `<T>` in this example is used to render `IReadOnlyList<T>` values, which in this case is a series of pet rows in a component that displays a table of pets.</span></span>

<span data-ttu-id="7839b-114">`Shared/TableTemplate.razor`:</span><span class="sxs-lookup"><span data-stu-id="7839b-114">`Shared/TableTemplate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

<span data-ttu-id="7839b-115">使用樣板化元件時，可以使用符合參數名稱的子專案來指定範本參數。</span><span class="sxs-lookup"><span data-stu-id="7839b-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters.</span></span> <span data-ttu-id="7839b-116">在下列範例中， `<TableHeader>...</TableHeader>` 和會 `<RowTemplate>...<RowTemplate>` 提供 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 元件的 `TableHeader` 和的範本 `RowTemplate` `TableTemplate` 。</span><span class="sxs-lookup"><span data-stu-id="7839b-116">In the following example, `<TableHeader>...</TableHeader>` and `<RowTemplate>...<RowTemplate>` supply <xref:Microsoft.AspNetCore.Components.RenderFragment%601> templates for `TableHeader` and `RowTemplate` of the `TableTemplate` component.</span></span>

<span data-ttu-id="7839b-117">`Context`當您想要指定隱含子內容的內容參數名稱時，請在元件專案上指定屬性， (沒有任何換行的子項目) 。</span><span class="sxs-lookup"><span data-stu-id="7839b-117">Specify the `Context` attribute on the component element when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="7839b-118">在下列範例中， `Context` 屬性會出現在元素上， `TableTemplate` 並套用至所有 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 範本參數。</span><span class="sxs-lookup"><span data-stu-id="7839b-118">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all <xref:Microsoft.AspNetCore.Components.RenderFragment%601> template parameters.</span></span>

<span data-ttu-id="7839b-119">`Pages/Pets.razor`:</span><span class="sxs-lookup"><span data-stu-id="7839b-119">`Pages/Pets.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

<span data-ttu-id="7839b-120">或者，您也可以使用 `Context` 子項目上的屬性來變更參數名稱 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。</span><span class="sxs-lookup"><span data-stu-id="7839b-120">Alternatively, you can change the parameter name using the `Context` attribute on the <xref:Microsoft.AspNetCore.Components.RenderFragment%601> child element.</span></span> <span data-ttu-id="7839b-121">在下列範例中， `Context` 會設定為 on， `RowTemplate` 而不是 `TableTemplate` ：</span><span class="sxs-lookup"><span data-stu-id="7839b-121">In the following example, the `Context` is set on `RowTemplate` rather than `TableTemplate`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

<span data-ttu-id="7839b-122">型別的元件引數 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 具有名為的隱含參數 `context` ，可供使用。</span><span class="sxs-lookup"><span data-stu-id="7839b-122">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> have an implicit parameter named `context`, which can be used.</span></span> <span data-ttu-id="7839b-123">在下列範例中， `Context` 未設定。</span><span class="sxs-lookup"><span data-stu-id="7839b-123">In the following example, `Context` isn't set.</span></span> <span data-ttu-id="7839b-124">`@context.{PROPERTY}` 提供寵物值給範本，其中 `{PROPERTY}` 是一個 `Pet` 屬性：</span><span class="sxs-lookup"><span data-stu-id="7839b-124">`@context.{PROPERTY}` supplies pet values to the template, where `{PROPERTY}` is a `Pet` property:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

<span data-ttu-id="7839b-125">使用泛型型別的元件時，會盡可能推斷類型參數。</span><span class="sxs-lookup"><span data-stu-id="7839b-125">When using generic-typed components, the type parameter is inferred if possible.</span></span> <span data-ttu-id="7839b-126">不過，您可以使用具有與類型參數相符之名稱的屬性（ `TItem` 在上述範例中）明確指定類型：</span><span class="sxs-lookup"><span data-stu-id="7839b-126">However, you can explicitly specify the type with an attribute that has a name matching the type parameter, which is `TItem` in the preceding example:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="infer-generic-types-based-on-ancestor-components"></a><span data-ttu-id="7839b-127">根據上階元件推斷泛型型別</span><span class="sxs-lookup"><span data-stu-id="7839b-127">Infer generic types based on ancestor components</span></span>

::: moniker range=">= aspnetcore-6.0"

<span data-ttu-id="7839b-128">上階元件可以使用屬性，以名稱將類型參數串聯至子系 `CascadingTypeParameter` 。</span><span class="sxs-lookup"><span data-stu-id="7839b-128">An ancestor component can cascade a type parameter by name to descendants using the `CascadingTypeParameter` attribute.</span></span> <span data-ttu-id="7839b-129">這個屬性可讓泛型型別推斷以具有相同名稱之類型參數的子系自動使用指定的型別參數。</span><span class="sxs-lookup"><span data-stu-id="7839b-129">This attribute allows a generic type inference to use the specified type parameter automatically with descendants that have a type parameter with the same name.</span></span>

<span data-ttu-id="7839b-130">例如，下列元件會 `Chart` 接收股票價格資料，並將名為的泛型型別參數以其子元件的形式進行級聯 `TLineData` 。</span><span class="sxs-lookup"><span data-stu-id="7839b-130">For example, the following `Chart` component receives stock price data and cascades a generic type parameter named `TLineData` to its descendent components.</span></span>

<span data-ttu-id="7839b-131">`Shared/Chart.razor`:</span><span class="sxs-lookup"><span data-stu-id="7839b-131">`Shared/Chart.razor`:</span></span>

```razor
@typeparam TLineData
@attribute [CascadingTypeParameter(nameof(TLineData))]

...

@code {
    [Parameter]
    public IEnumerable<TLineData> Data { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }
}
```

<span data-ttu-id="7839b-132">`Shared/Line.razor`:</span><span class="sxs-lookup"><span data-stu-id="7839b-132">`Shared/Line.razor`:</span></span>

```razor
@typeparam TLineData

...

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public decimal Value { get; set; }

    [Parameter]
    public IEnumerable<TLineData> Data { get; set; }
}
```

<span data-ttu-id="7839b-133">`Chart`使用元件時，不會 `TLineData` 針對圖表的每個 `Line` 元件指定。</span><span class="sxs-lookup"><span data-stu-id="7839b-133">When the `Chart` component is used, `TLineData` isn't specified for each `Line` component of the chart.</span></span>

<span data-ttu-id="7839b-134">`Pages/StockPriceHistory.razor`:</span><span class="sxs-lookup"><span data-stu-id="7839b-134">`Pages/StockPriceHistory.razor`:</span></span>

```razor
@page "/stock-price-history"

<Chart Data="stockPriceHistory.GroupBy(x => x.Date)">
    <Line Title="Open" Value="day => day.Values.First()" />
    <Line Title="High" Value="day => day.Values.Max()" />
    <Line Title="Low" Value="day => day.Values.Min()" />
    <Line Title="Close" Value="day => day.Values.Last()" />
</Chart>
```

> [!NOTE]
> <span data-ttu-id="7839b-135">RazorVisual Studio Code 中的支援尚未更新為支援這項功能，因此您可能會收到錯誤的錯誤，即使專案正確建立也是一樣。</span><span class="sxs-lookup"><span data-stu-id="7839b-135">The Razor support in Visual Studio Code hasn't been updated to support this feature, so you may receive incorrect errors even though the project correctly builds.</span></span> <span data-ttu-id="7839b-136">即將推出的工具版本將會解決此問題。</span><span class="sxs-lookup"><span data-stu-id="7839b-136">This will be addressed in an upcoming tooling release.</span></span>

<span data-ttu-id="7839b-137">藉由將 `@attribute [CascadingTypeParameter(...)]` 指定的泛型型別引數加入至元件，下階會自動使用指定的泛型型別引數：</span><span class="sxs-lookup"><span data-stu-id="7839b-137">By adding `@attribute [CascadingTypeParameter(...)]` to a component, the specified generic type argument is automatically used by descendants that:</span></span>

* <span data-ttu-id="7839b-138">會在相同檔中做為元件的子內容進行嵌套 `.razor` 。</span><span class="sxs-lookup"><span data-stu-id="7839b-138">Are nested as child content for the component in the same `.razor` document.</span></span>
* <span data-ttu-id="7839b-139">也請 [`@typeparam`](xref:mvc/views/razor#typeparam) 使用完全相同的名稱來宣告。</span><span class="sxs-lookup"><span data-stu-id="7839b-139">Also declare a [`@typeparam`](xref:mvc/views/razor#typeparam) with the exact same name.</span></span>
* <span data-ttu-id="7839b-140">請不要為類型參數提供或推斷另一個值。</span><span class="sxs-lookup"><span data-stu-id="7839b-140">Don't have another value supplied or inferred for the type parameter.</span></span> <span data-ttu-id="7839b-141">如果有提供或推斷另一個值，其優先順序會高於串聯的泛型型別。</span><span class="sxs-lookup"><span data-stu-id="7839b-141">If another value is supplied or inferred, it takes precedence over the cascaded generic type.</span></span>

<span data-ttu-id="7839b-142">當接收串聯型別參數時，元件會從最接近具有相符名稱的上階取得參數值 `CascadingTypeParameter` 。</span><span class="sxs-lookup"><span data-stu-id="7839b-142">When receiving a cascaded type parameter, components obtain the parameter value from the closest ancestor that has a `CascadingTypeParameter` with a matching name.</span></span> <span data-ttu-id="7839b-143">在特定子樹中，會覆寫串聯的泛型型別參數。</span><span class="sxs-lookup"><span data-stu-id="7839b-143">Cascaded generic type parameters are overridden within a particular subtree.</span></span>

<span data-ttu-id="7839b-144">比對只會依名稱執行。</span><span class="sxs-lookup"><span data-stu-id="7839b-144">Matching is only performed by name.</span></span> <span data-ttu-id="7839b-145">因此，我們建議您避免使用泛型名稱進行串聯的泛型型別參數，例如 `T` 或 `TItem` 。</span><span class="sxs-lookup"><span data-stu-id="7839b-145">Therefore, we recommend avoiding a cascaded generic type parameter with a generic name, for example `T` or `TItem`.</span></span> <span data-ttu-id="7839b-146">如果開發人員選擇串聯型別參數，則會隱含地暗示它的名稱是唯一的，而不會與不相關元件中的其他串聯型別參數衝突。</span><span class="sxs-lookup"><span data-stu-id="7839b-146">If a developer opts into cascading a type parameter, they're implicitly promising that its name is unique enough not to clash with other cascaded type parameters from unrelated components.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-6.0"

> [!NOTE]
> <span data-ttu-id="7839b-147">ASP.NET Core 6.0 或更新版本支援推斷的泛型型別。</span><span class="sxs-lookup"><span data-stu-id="7839b-147">Inferred generic types are supported in ASP.NET Core 6.0 or later.</span></span> <span data-ttu-id="7839b-148">如需詳細資訊，請參閱本文的版本，而不是 ASP.NET Core 5.0。</span><span class="sxs-lookup"><span data-stu-id="7839b-148">For more information, see a version of this article later than ASP.NET Core 5.0.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7839b-149">其他資源</span><span class="sxs-lookup"><span data-stu-id="7839b-149">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
