---
title: ASP.NET Core 中的元件標記協助程式
author: guardrex
ms.author: riande
description: 瞭解如何使用 ASP.NET Core 元件標籤協助程式來轉譯 Razor 頁面和視圖中的元件。
ms.custom: mvc
ms.date: 10/29/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431039"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core 中的元件標記協助程式

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

## <a name="prerequisites"></a>先決條件

遵循下列其中一項 *的設定一節中* 的指導方針：

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a>元件標記協助程式

若要從頁面或視圖轉譯元件，請使用 [元件標記](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) 協助程式 (`<component>` 標記) 。

> [!NOTE]
> Razor Razor .Net 5.0 或更新版本的 ASP.NET Core 支援將元件整合至裝載 *Blazor WebAssembly 應用程式* 中的頁面和 MVC 應用程式。

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 設定元件是否為：

* 會資源清單到頁面中。
* 會在頁面上轉譯為靜態 HTML，或包含從使用者代理程式啟動應用程式所需的資訊 Blazor 。

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly 應用程式轉譯模式如下表所示。

| 轉譯模式 | 說明 |
| ----------- | ----------- |
| `WebAssembly` | 轉譯應用程式的標記，以在 Blazor WebAssembly 瀏覽器中載入時用來包含互動式元件。 元件不是資源清單。 此選項可讓您更輕鬆地 Blazor WebAssembly 在不同的頁面上呈現不同的元件。 |
| `WebAssemblyPrerendered` | 將元件 Prerenders 為靜態 HTML，並包含應用程式的標記， Blazor WebAssembly 以便稍後在瀏覽器中載入時，讓元件成為互動式元件。 |

Blazor Server 應用程式轉譯模式如下表所示。

| 轉譯模式 | 說明 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 將元件轉譯為靜態 HTML，並包含 Blazor Server 應用程式的標記。 當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 轉譯應用程式的標記 Blazor Server 。 不包含元件的輸出。 當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 將元件轉譯為靜態 HTML。 |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor Server 應用程式轉譯模式如下表所示。

| 轉譯模式 | 說明 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 將元件轉譯為靜態 HTML，並包含 Blazor Server 應用程式的標記。 當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 轉譯應用程式的標記 Blazor Server 。 不包含元件的輸出。 當使用者代理程式啟動時，會使用此標記來啟動 Blazor 應用程式。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 將元件轉譯為靜態 HTML。 |

::: moniker-end

其他特性包括：

* 允許多個元件標記協助程式轉譯多個 Razor 元件。
* 應用程式啟動之後，就無法動態呈現元件。
* 雖然頁面和觀點可以使用元件，但反向並不成立。 元件無法使用 view 和 page 特有的功能，例如部分視圖和區段。 若要在元件中使用部分視圖的邏輯，請將部分視圖邏輯視為元件。
* 不支援從靜態 HTML 網頁轉譯伺服器元件。

下列元件標籤協助程式會在 `Counter` 應用程式的頁面或視圖中轉譯元件 Blazor Server ，並使用 `ServerPrerendered` ：

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

上述範例假設 `Counter` 元件是在應用程式的 *Pages* 資料夾中。 預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如，或是裝載 `@using BlazorSample.Pages` 的 `@using BlazorSample.Client.Pages` Blazor 解決方案) 。

元件標記協助程式也可以將參數傳遞至元件。 請考慮下列 `ColorfulCheckbox` 設定核取方塊標籤色彩和大小的元件：

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

`Size` `int` `Color` `string` 元件標記協助程式可以設定 () 和 () [元件參數](xref:blazor/components/index#component-parameters)：

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

上述範例假設 `ColorfulCheckbox` 元件是在應用程式的 *共用* 資料夾中。 預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `@using BlazorSample.Shared`) 。

下列 HTML 會在頁面或視圖中呈現：

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

傳遞引號字串需要明確的 [ Razor 運算式](xref:mvc/views/razor#explicit-razor-expressions)，如 `param-Color` 先前範例中所示。 Razor `string` 因為屬性是類型，所以類型值的剖析行為不會套用至 `param-*` 屬性 `object` 。

支援所有類型的參數，但下列情況除外：

* 泛型參數。
* 不可序列化的參數。
* 集合參數中的繼承。
* 其類型是在 Blazor WebAssembly 應用程式外部或在延遲載入的元件中定義的參數。

參數類型必須是 JSON 可序列化的，這通常表示型別必須具有預設的函式和可設定的屬性。 例如，您可以 `Size` `Color` 在前面的範例中指定和的值，因為和的型 `Size` 別 `Color` 是 (`int` 和 `string`) 的基本類型，但 JSON 序列化程式支援這些類型。

在下列範例中，會將類別物件傳遞給元件：

*MyClass.cs* ：

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

**類別必須有公用無參數的函式。**

*Shared/MyComponent razor* ：

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

*Pages/mypage.aspx. cshtml* ：

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

上述範例假設 `MyComponent` 元件是在應用程式的 *共用* 資料夾中。 預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如， `@using BlazorSample` 以及 `@using BlazorSample.Shared`) 。 `MyClass` 位於應用程式的命名空間中。

## <a name="additional-resources"></a>其他資源

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
