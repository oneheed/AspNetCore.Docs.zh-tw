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
ms.openlocfilehash: bca4035d625d5b6e51f2e51c194713014ccd5e90
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586536"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="02472-103">從 ASP.NET Core 中的 JavaScript 函式呼叫 .NET 方法 Blazor</span><span class="sxs-lookup"><span data-stu-id="02472-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="02472-104">Blazor應用程式可以從 javascript 函式的 .net 方法和 .net 方法中叫用 javascript 函式。</span><span class="sxs-lookup"><span data-stu-id="02472-104">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="02472-105">這些案例稱為 *JavaScript 互通性* (*JS interop*) 。</span><span class="sxs-lookup"><span data-stu-id="02472-105">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="02472-106">本文涵蓋從 JavaScript 叫用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="02472-106">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="02472-107">如需如何從 .NET 呼叫 JavaScript 函數的詳細資訊，請參閱 <xref:blazor/call-javascript-from-dotnet> 。</span><span class="sxs-lookup"><span data-stu-id="02472-107">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="02472-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="02472-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

> [!NOTE]
> <span data-ttu-id="02472-109">在檔案 `<script>` `</body>` `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` () 中的結束記號之前，將 JS 檔案新增 (標記) Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="02472-109">Add JS files (`<script>` tags) before the closing `</body>` tag in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span> <span data-ttu-id="02472-110">確定具有 JS interop 方法的 JS 檔案包含在 Blazor FRAMEWORK JS 檔案之前。</span><span class="sxs-lookup"><span data-stu-id="02472-110">Ensure that JS files with JS interop methods are included before Blazor framework JS files.</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="02472-111">靜態 .NET 方法呼叫</span><span class="sxs-lookup"><span data-stu-id="02472-111">Static .NET method call</span></span>

<span data-ttu-id="02472-112">若要從 JavaScript 叫用靜態 .NET 方法，請使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函數。</span><span class="sxs-lookup"><span data-stu-id="02472-112">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="02472-113">傳入您想要呼叫之靜態方法的識別碼、包含函數的元件名稱，以及任何引數。</span><span class="sxs-lookup"><span data-stu-id="02472-113">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="02472-114">非同步版本是支援案例的首選 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="02472-114">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="02472-115">.NET 方法必須是公用、靜態，且具有[ `[JSInvokable]` 屬性](xref:Microsoft.JSInterop.JSInvokableAttribute)。</span><span class="sxs-lookup"><span data-stu-id="02472-115">The .NET method must be public, static, and have the [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute).</span></span> <span data-ttu-id="02472-116">目前不支援呼叫開放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="02472-116">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="02472-117">範例應用程式包含可傳回陣列的 c # 方法 `int` 。</span><span class="sxs-lookup"><span data-stu-id="02472-117">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="02472-118">[ `[JSInvokable]` 屬性](xref:Microsoft.JSInterop.JSInvokableAttribute)會套用至方法。</span><span class="sxs-lookup"><span data-stu-id="02472-118">The [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute) is applied to the method.</span></span>

<span data-ttu-id="02472-119">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="02472-119">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="02472-120">提供給用戶端的 JavaScript 會叫用 c # .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="02472-120">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="02472-121">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="02472-121">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="02472-122">當您 **`Trigger .NET static method ReturnArrayAsync`** 選取此按鈕時，請檢查瀏覽器的 網頁程式開發人員工具中的主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="02472-122">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="02472-123">主控台輸出為：</span><span class="sxs-lookup"><span data-stu-id="02472-123">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="02472-124">第四個數組值會被推送至所傳回的陣列 (`data.push(4);`) `ReturnArrayAsync` 。</span><span class="sxs-lookup"><span data-stu-id="02472-124">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="02472-125">根據預設，方法識別碼是方法名稱，但您可以使用[ `[JSInvokable]` 屬性](xref:Microsoft.JSInterop.JSInvokableAttribute)函式來指定不同的識別碼：</span><span class="sxs-lookup"><span data-stu-id="02472-125">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]` attribute](xref:Microsoft.JSInterop.JSInvokableAttribute) constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="02472-126">在用戶端 JavaScript 檔案中：</span><span class="sxs-lookup"><span data-stu-id="02472-126">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="02472-127">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="02472-127">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="02472-128">實例方法呼叫</span><span class="sxs-lookup"><span data-stu-id="02472-128">Instance method call</span></span>

<span data-ttu-id="02472-129">您也可以從 JavaScript 呼叫 .NET 實例方法。</span><span class="sxs-lookup"><span data-stu-id="02472-129">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="02472-130">從 JavaScript 叫用 .NET 實例方法：</span><span class="sxs-lookup"><span data-stu-id="02472-130">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="02472-131">以傳址方式將 .NET 實例傳遞至 JavaScript：</span><span class="sxs-lookup"><span data-stu-id="02472-131">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="02472-132">對進行靜態呼叫 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="02472-132">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="02472-133">將實例包裝在實例中 <xref:Microsoft.JSInterop.DotNetObjectReference> ，並 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> 在實例上呼叫 <xref:Microsoft.JSInterop.DotNetObjectReference> 。</span><span class="sxs-lookup"><span data-stu-id="02472-133">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="02472-134"><xref:Microsoft.JSInterop.DotNetObjectReference> (範例中的物件處置會在本節稍後的) 。</span><span class="sxs-lookup"><span data-stu-id="02472-134">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="02472-135">使用或函數，在實例上叫用 .NET 實例方法 `invokeMethod` `invokeMethodAsync` 。</span><span class="sxs-lookup"><span data-stu-id="02472-135">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="02472-136">從 JavaScript 叫用其他 .NET 方法時，也可以將 .NET 實例當作引數傳遞。</span><span class="sxs-lookup"><span data-stu-id="02472-136">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="02472-137">範例應用程式會將訊息記錄至用戶端主控台。</span><span class="sxs-lookup"><span data-stu-id="02472-137">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="02472-138">如需範例應用程式所示範的下列範例，請在瀏覽器的開發人員工具中檢查瀏覽器的主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="02472-138">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="02472-139">當 **`Trigger .NET instance method HelloHelper.SayHello`** 選取按鈕時， `ExampleJsInterop.CallHelloHelperSayHello` 會呼叫，並將名稱傳遞 `Blazor` 給方法。</span><span class="sxs-lookup"><span data-stu-id="02472-139">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="02472-140">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="02472-140">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="02472-141">`CallHelloHelperSayHello``sayHello`使用的新實例叫用 JavaScript 函數 `HelloHelper` 。</span><span class="sxs-lookup"><span data-stu-id="02472-141">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="02472-142">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="02472-142">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="02472-143">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="02472-143">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="02472-144">名稱會傳遞至的函式 `HelloHelper` ，以設定 `HelloHelper.Name` 屬性。</span><span class="sxs-lookup"><span data-stu-id="02472-144">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="02472-145">執行 JavaScript 函式時 `sayHello` ， `HelloHelper.SayHello` 會傳回 `Hello, {Name}!` 訊息，javascript 函式會將此訊息寫入主控台。</span><span class="sxs-lookup"><span data-stu-id="02472-145">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="02472-146">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="02472-146">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="02472-147">瀏覽器的 網頁程式開發人員工具中的主控台輸出：</span><span class="sxs-lookup"><span data-stu-id="02472-147">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="02472-148">若要避免記憶體流失，並允許在建立的元件上進行垃圾收集 <xref:Microsoft.JSInterop.DotNetObjectReference> ，請採用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="02472-148">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="02472-149">處置建立實例之類別中的物件 <xref:Microsoft.JSInterop.DotNetObjectReference> ：</span><span class="sxs-lookup"><span data-stu-id="02472-149">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

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

  <span data-ttu-id="02472-150">類別中所顯示的上述模式 `ExampleJsInterop` 也可以在元件中執行：</span><span class="sxs-lookup"><span data-stu-id="02472-150">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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
  
  <span data-ttu-id="02472-151">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="02472-151">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="02472-152">當元件或類別未處置時 <xref:Microsoft.JSInterop.DotNetObjectReference> ，請呼叫下列方法，在用戶端上處置物件 `.dispose()` ：</span><span class="sxs-lookup"><span data-stu-id="02472-152">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="02472-153">元件實例方法呼叫</span><span class="sxs-lookup"><span data-stu-id="02472-153">Component instance method call</span></span>

<span data-ttu-id="02472-154">若要叫用元件的 .NET 方法：</span><span class="sxs-lookup"><span data-stu-id="02472-154">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="02472-155">使用或函式 `invokeMethod` `invokeMethodAsync` ，對元件進行靜態方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="02472-155">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="02472-156">元件的靜態方法會將其實例方法的呼叫包裝為叫用的 <xref:System.Action> 。</span><span class="sxs-lookup"><span data-stu-id="02472-156">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="02472-157">針對 Blazor Server 有多個使用者可能同時使用相同元件的應用程式，請使用 helper 類別來叫用實例方法。</span><span class="sxs-lookup"><span data-stu-id="02472-157">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="02472-158">如需詳細資訊，請參閱 [元件實例方法 helper 類別](#component-instance-method-helper-class) 一節。</span><span class="sxs-lookup"><span data-stu-id="02472-158">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="02472-159">在用戶端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="02472-159">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="02472-160">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="02472-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="02472-161">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="02472-161">`Pages/JSInteropComponent.razor`:</span></span>

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

<span data-ttu-id="02472-162">若要將引數傳遞至實例方法：</span><span class="sxs-lookup"><span data-stu-id="02472-162">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="02472-163">將參數加入至 JS 方法調用。</span><span class="sxs-lookup"><span data-stu-id="02472-163">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="02472-164">在下列範例中，會將名稱傳遞給方法。</span><span class="sxs-lookup"><span data-stu-id="02472-164">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="02472-165">您可以視需要將其他參數新增至清單。</span><span class="sxs-lookup"><span data-stu-id="02472-165">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="02472-166">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="02472-166">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="02472-167">為參數提供正確的類型 <xref:System.Action> 。</span><span class="sxs-lookup"><span data-stu-id="02472-167">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="02472-168">將參數清單提供給 c # 方法。</span><span class="sxs-lookup"><span data-stu-id="02472-168">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="02472-169"><xref:System.Action> `UpdateMessage` 使用 () 的參數叫用 () `action.Invoke(name)` 。</span><span class="sxs-lookup"><span data-stu-id="02472-169">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="02472-170">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="02472-170">`Pages/JSInteropComponent.razor`:</span></span>

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

  <span data-ttu-id="02472-171">`message`選取 [**呼叫 JS 方法**] 按鈕時的輸出：</span><span class="sxs-lookup"><span data-stu-id="02472-171">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="02472-172">元件實例方法 helper 類別</span><span class="sxs-lookup"><span data-stu-id="02472-172">Component instance method helper class</span></span>

<span data-ttu-id="02472-173">Helper 類別是用來叫用實例方法做為 <xref:System.Action> 。</span><span class="sxs-lookup"><span data-stu-id="02472-173">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="02472-174">Helper 類別在下列情況很有用：</span><span class="sxs-lookup"><span data-stu-id="02472-174">Helper classes are useful when:</span></span>

* <span data-ttu-id="02472-175">相同類型的數個元件會在相同的頁面上轉譯。</span><span class="sxs-lookup"><span data-stu-id="02472-175">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="02472-176">Blazor Server使用應用程式，而多個使用者可能同時使用元件。</span><span class="sxs-lookup"><span data-stu-id="02472-176">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="02472-177">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="02472-177">In the following example:</span></span>

* <span data-ttu-id="02472-178">`JSInteropExample`元件包含數個 `ListItem` 元件。</span><span class="sxs-lookup"><span data-stu-id="02472-178">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="02472-179">每個 `ListItem` 元件都是由訊息和按鈕所組成。</span><span class="sxs-lookup"><span data-stu-id="02472-179">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="02472-180">`ListItem`選取 [元件] 按鈕時，該 `ListItem` `UpdateMessage` 方法會變更清單專案文字並隱藏按鈕。</span><span class="sxs-lookup"><span data-stu-id="02472-180">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="02472-181">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="02472-181">`MessageUpdateInvokeHelper.cs`:</span></span>

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

<span data-ttu-id="02472-182">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="02472-182">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="02472-183">在用戶端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="02472-183">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="02472-184">預留位置 `{APP ASSEMBLY}` 是應用程式的應用程式元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="02472-184">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="02472-185">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="02472-185">`Shared/ListItem.razor`:</span></span>

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

<span data-ttu-id="02472-186">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="02472-186">`Pages/JSInteropExample.razor`:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="02472-187">避免迴圈物件參考</span><span class="sxs-lookup"><span data-stu-id="02472-187">Avoid circular object references</span></span>

<span data-ttu-id="02472-188">包含迴圈參考的物件無法在用戶端上針對下列任一項進行序列化：</span><span class="sxs-lookup"><span data-stu-id="02472-188">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="02472-189">.NET 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="02472-189">.NET method calls.</span></span>
* <span data-ttu-id="02472-190">當傳回型別有迴圈參考時，來自 c # 的 JavaScript 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="02472-190">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="02472-191">如需詳細資訊，請參閱下列問題：</span><span class="sxs-lookup"><span data-stu-id="02472-191">For more information, see the following issues:</span></span>

* [<span data-ttu-id="02472-192">不支援迴圈參考，請採用兩個 (dotnet/aspnetcore #20525) </span><span class="sxs-lookup"><span data-stu-id="02472-192">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="02472-193">提案：在將 (dotnet/執行時間 #30820 序列化時，新增處理迴圈參考的機制) </span><span class="sxs-lookup"><span data-stu-id="02472-193">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="02472-194">JS interop 呼叫的大小限制</span><span class="sxs-lookup"><span data-stu-id="02472-194">Size limits on JS interop calls</span></span>

<span data-ttu-id="02472-195">在中 Blazor WebAssembly ，架構不會限制 JS interop 輸入和輸出的大小。</span><span class="sxs-lookup"><span data-stu-id="02472-195">In Blazor WebAssembly, the framework doesn't impose a limit on the size of JS interop inputs and outputs.</span></span>

<span data-ttu-id="02472-196">在中 Blazor Server ，JS interop 呼叫的大小受限於中樞方法所允許的最大傳入 SignalR 訊息大小，這會由 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (預設值： 32 KB) 來強制執行。</span><span class="sxs-lookup"><span data-stu-id="02472-196">In Blazor Server, JS interop calls are limited in size by the maximum incoming SignalR message size permitted for hub methods, which is enforced by <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> (default: 32 KB).</span></span> <span data-ttu-id="02472-197">JS 至 .NET 的 SignalR 訊息大於 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> 擲回錯誤。</span><span class="sxs-lookup"><span data-stu-id="02472-197">JS to .NET SignalR messages larger than <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="02472-198">此架構不會限制 SignalR 從中樞到用戶端的訊息大小限制。</span><span class="sxs-lookup"><span data-stu-id="02472-198">The framework doesn't impose a limit on the size of a SignalR message from the hub to a client.</span></span>

<span data-ttu-id="02472-199">當 SignalR 記錄未設定為 [ [Debug](xref:Microsoft.Extensions.Logging.LogLevel) ] 或 [ [追蹤](xref:Microsoft.Extensions.Logging.LogLevel)] 時，訊息大小錯誤只會出現在瀏覽器的開發人員工具主控台中：</span><span class="sxs-lookup"><span data-stu-id="02472-199">When SignalR logging isn't set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), a message size error only appears in the browser's developer tools console:</span></span>

> <span data-ttu-id="02472-200">錯誤：連接中斷，發生錯誤「錯誤：伺服器在關閉時傳回錯誤：連接已關閉，但發生錯誤。」。</span><span class="sxs-lookup"><span data-stu-id="02472-200">Error: Connection disconnected with error 'Error: Server returned an error on close: Connection closed with an error.'.</span></span>

<span data-ttu-id="02472-201">當[ SignalR 伺服器端記錄](xref:signalr/diagnostics#server-side-logging)設定為[Debug](xref:Microsoft.Extensions.Logging.LogLevel)或[Trace](xref:Microsoft.Extensions.Logging.LogLevel)時，伺服器端記錄會顯示 <xref:System.IO.InvalidDataException> 訊息大小錯誤的。</span><span class="sxs-lookup"><span data-stu-id="02472-201">When [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) is set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel), server-side logging surfaces an <xref:System.IO.InvalidDataException> for a message size error.</span></span>

<span data-ttu-id="02472-202">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="02472-202">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> <span data-ttu-id="02472-203">System.io.invaliddataexception：已超過32768B 的訊息大小上限。</span><span class="sxs-lookup"><span data-stu-id="02472-203">System.IO.InvalidDataException: The maximum message size of 32768B was exceeded.</span></span> <span data-ttu-id="02472-204">訊息大小可在 AddHubOptions 中設定。</span><span class="sxs-lookup"><span data-stu-id="02472-204">The message size can be configured in AddHubOptions.</span></span>

<span data-ttu-id="02472-205">在中設定，以增加限制 <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="02472-205">Increase the limit by setting <xref:Microsoft.AspNetCore.SignalR.HubOptions.MaximumReceiveMessageSize> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="02472-206">下列範例會將接收訊息大小上限設定為 64 KB (64 \* 1024) ：</span><span class="sxs-lookup"><span data-stu-id="02472-206">The following example sets the maximum receive message size to 64 KB (64 \* 1024):</span></span>

```csharp
services.AddServerSideBlazor()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024);
```

<span data-ttu-id="02472-207">增加 SignalR 內送訊息大小限制的代價是需要更多的伺服器資源，而且會公開伺服器以增加惡意使用者的風險。</span><span class="sxs-lookup"><span data-stu-id="02472-207">Increasing the SignalR incoming message size limit comes at the cost of requiring more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="02472-208">此外，以字串或位元組陣列的形式將大量內容讀取到記憶體中，也會導致與垃圾收集行程效能不佳的配置，進而造成額外的效能降低。</span><span class="sxs-lookup"><span data-stu-id="02472-208">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span>

<span data-ttu-id="02472-209">讀取大型承載的其中一個選項是以較小的區區塊轉送內容，並以的形式處理承載 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="02472-209">One option for reading large payloads is to send the content in smaller chunks and process the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="02472-210">這可以在讀取大型 JSON 承載時使用，或在 JavaScript 中以原始位元組的形式提供資料。</span><span class="sxs-lookup"><span data-stu-id="02472-210">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="02472-211">如需示範如何使用類似于元件的技術，在中傳送大型二進位裝載 Blazor Server 的範例 `InputFile` ，請參閱 [二進位提交範例應用程式](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit)。</span><span class="sxs-lookup"><span data-stu-id="02472-211">For an example that demonstrates sending large binary payloads in Blazor Server that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="02472-212">開發在 JavaScript 與之間傳輸大量資料的程式碼時，請考慮下列指導方針 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="02472-212">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="02472-213">將資料分割成較小的片段，並依序傳送資料區段，直到伺服器收到所有資料為止。</span><span class="sxs-lookup"><span data-stu-id="02472-213">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="02472-214">請勿在 JavaScript 和 c # 程式碼中配置大型物件。</span><span class="sxs-lookup"><span data-stu-id="02472-214">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="02472-215">傳送或接收資料時，請勿封鎖主要 UI 執行緒長時間。</span><span class="sxs-lookup"><span data-stu-id="02472-215">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="02472-216">釋放處理常式完成或取消時所耗用的記憶體。</span><span class="sxs-lookup"><span data-stu-id="02472-216">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="02472-217">基於安全性考慮，強制執行下列其他需求：</span><span class="sxs-lookup"><span data-stu-id="02472-217">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="02472-218">宣告可傳遞的檔案或資料大小上限。</span><span class="sxs-lookup"><span data-stu-id="02472-218">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="02472-219">宣告從用戶端到伺服器的最小上傳速率。</span><span class="sxs-lookup"><span data-stu-id="02472-219">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="02472-220">當伺服器收到資料之後，資料可以是：</span><span class="sxs-lookup"><span data-stu-id="02472-220">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="02472-221">暫時儲存在記憶體緩衝區中，直到收集所有區段為止。</span><span class="sxs-lookup"><span data-stu-id="02472-221">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="02472-222">立即使用。</span><span class="sxs-lookup"><span data-stu-id="02472-222">Consumed immediately.</span></span> <span data-ttu-id="02472-223">例如，資料可以立即儲存在資料庫中，或在收到每個區段時寫入磁片。</span><span class="sxs-lookup"><span data-stu-id="02472-223">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="js-modules"></a><span data-ttu-id="02472-224">JS 模組</span><span class="sxs-lookup"><span data-stu-id="02472-224">JS modules</span></span>

<span data-ttu-id="02472-225">針對 JS 隔離，JS interop 適用于瀏覽器的 [EcmaScript 模組預設支援 (ESM) ](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ecmascript 規格](https://tc39.es/ecma262/#sec-modules)) 。</span><span class="sxs-lookup"><span data-stu-id="02472-225">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02472-226">其他資源</span><span class="sxs-lookup"><span data-stu-id="02472-226">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <span data-ttu-id="02472-227">[ `InteropComponent.razor` 範例 (Dotnet/AspNetCore GitHub 存放庫 `main` 分支) ](https://github.com/dotnet/AspNetCore/blob/main/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)： `main` 分支代表下一版 ASP.NET Core 的產品單位目前開發。</span><span class="sxs-lookup"><span data-stu-id="02472-227">[`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository `main` branch)](https://github.com/dotnet/AspNetCore/blob/main/src/Components/test/testassets/BasicTestApp/InteropComponent.razor): The `main` branch represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="02472-228">若要選取不同版本的分支 (例如 `release/5.0`) ，請使用 [ **切換分支或標記** ] 下拉式清單來選取分支。</span><span class="sxs-lookup"><span data-stu-id="02472-228">To select the branch for a different release (for example, `release/5.0`), use the **Switch branches or tags** drop-down list to select the branch.</span></span>
