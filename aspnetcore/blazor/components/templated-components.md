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
# <a name="aspnet-core-blazor-templated-components"></a>ASP.NET Core 樣板 Blazor 化元件

樣板化元件是接受一或多個 UI 範本作為參數的元件，可接著用來作為元件轉譯邏輯的一部分。 樣板化元件可讓您撰寫比一般元件更容易重複使用的較高層級元件。 其中一些範例包括：

* 資料表元件，可讓使用者指定資料表標頭、資料列和頁尾的範本。
* 清單元件，可讓使用者指定用於轉譯清單中專案的範本。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="template-parameters"></a>範本參數

樣板化元件是藉由指定一或多個類型或的元件參數來定義 <xref:Microsoft.AspNetCore.Components.RenderFragment> <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 。 轉譯片段表示要呈現的 UI 區段。 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 採用可在叫用轉譯片段時指定的型別參數。

`TableTemplate` 元件 (`TableTemplate.razor`) ：

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

使用樣板化元件時，您可以使用符合參數名稱的子專案來指定範本參數 (`TableHeader` ， `RowTemplate` 在下列範例中) ：

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
> 未來的版本將支援泛型型別條件約束。 如需詳細資訊，請參閱 [允許泛型型別條件約束 (dotnet/aspnetcore #8433) ](https://github.com/dotnet/aspnetcore/issues/8433)。

## <a name="template-context-parameters"></a>範本內容參數

傳遞做為專案之型別的元件引數 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 有一個名為 `context` (的隱含參數，例如，上述程式碼範例中 `@context.PetId`) ，但您可以使用 `Context` 子項目上的屬性來變更參數名稱。 在下列範例中， `RowTemplate` 元素的 `Context` 屬性會指定 `pet` 參數：

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

或者，您也可以在 `Context` component 元素上指定屬性。 指定的 `Context` 屬性會套用至所有指定的範本參數。 當您想要指定隱含子內容的內容參數名稱時，這會很有用， (沒有任何換行的子專案) 。 在下列範例中， `Context` 屬性會出現在元素上， `TableTemplate` 並套用至所有範本參數：

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

## <a name="generic-typed-components"></a>泛型型別的元件

樣板化元件通常會以一般類型。 例如， `ListViewTemplate` () 的泛型元件 `ListViewTemplate.razor` 可以用來呈現 `IEnumerable<T>` 值。 若要定義泛型元件，請使用指示詞 [`@typeparam`](xref:mvc/views/razor#typeparam) 來指定類型參數：

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

使用泛型型別的元件時，會盡可能推斷類型參數：

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

否則，必須使用符合型別參數名稱的屬性來明確指定型別參數。 在下列範例中， `TItem="Pet"` 指定型別：

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

## <a name="additional-resources"></a>其他資源

* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>
