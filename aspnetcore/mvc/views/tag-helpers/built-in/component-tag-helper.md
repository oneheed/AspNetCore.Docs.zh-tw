---
title: ASP.NET Core 中的元件標記協助程式
author: guardrex
ms.author: riande
description: '瞭解如何使用 ASP.NET Core 元件標籤協助程式來轉譯 Razor 頁面和視圖中的元件。'
ms.custom: mvc
ms.date: 10/29/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595450"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="e1773-103">ASP.NET Core 中的元件標記協助程式</span><span class="sxs-lookup"><span data-stu-id="e1773-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="e1773-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e1773-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e1773-105">先決條件</span><span class="sxs-lookup"><span data-stu-id="e1773-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e1773-106">遵循下列其中一項 *的設定一節中* 的指導方針：</span><span class="sxs-lookup"><span data-stu-id="e1773-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e1773-107">遵循本文 *的設定一節中* 的指導方針 <xref:blazor/components/prerendering-and-integration?pivots=server> 。</span><span class="sxs-lookup"><span data-stu-id="e1773-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="e1773-108">元件標記協助程式</span><span class="sxs-lookup"><span data-stu-id="e1773-108">Component Tag Helper</span></span>

<span data-ttu-id="e1773-109">若要從頁面或視圖轉譯元件，請使用 [元件標記](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) 協助程式 (`<component>` 標記) 。</span><span class="sxs-lookup"><span data-stu-id="e1773-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="e1773-110">Razor Razor .Net 5.0 或更新版本的 ASP.NET Core 支援將元件整合至裝載 *Blazor WebAssembly 應用程式* 中的頁面和 MVC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e1773-110">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="e1773-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為：</span><span class="sxs-lookup"><span data-stu-id="e1773-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="e1773-112">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="e1773-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="e1773-113">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="e1773-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e1773-114">Blazor WebAssembly 應用程式轉譯模式如下表所示。</span><span class="sxs-lookup"><span data-stu-id="e1773-114">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="e1773-115">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="e1773-115">Render Mode</span></span> | <span data-ttu-id="e1773-116">描述</span><span class="sxs-lookup"><span data-stu-id="e1773-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="e1773-117">轉譯應用程式的標記，以在 Blazor WebAssembly 瀏覽器中載入時用來包含互動式元件。</span><span class="sxs-lookup"><span data-stu-id="e1773-117">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="e1773-118">元件不是資源清單。</span><span class="sxs-lookup"><span data-stu-id="e1773-118">The component isn't prerendered.</span></span> <span data-ttu-id="e1773-119">此選項可讓您更輕鬆地 Blazor WebAssembly 在不同的頁面上呈現不同的元件。</span><span class="sxs-lookup"><span data-stu-id="e1773-119">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="e1773-120">將元件 Prerenders 為靜態 HTML，並包含應用程式的標記， Blazor WebAssembly 以便稍後在瀏覽器中載入時，讓元件成為互動式元件。</span><span class="sxs-lookup"><span data-stu-id="e1773-120">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="e1773-121">Blazor Server 應用程式轉譯模式如下表所示。</span><span class="sxs-lookup"><span data-stu-id="e1773-121">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="e1773-122">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="e1773-122">Render Mode</span></span> | <span data-ttu-id="e1773-123">描述</span><span class="sxs-lookup"><span data-stu-id="e1773-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e1773-124">將元件轉譯為靜態 HTML，並包含 Blazor Server 應用程式的標記。</span><span class="sxs-lookup"><span data-stu-id="e1773-124">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e1773-125">當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e1773-125">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e1773-126">轉譯應用程式的標記 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e1773-126">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e1773-127">不包含元件的輸出。</span><span class="sxs-lookup"><span data-stu-id="e1773-127">Output from the component isn't included.</span></span> <span data-ttu-id="e1773-128">當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e1773-128">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e1773-129">將元件轉譯為靜態 HTML。</span><span class="sxs-lookup"><span data-stu-id="e1773-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e1773-130">Blazor Server 應用程式轉譯模式如下表所示。</span><span class="sxs-lookup"><span data-stu-id="e1773-130">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="e1773-131">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="e1773-131">Render Mode</span></span> | <span data-ttu-id="e1773-132">描述</span><span class="sxs-lookup"><span data-stu-id="e1773-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e1773-133">將元件轉譯為靜態 HTML，並包含 Blazor Server 應用程式的標記。</span><span class="sxs-lookup"><span data-stu-id="e1773-133">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e1773-134">當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e1773-134">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e1773-135">轉譯應用程式的標記 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="e1773-135">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e1773-136">不包含元件的輸出。</span><span class="sxs-lookup"><span data-stu-id="e1773-136">Output from the component isn't included.</span></span> <span data-ttu-id="e1773-137">當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="e1773-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e1773-138">將元件轉譯為靜態 HTML。</span><span class="sxs-lookup"><span data-stu-id="e1773-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="e1773-139">其他特性包括：</span><span class="sxs-lookup"><span data-stu-id="e1773-139">Additional characteristics include:</span></span>

* <span data-ttu-id="e1773-140">允許多個元件標記協助程式轉譯多個 Razor 元件。</span><span class="sxs-lookup"><span data-stu-id="e1773-140">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="e1773-141">應用程式啟動之後，就無法動態呈現元件。</span><span class="sxs-lookup"><span data-stu-id="e1773-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="e1773-142">雖然頁面和觀點可以使用元件，但反向並不成立。</span><span class="sxs-lookup"><span data-stu-id="e1773-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="e1773-143">元件無法使用 view 和 page 特有的功能，例如部分視圖和區段。</span><span class="sxs-lookup"><span data-stu-id="e1773-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="e1773-144">若要在元件中使用部分視圖的邏輯，請將部分視圖邏輯視為元件。</span><span class="sxs-lookup"><span data-stu-id="e1773-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="e1773-145">不支援從靜態 HTML 網頁轉譯伺服器元件。</span><span class="sxs-lookup"><span data-stu-id="e1773-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="e1773-146">下列元件標籤協助程式會在 `Counter` 應用程式的頁面或視圖中轉譯元件 Blazor Server ，並使用 `ServerPrerendered` ：</span><span class="sxs-lookup"><span data-stu-id="e1773-146">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="e1773-147">上述範例假設 `Counter` 元件是在應用程式的 *Pages* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="e1773-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="e1773-148">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如，或是裝載 `@using BlazorSample.Pages` 的 `@using BlazorSample.Client.Pages` Blazor 解決方案) 。</span><span class="sxs-lookup"><span data-stu-id="e1773-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="e1773-149">元件標記協助程式也可以將參數傳遞至元件。</span><span class="sxs-lookup"><span data-stu-id="e1773-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="e1773-150">請考慮下列 `ColorfulCheckbox` 設定核取方塊標籤色彩和大小的元件：</span><span class="sxs-lookup"><span data-stu-id="e1773-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="e1773-151">`Size` `int` `Color` `string` 元件標記協助程式可以設定 () 和 () [元件參數](xref:blazor/components/index#component-parameters)：</span><span class="sxs-lookup"><span data-stu-id="e1773-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="e1773-152">上述範例假設 `ColorfulCheckbox` 元件是在應用程式的 *共用* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="e1773-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="e1773-153">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `@using BlazorSample.Shared`) 。</span><span class="sxs-lookup"><span data-stu-id="e1773-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="e1773-154">下列 HTML 會在頁面或視圖中呈現：</span><span class="sxs-lookup"><span data-stu-id="e1773-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="e1773-155">傳遞引號字串需要明確的 [ Razor 運算式](xref:mvc/views/razor#explicit-razor-expressions)，如 `param-Color` 先前範例中所示。</span><span class="sxs-lookup"><span data-stu-id="e1773-155">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="e1773-156">Razor `string` 因為屬性是類型，所以類型值的剖析行為不會套用至 `param-*` 屬性 `object` 。</span><span class="sxs-lookup"><span data-stu-id="e1773-156">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="e1773-157">支援所有類型的參數，但下列情況除外：</span><span class="sxs-lookup"><span data-stu-id="e1773-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="e1773-158">泛型參數。</span><span class="sxs-lookup"><span data-stu-id="e1773-158">Generic parameters.</span></span>
* <span data-ttu-id="e1773-159">不可序列化的參數。</span><span class="sxs-lookup"><span data-stu-id="e1773-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="e1773-160">集合參數中的繼承。</span><span class="sxs-lookup"><span data-stu-id="e1773-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="e1773-161">其類型是在 Blazor WebAssembly 應用程式外部或在延遲載入的元件中定義的參數。</span><span class="sxs-lookup"><span data-stu-id="e1773-161">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="e1773-162">參數類型必須是 JSON 可序列化的，這通常表示型別必須具有預設的函式和可設定的屬性。</span><span class="sxs-lookup"><span data-stu-id="e1773-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="e1773-163">例如，您可以 `Size` `Color` 在前面的範例中指定和的值，因為和的型 `Size` 別 `Color` 是 (`int` 和 `string`) 的基本類型，但 JSON 序列化程式支援這些類型。</span><span class="sxs-lookup"><span data-stu-id="e1773-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="e1773-164">在下列範例中，會將類別物件傳遞給元件：</span><span class="sxs-lookup"><span data-stu-id="e1773-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="e1773-165">*MyClass.cs* ：</span><span class="sxs-lookup"><span data-stu-id="e1773-165">*MyClass.cs* :</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="e1773-166">**類別必須有公用無參數的函式。**</span><span class="sxs-lookup"><span data-stu-id="e1773-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="e1773-167">*Shared/MyComponent razor* ：</span><span class="sxs-lookup"><span data-stu-id="e1773-167">*Shared/MyComponent.razor* :</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="e1773-168">*Pages/mypage.aspx. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="e1773-168">*Pages/MyPage.cshtml* :</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="e1773-169">上述範例假設 `MyComponent` 元件是在應用程式的 *共用* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="e1773-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="e1773-170">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如， `@using BlazorSample` 以及 `@using BlazorSample.Shared`) 。</span><span class="sxs-lookup"><span data-stu-id="e1773-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="e1773-171">`MyClass` 位於應用程式的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="e1773-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1773-172">其他資源</span><span class="sxs-lookup"><span data-stu-id="e1773-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
