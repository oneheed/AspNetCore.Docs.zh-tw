---
title: 從 ASP.NET Core 中的 .NET 方法呼叫 JavaScript 函式 Blazor
author: guardrex
description: 瞭解如何在應用程式中叫用 .NET 方法的 JavaScript 函式 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: a62462e3a0a2366a8662573ada5d2e7589c14c0d
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722471"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="fff62-103">從 ASP.NET Core 中的 .NET 方法呼叫 JavaScript 函式 Blazor</span><span class="sxs-lookup"><span data-stu-id="fff62-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="fff62-104">[Javier Calvarro Nelson](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fff62-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fff62-105">Blazor應用程式可以從 javascript 函式的 .net 方法和 .net 方法中叫用 javascript 函式。</span><span class="sxs-lookup"><span data-stu-id="fff62-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="fff62-106">這些案例稱為 *JavaScript 互通性* (*JS interop*) 。</span><span class="sxs-lookup"><span data-stu-id="fff62-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="fff62-107">本文涵蓋從 .NET 叫用 JavaScript 函式的功能。</span><span class="sxs-lookup"><span data-stu-id="fff62-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="fff62-108">如需如何從 JavaScript 呼叫 .NET 方法的詳細資訊，請參閱 <xref:blazor/call-dotnet-from-javascript> 。</span><span class="sxs-lookup"><span data-stu-id="fff62-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="fff62-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="fff62-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fff62-110">若要從 .NET 呼叫 JavaScript，請使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念。</span><span class="sxs-lookup"><span data-stu-id="fff62-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="fff62-111">若要發出 JS interop 呼叫，請 <xref:Microsoft.JSInterop.IJSRuntime> 在您的元件中插入抽象概念。</span><span class="sxs-lookup"><span data-stu-id="fff62-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="fff62-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 取得您想要叫用之 JavaScript 函式的識別碼，以及任何數目的 JSON 可序列化引數。</span><span class="sxs-lookup"><span data-stu-id="fff62-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="fff62-113">函數識別碼是相對於全域範圍 (`window`) 。</span><span class="sxs-lookup"><span data-stu-id="fff62-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="fff62-114">如果您想要呼叫 `window.someScope.someFunction` ，則識別碼為 `someScope.someFunction` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="fff62-115">在呼叫函式之前，不需要先註冊函式。</span><span class="sxs-lookup"><span data-stu-id="fff62-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="fff62-116">傳回型別 `T` 也必須是 JSON 可序列化。</span><span class="sxs-lookup"><span data-stu-id="fff62-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="fff62-117">`T` 應符合最適合對應至所傳回 JSON 型別的 .NET 型別。</span><span class="sxs-lookup"><span data-stu-id="fff62-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="fff62-118">針對 Blazor Server 已啟用可執行處理的應用程式，在初始未處理期間無法呼叫 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="fff62-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="fff62-119">必須先延遲 JavaScript interop 呼叫，才能建立與瀏覽器的連接。</span><span class="sxs-lookup"><span data-stu-id="fff62-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="fff62-120">如需詳細資訊，請參閱「偵測 [ Blazor Server 應用程式何時進行](#detect-when-a-blazor-server-app-is-prerendering) 偵測」一節。</span><span class="sxs-lookup"><span data-stu-id="fff62-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="fff62-121">下列範例是 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) 以 JavaScript 為基礎的解碼器為基礎。</span><span class="sxs-lookup"><span data-stu-id="fff62-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="fff62-122">此範例示範如何從 c # 方法叫用 JavaScript 函式，該方法會從開發人員程式碼將要求卸載至現有的 JavaScript API。</span><span class="sxs-lookup"><span data-stu-id="fff62-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="fff62-123">JavaScript 函式會從 c # 方法接受位元組陣列、將陣列解碼，然後將文字傳回至元件以供顯示。</span><span class="sxs-lookup"><span data-stu-id="fff62-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="fff62-124">在 `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 () 的元素內 `Pages/_Host.cshtml` ，提供 JavaScript 函式， Blazor Server 用 `TextDecoder` 來解碼傳遞的陣列並傳回已解碼的值：</span><span class="sxs-lookup"><span data-stu-id="fff62-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="fff62-125">JavaScript 程式碼（例如上述範例中所示的程式碼）也可以從 JavaScript 檔案載入， (`.js`) 並參考腳本檔案：</span><span class="sxs-lookup"><span data-stu-id="fff62-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="fff62-126">下列元件：</span><span class="sxs-lookup"><span data-stu-id="fff62-126">The following component:</span></span>

* <span data-ttu-id="fff62-127">`convertArray` `JSRuntime` 當選取元件按鈕 () 時，會叫用 JavaScript 函數 **`Convert Array`** 。</span><span class="sxs-lookup"><span data-stu-id="fff62-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="fff62-128">呼叫 JavaScript 函數之後，傳遞的陣列就會轉換成字串。</span><span class="sxs-lookup"><span data-stu-id="fff62-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="fff62-129">字串會傳回給元件以供顯示。</span><span class="sxs-lookup"><span data-stu-id="fff62-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="fff62-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="fff62-130">IJSRuntime</span></span>

<span data-ttu-id="fff62-131">若要使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念，請採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="fff62-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="fff62-132">將 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念插入 Razor 元件 (`.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="fff62-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="fff62-133">在 `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 () 的元素內 `Pages/_Host.cshtml` Blazor Server ，提供 `handleTickerChanged` JavaScript 函數。</span><span class="sxs-lookup"><span data-stu-id="fff62-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="fff62-134">呼叫函式時，會傳回 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ，而且不會傳回值：</span><span class="sxs-lookup"><span data-stu-id="fff62-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="fff62-135">將 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念插入類別 (`.cs`) ：</span><span class="sxs-lookup"><span data-stu-id="fff62-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="fff62-136">在 `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 () 的元素內 `Pages/_Host.cshtml` Blazor Server ，提供 `handleTickerChanged` JavaScript 函數。</span><span class="sxs-lookup"><span data-stu-id="fff62-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="fff62-137">呼叫函式時，會傳回 `JSRuntime.InvokeAsync` 值：</span><span class="sxs-lookup"><span data-stu-id="fff62-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="fff62-138">針對使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)的動態內容產生，請使用 `[Inject]` 屬性：</span><span class="sxs-lookup"><span data-stu-id="fff62-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="fff62-139">在本主題隨附的用戶端範例應用程式中，應用程式可以使用兩個 JavaScript 函式來與 DOM 互動，以接收使用者輸入並顯示歡迎訊息：</span><span class="sxs-lookup"><span data-stu-id="fff62-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="fff62-140">`showPrompt`：產生提示以接受使用者輸入 (使用者的名稱) 並將名稱傳回給呼叫者。</span><span class="sxs-lookup"><span data-stu-id="fff62-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="fff62-141">`displayWelcome`：將歡迎訊息從呼叫端指派給具有 of 的 DOM 物件 `id` `welcome` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="fff62-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="fff62-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="fff62-143">將 `<script>` 參考 JavaScript 檔案的標記放置在檔案 `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) 。</span><span class="sxs-lookup"><span data-stu-id="fff62-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="fff62-144">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="fff62-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="fff62-145">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="fff62-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="fff62-146">請勿將 `<script>` 標記放在元件檔中，因為 `<script>` 無法動態更新標記。</span><span class="sxs-lookup"><span data-stu-id="fff62-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="fff62-147">.NET 方法會藉由呼叫，與檔案中的 JavaScript 函式進行 interop `exampleJsInterop.js` <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="fff62-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="fff62-148"><xref:Microsoft.JSInterop.IJSRuntime>抽象概念是非同步，以允許 Blazor Server 案例。</span><span class="sxs-lookup"><span data-stu-id="fff62-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="fff62-149">如果應用程式是應用程式， Blazor WebAssembly 而您想要以同步方式叫用 JavaScript 函式，則改為向下轉換 <xref:Microsoft.JSInterop.IJSInProcessRuntime> 並呼叫 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> 。</span><span class="sxs-lookup"><span data-stu-id="fff62-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="fff62-150">我們建議大多數的 JS interop 程式庫都使用非同步 Api，以確保所有案例中都有可用的程式庫。</span><span class="sxs-lookup"><span data-stu-id="fff62-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="fff62-151">若要在標準[javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中啟用 JavaScript 隔離，請參閱[ Blazor javascript 隔離和物件參考](#blazor-javascript-isolation-and-object-references)一節。</span><span class="sxs-lookup"><span data-stu-id="fff62-151">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="fff62-152">範例應用程式包含一個可示範 JS interop 的元件。</span><span class="sxs-lookup"><span data-stu-id="fff62-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="fff62-153">元件：</span><span class="sxs-lookup"><span data-stu-id="fff62-153">The component:</span></span>

* <span data-ttu-id="fff62-154">透過 JavaScript 提示字元接收使用者輸入。</span><span class="sxs-lookup"><span data-stu-id="fff62-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="fff62-155">將文字傳回至元件以進行處理。</span><span class="sxs-lookup"><span data-stu-id="fff62-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="fff62-156">呼叫第二個 JavaScript 函式，此函式會與 DOM 互動以顯示歡迎訊息。</span><span class="sxs-lookup"><span data-stu-id="fff62-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="fff62-157">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="fff62-157">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="fff62-158">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="fff62-158">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="fff62-159">當您 `TriggerJsPrompt` 選取元件的按鈕來執行時 **`Trigger JavaScript Prompt`** ， `showPrompt` 會呼叫檔案中提供的 JavaScript 函式 `wwwroot/exampleJsInterop.js` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-159">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="fff62-160">此函式 `showPrompt` 會接受使用者輸入 (使用者的名稱) ，其以 HTML 編碼並傳回至元件。</span><span class="sxs-lookup"><span data-stu-id="fff62-160">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="fff62-161">元件會將使用者名稱儲存在本機變數中 `name` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-161">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="fff62-162">中儲存的字串 `name` 會併入歡迎訊息中，該訊息會傳遞至 JavaScript 函式， `displayWelcome` 這會將歡迎訊息轉譯成標題標記。</span><span class="sxs-lookup"><span data-stu-id="fff62-162">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="fff62-163">呼叫 void JavaScript 函數</span><span class="sxs-lookup"><span data-stu-id="fff62-163">Call a void JavaScript function</span></span>

<span data-ttu-id="fff62-164">傳回 [void (0) /void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [未定義](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) 的 JavaScript 函數會使用來呼叫 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="fff62-164">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="fff62-165">偵測 Blazor Server 應用程式何時進行呈現</span><span class="sxs-lookup"><span data-stu-id="fff62-165">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="fff62-166">捕捉元素的參考</span><span class="sxs-lookup"><span data-stu-id="fff62-166">Capture references to elements</span></span>

<span data-ttu-id="fff62-167">某些 JS interop 案例需要 HTML 元素的參考。</span><span class="sxs-lookup"><span data-stu-id="fff62-167">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="fff62-168">例如，UI 程式庫可能需要初始化專案參考，或者您可能需要在元素上呼叫類似命令的 Api，例如 `focus` 或 `play` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-168">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="fff62-169">使用下列方法來捕捉元件中的 HTML 元素參考：</span><span class="sxs-lookup"><span data-stu-id="fff62-169">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="fff62-170">將 `@ref` 屬性新增至 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="fff62-170">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="fff62-171">定義類型的欄位， <xref:Microsoft.AspNetCore.Components.ElementReference> 其名稱符合屬性的值 `@ref` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-171">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="fff62-172">下列範例將示範如何捕獲元素的參考 `username` `<input>` ：</span><span class="sxs-lookup"><span data-stu-id="fff62-172">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="fff62-173">只使用專案參考來改變不與互動之空白元素的內容 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="fff62-173">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="fff62-174">當協力廠商 API 將內容提供給元素時，此案例很有用。</span><span class="sxs-lookup"><span data-stu-id="fff62-174">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="fff62-175">因為 Blazor 不會與專案互動，所以在專案和 DOM 的表示之間沒有任何可能發生衝突 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="fff62-175">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="fff62-176">在下列範例中，將未排序清單的內容 () 是很 *危險* 的， `ul` 因為會 Blazor 與 DOM 互動以將此專案的清單專案填入 (`<li>`) ：</span><span class="sxs-lookup"><span data-stu-id="fff62-176">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="fff62-177">如果 JS interop 變動元素的內容， `MyList` 並 Blazor 嘗試將差異套用至專案，則差異不會與 DOM 相符。</span><span class="sxs-lookup"><span data-stu-id="fff62-177">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="fff62-178">至於 .NET 程式碼， <xref:Microsoft.AspNetCore.Components.ElementReference> 則是不透明的控制碼。</span><span class="sxs-lookup"><span data-stu-id="fff62-178">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="fff62-179">您 *唯一* 可以做的事 <xref:Microsoft.AspNetCore.Components.ElementReference> ，是透過 JS interop 將其傳遞至 JavaScript 程式碼。</span><span class="sxs-lookup"><span data-stu-id="fff62-179">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="fff62-180">當您這樣做時，JavaScript 端程式碼會收到 `HTMLElement` 可搭配一般 DOM api 使用的實例。</span><span class="sxs-lookup"><span data-stu-id="fff62-180">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="fff62-181">例如，下列程式碼會定義可讓您在元素上設定焦點的 .NET 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="fff62-181">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="fff62-182">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="fff62-182">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="fff62-183">若要呼叫不會傳回值的 JavaScript 函式，請使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="fff62-183">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="fff62-184">下列程式碼會藉由使用已捕捉的來呼叫上述 JavaScript 函式，將焦點放在使用者名稱輸入上 <xref:Microsoft.AspNetCore.Components.ElementReference> ：</span><span class="sxs-lookup"><span data-stu-id="fff62-184">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="fff62-185">若要使用擴充方法，請建立可接收實例的靜態擴充方法 <xref:Microsoft.JSInterop.IJSRuntime> ：</span><span class="sxs-lookup"><span data-stu-id="fff62-185">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="fff62-186">`Focus`方法會直接在物件上呼叫。</span><span class="sxs-lookup"><span data-stu-id="fff62-186">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="fff62-187">下列範例假設 `Focus` 方法可從 `JsInteropClasses` 命名空間中取得：</span><span class="sxs-lookup"><span data-stu-id="fff62-187">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="fff62-188">`username`變數只會在呈現元件之後填入。</span><span class="sxs-lookup"><span data-stu-id="fff62-188">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="fff62-189">如果將擴展 <xref:Microsoft.AspNetCore.Components.ElementReference> 傳遞至 javascript 程式碼，javascript 程式碼會收到的值 `null` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-189">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="fff62-190">若要在元件完成轉譯之後操作專案參考 (設定元素的初始焦點) 使用[ `OnAfterRenderAsync` 或 `OnAfterRender` 元件生命週期方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="fff62-190">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="fff62-191">使用泛型型別並傳回值時，請使用 <xref:System.Threading.Tasks.ValueTask%601> ：</span><span class="sxs-lookup"><span data-stu-id="fff62-191">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="fff62-192">`GenericMethod` 會直接在具有類型的物件上呼叫。</span><span class="sxs-lookup"><span data-stu-id="fff62-192">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="fff62-193">下列範例假設 `GenericMethod` 可從 `JsInteropClasses` 命名空間取得：</span><span class="sxs-lookup"><span data-stu-id="fff62-193">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="fff62-194">跨元件參考元素</span><span class="sxs-lookup"><span data-stu-id="fff62-194">Reference elements across components</span></span>

<span data-ttu-id="fff62-195"><xref:Microsoft.AspNetCore.Components.ElementReference>只有在元件的 (方法中才保證有效， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 而專案參考是 `struct`) ，因此無法在元件之間傳遞元素參考。</span><span class="sxs-lookup"><span data-stu-id="fff62-195">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="fff62-196">若要讓父元件能讓元素參考可供其他元件使用，父元件可以：</span><span class="sxs-lookup"><span data-stu-id="fff62-196">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="fff62-197">允許子元件註冊回呼。</span><span class="sxs-lookup"><span data-stu-id="fff62-197">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="fff62-198">使用傳遞的元素參考，在事件期間叫用已註冊的回呼 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 。</span><span class="sxs-lookup"><span data-stu-id="fff62-198">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="fff62-199">這種方法間接可讓子元件與父系的元素參考互動。</span><span class="sxs-lookup"><span data-stu-id="fff62-199">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="fff62-200">下列 Blazor WebAssembly 範例說明此方法。</span><span class="sxs-lookup"><span data-stu-id="fff62-200">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="fff62-201">在 `<head>` 的 `wwwroot/index.html` ：</span><span class="sxs-lookup"><span data-stu-id="fff62-201">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="fff62-202">在 `<body>` 的 `wwwroot/index.html` ：</span><span class="sxs-lookup"><span data-stu-id="fff62-202">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="fff62-203">`Pages/Index.razor` (父元件) ：</span><span class="sxs-lookup"><span data-stu-id="fff62-203">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="fff62-204">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="fff62-204">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="fff62-205">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="fff62-205">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="fff62-206">`Shared/SurveyPrompt.razor` (子元件) ：</span><span class="sxs-lookup"><span data-stu-id="fff62-206">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="fff62-207">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="fff62-207">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="fff62-208">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="fff62-208">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="fff62-209">強化 JS interop 呼叫</span><span class="sxs-lookup"><span data-stu-id="fff62-209">Harden JS interop calls</span></span>

<span data-ttu-id="fff62-210">JS interop 可能因為網路錯誤而失敗，應該視為不可靠。</span><span class="sxs-lookup"><span data-stu-id="fff62-210">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="fff62-211">根據預設， Blazor Server 應用程式會在一分鐘後，在伺服器上使用 JS interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="fff62-211">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="fff62-212">如果應用程式可容忍更積極的超時，請使用下列其中一種方法來設定 timeout：</span><span class="sxs-lookup"><span data-stu-id="fff62-212">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="fff62-213">在中 `Startup.ConfigureServices` ，指定 timeout：</span><span class="sxs-lookup"><span data-stu-id="fff62-213">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="fff62-214">元件程式碼中的每個調用，單一呼叫可以指定 timeout：</span><span class="sxs-lookup"><span data-stu-id="fff62-214">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="fff62-215">如需資源耗盡的詳細資訊，請參閱 <xref:blazor/security/server/threat-mitigation> 。</span><span class="sxs-lookup"><span data-stu-id="fff62-215">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="fff62-216">避免迴圈物件參考</span><span class="sxs-lookup"><span data-stu-id="fff62-216">Avoid circular object references</span></span>

<span data-ttu-id="fff62-217">包含迴圈參考的物件無法在用戶端上針對下列任一項進行序列化：</span><span class="sxs-lookup"><span data-stu-id="fff62-217">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="fff62-218">.NET 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="fff62-218">.NET method calls.</span></span>
* <span data-ttu-id="fff62-219">當傳回型別有迴圈參考時，來自 c # 的 JavaScript 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="fff62-219">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="fff62-220">如需詳細資訊，請參閱下列問題：</span><span class="sxs-lookup"><span data-stu-id="fff62-220">For more information, see the following issues:</span></span>

* [<span data-ttu-id="fff62-221">不支援迴圈參考，請採用兩個 (dotnet/aspnetcore #20525) </span><span class="sxs-lookup"><span data-stu-id="fff62-221">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="fff62-222">提案：在將 (dotnet/執行時間 #30820 序列化時，新增處理迴圈參考的機制) </span><span class="sxs-lookup"><span data-stu-id="fff62-222">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="fff62-223">Blazor JavaScript 隔離和物件參考</span><span class="sxs-lookup"><span data-stu-id="fff62-223">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="fff62-224">Blazor 啟用標準 [javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中的 JavaScript 隔離。</span><span class="sxs-lookup"><span data-stu-id="fff62-224">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="fff62-225">JavaScript 隔離提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="fff62-225">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="fff62-226">匯入的 JavaScript 不再干擾全域命名空間。</span><span class="sxs-lookup"><span data-stu-id="fff62-226">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="fff62-227">程式庫和元件的取用者不需要匯入相關的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="fff62-227">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="fff62-228">例如，下列 JavaScript 模組會匯出 JavaScript 函式，以顯示瀏覽器提示：</span><span class="sxs-lookup"><span data-stu-id="fff62-228">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="fff62-229">將上述 JavaScript 模組新增至 .NET 程式庫作為靜態 web 資產 (`wwwroot/exampleJsInterop.js`) 然後使用服務將模組匯入至 .net 程式碼 <xref:Microsoft.JSInterop.IJSRuntime> 。</span><span class="sxs-lookup"><span data-stu-id="fff62-229">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="fff62-230">服務會插入為 `jsRuntime` 下列範例中未顯示的 () ：</span><span class="sxs-lookup"><span data-stu-id="fff62-230">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="fff62-231">`import`上述範例中的識別碼是專門用來匯入 JavaScript 模組的特殊識別碼。</span><span class="sxs-lookup"><span data-stu-id="fff62-231">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="fff62-232">使用其穩定靜態 web 資產路徑來指定模組： `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-232">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="fff62-233">預留位置 `{LIBRARY NAME}` 是程式庫名稱。</span><span class="sxs-lookup"><span data-stu-id="fff62-233">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="fff62-234">預留位置 `{PATH UNDER WWWROOT}` 是下腳本的路徑 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="fff62-234">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="fff62-235"><xref:Microsoft.JSInterop.IJSRuntime> 將模組匯入為 `JSObjectReference` ，表示從 .net 程式碼到 JavaScript 物件的參考。</span><span class="sxs-lookup"><span data-stu-id="fff62-235"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="fff62-236">使用來叫用 `JSObjectReference` 模組中匯出的 JavaScript 函式：</span><span class="sxs-lookup"><span data-stu-id="fff62-236">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fff62-237">其他資源</span><span class="sxs-lookup"><span data-stu-id="fff62-237">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="fff62-238">InteropComponent razor 範例 (dotnet/AspNetCore GitHub 存放庫，3.1 版本分支) </span><span class="sxs-lookup"><span data-stu-id="fff62-238">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="fff62-239">在應用程式中執行大量資料傳輸 Blazor Server</span><span class="sxs-lookup"><span data-stu-id="fff62-239">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
