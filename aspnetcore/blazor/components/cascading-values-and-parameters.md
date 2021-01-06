---
title: ASP.NET Core Blazor 級聯值和參數
author: guardrex
description: 瞭解如何將資料從上階元件傳送到附屬元件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 56d70cea50a3a913b4483f6ea488438269aa58fe
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507976"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor 級聯值和參數

依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

在某些情況下，使用 [元件參數](xref:blazor/components/index#component-parameters)將資料從上階元件傳送至子元件很不方便，尤其是在有數個元件層時。 串聯值和參數藉由提供一個便利的方式，讓祖系元件為其所有的子元件提供值，來解決這個問題。 串聯值和參數也會提供元件的座標方法。

### <a name="theme-example"></a>主題範例

在來自範例應用程式的下列範例中， `ThemeInfo` 類別會指定要在元件階層中流動的主題資訊，讓應用程式的特定部分中的所有按鈕共用相同的樣式。

`UIThemeClasses/ThemeInfo.cs`:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

上階的元件可以使用級聯的值元件來提供階層式值。 <xref:Microsoft.AspNetCore.Components.CascadingValue%601>元件會包裝元件階層的子樹，並提供單一值給該子樹內的所有元件。

例如，範例應用程式 `ThemeInfo` 會在其中一個應用程式的版面配置中，指定主題資訊 () ，做為組成屬性之版面配置主體的所有元件的串聯參數 `@Body` 。 `ButtonClass` 會 `btn-success` 在版面配置元件中指派的值。 任何子代元件都可以透過串聯物件取用這個屬性 `ThemeInfo` 。

`CascadingValuesParametersLayout` 元件：

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

為了利用串聯值，元件會使用屬性來宣告串聯參數 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 。 串聯值會依類型系結至串聯參數。

在範例應用程式中，元件會將串聯值系結 `CascadingValuesParametersTheme` `ThemeInfo` 至串聯參數。 參數是用來設定元件所顯示的其中一個按鈕的 CSS 類別。

`CascadingValuesParametersTheme` 元件：

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

若要在相同的子樹中串聯多個相同類型的值，請為 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 每個 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 元件和其對應的屬性提供唯一的字串 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 。 在下列範例中，兩個 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 元件 `MyCascadingType` 依名稱來串聯不同的實例：

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

在子系元件中，串聯的參數會依名稱從上階元件的對應串聯值接收其值：

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet 範例

串聯參數也可讓元件在元件階層之間共同作業。 例如，請考慮 `TabSet` 範例應用程式中的下列範例。

範例應用程式具有可執行索引標籤 `ITab` 的介面：

[!code-csharp[](../common/samples/5.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet`元件使用 `TabSet` 元件，其中包含數個 `Tab` 元件：

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```

子 `Tab` 元件不會明確地以參數形式傳遞至 `TabSet` 。 相反地，子 `Tab` 元件是的子內容的一部分 `TabSet` 。 但是， `TabSet` 仍然需要知道每個元件， `Tab` 才能轉譯標頭和使用中的索引標籤。若要在不需要額外程式碼的情況下啟用此協調， `TabSet` 元件 *可以提供本身* 的串聯值，然後由子 `Tab` 元件挑選。

`TabSet` 元件：

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TabSet.razor)]

這些子代 `Tab` 元件會將包含的 `TabSet` 視為串聯參數，因此 `Tab` 元件會將自己加入至 `TabSet` ，並在哪一個索引標籤為作用中協調。

`Tab` 元件：

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/Tab.razor)]
