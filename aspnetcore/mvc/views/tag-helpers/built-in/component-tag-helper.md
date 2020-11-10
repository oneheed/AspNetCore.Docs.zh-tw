---
title: ASP.NET Core 中的元件標記協助程式
author: guardrex
ms.author: riande
description: '瞭解如何使用 ASP.NET Core 元件標籤協助程式來轉譯 :::no-loc(Razor)::: 頁面和視圖中的元件。'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431039"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="4872b-103">ASP.NET Core 中的元件標記協助程式</span><span class="sxs-lookup"><span data-stu-id="4872b-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="4872b-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4872b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4872b-105">先決條件</span><span class="sxs-lookup"><span data-stu-id="4872b-105">Prerequisites</span></span>

<span data-ttu-id="4872b-106">遵循下列其中一項 *的設定一節中* 的指導方針：</span><span class="sxs-lookup"><span data-stu-id="4872b-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a><span data-ttu-id="4872b-107">元件標記協助程式</span><span class="sxs-lookup"><span data-stu-id="4872b-107">Component Tag Helper</span></span>

<span data-ttu-id="4872b-108">若要從頁面或視圖轉譯元件，請使用 [元件標記](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) 協助程式 (`<component>` 標記) 。</span><span class="sxs-lookup"><span data-stu-id="4872b-108">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="4872b-109">:::no-loc(Razor)::: :::no-loc(Razor)::: .Net 5.0 或更新版本的 ASP.NET Core 支援將元件整合至裝載 *:::no-loc(Blazor WebAssembly)::: 應用程式* 中的頁面和 MVC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4872b-109">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="4872b-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為：</span><span class="sxs-lookup"><span data-stu-id="4872b-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="4872b-111">會資源清單到頁面中。</span><span class="sxs-lookup"><span data-stu-id="4872b-111">Is prerendered into the page.</span></span>
* <span data-ttu-id="4872b-112">會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="4872b-112">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4872b-113">:::no-loc(Blazor WebAssembly)::: 應用程式轉譯模式如下表所示。</span><span class="sxs-lookup"><span data-stu-id="4872b-113">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="4872b-114">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="4872b-114">Render Mode</span></span> | <span data-ttu-id="4872b-115">說明</span><span class="sxs-lookup"><span data-stu-id="4872b-115">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="4872b-116">轉譯應用程式的標記，以在 :::no-loc(Blazor WebAssembly)::: 瀏覽器中載入時用來包含互動式元件。</span><span class="sxs-lookup"><span data-stu-id="4872b-116">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="4872b-117">元件不是資源清單。</span><span class="sxs-lookup"><span data-stu-id="4872b-117">The component isn't prerendered.</span></span> <span data-ttu-id="4872b-118">此選項可讓您更輕鬆地 :::no-loc(Blazor WebAssembly)::: 在不同的頁面上呈現不同的元件。</span><span class="sxs-lookup"><span data-stu-id="4872b-118">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="4872b-119">將元件 Prerenders 為靜態 HTML，並包含應用程式的標記， :::no-loc(Blazor WebAssembly)::: 以便稍後在瀏覽器中載入時，讓元件成為互動式元件。</span><span class="sxs-lookup"><span data-stu-id="4872b-119">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="4872b-120">:::no-loc(Blazor Server)::: 應用程式轉譯模式如下表所示。</span><span class="sxs-lookup"><span data-stu-id="4872b-120">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="4872b-121">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="4872b-121">Render Mode</span></span> | <span data-ttu-id="4872b-122">說明</span><span class="sxs-lookup"><span data-stu-id="4872b-122">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4872b-123">將元件轉譯為靜態 HTML，並包含 :::no-loc(Blazor Server)::: 應用程式的標記。</span><span class="sxs-lookup"><span data-stu-id="4872b-123">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="4872b-124">當使用者代理程式啟動時，會使用此標記來啟動 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4872b-124">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4872b-125">轉譯應用程式的標記 :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="4872b-125">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="4872b-126">不包含元件的輸出。</span><span class="sxs-lookup"><span data-stu-id="4872b-126">Output from the component isn't included.</span></span> <span data-ttu-id="4872b-127">當使用者代理程式啟動時，會使用此標記來啟動 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4872b-127">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4872b-128">將元件轉譯為靜態 HTML。</span><span class="sxs-lookup"><span data-stu-id="4872b-128">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4872b-129">:::no-loc(Blazor Server)::: 應用程式轉譯模式如下表所示。</span><span class="sxs-lookup"><span data-stu-id="4872b-129">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="4872b-130">轉譯模式</span><span class="sxs-lookup"><span data-stu-id="4872b-130">Render Mode</span></span> | <span data-ttu-id="4872b-131">說明</span><span class="sxs-lookup"><span data-stu-id="4872b-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4872b-132">將元件轉譯為靜態 HTML，並包含 :::no-loc(Blazor Server)::: 應用程式的標記。</span><span class="sxs-lookup"><span data-stu-id="4872b-132">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="4872b-133">當使用者代理程式啟動時，會使用此標記來啟動 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4872b-133">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4872b-134">轉譯應用程式的標記 :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="4872b-134">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="4872b-135">不包含元件的輸出。</span><span class="sxs-lookup"><span data-stu-id="4872b-135">Output from the component isn't included.</span></span> <span data-ttu-id="4872b-136">當使用者代理程式啟動時，會使用此標記來啟動 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="4872b-136">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4872b-137">將元件轉譯為靜態 HTML。</span><span class="sxs-lookup"><span data-stu-id="4872b-137">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="4872b-138">其他特性包括：</span><span class="sxs-lookup"><span data-stu-id="4872b-138">Additional characteristics include:</span></span>

* <span data-ttu-id="4872b-139">允許多個元件標記協助程式轉譯多個 :::no-loc(Razor)::: 元件。</span><span class="sxs-lookup"><span data-stu-id="4872b-139">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="4872b-140">應用程式啟動之後，就無法動態呈現元件。</span><span class="sxs-lookup"><span data-stu-id="4872b-140">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="4872b-141">雖然頁面和觀點可以使用元件，但反向並不成立。</span><span class="sxs-lookup"><span data-stu-id="4872b-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="4872b-142">元件無法使用 view 和 page 特有的功能，例如部分視圖和區段。</span><span class="sxs-lookup"><span data-stu-id="4872b-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="4872b-143">若要在元件中使用部分視圖的邏輯，請將部分視圖邏輯視為元件。</span><span class="sxs-lookup"><span data-stu-id="4872b-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="4872b-144">不支援從靜態 HTML 網頁轉譯伺服器元件。</span><span class="sxs-lookup"><span data-stu-id="4872b-144">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="4872b-145">下列元件標籤協助程式會在 `Counter` 應用程式的頁面或視圖中轉譯元件 :::no-loc(Blazor Server)::: ，並使用 `ServerPrerendered` ：</span><span class="sxs-lookup"><span data-stu-id="4872b-145">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="4872b-146">上述範例假設 `Counter` 元件是在應用程式的 *Pages* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="4872b-146">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="4872b-147">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如，或是裝載 `@using :::no-loc(Blazor):::Sample.Pages` 的 `@using :::no-loc(Blazor):::Sample.Client.Pages` :::no-loc(Blazor)::: 解決方案) 。</span><span class="sxs-lookup"><span data-stu-id="4872b-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="4872b-148">元件標記協助程式也可以將參數傳遞至元件。</span><span class="sxs-lookup"><span data-stu-id="4872b-148">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="4872b-149">請考慮下列 `ColorfulCheckbox` 設定核取方塊標籤色彩和大小的元件：</span><span class="sxs-lookup"><span data-stu-id="4872b-149">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
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

<span data-ttu-id="4872b-150">`Size` `int` `Color` `string` 元件標記協助程式可以設定 () 和 () [元件參數](xref:blazor/components/index#component-parameters)：</span><span class="sxs-lookup"><span data-stu-id="4872b-150">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="4872b-151">上述範例假設 `ColorfulCheckbox` 元件是在應用程式的 *共用* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="4872b-151">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="4872b-152">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `@using :::no-loc(Blazor):::Sample.Shared`) 。</span><span class="sxs-lookup"><span data-stu-id="4872b-152">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="4872b-153">下列 HTML 會在頁面或視圖中呈現：</span><span class="sxs-lookup"><span data-stu-id="4872b-153">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="4872b-154">傳遞引號字串需要明確的 [ :::no-loc(Razor)::: 運算式](xref:mvc/views/razor#explicit-razor-expressions)，如 `param-Color` 先前範例中所示。</span><span class="sxs-lookup"><span data-stu-id="4872b-154">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="4872b-155">:::no-loc(Razor)::: `string` 因為屬性是類型，所以類型值的剖析行為不會套用至 `param-*` 屬性 `object` 。</span><span class="sxs-lookup"><span data-stu-id="4872b-155">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="4872b-156">支援所有類型的參數，但下列情況除外：</span><span class="sxs-lookup"><span data-stu-id="4872b-156">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="4872b-157">泛型參數。</span><span class="sxs-lookup"><span data-stu-id="4872b-157">Generic parameters.</span></span>
* <span data-ttu-id="4872b-158">不可序列化的參數。</span><span class="sxs-lookup"><span data-stu-id="4872b-158">Non-serializable parameters.</span></span>
* <span data-ttu-id="4872b-159">集合參數中的繼承。</span><span class="sxs-lookup"><span data-stu-id="4872b-159">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="4872b-160">其類型是在 :::no-loc(Blazor WebAssembly)::: 應用程式外部或在延遲載入的元件中定義的參數。</span><span class="sxs-lookup"><span data-stu-id="4872b-160">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="4872b-161">參數類型必須是 JSON 可序列化的，這通常表示型別必須具有預設的函式和可設定的屬性。</span><span class="sxs-lookup"><span data-stu-id="4872b-161">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="4872b-162">例如，您可以 `Size` `Color` 在前面的範例中指定和的值，因為和的型 `Size` 別 `Color` 是 (`int` 和 `string`) 的基本類型，但 JSON 序列化程式支援這些類型。</span><span class="sxs-lookup"><span data-stu-id="4872b-162">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="4872b-163">在下列範例中，會將類別物件傳遞給元件：</span><span class="sxs-lookup"><span data-stu-id="4872b-163">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="4872b-164">*MyClass.cs* ：</span><span class="sxs-lookup"><span data-stu-id="4872b-164">*MyClass.cs* :</span></span>

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

<span data-ttu-id="4872b-165">**類別必須有公用無參數的函式。**</span><span class="sxs-lookup"><span data-stu-id="4872b-165">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="4872b-166">*Shared/MyComponent razor* ：</span><span class="sxs-lookup"><span data-stu-id="4872b-166">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="4872b-167">*Pages/mypage.aspx. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="4872b-167">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="4872b-168">上述範例假設 `MyComponent` 元件是在應用程式的 *共用* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="4872b-168">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="4872b-169">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如， `@using :::no-loc(Blazor):::Sample` 以及 `@using :::no-loc(Blazor):::Sample.Shared`) 。</span><span class="sxs-lookup"><span data-stu-id="4872b-169">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="4872b-170">`MyClass` 位於應用程式的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="4872b-170">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4872b-171">其他資源</span><span class="sxs-lookup"><span data-stu-id="4872b-171">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
