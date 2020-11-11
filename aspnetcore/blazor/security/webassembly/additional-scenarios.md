---
title: ASP.NET Core Blazor WebAssembly 額外的安全性案例
author: guardrex
description: 瞭解如何設定 Blazor WebAssembly 額外的安全性案例。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: baed18df2d127b592f420aac0432e0b28f076d46
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508041"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly 額外的安全性案例

由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>將權杖附加至傳出要求

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 是 <xref:System.Net.Http.DelegatingHandler> 用來將存取權杖附加至外寄 <xref:System.Net.Http.HttpResponseMessage> 實例。 您可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 由架構註冊的服務來取得權杖。 如果無法取得權杖， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 就會擲回。 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 具有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，可用來將使用者流覽至身分識別提供者，以取得新的權杖。

為了方便起見，架構會提供 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 預先設定的應用程式基底位址作為授權的 URL。 **只有當要求 URI 在應用程式的基底 URI 內時，才會新增存取權杖。** 當外寄要求 Uri 不在應用程式的基底 URI 內時，請使用 [自訂 `AuthorizationMessageHandler` 類別 ( *建議*](#custom-authorizationmessagehandler-class) [的 `AuthorizationMessageHandler`](#configure-authorizationmessagehandler)) 或設定。

> [!NOTE]
> 除了用於伺服器 API 存取的用戶端應用程式設定之外，當用戶端和伺服器不在相同的基底位址時，伺服器 API 也必須允許跨原始來源的要求 (CORS) 。 如需伺服器端 CORS 設定的詳細資訊，請參閱本文稍後的「 [跨原始資源分享 (CORS) ](#cross-origin-resource-sharing-cors) 一節。

在下例中︰

* <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> 將 <xref:System.Net.Http.IHttpClientFactory> 和相關服務新增至服務集合，並設定名為 <xref:System.Net.Http.HttpClient> (`ServerAPI`) 。 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 這是傳送要求時，資源 URI 的基底位址。 <xref:System.Net.Http.IHttpClientFactory> 是由 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet 套件提供。
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 這是 <xref:System.Net.Http.DelegatingHandler> 用來將存取權杖附加至傳出 <xref:System.Net.Http.HttpResponseMessage> 實例的。 只有當要求 URI 在應用程式的基底 URI 內時，才會新增存取權杖。
* <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType><xref:System.Net.Http.HttpClient>使用對應于命名 () 的設定，建立並設定傳出要求的實例 <xref:System.Net.Http.HttpClient> `ServerAPI` 。

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ，預設會在應用程式的基底 URI 內要求 uri。 因此， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> `new Uri(builder.HostEnvironment.BaseAddress)` 會 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 在從專案範本產生的應用程式中，將 () 指派給。

設定 <xref:System.Net.Http.HttpClient> 用來利用模式提出授權的要求 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ：

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Http.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a>自訂 `AuthorizationMessageHandler` 類別

*針對不在應用程式基底 URI 內的 Uri 提出傳出要求的用戶端應用程式，建議您在本節中使用本指南。*

在下列範例中，自訂類別會擴充 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 以做為的 <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpClient> 。 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 設定此處理程式，以使用存取權杖授權輸出 HTTP 要求。 只有當至少有一個授權的 Url 是要求 URI 的基底 () 時，才會附加存取權杖 <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> 。

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

在 `Program.Main` (`Program.cs`) 中， `CustomAuthorizationMessageHandler` 會註冊為範圍服務，並設定為 <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpResponseMessage> 已命名之的傳出實例 <xref:System.Net.Http.HttpClient> ：

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> `new Uri(builder.HostEnvironment.BaseAddress)` 預設會將 () 指派給 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 。

設定 <xref:System.Net.Http.HttpClient> 用來利用模式提出授權的要求 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 。 使用 (封裝) 來建立用戶端時 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) ，會在對 <xref:System.Net.Http.HttpClient> 伺服器 API 提出要求時，提供包含存取權杖的實例。 如果要求 URI 是相對 URI，如下列範例 (`ExampleAPIMethod`) ，它會與 <xref:System.Net.Http.HttpClient.BaseAddress> 用戶端應用程式提出要求時結合：

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a>設定 `AuthorizationMessageHandler`

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 您可以使用方法，利用授權的 Url、範圍和傳回 URL 來設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 。 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 設定處理常式，以使用存取權杖授權輸出 HTTP 要求。 只有當至少有一個授權的 Url 是要求 URI 的基底 () 時，才會附加存取權杖 <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> 。 如果要求 URI 是相對 URI，則會與結合 <xref:System.Net.Http.HttpClient.BaseAddress> 。

在下列範例中，會 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> 在 `Program.Main` () 中設定 `Program.cs` ：

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 預設會指派給下列專案：

* <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 。
* 陣列的 URL `authorizedUrls` 。

## <a name="typed-httpclient"></a>類型 `HttpClient`

您可以定義具型別用戶端，以處理單一類別內的所有 HTTP 和權杖取得問題。

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient http;
 
    public WeatherForecastClient(HttpClient http)
    {
        this.http = http;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `using static BlazorSample.Data;`) 。

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> `new Uri(builder.HostEnvironment.BaseAddress)` 預設會將 () 指派給 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 。

`FetchData` 元件 (`Pages/FetchData.razor`) ：

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>設定 `HttpClient` 處理常式

您可以 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 針對輸出 HTTP 要求進一步設定處理常式。

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 預設會指派給下列專案：

* <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 。
* 陣列的 URL `authorizedUrls` 。

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求

如果 Blazor WebAssembly 應用程式通常使用安全的預設值 <xref:System.Net.Http.HttpClient> ，則應用程式也可以藉由設定名為的，來提出未經驗證或未經授權的 web API 要求 <xref:System.Net.Http.HttpClient> ：

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> `new Uri(builder.HostEnvironment.BaseAddress)` 預設會將 () 指派給 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 。

先前的註冊是現有的安全預設註冊以外的註冊 <xref:System.Net.Http.HttpClient> 。

元件會 <xref:System.Net.Http.HttpClient> 從 <xref:System.Net.Http.IHttpClientFactory> (套件建立， [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)) 以提出未經驗證或未經授權的要求：

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> 伺服器 API 中的控制器在 `WeatherForecastNoAuthenticationController` 上述範例中，不會以 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性標記。

決定是否要使用安全用戶端或不安全的用戶端做為預設 <xref:System.Net.Http.HttpClient> 實例，取決於開發人員。 做出這項決定的其中一種方式，是考慮應用程式所聯繫的已驗證和未驗證端點數目。 如果應用程式的大部分要求都是保護 API 端點，請使用已驗證的 <xref:System.Net.Http.HttpClient> 實例作為預設值。 否則，請將未驗證的 <xref:System.Net.Http.HttpClient> 實例註冊為預設值。

使用的替代方法是建立具型別 <xref:System.Net.Http.IHttpClientFactory> [用戶端](#typed-httpclient) ，以未經驗證的匿名端點存取。

## <a name="request-additional-access-tokens"></a>要求其他存取權杖

您可以藉由呼叫來手動取得存取權杖 `IAccessTokenProvider.RequestAccessToken` 。 在下列範例中，應用程式需要一個額外的範圍作為預設值 <xref:System.Net.Http.HttpClient> 。 Microsoft 驗證程式庫 (MSAL) 範例會使用下列內容來設定領域 `MsalProviderOptions` ：

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

`{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` 上述範例中的和預留位置是自訂範圍。

`IAccessTokenProvider.RequestToken`方法提供多載，可讓應用程式使用一組指定的範圍布建存取權杖。

在 Razor 元件中：

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

`{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` 上述範例中的和預留位置是自訂範圍。

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> 返回：

* `true` 搭配 `token` 使用。
* `false` 如果未抓取權杖，則為。

## <a name="cross-origin-resource-sharing-cors"></a>跨原始來源資源分享 (CORS)

在 cookie cors 要求上 (授權 s/標頭) 傳送認證時， `Authorization` cors 原則必須允許標頭。

下列原則包含的設定：

* 要求來源 (`http://localhost:5000` ， `https://localhost:5001`) 。
* 任何方法 (動詞) 。
* `Content-Type` 和 `Authorization` 標頭。 若要允許自訂標頭 (例如， `x-custom-header`) ，請在呼叫時列出標頭 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 。
* 用戶端 JavaScript 程式碼 (`credentials` 屬性設定為) 的認證 `include` 。

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

以裝載 Blazor 的專案範本為基礎的託管解決方案會 Blazor 針對用戶端和伺服器應用程式使用相同的基底位址。 用戶端應用程式的 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 預設設定為 URI `builder.HostEnvironment.BaseAddress` 。 從裝載的專案範本建立之裝載應用程式的預設設定中， **不** 需要 CORS 設定 Blazor 。 不是由伺服器專案所裝載且不共用伺服器應用程式基底位址的其他用戶端應用程式 **，需要伺服器** 專案中的 CORS 設定。

如需詳細資訊，請參閱 <xref:security/cors> 和範例應用程式的 HTTP 要求測試器元件 (`Components/HTTPRequestTester.razor`) 。

## <a name="handle-token-request-errors"></a>處理權杖要求錯誤

當單一頁面應用程式 (SPA) 使用 OpenID Connect (OIDC) 來驗證使用者時，驗證狀態會在 SPA 和 Identity 提供者 (IP) 中，以 cookie 因使用者提供其認證而設定的會話形式在本機維護。

IP 發出給使用者的權杖通常會在短時間內有效（大約一小時），因此用戶端應用程式必須定期提取新的權杖。 否則，使用者將會在授與的權杖到期之後登出。 在大多數情況下，OIDC 用戶端可以布建新權杖，而不需要使用者再次驗證，因為在 IP 內保留驗證狀態或「會話」。

在某些情況下，用戶端無法在沒有使用者互動的情況下取得權杖，例如，基於某個原因而導致使用者明確登出 IP。 當使用者造訪並登出時，就會發生這種情況 `https://login.microsoftonline.com` 。在這些情況下，應用程式不會立即知道使用者已登出。用戶端保存的任何權杖都可能不再有效。 此外，當目前的權杖到期後，用戶端無法在沒有使用者互動的情況下布建新的權杖。

這些案例不是以權杖為基礎的驗證所特有。 它們是 Spa 本質的一部分。 如果已移除驗證，則使用的 SPA cookie 也無法呼叫伺服器 API cookie 。

當應用程式執行受保護資源的 API 呼叫時，您必須注意下列事項：

* 若要布建新的存取權杖以呼叫 API，使用者可能需要再次進行驗證。
* 即使用戶端具有看似有效的權杖，對伺服器的呼叫可能會失敗，因為使用者已撤銷權杖。

當應用程式要求權杖時，有兩個可能的結果：

* 要求成功，且應用程式具有有效的權杖。
* 要求失敗，且應用程式必須再次驗證使用者，才能取得新的權杖。

當令牌要求失敗時，您必須決定是否要儲存任何目前的狀態，然後再執行重新導向。 有幾種方法存在，並增加複雜度：

* 將目前的頁面狀態儲存在會話儲存體中。 在[ `OnInitializedAsync` 生命週期事件](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 期間，檢查是否可還原狀態後再繼續。
* 新增查詢字串參數，並使用該參數做為應用程式的信號，以指示應用程式需要重新以提供先前儲存的狀態。
* 新增具有唯一識別碼的查詢字串參數，以在會話儲存體中儲存資料，而不會與其他專案發生風險衝突。

下列範例示範如何執行：

* 重新導向至登入頁面之前，請先保留狀態。
* 使用查詢字串參數，在驗證之後復原先前的狀態。

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider
...

<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var http = new HttpClient();
        http.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            http.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await http.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>在驗證操作之前儲存應用程式狀態

在驗證作業期間，某些情況下，您會想要先儲存應用程式狀態，然後再將瀏覽器重新導向至 IP。 當您使用狀態容器，並且想要在驗證成功後還原狀態時，就會發生這種情況。 您可以使用自訂驗證狀態物件來保留應用程式特定狀態或其參考，並在驗證作業成功完成之後還原該狀態。 下列範例會示範方法。

在應用程式中建立狀態容器類別，其屬性可保存應用程式的狀態值。 在下列範例中，容器是用來維護預設專案範本元件的計數器值 `Counter` (`Pages/Counter.razor`) 。 序列化和還原序列化容器的方法是依據 <xref:System.Text.Json> 。

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

`Counter`元件會使用狀態容器來維護 `currentCount` 元件以外的值：

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

從建立 `ApplicationAuthenticationState` <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> 。 提供 `Id` 屬性，作為本機儲存狀態的識別碼。

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

`Authentication`元件 (`Pages/Authentication.razor`) 使用具有序列化和還原序列化方法的本機會話儲存體來儲存及還原應用程式的狀態 `StateContainer` ， `GetStateForLocalStorage` 以及 `SetStateFromLocalStorage` ：

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

此範例使用 Azure Active Directory (AAD) 進行驗證。 在 `Program.Main` (`Program.cs`) ：

* `ApplicationAuthenticationState`設定為 Microsoft 驗證程式庫 (MSAL) `RemoteAuthenticationState` 類型。
* 狀態容器是在服務容器中註冊。

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>自訂應用程式路由

根據預設，連結 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 庫會使用下表所示的路由來表示不同的驗證狀態。

| 路由                            | 目的 |
| -------------------------------- | ------- |
| `authentication/login`           | 觸發登入操作。 |
| `authentication/login-callback`  | 處理任何登入作業的結果。 |
| `authentication/login-failed`    | 當登入作業因某些原因而失敗時，會顯示錯誤訊息。 |
| `authentication/logout`          | 觸發登出操作。 |
| `authentication/logout-callback` | 處理登出作業的結果。 |
| `authentication/logout-failed`   | 當登出作業因某些原因而失敗時，會顯示錯誤訊息。 |
| `authentication/logged-out`      | 指出使用者已成功登出。 |
| `authentication/profile`         | 觸發操作以編輯使用者設定檔。 |
| `authentication/register`        | 觸發操作以註冊新的使用者。 |

上表中顯示的路由可透過進行設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 。 設定選項以提供自訂路由時，請確認應用程式具有可處理每個路徑的路由。

在下列範例中，所有路徑都會加上前置詞 `/security` 。

`Authentication` 元件 (`Pages/Authentication.razor`) ：

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

如果要求需要完全不同的路徑，請如先前所述設定路由，並 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 使用明確的動作參數來轉譯：

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

如果您選擇這麼做，就可以將 UI 分成不同的頁面。

## <a name="customize-the-authentication-user-interface"></a>自訂驗證使用者介面

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 針對每個驗證狀態包含一組預設的 UI 片段。 您可以藉由傳入自訂來自訂每個狀態 <xref:Microsoft.AspNetCore.Components.RenderFragment> 。 若要在初始登入程式期間自訂所顯示的文字，可以變更，如下所示 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 。

`Authentication` 元件 (`Pages/Authentication.razor`) ：

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>有一個片段可用於下表所示的每個驗證路由。

| 路由                            | 片段                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a>自訂使用者

可以自訂系結至應用程式的使用者。

### <a name="customize-the-user-with-a-payload-claim"></a>使用承載宣告自訂使用者

在下列範例中，應用程式的已驗證使用者會收到 `amr` 每個使用者驗證方法的宣告。 `amr`宣告會識別如何在 Microsoft Platform v1.0 承載宣告中驗證權杖的主體 Identity 。 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim) 此範例使用根據的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 。

建立擴充類別的類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 。 下列範例會將 `AuthenticationMethod` 屬性設定為使用者的 `amr` JSON 屬性值陣列。 `AuthenticationMethod` 當使用者經過驗證時，架構會自動填入。

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

建立可延伸的 factory， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> 以從儲存在中的使用者驗證方法建立宣告 `CustomUserAccount.AuthenticationMethod` ：

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }

        return initialUser;
    }
}
```

`CustomAccountFactory`為使用中的驗證提供者註冊。 下列任何一個註冊都有效：

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a>具有自訂使用者帳戶類別的 AAD 安全性群組和角色

如需適用于 AAD 安全性群組和 AAD 系統管理員角色和自訂使用者帳戶類別的其他範例，請參閱 <xref:blazor/security/webassembly/aad-groups-roles> 。

## <a name="support-prerendering-with-authentication"></a>支援使用驗證進行預進行

遵循其中一個託管應用程式主題中的指引之後 Blazor WebAssembly ，請使用下列指示來建立應用程式：

* 不需要授權的 Prerenders 路徑。
* 不需要授權的呈現路徑。

在 *`Client`* 應用程式的 `Program` 類別 (`Program.cs`) 中，將常見的服務註冊納入不同的方法 (例如 `ConfigureCommonServices`) ：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add...;

        builder.Services.AddScoped( ... );

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

在伺服器應用程式的中 `Startup.ConfigureServices` ，註冊下列其他服務：

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

在伺服器應用程式的 `Startup.Configure` 方法中，將取代 [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) 為 [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) ：

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

在伺服器應用程式中， `Pages` 如果資料夾不存在，請加以建立。 在 `_Host.cshtml` 伺服器應用程式的資料夾內建立頁面 `Pages` 。 將應用程式檔案中的內容貼到檔案中 *`Client`* `wwwroot/index.html` `Pages/_Host.cshtml` 。 更新檔案的內容：

* 將 `@page "_Host"` 新增到檔案的頂端。
* `<app>Loading...</app>`以下列內容取代標記：

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>託管應用程式和協力廠商登入提供者的選項

Blazor WebAssembly使用協力廠商提供者驗證及授權託管應用程式時，有數個選項可用來驗證使用者。 您選擇哪一個取決於您的案例。

如需詳細資訊，請參閱<xref:security/authentication/social/additional-claims>。

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>驗證使用者只呼叫受保護的協力廠商 Api

使用用戶端的 OAuth 流程，向協力廠商 API 提供者驗證使用者：

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 在此情節中：

* 裝載應用程式的伺服器不會扮演角色。
* 無法保護伺服器上的 Api。
* 應用程式只能呼叫受保護的協力廠商 Api。

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>使用協力廠商提供者驗證使用者，並在主機伺服器和協力廠商呼叫受保護的 Api

Identity使用協力廠商登入提供者進行設定。 取得協力廠商 API 存取所需的權杖，並加以儲存。

當使用者登入時，會在 Identity 驗證過程中收集存取和重新整理權杖。 屆時，有幾種方法可以對協力廠商 Api 進行 API 呼叫。

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>使用伺服器存取權杖來取出協力廠商存取權杖

使用伺服器上產生的存取權杖，從伺服器 API 端點取出協力廠商存取權杖。 從該處，使用協力廠商存取權杖，直接從用戶端呼叫協力廠商 API 資源 Identity 。

我們不建議採用此方法。 這種方法需要將協力廠商存取權杖視為針對公用用戶端產生的權杖。 在 OAuth 詞彙中，公用應用程式沒有用戶端密碼，因為它無法受信任以安全地儲存秘密，而且會為機密用戶端產生存取權杖。 機密用戶端是具有用戶端密碼的用戶端，並假設能夠安全地儲存秘密。

* 協力廠商存取權杖可能會被授與其他範圍，以根據協力廠商針對較受信任的用戶端發出權杖的事實來執行敏感性作業。
* 同樣地，重新整理權杖不應發給不受信任的用戶端，因為這樣做會讓用戶端無限制存取，除非有其他限制。

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>從用戶端對伺服器 API 進行 API 呼叫，以便呼叫協力廠商 Api

從用戶端對伺服器 API 進行 API 呼叫。 從伺服器取得協力廠商 API 資源的存取權杖，併發出需要的任何呼叫。

雖然此方法需要透過伺服器的額外網路躍點來呼叫協力廠商 API，但最終會產生更安全的體驗：

* 伺服器可以儲存重新整理權杖，並確保應用程式不會失去協力廠商資源的存取權。
* 應用程式無法從伺服器洩漏存取權杖，可能包含更多敏感性許可權。

## <a name="use-openid-connect-oidc-v20-endpoints"></a>使用 OpenID Connect (OIDC) v2.0 端點

驗證程式庫和 Blazor 專案範本使用 OpenID Connect (OIDC) v1.0 端點。 若要使用 v2.0 端點，請設定 JWT 持有人 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 選項。 在下列範例中，會將區段附加至屬性，以針對 v2.0 設定 AAD `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> ：

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

或者，您可以在應用程式設定 () 檔中進行設定 `appsettings.json` ：

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

如果追蹤于區段上的授權不適合應用程式的 OIDC 提供者（例如，使用非 AAD 提供者），請 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接設定屬性。 您可以在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 應用程式佈建檔案中設定或在應用程式佈建檔案中設定屬性 (`appsettings.json`) 的 `Authority` 金鑰。

V2.0 端點的識別碼權杖中宣告的清單會變更。 如需詳細資訊，請參閱 [為何要更新至 Microsoft 身分識別平臺 (v2.0) ？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。

## <a name="configure-and-use-grpc-in-components"></a>在元件中設定和使用 gRPC

若要將 Blazor WebAssembly 應用程式設定為使用 [ASP.NET Core gRPC 架構](xref:grpc/index)：

* 在伺服器上啟用 gRPC Web。 如需詳細資訊，請參閱<xref:grpc/browser>。
* 註冊應用程式訊息處理常式的 gRPC 服務。 下列範例會設定應用程式的授權訊息處理常式，以使用[ `GreeterClient` gRPC 教學](xref:tutorials/grpc/grpc-start#create-a-grpc-service)課程 () 中的服務 `Program.Main` ：

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample`) 。 將檔案放 `.proto` 在 `Shared` 主控方案的專案中 Blazor 。

用戶端應用程式中的元件可以使用 gRPC 用戶端 () 來進行 gRPC 呼叫 `Pages/Grpc.razor` ：

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample`) 。 若要使用 `Status.DebugException` 屬性，請使用 [Grpc .Net. Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 或更新版本。

如需詳細資訊，請參閱<xref:grpc/browser>。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/security/webassembly/graph-api>
* [`HttpClient` 以及 `HttpRequestMessage` 使用 FETCH API 要求選項](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
