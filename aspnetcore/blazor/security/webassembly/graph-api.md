---
title: 搭配使用圖形 API 與 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 瞭解如何將圖形 API 與 Blazor WebAssemlby apps 搭配使用。
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
ms.openlocfilehash: b9e95afc2ccff95c6acd215a70c8b0f92fb1b8c0
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394729"
---
# <a name="use-graph-api-with-aspnet-core-blazor-webassembly"></a><span data-ttu-id="f3c34-103">搭配使用圖形 API 與 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f3c34-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f3c34-104">[Microsoft GRAPH api](/graph/use-the-api) 是一種 RESTful 的 web api，可讓 Blazor 和其他 .net Framework 應用程式存取 Microsoft 雲端服務資源。</span><span class="sxs-lookup"><span data-stu-id="f3c34-104">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="f3c34-105">Graph SDK</span><span class="sxs-lookup"><span data-stu-id="f3c34-105">Graph SDK</span></span>

<span data-ttu-id="f3c34-106">[Microsoft Graph sdk](/graph/sdks/sdks-overview) 的設計目的是要簡化可存取 Microsoft graph 的高品質、有效率且彈性的應用程式。</span><span class="sxs-lookup"><span data-stu-id="f3c34-106">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="f3c34-107">本節中的範例需要獨立或應用程式專案檔的專案檔中，下列套件的套件參考 **`Client`** ：</span><span class="sxs-lookup"><span data-stu-id="f3c34-107">The examples in this section require package references for the following packages in the project file of the standalone or **`Client`** app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="f3c34-108">本文的下列各節將使用下列公用程式類別和設定：</span><span class="sxs-lookup"><span data-stu-id="f3c34-108">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="f3c34-109">使用 Graph SDK 從元件呼叫圖形 API</span><span class="sxs-lookup"><span data-stu-id="f3c34-109">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="f3c34-110">使用 Graph SDK 自訂使用者宣告</span><span class="sxs-lookup"><span data-stu-id="f3c34-110">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="f3c34-111">在 Azure 入口網站的 AAD 區域中新增 Microsoft Graph API 範圍之後：</span><span class="sxs-lookup"><span data-stu-id="f3c34-111">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="f3c34-112">將下列 `GraphClientExtensions.cs` 類別新增至裝載解決方案的獨立應用程式或 **`Client`** 應用程式 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="f3c34-112">Add the following `GraphClientExtensions.cs` class to the standalone app or **`Client`** app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="f3c34-113"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes>在方法的屬性中，提供必要的範圍 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> `AuthenticateRequestAsync` 。</span><span class="sxs-lookup"><span data-stu-id="f3c34-113">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="f3c34-114">在下列範例中， `User.Read` 會指定範圍，以符合本文稍後章節中的範例。</span><span class="sxs-lookup"><span data-stu-id="f3c34-114">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

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
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = new[] { "{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}" }
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

<span data-ttu-id="f3c34-115">上述程式碼中的範圍預留位置 `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` 代表一或多個允許的範圍。</span><span class="sxs-lookup"><span data-stu-id="f3c34-115">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="f3c34-116">例如，針對本文的 `Scopes` `User.Read` 下列各節中的範例，將設定為一個範圍的字串陣列：</span><span class="sxs-lookup"><span data-stu-id="f3c34-116">For example, set `Scopes` to a string array of one scope for `User.Read` for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="f3c34-117">在 `Program.Main` (`Program.cs`) 中，使用擴充方法新增圖形用戶端服務和設定 `AddGraphClient` ：</span><span class="sxs-lookup"><span data-stu-id="f3c34-117">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

<span data-ttu-id="f3c34-118">上述程式碼中的範圍預留位置 `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` 代表一或多個允許的範圍。</span><span class="sxs-lookup"><span data-stu-id="f3c34-118">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="f3c34-119">例如，將範圍傳遞 `User.Read` 給 `AddGraphClient` 本文的下列各節中的範例：</span><span class="sxs-lookup"><span data-stu-id="f3c34-119">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="f3c34-120">使用 Graph SDK 從元件呼叫圖形 API</span><span class="sxs-lookup"><span data-stu-id="f3c34-120">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="f3c34-121">本節會使用本文稍早所述的 [公用程式類別 (`GraphClientExtensions.cs`) ](#graph-sdk) 。</span><span class="sxs-lookup"><span data-stu-id="f3c34-121">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="f3c34-122">下列 `GraphExample` 元件會使用插入的 `GraphServiceClient` 來取得使用者的 AAD 設定檔資料，並顯示其行動電話號碼：</span><span class="sxs-lookup"><span data-stu-id="f3c34-122">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

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

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="f3c34-123">使用 Graph SDK 自訂使用者宣告</span><span class="sxs-lookup"><span data-stu-id="f3c34-123">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="f3c34-124">本節會使用本文稍早所述的 [公用程式類別 (`GraphClientExtensions.cs`) ](#graph-sdk) 。</span><span class="sxs-lookup"><span data-stu-id="f3c34-124">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="f3c34-125">在下列範例中，應用程式會從其 AAD 使用者設定檔的行動電話號碼，建立使用者的行動電話號碼宣告。</span><span class="sxs-lookup"><span data-stu-id="f3c34-125">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="f3c34-126">應用程式必須 `User.Read` 在 AAD 中設定圖形 API 範圍。</span><span class="sxs-lookup"><span data-stu-id="f3c34-126">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="f3c34-127">在下列自訂使用者帳戶 factory 中，架構 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 代表使用者的帳戶。</span><span class="sxs-lookup"><span data-stu-id="f3c34-127">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="f3c34-128">如果應用程式需要擴充的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ，請在下列程式碼中交換的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 。</span><span class="sxs-lookup"><span data-stu-id="f3c34-128">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="f3c34-129">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="f3c34-129">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="f3c34-130">在 `Program.Main` (`Program.cs`) 中，將 MSAL authentication 設定為使用自訂使用者帳戶 Factory：如果應用程式使用擴充的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ，請 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 在下列程式碼中交換的自訂使用者帳戶類別：</span><span class="sxs-lookup"><span data-stu-id="f3c34-130">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

## <a name="named-client-with-graph-api"></a><span data-ttu-id="f3c34-131">使用圖形 API 命名用戶端</span><span class="sxs-lookup"><span data-stu-id="f3c34-131">Named client with Graph API</span></span>

<span data-ttu-id="f3c34-132">本節中的範例會使用名 <xref:System.Net.Http.HttpClient> 為 For GRAPH API 的，來取得使用者的行動電話號碼來處理通話。</span><span class="sxs-lookup"><span data-stu-id="f3c34-132">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="f3c34-133">本節中的範例需要 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 獨立或應用程式專案檔的專案檔中的套件參考 **`Client`** 。</span><span class="sxs-lookup"><span data-stu-id="f3c34-133">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or **`Client`** app's project file.</span></span>

<span data-ttu-id="f3c34-134">建立下列類別和專案設定，以使用圖形 API。</span><span class="sxs-lookup"><span data-stu-id="f3c34-134">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="f3c34-135">本文的下列各節會使用下列類別和設定：</span><span class="sxs-lookup"><span data-stu-id="f3c34-135">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="f3c34-136">從元件呼叫圖形 API</span><span class="sxs-lookup"><span data-stu-id="f3c34-136">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="f3c34-137">使用圖形 API 和命名用戶端自訂使用者宣告</span><span class="sxs-lookup"><span data-stu-id="f3c34-137">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="f3c34-138">在 Azure 入口網站的 AAD 區域中新增 Microsoft Graph API 範圍之後，請將所需的範圍提供給應用程式針對圖形 API 所設定的處理常式。</span><span class="sxs-lookup"><span data-stu-id="f3c34-138">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="f3c34-139">下列範例會設定範圍的處理常式 `User.Read` 。</span><span class="sxs-lookup"><span data-stu-id="f3c34-139">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="f3c34-140">您可以新增其他範圍。</span><span class="sxs-lookup"><span data-stu-id="f3c34-140">Additional scopes can be added.</span></span>

<span data-ttu-id="f3c34-141">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="f3c34-141">`GraphAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="f3c34-142">在 `Program.Main` (`Program.cs`) 中，設定名 <xref:System.Net.Http.HttpClient> 為 for Graph API：</span><span class="sxs-lookup"><span data-stu-id="f3c34-142">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="f3c34-143">從元件呼叫圖形 API</span><span class="sxs-lookup"><span data-stu-id="f3c34-143">Call Graph API from a component</span></span>

<span data-ttu-id="f3c34-144">本節會使用 [圖形授權訊息處理常式 (本文稍早所述 `GraphAuthorizationMessageHandler.cs` `Program.Main` 的應用程式) 和新增](#named-client-with-graph-api) 專案，以提供 <xref:System.Net.Http.HttpClient> 針對圖形 API 命名的。</span><span class="sxs-lookup"><span data-stu-id="f3c34-144">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="f3c34-145">在 Razor 元件中：</span><span class="sxs-lookup"><span data-stu-id="f3c34-145">In a Razor component:</span></span>

* <span data-ttu-id="f3c34-146">建立 <xref:System.Net.Http.HttpClient> 適用于圖形 API 的，併發出使用者設定檔資料的要求。</span><span class="sxs-lookup"><span data-stu-id="f3c34-146">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="f3c34-147">`UserInfo.cs`類別會指定具有屬性的必要使用者設定檔屬性 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> ，以及 AAD 針對這些屬性所使用的 JSON 名稱。</span><span class="sxs-lookup"><span data-stu-id="f3c34-147">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="f3c34-148">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="f3c34-148">`Pages/CallUser.razor`:</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger

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
                // Use userInfo.MobilePhone and callInfo.Message to make a call

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

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="f3c34-149">使用圖形 API 和命名用戶端自訂使用者宣告</span><span class="sxs-lookup"><span data-stu-id="f3c34-149">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="f3c34-150">本節會使用 [圖形授權訊息處理常式 (本文稍早所述 `GraphAuthorizationMessageHandler.cs` `Program.Main` 的應用程式) 和新增](#named-client-with-graph-api) 專案，以提供 <xref:System.Net.Http.HttpClient> 針對圖形 API 命名的。</span><span class="sxs-lookup"><span data-stu-id="f3c34-150">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="f3c34-151">在下列範例中，應用程式會從其 AAD 使用者設定檔的行動電話號碼，建立使用者的行動電話號碼宣告。</span><span class="sxs-lookup"><span data-stu-id="f3c34-151">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="f3c34-152">應用程式必須 `User.Read` 在 AAD 中設定圖形 API 範圍。</span><span class="sxs-lookup"><span data-stu-id="f3c34-152">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="f3c34-153">將 `UserInfo.cs` 類別新增至應用程式，並使用 AAD 針對這些屬性指定所需的使用者設定檔屬性（ <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute）和 JSON 名稱：</span><span class="sxs-lookup"><span data-stu-id="f3c34-153">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="f3c34-154">在下列自訂使用者帳戶 factory 中，架構 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 代表使用者的帳戶。</span><span class="sxs-lookup"><span data-stu-id="f3c34-154">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="f3c34-155">如果應用程式需要擴充的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ，請在下列程式碼中交換的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 。</span><span class="sxs-lookup"><span data-stu-id="f3c34-155">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="f3c34-156">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="f3c34-156">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="f3c34-157">在 `Program.Main` (`Program.cs`) 中，將 MSAL authentication 設定為使用自訂使用者帳戶 Factory：如果應用程式使用擴充的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ，請 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 在下列程式碼中交換的自訂使用者帳戶類別：</span><span class="sxs-lookup"><span data-stu-id="f3c34-157">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

<span data-ttu-id="f3c34-158">上述範例適用于搭配 MSAL 使用 AAD 驗證的應用程式。</span><span class="sxs-lookup"><span data-stu-id="f3c34-158">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="f3c34-159">OIDC 和 API 驗證都有類似的模式。</span><span class="sxs-lookup"><span data-stu-id="f3c34-159">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="f3c34-160">如需詳細資訊，請參閱使用承載宣告 [自訂使用者](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) 一節中的範例。</span><span class="sxs-lookup"><span data-stu-id="f3c34-160">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
