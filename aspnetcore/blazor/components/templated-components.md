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
# <a name="aspnet-core-blazor-templated-components"></a>ASP.NET Core 樣板 Blazor 化元件

樣板化元件是接受一或多個 UI 範本作為參數的元件，可接著用來作為元件轉譯邏輯的一部分。 樣板化元件可讓您撰寫比一般元件更容易重複使用的較高層級元件。 其中一些範例包括：

* 資料表元件，可讓使用者指定資料表標頭、資料列和頁尾的範本。
* 清單元件，可讓使用者指定用於轉譯清單中專案的範本。

樣板化元件是藉由指定一或多個類型或的元件參數來定義 <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。 轉譯片段表示要呈現的 UI 區段。 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 採用可在叫用轉譯片段時指定的型別參數。

樣板化元件通常會以一般類型，如下列 `TableTemplate` 元件所示。 `<T>`此範例中的泛型型別是用來轉譯 `IReadOnlyList<T>` 值，在此案例中是在顯示寵物資料表的元件中的一系列寵物資料列。

`Shared/TableTemplate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

使用樣板化元件時，可以使用符合參數名稱的子專案來指定範本參數。 在下列範例中， `<TableHeader>...</TableHeader>` 和會 `<RowTemplate>...<RowTemplate>` 提供 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 元件的 `TableHeader` 和的範本 `RowTemplate` `TableTemplate` 。

`Context`當您想要指定隱含子內容的內容參數名稱時，請在元件專案上指定屬性， (沒有任何換行的子項目) 。 在下列範例中， `Context` 屬性會出現在元素上， `TableTemplate` 並套用至所有 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 範本參數。

`Pages/Pets.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

或者，您也可以使用 `Context` 子項目上的屬性來變更參數名稱 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。 在下列範例中， `Context` 會設定為 on， `RowTemplate` 而不是 `TableTemplate` ：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

型別的元件引數 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 具有名為的隱含參數 `context` ，可供使用。 在下列範例中， `Context` 未設定。 `@context.{PROPERTY}` 提供寵物值給範本，其中 `{PROPERTY}` 是一個 `Pet` 屬性：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

使用泛型型別的元件時，會盡可能推斷類型參數。 不過，您可以使用具有與類型參數相符之名稱的屬性（ `TItem` 在上述範例中）明確指定類型：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="infer-generic-types-based-on-ancestor-components"></a>根據上階元件推斷泛型型別

::: moniker range=">= aspnetcore-6.0"

上階元件可以使用屬性，以名稱將類型參數串聯至子系 `CascadingTypeParameter` 。 這個屬性可讓泛型型別推斷以具有相同名稱之類型參數的子系自動使用指定的型別參數。

例如，下列元件會 `Chart` 接收股票價格資料，並將名為的泛型型別參數以其子元件的形式進行級聯 `TLineData` 。

`Shared/Chart.razor`:

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

`Shared/Line.razor`:

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

`Chart`使用元件時，不會 `TLineData` 針對圖表的每個 `Line` 元件指定。

`Pages/StockPriceHistory.razor`:

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
> RazorVisual Studio Code 中的支援尚未更新為支援這項功能，因此您可能會收到錯誤的錯誤，即使專案正確建立也是一樣。 即將推出的工具版本將會解決此問題。

藉由將 `@attribute [CascadingTypeParameter(...)]` 指定的泛型型別引數加入至元件，下階會自動使用指定的泛型型別引數：

* 會在相同檔中做為元件的子內容進行嵌套 `.razor` 。
* 也請 [`@typeparam`](xref:mvc/views/razor#typeparam) 使用完全相同的名稱來宣告。
* 請不要為類型參數提供或推斷另一個值。 如果有提供或推斷另一個值，其優先順序會高於串聯的泛型型別。

當接收串聯型別參數時，元件會從最接近具有相符名稱的上階取得參數值 `CascadingTypeParameter` 。 在特定子樹中，會覆寫串聯的泛型型別參數。

比對只會依名稱執行。 因此，我們建議您避免使用泛型名稱進行串聯的泛型型別參數，例如 `T` 或 `TItem` 。 如果開發人員選擇串聯型別參數，則會隱含地暗示它的名稱是唯一的，而不會與不相關元件中的其他串聯型別參數衝突。

::: moniker-end

::: moniker range="< aspnetcore-6.0"

> [!NOTE]
> ASP.NET Core 6.0 或更新版本支援推斷的泛型型別。 如需詳細資訊，請參閱本文的版本，而不是 ASP.NET Core 5.0。

::: moniker-end

## <a name="additional-resources"></a>其他資源

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
