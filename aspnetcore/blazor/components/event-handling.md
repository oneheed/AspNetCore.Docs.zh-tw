---
title: ASP.NET Core Blazor 事件處理
author: guardrex
description: 瞭解 Blazor 的事件處理功能，包括事件引數類型、事件回呼，以及管理預設瀏覽器事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
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
uid: blazor/components/event-handling
ms.openlocfilehash: e8c3d6a9f2c6b50fc18da59b8e0b5475360673c7
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491455"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a>ASP.NET Core Blazor 事件處理

依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

Razor 元件提供事件處理功能。 針對名為 (的 HTML 元素屬性 [`@on{EVENT}`](xref:mvc/views/razor#onevent) ，例如， `@onclick`) 具有委派類型值的，元件會將 Razor 屬性的值視為事件處理常式。

下列程式碼會在 `UpdateHeading` UI 中選取按鈕時呼叫方法：

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

下列程式碼會在 `CheckChanged` UI 中的核取方塊變更時呼叫方法：

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

事件處理常式也可以是非同步，而且會傳回 <xref:System.Threading.Tasks.Task> 。 不需要手動呼叫 [StateHasChanged](xref:blazor/components/lifecycle#state-changes)。 例外狀況會在發生時記錄。

在下列範例中， `UpdateHeading` 當選取按鈕時，會以非同步方式呼叫：

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a>事件引數類型

針對某些事件，允許事件引數類型。 在事件方法定義中指定事件參數是選擇性的，只有在方法中使用事件種類時才需要。 在下列範例中， `MouseEventArgs` 事件引數是在方法中用 `ShowMessage` 來設定郵件內文：

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<xref:System.EventArgs>下表顯示支援的。

::: moniker range=">= aspnetcore-5.0"

| 事件            | 類別  | DOM 事件和注意事項 |
| ---------------- | ------ | -------------------- |
| 剪貼簿        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| 拖曳             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 並 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 按住拖曳的專案資料。<br><br>Blazor使用[JS Interop](xref:blazor/call-javascript-from-dotnet)搭配[HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)，在應用程式中執行拖放功能。 |
| 錯誤            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| 事件            | <xref:System.EventArgs> | *一般*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*剪貼簿*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*輸入*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*媒體*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保存屬性，以設定事件名稱和事件引數類型之間的對應。 |
| 焦點            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>不包含的支援 `relatedTarget` 。 |
| 輸入            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| 鍵盤         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| 滑鼠            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 滑鼠指標    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 滑鼠滾輪      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| 進度         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 觸控            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 代表觸控式裝置上的單一接觸點。 |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| 事件            | 類別 | DOM 事件和注意事項 |
| ---------------- | ----- | -------------------- |
| 剪貼簿        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| 拖曳             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 並 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 按住拖曳的專案資料。<br><br>Blazor使用[JS Interop](xref:blazor/call-javascript-from-dotnet)搭配[HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)，在應用程式中執行拖放功能。 |
| 錯誤            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| 事件            | <xref:System.EventArgs> | *一般*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*剪貼簿*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*輸入*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*媒體*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保存屬性，以設定事件名稱和事件引數類型之間的對應。 |
| 焦點            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>不包含的支援 `relatedTarget` 。 |
| 輸入            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| 鍵盤         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| 滑鼠            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| 滑鼠指標    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| 滑鼠滾輪      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| 進度         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| 觸控            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 代表觸控式裝置上的單一接觸點。 |

::: moniker-end

如需詳細資訊，請參閱下列資源：

* [ `EventArgs` ASP.NET Core 參考來源 (dotnet/aspnetcore `master` 分支) 中的類別](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web)。 在 `master` *下一個* ASP.NET Core 版本中，分支代表開發中的 API。 針對最新版本，請選取適當的 GitHub 存放庫分支 (例如 `release/3.1`) 。
* [MDN web 檔： GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含哪些 HTML 元素支援每個 DOM 事件的資訊。

## <a name="lambda-expressions"></a>Lambda 運算式

您也可以使用[Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)：

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

更方便地關閉其他值，例如反覆運算一組專案時。 下列範例會建立三個按鈕，每個按鈕都會呼叫 `UpdateHeading` 傳遞事件引數 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) ，以及 `buttonNumber` 在 UI 中選取時 () 的按鈕編號：

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> 請勿直接在 lambda 運算式 **中使用迴圈** 變數，例如 `i` 在先前的 `for` 迴圈範例中。 否則，所有 lambda 運算式都會使用相同的變數，這會導致在所有 lambda 中使用相同的值。 一律在本機變數中捕捉變數的值，然後使用它。 在上述範例中，會將迴圈變數 `i` 指派給 `buttonNumber` 。

## <a name="eventcallback"></a>>app >eventcallback

具有嵌套元件的常見案例是，在子元件事件發生時，想要執行父元件的方法。 `onclick`子元件中發生的事件是常見的使用案例。 若要公開元件之間的事件，請使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 。 父元件可以將回呼方法指派給子元件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 。

`ChildComponent`範例應用程式 (`Components/ChildComponent.razor`) 示範如何設定按鈕的 `onclick` 處理常式，以接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 來自範例的委派 `ParentComponent` 。 的 <xref:Microsoft.AspNetCore.Components.EventCallback> 類型是 `MouseEventArgs` ，適用于來自周邊裝置的 `onclick` 事件：

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

會將 `ParentComponent` 子系的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 設定為它的 `ShowMessage` 方法。

`Pages/ParentComponent.razor`:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

當您在中選取此按鈕時 `ChildComponent` ：

* `ParentComponent` `ShowMessage` 會呼叫的方法。 `messageText` 會更新並顯示在中 `ParentComponent` 。
* [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes)回呼的方法 () 不需要呼叫 `ShowMessage` 。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動呼叫以 rerender `ParentComponent` ，就像是事件處理常式在子系內執行的子事件觸發程式轉譯資料流程一樣。

<xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 允許非同步委派。 <xref:Microsoft.AspNetCore.Components.EventCallback> 是弱型別，可讓傳遞中的任何型別引數 `InvokeAsync(Object)` 。 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 是強型別，而且需要傳遞可 `T` `InvokeAsync(T)` 指派給的引數 `TValue` 。

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<xref:Microsoft.AspNetCore.Components.EventCallback>使用或叫 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 用 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> ，並等待 <xref:System.Threading.Tasks.Task> ：

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<xref:Microsoft.AspNetCore.Components.EventCallback> <xref:Microsoft.AspNetCore.Components.EventCallback%601> 針對事件處理和系結元件參數使用和。

偏好強型別 <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> 。 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 提供更好的錯誤意見反應給元件的使用者。 與其他 UI 事件處理常式類似，指定事件參數是選擇性的。 <xref:Microsoft.AspNetCore.Components.EventCallback>未傳遞任何值給回呼時使用。

## <a name="prevent-default-actions"></a>防止預設動作

使用指示詞 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 屬性以防止事件的預設動作。

在輸入裝置上選取索引鍵，且元素焦點在文字方塊上時，瀏覽器通常會在文字方塊中顯示索引鍵的字元。 在下列範例中，藉由指定指示詞屬性來防止預設行為 `@onkeypress:preventDefault` 。 計數器會遞增，而不會將索引 **+** 鍵捕捉到 `<input>` 元素的值中：

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

指定 `@on{EVENT}:preventDefault` 沒有值的屬性相當於 `@on{EVENT}:preventDefault="true"` 。

屬性的值也可以是運算式。 在下列範例中， `shouldPreventDefault` 是 `bool` 設定為或的欄位 `true` `false` ：

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a>停止事件傳播

使用指示詞 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 屬性停止事件傳播。

在下列範例中，選取此核取方塊可防止從第二個子系將事件 `<div>` 傳播至父系 `<div>` ：

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a>將焦點放在元素

`FocusAsync`在專案[參考](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)上呼叫，以在程式碼中將專案焦點放在元素上：

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
