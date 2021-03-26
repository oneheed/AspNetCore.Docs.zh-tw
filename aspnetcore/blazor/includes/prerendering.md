---
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
ms.openlocfilehash: f28a272c2e55e1fd6620ed4a23ef8cadcfad1ce9
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105554705"
---
<span data-ttu-id="1eb6a-101">*本節適用于已 Blazor Server Blazor WebAssembly 構成元件的託管應用程式 Razor 。*</span><span class="sxs-lookup"><span data-stu-id="1eb6a-101">*This section applies to Blazor Server and hosted Blazor WebAssembly apps that prerender Razor components.*</span></span>

<span data-ttu-id="1eb6a-102">當應用程式進行預載入時，無法執行某些動作，例如呼叫 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-102">While an app is prerendering, certain actions, such as calling into JavaScript, aren't possible.</span></span> <span data-ttu-id="1eb6a-103">元件在資源清單時可能需要以不同的方式呈現。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-103">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="1eb6a-104">若要延遲 JavaScript interop 呼叫，直到這類呼叫保證可以運作的一點，請覆寫[ `OnAfterRender{Async}` 生命週期事件](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-104">To delay JavaScript interop calls until a point where such calls are guaranteed to work, override the [`OnAfterRender{Async}` lifecycle event](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync).</span></span> <span data-ttu-id="1eb6a-105">只有在應用程式完全呈現之後，才會呼叫此事件。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-105">This event is only called after the app is fully rendered.</span></span>

<span data-ttu-id="1eb6a-106">`Pages/PrerenderedInterop1.razor`:</span><span class="sxs-lookup"><span data-stu-id="1eb6a-106">`Pages/PrerenderedInterop1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop1.razor)]

::: moniker-end

<span data-ttu-id="1eb6a-107">針對上述的範例程式碼，請在 `setElementText1` `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` () 的元素內提供 JavaScript 函式 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-107">For the preceding example code, provide a `setElementText1` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="1eb6a-108">呼叫函式時，會傳回 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ，而且不會傳回值：</span><span class="sxs-lookup"><span data-stu-id="1eb6a-108">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText1 = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="1eb6a-109">**上述範例僅針對示範目的，直接修改檔物件模型 (DOM) 。**</span><span class="sxs-lookup"><span data-stu-id="1eb6a-109">**The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.**</span></span> <span data-ttu-id="1eb6a-110">在大部分的情況下，不建議直接修改具有 JavaScript 的 DOM，因為 JavaScript 可能會干擾 Blazor 變更追蹤。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-110">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

> [!NOTE]
> <span data-ttu-id="1eb6a-111">上述範例會干擾具有全域方法的用戶端。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-111">The preceding example pollutes the client with global methods.</span></span> <span data-ttu-id="1eb6a-112">若要在生產應用程式中提供更好的方法，請參閱[ Blazor JavaScript 隔離和物件參考](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-112">For a better approach in production apps, see [Blazor JavaScript isolation and object references](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references).</span></span>
>
> <span data-ttu-id="1eb6a-113">範例：</span><span class="sxs-lookup"><span data-stu-id="1eb6a-113">Example:</span></span>
>
> ```javascript
> export setElementText1 = (element, text) => element.innerText = text;
> ```

<span data-ttu-id="1eb6a-114">下列元件示範如何使用 JavaScript interop 做為元件初始化邏輯的一部分，而這種方式與可呈現的方式相容。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-114">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="1eb6a-115">元件顯示可以從內部觸發轉譯更新 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-115">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="1eb6a-116">開發人員必須小心避免在此案例中建立無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-116">The developer must be careful to avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="1eb6a-117">在 <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 中呼叫的位置， `ElementRef` 只會用於中， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 而不會用於任何較早的生命週期方法，因為在轉譯元件之後，才會有 JavaScript 元素。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-117">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="1eb6a-118">[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) 呼叫以 rerender 具有從 JavaScript interop 呼叫取得之新狀態的元件 (如需詳細資訊，請參閱 <xref:blazor/components/rendering>) 。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-118">[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) is called to rerender the component with the new state obtained from the JavaScript interop call (for more information, see <xref:blazor/components/rendering>).</span></span> <span data-ttu-id="1eb6a-119">程式碼不會建立無限迴圈，因為 `StateHasChanged` 只有在是時才會呼叫 `infoFromJs` `null` 。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-119">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

<span data-ttu-id="1eb6a-120">`Pages/PrerenderedInterop2.razor`:</span><span class="sxs-lookup"><span data-stu-id="1eb6a-120">`Pages/PrerenderedInterop2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop2.razor)]

::: moniker-end

<span data-ttu-id="1eb6a-121">針對上述的範例程式碼，請在 `setElementText2` `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` () 的元素內提供 JavaScript 函式 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-121">For the preceding example code, provide a `setElementText2` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="1eb6a-122">呼叫函式時，會傳回 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 值：</span><span class="sxs-lookup"><span data-stu-id="1eb6a-122">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText2 = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="1eb6a-123">**上述範例僅針對示範目的，直接修改檔物件模型 (DOM) 。**</span><span class="sxs-lookup"><span data-stu-id="1eb6a-123">**The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.**</span></span> <span data-ttu-id="1eb6a-124">在大部分的情況下，不建議直接修改具有 JavaScript 的 DOM，因為 JavaScript 可能會干擾 Blazor 變更追蹤。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-124">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

> [!NOTE]
> <span data-ttu-id="1eb6a-125">上述範例會干擾具有全域方法的用戶端。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-125">The preceding example pollutes the client with global methods.</span></span> <span data-ttu-id="1eb6a-126">若要在生產應用程式中提供更好的方法，請參閱[ Blazor JavaScript 隔離和物件參考](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。</span><span class="sxs-lookup"><span data-stu-id="1eb6a-126">For a better approach in production apps, see [Blazor JavaScript isolation and object references](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references).</span></span>
>
> <span data-ttu-id="1eb6a-127">範例：</span><span class="sxs-lookup"><span data-stu-id="1eb6a-127">Example:</span></span>
>
> ```javascript
> export setElementText2 = (element, text) => {
>   element.innerText = text;
>   return text;
> };
> ```
