此 `FetchData` 元件顯示如何：

* 布建存取權杖。
* 使用存取權杖，在 *伺服器* 應用程式中呼叫受保護的資源 API。

[`@attribute [Authorize]`](xref:mvc/views/razor#attribute)指示詞向 Blazor WebAssembly 授權系統指出，使用者必須獲得授權才能造訪此元件。 應用程式中的屬性存在 `Client` 時，不會讓伺服器上的 API 無法在沒有適當認證的情況下呼叫。 `Server`應用程式也必須 `[Authorize]` 在適當的端點上使用，才能正確地加以保護。

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> 負責要求可新增至要求的存取權杖，以呼叫 API。 如果已快取權杖，或服務能夠在沒有使用者互動的情況下布建新的存取權杖，則權杖要求會成功。 否則，權杖要求會失敗，並出現 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 在語句中攔截到的 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 。

為了取得要包含在要求中的實際權杖，應用程式必須呼叫，以檢查要求是否成功 [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) 。

如果要求成功，權杖變數就會填入存取權杖。 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>權杖的屬性會公開要包含在要求標頭中的常值字串 `Authorization` 。

如果要求失敗，因為無法在沒有使用者互動的情況下布建權杖，權杖結果會包含重新導向 URL。 流覽到此 URL 會讓使用者前往登入頁面，並在驗證成功後回到目前頁面。

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
