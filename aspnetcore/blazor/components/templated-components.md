---
title: ASP.NET Core 樣板 Blazor 化元件
author: guardrex
description: 瞭解樣板化元件如何接受一或多個 UI 範本作為參數，然後將其當做元件轉譯邏輯的一部分來使用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
ms.openlocfilehash: f818a0b7f1ba6d4dd6affeeba785c5e288568dd8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507950"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a><span data-ttu-id="79c01-103">ASP.NET Core 樣板 Blazor 化元件</span><span class="sxs-lookup"><span data-stu-id="79c01-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="79c01-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="79c01-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="79c01-105">樣板化元件是接受一或多個 UI 範本作為參數的元件，可接著用來作為元件轉譯邏輯的一部分。</span><span class="sxs-lookup"><span data-stu-id="79c01-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="79c01-106">樣板化元件可讓您撰寫比一般元件更容易重複使用的較高層級元件。</span><span class="sxs-lookup"><span data-stu-id="79c01-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="79c01-107">其中一些範例包括：</span><span class="sxs-lookup"><span data-stu-id="79c01-107">A couple of examples include:</span></span>

* <span data-ttu-id="79c01-108">資料表元件，可讓使用者指定資料表標頭、資料列和頁尾的範本。</span><span class="sxs-lookup"><span data-stu-id="79c01-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="79c01-109">清單元件，可讓使用者指定用於轉譯清單中專案的範本。</span><span class="sxs-lookup"><span data-stu-id="79c01-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="79c01-110">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="79c01-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="79c01-111">範本參數</span><span class="sxs-lookup"><span data-stu-id="79c01-111">Template parameters</span></span>

<span data-ttu-id="79c01-112">樣板化元件是藉由指定一或多個類型或的元件參數來定義 <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。</span><span class="sxs-lookup"><span data-stu-id="79c01-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="79c01-113">轉譯片段表示要呈現的 UI 區段。</span><span class="sxs-lookup"><span data-stu-id="79c01-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="79c01-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> 採用可在叫用轉譯片段時指定的型別參數。</span><span class="sxs-lookup"><span data-stu-id="79c01-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="79c01-115">`TableTemplate` 元件 (`TableTemplate.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="79c01-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="79c01-116">使用樣板化元件時，您可以使用符合參數名稱的子專案來指定範本參數 (`TableHeader` ， `RowTemplate` 在下列範例中) ：</span><span class="sxs-lookup"><span data-stu-id="79c01-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="79c01-117">未來的版本將支援泛型型別條件約束。</span><span class="sxs-lookup"><span data-stu-id="79c01-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="79c01-118">如需詳細資訊，請參閱 [允許泛型型別條件約束 (dotnet/aspnetcore #8433) ](https://github.com/dotnet/aspnetcore/issues/8433)。</span><span class="sxs-lookup"><span data-stu-id="79c01-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="79c01-119">範本內容參數</span><span class="sxs-lookup"><span data-stu-id="79c01-119">Template context parameters</span></span>

<span data-ttu-id="79c01-120">傳遞做為專案之型別的元件引數 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 有一個名為 `context` (的隱含參數，例如，上述程式碼範例中 `@context.PetId`) ，但您可以使用 `Context` 子項目上的屬性來變更參數名稱。</span><span class="sxs-lookup"><span data-stu-id="79c01-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="79c01-121">在下列範例中， `RowTemplate` 元素的 `Context` 屬性會指定 `pet` 參數：</span><span class="sxs-lookup"><span data-stu-id="79c01-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

<span data-ttu-id="79c01-122">或者，您也可以在 `Context` component 元素上指定屬性。</span><span class="sxs-lookup"><span data-stu-id="79c01-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="79c01-123">指定的 `Context` 屬性會套用至所有指定的範本參數。</span><span class="sxs-lookup"><span data-stu-id="79c01-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="79c01-124">當您想要指定隱含子內容的內容參數名稱時，這會很有用， (沒有任何換行的子專案) 。</span><span class="sxs-lookup"><span data-stu-id="79c01-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="79c01-125">在下列範例中， `Context` 屬性會出現在元素上， `TableTemplate` 並套用至所有範本參數：</span><span class="sxs-lookup"><span data-stu-id="79c01-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

## <a name="generic-typed-components"></a><span data-ttu-id="79c01-126">泛型型別的元件</span><span class="sxs-lookup"><span data-stu-id="79c01-126">Generic-typed components</span></span>

<span data-ttu-id="79c01-127">樣板化元件通常會以一般類型。</span><span class="sxs-lookup"><span data-stu-id="79c01-127">Templated components are often generically typed.</span></span> <span data-ttu-id="79c01-128">例如， `ListViewTemplate` () 的泛型元件 `ListViewTemplate.razor` 可以用來呈現 `IEnumerable<T>` 值。</span><span class="sxs-lookup"><span data-stu-id="79c01-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="79c01-129">若要定義泛型元件，請使用指示詞 [`@typeparam`](xref:mvc/views/razor#typeparam) 來指定類型參數：</span><span class="sxs-lookup"><span data-stu-id="79c01-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="79c01-130">使用泛型型別的元件時，會盡可能推斷類型參數：</span><span class="sxs-lookup"><span data-stu-id="79c01-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="79c01-131">否則，必須使用符合型別參數名稱的屬性來明確指定型別參數。</span><span class="sxs-lookup"><span data-stu-id="79c01-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="79c01-132">在下列範例中， `TItem="Pet"` 指定型別：</span><span class="sxs-lookup"><span data-stu-id="79c01-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```
