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
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="0e271-103">ASP.NET 核心樣板 Blazor 化元件</span><span class="sxs-lookup"><span data-stu-id="0e271-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="0e271-104">樣板化元件是接受一或多個 UI 範本作為參數的元件，可接著用來作為元件轉譯邏輯的一部分。</span><span class="sxs-lookup"><span data-stu-id="0e271-104">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="0e271-105">樣板化元件可讓您撰寫比一般元件更容易重複使用的較高層級元件。</span><span class="sxs-lookup"><span data-stu-id="0e271-105">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="0e271-106">其中一些範例包括：</span><span class="sxs-lookup"><span data-stu-id="0e271-106">A couple of examples include:</span></span>

* <span data-ttu-id="0e271-107">資料表元件，可讓使用者指定資料表標頭、資料列和頁尾的範本。</span><span class="sxs-lookup"><span data-stu-id="0e271-107">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="0e271-108">清單元件，可讓使用者指定用於轉譯清單中專案的範本。</span><span class="sxs-lookup"><span data-stu-id="0e271-108">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="0e271-109">樣板化元件是藉由指定一或多個類型或的元件參數來定義 <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。</span><span class="sxs-lookup"><span data-stu-id="0e271-109">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="0e271-110">轉譯片段表示要呈現的 UI 區段。</span><span class="sxs-lookup"><span data-stu-id="0e271-110">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="0e271-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> 採用可在叫用轉譯片段時指定的型別參數。</span><span class="sxs-lookup"><span data-stu-id="0e271-111"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="0e271-112">樣板化元件通常會以一般類型，如下列 `TableTemplate` 元件所示。</span><span class="sxs-lookup"><span data-stu-id="0e271-112">Often, templated components are generically typed, as the following `TableTemplate` component demonstrates.</span></span> <span data-ttu-id="0e271-113">`<T>`此範例中的泛型型別是用來轉譯 `IReadOnlyList<T>` 值，在此案例中是在顯示寵物資料表的元件中的一系列寵物資料列。</span><span class="sxs-lookup"><span data-stu-id="0e271-113">The generic type `<T>` in this example is used to render `IReadOnlyList<T>` values, which in this case is a series of pet rows in a component that displays a table of pets.</span></span>

<span data-ttu-id="0e271-114">`Shared/TableTemplate.razor`:</span><span class="sxs-lookup"><span data-stu-id="0e271-114">`Shared/TableTemplate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/templated-components/TableTemplate.razor)]

::: moniker-end

<span data-ttu-id="0e271-115">使用樣板化元件時，可以使用符合參數名稱的子專案來指定範本參數。</span><span class="sxs-lookup"><span data-stu-id="0e271-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters.</span></span> <span data-ttu-id="0e271-116">在下列範例中， `<TableHeader>...</TableHeader>` 和會 `<RowTemplate>...<RowTemplate>` 提供 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 元件的 `TableHeader` 和的範本 `RowTemplate` `TableTemplate` 。</span><span class="sxs-lookup"><span data-stu-id="0e271-116">In the following example, `<TableHeader>...</TableHeader>` and `<RowTemplate>...<RowTemplate>` supply <xref:Microsoft.AspNetCore.Components.RenderFragment%601> templates for `TableHeader` and `RowTemplate` of the `TableTemplate` component.</span></span>

<span data-ttu-id="0e271-117">`Context`當您想要指定隱含子內容的內容參數名稱時，請在元件專案上指定屬性， (沒有任何換行的子項目) 。</span><span class="sxs-lookup"><span data-stu-id="0e271-117">Specify the `Context` attribute on the component element when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="0e271-118">在下列範例中， `Context` 屬性會出現在元素上， `TableTemplate` 並套用至所有 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 範本參數。</span><span class="sxs-lookup"><span data-stu-id="0e271-118">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all <xref:Microsoft.AspNetCore.Components.RenderFragment%601> template parameters.</span></span>

<span data-ttu-id="0e271-119">`Pages/Pets.razor`:</span><span class="sxs-lookup"><span data-stu-id="0e271-119">`Pages/Pets.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets1.razor)]

::: moniker-end

<span data-ttu-id="0e271-120">或者，您也可以使用 `Context` 子項目上的屬性來變更參數名稱 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。</span><span class="sxs-lookup"><span data-stu-id="0e271-120">Alternatively, you can change the parameter name using the `Context` attribute on the <xref:Microsoft.AspNetCore.Components.RenderFragment%601> child element.</span></span> <span data-ttu-id="0e271-121">在下列範例中， `Context` 會設定為 on， `RowTemplate` 而不是 `TableTemplate` ：</span><span class="sxs-lookup"><span data-stu-id="0e271-121">In the following example, the `Context` is set on `RowTemplate` rather than `TableTemplate`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets2.razor?name=snippet&highlight=6)]

::: moniker-end

<span data-ttu-id="0e271-122">型別的元件引數 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 具有名為的隱含參數 `context` ，可供使用。</span><span class="sxs-lookup"><span data-stu-id="0e271-122">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> have an implicit parameter named `context`, which can be used.</span></span> <span data-ttu-id="0e271-123">在下列範例中， `Context` 未設定。</span><span class="sxs-lookup"><span data-stu-id="0e271-123">In the following example, `Context` isn't set.</span></span> <span data-ttu-id="0e271-124">`@context.{PROPERTY}` 提供寵物值給範本，其中 `{PROPERTY}` 是一個 `Pet` 屬性：</span><span class="sxs-lookup"><span data-stu-id="0e271-124">`@context.{PROPERTY}` supplies pet values to the template, where `{PROPERTY}` is a `Pet` property:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets3.razor?name=snippet&highlight=7-8)]

::: moniker-end

<span data-ttu-id="0e271-125">使用泛型型別的元件時，會盡可能推斷類型參數。</span><span class="sxs-lookup"><span data-stu-id="0e271-125">When using generic-typed components, the type parameter is inferred if possible.</span></span> <span data-ttu-id="0e271-126">不過，您可以使用具有與類型參數相符之名稱的屬性（ `TItem` 在上述範例中）明確指定類型：</span><span class="sxs-lookup"><span data-stu-id="0e271-126">However, you can explicitly specify the type with an attribute that has a name matching the type parameter, which is `TItem` in the preceding example:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/templated-components/Pets4.razor?name=snippet&highlight=1)]

::: moniker-end

## <a name="generic-type-constraints"></a><span data-ttu-id="0e271-127">泛型型別條件約束</span><span class="sxs-lookup"><span data-stu-id="0e271-127">Generic type constraints</span></span>

> [!NOTE]
> <span data-ttu-id="0e271-128">未來的版本將支援泛型型別條件約束。</span><span class="sxs-lookup"><span data-stu-id="0e271-128">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="0e271-129">如需詳細資訊，請參閱 [允許泛型型別條件約束 (dotnet/aspnetcore #8433) ](https://github.com/dotnet/aspnetcore/issues/8433)。</span><span class="sxs-lookup"><span data-stu-id="0e271-129">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e271-130">其他資源</span><span class="sxs-lookup"><span data-stu-id="0e271-130">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
