---
title: ASP.NET Core Blazor 級聯值和參數
author: guardrex
description: 瞭解如何將資料從上階元件傳送到附屬元件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280241"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor 級聯值和參數

串聯 *值和參數* 提供 convienent 的方式，可將元件階層的資料從上階元件往下傳送至任意數目的 decendent 元件。 不同于 [元件參數](xref:blazor/components/index#component-parameters)，串聯值和參數不需要針對取用資料的每個子代元件進行屬性指派。 串聯值和參數也允許元件跨元件階層彼此協調。

## <a name="cascadingvalue-component"></a>`CascadingValue` 元件

上階元件會使用架構的元件提供階層式值 Blazor [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) ，以包裝元件階層的子樹，並提供單一值給其子樹內的所有元件。

下列範例將示範如何在版面配置元件的元件階層中，將主題資訊的流程向下示範，以提供子元件中按鈕的 CSS 樣式類別。

下列 `ThemeInfo` c # 類別放在名為的資料夾中 `UIThemeClasses` ，並指定主題資訊。

> [!NOTE]
> 針對本節中的範例，應用程式的命名空間為 `BlazorSample` 。 當您在自己的範例應用程式中試驗程式碼時，請將應用程式的命名空間變更為範例應用程式的命名空間。

`UIThemeClasses/ThemeInfo.cs`:

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

下列配置 [元件](xref:blazor/layouts) 會指定主題資訊 (`ThemeInfo`) 做為組成屬性之版面配置主體的所有元件的串聯值 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 。 `ButtonClass` 會指派值 [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) ，這是啟動程式按鈕樣式。 元件階層中的任何子代元件都可以 `ButtonClass` 透過串聯值使用屬性 `ThemeInfo` 。

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a>`[CascadingParameter]` 屬性

為了利用串聯值，子代元件會使用[ `[CascadingParameter]` 屬性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)來宣告串聯參數。 串聯值會 **依類型** 系結至串聯參數。 本文稍後的「串聯 [多值](#cascade-multiple-values) 」一節中涵蓋了相同型別的串聯多個值。

下列元件會將串聯 `ThemeInfo` 值系結至串聯參數，並選擇性地使用相同的名稱 `ThemeInfo` 。 參數是用來設定按鈕的 CSS 類別 **`Increment Counter (Themed)`** 。

`Pages/ThemedCounter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a>串聯多個值

若要在相同的子樹中串聯多個相同類型的值，請提供 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 每個元件的唯一字串 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 及其對應的[ `[CascadingParameter]` 屬性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)。

在下列範例中，兩個元件會串聯 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 不同的實例 `CascadingType` ：

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

在子系元件中，串聯的參數會透過下列方式，從上階元件接收其串聯值 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> ：

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a>跨元件階層傳遞資料

串聯參數也可讓元件跨元件階層傳遞資料。 請考慮下列 UI 索引標籤集範例，其中索引標籤集合元件會維護一系列的個別索引標籤。

> [!NOTE]
> 針對本節中的範例，應用程式的命名空間為 `BlazorSample` 。 當您在自己的範例應用程式中試驗程式碼時，請將命名空間變更為範例應用程式的命名空間。

建立 `ITab` 會在名為的資料夾中執行 tab 鍵的介面 `UIInterfaces` 。

`UIInterfaces/ITab.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

下列 `TabSet` 元件會維護一組索引標籤。 在本節稍後建立的索引標籤集合 `Tab` 元件，提供清單專案 (`<li>...</li>`)  (`<ul>...</ul>`) 。

子 `Tab` 元件不會明確地以參數形式傳遞至 `TabSet` 。 相反地，子 `Tab` 元件是的子內容的一部分 `TabSet` 。 但是， `TabSet` 仍然需要參考每個 `Tab` 元件，才能轉譯標頭和使用中的索引標籤。若要在不需要額外程式碼的情況下啟用此協調， `TabSet` 元件 *可以提供本身* 的串聯值，然後由子 `Tab` 元件挑選。

`Shared/TabSet.razor`:

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

子代 `Tab` 元件會將包含的 `TabSet` 視為串聯參數。 `Tab`元件會將自己加入至 `TabSet` 和座標，以設定使用中的索引標籤。

`Shared/Tab.razor`:

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

下列 `ExampleTabSet` 元件使用 `TabSet` 包含三個元件的元件 `Tab` 。

`Pages/ExampleTabSet.razor`:

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
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
