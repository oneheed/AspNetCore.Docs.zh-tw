---
title: ASP.NET Core Blazor Server 額外的安全性案例
author: guardrex
description: 瞭解如何設定 Blazor Server 額外的安全性案例。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 89288f3fce2dbb6f2647693ba8aaf29500b5bb2b
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805488"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server 額外的安全性案例

[Javier Calvarro Nelson](https://github.com/javiercn)

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">將權杖傳遞至 Blazor Server 應用程式</h2>

您 Razor Blazor Server 可以使用本節所述的方法，將應用程式中元件之外可用的權杖傳遞給元件。

Blazor Server使用一般 Razor 頁面或 MVC 應用程式來驗證應用程式。 布建權杖並將其儲存至驗證 cookie 。 例如：

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

（選擇性）加入其他範圍 `options.Scope.Add("{SCOPE}");` ，其中預留位置 `{SCOPE}` 是要加入的其他範圍。

定義 **範圍** 的權杖提供者服務，該服務可在 Blazor 應用程式內用來從相依性 [插入 (DI) ](xref:blazor/fundamentals/dependency-injection)解析權杖：

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

在中 `Startup.ConfigureServices` ，新增下列服務：

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

使用存取和重新整理權杖來定義要在初始應用程式狀態中傳遞的類別：

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

在檔案中 `_Host.cshtml` ，建立和的實例， `InitialApplicationState` 並將它當作參數傳遞至應用程式：

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

在 `App` 元件 (`App.razor`) 中，解析服務並使用參數中的資料將它初始化：

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

將套件參考新增至 NuGet 套件的應用程式 [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) 。

在提出安全 API 要求的服務中，插入權杖提供者，並取得 API 要求的權杖：

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">設定驗證配置</h2>

如果應用程式使用一個以上的驗證中介軟體，因此有一個以上的驗證配置，則使用的 Blazor 配置可以在的端點設定中明確設定 `Startup.Configure` 。 下列範例會設定 Azure Active Directory 配置：

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">將權杖傳遞至 Blazor Server 應用程式</h2>

您 Razor Blazor Server 可以使用本節所述的方法，將應用程式中元件之外可用的權杖傳遞給元件。

Blazor Server使用一般 Razor 頁面或 MVC 應用程式來驗證應用程式。 布建權杖並將其儲存至驗證 cookie 。 例如：

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

（選擇性）加入其他範圍 `options.Scope.Add("{SCOPE}");` ，其中預留位置 `{SCOPE}` 是要加入的其他範圍。

（選擇性）使用指定的資源 `options.Resource = "{RESOURCE}";` ，其中預留位置 `{RESOURCE}` 為資源。 例如：

```csharp
options.Resource = "https://graph.microsoft.com";
```

使用存取和重新整理權杖來定義要在初始應用程式狀態中傳遞的類別：

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

定義 **範圍** 的權杖提供者服務，該服務可在 Blazor 應用程式內用來從相依性 [插入 (DI) ](xref:blazor/fundamentals/dependency-injection)解析權杖：

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

在中 `Startup.ConfigureServices` ，新增下列服務：

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

在檔案中 `_Host.cshtml` ，建立和的實例， `InitialApplicationState` 並將它當作參數傳遞至應用程式：

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

在 `App` 元件 (`App.razor`) 中，解析服務並使用參數中的資料將它初始化：

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

將套件參考新增至 NuGet 套件的應用程式 [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) 。

在提出安全 API 要求的服務中，插入權杖提供者，並取得 API 要求的權杖：

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">設定驗證配置</h2>

如果應用程式使用一個以上的驗證中介軟體，因此有一個以上的驗證配置，則使用的 Blazor 配置可以在的端點設定中明確設定 `Startup.Configure` 。 下列範例會設定 Azure Active Directory 配置：

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>使用 OpenID Connect (OIDC) v2.0 端點

在5.0 之前的 ASP.NET Core 版本中，驗證程式庫和 Blazor 範本會使用 OpenID Connect (OIDC) v1.0 端點。 若要在5.0 之前使用 ASP.NET Core 版的 v2.0 端點，請 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> 在中設定 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 下列選項：

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

或者，您可以在應用程式設定 () 檔中進行設定 `appsettings.json` ：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

如果追蹤于區段上的授權不適合應用程式的 OIDC 提供者（例如，使用非 AAD 提供者），請 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接設定屬性。 請在 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 應用程式佈建檔中使用金鑰設定或的屬性 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 。

### <a name="code-changes"></a>程式碼變更

* V2.0 端點的識別碼權杖中宣告的清單會變更。 如需詳細資訊，請參閱 [為何要更新至 Microsoft 身分識別平臺 (v2.0) ？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) 在 Azure 檔中。
* 由於已在 v2.0 端點的範圍 Uri 中指定資源，因此請移除 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 中的屬性設定 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ：

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  如需詳細資訊，請參閱 Azure 檔中的 [範圍，而非資源](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) 。

### <a name="app-id-uri"></a>應用程式識別碼 URI

* 使用 v2.0 端點時，Api *`App ID URI`* 會定義，其目的是要代表 API 的唯一識別碼。
* 所有範圍都包含應用程式識別碼 URI 做為前置詞，而 v2.0 端點會以應用程式識別碼 URI 作為物件來發出存取權杖。
* 使用 v2.0 端點時，伺服器 API 中設定的用戶端識別碼會從 API 應用程式識別碼 (用戶端識別碼) 變更為應用程式識別碼 URI。

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

您可以在 OIDC 提供者應用程式註冊描述中找到要使用的應用程式識別碼 URI。

::: moniker-end
