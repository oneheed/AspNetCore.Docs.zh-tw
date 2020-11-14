---
title: 'ASP.NET Core Blazor WebAssembly 額外的安全性案例'
author: guardrex
description: '瞭解如何設定 Blazor WebAssembly 額外的安全性案例。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: baed18df2d127b592f420aac0432e0b28f076d46
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508041"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="b8d4b-103">ASP.NET Core Blazor WebAssembly 額外的安全性案例</span><span class="sxs-lookup"><span data-stu-id="b8d4b-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="b8d4b-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8d4b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="b8d4b-105">將權杖附加至傳出要求</span><span class="sxs-lookup"><span data-stu-id="b8d4b-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="b8d4b-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 是 <xref:System.Net.Http.DelegatingHandler> 用來將存取權杖附加至外寄 <xref:System.Net.Http.HttpResponseMessage> 實例。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="b8d4b-107">您可以使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 由架構註冊的服務來取得權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="b8d4b-108">如果無法取得權杖， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 就會擲回。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="b8d4b-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 具有 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 方法，可用來將使用者流覽至身分識別提供者，以取得新的權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="b8d4b-110">為了方便起見，架構會提供 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 預先設定的應用程式基底位址作為授權的 URL。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="b8d4b-111">**只有當要求 URI 在應用程式的基底 URI 內時，才會新增存取權杖。**</span><span class="sxs-lookup"><span data-stu-id="b8d4b-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="b8d4b-112">當外寄要求 Uri 不在應用程式的基底 URI 內時，請使用 [自訂 `AuthorizationMessageHandler` 類別 ( *建議*](#custom-authorizationmessagehandler-class) [的 `AuthorizationMessageHandler`](#configure-authorizationmessagehandler)) 或設定。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class ( *recommended* )](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="b8d4b-113">除了用於伺服器 API 存取的用戶端應用程式設定之外，當用戶端和伺服器不在相同的基底位址時，伺服器 API 也必須允許跨原始來源的要求 (CORS) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="b8d4b-114">如需伺服器端 CORS 設定的詳細資訊，請參閱本文稍後的「 [跨原始資源分享 (CORS) ](#cross-origin-resource-sharing-cors) 一節。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="b8d4b-115">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="b8d4b-115">In the following example:</span></span>

* <span data-ttu-id="b8d4b-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> 將 <xref:System.Net.Http.IHttpClientFactory> 和相關服務新增至服務集合，並設定名為 <xref:System.Net.Http.HttpClient> (`ServerAPI`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="b8d4b-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 這是傳送要求時，資源 URI 的基底位址。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="b8d4b-118"><xref:System.Net.Http.IHttpClientFactory> 是由 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet 套件提供。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="b8d4b-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 這是 <xref:System.Net.Http.DelegatingHandler> 用來將存取權杖附加至傳出 <xref:System.Net.Http.HttpResponseMessage> 實例的。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="b8d4b-120">只有當要求 URI 在應用程式的基底 URI 內時，才會新增存取權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="b8d4b-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType><xref:System.Net.Http.HttpClient>使用對應于命名 () 的設定，建立並設定傳出要求的實例 <xref:System.Net.Http.HttpClient> `ServerAPI` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

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

<span data-ttu-id="b8d4b-122">針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ，預設會在應用程式的基底 URI 內要求 uri。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="b8d4b-123">因此， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> `new Uri(builder.HostEnvironment.BaseAddress)` 會 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 在從專案範本產生的應用程式中，將 () 指派給。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="b8d4b-124">設定 <xref:System.Net.Http.HttpClient> 用來利用模式提出授權的要求 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="b8d4b-125">自訂 `AuthorizationMessageHandler` 類別</span><span class="sxs-lookup"><span data-stu-id="b8d4b-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="b8d4b-126">*針對不在應用程式基底 URI 內的 Uri 提出傳出要求的用戶端應用程式，建議您在本節中使用本指南。*</span><span class="sxs-lookup"><span data-stu-id="b8d4b-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="b8d4b-127">在下列範例中，自訂類別會擴充 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 以做為的 <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="b8d4b-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 設定此處理程式，以使用存取權杖授權輸出 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="b8d4b-129">只有當至少有一個授權的 Url 是要求 URI 的基底 () 時，才會附加存取權杖 <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

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

<span data-ttu-id="b8d4b-130">在 `Program.Main` (`Program.cs`) 中， `CustomAuthorizationMessageHandler` 會註冊為範圍服務，並設定為 <xref:System.Net.Http.DelegatingHandler> <xref:System.Net.Http.HttpResponseMessage> 已命名之的傳出實例 <xref:System.Net.Http.HttpClient> ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="b8d4b-131">針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> `new Uri(builder.HostEnvironment.BaseAddress)` 預設會將 () 指派給 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="b8d4b-132">設定 <xref:System.Net.Http.HttpClient> 用來利用模式提出授權的要求 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="b8d4b-133">使用 (封裝) 來建立用戶端時 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) ，會在對 <xref:System.Net.Http.HttpClient> 伺服器 API 提出要求時，提供包含存取權杖的實例。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="b8d4b-134">如果要求 URI 是相對 URI，如下列範例 (`ExampleAPIMethod`) ，它會與 <xref:System.Net.Http.HttpClient.BaseAddress> 用戶端應用程式提出要求時結合：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="b8d4b-135">設定 `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="b8d4b-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="b8d4b-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 您可以使用方法，利用授權的 Url、範圍和傳回 URL 來設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="b8d4b-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 設定處理常式，以使用存取權杖授權輸出 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="b8d4b-138">只有當至少有一個授權的 Url 是要求 URI 的基底 () 時，才會附加存取權杖 <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="b8d4b-139">如果要求 URI 是相對 URI，則會與結合 <xref:System.Net.Http.HttpClient.BaseAddress> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="b8d4b-140">在下列範例中，會 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> 在 `Program.Main` () 中設定 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="b8d4b-141">針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 預設會指派給下列專案：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="b8d4b-142"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="b8d4b-143">陣列的 URL `authorizedUrls` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="b8d4b-144">類型 `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="b8d4b-144">Typed `HttpClient`</span></span>

<span data-ttu-id="b8d4b-145">您可以定義具型別用戶端，以處理單一類別內的所有 HTTP 和權杖取得問題。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="b8d4b-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="b8d4b-146">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="b8d4b-147">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `using static BlazorSample.Data;`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="b8d4b-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b8d4b-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="b8d4b-149">針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> `new Uri(builder.HostEnvironment.BaseAddress)` 預設會將 () 指派給 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="b8d4b-150">`FetchData` 元件 (`Pages/FetchData.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="b8d4b-151">設定 `HttpClient` 處理常式</span><span class="sxs-lookup"><span data-stu-id="b8d4b-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="b8d4b-152">您可以 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 針對輸出 HTTP 要求進一步設定處理常式。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="b8d4b-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b8d4b-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="b8d4b-154">針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 預設會指派給下列專案：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="b8d4b-155"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="b8d4b-156">陣列的 URL `authorizedUrls` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="b8d4b-157">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="b8d4b-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="b8d4b-158">如果 Blazor WebAssembly 應用程式通常使用安全的預設值 <xref:System.Net.Http.HttpClient> ，則應用程式也可以藉由設定名為的，來提出未經驗證或未經授權的 web API 要求 <xref:System.Net.Http.HttpClient> ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="b8d4b-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b8d4b-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="b8d4b-160">針對以 Blazor 託管專案範本為基礎的應用程式 Blazor WebAssembly ， <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> `new Uri(builder.HostEnvironment.BaseAddress)` 預設會將 () 指派給 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="b8d4b-161">先前的註冊是現有的安全預設註冊以外的註冊 <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="b8d4b-162">元件會 <xref:System.Net.Http.HttpClient> 從 <xref:System.Net.Http.IHttpClientFactory> (套件建立， [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)) 以提出未經驗證或未經授權的要求：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="b8d4b-163">伺服器 API 中的控制器在 `WeatherForecastNoAuthenticationController` 上述範例中，不會以 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 屬性標記。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="b8d4b-164">決定是否要使用安全用戶端或不安全的用戶端做為預設 <xref:System.Net.Http.HttpClient> 實例，取決於開發人員。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="b8d4b-165">做出這項決定的其中一種方式，是考慮應用程式所聯繫的已驗證和未驗證端點數目。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="b8d4b-166">如果應用程式的大部分要求都是保護 API 端點，請使用已驗證的 <xref:System.Net.Http.HttpClient> 實例作為預設值。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="b8d4b-167">否則，請將未驗證的 <xref:System.Net.Http.HttpClient> 實例註冊為預設值。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="b8d4b-168">使用的替代方法是建立具型別 <xref:System.Net.Http.IHttpClientFactory> [用戶端](#typed-httpclient) ，以未經驗證的匿名端點存取。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="b8d4b-169">要求其他存取權杖</span><span class="sxs-lookup"><span data-stu-id="b8d4b-169">Request additional access tokens</span></span>

<span data-ttu-id="b8d4b-170">您可以藉由呼叫來手動取得存取權杖 `IAccessTokenProvider.RequestAccessToken` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="b8d4b-171">在下列範例中，應用程式需要一個額外的範圍作為預設值 <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-171">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="b8d4b-172">Microsoft 驗證程式庫 (MSAL) 範例會使用下列內容來設定領域 `MsalProviderOptions` ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-172">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="b8d4b-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b8d4b-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="b8d4b-174">`{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` 上述範例中的和預留位置是自訂範圍。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-174">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="b8d4b-175">`IAccessTokenProvider.RequestToken`方法提供多載，可讓應用程式使用一組指定的範圍布建存取權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-175">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="b8d4b-176">在 Razor 元件中：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-176">In a Razor component:</span></span>

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

<span data-ttu-id="b8d4b-177">`{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` 上述範例中的和預留位置是自訂範圍。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-177">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="b8d4b-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> 返回：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="b8d4b-179">`true` 搭配 `token` 使用。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-179">`true` with the `token` for use.</span></span>
* <span data-ttu-id="b8d4b-180">`false` 如果未抓取權杖，則為。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-180">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="b8d4b-181">跨原始來源資源分享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="b8d4b-181">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="b8d4b-182">在 cookie cors 要求上 (授權 s/標頭) 傳送認證時， `Authorization` cors 原則必須允許標頭。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-182">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="b8d4b-183">下列原則包含的設定：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-183">The following policy includes configuration for:</span></span>

* <span data-ttu-id="b8d4b-184">要求來源 (`http://localhost:5000` ， `https://localhost:5001`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-184">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="b8d4b-185">任何方法 (動詞) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-185">Any method (verb).</span></span>
* <span data-ttu-id="b8d4b-186">`Content-Type` 和 `Authorization` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-186">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="b8d4b-187">若要允許自訂標頭 (例如， `x-custom-header`) ，請在呼叫時列出標頭 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-187">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="b8d4b-188">用戶端 JavaScript 程式碼 (`credentials` 屬性設定為) 的認證 `include` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-188">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="b8d4b-189">以裝載 Blazor 的專案範本為基礎的託管解決方案會 Blazor 針對用戶端和伺服器應用程式使用相同的基底位址。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-189">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="b8d4b-190">用戶端應用程式的 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> 預設設定為 URI `builder.HostEnvironment.BaseAddress` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-190">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="b8d4b-191">從裝載的專案範本建立之裝載應用程式的預設設定中， **不** 需要 CORS 設定 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-191">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="b8d4b-192">不是由伺服器專案所裝載且不共用伺服器應用程式基底位址的其他用戶端應用程式 **，需要伺服器** 專案中的 CORS 設定。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-192">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="b8d4b-193">如需詳細資訊，請參閱 <xref:security/cors> 和範例應用程式的 HTTP 要求測試器元件 (`Components/HTTPRequestTester.razor`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-193">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="b8d4b-194">處理權杖要求錯誤</span><span class="sxs-lookup"><span data-stu-id="b8d4b-194">Handle token request errors</span></span>

<span data-ttu-id="b8d4b-195">當單一頁面應用程式 (SPA) 使用 OpenID Connect (OIDC) 來驗證使用者時，驗證狀態會在 SPA 和 Identity 提供者 (IP) 中，以 cookie 因使用者提供其認證而設定的會話形式在本機維護。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-195">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="b8d4b-196">IP 發出給使用者的權杖通常會在短時間內有效（大約一小時），因此用戶端應用程式必須定期提取新的權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-196">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="b8d4b-197">否則，使用者將會在授與的權杖到期之後登出。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-197">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="b8d4b-198">在大多數情況下，OIDC 用戶端可以布建新權杖，而不需要使用者再次驗證，因為在 IP 內保留驗證狀態或「會話」。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-198">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="b8d4b-199">在某些情況下，用戶端無法在沒有使用者互動的情況下取得權杖，例如，基於某個原因而導致使用者明確登出 IP。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-199">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="b8d4b-200">當使用者造訪並登出時，就會發生這種情況 `https://login.microsoftonline.com` 。在這些情況下，應用程式不會立即知道使用者已登出。用戶端保存的任何權杖都可能不再有效。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-200">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="b8d4b-201">此外，當目前的權杖到期後，用戶端無法在沒有使用者互動的情況下布建新的權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-201">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="b8d4b-202">這些案例不是以權杖為基礎的驗證所特有。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-202">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="b8d4b-203">它們是 Spa 本質的一部分。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-203">They are part of the nature of SPAs.</span></span> <span data-ttu-id="b8d4b-204">如果已移除驗證，則使用的 SPA cookie 也無法呼叫伺服器 API cookie 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-204">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="b8d4b-205">當應用程式執行受保護資源的 API 呼叫時，您必須注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-205">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="b8d4b-206">若要布建新的存取權杖以呼叫 API，使用者可能需要再次進行驗證。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-206">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="b8d4b-207">即使用戶端具有看似有效的權杖，對伺服器的呼叫可能會失敗，因為使用者已撤銷權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-207">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="b8d4b-208">當應用程式要求權杖時，有兩個可能的結果：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-208">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="b8d4b-209">要求成功，且應用程式具有有效的權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-209">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="b8d4b-210">要求失敗，且應用程式必須再次驗證使用者，才能取得新的權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-210">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="b8d4b-211">當令牌要求失敗時，您必須決定是否要儲存任何目前的狀態，然後再執行重新導向。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-211">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="b8d4b-212">有幾種方法存在，並增加複雜度：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-212">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="b8d4b-213">將目前的頁面狀態儲存在會話儲存體中。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-213">Store the current page state in session storage.</span></span> <span data-ttu-id="b8d4b-214">在[ `OnInitializedAsync` 生命週期事件](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 期間，檢查是否可還原狀態後再繼續。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-214">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="b8d4b-215">新增查詢字串參數，並使用該參數做為應用程式的信號，以指示應用程式需要重新以提供先前儲存的狀態。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-215">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="b8d4b-216">新增具有唯一識別碼的查詢字串參數，以在會話儲存體中儲存資料，而不會與其他專案發生風險衝突。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-216">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="b8d4b-217">下列範例示範如何執行：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-217">The following example shows how to:</span></span>

* <span data-ttu-id="b8d4b-218">重新導向至登入頁面之前，請先保留狀態。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-218">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="b8d4b-219">使用查詢字串參數，在驗證之後復原先前的狀態。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-219">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="b8d4b-220">在驗證操作之前儲存應用程式狀態</span><span class="sxs-lookup"><span data-stu-id="b8d4b-220">Save app state before an authentication operation</span></span>

<span data-ttu-id="b8d4b-221">在驗證作業期間，某些情況下，您會想要先儲存應用程式狀態，然後再將瀏覽器重新導向至 IP。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-221">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="b8d4b-222">當您使用狀態容器，並且想要在驗證成功後還原狀態時，就會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-222">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="b8d4b-223">您可以使用自訂驗證狀態物件來保留應用程式特定狀態或其參考，並在驗證作業成功完成之後還原該狀態。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-223">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="b8d4b-224">下列範例會示範方法。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-224">The following example demonstrates the approach.</span></span>

<span data-ttu-id="b8d4b-225">在應用程式中建立狀態容器類別，其屬性可保存應用程式的狀態值。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-225">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="b8d4b-226">在下列範例中，容器是用來維護預設專案範本元件的計數器值 `Counter` (`Pages/Counter.razor`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-226">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="b8d4b-227">序列化和還原序列化容器的方法是依據 <xref:System.Text.Json> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-227">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="b8d4b-228">`Counter`元件會使用狀態容器來維護 `currentCount` 元件以外的值：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-228">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="b8d4b-229">從建立 `ApplicationAuthenticationState` <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-229">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="b8d4b-230">提供 `Id` 屬性，作為本機儲存狀態的識別碼。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-230">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="b8d4b-231">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="b8d4b-231">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="b8d4b-232">`Authentication`元件 (`Pages/Authentication.razor`) 使用具有序列化和還原序列化方法的本機會話儲存體來儲存及還原應用程式的狀態 `StateContainer` ， `GetStateForLocalStorage` 以及 `SetStateFromLocalStorage` ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-232">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="b8d4b-233">此範例使用 Azure Active Directory (AAD) 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-233">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="b8d4b-234">在 `Program.Main` (`Program.cs`) ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-234">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="b8d4b-235">`ApplicationAuthenticationState`設定為 Microsoft 驗證程式庫 (MSAL) `RemoteAuthenticationState` 類型。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-235">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="b8d4b-236">狀態容器是在服務容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-236">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="b8d4b-237">自訂應用程式路由</span><span class="sxs-lookup"><span data-stu-id="b8d4b-237">Customize app routes</span></span>

<span data-ttu-id="b8d4b-238">根據預設，連結 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 庫會使用下表所示的路由來表示不同的驗證狀態。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-238">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="b8d4b-239">路由</span><span class="sxs-lookup"><span data-stu-id="b8d4b-239">Route</span></span>                            | <span data-ttu-id="b8d4b-240">目的</span><span class="sxs-lookup"><span data-stu-id="b8d4b-240">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="b8d4b-241">觸發登入操作。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-241">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="b8d4b-242">處理任何登入作業的結果。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-242">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="b8d4b-243">當登入作業因某些原因而失敗時，會顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-243">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="b8d4b-244">觸發登出操作。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-244">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="b8d4b-245">處理登出作業的結果。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-245">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="b8d4b-246">當登出作業因某些原因而失敗時，會顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-246">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="b8d4b-247">指出使用者已成功登出。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-247">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="b8d4b-248">觸發操作以編輯使用者設定檔。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-248">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="b8d4b-249">觸發操作以註冊新的使用者。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-249">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="b8d4b-250">上表中顯示的路由可透過進行設定 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-250">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b8d4b-251">設定選項以提供自訂路由時，請確認應用程式具有可處理每個路徑的路由。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-251">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="b8d4b-252">在下列範例中，所有路徑都會加上前置詞 `/security` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-252">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="b8d4b-253">`Authentication` 元件 (`Pages/Authentication.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-253">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="b8d4b-254">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="b8d4b-254">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="b8d4b-255">如果要求需要完全不同的路徑，請如先前所述設定路由，並 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 使用明確的動作參數來轉譯：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-255">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="b8d4b-256">如果您選擇這麼做，就可以將 UI 分成不同的頁面。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-256">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="b8d4b-257">自訂驗證使用者介面</span><span class="sxs-lookup"><span data-stu-id="b8d4b-257">Customize the authentication user interface</span></span>

<span data-ttu-id="b8d4b-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 針對每個驗證狀態包含一組預設的 UI 片段。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="b8d4b-259">您可以藉由傳入自訂來自訂每個狀態 <xref:Microsoft.AspNetCore.Components.RenderFragment> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-259">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="b8d4b-260">若要在初始登入程式期間自訂所顯示的文字，可以變更，如下所示 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-260">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="b8d4b-261">`Authentication` 元件 (`Pages/Authentication.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-261">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="b8d4b-262"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>有一個片段可用於下表所示的每個驗證路由。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-262">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="b8d4b-263">路由</span><span class="sxs-lookup"><span data-stu-id="b8d4b-263">Route</span></span>                            | <span data-ttu-id="b8d4b-264">片段</span><span class="sxs-lookup"><span data-stu-id="b8d4b-264">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="b8d4b-265">自訂使用者</span><span class="sxs-lookup"><span data-stu-id="b8d4b-265">Customize the user</span></span>

<span data-ttu-id="b8d4b-266">可以自訂系結至應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-266">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="b8d4b-267">使用承載宣告自訂使用者</span><span class="sxs-lookup"><span data-stu-id="b8d4b-267">Customize the user with a payload claim</span></span>

<span data-ttu-id="b8d4b-268">在下列範例中，應用程式的已驗證使用者會收到 `amr` 每個使用者驗證方法的宣告。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-268">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="b8d4b-269">`amr`宣告會識別如何在 Microsoft Platform v1.0 承載宣告中驗證權杖的主體 Identity 。 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim)</span><span class="sxs-lookup"><span data-stu-id="b8d4b-269">The `amr` claim identifies how the subject of the token was authenticated in Microsoft Identity Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="b8d4b-270">此範例使用根據的自訂使用者帳戶類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-270">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="b8d4b-271">建立擴充類別的類別 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-271">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="b8d4b-272">下列範例會將 `AuthenticationMethod` 屬性設定為使用者的 `amr` JSON 屬性值陣列。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-272">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="b8d4b-273">`AuthenticationMethod` 當使用者經過驗證時，架構會自動填入。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-273">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="b8d4b-274">建立可延伸的 factory， <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> 以從儲存在中的使用者驗證方法建立宣告 `CustomUserAccount.AuthenticationMethod` ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-274">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

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

<span data-ttu-id="b8d4b-275">`CustomAccountFactory`為使用中的驗證提供者註冊。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-275">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="b8d4b-276">下列任何一個註冊都有效：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-276">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="b8d4b-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="b8d4b-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="b8d4b-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="b8d4b-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="b8d4b-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="b8d4b-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="b8d4b-280">具有自訂使用者帳戶類別的 AAD 安全性群組和角色</span><span class="sxs-lookup"><span data-stu-id="b8d4b-280">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="b8d4b-281">如需適用于 AAD 安全性群組和 AAD 系統管理員角色和自訂使用者帳戶類別的其他範例，請參閱 <xref:blazor/security/webassembly/aad-groups-roles> 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-281">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="b8d4b-282">支援使用驗證進行預進行</span><span class="sxs-lookup"><span data-stu-id="b8d4b-282">Support prerendering with authentication</span></span>

<span data-ttu-id="b8d4b-283">遵循其中一個託管應用程式主題中的指引之後 Blazor WebAssembly ，請使用下列指示來建立應用程式：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-283">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="b8d4b-284">不需要授權的 Prerenders 路徑。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-284">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="b8d4b-285">不需要授權的呈現路徑。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-285">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="b8d4b-286">在 *`Client`* 應用程式的 `Program` 類別 (`Program.cs`) 中，將常見的服務註冊納入不同的方法 (例如 `ConfigureCommonServices`) ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-286">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="b8d4b-287">在伺服器應用程式的中 `Startup.ConfigureServices` ，註冊下列其他服務：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-287">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="b8d4b-288">在伺服器應用程式的 `Startup.Configure` 方法中，將取代 [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) 為 [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-288">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="b8d4b-289">在伺服器應用程式中， `Pages` 如果資料夾不存在，請加以建立。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-289">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="b8d4b-290">在 `_Host.cshtml` 伺服器應用程式的資料夾內建立頁面 `Pages` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-290">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="b8d4b-291">將應用程式檔案中的內容貼到檔案中 *`Client`* `wwwroot/index.html` `Pages/_Host.cshtml` 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-291">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="b8d4b-292">更新檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-292">Update the file's contents:</span></span>

* <span data-ttu-id="b8d4b-293">將 `@page "_Host"` 新增到檔案的頂端。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-293">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="b8d4b-294">`<app>Loading...</app>`以下列內容取代標記：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-294">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="b8d4b-295">託管應用程式和協力廠商登入提供者的選項</span><span class="sxs-lookup"><span data-stu-id="b8d4b-295">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="b8d4b-296">Blazor WebAssembly使用協力廠商提供者驗證及授權託管應用程式時，有數個選項可用來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-296">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="b8d4b-297">您選擇哪一個取決於您的案例。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-297">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="b8d4b-298">如需詳細資訊，請參閱<xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-298">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="b8d4b-299">驗證使用者只呼叫受保護的協力廠商 Api</span><span class="sxs-lookup"><span data-stu-id="b8d4b-299">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="b8d4b-300">使用用戶端的 OAuth 流程，向協力廠商 API 提供者驗證使用者：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-300">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="b8d4b-301">在此情節中：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-301">In this scenario:</span></span>

* <span data-ttu-id="b8d4b-302">裝載應用程式的伺服器不會扮演角色。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-302">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="b8d4b-303">無法保護伺服器上的 Api。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-303">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="b8d4b-304">應用程式只能呼叫受保護的協力廠商 Api。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-304">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="b8d4b-305">使用協力廠商提供者驗證使用者，並在主機伺服器和協力廠商呼叫受保護的 Api</span><span class="sxs-lookup"><span data-stu-id="b8d4b-305">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="b8d4b-306">Identity使用協力廠商登入提供者進行設定。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-306">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="b8d4b-307">取得協力廠商 API 存取所需的權杖，並加以儲存。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-307">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="b8d4b-308">當使用者登入時，會在 Identity 驗證過程中收集存取和重新整理權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-308">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="b8d4b-309">屆時，有幾種方法可以對協力廠商 Api 進行 API 呼叫。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-309">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="b8d4b-310">使用伺服器存取權杖來取出協力廠商存取權杖</span><span class="sxs-lookup"><span data-stu-id="b8d4b-310">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="b8d4b-311">使用伺服器上產生的存取權杖，從伺服器 API 端點取出協力廠商存取權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-311">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="b8d4b-312">從該處，使用協力廠商存取權杖，直接從用戶端呼叫協力廠商 API 資源 Identity 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-312">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="b8d4b-313">我們不建議採用此方法。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-313">We don't recommend this approach.</span></span> <span data-ttu-id="b8d4b-314">這種方法需要將協力廠商存取權杖視為針對公用用戶端產生的權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-314">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="b8d4b-315">在 OAuth 詞彙中，公用應用程式沒有用戶端密碼，因為它無法受信任以安全地儲存秘密，而且會為機密用戶端產生存取權杖。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-315">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="b8d4b-316">機密用戶端是具有用戶端密碼的用戶端，並假設能夠安全地儲存秘密。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-316">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="b8d4b-317">協力廠商存取權杖可能會被授與其他範圍，以根據協力廠商針對較受信任的用戶端發出權杖的事實來執行敏感性作業。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-317">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="b8d4b-318">同樣地，重新整理權杖不應發給不受信任的用戶端，因為這樣做會讓用戶端無限制存取，除非有其他限制。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-318">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="b8d4b-319">從用戶端對伺服器 API 進行 API 呼叫，以便呼叫協力廠商 Api</span><span class="sxs-lookup"><span data-stu-id="b8d4b-319">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="b8d4b-320">從用戶端對伺服器 API 進行 API 呼叫。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-320">Make an API call from the client to the server API.</span></span> <span data-ttu-id="b8d4b-321">從伺服器取得協力廠商 API 資源的存取權杖，併發出需要的任何呼叫。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-321">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="b8d4b-322">雖然此方法需要透過伺服器的額外網路躍點來呼叫協力廠商 API，但最終會產生更安全的體驗：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-322">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="b8d4b-323">伺服器可以儲存重新整理權杖，並確保應用程式不會失去協力廠商資源的存取權。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-323">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="b8d4b-324">應用程式無法從伺服器洩漏存取權杖，可能包含更多敏感性許可權。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-324">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="b8d4b-325">使用 OpenID Connect (OIDC) v2.0 端點</span><span class="sxs-lookup"><span data-stu-id="b8d4b-325">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="b8d4b-326">驗證程式庫和 Blazor 專案範本使用 OpenID Connect (OIDC) v1.0 端點。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-326">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="b8d4b-327">若要使用 v2.0 端點，請設定 JWT 持有人 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 選項。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-327">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="b8d4b-328">在下列範例中，會將區段附加至屬性，以針對 v2.0 設定 AAD `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-328">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="b8d4b-329">或者，您可以在應用程式設定 () 檔中進行設定 `appsettings.json` ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-329">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="b8d4b-330">如果追蹤于區段上的授權不適合應用程式的 OIDC 提供者（例如，使用非 AAD 提供者），請 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 直接設定屬性。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-330">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="b8d4b-331">您可以在 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 應用程式佈建檔案中設定或在應用程式佈建檔案中設定屬性 (`appsettings.json`) 的 `Authority` 金鑰。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-331">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="b8d4b-332">V2.0 端點的識別碼權杖中宣告的清單會變更。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-332">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="b8d4b-333">如需詳細資訊，請參閱 [為何要更新至 Microsoft 身分識別平臺 (v2.0) ？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-333">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="b8d4b-334">在元件中設定和使用 gRPC</span><span class="sxs-lookup"><span data-stu-id="b8d4b-334">Configure and use gRPC in components</span></span>

<span data-ttu-id="b8d4b-335">若要將 Blazor WebAssembly 應用程式設定為使用 [ASP.NET Core gRPC 架構](xref:grpc/index)：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-335">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="b8d4b-336">在伺服器上啟用 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-336">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="b8d4b-337">如需詳細資訊，請參閱<xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-337">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="b8d4b-338">註冊應用程式訊息處理常式的 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-338">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="b8d4b-339">下列範例會設定應用程式的授權訊息處理常式，以使用[ `GreeterClient` gRPC 教學](xref:tutorials/grpc/grpc-start#create-a-grpc-service)課程 () 中的服務 `Program.Main` ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-339">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

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

<span data-ttu-id="b8d4b-340">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-340">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="b8d4b-341">將檔案放 `.proto` 在 `Shared` 主控方案的專案中 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-341">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="b8d4b-342">用戶端應用程式中的元件可以使用 gRPC 用戶端 () 來進行 gRPC 呼叫 `Pages/Grpc.razor` ：</span><span class="sxs-lookup"><span data-stu-id="b8d4b-342">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

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

<span data-ttu-id="b8d4b-343">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample`) 。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-343">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="b8d4b-344">若要使用 `Status.DebugException` 屬性，請使用 [Grpc .Net. Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-344">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="b8d4b-345">如需詳細資訊，請參閱<xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="b8d4b-345">For more information, see <xref:grpc/browser>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8d4b-346">其他資源</span><span class="sxs-lookup"><span data-stu-id="b8d4b-346">Additional resources</span></span>

* <xref:blazor/security/webassembly/graph-api>
* [<span data-ttu-id="b8d4b-347">`HttpClient` 以及 `HttpRequestMessage` 使用 FETCH API 要求選項</span><span class="sxs-lookup"><span data-stu-id="b8d4b-347">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
