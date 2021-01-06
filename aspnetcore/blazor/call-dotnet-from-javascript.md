---
title: 從 ASP.NET Core 中的 JavaScript 函式呼叫 .NET 方法 Blazor
author: guardrex
description: 瞭解如何從應用程式中的 JavaScript 函式叫用 .NET 方法 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: c1a97919cb41f42a93f28d9b5f1ecf6bd3e64da0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97592852"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a>從 ASP.NET Core 中的 JavaScript 函式呼叫 .NET 方法 Blazor

[Javier Calvarro Nelson](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Shashikant Rudrawadi](http://wisne.co)和[Luke Latham](https://github.com/guardrex)

Blazor應用程式可以從 javascript 函式的 .net 方法和 .net 方法中叫用 javascript 函式。 這些案例稱為 *JavaScript 互通性* (*JS interop*) 。

本文涵蓋從 JavaScript 叫用 .NET 方法。 如需如何從 .NET 呼叫 JavaScript 函數的詳細資訊，請參閱 <xref:blazor/call-javascript-from-dotnet> 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="static-net-method-call"></a>靜態 .NET 方法呼叫

若要從 JavaScript 叫用靜態 .NET 方法，請使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函數。 傳入您想要呼叫之靜態方法的識別碼、包含函數的元件名稱，以及任何引數。 非同步版本是支援案例的首選 Blazor Server 。 .NET 方法必須是公用、靜態，且具有 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 屬性。 目前不支援呼叫開放式泛型方法。

範例應用程式包含可傳回陣列的 c # 方法 `int` 。 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)屬性會套用至方法。

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

提供給用戶端的 JavaScript 會叫用 c # .NET 方法。

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

當您 **`Trigger .NET static method ReturnArrayAsync`** 選取此按鈕時，請檢查瀏覽器的 網頁程式開發人員工具中的主控台輸出。

主控台輸出為：

```console
Array(4) [ 1, 2, 3, 4 ]
```

第四個數組值會被推送至所傳回的陣列 (`data.push(4);`) `ReturnArrayAsync` 。

根據預設，方法識別碼是方法名稱，但您可以使用屬性函式來指定不同的識別碼 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) ：

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

在用戶端 JavaScript 檔案中：

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

## <a name="instance-method-call"></a>實例方法呼叫

您也可以從 JavaScript 呼叫 .NET 實例方法。 從 JavaScript 叫用 .NET 實例方法：

* 以傳址方式將 .NET 實例傳遞至 JavaScript：
  * 對進行靜態呼叫 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> 。
  * 將實例包裝在實例中 <xref:Microsoft.JSInterop.DotNetObjectReference> ，並 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> 在實例上呼叫 <xref:Microsoft.JSInterop.DotNetObjectReference> 。 <xref:Microsoft.JSInterop.DotNetObjectReference> (範例中的物件處置會在本節稍後的) 。
* 使用或函數，在實例上叫用 .NET 實例方法 `invokeMethod` `invokeMethodAsync` 。 從 JavaScript 叫用其他 .NET 方法時，也可以將 .NET 實例當作引數傳遞。

> [!NOTE]
> 範例應用程式會將訊息記錄至用戶端主控台。 如需範例應用程式所示範的下列範例，請在瀏覽器的開發人員工具中檢查瀏覽器的主控台輸出。

當 **`Trigger .NET instance method HelloHelper.SayHello`** 選取按鈕時， `ExampleJsInterop.CallHelloHelperSayHello` 會呼叫，並將名稱傳遞 `Blazor` 給方法。

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello``sayHello`使用的新實例叫用 JavaScript 函數 `HelloHelper` 。

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

名稱會傳遞至的函式 `HelloHelper` ，以設定 `HelloHelper.Name` 屬性。 執行 JavaScript 函式時 `sayHello` ， `HelloHelper.SayHello` 會傳回 `Hello, {Name}!` 訊息，javascript 函式會將此訊息寫入主控台。

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

瀏覽器的 網頁程式開發人員工具中的主控台輸出：

```console
Hello, Blazor!
```

若要避免記憶體流失，並允許在建立的元件上進行垃圾收集 <xref:Microsoft.JSInterop.DotNetObjectReference> ，請採用下列其中一種方法：

* 處置建立實例之類別中的物件 <xref:Microsoft.JSInterop.DotNetObjectReference> ：

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  類別中所顯示的上述模式 `ExampleJsInterop` 也可以在元件中執行：

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

* 當元件或類別未處置時 <xref:Microsoft.JSInterop.DotNetObjectReference> ，請呼叫下列方法，在用戶端上處置物件 `.dispose()` ：

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>元件實例方法呼叫

若要叫用元件的 .NET 方法：

* 使用或函式 `invokeMethod` `invokeMethodAsync` ，對元件進行靜態方法呼叫。
* 元件的靜態方法會將其實例方法的呼叫包裝為叫用的 <xref:System.Action> 。

> [!NOTE]
> 針對 Blazor Server 有多個使用者可能同時使用相同元件的應用程式，請使用 helper 類別來叫用實例方法。
>
> 如需詳細資訊，請參閱 [元件實例方法 helper 類別](#component-instance-method-helper-class) 一節。

在用戶端 JavaScript 中：

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

`Pages/JSInteropComponent.razor`:

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

若要將引數傳遞至實例方法：

* 將參數加入至 JS 方法調用。 在下列範例中，會將名稱傳遞給方法。 您可以視需要將其他參數新增至清單。

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

* 為參數提供正確的類型 <xref:System.Action> 。 將參數清單提供給 c # 方法。 <xref:System.Action> `UpdateMessage` 使用 () 的參數叫用 () `action.Invoke(name)` 。

  `Pages/JSInteropComponent.razor`:

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  `message`選取 [**呼叫 JS 方法**] 按鈕時的輸出：

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>元件實例方法 helper 類別

Helper 類別是用來叫用實例方法做為 <xref:System.Action> 。 Helper 類別在下列情況很有用：

* 相同類型的數個元件會在相同的頁面上轉譯。
* Blazor Server使用應用程式，而多個使用者可能同時使用元件。

在下例中︰

* `JSInteropExample`元件包含數個 `ListItem` 元件。
* 每個 `ListItem` 元件都是由訊息和按鈕所組成。
* `ListItem`選取 [元件] 按鈕時，該 `ListItem` `UpdateMessage` 方法會變更清單專案文字並隱藏按鈕。

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

在用戶端 JavaScript 中：

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。

`Shared/ListItem.razor`:

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

`Pages/JSInteropExample.razor`:

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>避免迴圈物件參考

包含迴圈參考的物件無法在用戶端上針對下列任一項進行序列化：

* .NET 方法呼叫。
* 當傳回型別有迴圈參考時，來自 c # 的 JavaScript 方法呼叫。

如需詳細資訊，請參閱下列問題：

* [不支援迴圈參考，請採用兩個 (dotnet/aspnetcore #20525) ](https://github.com/dotnet/aspnetcore/issues/20525)
* [提案：在將 (dotnet/執行時間 #30820 序列化時，新增處理迴圈參考的機制) ](https://github.com/dotnet/runtime/issues/30820)

## <a name="js-modules"></a>JS 模組

針對 JS 隔離，JS interop 適用于瀏覽器的 [EcmaScript 模組預設支援 (ESM) ](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ecmascript 規格](https://tc39.es/ecma262/#sec-modules)) 。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/call-javascript-from-dotnet>
* [`InteropComponent.razor` 範例 (dotnet/AspNetCore GitHub 存放庫，3.1 版本分支) ](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
