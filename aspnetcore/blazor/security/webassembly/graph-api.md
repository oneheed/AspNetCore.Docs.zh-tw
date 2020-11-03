---
title: 搭配使用圖形 API 與 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何搭配使用圖形 API 與 Blazor WebAssemlby apps。
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 569a88630f7b75e866d8ecda99605ebe3bc58db8
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234423"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>搭配使用圖形 API 與 ASP.NET Core Blazor WebAssembly

由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

[MICROSOFT GRAPH API](/graph/use-the-api) 是一種 RESTFUL web API，可讓 Blazor 和其他 .NET Framework 應用程式存取 Microsoft 雲端服務資源。

## <a name="graph-sdk"></a>Graph SDK

[Microsoft Graph sdk](/graph/sdks/sdks-overview) 的設計目的是要簡化可存取 Microsoft Graph 的高品質、有效率且可復原的應用程式。

本節中的範例需要獨立或應用程式專案檔的專案檔中，下列套件的套件參考 *`Client`* ：

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

本文的下列各節將使用下列公用程式類別和設定：

* [使用 Graph SDK 從元件呼叫圖形 API](#call-graph-api-from-a-component-using-the-graph-sdk)
* [使用 Graph SDK 自訂使用者宣告](#customize-user-claims-with-the-graph-sdk)

將 Microsoft Graph API 範圍新增至 Azure 入口網站的 AAD 區域之後：

* 將下列 `GraphClientExtensions.cs` 類別新增至裝載解決方案的獨立應用程式或 *`Client`* 應用程式 Blazor 。
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes>在方法的屬性中，提供必要的範圍 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> `AuthenticateRequestAsync` 。 在下列範例中， `User.Read` 會指定範圍，以符合本文稍後章節中的範例。

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider provider)
        {
            Provider = provider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = {STRING ARRAY OF SCOPES}
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

`{STRING ARRAY OF SCOPES}`上述程式碼中的預留位置是允許範圍的字串陣列。 例如，在本文的 `Scopes` `User.Read` 下列各節中，設定為範例的範圍：

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

在 `Program.Main` (`Program.cs`) 中，使用擴充方法新增圖形用戶端服務和設定 `AddGraphClient` ：

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

`{STRING ARRAY OF SCOPES}`上述程式碼中的預留位置是允許範圍的字串陣列。 例如，將範圍傳遞 `User.Read` 給 `AddGraphClient` 本文的下列各節中的範例：

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>使用 Graph SDK 從元件呼叫圖形 API

本節會使用本文稍早所述的 [公用程式類別 (`GraphClientExtensions.cs`) ](#graph-sdk) 。 下列 `GraphExample` 元件會使用插入的 `GraphServiceClient` 來取得使用者的 AAD 設定檔資料，並顯示其行動電話號碼：

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a>使用 Graph SDK 自訂使用者宣告

本節會使用本文稍早所述的 [公用程式類別 (`GraphClientExtensions.cs`) ](#graph-sdk) 。

在下列範例中，應用程式會從其 AAD 使用者設定檔的行動電話號碼，建立使用者的行動電話號碼宣告。 應用程式必須 `User.Read` 在 AAD 中設定圖形 API 範圍。

在下列自訂使用者帳戶 factory 中，架構 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 代表使用者的帳戶。 如果應用程式需要擴充的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ，請在下列程式碼中交換的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 。

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

在 `Program.Main` (`Program.cs`) 中，將 MSAL authentication 設定為使用自訂使用者帳戶 Factory：如果應用程式使用擴充的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ，請 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 在下列程式碼中交換的自訂使用者帳戶類別：

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a>使用圖形 API 命名用戶端

本節中的範例會使用名 <xref:System.Net.Http.HttpClient> 為的圖形 API 來取得使用者的行動電話號碼來處理通話。

本節中的範例需要 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 獨立或應用程式專案檔的專案檔中的套件參考 *`Client`* 。

建立下列類別和專案設定，以使用圖形 API。 本文的下列各節會使用下列類別和設定：

* [從元件呼叫圖形 API](#call-graph-api-from-a-component)
* [使用圖形 API 和命名用戶端自訂使用者宣告](#customize-user-claims-with-graph-api-and-a-named-client)

在 Azure 入口網站的 AAD 區域中新增 Microsoft Graph API 範圍之後，請將所需的範圍提供給應用程式為圖形 API 設定的處理常式。 下列範例會設定範圍的處理常式 `User.Read` 。 您可以新增其他範圍。

`GraphAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

在 `Program.Main` (`Program.cs`) 中，設定圖形 API 的命名 <xref:System.Net.Http.HttpClient> ：

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>從元件呼叫圖形 API

本節會使用 [圖形授權訊息處理常式 (本文稍早所述 `GraphAuthorizationMessageHandler.cs` `Program.Main` 的應用程式) 和新增](#named-client-with-graph-api) 專案，以提供 <xref:System.Net.Http.HttpClient> 針對圖形 API 命名的。

在 Razor 元件中：

* 建立 <xref:System.Net.Http.HttpClient> 圖形 API 的，併發出使用者設定檔資料的要求。
* `UserInfo.cs`類別會指定具有屬性的必要使用者設定檔屬性 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> ，以及 AAD 針對這些屬性所使用的 JSON 名稱。

`Pages/CallUser.razor`:

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

> [!NOTE]
> 在上述範例中，開發人員會將自訂 `ICallProcessor` (`CallProcessor`) 排入佇列，然後放置自動化的呼叫。

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>使用圖形 API 和命名用戶端自訂使用者宣告

本節會使用 [圖形授權訊息處理常式 (本文稍早所述 `GraphAuthorizationMessageHandler.cs` `Program.Main` 的應用程式) 和新增](#named-client-with-graph-api) 專案，以提供 <xref:System.Net.Http.HttpClient> 針對圖形 API 命名的。

在下列範例中，應用程式會從其 AAD 使用者設定檔的行動電話號碼，建立使用者的行動電話號碼宣告。 應用程式必須 `User.Read` 在 AAD 中設定圖形 API 範圍。

將 `UserInfo.cs` 類別新增至應用程式，並使用 AAD 針對這些屬性指定所需的使用者設定檔屬性（ <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute）和 JSON 名稱：

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

在下列自訂使用者帳戶 factory 中，架構 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 代表使用者的帳戶。 如果應用程式需要擴充的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ，請在下列程式碼中交換的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 。

`CustomAccountFactory.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

在 `Program.Main` (`Program.cs`) 中，將 MSAL authentication 設定為使用自訂使用者帳戶 Factory：如果應用程式使用擴充的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ，請 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 在下列程式碼中交換的自訂使用者帳戶類別：

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

上述範例適用于搭配 MSAL 使用 AAD 驗證的應用程式。 OIDC 和 API 驗證都有類似的模式。 如需詳細資訊，請參閱使用承載宣告 [自訂使用者](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) 一節中的範例。
