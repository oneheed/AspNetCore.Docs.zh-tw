---
title: '從 ASP.NET Core 中的 .NET 方法呼叫 JavaScript 函式 :::no-loc(Blazor):::'
author: guardrex
description: '瞭解如何在應用程式中叫用 .NET 方法的 JavaScript 函式 :::no-loc(Blazor)::: 。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/20/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 17d6087b884775a8bfcb41fe23296f508467e924
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234448"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="9078e-103">從 ASP.NET Core 中的 .NET 方法呼叫 JavaScript 函式 :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="9078e-103">Call JavaScript functions from .NET methods in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="9078e-104">[Javier Calvarro Nelson](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9078e-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9078e-105">:::no-loc(Blazor):::應用程式可以從 javascript 函式的 .net 方法和 .net 方法中叫用 javascript 函式。</span><span class="sxs-lookup"><span data-stu-id="9078e-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="9078e-106">這些案例稱為 *JavaScript 互通性* ( *JS interop* ) 。</span><span class="sxs-lookup"><span data-stu-id="9078e-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="9078e-107">本文涵蓋從 .NET 叫用 JavaScript 函式的功能。</span><span class="sxs-lookup"><span data-stu-id="9078e-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="9078e-108">如需如何從 JavaScript 呼叫 .NET 方法的詳細資訊，請參閱 <xref:blazor/call-dotnet-from-javascript> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="9078e-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9078e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9078e-110">若要從 .NET 呼叫 JavaScript，請使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念。</span><span class="sxs-lookup"><span data-stu-id="9078e-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="9078e-111">若要發出 JS interop 呼叫，請 <xref:Microsoft.JSInterop.IJSRuntime> 在您的元件中插入抽象概念。</span><span class="sxs-lookup"><span data-stu-id="9078e-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="9078e-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 取得您想要叫用之 JavaScript 函式的識別碼，以及任何數目的 JSON 可序列化引數。</span><span class="sxs-lookup"><span data-stu-id="9078e-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="9078e-113">函數識別碼是相對於全域範圍 (`window`) 。</span><span class="sxs-lookup"><span data-stu-id="9078e-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="9078e-114">如果您想要呼叫 `window.someScope.someFunction` ，則識別碼為 `someScope.someFunction` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="9078e-115">在呼叫函式之前，不需要先註冊函式。</span><span class="sxs-lookup"><span data-stu-id="9078e-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="9078e-116">傳回型別 `T` 也必須是 JSON 可序列化。</span><span class="sxs-lookup"><span data-stu-id="9078e-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="9078e-117">`T` 應符合最適合對應至所傳回 JSON 型別的 .NET 型別。</span><span class="sxs-lookup"><span data-stu-id="9078e-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="9078e-118">傳回 [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 的 JavaScript 函式是使用來呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="9078e-119">`InvokeAsync` 解除包裝承諾，並傳回承諾所等待的值。</span><span class="sxs-lookup"><span data-stu-id="9078e-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="9078e-120">針對 :::no-loc(Blazor Server)::: 已啟用可執行處理的應用程式，在初始未處理期間無法呼叫 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="9078e-120">For :::no-loc(Blazor Server)::: apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="9078e-121">必須先延遲 JavaScript interop 呼叫，才能建立與瀏覽器的連接。</span><span class="sxs-lookup"><span data-stu-id="9078e-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="9078e-122">如需詳細資訊，請參閱「偵測 [ :::no-loc(Blazor Server)::: 應用程式何時進行](#detect-when-a-blazor-server-app-is-prerendering) 偵測」一節。</span><span class="sxs-lookup"><span data-stu-id="9078e-122">For more information, see the [Detect when a :::no-loc(Blazor Server)::: app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="9078e-123">下列範例是 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) 以 JavaScript 為基礎的解碼器為基礎。</span><span class="sxs-lookup"><span data-stu-id="9078e-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="9078e-124">此範例示範如何從 c # 方法叫用 JavaScript 函式，該方法會從開發人員程式碼將要求卸載至現有的 JavaScript API。</span><span class="sxs-lookup"><span data-stu-id="9078e-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="9078e-125">JavaScript 函式會從 c # 方法接受位元組陣列、將陣列解碼，然後將文字傳回至元件以供顯示。</span><span class="sxs-lookup"><span data-stu-id="9078e-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="9078e-126">在 `<head>` `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) 或 () 的元素內 `Pages/_Host.cshtml` ，提供 JavaScript 函式， :::no-loc(Blazor Server)::: 用 `TextDecoder` 來解碼傳遞的陣列並傳回已解碼的值：</span><span class="sxs-lookup"><span data-stu-id="9078e-126">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="9078e-127">JavaScript 程式碼（例如上述範例中所示的程式碼）也可以從 JavaScript 檔案載入， (`.js`) 並參考腳本檔案：</span><span class="sxs-lookup"><span data-stu-id="9078e-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="9078e-128">下列元件：</span><span class="sxs-lookup"><span data-stu-id="9078e-128">The following component:</span></span>

* <span data-ttu-id="9078e-129">`convertArray` `JS` 當選取元件按鈕 () 時，會叫用 JavaScript 函數 **`Convert Array`** 。</span><span class="sxs-lookup"><span data-stu-id="9078e-129">Invokes the `convertArray` JavaScript function using `JS` when a component button ( **`Convert Array`** ) is selected.</span></span>
* <span data-ttu-id="9078e-130">呼叫 JavaScript 函數之後，傳遞的陣列就會轉換成字串。</span><span class="sxs-lookup"><span data-stu-id="9078e-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="9078e-131">字串會傳回給元件以供顯示。</span><span class="sxs-lookup"><span data-stu-id="9078e-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="9078e-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="9078e-132">IJSRuntime</span></span>

<span data-ttu-id="9078e-133">若要使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念，請採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="9078e-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="9078e-134">將 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念插入 :::no-loc(Razor)::: 元件 (`.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="9078e-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the :::no-loc(Razor)::: component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="9078e-135">在 `<head>` `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) 或 () 的元素內 `Pages/_Host.cshtml` :::no-loc(Blazor Server)::: ，提供 `handleTickerChanged` JavaScript 函數。</span><span class="sxs-lookup"><span data-stu-id="9078e-135">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="9078e-136">呼叫函式時，會傳回 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ，而且不會傳回值：</span><span class="sxs-lookup"><span data-stu-id="9078e-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="9078e-137">將 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念插入類別 (`.cs`) ：</span><span class="sxs-lookup"><span data-stu-id="9078e-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="9078e-138">在 `<head>` `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) 或 () 的元素內 `Pages/_Host.cshtml` :::no-loc(Blazor Server)::: ，提供 `handleTickerChanged` JavaScript 函數。</span><span class="sxs-lookup"><span data-stu-id="9078e-138">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="9078e-139">呼叫函式時，會傳回 `JS.InvokeAsync` 值：</span><span class="sxs-lookup"><span data-stu-id="9078e-139">The function is called with `JS.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="9078e-140">針對使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)的動態內容產生，請使用 `[Inject]` 屬性：</span><span class="sxs-lookup"><span data-stu-id="9078e-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

<span data-ttu-id="9078e-141">在本主題隨附的用戶端範例應用程式中，應用程式可以使用兩個 JavaScript 函式來與 DOM 互動，以接收使用者輸入並顯示歡迎訊息：</span><span class="sxs-lookup"><span data-stu-id="9078e-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="9078e-142">`showPrompt`：產生提示以接受使用者輸入 (使用者的名稱) 並將名稱傳回給呼叫者。</span><span class="sxs-lookup"><span data-stu-id="9078e-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="9078e-143">`displayWelcome`：將歡迎訊息從呼叫端指派給具有 of 的 DOM 物件 `id` `welcome` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="9078e-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="9078e-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="9078e-145">將 `<script>` 參考 JavaScript 檔案的標記放置在檔案 `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) 或檔案 `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) 。</span><span class="sxs-lookup"><span data-stu-id="9078e-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::).</span></span>

<span data-ttu-id="9078e-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span><span class="sxs-lookup"><span data-stu-id="9078e-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span></span>

[!code-html[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="9078e-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span><span class="sxs-lookup"><span data-stu-id="9078e-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span></span>

[!code-cshtml[](./common/samples/3.x/:::no-loc(Blazor):::ServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="9078e-148">請勿將 `<script>` 標記放在元件檔中，因為 `<script>` 無法動態更新標記。</span><span class="sxs-lookup"><span data-stu-id="9078e-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="9078e-149">.NET 方法會藉由呼叫，與檔案中的 JavaScript 函式進行 interop `exampleJsInterop.js` <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="9078e-150"><xref:Microsoft.JSInterop.IJSRuntime>抽象概念是非同步，以允許 :::no-loc(Blazor Server)::: 案例。</span><span class="sxs-lookup"><span data-stu-id="9078e-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="9078e-151">如果應用程式是應用程式， :::no-loc(Blazor WebAssembly)::: 而您想要以同步方式叫用 JavaScript 函式，則改為向下轉換 <xref:Microsoft.JSInterop.IJSInProcessRuntime> 並呼叫 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-151">If the app is a :::no-loc(Blazor WebAssembly)::: app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="9078e-152">我們建議大多數的 JS interop 程式庫都使用非同步 Api，以確保所有案例中都有可用的程式庫。</span><span class="sxs-lookup"><span data-stu-id="9078e-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="9078e-153">若要在標準[javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中啟用 JavaScript 隔離，請參閱[ :::no-loc(Blazor)::: javascript 隔離和物件參考](#blazor-javascript-isolation-and-object-references)一節。</span><span class="sxs-lookup"><span data-stu-id="9078e-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [:::no-loc(Blazor)::: JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="9078e-154">範例應用程式包含一個可示範 JS interop 的元件。</span><span class="sxs-lookup"><span data-stu-id="9078e-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="9078e-155">元件：</span><span class="sxs-lookup"><span data-stu-id="9078e-155">The component:</span></span>

* <span data-ttu-id="9078e-156">透過 JavaScript 提示字元接收使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="9078e-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="9078e-157">將文字傳回至元件以進行處理。</span><span class="sxs-lookup"><span data-stu-id="9078e-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="9078e-158">呼叫第二個 JavaScript 函式，此函式會與 DOM 互動以顯示歡迎訊息。</span><span class="sxs-lookup"><span data-stu-id="9078e-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="9078e-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="9078e-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JS

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JS.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JS.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to :::no-loc(Blazor):::!");
    }
}
```

<span data-ttu-id="9078e-160">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `:::no-loc(Blazor):::Sample`) 。</span><span class="sxs-lookup"><span data-stu-id="9078e-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

1. <span data-ttu-id="9078e-161">當您 `TriggerJsPrompt` 選取元件的按鈕來執行時 **`Trigger JavaScript Prompt`** ， `showPrompt` 會呼叫檔案中提供的 JavaScript 函式 `wwwroot/exampleJsInterop.js` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="9078e-162">此函式 `showPrompt` 會接受使用者輸入 (使用者的名稱) ，其以 HTML 編碼並傳回至元件。</span><span class="sxs-lookup"><span data-stu-id="9078e-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="9078e-163">元件會將使用者名稱儲存在本機變數中 `name` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="9078e-164">中儲存的字串 `name` 會併入歡迎訊息中，該訊息會傳遞至 JavaScript 函式， `displayWelcome` 這會將歡迎訊息轉譯成標題標記。</span><span class="sxs-lookup"><span data-stu-id="9078e-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="9078e-165">呼叫 void JavaScript 函數</span><span class="sxs-lookup"><span data-stu-id="9078e-165">Call a void JavaScript function</span></span>

<span data-ttu-id="9078e-166">使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 于下列各項：</span><span class="sxs-lookup"><span data-stu-id="9078e-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="9078e-167">傳回 [void (0) /void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [未定義](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)的 JavaScript 函數。</span><span class="sxs-lookup"><span data-stu-id="9078e-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="9078e-168">如果不需要 .NET 就能讀取 JavaScript 呼叫的結果。</span><span class="sxs-lookup"><span data-stu-id="9078e-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="9078e-169">偵測 :::no-loc(Blazor Server)::: 應用程式何時進行呈現</span><span class="sxs-lookup"><span data-stu-id="9078e-169">Detect when a :::no-loc(Blazor Server)::: app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="9078e-170">捕捉元素的參考</span><span class="sxs-lookup"><span data-stu-id="9078e-170">Capture references to elements</span></span>

<span data-ttu-id="9078e-171">某些 JS interop 案例需要 HTML 元素的參考。</span><span class="sxs-lookup"><span data-stu-id="9078e-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="9078e-172">例如，UI 程式庫可能需要初始化專案參考，或者您可能需要在元素上呼叫類似命令的 Api，例如 `focus` 或 `play` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="9078e-173">使用下列方法來捕捉元件中的 HTML 元素參考：</span><span class="sxs-lookup"><span data-stu-id="9078e-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="9078e-174">將 `@ref` 屬性新增至 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="9078e-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="9078e-175">定義類型的欄位， <xref:Microsoft.AspNetCore.Components.ElementReference> 其名稱符合屬性的值 `@ref` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="9078e-176">下列範例將示範如何捕獲元素的參考 `username` `<input>` ：</span><span class="sxs-lookup"><span data-stu-id="9078e-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="9078e-177">只使用專案參考來改變不與互動之空白元素的內容 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="9078e-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with :::no-loc(Blazor):::.</span></span> <span data-ttu-id="9078e-178">當協力廠商 API 將內容提供給元素時，此案例很有用。</span><span class="sxs-lookup"><span data-stu-id="9078e-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="9078e-179">因為 :::no-loc(Blazor)::: 不會與專案互動，所以在專案和 DOM 的表示之間沒有任何可能發生衝突 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="9078e-179">Because :::no-loc(Blazor)::: doesn't interact with the element, there's no possibility of a conflict between :::no-loc(Blazor):::'s representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="9078e-180">在下列範例中，將未排序清單的內容 () 是很 *危險* 的， `ul` 因為會 :::no-loc(Blazor)::: 與 DOM 互動以將此專案的清單專案填入 (`<li>`) ：</span><span class="sxs-lookup"><span data-stu-id="9078e-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because :::no-loc(Blazor)::: interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="9078e-181">如果 JS interop 變動元素的內容， `MyList` 並 :::no-loc(Blazor)::: 嘗試將差異套用至專案，則差異不會與 DOM 相符。</span><span class="sxs-lookup"><span data-stu-id="9078e-181">If JS interop mutates the contents of element `MyList` and :::no-loc(Blazor)::: attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="9078e-182"><xref:Microsoft.AspNetCore.Components.ElementReference>會透過 JS interop 傳遞至 JavaScript 程式碼。</span><span class="sxs-lookup"><span data-stu-id="9078e-182">An <xref:Microsoft.AspNetCore.Components.ElementReference> is passed through to JavaScript code via JS interop.</span></span> <span data-ttu-id="9078e-183">JavaScript 程式碼會接收 `HTMLElement` 可搭配一般 DOM api 使用的實例。</span><span class="sxs-lookup"><span data-stu-id="9078e-183">The JavaScript code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span> <span data-ttu-id="9078e-184">例如，下列程式碼會定義可讓您將滑鼠點擊傳送至專案的 .NET 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="9078e-184">For example, the following code defines a .NET extension method that enables sending a mouse click to an element:</span></span>

<span data-ttu-id="9078e-185">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="9078e-185">`exampleJsInterop.js`:</span></span>

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="9078e-186">[`FocusAsync`](xref:blazor/components/event-handling#focus-an-element)在 c # 程式碼中使用，將內建于架構中的專案， :::no-loc(Blazor)::: 並搭配專案參考使用。</span><span class="sxs-lookup"><span data-stu-id="9078e-186">Use [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element) in C# code to focus an element, which is built-into the :::no-loc(Blazor)::: framework and works with element references.</span></span>

::: moniker-end

<span data-ttu-id="9078e-187">若要呼叫不會傳回值的 JavaScript 函式，請使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9078e-188">下列程式碼會 `Click` 使用所捕捉的先前 JavaScript 函式來呼叫之前的 JavaScript 函式，以觸發用戶端事件 <xref:Microsoft.AspNetCore.Components.ElementReference> ：</span><span class="sxs-lookup"><span data-stu-id="9078e-188">The following code triggers a client-side `Click` event by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

<span data-ttu-id="9078e-189">若要使用擴充方法，請建立可接收實例的靜態擴充方法 <xref:Microsoft.JSInterop.IJSRuntime> ：</span><span class="sxs-lookup"><span data-stu-id="9078e-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

<span data-ttu-id="9078e-190">`clickElement`方法會直接在物件上呼叫。</span><span class="sxs-lookup"><span data-stu-id="9078e-190">The `clickElement` method is called directly on the object.</span></span> <span data-ttu-id="9078e-191">下列範例假設 `TriggerClickEvent` 方法可從 `JsInteropClasses` 命名空間中取得：</span><span class="sxs-lookup"><span data-stu-id="9078e-191">The following example assumes that the `TriggerClickEvent` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> <span data-ttu-id="9078e-192">`exampleButton`變數只會在呈現元件之後填入。</span><span class="sxs-lookup"><span data-stu-id="9078e-192">The `exampleButton` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="9078e-193">如果將擴展 <xref:Microsoft.AspNetCore.Components.ElementReference> 傳遞至 javascript 程式碼，javascript 程式碼會收到的值 `null` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="9078e-194">若要在元件完成轉譯後操作元素參考，請使用[ `OnAfterRenderAsync` 或 `OnAfterRender` 元件生命週期方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="9078e-194">To manipulate element references after the component has finished rendering use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="9078e-195">使用泛型型別並傳回值時，請使用 <xref:System.Threading.Tasks.ValueTask%601> ：</span><span class="sxs-lookup"><span data-stu-id="9078e-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="9078e-196">`GenericMethod` 會直接在具有類型的物件上呼叫。</span><span class="sxs-lookup"><span data-stu-id="9078e-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="9078e-197">下列範例假設 `GenericMethod` 可從 `JsInteropClasses` 命名空間取得：</span><span class="sxs-lookup"><span data-stu-id="9078e-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="9078e-198">跨元件參考元素</span><span class="sxs-lookup"><span data-stu-id="9078e-198">Reference elements across components</span></span>

<span data-ttu-id="9078e-199"><xref:Microsoft.AspNetCore.Components.ElementReference>無法在元件之間傳遞，因為：</span><span class="sxs-lookup"><span data-stu-id="9078e-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> can't be passed between components because:</span></span>

* <span data-ttu-id="9078e-200">只有在轉譯元件之後（在元件的方法執行期間或之後），才能保證實例存在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-200">The instance is only guaranteed to exist after the component is rendered, which is during or after a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>/<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> method executes.</span></span>
* <span data-ttu-id="9078e-201"><xref:Microsoft.AspNetCore.Components.ElementReference>是 [`struct`](/csharp/language-reference/builtin-types/struct) ，無法以[元件參數](xref:blazor/components/index#component-parameters)形式傳遞。</span><span class="sxs-lookup"><span data-stu-id="9078e-201">An <xref:Microsoft.AspNetCore.Components.ElementReference> is a [`struct`](/csharp/language-reference/builtin-types/struct), which can't be passed as a [component parameter](xref:blazor/components/index#component-parameters).</span></span>

<span data-ttu-id="9078e-202">若要讓父元件能讓元素參考可供其他元件使用，父元件可以：</span><span class="sxs-lookup"><span data-stu-id="9078e-202">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="9078e-203">允許子元件註冊回呼。</span><span class="sxs-lookup"><span data-stu-id="9078e-203">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="9078e-204">使用傳遞的元素參考，在事件期間叫用已註冊的回呼 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-204">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="9078e-205">這種方法間接可讓子元件與父系的元素參考互動。</span><span class="sxs-lookup"><span data-stu-id="9078e-205">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="9078e-206">下列 :::no-loc(Blazor WebAssembly)::: 範例說明此方法。</span><span class="sxs-lookup"><span data-stu-id="9078e-206">The following :::no-loc(Blazor WebAssembly)::: example illustrates the approach.</span></span>

<span data-ttu-id="9078e-207">在 `<head>` 的 `wwwroot/index.html` ：</span><span class="sxs-lookup"><span data-stu-id="9078e-207">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="9078e-208">在 `<body>` 的 `wwwroot/index.html` ：</span><span class="sxs-lookup"><span data-stu-id="9078e-208">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="9078e-209">`Pages/Index.razor` (父元件) ：</span><span class="sxs-lookup"><span data-stu-id="9078e-209">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is :::no-loc(Blazor)::: working for you?" />
```

<span data-ttu-id="9078e-210">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="9078e-210">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="9078e-211">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `:::no-loc(Blazor):::Sample`) 。</span><span class="sxs-lookup"><span data-stu-id="9078e-211">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="9078e-212">`Shared/SurveyPrompt.razor` (子元件) ：</span><span class="sxs-lookup"><span data-stu-id="9078e-212">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="9078e-213">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="9078e-213">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="9078e-214">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `:::no-loc(Blazor):::Sample`) 。</span><span class="sxs-lookup"><span data-stu-id="9078e-214">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="9078e-215">強化 JS interop 呼叫</span><span class="sxs-lookup"><span data-stu-id="9078e-215">Harden JS interop calls</span></span>

<span data-ttu-id="9078e-216">JS interop 可能因為網路錯誤而失敗，應該視為不可靠。</span><span class="sxs-lookup"><span data-stu-id="9078e-216">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="9078e-217">根據預設， :::no-loc(Blazor Server)::: 應用程式會在一分鐘後，在伺服器上使用 JS interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="9078e-217">By default, a :::no-loc(Blazor Server)::: app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="9078e-218">如果應用程式可容忍更積極的超時，請使用下列其中一種方法來設定 timeout：</span><span class="sxs-lookup"><span data-stu-id="9078e-218">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="9078e-219">在中 `Startup.ConfigureServices` ，指定 timeout：</span><span class="sxs-lookup"><span data-stu-id="9078e-219">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSide:::no-loc(Blazor):::(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="9078e-220">元件程式碼中的每個調用，單一呼叫可以指定 timeout：</span><span class="sxs-lookup"><span data-stu-id="9078e-220">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="9078e-221">如需資源耗盡的詳細資訊，請參閱 <xref:blazor/security/server/threat-mitigation> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-221">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="9078e-222">避免迴圈物件參考</span><span class="sxs-lookup"><span data-stu-id="9078e-222">Avoid circular object references</span></span>

<span data-ttu-id="9078e-223">包含迴圈參考的物件無法在用戶端上針對下列任一項進行序列化：</span><span class="sxs-lookup"><span data-stu-id="9078e-223">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="9078e-224">.NET 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="9078e-224">.NET method calls.</span></span>
* <span data-ttu-id="9078e-225">當傳回型別有迴圈參考時，來自 c # 的 JavaScript 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="9078e-225">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="9078e-226">如需詳細資訊，請參閱下列問題：</span><span class="sxs-lookup"><span data-stu-id="9078e-226">For more information, see the following issues:</span></span>

* [<span data-ttu-id="9078e-227">不支援迴圈參考，請採用兩個 (dotnet/aspnetcore #20525) </span><span class="sxs-lookup"><span data-stu-id="9078e-227">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="9078e-228">提案：在將 (dotnet/執行時間 #30820 序列化時，新增處理迴圈參考的機制) </span><span class="sxs-lookup"><span data-stu-id="9078e-228">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="9078e-229">:::no-loc(Blazor)::: JavaScript 隔離和物件參考</span><span class="sxs-lookup"><span data-stu-id="9078e-229">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="9078e-230">:::no-loc(Blazor)::: 啟用標準 [javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中的 JavaScript 隔離。</span><span class="sxs-lookup"><span data-stu-id="9078e-230">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="9078e-231">JavaScript 隔離提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="9078e-231">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="9078e-232">匯入的 JavaScript 不再干擾全域命名空間。</span><span class="sxs-lookup"><span data-stu-id="9078e-232">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="9078e-233">程式庫和元件的取用者不需要匯入相關的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="9078e-233">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="9078e-234">例如，下列 JavaScript 模組會匯出 JavaScript 函式，以顯示瀏覽器提示：</span><span class="sxs-lookup"><span data-stu-id="9078e-234">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="9078e-235">將上述 JavaScript 模組新增至 .NET 程式庫作為靜態 web 資產 (`wwwroot/exampleJsInterop.js`) 然後使用服務將模組匯入至 .net 程式碼 <xref:Microsoft.JSInterop.IJSRuntime> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-235">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="9078e-236">服務會插入為 `js` 下列範例中未顯示的 () ：</span><span class="sxs-lookup"><span data-stu-id="9078e-236">The service is injected as `js` (not shown) for the following example:</span></span>

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="9078e-237">`import`上述範例中的識別碼是專門用來匯入 JavaScript 模組的特殊識別碼。</span><span class="sxs-lookup"><span data-stu-id="9078e-237">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="9078e-238">使用其穩定靜態 web 資產路徑來指定模組： `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-238">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="9078e-239">預留位置 `{LIBRARY NAME}` 是程式庫名稱。</span><span class="sxs-lookup"><span data-stu-id="9078e-239">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="9078e-240">預留位置 `{PATH UNDER WWWROOT}` 是下腳本的路徑 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-240">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="9078e-241"><xref:Microsoft.JSInterop.IJSRuntime> 將模組匯入為 `IJSObjectReference` ，表示從 .net 程式碼到 JavaScript 物件的參考。</span><span class="sxs-lookup"><span data-stu-id="9078e-241"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="9078e-242">使用來叫用 `IJSObjectReference` 模組中匯出的 JavaScript 函式：</span><span class="sxs-lookup"><span data-stu-id="9078e-242">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="9078e-243">`IJSInProcessObjectReference` 表示 JavaScript 物件的參考，該物件的函式可以同步叫用。</span><span class="sxs-lookup"><span data-stu-id="9078e-243">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="9078e-244">`IJSUnmarshalledObjectReference` 代表 JavaScript 物件的參考，該物件的函式可以叫用，而不會有序列化 .NET 資料的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="9078e-244">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="9078e-245">:::no-loc(Blazor WebAssembly):::當效能很重要時，即可使用此功能：</span><span class="sxs-lookup"><span data-stu-id="9078e-245">This can be used in :::no-loc(Blazor WebAssembly)::: when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = :::no-loc(Blazor):::.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)js;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

<span data-ttu-id="9078e-246">在上述範例中， <xref:Microsoft.JSInterop.IJSRuntime> 服務會插入到類別中，並指派給 `js` (未顯示) 。</span><span class="sxs-lookup"><span data-stu-id="9078e-246">In the preceding example, the <xref:Microsoft.JSInterop.IJSRuntime> service is injected into the class and assigned to `js` (not shown).</span></span>

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="9078e-247">使用可轉譯 UI (DOM 元素的 JavaScript 程式庫) </span><span class="sxs-lookup"><span data-stu-id="9078e-247">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="9078e-248">有時您可能會想要使用 JavaScript 程式庫，在瀏覽器 DOM 內產生可見的使用者介面元素。</span><span class="sxs-lookup"><span data-stu-id="9078e-248">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="9078e-249">乍看之下，這似乎很難，因為比較 :::no-loc(Blazor)::: 系統需要控制 dom 元素的樹狀結構，而且如果某些外部程式碼變動 dom 樹狀結構，並使其套用差異的機制失效，就會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="9078e-249">At first glance, this might seem difficult because :::no-loc(Blazor):::'s diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="9078e-250">這並不是 :::no-loc(Blazor)::: 特定的限制。</span><span class="sxs-lookup"><span data-stu-id="9078e-250">This isn't a :::no-loc(Blazor):::-specific limitation.</span></span> <span data-ttu-id="9078e-251">任何差異型 UI 架構都會發生相同的挑戰。</span><span class="sxs-lookup"><span data-stu-id="9078e-251">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="9078e-252">幸運的是，將外部產生的 UI 可靠地內嵌在 :::no-loc(Blazor)::: 元件 UI 中相當簡單。</span><span class="sxs-lookup"><span data-stu-id="9078e-252">Fortunately, it's straightforward to embed externally-generated UI within a :::no-loc(Blazor)::: component UI reliably.</span></span> <span data-ttu-id="9078e-253">建議的技巧是讓元件的程式碼 (檔案 `.razor`) 產生空的元素。</span><span class="sxs-lookup"><span data-stu-id="9078e-253">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="9078e-254">在比較 :::no-loc(Blazor)::: 系統的考慮下，元素一律是空的，因此轉譯器不會遞迴到專案中，而是只保留其內容。</span><span class="sxs-lookup"><span data-stu-id="9078e-254">As far as :::no-loc(Blazor):::'s diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="9078e-255">這可讓您安全地在專案中填入任意外部管理的內容。</span><span class="sxs-lookup"><span data-stu-id="9078e-255">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="9078e-256">下列範例示範概念。</span><span class="sxs-lookup"><span data-stu-id="9078e-256">The following example demonstrates the concept.</span></span> <span data-ttu-id="9078e-257">在語句中的 `if` 時 `firstRender` `true` ，請使用來做一些動作 `myElement` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-257">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="9078e-258">例如，呼叫外部 JavaScript 程式庫來填入它。</span><span class="sxs-lookup"><span data-stu-id="9078e-258">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="9078e-259">:::no-loc(Blazor)::: 只保留專案的內容，直到移除此元件本身為止。</span><span class="sxs-lookup"><span data-stu-id="9078e-259">:::no-loc(Blazor)::: leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="9078e-260">移除元件時，也會移除元件的整個 DOM 子樹。</span><span class="sxs-lookup"><span data-stu-id="9078e-260">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a :::no-loc(Blazor)::: component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="9078e-261">如需更詳細的範例，請考慮使用 [開放原始碼 Mapbox api](https://www.mapbox.com/)呈現互動式地圖的下列元件：</span><span class="sxs-lookup"><span data-stu-id="9078e-261">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="9078e-262">對應的 JavaScript 模組（應放置於 `wwwroot/mapComponent.js` ）如下所示：</span><span class="sxs-lookup"><span data-stu-id="9078e-262">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="9078e-263">在上述範例中，請將字串取代為 `{ACCESS TOKEN}` 可從中取得的有效存取權杖 https://account.mapbox.com 。</span><span class="sxs-lookup"><span data-stu-id="9078e-263">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="9078e-264">若要產生正確的樣式，請將下列樣式表單標記新增至主控制項 HTML 網頁 (`index.html` 或 `_Host.cshtml`) ：</span><span class="sxs-lookup"><span data-stu-id="9078e-264">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="9078e-265">上述範例會產生互動式地圖 UI，使用者在其中：</span><span class="sxs-lookup"><span data-stu-id="9078e-265">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="9078e-266">可以拖曳到滾動或縮放。</span><span class="sxs-lookup"><span data-stu-id="9078e-266">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="9078e-267">按一下按鈕以跳到預先定義的位置。</span><span class="sxs-lookup"><span data-stu-id="9078e-267">Click buttons to jump to predefined locations.</span></span>

![東京、日本和 Mapbox 的街道地圖，可選擇 Bristol、英國和東京、日本](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="9078e-269">要瞭解的重點是：</span><span class="sxs-lookup"><span data-stu-id="9078e-269">The key points to understand are:</span></span>

 * <span data-ttu-id="9078e-270">在 `<div>` 考慮時，with `@ref="mapElement"` 會保持空白 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="9078e-270">The `<div>` with `@ref="mapElement"` is left empty as far as :::no-loc(Blazor)::: is concerned.</span></span> <span data-ttu-id="9078e-271">因此，在一段 `mapbox-gl.js` 時間內填入並修改其內容是安全的。</span><span class="sxs-lookup"><span data-stu-id="9078e-271">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="9078e-272">您可以使用這項技術搭配任何轉譯 UI 的 JavaScript 程式庫。</span><span class="sxs-lookup"><span data-stu-id="9078e-272">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="9078e-273">您甚至可以將協力廠商 JavaScript SPA 架構中的元件內嵌在 :::no-loc(Blazor)::: 元件中，只要它們不會嘗試連接並修改頁面的其他部分即可。</span><span class="sxs-lookup"><span data-stu-id="9078e-273">You could even embed components from a third-party JavaScript SPA framework inside :::no-loc(Blazor)::: components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="9078e-274">外部 JavaScript 程式碼修改不是空的元素並 *不* 安全 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="9078e-274">It is *not* safe for external JavaScript code to modify elements that :::no-loc(Blazor)::: does not regard as empty.</span></span>
 * <span data-ttu-id="9078e-275">使用這種方法時，請記住有關如何 :::no-loc(Blazor)::: 保留或終結 DOM 元素的規則。</span><span class="sxs-lookup"><span data-stu-id="9078e-275">When using this approach, bear in mind the rules about how :::no-loc(Blazor)::: retains or destroys DOM elements.</span></span> <span data-ttu-id="9078e-276">在上述範例中，元件會安全地處理按鈕的 click 事件，並更新現有的對應實例，因為預設會保留 DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="9078e-276">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="9078e-277">如果您要從迴圈內轉譯地圖元素清單 `@foreach` ，您想要使用 `@key` 確保元件實例的保留。</span><span class="sxs-lookup"><span data-stu-id="9078e-277">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="9078e-278">否則，清單資料中的變更可能會導致元件實例以不當的方式保留先前實例的狀態。</span><span class="sxs-lookup"><span data-stu-id="9078e-278">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="9078e-279">如需詳細資訊，請參閱 [使用 @key 來保留元素和元件](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)。</span><span class="sxs-lookup"><span data-stu-id="9078e-279">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="9078e-280">此外，上述範例示範如何將 JavaScript 邏輯和相依性封裝在 ES6 模組內，並使用識別碼動態載入它 `import` 。</span><span class="sxs-lookup"><span data-stu-id="9078e-280">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="9078e-281">如需詳細資訊，請參閱 [JavaScript 隔離和物件參考](#blazor-javascript-isolation-and-object-references)。</span><span class="sxs-lookup"><span data-stu-id="9078e-281">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="9078e-282">JS interop 呼叫的大小限制</span><span class="sxs-lookup"><span data-stu-id="9078e-282">Size limits on JS interop calls</span></span>

<span data-ttu-id="9078e-283">在中 :::no-loc(Blazor WebAssembly)::: ，架構不會限制 JS interop 呼叫的輸入和輸出大小。</span><span class="sxs-lookup"><span data-stu-id="9078e-283">In :::no-loc(Blazor WebAssembly):::, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="9078e-284">在中 :::no-loc(Blazor Server)::: ，JS interop 呼叫的結果受限於 () 所強制執行的承載大小 :::no-loc(SignalR)::: 上限 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> ，預設為 32 KB。</span><span class="sxs-lookup"><span data-stu-id="9078e-284">In :::no-loc(Blazor Server):::, the result of a JS interop call is limited by the maximum payload size enforced by :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="9078e-285">嘗試回應 JS interop 呼叫的應用程式，其承載大於或等於 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> 擲回錯誤。</span><span class="sxs-lookup"><span data-stu-id="9078e-285">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="9078e-286">您可以藉由修改來設定較大的限制 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-286">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="9078e-287">下列範例會將接收訊息大小上限設定為 64 KB (64 \* 1024 \* 1024) ：</span><span class="sxs-lookup"><span data-stu-id="9078e-287">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSide:::no-loc(Blazor):::()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="9078e-288">增加 :::no-loc(SignalR)::: 限制的代價是需要使用更多的伺服器資源，而且會公開伺服器以增加惡意使用者的風險。</span><span class="sxs-lookup"><span data-stu-id="9078e-288">Increasing the :::no-loc(SignalR)::: limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="9078e-289">此外，以字串或位元組陣列的形式將大量內容讀取到記憶體中，也會導致與垃圾收集行程效能不佳的配置，進而造成額外的效能降低。</span><span class="sxs-lookup"><span data-stu-id="9078e-289">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="9078e-290">讀取大型承載的其中一個選項是考慮以較小的區區塊轉送內容，並以的形式處理承載 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="9078e-290">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="9078e-291">這可以在讀取大型 JSON 承載時使用，或在 JavaScript 中以原始位元組的形式提供資料。</span><span class="sxs-lookup"><span data-stu-id="9078e-291">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="9078e-292">如需示範如何使用類似于元件的技術，在中傳送大型二進位裝載 :::no-loc(Blazor Server)::: 的範例 `InputFile` ，請參閱 [二進位提交範例應用程式](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit)。</span><span class="sxs-lookup"><span data-stu-id="9078e-292">For an example that demonstrates sending large binary payloads in :::no-loc(Blazor Server)::: that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="9078e-293">開發在 JavaScript 與之間傳輸大量資料的程式碼時，請考慮下列指導方針 :::no-loc(Blazor)::: ：</span><span class="sxs-lookup"><span data-stu-id="9078e-293">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and :::no-loc(Blazor)::::</span></span>

* <span data-ttu-id="9078e-294">將資料分割成較小的片段，並依序傳送資料區段，直到伺服器收到所有資料為止。</span><span class="sxs-lookup"><span data-stu-id="9078e-294">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="9078e-295">請勿在 JavaScript 和 c # 程式碼中配置大型物件。</span><span class="sxs-lookup"><span data-stu-id="9078e-295">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="9078e-296">傳送或接收資料時，請勿封鎖主要 UI 執行緒長時間。</span><span class="sxs-lookup"><span data-stu-id="9078e-296">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="9078e-297">釋放處理常式完成或取消時所耗用的記憶體。</span><span class="sxs-lookup"><span data-stu-id="9078e-297">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="9078e-298">基於安全性考慮，強制執行下列其他需求：</span><span class="sxs-lookup"><span data-stu-id="9078e-298">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="9078e-299">宣告可傳遞的檔案或資料大小上限。</span><span class="sxs-lookup"><span data-stu-id="9078e-299">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="9078e-300">宣告從用戶端到伺服器的最小上傳速率。</span><span class="sxs-lookup"><span data-stu-id="9078e-300">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="9078e-301">當伺服器收到資料之後，資料可以是：</span><span class="sxs-lookup"><span data-stu-id="9078e-301">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="9078e-302">暫時儲存在記憶體緩衝區中，直到收集所有區段為止。</span><span class="sxs-lookup"><span data-stu-id="9078e-302">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="9078e-303">立即使用。</span><span class="sxs-lookup"><span data-stu-id="9078e-303">Consumed immediately.</span></span> <span data-ttu-id="9078e-304">例如，資料可以立即儲存在資料庫中，或在收到每個區段時寫入磁片。</span><span class="sxs-lookup"><span data-stu-id="9078e-304">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9078e-305">其他資源</span><span class="sxs-lookup"><span data-stu-id="9078e-305">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="9078e-306">InteropComponent razor 範例 (dotnet/AspNetCore GitHub 存放庫，3.1 版本分支) </span><span class="sxs-lookup"><span data-stu-id="9078e-306">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
