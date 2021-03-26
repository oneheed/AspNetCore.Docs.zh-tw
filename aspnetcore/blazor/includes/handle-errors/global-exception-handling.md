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
ms.openlocfilehash: 0ce606cc4800bcf5855b70a26272fceb3ad8e944
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105554874"
---
<span data-ttu-id="a0345-101">Blazor 是單一頁面應用程式， (SPA) 用戶端架構。</span><span class="sxs-lookup"><span data-stu-id="a0345-101">Blazor is a single-page application (SPA) client-side framework.</span></span> <span data-ttu-id="a0345-102">瀏覽器會作為應用程式的主機，因此會根據 Razor 流覽和靜態資產的 URI 要求，作為個別元件的處理管線。</span><span class="sxs-lookup"><span data-stu-id="a0345-102">The browser serves as the app's host and thus acts as the processing pipeline for individual Razor components based on URI requests for navigation and static assets.</span></span> <span data-ttu-id="a0345-103">不同于在具有中介軟體處理管線的伺服器上執行的 ASP.NET Core 應用程式，沒有中介軟體管線可處理 Razor 元件的要求，這些元件可用於全域錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="a0345-103">Unlike ASP.NET Core apps that run on the server with a middleware processing pipeline, there is no middleware pipeline that processes requests for Razor components that can be leveraged for global error handling.</span></span> <span data-ttu-id="a0345-104">不過，應用程式可以使用錯誤處理元件做為串聯值，以集中方式處理錯誤。</span><span class="sxs-lookup"><span data-stu-id="a0345-104">However, an app can use an error processing component as a cascading value to process errors in a centralized way.</span></span>

<span data-ttu-id="a0345-105">下列元件會將 `Error` 本身當作 [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) 子元件傳遞。</span><span class="sxs-lookup"><span data-stu-id="a0345-105">The following `Error` component passes itself as a [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) to child components.</span></span> <span data-ttu-id="a0345-106">下列範例只會記錄錯誤，但元件的方法可以透過應用程式所需的任何方式來處理錯誤，包括使用多個錯誤處理方法。</span><span class="sxs-lookup"><span data-stu-id="a0345-106">The following example merely logs the error, but methods of the component can process errors in any way required by the app, including through the use of multiple error processing methods.</span></span> <span data-ttu-id="a0345-107">使用元件而不使用 [插入的服務](xref:blazor/fundamentals/dependency-injection) 或自訂記錄器執行的優點是，串聯元件可以轉譯內容，並在發生錯誤時套用 CSS 樣式。</span><span class="sxs-lookup"><span data-stu-id="a0345-107">An advantage of using a component over using an [injected service](xref:blazor/fundamentals/dependency-injection) or a custom logger implementation is that a cascaded component can render content and apply CSS styles when an error occurs.</span></span>

<span data-ttu-id="a0345-108">`Shared/Error.razor`:</span><span class="sxs-lookup"><span data-stu-id="a0345-108">`Shared/Error.razor`:</span></span>

```razor
@using Microsoft.Extensions.Logging
@inject ILogger<Error> Logger

<CascadingValue Value=this>
    @ChildContent
</CascadingValue>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public void ProcessError(Exception ex)
    {
        Logger.LogError("Error:ProcessError - Type: {Type} Message: {Message}", 
            ex.GetType(), ex.Message);
    }
}
```

<span data-ttu-id="a0345-109">在元件中，將元件包裝在元件中 `App` `Router` `Error` 。</span><span class="sxs-lookup"><span data-stu-id="a0345-109">In the `App` component, wrap the `Router` component with the `Error` component.</span></span> <span data-ttu-id="a0345-110">這可讓 `Error` 元件向下串聯至應用程式中的任何元件，而該元件 `Error` 會收到做為 [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) 。</span><span class="sxs-lookup"><span data-stu-id="a0345-110">This permits the `Error` component to cascade down to any component of the app where the `Error` component is received as a [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute).</span></span>

<span data-ttu-id="a0345-111">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="a0345-111">`App.razor`:</span></span>

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

<span data-ttu-id="a0345-112">若要處理元件中的錯誤：</span><span class="sxs-lookup"><span data-stu-id="a0345-112">To process errors in a component:</span></span>

* <span data-ttu-id="a0345-113">`Error` [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) 在區塊中將元件指定為 [`@code`](xref:mvc/views/razor#code) ：</span><span class="sxs-lookup"><span data-stu-id="a0345-113">Designate the `Error` component as a [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) in the [`@code`](xref:mvc/views/razor#code) block:</span></span>

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* <span data-ttu-id="a0345-114">在任何具有適當例外狀況類型的區塊中呼叫錯誤處理方法 `catch` 。</span><span class="sxs-lookup"><span data-stu-id="a0345-114">Call an error processing method in any `catch` block with an appropriate exception type.</span></span> <span data-ttu-id="a0345-115">範例 `Error` 元件只提供單一 `ProcessError` 方法，但錯誤處理元件可以提供任何數目的錯誤處理方法，以解決整個應用程式的替代錯誤處理需求。</span><span class="sxs-lookup"><span data-stu-id="a0345-115">The example `Error` component only offers a single `ProcessError` method, but the error processing component can provide any number of error processing methods to address alternative error processing requirements throughout the app.</span></span>

  ```csharp
  try
  {
      ...
  }
  catch (Exception ex)
  {
      Error.ProcessError(ex);
  }
  ```

<span data-ttu-id="a0345-116">使用上述的範例 `Error` 元件和 `ProcessError` 方法，瀏覽器的開發人員工具主控台會指出已攔截的已記錄錯誤：</span><span class="sxs-lookup"><span data-stu-id="a0345-116">Using the preceding example `Error` component and `ProcessError` method, the browser's developer tools console indicates the trapped, logged error:</span></span>

> <span data-ttu-id="a0345-117">失敗： Blazor 範例。共用。錯誤 [0] 錯誤： ProcessError-Type： NullReferenceException Message： object 參考未設定為物件的實例。</span><span class="sxs-lookup"><span data-stu-id="a0345-117">fail: BlazorSample.Shared.Error[0] Error:ProcessError - Type: System.NullReferenceException Message: Object reference not set to an instance of an object.</span></span>

<span data-ttu-id="a0345-118">如果 `ProcessError` 方法直接參與轉譯，例如顯示自訂錯誤訊息列或變更所呈現專案的 CSS 樣式，請 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) 在方法結尾處呼叫 `ProcessErrors` 以 rerender UI。</span><span class="sxs-lookup"><span data-stu-id="a0345-118">If the `ProcessError` method directly participates in rendering, such as showing a custom error message bar or changing the CSS styles of the rendered elements, call [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) at the end of the `ProcessErrors` method to rerender the UI.</span></span>
