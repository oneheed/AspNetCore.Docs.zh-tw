---
title: ASP.NET Core Blazor 事件處理
author: guardrex
description: 瞭解 Blazor 的事件處理功能，包括事件引數類型、事件回呼，以及管理預設瀏覽器事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2021
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
uid: blazor/components/event-handling
ms.openlocfilehash: c04ff3c2a8de6e8c9d0fa0653f09da2984dc528e
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105555041"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor 事件處理

Razor使用語法指定元件標記中的委派事件處理常式 [`@on{DOM EVENT}="{DELEGATE}"`](xref:mvc/views/razor#onevent) Razor ：

* `{DOM EVENT}`預留位置是[檔物件模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events) (例如 `click`) 。
* `{DELEGATE}`預留位置是 c # 委派事件處理常式。

針對事件處理：

* 支援傳回的非同步委派事件處理常式 <xref:System.Threading.Tasks.Task> 。
* 委派事件處理常式會自動觸發 UI 呈現，因此不需要手動呼叫 [StateHasChanged](xref:blazor/components/lifecycle#state-changes-statehaschanged)。
* 系統會記錄例外狀況。

下列程式碼：

* 在 `UpdateHeading` UI 中選取按鈕時，呼叫方法。
* `CheckChanged`當 UI 中的核取方塊變更時，呼叫方法。

`Pages/EventHandlerExample1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

::: moniker-end

在下列範例中， `UpdateHeading` ：

* 當選取按鈕時，會以非同步方式呼叫。
* 在更新標題之前等候兩秒鐘。

`Pages/EventHandlerExample2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

::: moniker-end

## <a name="event-arguments"></a>事件引數

::: moniker range=">= aspnetcore-6.0"

### <a name="built-in-event-arguments"></a>內建事件引數

::: moniker-end

針對支援事件引數類型的事件，只有在方法中使用事件種類時，才需要在事件方法定義中指定事件參數。 在下列範例中， <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> 會在方法中用 `ReportPointerLocation` 來設定郵件內文，以便在使用者選取 UI 中的按鈕時報告滑鼠座標。

`Pages/EventHandlerExample3.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

::: moniker-end

<xref:System.EventArgs>下表顯示支援的。

::: moniker range=">= aspnetcore-5.0"

| 事件            | 類別  | [檔物件模型 (DOM) ](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件和附注 |
| ---------------- | ------ | --- |
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

| 事件            | 類別 | [檔物件模型 (DOM) ](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件和附注 |
| ---------------- | ----- | --- |
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

* [`EventArgs` ASP.NET Core 參考來源中的類別 (dotnet/aspnetcore `main` 分支) ](https://github.com/dotnet/aspnetcore/tree/main/src/Components/Web/src/Web)

  [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

* [MDN web 檔： GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含哪些 HTML 元素支援每個 DOM 事件的資訊。

::: moniker range=">= aspnetcore-6.0"

### <a name="custom-event-arguments"></a>自訂事件引數

Blazor 支援自訂事件引數，可讓您使用自訂事件將任意資料傳遞給 .NET 事件處理常式。

#### <a name="general-configuration"></a>一般設定

使用自訂事件引數的自訂事件，通常會透過下列步驟啟用。

1. 在 JavaScript 中，定義函式以從來源事件建立自訂事件引數物件：

   ```javascript
   function eventArgsCreator(event) { 
     return {
       customProperty1: 'any value for property 1',
       customProperty2: event.srcElement.value
     };
   }
   ```

1. 使用先前的處理常式，在 () 中註冊自訂事件， `wwwroot/index.html` Blazor WebAssembly 或在下列專案 `Pages/_Host.cshtml` Blazor Server 之後立即 () Blazor `<script>` ：

   ```html
   <script>
       Blazor.registerCustomEventType('customevent', {
           createEventArgs: eventArgsCreator;
       });
   </script>
   ```

   > [!NOTE]
   > 的呼叫 `registerCustomEventType` 只會在每個事件一次的腳本中執行。

1. 定義事件引數的類別：

   ```csharp
   public class CustomEventArgs : EventArgs
   {
       public string CustomProperty1 {get; set;}
       public string CustomProperty2 {get; set;}
   }
   ```

1. 藉由新增 <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> 自訂事件的屬性注釋，將自訂事件與事件引數連接在一起。 類別不需要成員：

   ```csharp
   [EventHandler("oncustomevent", typeof(CustomEventArgs), enableStopPropagation: true, enablePreventDefault: true)]
   static class EventHandlers
   {
   }
   ```

1. 在一或多個 HTML 元素上註冊事件處理常式。 從委派處理常式方法中的 JAVAscript 存取傳入的資料：

   ```razor
   <button @oncustomevent="HandleCustomEvent">Handle</button>

   @code
   {
       void HandleCustomEvent(CustomEventArgs eventArgs)
       {
           // eventArgs.CustomProperty1
           // eventArgs.CustomProperty2
       }
   }
   ```

每當在 DOM 上引發自訂事件時，就會使用從 JAVAscript 傳遞的資料來呼叫事件處理常式。

如果您要嘗試引發自訂事件， [`bubbles`](https://developer.mozilla.org/docs/Web/API/Event/bubbles) 必須將其值設定為來啟用 `true` 。 否則，事件就不會到達 Blazor 處理常式以處理至 c # 自訂 <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> 方法。 如需詳細資訊，請參閱 [MDN Web 檔：事件反升](https://developer.mozilla.org/docs/Web/Guide/Events/Creating_and_triggering_events#event_bubbling)。

#### <a name="custom-clipboard-paste-event-example"></a>自訂剪貼簿貼上事件範例

下列範例會接收自訂剪貼簿貼上事件，其中包含貼上的時間和使用者的貼上文字。

宣告事件的自訂名稱 (`oncustompaste`) ，並 () 的 .net 類別 `CustomPasteEventArgs` 來保存這個事件的事件引數：

`CustomEvents.cs`:

```csharp
[EventHandler("oncustompaste", typeof(CustomPasteEventArgs), 
    enableStopPropagation: true, enablePreventDefault: true)]
public static class EventHandlers
{
}

public class CustomPasteEventArgs : EventArgs
{
    public DateTime EventTimestamp { get; set; }
    public string PastedData { get; set; }
}
```

新增 JavaScript 程式碼以提供子類別的資料 <xref:System.EventArgs> 。 在或檔案中 `wwwroot/index.html` `Pages/_Host.cshtml` ，將下列 `<script>` 標記和內容緊接在腳本之後 Blazor 。 下列範例只會處理貼上文字，但您可以使用任意的 JavaScript Api 來處理使用者貼上其他類型的資料（例如影像）。

`wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server 緊接在腳本之後) Blazor ：

```html
<script>
    Blazor.registerCustomEventType('custompaste', {
        browserEventName: 'paste',
        createEventArgs: event => {
            return {
                eventTimestamp: new Date(),
                pastedData: event.clipboardData.getData('text')
            };
        }
    });
</script>
```

上述程式碼會告知瀏覽器在發生原生 [`paste`](https://developer.mozilla.org/docs/Web/API/Element/paste_event) 事件時：

* 引發 `custompaste` 事件。
* 使用所述的自訂邏輯來提供事件引數資料：
  * 若為 `eventTimestamp` ，請建立新的日期。
  * 若為 `pastedData` ，請以文字形式取得剪貼簿資料。 如需詳細資訊，請參閱 [MDN Web 檔： ClipboardEvent. clipboardData](https://developer.mozilla.org/docs/Web/API/ClipboardEvent/clipboardData)。

.NET 和 JavaScript 之間的事件名稱慣例不同：

* 在 .NET 中，事件名稱前面會加上 " `on` "。
* 在 JavaScript 中，事件名稱沒有前置詞。

在 Razor 元件中，將自訂處理常式附加至元素。

`Pages/CustomPasteArguments.razor`:

```razor
@page "/custom-paste-arguments"

<label>
    Try pasting into the following text box:
    <input @oncustompaste="HandleCustomPaste" />
</label>

<p>
    @message
</p>

@code {
    private string message;

    private void HandleCustomPaste(CustomPasteEventArgs eventArgs)
    {
        message = $"At {eventArgs.EventTimestamp.ToShortTimeString()}, " +
            $"you pasted: {eventArgs.PastedData}";
    }
}
```

::: moniker-end

## <a name="lambda-expressions"></a>Lambda 運算式

[Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) 支援做為委派事件處理常式。

`Pages/EventHandlerExample4.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

::: moniker-end

使用 c # 方法參數（例如反復查看一組元素）來關閉其他值，通常會很方便。 下列範例會建立三個按鈕，每個按鈕都會呼叫 `UpdateHeading` 並傳遞下列資料：

*  (中) 的事件引數 <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> `e` 。
* 中的按鈕編號 `buttonNumber` 。

`Pages/EventHandlerExample5.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

::: moniker-end

> [!NOTE]
> 請勿直接在 lambda 運算式 **中使用迴圈** 變數，例如 `i` 在先前的 `for` 迴圈範例中。 否則，所有 lambda 運算式都會使用相同的變數，這會導致在所有 lambda 中使用相同的值。 一律在本機變數中捕捉變數的值，然後使用它。 在上述範例中：
>
> * 迴圈變數 `i` 指派給 `buttonNumber` 。
> * `buttonNumber` 在 lambda 運算式中使用。

## <a name="eventcallback"></a>>app >eventcallback

具有嵌套元件的常見案例會在子元件事件發生時，執行父元件的方法。 `onclick`子元件中發生的事件是常見的使用案例。 若要公開元件之間的事件，請使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 。 父元件可以將回呼方法指派給子元件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 。

下列 `Child` 元件示範如何設定按鈕的 `onclick` 處理常式，以接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 來自範例的委派 `ParentComponent` 。 的 <xref:Microsoft.AspNetCore.Components.EventCallback> 類型是 `MouseEventArgs` ，適用于來自周邊裝置的 `onclick` 事件。

`Shared/Child.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

::: moniker-end

`Parent`元件會將子系的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 設定為它的 `ShowMessage` 方法。

`Pages/Parent.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

::: moniker-end

當您在中選取此按鈕時 `ChildComponent` ：

* `Parent`呼叫元件的 `ShowMessage` 方法。 `message` 會更新並顯示在 `Parent` 元件中。
* [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged)回呼的方法 () 不需要呼叫 `ShowMessage` 。 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動呼叫以 rerender `Parent` 元件，就像在子事件處理常式中執行的子事件觸發程式元件轉譯資料流程一樣。 如需詳細資訊，請參閱<xref:blazor/components/rendering>。

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

偏好強型別 <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> 。 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 提供增強的錯誤意見反應給元件的使用者。 與其他 UI 事件處理常式類似，指定事件參數是選擇性的。 <xref:Microsoft.AspNetCore.Components.EventCallback>未傳遞任何值給回呼時使用。

## <a name="prevent-default-actions"></a>防止預設動作

使用指示詞 [`@on{DOM EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 屬性以防止事件的預設動作，其中 `{DOM EVENT}` 預留位置是 [檔物件模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)。

在輸入裝置上選取索引鍵，且元素焦點在文字方塊上時，瀏覽器通常會在文字方塊中顯示索引鍵的字元。 在下列範例中，藉由指定指示詞屬性來防止預設行為 `@onkeydown:preventDefault` 。 當焦點在元素上時 `<input>` ，計數器會以索引鍵序列移位遞增<kbd></kbd> + <kbd>+</kbd> 。 `+`未將字元指派給 `<input>` 元素的值。 如需的詳細資訊 `keydown` ，請參閱[ `MDN Web Docs: Document: keydown` 事件](https://developer.mozilla.org/docs/Web/API/Document/keydown_event)。

`Pages/EventHandlerExample6.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

::: moniker-end

指定 `@on{DOM EVENT}:preventDefault` 沒有值的屬性相當於 `@on{DOM EVENT}:preventDefault="true"` 。

運算式也是允許的屬性值。 在下列範例中， `shouldPreventDefault` 是 `bool` 設定為或的欄位 `true` `false` ：

```razor
<input @onkeydown:preventDefault="shouldPreventDefault" />

...

@code {
    private bool shouldPreventDefault = true;
}
```

## <a name="stop-event-propagation"></a>停止事件傳播

使用指示詞 [`@on{DOM EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 屬性停止事件傳播，其中 `{DOM EVENT}` 預留位置是 [檔物件模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)。

在下列範例中，選取此核取方塊可防止從第二個子系的事件 `<div>` 傳播到父系 `<div>` 。 因為傳播的 click 事件通常 `OnSelectParentDiv` 會引發方法，所以 `<div>` 除非已選取核取方塊，否則在父 div 訊息中選取第二個子代會產生。

`Pages/EventHandlerExample7.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a>將焦點放在元素

<xref:Microsoft.AspNetCore.Components.ElementReferenceExtensions.FocusAsync%2A>在專案[參考](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)上呼叫，以將專案放在程式碼中。 在下列範例中，選取要將焦點放在元素上的按鈕 `<input>` 。

`Pages/EventHandlerExample8.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample8.razor?highlight=16)]

::: moniker-end
