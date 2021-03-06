---
title: ASP.NET 核心 Blazor 事件處理
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
ms.openlocfilehash: 4c574a630176010ab30006b53c42e5cad7f93a54
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395184"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="10482-103">ASP.NET 核心 Blazor 事件處理</span><span class="sxs-lookup"><span data-stu-id="10482-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="10482-104">Razor 元件提供事件處理功能。</span><span class="sxs-lookup"><span data-stu-id="10482-104">Razor components provide event handling features.</span></span> <span data-ttu-id="10482-105">針對名為 (的 HTML 元素屬性 [`@on{EVENT}`](xref:mvc/views/razor#onevent) ，例如， `@onclick`) 具有委派類型值的，元件會將 Razor 屬性的值視為事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="10482-105">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="10482-106">下列程式碼會在 `UpdateHeading` UI 中選取按鈕時呼叫方法：</span><span class="sxs-lookup"><span data-stu-id="10482-106">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="10482-107">下列程式碼會在 `CheckChanged` UI 中的核取方塊變更時呼叫方法：</span><span class="sxs-lookup"><span data-stu-id="10482-107">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="10482-108">事件處理常式也可以是非同步，而且會傳回 <xref:System.Threading.Tasks.Task> 。</span><span class="sxs-lookup"><span data-stu-id="10482-108">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="10482-109">不需要手動呼叫 [StateHasChanged](xref:blazor/components/lifecycle#state-changes)。</span><span class="sxs-lookup"><span data-stu-id="10482-109">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="10482-110">例外狀況會在發生時記錄。</span><span class="sxs-lookup"><span data-stu-id="10482-110">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="10482-111">在下列範例中， `UpdateHeading` 當選取按鈕時，會以非同步方式呼叫：</span><span class="sxs-lookup"><span data-stu-id="10482-111">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="10482-112">事件引數類型</span><span class="sxs-lookup"><span data-stu-id="10482-112">Event argument types</span></span>

<span data-ttu-id="10482-113">針對某些事件，允許事件引數類型。</span><span class="sxs-lookup"><span data-stu-id="10482-113">For some events, event argument types are permitted.</span></span> <span data-ttu-id="10482-114">在事件方法定義中指定事件參數是選擇性的，只有在方法中使用事件種類時才需要。</span><span class="sxs-lookup"><span data-stu-id="10482-114">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="10482-115">在下列範例中， `MouseEventArgs` 事件引數是在方法中用 `ShowMessage` 來設定郵件內文：</span><span class="sxs-lookup"><span data-stu-id="10482-115">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="10482-116"><xref:System.EventArgs>下表顯示支援的。</span><span class="sxs-lookup"><span data-stu-id="10482-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="10482-117">事件</span><span class="sxs-lookup"><span data-stu-id="10482-117">Event</span></span>            | <span data-ttu-id="10482-118">類別</span><span class="sxs-lookup"><span data-stu-id="10482-118">Class</span></span>  | <span data-ttu-id="10482-119">DOM 事件和注意事項</span><span class="sxs-lookup"><span data-stu-id="10482-119">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="10482-120">剪貼簿</span><span class="sxs-lookup"><span data-stu-id="10482-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="10482-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="10482-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="10482-122">拖曳</span><span class="sxs-lookup"><span data-stu-id="10482-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="10482-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="10482-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="10482-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 並 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 按住拖曳的專案資料。</span><span class="sxs-lookup"><span data-stu-id="10482-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="10482-125">Blazor使用[JS Interop](xref:blazor/call-javascript-from-dotnet)搭配[HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)，在應用程式中執行拖放功能。</span><span class="sxs-lookup"><span data-stu-id="10482-125">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="10482-126">錯誤</span><span class="sxs-lookup"><span data-stu-id="10482-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="10482-127">事件</span><span class="sxs-lookup"><span data-stu-id="10482-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="10482-128">*一般*</span><span class="sxs-lookup"><span data-stu-id="10482-128">*General*</span></span><br><span data-ttu-id="10482-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="10482-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="10482-130">*剪貼簿*</span><span class="sxs-lookup"><span data-stu-id="10482-130">*Clipboard*</span></span><br><span data-ttu-id="10482-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="10482-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="10482-132">*輸入*</span><span class="sxs-lookup"><span data-stu-id="10482-132">*Input*</span></span><br><span data-ttu-id="10482-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="10482-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="10482-134">*媒體*</span><span class="sxs-lookup"><span data-stu-id="10482-134">*Media*</span></span><br><span data-ttu-id="10482-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="10482-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="10482-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保存屬性，以設定事件名稱和事件引數類型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="10482-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="10482-137">焦點</span><span class="sxs-lookup"><span data-stu-id="10482-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="10482-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="10482-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="10482-139">不包含的支援 `relatedTarget` 。</span><span class="sxs-lookup"><span data-stu-id="10482-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="10482-140">輸入</span><span class="sxs-lookup"><span data-stu-id="10482-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="10482-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="10482-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="10482-142">鍵盤</span><span class="sxs-lookup"><span data-stu-id="10482-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="10482-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="10482-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="10482-144">滑鼠</span><span class="sxs-lookup"><span data-stu-id="10482-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="10482-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="10482-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="10482-146">滑鼠指標</span><span class="sxs-lookup"><span data-stu-id="10482-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="10482-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="10482-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="10482-148">滑鼠滾輪</span><span class="sxs-lookup"><span data-stu-id="10482-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="10482-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="10482-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="10482-150">進度</span><span class="sxs-lookup"><span data-stu-id="10482-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="10482-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="10482-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="10482-152">觸控</span><span class="sxs-lookup"><span data-stu-id="10482-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="10482-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="10482-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="10482-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 代表觸控式裝置上的單一接觸點。</span><span class="sxs-lookup"><span data-stu-id="10482-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="10482-155">事件</span><span class="sxs-lookup"><span data-stu-id="10482-155">Event</span></span>            | <span data-ttu-id="10482-156">類別</span><span class="sxs-lookup"><span data-stu-id="10482-156">Class</span></span> | <span data-ttu-id="10482-157">DOM 事件和注意事項</span><span class="sxs-lookup"><span data-stu-id="10482-157">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="10482-158">剪貼簿</span><span class="sxs-lookup"><span data-stu-id="10482-158">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="10482-159">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="10482-159">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="10482-160">拖曳</span><span class="sxs-lookup"><span data-stu-id="10482-160">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="10482-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="10482-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="10482-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 並 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 按住拖曳的專案資料。</span><span class="sxs-lookup"><span data-stu-id="10482-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="10482-163">Blazor使用[JS Interop](xref:blazor/call-javascript-from-dotnet)搭配[HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)，在應用程式中執行拖放功能。</span><span class="sxs-lookup"><span data-stu-id="10482-163">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="10482-164">錯誤</span><span class="sxs-lookup"><span data-stu-id="10482-164">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="10482-165">事件</span><span class="sxs-lookup"><span data-stu-id="10482-165">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="10482-166">*一般*</span><span class="sxs-lookup"><span data-stu-id="10482-166">*General*</span></span><br><span data-ttu-id="10482-167">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="10482-167">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="10482-168">*剪貼簿*</span><span class="sxs-lookup"><span data-stu-id="10482-168">*Clipboard*</span></span><br><span data-ttu-id="10482-169">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="10482-169">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="10482-170">*輸入*</span><span class="sxs-lookup"><span data-stu-id="10482-170">*Input*</span></span><br><span data-ttu-id="10482-171">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="10482-171">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="10482-172">*媒體*</span><span class="sxs-lookup"><span data-stu-id="10482-172">*Media*</span></span><br><span data-ttu-id="10482-173">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="10482-173">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="10482-174"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保存屬性，以設定事件名稱和事件引數類型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="10482-174"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="10482-175">焦點</span><span class="sxs-lookup"><span data-stu-id="10482-175">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="10482-176">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="10482-176">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="10482-177">不包含的支援 `relatedTarget` 。</span><span class="sxs-lookup"><span data-stu-id="10482-177">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="10482-178">輸入</span><span class="sxs-lookup"><span data-stu-id="10482-178">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="10482-179">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="10482-179">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="10482-180">鍵盤</span><span class="sxs-lookup"><span data-stu-id="10482-180">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="10482-181">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="10482-181">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="10482-182">滑鼠</span><span class="sxs-lookup"><span data-stu-id="10482-182">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="10482-183">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="10482-183">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="10482-184">滑鼠指標</span><span class="sxs-lookup"><span data-stu-id="10482-184">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="10482-185">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="10482-185">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="10482-186">滑鼠滾輪</span><span class="sxs-lookup"><span data-stu-id="10482-186">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="10482-187">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="10482-187">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="10482-188">進度</span><span class="sxs-lookup"><span data-stu-id="10482-188">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="10482-189">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="10482-189">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="10482-190">觸控</span><span class="sxs-lookup"><span data-stu-id="10482-190">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="10482-191">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="10482-191">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="10482-192"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 代表觸控式裝置上的單一接觸點。</span><span class="sxs-lookup"><span data-stu-id="10482-192"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="10482-193">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="10482-193">For more information, see the following resources:</span></span>

* [<span data-ttu-id="10482-194">`EventArgs` ASP.NET Core 參考來源中的類別 (dotnet/aspnetcore `main` 分支) </span><span class="sxs-lookup"><span data-stu-id="10482-194">`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `main` branch)</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Components/Web/src/Web)

  [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

* <span data-ttu-id="10482-195">[MDN web 檔： GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含哪些 HTML 元素支援每個 DOM 事件的資訊。</span><span class="sxs-lookup"><span data-stu-id="10482-195">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="10482-196">Lambda 運算式</span><span class="sxs-lookup"><span data-stu-id="10482-196">Lambda expressions</span></span>

<span data-ttu-id="10482-197">您也可以使用[Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)：</span><span class="sxs-lookup"><span data-stu-id="10482-197">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="10482-198">更方便地關閉其他值，例如反覆運算一組專案時。</span><span class="sxs-lookup"><span data-stu-id="10482-198">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="10482-199">下列範例會建立三個按鈕，每個按鈕都會呼叫 `UpdateHeading` 傳遞事件引數 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) ，以及 `buttonNumber` 在 UI 中選取時 () 的按鈕編號：</span><span class="sxs-lookup"><span data-stu-id="10482-199">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="10482-200">請勿直接在 lambda 運算式 **中使用迴圈** 變數，例如 `i` 在先前的 `for` 迴圈範例中。</span><span class="sxs-lookup"><span data-stu-id="10482-200">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="10482-201">否則，所有 lambda 運算式都會使用相同的變數，這會導致在所有 lambda 中使用相同的值。</span><span class="sxs-lookup"><span data-stu-id="10482-201">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="10482-202">一律在本機變數中捕捉變數的值，然後使用它。</span><span class="sxs-lookup"><span data-stu-id="10482-202">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="10482-203">在上述範例中，會將迴圈變數 `i` 指派給 `buttonNumber` 。</span><span class="sxs-lookup"><span data-stu-id="10482-203">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="10482-204">>app >eventcallback</span><span class="sxs-lookup"><span data-stu-id="10482-204">EventCallback</span></span>

<span data-ttu-id="10482-205">具有嵌套元件的常見案例是，在子元件事件發生時，想要執行父元件的方法。</span><span class="sxs-lookup"><span data-stu-id="10482-205">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="10482-206">`onclick`子元件中發生的事件是常見的使用案例。</span><span class="sxs-lookup"><span data-stu-id="10482-206">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="10482-207">若要公開元件之間的事件，請使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="10482-207">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="10482-208">父元件可以將回呼方法指派給子元件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="10482-208">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="10482-209">`ChildComponent`範例應用程式 (`Components/ChildComponent.razor`) 示範如何設定按鈕的 `onclick` 處理常式，以接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 來自範例的委派 `ParentComponent` 。</span><span class="sxs-lookup"><span data-stu-id="10482-209">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="10482-210">的 <xref:Microsoft.AspNetCore.Components.EventCallback> 類型是 `MouseEventArgs` ，適用于來自周邊裝置的 `onclick` 事件：</span><span class="sxs-lookup"><span data-stu-id="10482-210">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="10482-211">會將 `ParentComponent` 子系的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 設定為它的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="10482-211">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="10482-212">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="10482-212">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="10482-213">當您在中選取此按鈕時 `ChildComponent` ：</span><span class="sxs-lookup"><span data-stu-id="10482-213">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="10482-214">`ParentComponent` `ShowMessage` 會呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="10482-214">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="10482-215">`messageText` 會更新並顯示在中 `ParentComponent` 。</span><span class="sxs-lookup"><span data-stu-id="10482-215">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="10482-216">[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes)回呼的方法 () 不需要呼叫 `ShowMessage` 。</span><span class="sxs-lookup"><span data-stu-id="10482-216">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="10482-217"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動呼叫以 rerender `ParentComponent` ，就像是事件處理常式在子系內執行的子事件觸發程式轉譯資料流程一樣。</span><span class="sxs-lookup"><span data-stu-id="10482-217"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span> <span data-ttu-id="10482-218">如需詳細資訊，請參閱<xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="10482-218">For more information, see <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="10482-219"><xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 允許非同步委派。</span><span class="sxs-lookup"><span data-stu-id="10482-219"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="10482-220"><xref:Microsoft.AspNetCore.Components.EventCallback> 是弱型別，可讓傳遞中的任何型別引數 `InvokeAsync(Object)` 。</span><span class="sxs-lookup"><span data-stu-id="10482-220"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="10482-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 是強型別，而且需要傳遞可 `T` `InvokeAsync(T)` 指派給的引數 `TValue` 。</span><span class="sxs-lookup"><span data-stu-id="10482-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="10482-222"><xref:Microsoft.AspNetCore.Components.EventCallback>使用或叫 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 用 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> ，並等待 <xref:System.Threading.Tasks.Task> ：</span><span class="sxs-lookup"><span data-stu-id="10482-222">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="10482-223"><xref:Microsoft.AspNetCore.Components.EventCallback> <xref:Microsoft.AspNetCore.Components.EventCallback%601> 針對事件處理和系結元件參數使用和。</span><span class="sxs-lookup"><span data-stu-id="10482-223">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="10482-224">偏好強型別 <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="10482-224">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="10482-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 提供更好的錯誤意見反應給元件的使用者。</span><span class="sxs-lookup"><span data-stu-id="10482-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="10482-226">與其他 UI 事件處理常式類似，指定事件參數是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="10482-226">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="10482-227"><xref:Microsoft.AspNetCore.Components.EventCallback>未傳遞任何值給回呼時使用。</span><span class="sxs-lookup"><span data-stu-id="10482-227">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="10482-228">防止預設動作</span><span class="sxs-lookup"><span data-stu-id="10482-228">Prevent default actions</span></span>

<span data-ttu-id="10482-229">使用指示詞 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 屬性以防止事件的預設動作。</span><span class="sxs-lookup"><span data-stu-id="10482-229">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="10482-230">在輸入裝置上選取索引鍵，且元素焦點在文字方塊上時，瀏覽器通常會在文字方塊中顯示索引鍵的字元。</span><span class="sxs-lookup"><span data-stu-id="10482-230">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="10482-231">在下列範例中，藉由指定指示詞屬性來防止預設行為 `@onkeypress:preventDefault` 。</span><span class="sxs-lookup"><span data-stu-id="10482-231">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="10482-232">計數器會遞增，而不會將索引 **+** 鍵捕捉到 `<input>` 元素的值中：</span><span class="sxs-lookup"><span data-stu-id="10482-232">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="10482-233">指定 `@on{EVENT}:preventDefault` 沒有值的屬性相當於 `@on{EVENT}:preventDefault="true"` 。</span><span class="sxs-lookup"><span data-stu-id="10482-233">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="10482-234">屬性的值也可以是運算式。</span><span class="sxs-lookup"><span data-stu-id="10482-234">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="10482-235">在下列範例中， `shouldPreventDefault` 是 `bool` 設定為或的欄位 `true` `false` ：</span><span class="sxs-lookup"><span data-stu-id="10482-235">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="10482-236">停止事件傳播</span><span class="sxs-lookup"><span data-stu-id="10482-236">Stop event propagation</span></span>

<span data-ttu-id="10482-237">使用指示詞 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 屬性停止事件傳播。</span><span class="sxs-lookup"><span data-stu-id="10482-237">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="10482-238">在下列範例中，選取此核取方塊可防止從第二個子系將事件 `<div>` 傳播至父系 `<div>` ：</span><span class="sxs-lookup"><span data-stu-id="10482-238">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

## <a name="focus-an-element"></a><span data-ttu-id="10482-239">將焦點放在元素</span><span class="sxs-lookup"><span data-stu-id="10482-239">Focus an element</span></span>

<span data-ttu-id="10482-240">`FocusAsync`在專案[參考](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)上呼叫，以在程式碼中將專案焦點放在元素上：</span><span class="sxs-lookup"><span data-stu-id="10482-240">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

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
