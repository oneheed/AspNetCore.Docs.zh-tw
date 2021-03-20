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
ms.openlocfilehash: a768934bf761632803b667b32afeab686ebed6f9
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711044"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="7a7be-103">ASP.NET Core Blazor 事件處理</span><span class="sxs-lookup"><span data-stu-id="7a7be-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="7a7be-104">Razor使用語法指定元件標記中的委派事件處理常式 [`@on{DOM EVENT}="{DELEGATE}"`](xref:mvc/views/razor#onevent) Razor ：</span><span class="sxs-lookup"><span data-stu-id="7a7be-104">Specify delegate event handlers in Razor component markup with [`@on{DOM EVENT}="{DELEGATE}"`](xref:mvc/views/razor#onevent) Razor syntax:</span></span>

* <span data-ttu-id="7a7be-105">`{DOM EVENT}`預留位置是[檔物件模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events) (例如 `click`) 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-105">The `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events) (for example, `click`).</span></span>
* <span data-ttu-id="7a7be-106">`{DELEGATE}`預留位置是 c # 委派事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a7be-106">The `{DELEGATE}` placeholder is the C# delegate event handler.</span></span>

<span data-ttu-id="7a7be-107">針對事件處理：</span><span class="sxs-lookup"><span data-stu-id="7a7be-107">For event handling:</span></span>

* <span data-ttu-id="7a7be-108">支援傳回的非同步委派事件處理常式 <xref:System.Threading.Tasks.Task> 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-108">Asynchronous delegate event handlers that return a <xref:System.Threading.Tasks.Task> are supported.</span></span>
* <span data-ttu-id="7a7be-109">委派事件處理常式會自動觸發 UI 呈現，因此不需要手動呼叫 [StateHasChanged](xref:blazor/components/lifecycle#state-changes)。</span><span class="sxs-lookup"><span data-stu-id="7a7be-109">Delegate event handlers automatically trigger a UI render, so there's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span>
* <span data-ttu-id="7a7be-110">系統會記錄例外狀況。</span><span class="sxs-lookup"><span data-stu-id="7a7be-110">Exceptions are logged.</span></span>

<span data-ttu-id="7a7be-111">下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="7a7be-111">The following code:</span></span>

* <span data-ttu-id="7a7be-112">在 `UpdateHeading` UI 中選取按鈕時，呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="7a7be-112">Calls the `UpdateHeading` method when the button is selected in the UI.</span></span>
* <span data-ttu-id="7a7be-113">`CheckChanged`當 UI 中的核取方塊變更時，呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="7a7be-113">Calls the `CheckChanged` method when the check box is changed in the UI.</span></span>

<span data-ttu-id="7a7be-114">`Pages/EventHandlerExample1.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-114">`Pages/EventHandlerExample1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

::: moniker-end

<span data-ttu-id="7a7be-115">在下列範例中， `UpdateHeading` ：</span><span class="sxs-lookup"><span data-stu-id="7a7be-115">In the following example, `UpdateHeading`:</span></span>

* <span data-ttu-id="7a7be-116">當選取按鈕時，會以非同步方式呼叫。</span><span class="sxs-lookup"><span data-stu-id="7a7be-116">Is called asynchronously when the button is selected.</span></span>
* <span data-ttu-id="7a7be-117">在更新標題之前等候兩秒鐘。</span><span class="sxs-lookup"><span data-stu-id="7a7be-117">Waits two seconds before updating the heading.</span></span>

<span data-ttu-id="7a7be-118">`Pages/EventHandlerExample2.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-118">`Pages/EventHandlerExample2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

::: moniker-end

## <a name="event-arguments"></a><span data-ttu-id="7a7be-119">事件引數</span><span class="sxs-lookup"><span data-stu-id="7a7be-119">Event arguments</span></span>

::: moniker range=">= aspnetcore-6.0"

### <a name="built-in-event-arguments"></a><span data-ttu-id="7a7be-120">內建事件引數</span><span class="sxs-lookup"><span data-stu-id="7a7be-120">Built-in event arguments</span></span>

::: moniker-end

<span data-ttu-id="7a7be-121">針對支援事件引數類型的事件，只有在方法中使用事件種類時，才需要在事件方法定義中指定事件參數。</span><span class="sxs-lookup"><span data-stu-id="7a7be-121">For events that support an event argument type, specifying an event parameter in the event method definition is only necessary if the event type is used in the method.</span></span> <span data-ttu-id="7a7be-122">在下列範例中， <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> 會在方法中用 `ReportPointerLocation` 來設定郵件內文，以便在使用者選取 UI 中的按鈕時報告滑鼠座標。</span><span class="sxs-lookup"><span data-stu-id="7a7be-122">In the following example, <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> is used in the `ReportPointerLocation` method to set message text that reports the mouse coordinates when the user selects a button in the UI.</span></span>

<span data-ttu-id="7a7be-123">`Pages/EventHandlerExample3.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-123">`Pages/EventHandlerExample3.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

::: moniker-end

<span data-ttu-id="7a7be-124"><xref:System.EventArgs>下表顯示支援的。</span><span class="sxs-lookup"><span data-stu-id="7a7be-124">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="7a7be-125">事件</span><span class="sxs-lookup"><span data-stu-id="7a7be-125">Event</span></span>            | <span data-ttu-id="7a7be-126">類別</span><span class="sxs-lookup"><span data-stu-id="7a7be-126">Class</span></span>  | <span data-ttu-id="7a7be-127">[檔物件模型 (DOM) ](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件和附注</span><span class="sxs-lookup"><span data-stu-id="7a7be-127">[Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) events and notes</span></span> |
| ---------------- | ------ | --- |
| <span data-ttu-id="7a7be-128">剪貼簿</span><span class="sxs-lookup"><span data-stu-id="7a7be-128">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="7a7be-129">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="7a7be-129">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="7a7be-130">拖曳</span><span class="sxs-lookup"><span data-stu-id="7a7be-130">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="7a7be-131">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="7a7be-131">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="7a7be-132"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 並 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 按住拖曳的專案資料。</span><span class="sxs-lookup"><span data-stu-id="7a7be-132"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="7a7be-133">Blazor使用[JS Interop](xref:blazor/call-javascript-from-dotnet)搭配[HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)，在應用程式中執行拖放功能。</span><span class="sxs-lookup"><span data-stu-id="7a7be-133">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="7a7be-134">錯誤</span><span class="sxs-lookup"><span data-stu-id="7a7be-134">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="7a7be-135">事件</span><span class="sxs-lookup"><span data-stu-id="7a7be-135">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="7a7be-136">*一般*</span><span class="sxs-lookup"><span data-stu-id="7a7be-136">*General*</span></span><br><span data-ttu-id="7a7be-137">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="7a7be-137">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="7a7be-138">*剪貼簿*</span><span class="sxs-lookup"><span data-stu-id="7a7be-138">*Clipboard*</span></span><br><span data-ttu-id="7a7be-139">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="7a7be-139">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="7a7be-140">*輸入*</span><span class="sxs-lookup"><span data-stu-id="7a7be-140">*Input*</span></span><br><span data-ttu-id="7a7be-141">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="7a7be-141">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="7a7be-142">*媒體*</span><span class="sxs-lookup"><span data-stu-id="7a7be-142">*Media*</span></span><br><span data-ttu-id="7a7be-143">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="7a7be-143">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="7a7be-144"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保存屬性，以設定事件名稱和事件引數類型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="7a7be-144"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="7a7be-145">焦點</span><span class="sxs-lookup"><span data-stu-id="7a7be-145">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="7a7be-146">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="7a7be-146">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="7a7be-147">不包含的支援 `relatedTarget` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-147">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="7a7be-148">輸入</span><span class="sxs-lookup"><span data-stu-id="7a7be-148">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="7a7be-149">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="7a7be-149">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="7a7be-150">鍵盤</span><span class="sxs-lookup"><span data-stu-id="7a7be-150">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="7a7be-151">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="7a7be-151">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="7a7be-152">滑鼠</span><span class="sxs-lookup"><span data-stu-id="7a7be-152">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="7a7be-153">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="7a7be-153">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="7a7be-154">滑鼠指標</span><span class="sxs-lookup"><span data-stu-id="7a7be-154">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="7a7be-155">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="7a7be-155">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="7a7be-156">滑鼠滾輪</span><span class="sxs-lookup"><span data-stu-id="7a7be-156">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="7a7be-157">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="7a7be-157">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="7a7be-158">進度</span><span class="sxs-lookup"><span data-stu-id="7a7be-158">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="7a7be-159">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="7a7be-159">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="7a7be-160">觸控</span><span class="sxs-lookup"><span data-stu-id="7a7be-160">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="7a7be-161">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="7a7be-161">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="7a7be-162"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 代表觸控式裝置上的單一接觸點。</span><span class="sxs-lookup"><span data-stu-id="7a7be-162"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="7a7be-163">事件</span><span class="sxs-lookup"><span data-stu-id="7a7be-163">Event</span></span>            | <span data-ttu-id="7a7be-164">類別</span><span class="sxs-lookup"><span data-stu-id="7a7be-164">Class</span></span> | <span data-ttu-id="7a7be-165">[檔物件模型 (DOM) ](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 事件和附注</span><span class="sxs-lookup"><span data-stu-id="7a7be-165">[Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) events and notes</span></span> |
| ---------------- | ----- | --- |
| <span data-ttu-id="7a7be-166">剪貼簿</span><span class="sxs-lookup"><span data-stu-id="7a7be-166">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="7a7be-167">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="7a7be-167">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="7a7be-168">拖曳</span><span class="sxs-lookup"><span data-stu-id="7a7be-168">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="7a7be-169">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="7a7be-169">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="7a7be-170"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> 並 <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> 按住拖曳的專案資料。</span><span class="sxs-lookup"><span data-stu-id="7a7be-170"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="7a7be-171">Blazor使用[JS Interop](xref:blazor/call-javascript-from-dotnet)搭配[HTML 拖放 API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)，在應用程式中執行拖放功能。</span><span class="sxs-lookup"><span data-stu-id="7a7be-171">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="7a7be-172">錯誤</span><span class="sxs-lookup"><span data-stu-id="7a7be-172">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="7a7be-173">事件</span><span class="sxs-lookup"><span data-stu-id="7a7be-173">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="7a7be-174">*一般*</span><span class="sxs-lookup"><span data-stu-id="7a7be-174">*General*</span></span><br><span data-ttu-id="7a7be-175">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="7a7be-175">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="7a7be-176">*剪貼簿*</span><span class="sxs-lookup"><span data-stu-id="7a7be-176">*Clipboard*</span></span><br><span data-ttu-id="7a7be-177">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="7a7be-177">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="7a7be-178">*輸入*</span><span class="sxs-lookup"><span data-stu-id="7a7be-178">*Input*</span></span><br><span data-ttu-id="7a7be-179">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="7a7be-179">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="7a7be-180">*媒體*</span><span class="sxs-lookup"><span data-stu-id="7a7be-180">*Media*</span></span><br><span data-ttu-id="7a7be-181">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="7a7be-181">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="7a7be-182"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> 保存屬性，以設定事件名稱和事件引數類型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="7a7be-182"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="7a7be-183">焦點</span><span class="sxs-lookup"><span data-stu-id="7a7be-183">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="7a7be-184">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="7a7be-184">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="7a7be-185">不包含的支援 `relatedTarget` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-185">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="7a7be-186">輸入</span><span class="sxs-lookup"><span data-stu-id="7a7be-186">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="7a7be-187">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="7a7be-187">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="7a7be-188">鍵盤</span><span class="sxs-lookup"><span data-stu-id="7a7be-188">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="7a7be-189">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="7a7be-189">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="7a7be-190">滑鼠</span><span class="sxs-lookup"><span data-stu-id="7a7be-190">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="7a7be-191">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="7a7be-191">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="7a7be-192">滑鼠指標</span><span class="sxs-lookup"><span data-stu-id="7a7be-192">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="7a7be-193">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="7a7be-193">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="7a7be-194">滑鼠滾輪</span><span class="sxs-lookup"><span data-stu-id="7a7be-194">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="7a7be-195">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="7a7be-195">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="7a7be-196">進度</span><span class="sxs-lookup"><span data-stu-id="7a7be-196">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="7a7be-197">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="7a7be-197">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="7a7be-198">觸控</span><span class="sxs-lookup"><span data-stu-id="7a7be-198">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="7a7be-199">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="7a7be-199">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="7a7be-200"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> 代表觸控式裝置上的單一接觸點。</span><span class="sxs-lookup"><span data-stu-id="7a7be-200"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="7a7be-201">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="7a7be-201">For more information, see the following resources:</span></span>

* [<span data-ttu-id="7a7be-202">`EventArgs` ASP.NET Core 參考來源中的類別 (dotnet/aspnetcore `main` 分支) </span><span class="sxs-lookup"><span data-stu-id="7a7be-202">`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `main` branch)</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Components/Web/src/Web)

  [!INCLUDE[](~/blazor/includes/aspnetcore-repo-ref-source-links.md)]

* <span data-ttu-id="7a7be-203">[MDN web 檔： GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers)：包含哪些 HTML 元素支援每個 DOM 事件的資訊。</span><span class="sxs-lookup"><span data-stu-id="7a7be-203">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

::: moniker range=">= aspnetcore-6.0"

### <a name="custom-event-arguments"></a><span data-ttu-id="7a7be-204">自訂事件引數</span><span class="sxs-lookup"><span data-stu-id="7a7be-204">Custom event arguments</span></span>

<span data-ttu-id="7a7be-205">Blazor 支援自訂事件引數，可讓您使用自訂事件將任意資料傳遞給 .NET 事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a7be-205">Blazor supports custom event arguments, which enable you to pass arbitrary data to .NET event handlers with custom events.</span></span>

#### <a name="general-configuration"></a><span data-ttu-id="7a7be-206">一般設定</span><span class="sxs-lookup"><span data-stu-id="7a7be-206">General configuration</span></span>

<span data-ttu-id="7a7be-207">使用自訂事件引數的自訂事件，通常會透過下列步驟啟用。</span><span class="sxs-lookup"><span data-stu-id="7a7be-207">Custom events with custom event arguments are generally enabled with the following steps.</span></span>

1. <span data-ttu-id="7a7be-208">在 JavaScript 中，定義函式以從來源事件建立自訂事件引數物件：</span><span class="sxs-lookup"><span data-stu-id="7a7be-208">In JavaScript, define a function for building the custom event argument object from the source event:</span></span>

   ```javascript
   function eventArgsCreator(event) { 
     return {
       customProperty1: 'any value for property 1',
       customProperty2: event.srcElement.value
     };
   }
   ```

1. <span data-ttu-id="7a7be-209">使用先前的處理常式，在 () 中註冊自訂事件， `wwwroot/index.html` Blazor WebAssembly 或在下列專案 `Pages/_Host.cshtml` Blazor Server 之後立即 () Blazor `<script>` ：</span><span class="sxs-lookup"><span data-stu-id="7a7be-209">Register the custom event with the preceding handler in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server) immediately after the Blazor `<script>`:</span></span>

   ```html
   <script>
       Blazor.registerCustomEventType('customevent', {
           createEventArgs: eventArgsCreator;
       });
   </script>
   ```

   > [!NOTE]
   > <span data-ttu-id="7a7be-210">的呼叫 `registerCustomEventType` 只會在每個事件一次的腳本中執行。</span><span class="sxs-lookup"><span data-stu-id="7a7be-210">The call to `registerCustomEventType` is performed in a script only once per event.</span></span>

1. <span data-ttu-id="7a7be-211">定義事件引數的類別：</span><span class="sxs-lookup"><span data-stu-id="7a7be-211">Define a class for the event arguments:</span></span>

   ```csharp
   public class CustomEventArgs : EventArgs
   {
       public string CustomProperty1 {get; set;}
       public string CustomProperty2 {get; set;}
   }
   ```

1. <span data-ttu-id="7a7be-212">藉由新增 <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> 自訂事件的屬性注釋，將自訂事件與事件引數連接在一起。</span><span class="sxs-lookup"><span data-stu-id="7a7be-212">Wire up the custom event with the event arguments by adding an <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> attribute annotation for the custom event.</span></span> <span data-ttu-id="7a7be-213">類別不需要成員：</span><span class="sxs-lookup"><span data-stu-id="7a7be-213">The class doesn't require members:</span></span>

   ```csharp
   [EventHandler("oncustomevent", typeof(CustomEventArgs), enableStopPropagation: true, enablePreventDefault: true)]
   static class EventHandlers
   {
   }
   ```

1. <span data-ttu-id="7a7be-214">在一或多個 HTML 元素上註冊事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a7be-214">Register the event handler on one or more HTML elements.</span></span> <span data-ttu-id="7a7be-215">從委派處理常式方法中的 JAVAscript 存取傳入的資料：</span><span class="sxs-lookup"><span data-stu-id="7a7be-215">Access the data that was passed in from Javascript in the delegate handler method:</span></span>

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

<span data-ttu-id="7a7be-216">每當在 DOM 上引發自訂事件時，就會使用從 JAVAscript 傳遞的資料來呼叫事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a7be-216">Whenever the custom event is fired on the DOM, the event handler is called with the data passed from the Javascript.</span></span>

<span data-ttu-id="7a7be-217">如果您要嘗試引發自訂事件， [`bubbles`](https://developer.mozilla.org/docs/Web/API/Event/bubbles) 必須將其值設定為來啟用 `true` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-217">If you're attempting to fire a custom event, [`bubbles`](https://developer.mozilla.org/docs/Web/API/Event/bubbles) must be enabled by setting its value to `true`.</span></span> <span data-ttu-id="7a7be-218">否則，事件就不會到達 Blazor 處理常式以處理至 c # 自訂 <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> 方法。</span><span class="sxs-lookup"><span data-stu-id="7a7be-218">Otherwise, the event doesn't reach the Blazor handler for processing into the C# custom <xref:Microsoft.AspNetCore.Components.EventHandlerAttribute> method.</span></span> <span data-ttu-id="7a7be-219">如需詳細資訊，請參閱 [MDN Web 檔：事件反升](https://developer.mozilla.org/docs/Web/Guide/Events/Creating_and_triggering_events#event_bubbling)。</span><span class="sxs-lookup"><span data-stu-id="7a7be-219">For more information, see [MDN Web Docs: Event bubbling](https://developer.mozilla.org/docs/Web/Guide/Events/Creating_and_triggering_events#event_bubbling).</span></span>

#### <a name="custom-clipboard-paste-event-example"></a><span data-ttu-id="7a7be-220">自訂剪貼簿貼上事件範例</span><span class="sxs-lookup"><span data-stu-id="7a7be-220">Custom clipboard paste event example</span></span>

<span data-ttu-id="7a7be-221">下列範例會接收自訂剪貼簿貼上事件，其中包含貼上的時間和使用者的貼上文字。</span><span class="sxs-lookup"><span data-stu-id="7a7be-221">The following example receives a custom clipboard paste event that includes the time of the paste and the user's pasted text.</span></span>

<span data-ttu-id="7a7be-222">宣告事件的自訂名稱 (`oncustompaste`) ，並 () 的 .net 類別 `CustomPasteEventArgs` 來保存這個事件的事件引數：</span><span class="sxs-lookup"><span data-stu-id="7a7be-222">Declare a custom name (`oncustompaste`) for the event and a .NET class (`CustomPasteEventArgs`) to hold the event arguments for this event:</span></span>

<span data-ttu-id="7a7be-223">`CustomEvents.cs`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-223">`CustomEvents.cs`:</span></span>

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

<span data-ttu-id="7a7be-224">新增 JavaScript 程式碼以提供子類別的資料 <xref:System.EventArgs> 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-224">Add JavaScript code to supply data for the <xref:System.EventArgs> subclass.</span></span> <span data-ttu-id="7a7be-225">在或檔案中 `wwwroot/index.html` `Pages/_Host.cshtml` ，將下列 `<script>` 標記和內容緊接在腳本之後 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-225">In the `wwwroot/index.html` or `Pages/_Host.cshtml` file, add the following `<script>` tag and content immediately after the Blazor script.</span></span> <span data-ttu-id="7a7be-226">下列範例只會處理貼上文字，但您可以使用任意的 JavaScript Api 來處理使用者貼上其他類型的資料（例如影像）。</span><span class="sxs-lookup"><span data-stu-id="7a7be-226">The following example only handles pasting text, but you could use arbitrary JavaScript APIs to deal with users pasting other types of data, such as images.</span></span>

<span data-ttu-id="7a7be-227">`wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server 緊接在腳本之後) Blazor ：</span><span class="sxs-lookup"><span data-stu-id="7a7be-227">`wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server) immediately after the Blazor script:</span></span>

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

<span data-ttu-id="7a7be-228">上述程式碼會告知瀏覽器在發生原生 [`paste`](https://developer.mozilla.org/docs/Web/API/Element/paste_event) 事件時：</span><span class="sxs-lookup"><span data-stu-id="7a7be-228">The preceding code tells the browser that when a native [`paste`](https://developer.mozilla.org/docs/Web/API/Element/paste_event) event occurs:</span></span>

* <span data-ttu-id="7a7be-229">引發 `custompaste` 事件。</span><span class="sxs-lookup"><span data-stu-id="7a7be-229">Raise a `custompaste` event.</span></span>
* <span data-ttu-id="7a7be-230">使用所述的自訂邏輯來提供事件引數資料：</span><span class="sxs-lookup"><span data-stu-id="7a7be-230">Supply the event arguments data using the custom logic stated:</span></span>
  * <span data-ttu-id="7a7be-231">若為 `eventTimestamp` ，請建立新的日期。</span><span class="sxs-lookup"><span data-stu-id="7a7be-231">For the `eventTimestamp`, create a new date.</span></span>
  * <span data-ttu-id="7a7be-232">若為 `pastedData` ，請以文字形式取得剪貼簿資料。</span><span class="sxs-lookup"><span data-stu-id="7a7be-232">For the `pastedData`, get the clipboard data as text.</span></span> <span data-ttu-id="7a7be-233">如需詳細資訊，請參閱 [MDN Web 檔： ClipboardEvent. clipboardData](https://developer.mozilla.org/docs/Web/API/ClipboardEvent/clipboardData)。</span><span class="sxs-lookup"><span data-stu-id="7a7be-233">For more information, see [MDN Web Docs: ClipboardEvent.clipboardData](https://developer.mozilla.org/docs/Web/API/ClipboardEvent/clipboardData).</span></span>

<span data-ttu-id="7a7be-234">.NET 和 JavaScript 之間的事件名稱慣例不同：</span><span class="sxs-lookup"><span data-stu-id="7a7be-234">Event name conventions differ between .NET and JavaScript:</span></span>

* <span data-ttu-id="7a7be-235">在 .NET 中，事件名稱前面會加上 " `on` "。</span><span class="sxs-lookup"><span data-stu-id="7a7be-235">In .NET, event names are prefixed with "`on`".</span></span>
* <span data-ttu-id="7a7be-236">在 JavaScript 中，事件名稱沒有前置詞。</span><span class="sxs-lookup"><span data-stu-id="7a7be-236">In JavaScript, event names don't have a prefix.</span></span>

<span data-ttu-id="7a7be-237">在 Razor 元件中，將自訂處理常式附加至元素。</span><span class="sxs-lookup"><span data-stu-id="7a7be-237">In a Razor component, attach the custom handler to an element.</span></span>

<span data-ttu-id="7a7be-238">`Pages/CustomPasteArguments.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-238">`Pages/CustomPasteArguments.razor`:</span></span>

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

## <a name="lambda-expressions"></a><span data-ttu-id="7a7be-239">Lambda 運算式</span><span class="sxs-lookup"><span data-stu-id="7a7be-239">Lambda expressions</span></span>

<span data-ttu-id="7a7be-240">[Lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) 支援做為委派事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a7be-240">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) are supported as the delegate event handler.</span></span>

<span data-ttu-id="7a7be-241">`Pages/EventHandlerExample4.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-241">`Pages/EventHandlerExample4.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

::: moniker-end

<span data-ttu-id="7a7be-242">使用 c # 方法參數（例如反復查看一組元素）來關閉其他值，通常會很方便。</span><span class="sxs-lookup"><span data-stu-id="7a7be-242">It's often convenient to close over additional values using C# method parameters, such as when iterating over a set of elements.</span></span> <span data-ttu-id="7a7be-243">下列範例會建立三個按鈕，每個按鈕都會呼叫 `UpdateHeading` 並傳遞下列資料：</span><span class="sxs-lookup"><span data-stu-id="7a7be-243">The following example creates three buttons, each of which calls `UpdateHeading` and passes the following data:</span></span>

* <span data-ttu-id="7a7be-244"> (中) 的事件引數 <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> `e` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-244">An event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) in `e`.</span></span>
* <span data-ttu-id="7a7be-245">中的按鈕編號 `buttonNumber` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-245">The button number in `buttonNumber`.</span></span>

<span data-ttu-id="7a7be-246">`Pages/EventHandlerExample5.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-246">`Pages/EventHandlerExample5.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7a7be-247">請勿直接在 lambda 運算式 **中使用迴圈** 變數，例如 `i` 在先前的 `for` 迴圈範例中。</span><span class="sxs-lookup"><span data-stu-id="7a7be-247">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="7a7be-248">否則，所有 lambda 運算式都會使用相同的變數，這會導致在所有 lambda 中使用相同的值。</span><span class="sxs-lookup"><span data-stu-id="7a7be-248">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="7a7be-249">一律在本機變數中捕捉變數的值，然後使用它。</span><span class="sxs-lookup"><span data-stu-id="7a7be-249">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="7a7be-250">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="7a7be-250">In the preceding example:</span></span>
>
> * <span data-ttu-id="7a7be-251">迴圈變數 `i` 指派給 `buttonNumber` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-251">The loop variable `i` is assigned to `buttonNumber`.</span></span>
> * <span data-ttu-id="7a7be-252">`buttonNumber` 在 lambda 運算式中使用。</span><span class="sxs-lookup"><span data-stu-id="7a7be-252">`buttonNumber` is used in the lambda expression.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="7a7be-253">>app >eventcallback</span><span class="sxs-lookup"><span data-stu-id="7a7be-253">EventCallback</span></span>

<span data-ttu-id="7a7be-254">具有嵌套元件的常見案例會在子元件事件發生時，執行父元件的方法。</span><span class="sxs-lookup"><span data-stu-id="7a7be-254">A common scenario with nested components executes a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="7a7be-255">`onclick`子元件中發生的事件是常見的使用案例。</span><span class="sxs-lookup"><span data-stu-id="7a7be-255">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="7a7be-256">若要公開元件之間的事件，請使用 <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-256">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="7a7be-257">父元件可以將回呼方法指派給子元件的 <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-257">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="7a7be-258">下列 `Child` 元件示範如何設定按鈕的 `onclick` 處理常式，以接收 <xref:Microsoft.AspNetCore.Components.EventCallback> 來自範例的委派 `ParentComponent` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-258">The following `Child` component demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="7a7be-259">的 <xref:Microsoft.AspNetCore.Components.EventCallback> 類型是 `MouseEventArgs` ，適用于來自周邊裝置的 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="7a7be-259">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device.</span></span>

<span data-ttu-id="7a7be-260">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-260">`Shared/Child.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

::: moniker-end

<span data-ttu-id="7a7be-261">`Parent`元件會將子系的 <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) 設定為它的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="7a7be-261">The `Parent` component sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="7a7be-262">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-262">`Pages/Parent.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

::: moniker-end

<span data-ttu-id="7a7be-263">當您在中選取此按鈕時 `ChildComponent` ：</span><span class="sxs-lookup"><span data-stu-id="7a7be-263">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="7a7be-264">`Parent`呼叫元件的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="7a7be-264">The `Parent` component's `ShowMessage` method is called.</span></span> <span data-ttu-id="7a7be-265">`message` 會更新並顯示在 `Parent` 元件中。</span><span class="sxs-lookup"><span data-stu-id="7a7be-265">`message` is updated and displayed in the `Parent` component.</span></span>
* <span data-ttu-id="7a7be-266">[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes)回呼的方法 () 不需要呼叫 `ShowMessage` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-266">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="7a7be-267"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 會自動呼叫以 rerender `Parent` 元件，就像在子事件處理常式中執行的子事件觸發程式元件轉譯資料流程一樣。</span><span class="sxs-lookup"><span data-stu-id="7a7be-267"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `Parent` component, just as child events trigger component rerendering in event handlers that execute within the child.</span></span> <span data-ttu-id="7a7be-268">如需詳細資訊，請參閱<xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="7a7be-268">For more information, see <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="7a7be-269"><xref:Microsoft.AspNetCore.Components.EventCallback> 和 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 允許非同步委派。</span><span class="sxs-lookup"><span data-stu-id="7a7be-269"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="7a7be-270"><xref:Microsoft.AspNetCore.Components.EventCallback> 是弱型別，可讓傳遞中的任何型別引數 `InvokeAsync(Object)` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-270"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="7a7be-271"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 是強型別，而且需要傳遞可 `T` `InvokeAsync(T)` 指派給的引數 `TValue` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-271"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="7a7be-272"><xref:Microsoft.AspNetCore.Components.EventCallback>使用或叫 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 用 <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> ，並等待 <xref:System.Threading.Tasks.Task> ：</span><span class="sxs-lookup"><span data-stu-id="7a7be-272">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="7a7be-273"><xref:Microsoft.AspNetCore.Components.EventCallback> <xref:Microsoft.AspNetCore.Components.EventCallback%601> 針對事件處理和系結元件參數使用和。</span><span class="sxs-lookup"><span data-stu-id="7a7be-273">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="7a7be-274">偏好強型別 <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-274">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="7a7be-275"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 提供增強的錯誤意見反應給元件的使用者。</span><span class="sxs-lookup"><span data-stu-id="7a7be-275"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides enhanced error feedback to users of the component.</span></span> <span data-ttu-id="7a7be-276">與其他 UI 事件處理常式類似，指定事件參數是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="7a7be-276">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="7a7be-277"><xref:Microsoft.AspNetCore.Components.EventCallback>未傳遞任何值給回呼時使用。</span><span class="sxs-lookup"><span data-stu-id="7a7be-277">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="7a7be-278">防止預設動作</span><span class="sxs-lookup"><span data-stu-id="7a7be-278">Prevent default actions</span></span>

<span data-ttu-id="7a7be-279">使用指示詞 [`@on{DOM EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) 屬性以防止事件的預設動作，其中 `{DOM EVENT}` 預留位置是 [檔物件模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)。</span><span class="sxs-lookup"><span data-stu-id="7a7be-279">Use the [`@on{DOM EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event, where the `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events).</span></span>

<span data-ttu-id="7a7be-280">在輸入裝置上選取索引鍵，且元素焦點在文字方塊上時，瀏覽器通常會在文字方塊中顯示索引鍵的字元。</span><span class="sxs-lookup"><span data-stu-id="7a7be-280">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="7a7be-281">在下列範例中，藉由指定指示詞屬性來防止預設行為 `@onkeydown:preventDefault` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-281">In the following example, the default behavior is prevented by specifying the `@onkeydown:preventDefault` directive attribute.</span></span> <span data-ttu-id="7a7be-282">當焦點在元素上時 `<input>` ，計數器會以索引鍵序列移位遞增<kbd></kbd> + <kbd>+</kbd> 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-282">When the focus is on the `<input>` element, the counter increments with the key sequence <kbd>Shift</kbd>+<kbd>+</kbd>.</span></span> <span data-ttu-id="7a7be-283">`+`未將字元指派給 `<input>` 元素的值。</span><span class="sxs-lookup"><span data-stu-id="7a7be-283">The `+` character isn't assigned to the `<input>` element's value.</span></span> <span data-ttu-id="7a7be-284">如需的詳細資訊 `keydown` ，請參閱[ `MDN Web Docs: Document: keydown` 事件](https://developer.mozilla.org/docs/Web/API/Document/keydown_event)。</span><span class="sxs-lookup"><span data-stu-id="7a7be-284">For more information on `keydown`, see [`MDN Web Docs: Document: keydown` event](https://developer.mozilla.org/docs/Web/API/Document/keydown_event).</span></span>

<span data-ttu-id="7a7be-285">`Pages/EventHandlerExample6.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-285">`Pages/EventHandlerExample6.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

::: moniker-end

<span data-ttu-id="7a7be-286">指定 `@on{DOM EVENT}:preventDefault` 沒有值的屬性相當於 `@on{DOM EVENT}:preventDefault="true"` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-286">Specifying the `@on{DOM EVENT}:preventDefault` attribute without a value is equivalent to `@on{DOM EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="7a7be-287">運算式也是允許的屬性值。</span><span class="sxs-lookup"><span data-stu-id="7a7be-287">An expression is also a permitted value of the attribute.</span></span> <span data-ttu-id="7a7be-288">在下列範例中， `shouldPreventDefault` 是 `bool` 設定為或的欄位 `true` `false` ：</span><span class="sxs-lookup"><span data-stu-id="7a7be-288">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeydown:preventDefault="shouldPreventDefault" />

...

@code {
    private bool shouldPreventDefault = true;
}
```

## <a name="stop-event-propagation"></a><span data-ttu-id="7a7be-289">停止事件傳播</span><span class="sxs-lookup"><span data-stu-id="7a7be-289">Stop event propagation</span></span>

<span data-ttu-id="7a7be-290">使用指示詞 [`@on{DOM EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) 屬性停止事件傳播，其中 `{DOM EVENT}` 預留位置是 [檔物件模型 (DOM) 事件](https://developer.mozilla.org/docs/Web/Events)。</span><span class="sxs-lookup"><span data-stu-id="7a7be-290">Use the [`@on{DOM EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation, where the `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events).</span></span>

<span data-ttu-id="7a7be-291">在下列範例中，選取此核取方塊可防止從第二個子系的事件 `<div>` 傳播到父系 `<div>` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-291">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`.</span></span> <span data-ttu-id="7a7be-292">因為傳播的 click 事件通常 `OnSelectParentDiv` 會引發方法，所以 `<div>` 除非已選取核取方塊，否則在父 div 訊息中選取第二個子代會產生。</span><span class="sxs-lookup"><span data-stu-id="7a7be-292">Since propagated click events normally fire the `OnSelectParentDiv` method, selecting the second child `<div>` results in the parent div message appearing unless the check box is selected.</span></span>

<span data-ttu-id="7a7be-293">`Pages/EventHandlerExample7.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-293">`Pages/EventHandlerExample7.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a><span data-ttu-id="7a7be-294">將焦點放在元素</span><span class="sxs-lookup"><span data-stu-id="7a7be-294">Focus an element</span></span>

<span data-ttu-id="7a7be-295"><xref:Microsoft.AspNetCore.Components.ElementReferenceExtensions.FocusAsync%2A>在專案[參考](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)上呼叫，以將專案放在程式碼中。</span><span class="sxs-lookup"><span data-stu-id="7a7be-295">Call <xref:Microsoft.AspNetCore.Components.ElementReferenceExtensions.FocusAsync%2A> on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code.</span></span> <span data-ttu-id="7a7be-296">在下列範例中，選取要將焦點放在元素上的按鈕 `<input>` 。</span><span class="sxs-lookup"><span data-stu-id="7a7be-296">In the following example, select the button to focus the `<input>` element.</span></span>

<span data-ttu-id="7a7be-297">`Pages/EventHandlerExample8.razor`:</span><span class="sxs-lookup"><span data-stu-id="7a7be-297">`Pages/EventHandlerExample8.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample8.razor?highlight=16)]

::: moniker-end
