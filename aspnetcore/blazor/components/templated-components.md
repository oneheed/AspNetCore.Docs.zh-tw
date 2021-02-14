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
ms.openlocfilehash: 579cabd9e6b7141ec6af4c6e221b805272a2fe40
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280036"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="a1620-103">ASP.NET Core 樣板 Blazor 化元件</span><span class="sxs-lookup"><span data-stu-id="a1620-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="a1620-104">樣板化元件是接受一或多個 UI 範本作為參數的元件，可接著用來作為元件轉譯邏輯的一部分。</span><span class="sxs-lookup"><span data-stu-id="a1620-104">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="a1620-105">樣板化元件可讓您撰寫比一般元件更容易重複使用的較高層級元件。</span><span class="sxs-lookup"><span data-stu-id="a1620-105">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="a1620-106">其中一些範例包括：</span><span class="sxs-lookup"><span data-stu-id="a1620-106">A couple of examples include:</span></span>

* <span data-ttu-id="a1620-107">資料表元件，可讓使用者指定資料表標頭、資料列和頁尾的範本。</span><span class="sxs-lookup"><span data-stu-id="a1620-107">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="a1620-108">清單元件，可讓使用者指定用於轉譯清單中專案的範本。</span><span class="sxs-lookup"><span data-stu-id="a1620-108">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="a1620-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a1620-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="a1620-110">範本參數</span><span class="sxs-lookup"><span data-stu-id="a1620-110">Template parameters</span></span>

<span data-ttu-id="a1620-111">樣板化元件是藉由指定一或多個類型或的元件參數來定義 <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。</span><span class="sxs-lookup"><span data-stu-id="a1620-111">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="a1620-112">轉譯片段表示要呈現的 UI 區段。</span><span class="sxs-lookup"><span data-stu-id="a1620-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="a1620-113"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> 採用可在叫用轉譯片段時指定的型別參數。</span><span class="sxs-lookup"><span data-stu-id="a1620-113"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="a1620-114">`TableTemplate` 元件 (`TableTemplate.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="a1620-114">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="a1620-115">使用樣板化元件時，您可以使用符合參數名稱的子專案來指定範本參數 (`TableHeader` ， `RowTemplate` 在下列範例中) ：</span><span class="sxs-lookup"><span data-stu-id="a1620-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="a1620-116">未來的版本將支援泛型型別條件約束。</span><span class="sxs-lookup"><span data-stu-id="a1620-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="a1620-117">如需詳細資訊，請參閱 [允許泛型型別條件約束 (dotnet/aspnetcore #8433) ](https://github.com/dotnet/aspnetcore/issues/8433)。</span><span class="sxs-lookup"><span data-stu-id="a1620-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="a1620-118">範本內容參數</span><span class="sxs-lookup"><span data-stu-id="a1620-118">Template context parameters</span></span>

<span data-ttu-id="a1620-119">傳遞做為專案之型別的元件引數 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 有一個名為 `context` (的隱含參數，例如，上述程式碼範例中 `@context.PetId`) ，但您可以使用 `Context` 子項目上的屬性來變更參數名稱。</span><span class="sxs-lookup"><span data-stu-id="a1620-119">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="a1620-120">在下列範例中， `RowTemplate` 元素的 `Context` 屬性會指定 `pet` 參數：</span><span class="sxs-lookup"><span data-stu-id="a1620-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="a1620-121">或者，您也可以在 `Context` component 元素上指定屬性。</span><span class="sxs-lookup"><span data-stu-id="a1620-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="a1620-122">指定的 `Context` 屬性會套用至所有指定的範本參數。</span><span class="sxs-lookup"><span data-stu-id="a1620-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="a1620-123">當您想要指定隱含子內容的內容參數名稱時，這會很有用， (沒有任何換行的子專案) 。</span><span class="sxs-lookup"><span data-stu-id="a1620-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="a1620-124">在下列範例中， `Context` 屬性會出現在元素上， `TableTemplate` 並套用至所有範本參數：</span><span class="sxs-lookup"><span data-stu-id="a1620-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="a1620-125">泛型型別的元件</span><span class="sxs-lookup"><span data-stu-id="a1620-125">Generic-typed components</span></span>

<span data-ttu-id="a1620-126">樣板化元件通常會以一般類型。</span><span class="sxs-lookup"><span data-stu-id="a1620-126">Templated components are often generically typed.</span></span> <span data-ttu-id="a1620-127">例如， `ListViewTemplate` () 的泛型元件 `ListViewTemplate.razor` 可以用來呈現 `IEnumerable<T>` 值。</span><span class="sxs-lookup"><span data-stu-id="a1620-127">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="a1620-128">若要定義泛型元件，請使用指示詞 [`@typeparam`](xref:mvc/views/razor#typeparam) 來指定類型參數：</span><span class="sxs-lookup"><span data-stu-id="a1620-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="a1620-129">使用泛型型別的元件時，會盡可能推斷類型參數：</span><span class="sxs-lookup"><span data-stu-id="a1620-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

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

<span data-ttu-id="a1620-130">否則，必須使用符合型別參數名稱的屬性來明確指定型別參數。</span><span class="sxs-lookup"><span data-stu-id="a1620-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="a1620-131">在下列範例中， `TItem="Pet"` 指定型別：</span><span class="sxs-lookup"><span data-stu-id="a1620-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a1620-132">其他資源</span><span class="sxs-lookup"><span data-stu-id="a1620-132">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
