---
title: 從 ASP.NET Core 中的 JavaScript 函式呼叫 .NET 方法 Blazor
author: guardrex
description: 瞭解如何從應用程式中的 JavaScript 函式叫用 .NET 方法 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 3df0fafe85d6decac3be41d4e25a4db51d8d72d8
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627048"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="7feb3-103">從 ASP.NET Core 中的 JavaScript 函式呼叫 .NET 方法 Blazor</span><span class="sxs-lookup"><span data-stu-id="7feb3-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="7feb3-104">[Javier Calvarro Nelson](https://github.com/javiercn)、 [Daniel Roth](https://github.com/danroth27)、 [Shashikant Rudrawadi](http://wisne.co)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7feb3-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7feb3-105">Blazor應用程式可以從 javascript 函式的 .net 方法和 .net 方法中叫用 javascript 函式。</span><span class="sxs-lookup"><span data-stu-id="7feb3-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="7feb3-106">這些案例稱為 *JavaScript 互通性* (*JS interop*) 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="7feb3-107">本文涵蓋從 JavaScript 叫用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="7feb3-108">如需如何從 .NET 呼叫 JavaScript 函數的詳細資訊，請參閱 <xref:blazor/call-javascript-from-dotnet> 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="7feb3-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="7feb3-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="7feb3-110">靜態 .NET 方法呼叫</span><span class="sxs-lookup"><span data-stu-id="7feb3-110">Static .NET method call</span></span>

<span data-ttu-id="7feb3-111">若要從 JavaScript 叫用靜態 .NET 方法，請使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函數。</span><span class="sxs-lookup"><span data-stu-id="7feb3-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="7feb3-112">傳入您想要呼叫之靜態方法的識別碼、包含函數的元件名稱，以及任何引數。</span><span class="sxs-lookup"><span data-stu-id="7feb3-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="7feb3-113">非同步版本是支援案例的首選 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="7feb3-114">.NET 方法必須是公用、靜態，且具有 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="7feb3-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="7feb3-115">目前不支援呼叫開放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="7feb3-116">範例應用程式包含可傳回陣列的 c # 方法 `int` 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="7feb3-117">[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)屬性會套用至方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="7feb3-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="7feb3-119">提供給用戶端的 JavaScript 會叫用 c # .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="7feb3-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="7feb3-121">當您 **`Trigger .NET static method ReturnArrayAsync`** 選取此按鈕時，請檢查瀏覽器的 網頁程式開發人員工具中的主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="7feb3-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="7feb3-122">主控台輸出為：</span><span class="sxs-lookup"><span data-stu-id="7feb3-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="7feb3-123">第四個數組值會被推送至所傳回的陣列 (`data.push(4);`) `ReturnArrayAsync` 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="7feb3-124">根據預設，方法識別碼是方法名稱，但您可以使用屬性函式來指定不同的識別碼 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) ：</span><span class="sxs-lookup"><span data-stu-id="7feb3-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="7feb3-125">在用戶端 JavaScript 檔案中：</span><span class="sxs-lookup"><span data-stu-id="7feb3-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="7feb3-126">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="7feb3-127">實例方法呼叫</span><span class="sxs-lookup"><span data-stu-id="7feb3-127">Instance method call</span></span>

<span data-ttu-id="7feb3-128">您也可以從 JavaScript 呼叫 .NET 實例方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="7feb3-129">從 JavaScript 叫用 .NET 實例方法：</span><span class="sxs-lookup"><span data-stu-id="7feb3-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="7feb3-130">以傳址方式將 .NET 實例傳遞至 JavaScript：</span><span class="sxs-lookup"><span data-stu-id="7feb3-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="7feb3-131">對進行靜態呼叫 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="7feb3-132">將實例包裝在實例中 <xref:Microsoft.JSInterop.DotNetObjectReference> ，並 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> 在實例上呼叫 <xref:Microsoft.JSInterop.DotNetObjectReference> 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="7feb3-133"><xref:Microsoft.JSInterop.DotNetObjectReference> (範例中的物件處置會在本節稍後的) 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="7feb3-134">使用或函數，在實例上叫用 .NET 實例方法 `invokeMethod` `invokeMethodAsync` 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="7feb3-135">從 JavaScript 叫用其他 .NET 方法時，也可以將 .NET 實例當作引數傳遞。</span><span class="sxs-lookup"><span data-stu-id="7feb3-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="7feb3-136">範例應用程式會將訊息記錄至用戶端主控台。</span><span class="sxs-lookup"><span data-stu-id="7feb3-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="7feb3-137">如需範例應用程式所示範的下列範例，請在瀏覽器的開發人員工具中檢查瀏覽器的主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="7feb3-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="7feb3-138">當 **`Trigger .NET instance method HelloHelper.SayHello`** 選取按鈕時， `ExampleJsInterop.CallHelloHelperSayHello` 會呼叫，並將名稱傳遞 `Blazor` 給方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="7feb3-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="7feb3-140">`CallHelloHelperSayHello``sayHello`使用的新實例叫用 JavaScript 函數 `HelloHelper` 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="7feb3-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="7feb3-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="7feb3-143">名稱會傳遞至的函式 `HelloHelper` ，以設定 `HelloHelper.Name` 屬性。</span><span class="sxs-lookup"><span data-stu-id="7feb3-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="7feb3-144">執行 JavaScript 函式時 `sayHello` ， `HelloHelper.SayHello` 會傳回 `Hello, {Name}!` 訊息，javascript 函式會將此訊息寫入主控台。</span><span class="sxs-lookup"><span data-stu-id="7feb3-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="7feb3-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="7feb3-146">瀏覽器的 網頁程式開發人員工具中的主控台輸出：</span><span class="sxs-lookup"><span data-stu-id="7feb3-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="7feb3-147">若要避免記憶體流失，並允許在建立的元件上進行垃圾收集 <xref:Microsoft.JSInterop.DotNetObjectReference> ，請採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="7feb3-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="7feb3-148">處置建立實例之類別中的物件 <xref:Microsoft.JSInterop.DotNetObjectReference> ：</span><span class="sxs-lookup"><span data-stu-id="7feb3-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="7feb3-149">類別中所顯示的上述模式 `ExampleJsInterop` 也可以在元件中執行：</span><span class="sxs-lookup"><span data-stu-id="7feb3-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="7feb3-150">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="7feb3-151">當元件或類別未處置時 <xref:Microsoft.JSInterop.DotNetObjectReference> ，請呼叫下列方法，在用戶端上處置物件 `.dispose()` ：</span><span class="sxs-lookup"><span data-stu-id="7feb3-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="7feb3-152">元件實例方法呼叫</span><span class="sxs-lookup"><span data-stu-id="7feb3-152">Component instance method call</span></span>

<span data-ttu-id="7feb3-153">若要叫用元件的 .NET 方法：</span><span class="sxs-lookup"><span data-stu-id="7feb3-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="7feb3-154">使用或函式 `invokeMethod` `invokeMethodAsync` ，對元件進行靜態方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="7feb3-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="7feb3-155">元件的靜態方法會將其實例方法的呼叫包裝為叫用的 <xref:System.Action> 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="7feb3-156">針對 Blazor Server 有多個使用者可能同時使用相同元件的應用程式，請使用 helper 類別來叫用實例方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-156">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="7feb3-157">如需詳細資訊，請參閱 [元件實例方法 helper 類別](#component-instance-method-helper-class) 一節。</span><span class="sxs-lookup"><span data-stu-id="7feb3-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="7feb3-158">在用戶端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="7feb3-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="7feb3-159">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="7feb3-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-160">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="7feb3-161">若要將引數傳遞至實例方法：</span><span class="sxs-lookup"><span data-stu-id="7feb3-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="7feb3-162">將參數加入至 JS 方法調用。</span><span class="sxs-lookup"><span data-stu-id="7feb3-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="7feb3-163">在下列範例中，會將名稱傳遞給方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="7feb3-164">您可以視需要將其他參數新增至清單。</span><span class="sxs-lookup"><span data-stu-id="7feb3-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="7feb3-165">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="7feb3-166">為參數提供正確的類型 <xref:System.Action> 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="7feb3-167">將參數清單提供給 c # 方法。</span><span class="sxs-lookup"><span data-stu-id="7feb3-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="7feb3-168"><xref:System.Action> `UpdateMessage` 使用 () 的參數叫用 () `action.Invoke(name)` 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="7feb3-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-169">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="7feb3-170">`message`選取 [**呼叫 JS 方法**] 按鈕時的輸出：</span><span class="sxs-lookup"><span data-stu-id="7feb3-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="7feb3-171">元件實例方法 helper 類別</span><span class="sxs-lookup"><span data-stu-id="7feb3-171">Component instance method helper class</span></span>

<span data-ttu-id="7feb3-172">Helper 類別是用來叫用實例方法做為 <xref:System.Action> 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="7feb3-173">Helper 類別在下列情況很有用：</span><span class="sxs-lookup"><span data-stu-id="7feb3-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="7feb3-174">相同類型的數個元件會在相同的頁面上轉譯。</span><span class="sxs-lookup"><span data-stu-id="7feb3-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="7feb3-175">Blazor Server使用應用程式，而多個使用者可能同時使用元件。</span><span class="sxs-lookup"><span data-stu-id="7feb3-175">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="7feb3-176">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="7feb3-176">In the following example:</span></span>

* <span data-ttu-id="7feb3-177">`JSInteropExample`元件包含數個 `ListItem` 元件。</span><span class="sxs-lookup"><span data-stu-id="7feb3-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="7feb3-178">每個 `ListItem` 元件都是由訊息和按鈕所組成。</span><span class="sxs-lookup"><span data-stu-id="7feb3-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="7feb3-179">`ListItem`選取 [元件] 按鈕時，該 `ListItem` `UpdateMessage` 方法會變更清單專案文字並隱藏按鈕。</span><span class="sxs-lookup"><span data-stu-id="7feb3-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="7feb3-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-180">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="7feb3-181">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="7feb3-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="7feb3-182">在用戶端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="7feb3-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="7feb3-183">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-183">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JsRuntime

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
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
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

<span data-ttu-id="7feb3-184">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="7feb3-184">`Pages/JSInteropExample.razor`:</span></span>

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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="7feb3-185">避免迴圈物件參考</span><span class="sxs-lookup"><span data-stu-id="7feb3-185">Avoid circular object references</span></span>

<span data-ttu-id="7feb3-186">包含迴圈參考的物件無法在用戶端上針對下列任一項進行序列化：</span><span class="sxs-lookup"><span data-stu-id="7feb3-186">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="7feb3-187">.NET 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="7feb3-187">.NET method calls.</span></span>
* <span data-ttu-id="7feb3-188">當傳回型別有迴圈參考時，來自 c # 的 JavaScript 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="7feb3-188">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="7feb3-189">如需詳細資訊，請參閱下列問題：</span><span class="sxs-lookup"><span data-stu-id="7feb3-189">For more information, see the following issues:</span></span>

* [<span data-ttu-id="7feb3-190">不支援迴圈參考，請採用兩個 (dotnet/aspnetcore #20525) </span><span class="sxs-lookup"><span data-stu-id="7feb3-190">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="7feb3-191">提案：在將 (dotnet/執行時間 #30820 序列化時，新增處理迴圈參考的機制) </span><span class="sxs-lookup"><span data-stu-id="7feb3-191">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="7feb3-192">其他資源</span><span class="sxs-lookup"><span data-stu-id="7feb3-192">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="7feb3-193">`InteropComponent.razor` 範例 (dotnet/AspNetCore GitHub 存放庫，3.1 版本分支) </span><span class="sxs-lookup"><span data-stu-id="7feb3-193">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="7feb3-194">在應用程式中執行大量資料傳輸 Blazor Server</span><span class="sxs-lookup"><span data-stu-id="7feb3-194">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
