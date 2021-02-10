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
ms.openlocfilehash: 9b667ff83bf6dd9b400805eff403c8c3f5c7b82a
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107086"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="0c245-103">ASP.NET Core Blazor 級聯值和參數</span><span class="sxs-lookup"><span data-stu-id="0c245-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="0c245-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0c245-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="0c245-105">串聯 *值和參數* 提供 convienent 的方式，可將元件階層的資料從上階元件往下傳送至任意數目的 decendent 元件。</span><span class="sxs-lookup"><span data-stu-id="0c245-105">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="0c245-106">不同于 [元件參數](xref:blazor/components/index#component-parameters)，串聯值和參數不需要針對取用資料的每個子代元件進行屬性指派。</span><span class="sxs-lookup"><span data-stu-id="0c245-106">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="0c245-107">串聯值和參數也允許元件跨元件階層彼此協調。</span><span class="sxs-lookup"><span data-stu-id="0c245-107">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="0c245-108">`CascadingValue` 元件</span><span class="sxs-lookup"><span data-stu-id="0c245-108">`CascadingValue` component</span></span>

<span data-ttu-id="0c245-109">上階元件會使用架構的元件提供階層式值 Blazor [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) ，以包裝元件階層的子樹，並提供單一值給其子樹內的所有元件。</span><span class="sxs-lookup"><span data-stu-id="0c245-109">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="0c245-110">下列範例將示範如何在版面配置元件的元件階層中，將主題資訊的流程向下示範，以提供子元件中按鈕的 CSS 樣式類別。</span><span class="sxs-lookup"><span data-stu-id="0c245-110">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="0c245-111">下列 `ThemeInfo` c # 類別放在名為的資料夾中 `UIThemeClasses` ，並指定主題資訊。</span><span class="sxs-lookup"><span data-stu-id="0c245-111">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="0c245-112">針對本節中的範例，應用程式的命名空間為 `BlazorSample` 。</span><span class="sxs-lookup"><span data-stu-id="0c245-112">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="0c245-113">當您在自己的範例應用程式中試驗程式碼時，請將應用程式的命名空間變更為範例應用程式的命名空間。</span><span class="sxs-lookup"><span data-stu-id="0c245-113">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="0c245-114">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="0c245-114">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="0c245-115">下列配置 [元件](xref:blazor/layouts) 會指定主題資訊 (`ThemeInfo`) 做為組成屬性之版面配置主體的所有元件的串聯值 <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> 。</span><span class="sxs-lookup"><span data-stu-id="0c245-115">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="0c245-116">`ButtonClass` 會指派值 [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/) ，這是啟動程式按鈕樣式。</span><span class="sxs-lookup"><span data-stu-id="0c245-116">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="0c245-117">元件階層中的任何子代元件都可以 `ButtonClass` 透過串聯值使用屬性 `ThemeInfo` 。</span><span class="sxs-lookup"><span data-stu-id="0c245-117">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="0c245-118">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c245-118">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="0c245-119">`[CascadingParameter]` 屬性</span><span class="sxs-lookup"><span data-stu-id="0c245-119">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="0c245-120">為了利用串聯值，子代元件會使用[ `[CascadingParameter]` 屬性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)來宣告串聯參數。</span><span class="sxs-lookup"><span data-stu-id="0c245-120">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="0c245-121">串聯值會 **依類型** 系結至串聯參數。</span><span class="sxs-lookup"><span data-stu-id="0c245-121">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="0c245-122">本文稍後的「串聯 [多值](#cascade-multiple-values) 」一節中涵蓋了相同型別的串聯多個值。</span><span class="sxs-lookup"><span data-stu-id="0c245-122">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="0c245-123">下列元件會將串聯 `ThemeInfo` 值系結至串聯參數，並選擇性地使用相同的名稱 `ThemeInfo` 。</span><span class="sxs-lookup"><span data-stu-id="0c245-123">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="0c245-124">參數是用來設定按鈕的 CSS 類別 **`Increment Counter (Themed)`** 。</span><span class="sxs-lookup"><span data-stu-id="0c245-124">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="0c245-125">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c245-125">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="0c245-126">串聯多個值</span><span class="sxs-lookup"><span data-stu-id="0c245-126">Cascade multiple values</span></span>

<span data-ttu-id="0c245-127">若要在相同的子樹中串聯多個相同類型的值，請提供 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 每個元件的唯一字串 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 及其對應的[ `[CascadingParameter]` 屬性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)。</span><span class="sxs-lookup"><span data-stu-id="0c245-127">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="0c245-128">在下列範例中，兩個元件會串聯 [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) 不同的實例 `CascadingType` ：</span><span class="sxs-lookup"><span data-stu-id="0c245-128">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

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

<span data-ttu-id="0c245-129">在子系元件中，串聯的參數會透過下列方式，從上階元件接收其串聯值 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> ：</span><span class="sxs-lookup"><span data-stu-id="0c245-129">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="0c245-130">跨元件階層傳遞資料</span><span class="sxs-lookup"><span data-stu-id="0c245-130">Pass data across a component hierarchy</span></span>

<span data-ttu-id="0c245-131">串聯參數也可讓元件跨元件階層傳遞資料。</span><span class="sxs-lookup"><span data-stu-id="0c245-131">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="0c245-132">請考慮下列 UI 索引標籤集範例，其中索引標籤集合元件會維護一系列的個別索引標籤。</span><span class="sxs-lookup"><span data-stu-id="0c245-132">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="0c245-133">針對本節中的範例，應用程式的命名空間為 `BlazorSample` 。</span><span class="sxs-lookup"><span data-stu-id="0c245-133">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="0c245-134">當您在自己的範例應用程式中試驗程式碼時，請將命名空間變更為範例應用程式的命名空間。</span><span class="sxs-lookup"><span data-stu-id="0c245-134">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="0c245-135">建立 `ITab` 會在名為的資料夾中執行 tab 鍵的介面 `UIInterfaces` 。</span><span class="sxs-lookup"><span data-stu-id="0c245-135">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="0c245-136">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="0c245-136">`UIInterfaces/ITab.cs`:</span></span>

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

<span data-ttu-id="0c245-137">下列 `TabSet` 元件會維護一組索引標籤。</span><span class="sxs-lookup"><span data-stu-id="0c245-137">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="0c245-138">在本節稍後建立的索引標籤集合 `Tab` 元件，提供清單專案 (`<li>...</li>`)  (`<ul>...</ul>`) 。</span><span class="sxs-lookup"><span data-stu-id="0c245-138">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="0c245-139">子 `Tab` 元件不會明確地以參數形式傳遞至 `TabSet` 。</span><span class="sxs-lookup"><span data-stu-id="0c245-139">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="0c245-140">相反地，子 `Tab` 元件是的子內容的一部分 `TabSet` 。</span><span class="sxs-lookup"><span data-stu-id="0c245-140">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="0c245-141">但是， `TabSet` 仍然需要參考每個 `Tab` 元件，才能轉譯標頭和使用中的索引標籤。若要在不需要額外程式碼的情況下啟用此協調， `TabSet` 元件 *可以提供本身* 的串聯值，然後由子 `Tab` 元件挑選。</span><span class="sxs-lookup"><span data-stu-id="0c245-141">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="0c245-142">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c245-142">`Shared/TabSet.razor`:</span></span>

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

<span data-ttu-id="0c245-143">子代 `Tab` 元件會將包含的 `TabSet` 視為串聯參數。</span><span class="sxs-lookup"><span data-stu-id="0c245-143">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="0c245-144">`Tab`元件會將自己加入至 `TabSet` 和座標，以設定使用中的索引標籤。</span><span class="sxs-lookup"><span data-stu-id="0c245-144">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="0c245-145">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c245-145">`Shared/Tab.razor`:</span></span>

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

<span data-ttu-id="0c245-146">下列 `ExampleTabSet` 元件使用 `TabSet` 包含三個元件的元件 `Tab` 。</span><span class="sxs-lookup"><span data-stu-id="0c245-146">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="0c245-147">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="0c245-147">`Pages/ExampleTabSet.razor`:</span></span>

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
