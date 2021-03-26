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
*本節適用于已 Blazor Server Blazor WebAssembly 構成元件的託管應用程式 Razor 。*

當應用程式進行預載入時，無法執行某些動作，例如呼叫 JavaScript。 元件在資源清單時可能需要以不同的方式呈現。

若要延遲 JavaScript interop 呼叫，直到這類呼叫保證可以運作的一點，請覆寫[ `OnAfterRender{Async}` 生命週期事件](xref:blazor/components/lifecycle#after-component-render-onafterrenderasync)。 只有在應用程式完全呈現之後，才會呼叫此事件。

`Pages/PrerenderedInterop1.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop1.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop1.razor)]

::: moniker-end

針對上述的範例程式碼，請在 `setElementText1` `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` () 的元素內提供 JavaScript 函式 Blazor Server 。 呼叫函式時，會傳回 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> ，而且不會傳回值：

```html
<script>
  window.setElementText1 = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> **上述範例僅針對示範目的，直接修改檔物件模型 (DOM) 。** 在大部分的情況下，不建議直接修改具有 JavaScript 的 DOM，因為 JavaScript 可能會干擾 Blazor 變更追蹤。

> [!NOTE]
> 上述範例會干擾具有全域方法的用戶端。 若要在生產應用程式中提供更好的方法，請參閱[ Blazor JavaScript 隔離和物件參考](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。
>
> 範例：
>
> ```javascript
> export setElementText1 = (element, text) => element.innerText = text;
> ```

下列元件示範如何使用 JavaScript interop 做為元件初始化邏輯的一部分，而這種方式與可呈現的方式相容。 元件顯示可以從內部觸發轉譯更新 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 。 開發人員必須小心避免在此案例中建立無限迴圈。

在 <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 中呼叫的位置， `ElementRef` 只會用於中， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 而不會用於任何較早的生命週期方法，因為在轉譯元件之後，才會有 JavaScript 元素。

[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) 呼叫以 rerender 具有從 JavaScript interop 呼叫取得之新狀態的元件 (如需詳細資訊，請參閱 <xref:blazor/components/rendering>) 。 程式碼不會建立無限迴圈，因為 `StateHasChanged` 只有在是時才會呼叫 `infoFromJs` `null` 。

`Pages/PrerenderedInterop2.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop2.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/prerendering/PrerenderedInterop2.razor)]

::: moniker-end

針對上述的範例程式碼，請在 `setElementText2` `<head>` `wwwroot/index.html` (Blazor WebAssembly) 或 `Pages/_Host.cshtml` () 的元素內提供 JavaScript 函式 Blazor Server 。 呼叫函式時，會傳回 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 值：

```html
<script>
  window.setElementText2 = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> **上述範例僅針對示範目的，直接修改檔物件模型 (DOM) 。** 在大部分的情況下，不建議直接修改具有 JavaScript 的 DOM，因為 JavaScript 可能會干擾 Blazor 變更追蹤。

> [!NOTE]
> 上述範例會干擾具有全域方法的用戶端。 若要在生產應用程式中提供更好的方法，請參閱[ Blazor JavaScript 隔離和物件參考](xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references)。
>
> 範例：
>
> ```javascript
> export setElementText2 = (element, text) => {
>   element.innerText = text;
>   return text;
> };
> ```
