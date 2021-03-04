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
ms.openlocfilehash: 1aa36c8d91dbd92485e85f223f2391303bebac42
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109701"
---
Blazor 是單一頁面應用程式， (SPA) 用戶端架構。 瀏覽器會作為應用程式的主機，因此會根據 Razor 流覽和靜態資產的 URI 要求，作為個別元件的處理管線。 不同于在具有中介軟體處理管線的伺服器上執行的 ASP.NET Core 應用程式，沒有中介軟體管線可處理 Razor 元件的要求，可用於全域錯誤處理。 不過，應用程式可以使用錯誤處理元件做為串聯值，以集中方式處理錯誤。

下列元件會將 `Error` 本身當作 [`CascadingValue`](xref:blazor/components/cascading-values-and-parameters#cascadingvalue-component) 子元件傳遞。 下列範例只會記錄錯誤，但元件的方法可以透過應用程式所需的任何方式來處理錯誤，包括使用多個錯誤處理方法。 使用元件而不使用 [插入的服務](xref:blazor/fundamentals/dependency-injection) 或自訂記錄器執行的優點是，串聯元件可以轉譯內容，並在發生錯誤時套用 CSS 樣式。

`Shared/Error.razor`:

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

在元件中，將元件包裝在元件中 `App` `Router` `Error` 。 這可讓 `Error` 元件向下串聯至應用程式中的任何元件，而該元件 `Error` 會收到做為 [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) 。

`App.razor`:

```razor
<Error>
    <Router ...>
        ...
    </Router>
</Error>
```

若要處理元件中的錯誤：

* `Error` [`CascadingParameter`](xref:blazor/components/cascading-values-and-parameters#cascadingparameter-attribute) 在區塊中將元件指定為 [`@code`](xref:mvc/views/razor#code) ：

  ```razor
  [CascadingParameter]
  public Error Error { get; set; }
  ```

* 在任何具有適當例外狀況類型的區塊中呼叫錯誤處理方法 `catch` 。 範例 `Error` 元件只提供單一 `ProcessError` 方法，但錯誤處理元件可以提供任何數目的錯誤處理方法，以解決整個應用程式的替代錯誤處理需求。

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

使用上述的範例 `Error` 元件和 `ProcessError` 方法，瀏覽器的開發人員工具主控台會指出已攔截的已記錄錯誤：

> 失敗： Blazor 範例。共用。錯誤 [0] 錯誤： ProcessError-Type： NullReferenceException Message： object 參考未設定為物件的實例。

如果 `ProcessError` 方法直接參與轉譯，例如顯示自訂錯誤訊息列或變更所呈現專案的 CSS 樣式，請 [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) 在方法結尾處呼叫 `ProcessErrors` 以 rerender UI。
