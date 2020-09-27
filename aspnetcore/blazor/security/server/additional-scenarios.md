---
title: ASP.NET Core Blazor Server 額外的安全性案例
author: guardrex
description: 瞭解如何設定 Blazor Server 額外的安全性案例。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
ms.openlocfilehash: 9b3698489300e45cf77c3d51611ff44e2f4e16a5
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393661"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="16aaa-103">ASP.NET Core Blazor Server 額外的安全性案例</span><span class="sxs-lookup"><span data-stu-id="16aaa-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="16aaa-104">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="16aaa-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a><span data-ttu-id="16aaa-105">將權杖傳遞至 Blazor Server 應用程式</span><span class="sxs-lookup"><span data-stu-id="16aaa-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="16aaa-106">您 Razor Blazor Server 可以使用本節所述的方法，將應用程式中元件之外可用的權杖傳遞給元件。</span><span class="sxs-lookup"><span data-stu-id="16aaa-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="16aaa-107">如需範例程式碼，包括完整的 `Startup.ConfigureServices` 範例，請參閱將 [權杖傳遞至伺服器端 Blazor 應用程式](https://github.com/javiercn/blazor-server-aad-sample)。</span><span class="sxs-lookup"><span data-stu-id="16aaa-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="16aaa-108">Blazor Server使用一般 Razor 頁面或 MVC 應用程式來驗證應用程式。</span><span class="sxs-lookup"><span data-stu-id="16aaa-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="16aaa-109">布建權杖並將其儲存至驗證 cookie 。</span><span class="sxs-lookup"><span data-stu-id="16aaa-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="16aaa-110">例如：</span><span class="sxs-lookup"><span data-stu-id="16aaa-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="16aaa-111">使用存取和重新整理權杖來定義要在初始應用程式狀態中傳遞的類別：</span><span class="sxs-lookup"><span data-stu-id="16aaa-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="16aaa-112">定義 **範圍** 的權杖提供者服務，該服務可在 Blazor 應用程式內用來從相依性 [插入 (DI) ](xref:blazor/fundamentals/dependency-injection)解析權杖：</span><span class="sxs-lookup"><span data-stu-id="16aaa-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="16aaa-113">在中 `Startup.ConfigureServices` ，新增下列服務：</span><span class="sxs-lookup"><span data-stu-id="16aaa-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="16aaa-114">在檔案中 `_Host.cshtml` ，建立和的實例， `InitialApplicationState` 並將它當作參數傳遞至應用程式：</span><span class="sxs-lookup"><span data-stu-id="16aaa-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="16aaa-115">在 `App` 元件 (`App.razor`) 中，解析服務並使用參數中的資料將它初始化：</span><span class="sxs-lookup"><span data-stu-id="16aaa-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="16aaa-116">將套件參考新增至 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) 的應用程式 NuGet 封裝。</span><span class="sxs-lookup"><span data-stu-id="16aaa-116">Add a package reference to the app for the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="16aaa-117">在提出安全 API 要求的服務中，插入權杖提供者並取得權杖以呼叫 API：</span><span class="sxs-lookup"><span data-stu-id="16aaa-117">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly TokenProvider store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="16aaa-118">設定驗證配置</span><span class="sxs-lookup"><span data-stu-id="16aaa-118">Set the authentication scheme</span></span>

<span data-ttu-id="16aaa-119">如果應用程式使用一個以上的驗證中介軟體，因此有一個以上的驗證配置，則使用的 Blazor 配置可以在的端點設定中明確設定 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="16aaa-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="16aaa-120">下列範例會設定 Azure Active Directory 配置：</span><span class="sxs-lookup"><span data-stu-id="16aaa-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="16aaa-121">使用 OpenID Connect (OIDC) v2.0 端點</span><span class="sxs-lookup"><span data-stu-id="16aaa-121">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="16aaa-122">驗證程式庫和 Blazor 範本使用 OpenID Connect (OIDC) v1.0 端點。</span><span class="sxs-lookup"><span data-stu-id="16aaa-122">The authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="16aaa-123">若要使用 v2.0 端點，請 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> 在中設定選項 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ：</span><span class="sxs-lookup"><span data-stu-id="16aaa-123">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="16aaa-124">或者，您可以在應用程式設定 () 檔中進行設定 `appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="16aaa-124">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="16aaa-125">如果追蹤于區段上的授權不適合應用程式的 OIDC 提供者（例如，使用非 AAD 提供者），請 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接設定屬性。</span><span class="sxs-lookup"><span data-stu-id="16aaa-125">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="16aaa-126">請在 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 應用程式佈建檔中使用金鑰設定或的屬性 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 。</span><span class="sxs-lookup"><span data-stu-id="16aaa-126">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="16aaa-127">程式碼變更</span><span class="sxs-lookup"><span data-stu-id="16aaa-127">Code changes</span></span>

* <span data-ttu-id="16aaa-128">V2.0 端點的識別碼權杖中宣告的清單會變更。</span><span class="sxs-lookup"><span data-stu-id="16aaa-128">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="16aaa-129">如需詳細資訊，請參閱 [為何要更新至 Microsoft 身分識別平臺 (v2.0) ？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="16aaa-129">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="16aaa-130">在 Azure 檔中。</span><span class="sxs-lookup"><span data-stu-id="16aaa-130">in the Azure documentation.</span></span>
* <span data-ttu-id="16aaa-131">由於已在 v2.0 端點的範圍 Uri 中指定資源，因此請移除 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 中的屬性設定 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ：</span><span class="sxs-lookup"><span data-stu-id="16aaa-131">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="16aaa-132">如需詳細資訊，請參閱 Azure 檔中的 [範圍，而非資源](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) 。</span><span class="sxs-lookup"><span data-stu-id="16aaa-132">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="16aaa-133">應用程式識別碼 URI</span><span class="sxs-lookup"><span data-stu-id="16aaa-133">App ID URI</span></span>

* <span data-ttu-id="16aaa-134">使用 v2.0 端點時，Api *`App ID URI`* 會定義，其目的是要代表 API 的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="16aaa-134">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="16aaa-135">所有範圍都包含應用程式識別碼 URI 做為前置詞，而 v2.0 端點會以應用程式識別碼 URI 作為物件來發出存取權杖。</span><span class="sxs-lookup"><span data-stu-id="16aaa-135">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="16aaa-136">使用 v2.0 端點時，伺服器 API 中設定的用戶端識別碼會從 API 應用程式識別碼 (用戶端識別碼) 變更為應用程式識別碼 URI。</span><span class="sxs-lookup"><span data-stu-id="16aaa-136">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="16aaa-137">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="16aaa-137">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="16aaa-138">您可以在 OIDC 提供者應用程式註冊描述中找到要使用的應用程式識別碼 URI。</span><span class="sxs-lookup"><span data-stu-id="16aaa-138">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
