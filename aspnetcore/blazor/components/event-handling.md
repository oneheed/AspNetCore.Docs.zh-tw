---
title: ASP.NET Core Blazor 事件處理
author: guardrex
description: 瞭解 Blazor 的事件處理功能，包括事件引數類型、事件回呼，以及管理預設瀏覽器事件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
ms.openlocfilehash: 56306d305a61d549380e847ab2754fa41130e14e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94507807"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="41ee0-103">ASP.NET Core Blazor 事件處理</span><span class="sxs-lookup"><span data-stu-id="41ee0-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="41ee0-104">依 [Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="41ee0-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="41ee0-105">Razor 元件提供事件處理功能。</span><span class="sxs-lookup"><span data-stu-id="41ee0-105">Razor components provide event handling features.</span></span> <span data-ttu-id="41ee0-106">針對名為 (的 HTML 元素屬性 [`@on{EVENT}`](xref:mvc/views/razor#onevent) ，例如， `@onclick`) 具有委派類型值的，元件會將 Razor 屬性的值視為事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="41ee0-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="41ee0-107">下列程式碼會在 `UpdateHeading` UI 中選取按鈕時呼叫方法：</span><span class="sxs-lookup"><span data-stu-id="41ee0-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="41ee0-108">下列程式碼會在 `CheckChanged` UI 中的核取方塊變更時呼叫方法：</span><span class="sxs-lookup"><span data-stu-id="41ee0-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="41ee0-109">事件處理常式也可以是非同步，而且會傳回 <xref:System.Threading.Tasks.Task> 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="41ee0-110">不需要手動呼叫 [StateHasChanged](xref:blazor/components/lifecycle#state-changes)。</span><span class="sxs-lookup"><span data-stu-id="41ee0-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="41ee0-111">例外狀況會在發生時記錄。</span><span class="sxs-lookup"><span data-stu-id="41ee0-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="41ee0-112">在下列範例中， `UpdateHeading` 當選取按鈕時，會以非同步方式呼叫：</span><span class="sxs-lookup"><span data-stu-id="41ee0-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="41ee0-113">事件引數類型</span><span class="sxs-lookup"><span data-stu-id="41ee0-113">Event argument types</span></span>

<span data-ttu-id="41ee0-114">針對某些事件，允許事件引數類型。</span><span class="sxs-lookup"><span data-stu-id="41ee0-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="41ee0-115">在事件方法定義中指定事件參數是選擇性的，只有在方法中使用事件種類時才需要。</span><span class="sxs-lookup"><span data-stu-id="41ee0-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="41ee0-116">在下列範例中， `MouseEventArgs` 事件引數是在方法中用 `ShowMessage` 來設定郵件內文：</span><span class="sxs-lookup"><span data-stu-id="41ee0-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="41ee0-117"><xref:System.EventArgs>下表顯示支援的。</span><span class="sxs-lookup"><span data-stu-id="41ee0-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="41ee0-118">事件</span><span class="sxs-lookup"><span data-stu-id="41ee0-118">Event</span></span>            | <span data-ttu-id="41ee0-119">類別</span><span class="sxs-lookup"><span data-stu-id="41ee0-119">Class</span></span>  | <span data-ttu-id="41ee0-120">DOM 事件和注意事項</span><span class="sxs-lookup"><span data-stu-id="41ee0-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="41ee0-121">剪貼簿</span><span class="sxs-lookup"><span data-stu-id="41ee0-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="41ee0-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="41ee0-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="41ee0-123">拖曳</span><span class="sxs-lookup"><span data-stu-id="41ee0-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="41ee0-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="41ee0-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="41ee0-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 並 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 按住拖曳的專案資料。</span><span class="sxs-lookup"><span data-stu-id="41ee0-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="41ee0-126">Blazor使用[JS Interop](xref:blazor/call-javascript-from-dotnet)搭配[HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)，在應用程式中執行拖放功能。</span><span class="sxs-lookup"><span data-stu-id="41ee0-126">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="41ee0-127">錯誤</span><span class="sxs-lookup"><span data-stu-id="41ee0-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="41ee0-128">事件</span><span class="sxs-lookup"><span data-stu-id="41ee0-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="41ee0-129">*一般*</span><span class="sxs-lookup"><span data-stu-id="41ee0-129">*General*</span></span><br><span data-ttu-id="41ee0-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="41ee0-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="41ee0-131">*剪貼簿*</span><span class="sxs-lookup"><span data-stu-id="41ee0-131">*Clipboard*</span></span><br><span data-ttu-id="41ee0-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="41ee0-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="41ee0-133">*輸入*</span><span class="sxs-lookup"><span data-stu-id="41ee0-133">*Input*</span></span><br><span data-ttu-id="41ee0-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="41ee0-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="41ee0-135">*媒體*</span><span class="sxs-lookup"><span data-stu-id="41ee0-135">*Media*</span></span><br><span data-ttu-id="41ee0-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="41ee0-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="41ee0-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保存屬性，以設定事件名稱和事件引數類型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="41ee0-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="41ee0-138">焦點</span><span class="sxs-lookup"><span data-stu-id="41ee0-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="41ee0-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="41ee0-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="41ee0-140">不包含的支援 `relatedTarget` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="41ee0-141">輸入</span><span class="sxs-lookup"><span data-stu-id="41ee0-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="41ee0-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="41ee0-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="41ee0-143">鍵盤</span><span class="sxs-lookup"><span data-stu-id="41ee0-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="41ee0-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="41ee0-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="41ee0-145">滑鼠</span><span class="sxs-lookup"><span data-stu-id="41ee0-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="41ee0-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="41ee0-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="41ee0-147">滑鼠指標</span><span class="sxs-lookup"><span data-stu-id="41ee0-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="41ee0-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="41ee0-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="41ee0-149">滑鼠滾輪</span><span class="sxs-lookup"><span data-stu-id="41ee0-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="41ee0-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="41ee0-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="41ee0-151">進度</span><span class="sxs-lookup"><span data-stu-id="41ee0-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="41ee0-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="41ee0-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="41ee0-153">觸控</span><span class="sxs-lookup"><span data-stu-id="41ee0-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="41ee0-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="41ee0-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="41ee0-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 代表觸控式裝置上的單一接觸點。</span><span class="sxs-lookup"><span data-stu-id="41ee0-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="41ee0-156">事件</span><span class="sxs-lookup"><span data-stu-id="41ee0-156">Event</span></span>            | <span data-ttu-id="41ee0-157">類別</span><span class="sxs-lookup"><span data-stu-id="41ee0-157">Class</span></span> | <span data-ttu-id="41ee0-158">DOM 事件和注意事項</span><span class="sxs-lookup"><span data-stu-id="41ee0-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="41ee0-159">剪貼簿</span><span class="sxs-lookup"><span data-stu-id="41ee0-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="41ee0-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="41ee0-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="41ee0-161">拖曳</span><span class="sxs-lookup"><span data-stu-id="41ee0-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="41ee0-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="41ee0-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="41ee0-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 並 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 按住拖曳的專案資料。</span><span class="sxs-lookup"><span data-stu-id="41ee0-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="41ee0-164">Blazor使用[JS Interop](xref:blazor/call-javascript-from-dotnet)搭配[HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)，在應用程式中執行拖放功能。</span><span class="sxs-lookup"><span data-stu-id="41ee0-164">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="41ee0-165">錯誤</span><span class="sxs-lookup"><span data-stu-id="41ee0-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="41ee0-166">事件</span><span class="sxs-lookup"><span data-stu-id="41ee0-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="41ee0-167">*一般*</span><span class="sxs-lookup"><span data-stu-id="41ee0-167">*General*</span></span><br><span data-ttu-id="41ee0-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="41ee0-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="41ee0-169">*剪貼簿*</span><span class="sxs-lookup"><span data-stu-id="41ee0-169">*Clipboard*</span></span><br><span data-ttu-id="41ee0-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="41ee0-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="41ee0-171">*輸入*</span><span class="sxs-lookup"><span data-stu-id="41ee0-171">*Input*</span></span><br><span data-ttu-id="41ee0-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="41ee0-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="41ee0-173">*媒體*</span><span class="sxs-lookup"><span data-stu-id="41ee0-173">*Media*</span></span><br><span data-ttu-id="41ee0-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="41ee0-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="41ee0-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保存屬性，以設定事件名稱和事件引數類型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="41ee0-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="41ee0-176">焦點</span><span class="sxs-lookup"><span data-stu-id="41ee0-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="41ee0-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="41ee0-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="41ee0-178">不包含的支援 `relatedTarget` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="41ee0-179">輸入</span><span class="sxs-lookup"><span data-stu-id="41ee0-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="41ee0-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="41ee0-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="41ee0-181">鍵盤</span><span class="sxs-lookup"><span data-stu-id="41ee0-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="41ee0-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="41ee0-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="41ee0-183">滑鼠</span><span class="sxs-lookup"><span data-stu-id="41ee0-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="41ee0-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="41ee0-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="41ee0-185">滑鼠指標</span><span class="sxs-lookup"><span data-stu-id="41ee0-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="41ee0-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="41ee0-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="41ee0-187">滑鼠滾輪</span><span class="sxs-lookup"><span data-stu-id="41ee0-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="41ee0-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="41ee0-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="41ee0-189">進度</span><span class="sxs-lookup"><span data-stu-id="41ee0-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="41ee0-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="41ee0-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="41ee0-191">觸控</span><span class="sxs-lookup"><span data-stu-id="41ee0-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="41ee0-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="41ee0-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="41ee0-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 代表觸控式裝置上的單一接觸點。</span><span class="sxs-lookup"><span data-stu-id="41ee0-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="41ee0-194">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="41ee0-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="41ee0-195">[ `EventArgs` ASP.NET Core 參考來源 (dotnet/aspnetcore `master` 分支) 中的類別](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="41ee0-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="41ee0-196">在 `master` *下一個* ASP.NET Core 版本中，分支代表開發中的 API。</span><span class="sxs-lookup"><span data-stu-id="41ee0-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="41ee0-197">針對最新版本，請選取適當的 GitHub 存放庫分支 (例如 `release/3.1`) 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="41ee0-198">[MDN web 檔： GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含哪些 HTML 元素支援每個 DOM 事件的資訊。</span><span class="sxs-lookup"><span data-stu-id="41ee0-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="41ee0-199">Lambda 運算式</span><span class="sxs-lookup"><span data-stu-id="41ee0-199">Lambda expressions</span></span>

<span data-ttu-id="41ee0-200">您也可以使用[Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)：</span><span class="sxs-lookup"><span data-stu-id="41ee0-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="41ee0-201">更方便地關閉其他值，例如反覆運算一組專案時。</span><span class="sxs-lookup"><span data-stu-id="41ee0-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="41ee0-202">下列範例會建立三個按鈕，每個按鈕都會呼叫 `UpdateHeading` 傳遞事件引數 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) ，以及 `buttonNumber` 在 UI 中選取時 () 的按鈕編號：</span><span class="sxs-lookup"><span data-stu-id="41ee0-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="41ee0-203">請勿直接在 lambda 運算式 **中使用迴圈** 變數，例如 `i` 在先前的 `for` 迴圈範例中。</span><span class="sxs-lookup"><span data-stu-id="41ee0-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="41ee0-204">否則，所有 lambda 運算式都會使用相同的變數，這會導致在所有 lambda 中使用相同的值。</span><span class="sxs-lookup"><span data-stu-id="41ee0-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="41ee0-205">一律在本機變數中捕捉變數的值，然後使用它。</span><span class="sxs-lookup"><span data-stu-id="41ee0-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="41ee0-206">在上述範例中，會將迴圈變數 `i` 指派給 `buttonNumber` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="41ee0-207">>app >eventcallback</span><span class="sxs-lookup"><span data-stu-id="41ee0-207">EventCallback</span></span>

<span data-ttu-id="41ee0-208">具有嵌套元件的常見案例是，在子元件事件發生時，想要執行父元件的方法。</span><span class="sxs-lookup"><span data-stu-id="41ee0-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="41ee0-209">`onclick`子元件中發生的事件是常見的使用案例。</span><span class="sxs-lookup"><span data-stu-id="41ee0-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="41ee0-210">若要公開元件之間的事件，請使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="41ee0-211">父元件可以將回呼方法指派給子元件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="41ee0-212">`ChildComponent`範例應用程式 (`Components/ChildComponent.razor`) 示範如何設定按鈕的 `onclick` 處理常式，以接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 來自範例的委派 `ParentComponent` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="41ee0-213">的 <xref:Microsoft.AspNetCore.Components.EventCallback> 類型是 `MouseEventArgs` ，適用于來自周邊裝置的 `onclick` 事件：</span><span class="sxs-lookup"><span data-stu-id="41ee0-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="41ee0-214">會將 `ParentComponent` 子系的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 設定為它的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="41ee0-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="41ee0-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="41ee0-215">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="41ee0-216">當您在中選取此按鈕時 `ChildComponent` ：</span><span class="sxs-lookup"><span data-stu-id="41ee0-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="41ee0-217">`ParentComponent` `ShowMessage` 會呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="41ee0-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="41ee0-218">`messageText` 會更新並顯示在中 `ParentComponent` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="41ee0-219">[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes)回呼的方法 () 不需要呼叫 `ShowMessage` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="41ee0-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動呼叫以 rerender `ParentComponent` ，就像是事件處理常式在子系內執行的子事件觸發程式轉譯資料流程一樣。</span><span class="sxs-lookup"><span data-stu-id="41ee0-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="41ee0-221"><xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 允許非同步委派。</span><span class="sxs-lookup"><span data-stu-id="41ee0-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="41ee0-222"><xref:Microsoft.AspNetCore.Components.EventCallback> 是弱型別，可讓傳遞中的任何型別引數 `InvokeAsync(Object)` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="41ee0-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 是強型別，而且需要傳遞可 `T` `InvokeAsync(T)` 指派給的引數 `TValue` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="41ee0-224"><xref:Microsoft.AspNetCore.Components.EventCallback>使用或叫 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 用 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> ，並等待 <xref:System.Threading.Tasks.Task> ：</span><span class="sxs-lookup"><span data-stu-id="41ee0-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="41ee0-225"><xref:Microsoft.AspNetCore.Components.EventCallback> <xref:Microsoft.AspNetCore.Components.EventCallback%601> 針對事件處理和系結元件參數使用和。</span><span class="sxs-lookup"><span data-stu-id="41ee0-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="41ee0-226">偏好強型別 <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="41ee0-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 提供更好的錯誤意見反應給元件的使用者。</span><span class="sxs-lookup"><span data-stu-id="41ee0-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="41ee0-228">與其他 UI 事件處理常式類似，指定事件參數是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="41ee0-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="41ee0-229"><xref:Microsoft.AspNetCore.Components.EventCallback>未傳遞任何值給回呼時使用。</span><span class="sxs-lookup"><span data-stu-id="41ee0-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="41ee0-230">防止預設動作</span><span class="sxs-lookup"><span data-stu-id="41ee0-230">Prevent default actions</span></span>

<span data-ttu-id="41ee0-231">使用指示詞 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 屬性以防止事件的預設動作。</span><span class="sxs-lookup"><span data-stu-id="41ee0-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="41ee0-232">在輸入裝置上選取索引鍵，且元素焦點在文字方塊上時，瀏覽器通常會在文字方塊中顯示索引鍵的字元。</span><span class="sxs-lookup"><span data-stu-id="41ee0-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="41ee0-233">在下列範例中，藉由指定指示詞屬性來防止預設行為 `@onkeypress:preventDefault` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="41ee0-234">計數器會遞增，而不會將索引 **+** 鍵捕捉到 `<input>` 元素的值中：</span><span class="sxs-lookup"><span data-stu-id="41ee0-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="41ee0-235">指定 `@on{EVENT}:preventDefault` 沒有值的屬性相當於 `@on{EVENT}:preventDefault="true"` 。</span><span class="sxs-lookup"><span data-stu-id="41ee0-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="41ee0-236">屬性的值也可以是運算式。</span><span class="sxs-lookup"><span data-stu-id="41ee0-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="41ee0-237">在下列範例中， `shouldPreventDefault` 是 `bool` 設定為或的欄位 `true` `false` ：</span><span class="sxs-lookup"><span data-stu-id="41ee0-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="41ee0-238">停止事件傳播</span><span class="sxs-lookup"><span data-stu-id="41ee0-238">Stop event propagation</span></span>

<span data-ttu-id="41ee0-239">使用指示詞 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 屬性停止事件傳播。</span><span class="sxs-lookup"><span data-stu-id="41ee0-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="41ee0-240">在下列範例中，選取此核取方塊可防止從第二個子系將事件 `<div>` 傳播至父系 `<div>` ：</span><span class="sxs-lookup"><span data-stu-id="41ee0-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

## <a name="focus-an-element"></a><span data-ttu-id="41ee0-241">將焦點放在元素</span><span class="sxs-lookup"><span data-stu-id="41ee0-241">Focus an element</span></span>

<span data-ttu-id="41ee0-242">`FocusAsync`在專案[參考](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)上呼叫，以在程式碼中將專案焦點放在元素上：</span><span class="sxs-lookup"><span data-stu-id="41ee0-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

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
