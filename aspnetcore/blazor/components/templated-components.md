---
title: ASP.NET 核心樣板 Blazor 化元件
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
ms.openlocfilehash: 6c94218f3808baca18f23a53688bafdd6354e760
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589474"
---
# <a name="aspnet-core-blazor-templated-components"></a>ASP.NET 核心樣板 Blazor 化元件

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

## <a name="generic-type-constraints"></a>泛型型別條件約束

> [!NOTE]
> 未來的版本將支援泛型型別條件約束。 如需詳細資訊，請參閱 [允許泛型型別條件約束 (dotnet/aspnetcore #8433) ](https://github.com/dotnet/aspnetcore/issues/8433)。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
