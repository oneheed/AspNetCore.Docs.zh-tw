---
title: 從 ASP.NET Core 中的 .NET 方法呼叫 JavaScript 函數 Blazor
author: guardrex
description: 瞭解如何在應用程式中叫用 .NET 方法的 JavaScript 函式 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/25/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: b5447a55dfa9e4fa55e1a09a3cc00e1f1a965c6e
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394514"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a>從 ASP.NET Core 中的 .NET 方法呼叫 JavaScript 函數 Blazor

Blazor應用程式可以從 javascript 函式的 .net 方法和 .net 方法中叫用 javascript 函式。 這些案例稱為 *JavaScript 互通性* (*JS interop*) 。

本文涵蓋從 .NET 叫用 JavaScript 函式的功能。 如需如何從 JavaScript 呼叫 .NET 方法的詳細資訊，請參閱 <xref:blazor/call-dotnet-from-javascript> 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

> [!NOTE]
> 在檔案 `<script>` `</body>` `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` () 中的結束記號之前，將 JS 檔案新增 (標記) Blazor Server 。 確定具有 JS interop 方法的 JS 檔案包含在 Blazor FRAMEWORK JS 檔案之前。

若要從 .NET 呼叫 JavaScript，請使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念。 若要發出 JS interop 呼叫，請 <xref:Microsoft.JSInterop.IJSRuntime> 在您的元件中插入抽象概念。 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 取得您想要叫用之 JavaScript 函式的識別碼，以及任何數目的 JSON 可序列化引數。 函數識別碼是相對於全域範圍 (`window`) 。 如果您想要呼叫 `window.someScope.someFunction` ，則識別碼為 `someScope.someFunction` 。 在呼叫函式之前，不需要先註冊函式。 傳回型別 `T` 也必須是 JSON 可序列化。 `T` 應符合最適合對應至所傳回 JSON 型別的 .NET 型別。

傳回 [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 的 JavaScript 函式是使用來呼叫 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 。 `InvokeAsync` 解除包裝承諾，並傳回承諾所等待的值。

針對 Blazor Server 已啟用可執行處理的應用程式，在初始未處理期間無法呼叫 JavaScript。 必須先延遲 JavaScript interop 呼叫，才能建立與瀏覽器的連接。 如需詳細資訊，請參閱「偵測 [ Blazor Server 應用程式何時進行](#detect-when-a-blazor-server-app-is-prerendering) 偵測」一節。

下列範例是 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) 以 JavaScript 為基礎的解碼器為基礎。 此範例示範如何從 c # 方法叫用 JavaScript 函式，該方法會從開發人員程式碼將要求卸載至現有的 JavaScript API。 JavaScript 函式會從 c # 方法接受位元組陣列、將陣列解碼，然後將文字傳回至元件以供顯示。

在 `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 () 的元素內 `Pages/_Host.cshtml` ，提供 JavaScript 函式， Blazor Server 用 `TextDecoder` 來解碼傳遞的陣列並傳回已解碼的值：

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

JavaScript 程式碼（例如上述範例中所示的程式碼）也可以從 JavaScript 檔案載入， (`.js`) 並參考腳本檔案：

```html
<script src="exampleJsInterop.js"></script>
```

下列元件：

* `convertArray` `JS` 當選取元件按鈕 () 時，會叫用 JavaScript 函數 **`Convert Array`** 。
* 呼叫 JavaScript 函數之後，傳遞的陣列就會轉換成字串。 字串會傳回給元件以供顯示。

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

若要使用 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念，請採用下列其中一種方法：

* 將 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念插入 Razor 元件 (`.razor`) ：

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  在 `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 () 的元素內 `Pages/_Host.cshtml` Blazor Server ，提供 `handleTickerChanged` JavaScript 函數。 呼叫函式時，會傳回 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ，而且不會傳回值：

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* 將 <xref:Microsoft.JSInterop.IJSRuntime> 抽象概念插入類別 (`.cs`) ：

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  在 `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 () 的元素內 `Pages/_Host.cshtml` Blazor Server ，提供 `handleTickerChanged` JavaScript 函數。 呼叫函式時，會傳回 `JS.InvokeAsync` 值：

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* 針對使用 [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)的動態內容產生，請使用 `[Inject]` 屬性：

  ```razor
  [Inject]
  IJSRuntime JS { get; set; }
  ```

在本主題隨附的用戶端範例應用程式中，應用程式可以使用兩個 JavaScript 函式來與 DOM 互動，以接收使用者輸入並顯示歡迎訊息：

* `showPrompt`：產生提示以接受使用者輸入 (使用者的名稱) 並將名稱傳回給呼叫者。
* `displayWelcome`：將歡迎訊息從呼叫端指派給具有 of 的 DOM 物件 `id` `welcome` 。

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

將 `<script>` 參考 JavaScript 檔案的標記放置在檔案 `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) 。

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](~/blazor/common/samples/5.x/BlazorServerSample/Pages/_Host.cshtml?highlight=33)]

請勿將 `<script>` 標記放在元件檔中，因為 `<script>` 無法動態更新標記。

.NET 方法會藉由呼叫，與檔案中的 JavaScript 函式進行 interop `exampleJsInterop.js` <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 。

<xref:Microsoft.JSInterop.IJSRuntime>抽象概念是非同步，以允許 Blazor Server 案例。 如果應用程式是應用程式， Blazor WebAssembly 而您想要以同步方式叫用 JavaScript 函式，則改為向下轉換 <xref:Microsoft.JSInterop.IJSInProcessRuntime> 並呼叫 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> 。 我們建議大多數的 JS interop 程式庫都使用非同步 Api，以確保所有案例中都有可用的程式庫。

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 若要在標準[javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中啟用 JavaScript 隔離，請參閱[ Blazor javascript 隔離和物件參考](#blazor-javascript-isolation-and-object-references)一節。

::: moniker-end

範例應用程式包含一個可示範 JS interop 的元件。 元件：

* 透過 JavaScript 提示字元接收使用者輸入。
* 將文字傳回至元件以進行處理。
* 呼叫第二個 JavaScript 函式，此函式會與 DOM 互動以顯示歡迎訊息。

`Pages/JsInterop.razor`:

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
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

1. 當您 `TriggerJsPrompt` 選取元件的按鈕來執行時 **`Trigger JavaScript Prompt`** ， `showPrompt` 會呼叫檔案中提供的 JavaScript 函式 `wwwroot/exampleJsInterop.js` 。
1. 此函式 `showPrompt` 會接受使用者輸入 (使用者的名稱) ，其以 HTML 編碼並傳回至元件。 元件會將使用者名稱儲存在本機變數中 `name` 。
1. 中儲存的字串 `name` 會併入歡迎訊息中，該訊息會傳遞至 JavaScript 函式， `displayWelcome` 這會將歡迎訊息轉譯成標題標記。

## <a name="call-a-void-javascript-function"></a>呼叫 void JavaScript 函數

使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 于下列各項：

* 傳回 [void (0) /void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 或 [未定義](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)的 JavaScript 函數。
* 如果不需要 .NET 就能讀取 JavaScript 呼叫的結果。

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a>偵測 Blazor Server 應用程式何時進行呈現
 
[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="capture-references-to-elements"></a>捕捉元素的參考

某些 JS interop 案例需要 HTML 元素的參考。 例如，UI 程式庫可能需要初始化專案參考，或者您可能需要在元素上呼叫類似命令的 Api，例如 `focus` 或 `play` 。

使用下列方法來捕捉元件中的 HTML 元素參考：

* 將 `@ref` 屬性新增至 HTML 元素。
* 定義類型的欄位， <xref:Microsoft.AspNetCore.Components.ElementReference> 其名稱符合屬性的值 `@ref` 。

下列範例將示範如何捕獲元素的參考 `username` `<input>` ：

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> 只使用專案參考來改變不與互動之空白元素的內容 Blazor 。 當協力廠商 API 將內容提供給元素時，此案例很有用。 因為 Blazor 不會與專案互動，所以在專案和 DOM 的表示之間沒有任何可能發生衝突 Blazor 。
>
> 在下列範例中，將未排序清單的內容 () 是很 *危險* 的， `ul` 因為會 Blazor 與 DOM 互動以將此專案的清單專案填入 (`<li>`) ：
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
> 如果 JS interop 變動元素的內容， `MyList` 並 Blazor 嘗試將差異套用至專案，則差異不會與 DOM 相符。

<xref:Microsoft.AspNetCore.Components.ElementReference>會透過 JS interop 傳遞至 JavaScript 程式碼。 JavaScript 程式碼會接收 `HTMLElement` 可搭配一般 DOM api 使用的實例。 例如，下列程式碼會定義可讓您將滑鼠點擊傳送至專案的 .NET 擴充方法：

`exampleJsInterop.js`:

```javascript
window.interopFunctions = {
  clickElement : function (element) {
    element.click();
  }
}
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> [`FocusAsync`](xref:blazor/components/event-handling#focus-an-element)在 c # 程式碼中使用，將內建于架構中的專案， Blazor 並搭配專案參考使用。

::: moniker-end

若要呼叫不會傳回值的 JavaScript 函式，請使用 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> 。 下列程式碼會 `Click` 使用所捕捉的先前 JavaScript 函式來呼叫之前的 JavaScript 函式，以觸發用戶端事件 <xref:Microsoft.AspNetCore.Components.ElementReference> ：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=14-15)]

若要使用擴充方法，請建立可接收實例的靜態擴充方法 <xref:Microsoft.JSInterop.IJSRuntime> ：

```csharp
public static async Task TriggerClickEvent(this ElementReference elementRef, 
    IJSRuntime js)
{
    await js.InvokeVoidAsync("interopFunctions.clickElement", elementRef);
}
```

`clickElement`方法會直接在物件上呼叫。 下列範例假設 `TriggerClickEvent` 方法可從 `JsInteropClasses` 命名空間中取得：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=15)]

> [!IMPORTANT]
> `exampleButton`變數只會在呈現元件之後填入。 如果將擴展 <xref:Microsoft.AspNetCore.Components.ElementReference> 傳遞至 javascript 程式碼，javascript 程式碼會收到的值 `null` 。 若要在元件完成轉譯後操作元素參考，請使用[ `OnAfterRenderAsync` 或 `OnAfterRender` 元件生命週期方法](xref:blazor/components/lifecycle#after-component-render)。

使用泛型型別並傳回值時，請使用 <xref:System.Threading.Tasks.ValueTask%601> ：

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime js)
{
    return js.InvokeAsync<T>("exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod` 會直接在具有類型的物件上呼叫。 下列範例假設 `GenericMethod` 可從 `JsInteropClasses` 命名空間取得：

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>跨元件參考元素

<xref:Microsoft.AspNetCore.Components.ElementReference>無法在元件之間傳遞，因為：

* 只有在轉譯元件之後（在元件的方法執行期間或之後），才能保證實例存在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 。
* <xref:Microsoft.AspNetCore.Components.ElementReference>是 [`struct`](/csharp/language-reference/builtin-types/struct) ，無法以[元件參數](xref:blazor/components/index#component-parameters)形式傳遞。

若要讓父元件能讓元素參考可供其他元件使用，父元件可以：

* 允許子元件註冊回呼。
* 使用傳遞的元素參考，在事件期間叫用已註冊的回呼 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 。 這種方法間接可讓子元件與父系的元素參考互動。

下列 Blazor WebAssembly 範例說明此方法。

在 `<head>` 的 `wwwroot/index.html` ：

```html
<style>
    .red { color: red }
</style>
```

在 `<body>` 的 `wwwroot/index.html` ：

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor` (父元件) ：

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

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

預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

`Shared/SurveyPrompt.razor` (子元件) ：

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

`Shared/SurveyPrompt.razor.cs`:

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

預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

## <a name="harden-js-interop-calls"></a>強化 JS interop 呼叫

JS interop 可能因為網路錯誤而失敗，應該視為不可靠。 根據預設， Blazor Server 應用程式會在一分鐘後，在伺服器上使用 JS interop 呼叫。 如果應用程式可容忍更積極的超時，請使用下列其中一種方法來設定 timeout：

* 在中 `Startup.ConfigureServices` ，指定 timeout：

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* 元件程式碼中的每個調用，單一呼叫可以指定 timeout：

  ```csharp
  var result = await JS.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

如需資源耗盡的詳細資訊，請參閱 <xref:blazor/security/server/threat-mitigation> 。

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>避免迴圈物件參考

包含迴圈參考的物件無法在用戶端上針對下列任一項進行序列化：

* .NET 方法呼叫。
* 當傳回型別有迴圈參考時，來自 c # 的 JavaScript 方法呼叫。

如需詳細資訊，請參閱 [不支援的迴圈參考， (的 dotnet/aspnetcore #20525) ](https://github.com/dotnet/aspnetcore/issues/20525)。

::: moniker range=">= aspnetcore-5.0"

## <a name="blazor-javascript-isolation-and-object-references"></a>Blazor JavaScript 隔離和物件參考

Blazor 啟用標準 [javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中的 JavaScript 隔離。 JavaScript 隔離提供下列優點：

* 匯入的 JavaScript 不再干擾全域命名空間。
* 程式庫和元件的取用者不需要匯入相關的 JavaScript。

例如，下列 JavaScript 模組會匯出 JavaScript 函式，以顯示瀏覽器提示：

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

將上述 JavaScript 模組新增至 .NET 程式庫作為靜態 web 資產 (`wwwroot/exampleJsInterop.js`) ，然後 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 在服務上呼叫，將模組匯入至 .net 程式碼 <xref:Microsoft.JSInterop.IJSRuntime> 。 服務會插入為 `js` 下列範例中未顯示的 () ：

```csharp
var module = await js.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

`import`上述範例中的識別碼是專門用來匯入 JavaScript 模組的特殊識別碼。 使用其穩定靜態 web 資產路徑來指定模組： `./_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` 。 需要 () 的路徑區段，才能 `./` 建立 JavaScript 檔案的正確靜態資產路徑。 以動態方式匯入模組需要網路要求，因此只能藉由呼叫來以非同步方式完成 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 。 `{LIBRARY NAME}`預留位置是程式庫名稱。 `{PATH UNDER WWWROOT}`預留位置是下腳本的路徑 `wwwroot` 。

<xref:Microsoft.JSInterop.IJSRuntime> 將模組匯入為 `IJSObjectReference` ，表示從 .net 程式碼到 JavaScript 物件的參考。 使用來叫用 `IJSObjectReference` 模組中匯出的 JavaScript 函式：

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

`IJSInProcessObjectReference` 表示 JavaScript 物件的參考，該物件的函式可以同步叫用。

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a>使用可轉譯 UI (DOM 元素的 JavaScript 程式庫) 

有時您可能會想要使用 JavaScript 程式庫，在瀏覽器 DOM 內產生可見的使用者介面元素。 乍看之下，這似乎很難，因為比較 Blazor 系統需要控制 dom 元素的樹狀結構，而且如果某些外部程式碼變動 dom 樹狀結構，並使其套用差異的機制失效，就會發生錯誤。 這並不是 Blazor 特定的限制。 任何差異型 UI 架構都會發生相同的挑戰。

幸運的是，將外部產生的 UI 可靠地內嵌在 Blazor 元件 UI 中相當簡單。 建議的技巧是讓元件的程式碼 (檔案 `.razor`) 產生空的元素。 在比較 Blazor 系統的考慮下，元素一律是空的，因此轉譯器不會遞迴到專案中，而是只保留其內容。 這可讓您安全地在專案中填入任意外部管理的內容。

下列範例示範概念。 在語句中的 `if` 時 `firstRender` `true` ，請使用來做一些動作 `myElement` 。 例如，呼叫外部 JavaScript 程式庫來填入它。 Blazor 只保留專案的內容，直到移除此元件本身為止。 移除元件時，也會移除元件的整個 DOM 子樹。

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

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

如需更詳細的範例，請考慮使用 [開放原始碼 Mapbox api](https://www.mapbox.com/)呈現互動式地圖的下列元件：

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

對應的 JavaScript 模組（應放置於 `wwwroot/mapComponent.js` ）如下所示：

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

在上述範例中，請將字串取代為 `{ACCESS TOKEN}` 可從中取得的有效存取權杖 https://account.mapbox.com 。

若要產生正確的樣式，請將下列樣式表單標記新增至主控制項 HTML 網頁 (`index.html` 或 `_Host.cshtml`) ：

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

上述範例會產生互動式地圖 UI，使用者在其中：

* 可以拖曳到滾動或縮放。
* 按一下按鈕以跳到預先定義的位置。

![東京、日本和 Mapbox 的街道地圖，可選擇 Bristol、英國和東京、日本](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

要瞭解的重點是：

 * 在 `<div>` 考慮時，with `@ref="mapElement"` 會保持空白 Blazor 。 因此，在一段 `mapbox-gl.js` 時間內填入並修改其內容是安全的。 您可以使用這項技術搭配任何轉譯 UI 的 JavaScript 程式庫。 您甚至可以將協力廠商 JavaScript SPA 架構中的元件內嵌在 Blazor 元件中，只要它們不會嘗試連接並修改頁面的其他部分即可。 外部 JavaScript 程式碼修改不是空的元素並 *不* 安全 Blazor 。
 * 使用這種方法時，請記住有關如何 Blazor 保留或終結 DOM 元素的規則。 在上述範例中，元件會安全地處理按鈕的 click 事件，並更新現有的對應實例，因為預設會保留 DOM 元素。 如果您要從迴圈內轉譯地圖元素清單 `@foreach` ，您想要使用 `@key` 確保元件實例的保留。 否則，清單資料中的變更可能會導致元件實例以不當的方式保留先前實例的狀態。 如需詳細資訊，請參閱 [使用 @key 來保留元素和元件](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components)。

此外，上述範例示範如何將 JavaScript 邏輯和相依性封裝在 ES6 模組內，並使用識別碼動態載入它 `import` 。 如需詳細資訊，請參閱 [JavaScript 隔離和物件參考](#blazor-javascript-isolation-and-object-references)。

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a>JS interop 呼叫的大小限制

在中 Blazor WebAssembly ，架構不會限制 JS interop 輸入和輸出的大小。

在中 Blazor Server ，JS interop 呼叫的大小受限於中樞方法所允許的最大傳入 SignalR 訊息大小，這會由 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (預設值： 32 KB) 來強制執行。 JS 至 .NET 的 SignalR 訊息大於 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> 擲回錯誤。 此架構不會限制 SignalR 從中樞到用戶端的訊息大小限制。 如需詳細資訊，請參閱<xref:blazor/call-dotnet-from-javascript#size-limits-on-js-interop-calls>。
  
## <a name="js-modules"></a>JS 模組

針對 JS 隔離，JS interop 適用于瀏覽器的 [EcmaScript 模組預設支援 (ESM) ](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ecmascript 規格](https://tc39.es/ecma262/#sec-modules)) 。

## <a name="unmarshalled-js-interop"></a>Unmarshalled JS interop

Blazor WebAssembly 當 .NET 物件序列化為 JS interop 且符合下列其中一項條件時，元件可能會遇到效能不佳的情況：

* 大量的 .NET 物件會迅速進行序列化。 範例： JS interop 呼叫是根據移動輸入裝置（例如旋轉滑鼠滾輪）來進行。
* 大型 .NET 物件或許多 .NET 物件必須針對 JS interop 進行序列化。 範例： JS interop 呼叫需要序列化數十個檔案。

<xref:Microsoft.JSInterop.IJSUnmarshalledObjectReference> 代表 JavaScript 物件的參考，該物件的函式可以叫用，而不會有序列化 .NET 資料的額外負荷。

在下例中︰

* 包含字串和整數的 [結構](/dotnet/csharp/language-reference/builtin-types/struct) 會 unserialized 傳遞給 JavaScript。
* JavaScript 函式會處理資料，並將布林值或字串傳回給呼叫者。
* JavaScript 字串無法直接轉換成 .NET `string` 物件。 `unmarshalledFunctionReturnString`函式呼叫 `BINDING.js_string_to_mono_string` 來管理 JAVAscript 字串的轉換。

> [!NOTE]
> 下列範例不是此案例的一般使用案例，因為傳遞至 JavaScript 的 [結構](/dotnet/csharp/language-reference/builtin-types/struct) 不會導致元件效能不良。 此範例只會使用小型物件來示範傳遞 unserialized .NET 資料的概念。

`<script>`中區塊的內容， `wwwroot/index.html` 或所參考的外部 JAVAscript 檔案 `wwwroot/index.html` ：

```javascript
window.returnJSObjectReference = () => {
    return {
        unmarshalledFunctionReturnBoolean: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return name === "Brigadier Alistair Gordon Lethbridge-Stewart" &&
                year === 1968;
        },
        unmarshalledFunctionReturnString: function (fields) {
            const name = Blazor.platform.readStringField(fields, 0);
            const year = Blazor.platform.readInt32Field(fields, 8);

            return BINDING.js_string_to_mono_string(`Hello, ${name} (${year})!`);
        }
    };
}
```

> [!WARNING]
> 函式 `js_string_to_mono_string` 名稱、行為和存在性在未來的 .net 版本中可能會有所變更。 例如：
>
> * 函數很可能會重新命名。
> * 函數本身可能會被移除，而不是由架構自動轉換字串。

`Pages/UnmarshalledJSInterop.razor` (URL： `/unmarshalled-js-interop`) ：

```razor
@page "/unmarshalled-js-interop"
@using System.Runtime.InteropServices
@using Microsoft.JSInterop
@inject IJSRuntime JS

<h1>Unmarshalled JS interop</h1>

@if (callResultForBoolean)
{
    <p>JS interop was successful!</p>
}

@if (!string.IsNullOrEmpty(callResultForString))
{
    <p>@callResultForString</p>
}

<p>
    <button @onclick="CallJSUnmarshalledForBoolean">
        Call Unmarshalled JS & Return Boolean
    </button>
    <button @onclick="CallJSUnmarshalledForString">
        Call Unmarshalled JS & Return String
    </button>
</p>

<p>
    <a href="https://www.doctorwho.tv">Doctor Who</a>
    is a registered trademark of the <a href="https://www.bbc.com/">BBC</a>.
</p>

@code {
    private bool callResultForBoolean;
    private string callResultForString;

    private void CallJSUnmarshalledForBoolean()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForBoolean = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, bool>(
                "unmarshalledFunctionReturnBoolean", GetStruct());
    }

    private void CallJSUnmarshalledForString()
    {
        var unmarshalledRuntime = (IJSUnmarshalledRuntime)JS;

        var jsUnmarshalledReference = unmarshalledRuntime
            .InvokeUnmarshalled<IJSUnmarshalledObjectReference>(
                "returnJSObjectReference");

        callResultForString = 
            jsUnmarshalledReference.InvokeUnmarshalled<InteropStruct, string>(
                "unmarshalledFunctionReturnString", GetStruct());
    }

    private InteropStruct GetStruct()
    {
        return new InteropStruct
        {
            Name = "Brigadier Alistair Gordon Lethbridge-Stewart",
            Year = 1968,
        };
    }

    [StructLayout(LayoutKind.Explicit)]
    public struct InteropStruct
    {
        [FieldOffset(0)]
        public string Name;

        [FieldOffset(8)]
        public int Year;
    }
}
```

如果 `IJSUnmarshalledObjectReference` 實例未在 c # 程式碼中處置，則可以使用 JavaScript 加以處置。 下列函式會 `dispose` 在從 JavaScript 呼叫時處置物件參考：

```javascript
window.exampleJSObjectReferenceNotDisposedInCSharp = () => {
    return {
        dispose: function () {
            DotNet.disposeJSObjectReference(this);
        },

        ...
    };
}
```

您可以使用將陣列類型從 JavaScript 物件轉換成 .NET 物件 `js_typed_array_to_array` ，但 javascript 陣列必須是具類型的陣列。 您可以在 c # 程式碼中，以 .NET 物件陣列的形式讀取 JavaScript 的陣列 (`object[]`) 。

其他資料類型（例如字串陣列）可以進行轉換，但需要建立新的 Mono 陣列物件 (`mono_obj_array_new`) ，並將其值 (`mono_obj_array_set`) 。

> [!WARNING]
> 架構所提供的 JavaScript 函式（ Blazor 例如 `js_typed_array_to_array` 、 `mono_obj_array_new` 和 `mono_obj_array_set` ）會受限於名稱變更、行為變更，或在未來的 .net 版本中移除。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/call-dotnet-from-javascript>
* [ `InteropComponent.razor` 範例 (Dotnet/AspNetCore GitHub 存放庫 `main` 分支) ](https://github.com/dotnet/AspNetCore/blob/main/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)： `main` 分支代表下一版 ASP.NET Core 的產品單位目前開發。 若要選取不同版本的分支 (例如 `release/5.0`) ，請使用 [ **切換分支或標記** ] 下拉式清單來選取分支。
