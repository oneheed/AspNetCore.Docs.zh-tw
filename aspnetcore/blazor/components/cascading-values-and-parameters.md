---
title: 'ASP.NET Core Blazor 級聯值和參數'
author: guardrex
description: 瞭解如何將資料從上階元件傳送到附屬元件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 56d70cea50a3a913b4483f6ea488438269aa58fe
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507976"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a><span data-ttu-id="9bd33-103">ASP.NET Core Blazor 級聯值和參數</span><span class="sxs-lookup"><span data-stu-id="9bd33-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="9bd33-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9bd33-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9bd33-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9bd33-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9bd33-106">在某些情況下，使用 [元件參數](xref:blazor/components/index#component-parameters)將資料從上階元件傳送至子元件很不方便，尤其是在有數個元件層時。</span><span class="sxs-lookup"><span data-stu-id="9bd33-106">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="9bd33-107">串聯值和參數藉由提供一個便利的方式，讓祖系元件為其所有的子元件提供值，來解決這個問題。</span><span class="sxs-lookup"><span data-stu-id="9bd33-107">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="9bd33-108">串聯值和參數也會提供元件的座標方法。</span><span class="sxs-lookup"><span data-stu-id="9bd33-108">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="9bd33-109">主題範例</span><span class="sxs-lookup"><span data-stu-id="9bd33-109">Theme example</span></span>

<span data-ttu-id="9bd33-110">在來自範例應用程式的下列範例中， `ThemeInfo` 類別會指定要在元件階層中流動的主題資訊，讓應用程式的特定部分中的所有按鈕共用相同的樣式。</span><span class="sxs-lookup"><span data-stu-id="9bd33-110">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="9bd33-111">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="9bd33-111">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="9bd33-112">上階的元件可以使用級聯的值元件來提供階層式值。</span><span class="sxs-lookup"><span data-stu-id="9bd33-112">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="9bd33-113"><xref:Microsoft.AspNetCore.Components.CascadingValue%601>元件會包裝元件階層的子樹，並提供單一值給該子樹內的所有元件。</span><span class="sxs-lookup"><span data-stu-id="9bd33-113">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="9bd33-114">例如，範例應用程式 `ThemeInfo` 會在其中一個應用程式的版面配置中，指定主題資訊 () ，做為組成屬性之版面配置主體的所有元件的串聯參數 `@Body` 。</span><span class="sxs-lookup"><span data-stu-id="9bd33-114">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="9bd33-115">`ButtonClass` 會 `btn-success` 在版面配置元件中指派的值。</span><span class="sxs-lookup"><span data-stu-id="9bd33-115">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="9bd33-116">任何子代元件都可以透過串聯物件取用這個屬性 `ThemeInfo` 。</span><span class="sxs-lookup"><span data-stu-id="9bd33-116">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="9bd33-117">`CascadingValuesParametersLayout` 元件：</span><span class="sxs-lookup"><span data-stu-id="9bd33-117">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="9bd33-118">為了利用串聯值，元件會使用屬性來宣告串聯參數 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="9bd33-118">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="9bd33-119">串聯值會依類型系結至串聯參數。</span><span class="sxs-lookup"><span data-stu-id="9bd33-119">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="9bd33-120">在範例應用程式中，元件會將串聯值系結 `CascadingValuesParametersTheme` `ThemeInfo` 至串聯參數。</span><span class="sxs-lookup"><span data-stu-id="9bd33-120">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="9bd33-121">參數是用來設定元件所顯示的其中一個按鈕的 CSS 類別。</span><span class="sxs-lookup"><span data-stu-id="9bd33-121">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="9bd33-122">`CascadingValuesParametersTheme` 元件：</span><span class="sxs-lookup"><span data-stu-id="9bd33-122">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="9bd33-123">若要在相同的子樹中串聯多個相同類型的值，請為 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 每個 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 元件和其對應的屬性提供唯一的字串 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="9bd33-123">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="9bd33-124">在下列範例中，兩個 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 元件 `MyCascadingType` 依名稱來串聯不同的實例：</span><span class="sxs-lookup"><span data-stu-id="9bd33-124">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="9bd33-125">在子系元件中，串聯的參數會依名稱從上階元件的對應串聯值接收其值：</span><span class="sxs-lookup"><span data-stu-id="9bd33-125">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="9bd33-126">TabSet 範例</span><span class="sxs-lookup"><span data-stu-id="9bd33-126">TabSet example</span></span>

<span data-ttu-id="9bd33-127">串聯參數也可讓元件在元件階層之間共同作業。</span><span class="sxs-lookup"><span data-stu-id="9bd33-127">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="9bd33-128">例如，請考慮 `TabSet` 範例應用程式中的下列範例。</span><span class="sxs-lookup"><span data-stu-id="9bd33-128">For example, consider the following `TabSet` example in the sample app.</span></span>

<span data-ttu-id="9bd33-129">範例應用程式具有可執行索引標籤 `ITab` 的介面：</span><span class="sxs-lookup"><span data-stu-id="9bd33-129">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/5.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="9bd33-130">`CascadingValuesParametersTabSet`元件使用 `TabSet` 元件，其中包含數個 `Tab` 元件：</span><span class="sxs-lookup"><span data-stu-id="9bd33-130">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="9bd33-131">子 `Tab` 元件不會明確地以參數形式傳遞至 `TabSet` 。</span><span class="sxs-lookup"><span data-stu-id="9bd33-131">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="9bd33-132">相反地，子 `Tab` 元件是的子內容的一部分 `TabSet` 。</span><span class="sxs-lookup"><span data-stu-id="9bd33-132">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="9bd33-133">但是， `TabSet` 仍然需要知道每個元件， `Tab` 才能轉譯標頭和使用中的索引標籤。若要在不需要額外程式碼的情況下啟用此協調， `TabSet` 元件 *可以提供本身* 的串聯值，然後由子 `Tab` 元件挑選。</span><span class="sxs-lookup"><span data-stu-id="9bd33-133">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="9bd33-134">`TabSet` 元件：</span><span class="sxs-lookup"><span data-stu-id="9bd33-134">`TabSet` component:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="9bd33-135">這些子代 `Tab` 元件會將包含的 `TabSet` 視為串聯參數，因此 `Tab` 元件會將自己加入至 `TabSet` ，並在哪一個索引標籤為作用中協調。</span><span class="sxs-lookup"><span data-stu-id="9bd33-135">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="9bd33-136">`Tab` 元件：</span><span class="sxs-lookup"><span data-stu-id="9bd33-136">`Tab` component:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/Tab.razor)]
