<span data-ttu-id="11185-101">雖然 Blazor 伺服器應用程式是預先處理的，但無法執行某些動作（例如呼叫 JavaScript），因為尚未建立與瀏覽器的連接。</span><span class="sxs-lookup"><span data-stu-id="11185-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="11185-102">元件在資源清單時可能需要以不同的方式呈現。</span><span class="sxs-lookup"><span data-stu-id="11185-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="11185-103">您可以使用 [OnAfterRenderAsync 元件生命週期事件](xref:blazor/components/lifecycle#after-component-render)，來延遲 JavaScript interop 呼叫，直到建立與瀏覽器的連線為止。</span><span class="sxs-lookup"><span data-stu-id="11185-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="11185-104">只有在完全轉譯應用程式並建立用戶端連接之後，才會呼叫此事件。</span><span class="sxs-lookup"><span data-stu-id="11185-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="11185-105">針對上述的範例程式碼，請在 `setElementText` `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的元素內提供 JavaScript 函式。</span><span class="sxs-lookup"><span data-stu-id="11185-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="11185-106">呼叫函式時，會傳回 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ，而且不會傳回值：</span><span class="sxs-lookup"><span data-stu-id="11185-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="11185-107">上述範例僅針對示範目的，直接修改檔物件模型 (DOM) 。</span><span class="sxs-lookup"><span data-stu-id="11185-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="11185-108">在大部分的情況下，不建議直接修改具有 JavaScript 的 DOM，因為 JavaScript 可能會干擾 Blazor 的變更追蹤。</span><span class="sxs-lookup"><span data-stu-id="11185-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="11185-109">下列元件示範如何使用 JavaScript interop 做為元件初始化邏輯的一部分，而這種方式與可呈現的方式相容。</span><span class="sxs-lookup"><span data-stu-id="11185-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="11185-110">元件顯示可以從內部觸發轉譯更新 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="11185-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="11185-111">開發人員必須避免在此案例中建立無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="11185-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="11185-112">在 <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 中呼叫的位置， `ElementRef` 只會用於中， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 而不會用於任何較早的生命週期方法，因為在轉譯元件之後，才會有 JavaScript 元素。</span><span class="sxs-lookup"><span data-stu-id="11185-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="11185-113">呼叫[StateHasChanged](xref:blazor/components/lifecycle#state-changes)時，會使用從 JavaScript interop 呼叫取得的新狀態來 rerender 元件。</span><span class="sxs-lookup"><span data-stu-id="11185-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="11185-114">程式碼不會建立無限迴圈，因為 `StateHasChanged` 只有在是時才會呼叫 `infoFromJs` `null` 。</span><span class="sxs-lookup"><span data-stu-id="11185-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="11185-115">針對上述的範例程式碼，請在 `setElementText` `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` (Blazor Server) 的元素內提供 JavaScript 函式。</span><span class="sxs-lookup"><span data-stu-id="11185-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="11185-116">呼叫函式時，會傳回 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 值：</span><span class="sxs-lookup"><span data-stu-id="11185-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="11185-117">上述範例僅針對示範目的，直接修改檔物件模型 (DOM) 。</span><span class="sxs-lookup"><span data-stu-id="11185-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="11185-118">在大部分的情況下，不建議直接修改具有 JavaScript 的 DOM，因為 JavaScript 可能會干擾 Blazor 的變更追蹤。</span><span class="sxs-lookup"><span data-stu-id="11185-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
