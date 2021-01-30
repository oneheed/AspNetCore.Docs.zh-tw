---
title: 在 ASP.NET Core 中使用 IHttpClientFactory 發出 HTTP 要求
author: stevejgordon
description: 深入了解在 ASP.NET Core 中使用 IHttpClientFactory 介面來管理邏輯 HttpClient 執行個體。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057326"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="574a1-103">在 ASP.NET Core 中使用 IHttpClientFactory 發出 HTTP 要求</span><span class="sxs-lookup"><span data-stu-id="574a1-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="574a1-104">[Kirk Larkin](https://github.com/serpent5)、 [Steve Gordon](https://github.com/stevejgordon)、 [Glenn Condron](https://github.com/glennc)和[Ryan Nowak](https://github.com/rynowak)。</span><span class="sxs-lookup"><span data-stu-id="574a1-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="574a1-105"><xref:System.Net.Http.IHttpClientFactory> 可以註冊及用來在應用程式中設定和建立 <xref:System.Net.Http.HttpClient> 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="574a1-106">`IHttpClientFactory` 提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="574a1-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="574a1-107">提供一個集中位置以便命名和設定邏輯 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="574a1-108">例如，名為  *github* 的用戶端可以註冊並設定為存取 [github](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="574a1-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="574a1-109">您可以註冊預設用戶端以進行一般存取。</span><span class="sxs-lookup"><span data-stu-id="574a1-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="574a1-110">在中委派處理常式，以會制訂傳出中介軟體的概念 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="574a1-111">提供 Polly 型中介軟體的擴充功能，以利用中的委派處理常式 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="574a1-112">管理基礎實例的共用和存留期 `HttpClientMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="574a1-113">自動管理可避免在手動管理存留期時發生的常見 DNS (網域名稱系統) 問題 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="574a1-114">針對透過處理站所建立之用戶端傳送的所有要求，新增可設定的記錄體驗 (透過 `ILogger`)。</span><span class="sxs-lookup"><span data-stu-id="574a1-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="574a1-115">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="574a1-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="574a1-116">本主題的範例程式碼會用 <xref:System.Text.Json> 來還原序列化 HTTP 回應中傳回的 JSON 內容。</span><span class="sxs-lookup"><span data-stu-id="574a1-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="574a1-117">如需使用和的範例 `Json.NET` `ReadAsAsync<T>` ，請使用版本選擇器選取此主題的2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="574a1-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="574a1-118">耗用量模式</span><span class="sxs-lookup"><span data-stu-id="574a1-118">Consumption patterns</span></span>

<span data-ttu-id="574a1-119">有數種方式可將 `IHttpClientFactory` 用於應用程式：</span><span class="sxs-lookup"><span data-stu-id="574a1-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="574a1-120">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="574a1-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="574a1-121">具名用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="574a1-122">具型別用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="574a1-123">產生的用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="574a1-124">最佳方法取決於應用程式的需求。</span><span class="sxs-lookup"><span data-stu-id="574a1-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="574a1-125">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="574a1-125">Basic usage</span></span>

<span data-ttu-id="574a1-126">`IHttpClientFactory` 可以透過呼叫來註冊 `AddHttpClient` ：</span><span class="sxs-lookup"><span data-stu-id="574a1-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="574a1-127">`IHttpClientFactory`可以使用相依性[插入 (DI) ](xref:fundamentals/dependency-injection)來要求。</span><span class="sxs-lookup"><span data-stu-id="574a1-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="574a1-128">下列程式碼會使用 `IHttpClientFactory` 來建立 `HttpClient` 實例：</span><span class="sxs-lookup"><span data-stu-id="574a1-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="574a1-129">在 `IHttpClientFactory` 上述範例中使用 like，是重構現有應用程式的好方法。</span><span class="sxs-lookup"><span data-stu-id="574a1-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="574a1-130">它不會影響使用方式 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="574a1-131">在 `HttpClient` 現有應用程式中建立實例的位置，將這些發生的呼叫取代為的呼叫 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 。</span><span class="sxs-lookup"><span data-stu-id="574a1-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="574a1-132">具名用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-132">Named clients</span></span>

<span data-ttu-id="574a1-133">在下列情況中，命名用戶端是不錯的選擇：</span><span class="sxs-lookup"><span data-stu-id="574a1-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="574a1-134">應用程式需要許多不同的用途 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="574a1-135">許多 `HttpClient` 具有不同的設定。</span><span class="sxs-lookup"><span data-stu-id="574a1-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="574a1-136">`HttpClient`在註冊期間，可以指定命名的設定 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="574a1-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="574a1-137">在上述程式碼中，用戶端會以下列方式設定：</span><span class="sxs-lookup"><span data-stu-id="574a1-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="574a1-138">基底位址 `https://api.github.com/` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="574a1-139">使用 GitHub API 時需要兩個標頭。</span><span class="sxs-lookup"><span data-stu-id="574a1-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="574a1-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="574a1-140">CreateClient</span></span>

<span data-ttu-id="574a1-141">每次 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 呼叫時：</span><span class="sxs-lookup"><span data-stu-id="574a1-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="574a1-142">建立新的實例 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="574a1-143">呼叫設定動作。</span><span class="sxs-lookup"><span data-stu-id="574a1-143">The configuration action is called.</span></span>

<span data-ttu-id="574a1-144">若要建立命名用戶端，請將其名稱傳遞至 `CreateClient` ：</span><span class="sxs-lookup"><span data-stu-id="574a1-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="574a1-145">在上述程式碼中，要求不需要指定主機名稱。</span><span class="sxs-lookup"><span data-stu-id="574a1-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="574a1-146">由於使用為用戶端設定的基底位址，程式碼只可以傳遞路徑。</span><span class="sxs-lookup"><span data-stu-id="574a1-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="574a1-147">具型別用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-147">Typed clients</span></span>

<span data-ttu-id="574a1-148">具型別用戶端：</span><span class="sxs-lookup"><span data-stu-id="574a1-148">Typed clients:</span></span>

* <span data-ttu-id="574a1-149">提供與具名用戶端相同的功能，而不需使用字串作為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="574a1-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="574a1-150">取用用戶端時提供 IntelliSense 和編譯器說明。</span><span class="sxs-lookup"><span data-stu-id="574a1-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="574a1-151">提供單一位置來設定特定的 `HttpClient` 並與其互動。</span><span class="sxs-lookup"><span data-stu-id="574a1-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="574a1-152">例如，可能會使用單一類型的用戶端：</span><span class="sxs-lookup"><span data-stu-id="574a1-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="574a1-153">適用于單一後端端點。</span><span class="sxs-lookup"><span data-stu-id="574a1-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="574a1-154">封裝處理端點的所有邏輯。</span><span class="sxs-lookup"><span data-stu-id="574a1-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="574a1-155">使用 DI，並可在應用程式需要的位置插入。</span><span class="sxs-lookup"><span data-stu-id="574a1-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="574a1-156">具型別用戶端會 `HttpClient` 在其函式中接受參數：</span><span class="sxs-lookup"><span data-stu-id="574a1-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="574a1-157">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="574a1-157">In the preceding code:</span></span>

* <span data-ttu-id="574a1-158">設定會移至具類型的用戶端。</span><span class="sxs-lookup"><span data-stu-id="574a1-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="574a1-159">`HttpClient` 物件會公開為公用屬性。</span><span class="sxs-lookup"><span data-stu-id="574a1-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="574a1-160">您可以建立可公開功能的 API 特定方法 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="574a1-161">例如，方法會 `GetAspNetDocsIssues` 封裝程式碼以取出開啟的問題。</span><span class="sxs-lookup"><span data-stu-id="574a1-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="574a1-162">下列程式碼會 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 在中呼叫 `Startup.ConfigureServices` 以註冊具類型的用戶端類別：</span><span class="sxs-lookup"><span data-stu-id="574a1-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="574a1-163">具型別用戶端會向 DI 註冊為暫時性。</span><span class="sxs-lookup"><span data-stu-id="574a1-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="574a1-164">在上述程式碼中，將 `AddHttpClient` 註冊 `GitHubService` 為暫時性服務。</span><span class="sxs-lookup"><span data-stu-id="574a1-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="574a1-165">此註冊會使用 factory 方法來：</span><span class="sxs-lookup"><span data-stu-id="574a1-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="574a1-166">建立 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="574a1-167">建立的實例 `GitHubService` ，並將的實例傳遞至它的函式 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="574a1-168">具型別用戶端可以直接插入並使用：</span><span class="sxs-lookup"><span data-stu-id="574a1-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="574a1-169">您可以在註冊期間指定具型別用戶端的設定 `Startup.ConfigureServices` ，而不是在具型別用戶端的函式中指定：</span><span class="sxs-lookup"><span data-stu-id="574a1-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="574a1-170">`HttpClient`可以封裝在具類型的用戶端中。</span><span class="sxs-lookup"><span data-stu-id="574a1-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="574a1-171">您可以定義在內部呼叫實例的方法，而不是將它公開為屬性 `HttpClient` ：</span><span class="sxs-lookup"><span data-stu-id="574a1-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="574a1-172">在上述程式碼中， `HttpClient` 會儲存在私用欄位中。</span><span class="sxs-lookup"><span data-stu-id="574a1-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="574a1-173">的存取 `HttpClient` 是由公用方法所存取 `GetRepos` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="574a1-174">產生的用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-174">Generated clients</span></span>

<span data-ttu-id="574a1-175">`IHttpClientFactory`可以搭配協力廠商程式庫（例如重新[調整）使用。](https://github.com/paulcbetts/refit)</span><span class="sxs-lookup"><span data-stu-id="574a1-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="574a1-176">Refit 是適用於 .NET 的 REST 程式庫。</span><span class="sxs-lookup"><span data-stu-id="574a1-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="574a1-177">它將 REST API 轉換為即時介面。</span><span class="sxs-lookup"><span data-stu-id="574a1-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="574a1-178">介面的實作由 `RestService` 動態產生，並使用 `HttpClient` 進行外部 HTTP 呼叫。</span><span class="sxs-lookup"><span data-stu-id="574a1-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="574a1-179">定義介面及回覆來代表外部 API 和其回應：</span><span class="sxs-lookup"><span data-stu-id="574a1-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="574a1-180">可以新增具型別用戶端，使用 Refit 產生實作：</span><span class="sxs-lookup"><span data-stu-id="574a1-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="574a1-181">定義的介面可在需要時使用，並搭配 DI 與 Refit 所提供的實作：</span><span class="sxs-lookup"><span data-stu-id="574a1-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="574a1-182">提出 POST、PUT 和 DELETE 要求</span><span class="sxs-lookup"><span data-stu-id="574a1-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="574a1-183">在上述範例中，所有 HTTP 要求都會使用 GET HTTP 指令動詞。</span><span class="sxs-lookup"><span data-stu-id="574a1-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="574a1-184">`HttpClient` 也支援其他 HTTP 動詞命令，包括：</span><span class="sxs-lookup"><span data-stu-id="574a1-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="574a1-185">POST</span><span class="sxs-lookup"><span data-stu-id="574a1-185">POST</span></span>
* <span data-ttu-id="574a1-186">PUT</span><span class="sxs-lookup"><span data-stu-id="574a1-186">PUT</span></span>
* <span data-ttu-id="574a1-187">DELETE</span><span class="sxs-lookup"><span data-stu-id="574a1-187">DELETE</span></span>
* <span data-ttu-id="574a1-188">PATCH</span><span class="sxs-lookup"><span data-stu-id="574a1-188">PATCH</span></span>

<span data-ttu-id="574a1-189">如需支援之 HTTP 動詞命令的完整清單，請參閱 <xref:System.Net.Http.HttpMethod> 。</span><span class="sxs-lookup"><span data-stu-id="574a1-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="574a1-190">下列範例顯示如何提出 HTTP POST 要求：</span><span class="sxs-lookup"><span data-stu-id="574a1-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="574a1-191">在上述程式碼中， `CreateItemAsync` 方法如下：</span><span class="sxs-lookup"><span data-stu-id="574a1-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="574a1-192">使用將 `TodoItem` 參數序列化為 JSON `System.Text.Json` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="574a1-193">這會使用的實例 <xref:System.Text.Json.JsonSerializerOptions> 來設定序列化進程。</span><span class="sxs-lookup"><span data-stu-id="574a1-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="574a1-194">建立的實例 <xref:System.Net.Http.StringContent> ，以封裝要在 HTTP 要求主體中傳送的序列化 JSON。</span><span class="sxs-lookup"><span data-stu-id="574a1-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="574a1-195">呼叫 <xref:System.Net.Http.HttpClient.PostAsync%2A> 以將 JSON 內容傳送至指定的 URL。</span><span class="sxs-lookup"><span data-stu-id="574a1-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="574a1-196">這是新增至 [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)的相對 URL。</span><span class="sxs-lookup"><span data-stu-id="574a1-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="574a1-197"><xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>如果回應狀態碼未指出成功，則呼叫會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="574a1-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="574a1-198">`HttpClient` 也支援其他類型的內容。</span><span class="sxs-lookup"><span data-stu-id="574a1-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="574a1-199">例如，<xref:System.Net.Http.MultipartContent> 與 <xref:System.Net.Http.StreamContent>。</span><span class="sxs-lookup"><span data-stu-id="574a1-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="574a1-200">如需支援之內容的完整清單，請參閱 <xref:System.Net.Http.HttpContent> 。</span><span class="sxs-lookup"><span data-stu-id="574a1-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="574a1-201">下列範例顯示 HTTP PUT 要求：</span><span class="sxs-lookup"><span data-stu-id="574a1-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="574a1-202">上述程式碼與 POST 範例非常類似。</span><span class="sxs-lookup"><span data-stu-id="574a1-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="574a1-203">`SaveItemAsync`方法會呼叫， <xref:System.Net.Http.HttpClient.PutAsync%2A> 而不是 `PostAsync` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="574a1-204">下列範例顯示 HTTP DELETE 要求：</span><span class="sxs-lookup"><span data-stu-id="574a1-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="574a1-205">在上述程式碼中， `DeleteItemAsync` 方法會呼叫 <xref:System.Net.Http.HttpClient.DeleteAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="574a1-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="574a1-206">因為 HTTP 刪除要求通常不包含任何主體，所以 `DeleteAsync` 方法不會提供接受實例的多載 `HttpContent` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="574a1-207">若要深入瞭解如何搭配使用不同的 HTTP 動詞命令 `HttpClient` ，請參閱 <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="574a1-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="574a1-208">外寄要求中介軟體</span><span class="sxs-lookup"><span data-stu-id="574a1-208">Outgoing request middleware</span></span>

<span data-ttu-id="574a1-209">`HttpClient` 具有委派處理常式的概念，這些處理常式可以連結在一起以用於傳出的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="574a1-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="574a1-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="574a1-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="574a1-211">簡化定義要套用至每個已命名用戶端的處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="574a1-212">支援多個處理常式的註冊和連結，以建立外寄要求中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="574a1-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="574a1-213">這些處理常式都可以在傳出要求前後執行工作。</span><span class="sxs-lookup"><span data-stu-id="574a1-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="574a1-214">此模式：</span><span class="sxs-lookup"><span data-stu-id="574a1-214">This pattern:</span></span>
  
    * <span data-ttu-id="574a1-215">類似于 ASP.NET Core 中的輸入中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="574a1-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="574a1-216">提供一種機制來管理 HTTP 要求的跨領域考慮，例如：</span><span class="sxs-lookup"><span data-stu-id="574a1-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="574a1-217">快取</span><span class="sxs-lookup"><span data-stu-id="574a1-217">caching</span></span>
      * <span data-ttu-id="574a1-218">錯誤處理</span><span class="sxs-lookup"><span data-stu-id="574a1-218">error handling</span></span>
      * <span data-ttu-id="574a1-219">序列化</span><span class="sxs-lookup"><span data-stu-id="574a1-219">serialization</span></span>
      * <span data-ttu-id="574a1-220">logging</span><span class="sxs-lookup"><span data-stu-id="574a1-220">logging</span></span>

<span data-ttu-id="574a1-221">若要建立委派處理常式：</span><span class="sxs-lookup"><span data-stu-id="574a1-221">To create a delegating handler:</span></span>

* <span data-ttu-id="574a1-222">衍生自 <xref:System.Net.Http.DelegatingHandler> 。</span><span class="sxs-lookup"><span data-stu-id="574a1-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="574a1-223">覆寫 <xref:System.Net.Http.DelegatingHandler.SendAsync*>。</span><span class="sxs-lookup"><span data-stu-id="574a1-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="574a1-224">將要求傳遞至管線中的下一個處理常式之前，先執行程式碼：</span><span class="sxs-lookup"><span data-stu-id="574a1-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="574a1-225">上述 `X-API-KEY` 程式碼會檢查標頭是否在要求中。</span><span class="sxs-lookup"><span data-stu-id="574a1-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="574a1-226">如果 `X-API-KEY` 遺失， <xref:System.Net.HttpStatusCode.BadRequest> 則會傳回。</span><span class="sxs-lookup"><span data-stu-id="574a1-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="574a1-227">您可以使用下列方式，將一個以上的處理常式新增至的設定 `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> ：</span><span class="sxs-lookup"><span data-stu-id="574a1-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="574a1-228">在上述程式碼，`ValidateHeaderHandler` 已向 DI 註冊。</span><span class="sxs-lookup"><span data-stu-id="574a1-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="574a1-229">註冊之後，便可以呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，並傳入處理常式的類型。</span><span class="sxs-lookup"><span data-stu-id="574a1-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="574a1-230">可以遵循應該執行的順序來註冊多個處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="574a1-231">每個處理常式會包裝下一個處理常式，直到最終 `HttpClientHandler` 執行要求：</span><span class="sxs-lookup"><span data-stu-id="574a1-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="574a1-232">在傳出要求中介軟體中使用 DI</span><span class="sxs-lookup"><span data-stu-id="574a1-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="574a1-233">當 `IHttpClientFactory` 建立新的委派處理常式時，它會使用 DI 來完成處理常式的函式參數。</span><span class="sxs-lookup"><span data-stu-id="574a1-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="574a1-234">`IHttpClientFactory` 為每個處理常式建立 **個別** 的 DI 範圍，當處理常式取用 *範圍* 服務時，可能會導致令人驚訝的行為。</span><span class="sxs-lookup"><span data-stu-id="574a1-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="574a1-235">例如，請考慮下列介面及其實作為作業（具有識別碼） `OperationId` ：</span><span class="sxs-lookup"><span data-stu-id="574a1-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="574a1-236">如其名所示， `IOperationScoped` 使用 *範圍* 存留期在 DI 註冊：</span><span class="sxs-lookup"><span data-stu-id="574a1-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="574a1-237">下列委派處理常式會使用和使用 `IOperationScoped` 來設定 `X-OPERATION-ID` 傳出要求的標頭：</span><span class="sxs-lookup"><span data-stu-id="574a1-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="574a1-238">在 [ [ `HttpRequestsSample` 下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] 中，流覽 `/Operation` 並重新整理頁面。</span><span class="sxs-lookup"><span data-stu-id="574a1-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="574a1-239">每個要求的要求範圍值都有所變更，但處理常式範圍值只會每隔5秒變更一次。</span><span class="sxs-lookup"><span data-stu-id="574a1-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="574a1-240">處理常式可以視任何範圍的服務而定。</span><span class="sxs-lookup"><span data-stu-id="574a1-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="574a1-241">處置處理常式時，會處置處理常式所相依的服務。</span><span class="sxs-lookup"><span data-stu-id="574a1-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="574a1-242">使用下列其中一種方式來與訊息處理常式共用個別要求狀態：</span><span class="sxs-lookup"><span data-stu-id="574a1-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="574a1-243">使用 [HttpRequestMessage](xref:System.Net.Http.HttpRequestMessage.Properties)將資料傳遞到處理程式。</span><span class="sxs-lookup"><span data-stu-id="574a1-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="574a1-244">使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 來存取目前的要求。</span><span class="sxs-lookup"><span data-stu-id="574a1-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="574a1-245">建立自訂 <xref:System.Threading.AsyncLocal`1> 儲存體物件以傳遞資料。</span><span class="sxs-lookup"><span data-stu-id="574a1-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="574a1-246">使用 Polly 為基礎的處理常式</span><span class="sxs-lookup"><span data-stu-id="574a1-246">Use Polly-based handlers</span></span>

<span data-ttu-id="574a1-247">`IHttpClientFactory` 整合協力廠商程式庫 [Polly](https://github.com/App-vNext/Polly)。</span><span class="sxs-lookup"><span data-stu-id="574a1-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="574a1-248">Polly 是適用於 .NET 的完整恢復功能和暫時性錯誤處理程式庫。</span><span class="sxs-lookup"><span data-stu-id="574a1-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="574a1-249">它可讓開發人員以流暢且執行緒安全的方式表達原則，例如重試、斷路器、逾時、艙隔離與後援。</span><span class="sxs-lookup"><span data-stu-id="574a1-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="574a1-250">提供擴充方法來啟用使用 Polly 原則搭配設定的 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="574a1-251">Polly 延伸模組支援將以 Polly 為基礎的處理常式新增至用戶端。</span><span class="sxs-lookup"><span data-stu-id="574a1-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="574a1-252">Polly 需要 [Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="574a1-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="574a1-253">處理暫時性錯誤</span><span class="sxs-lookup"><span data-stu-id="574a1-253">Handle transient faults</span></span>

<span data-ttu-id="574a1-254">當外部 HTTP 呼叫為暫時性時，通常會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="574a1-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="574a1-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 允許定義原則來處理暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="574a1-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="574a1-256">使用設定的原則會 `AddTransientHttpErrorPolicy` 處理下列回應：</span><span class="sxs-lookup"><span data-stu-id="574a1-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="574a1-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="574a1-257">HTTP 5xx</span></span>
* <span data-ttu-id="574a1-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="574a1-258">HTTP 408</span></span>

<span data-ttu-id="574a1-259">`AddTransientHttpErrorPolicy` 提供物件的存取權，此 `PolicyBuilder` 物件設定為處理代表可能暫時性錯誤的錯誤：</span><span class="sxs-lookup"><span data-stu-id="574a1-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="574a1-260">在上述程式碼，已定義了 `WaitAndRetryAsync` 原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="574a1-261">失敗的要求會重試最多三次，並且在嘗試之間會有 600 毫秒的延遲時間。</span><span class="sxs-lookup"><span data-stu-id="574a1-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="574a1-262">動態選取原則</span><span class="sxs-lookup"><span data-stu-id="574a1-262">Dynamically select policies</span></span>

<span data-ttu-id="574a1-263">提供擴充方法來新增以 Polly 為基礎的處理常式，例如 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> 。</span><span class="sxs-lookup"><span data-stu-id="574a1-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="574a1-264">下列多載會 `AddPolicyHandler` 檢查要求，以決定要套用的原則：</span><span class="sxs-lookup"><span data-stu-id="574a1-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="574a1-265">在上述程式碼中，如果外寄要求是 HTTP GET，就會套用 10 秒逾時。</span><span class="sxs-lookup"><span data-stu-id="574a1-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="574a1-266">任何其他 HTTP 方法會使用 30 秒逾時。</span><span class="sxs-lookup"><span data-stu-id="574a1-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="574a1-267">新增多個 Polly 處理常式</span><span class="sxs-lookup"><span data-stu-id="574a1-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="574a1-268">嵌套 Polly 原則是很常見的：</span><span class="sxs-lookup"><span data-stu-id="574a1-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="574a1-269">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="574a1-269">In the preceding example:</span></span>

* <span data-ttu-id="574a1-270">會加入兩個處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-270">Two handlers are added.</span></span>
* <span data-ttu-id="574a1-271">第一個處理常式會使用 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 來新增重試原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="574a1-272">失敗的要求會重試最多三次。</span><span class="sxs-lookup"><span data-stu-id="574a1-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="574a1-273">第二次 `AddTransientHttpErrorPolicy` 呼叫會加入斷路器原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="574a1-274">如果有5次失敗的嘗試順序，則會封鎖進一步的外部要求30秒。</span><span class="sxs-lookup"><span data-stu-id="574a1-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="574a1-275">斷路器原則可設定狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="574a1-276">透過此用戶端的所有呼叫都會共用相同的線路狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="574a1-277">從 Polly 登錄新增原則</span><span class="sxs-lookup"><span data-stu-id="574a1-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="574a1-278">管理定期使用原則的一個方法是定義一次，並向 `PolicyRegistry` 註冊它們。</span><span class="sxs-lookup"><span data-stu-id="574a1-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="574a1-279">在下列程式碼中：</span><span class="sxs-lookup"><span data-stu-id="574a1-279">In the following code:</span></span>

* <span data-ttu-id="574a1-280">新增「一般」和「長」原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="574a1-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> 從登錄新增「一般」和「長」原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="574a1-282">如需和 Polly 整合的詳細資訊 `IHttpClientFactory` ，請參閱 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。</span><span class="sxs-lookup"><span data-stu-id="574a1-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="574a1-283">HttpClient 和存留期管理</span><span class="sxs-lookup"><span data-stu-id="574a1-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="574a1-284">每次在 `IHttpClientFactory` 上呼叫 `CreateClient` 時，都會傳回新的 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="574a1-285">為 <xref:System.Net.Http.HttpMessageHandler> 每個命名的用戶端建立。</span><span class="sxs-lookup"><span data-stu-id="574a1-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="574a1-286">處理站會管理 `HttpMessageHandler` 執行個體的存留期。</span><span class="sxs-lookup"><span data-stu-id="574a1-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="574a1-287">`IHttpClientFactory` 會將處理站所建立的 `HttpMessageHandler` 執行個體放入集區以減少資源耗用量。</span><span class="sxs-lookup"><span data-stu-id="574a1-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="574a1-288">建立新的 `HttpClient` 執行個體時，如果其存留期間尚未過期，`HttpMessageHandler` 執行個體可從集區重複使用。</span><span class="sxs-lookup"><span data-stu-id="574a1-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="574a1-289">將處理常式放入集區非常實用，因為處理常式通常會管理自己專屬的底層 HTTP 連線。</span><span class="sxs-lookup"><span data-stu-id="574a1-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="574a1-290">建立比所需數目更多的處理常式，可能會導致連線延遲。</span><span class="sxs-lookup"><span data-stu-id="574a1-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="574a1-291">某些處理常式也會無限期地開啟連接，這可能會導致處理常式無法對 DNS (的網域名稱系統進行回應) 變更。</span><span class="sxs-lookup"><span data-stu-id="574a1-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="574a1-292">預設處理常式存留時間為兩分鐘。</span><span class="sxs-lookup"><span data-stu-id="574a1-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="574a1-293">您可以根據每個名稱的用戶端來覆寫預設值：</span><span class="sxs-lookup"><span data-stu-id="574a1-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="574a1-294">`HttpClient` 實例通常可視為 **不** 需要處置的 .net 物件。</span><span class="sxs-lookup"><span data-stu-id="574a1-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="574a1-295">處置會取消傳出的要求，並保證指定的 `HttpClient` 執行個體在呼叫 <xref:System.IDisposable.Dispose*> 之後無法使用。</span><span class="sxs-lookup"><span data-stu-id="574a1-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="574a1-296">`IHttpClientFactory` 會追蹤並處置 `HttpClient` 執行個體使用的資源。</span><span class="sxs-lookup"><span data-stu-id="574a1-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="574a1-297">在開始使用 `IHttpClientFactory` 之前，讓單一 `HttpClient` 執行個體維持一段較長的時間，是很常使用的模式。</span><span class="sxs-lookup"><span data-stu-id="574a1-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="574a1-298">在移轉到 `IHttpClientFactory` 之後，就不再需要此模式。</span><span class="sxs-lookup"><span data-stu-id="574a1-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="574a1-299">IHttpClientFactory 的替代方案</span><span class="sxs-lookup"><span data-stu-id="574a1-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="574a1-300">`IHttpClientFactory`在啟用 DI 的應用程式中使用，可避免：</span><span class="sxs-lookup"><span data-stu-id="574a1-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="574a1-301">共用實例的資源耗盡問題 `HttpMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="574a1-302">定期迴圈實例的過時 DNS 問題 `HttpMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="574a1-303">有其他方法可以解決上述問題，方法是使用長期的 <xref:System.Net.Http.SocketsHttpHandler> 實例。</span><span class="sxs-lookup"><span data-stu-id="574a1-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="574a1-304">在 `SocketsHttpHandler` 應用程式啟動時建立的實例，並在應用程式的存留期內使用它。</span><span class="sxs-lookup"><span data-stu-id="574a1-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="574a1-305"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>根據 DNS 重新整理時間設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="574a1-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="574a1-306">`HttpClient`視需要使用建立實例 `new HttpClient(handler, disposeHandler: false)` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="574a1-307">上述方法可解決以類似的方式解決的資源管理問題 `IHttpClientFactory` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="574a1-308">`SocketsHttpHandler`共用實例之間的 `HttpClient` 連接。</span><span class="sxs-lookup"><span data-stu-id="574a1-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="574a1-309">這種共用可防止通訊端耗盡。</span><span class="sxs-lookup"><span data-stu-id="574a1-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="574a1-310">會 `SocketsHttpHandler` 根據來迴圈連接 `PooledConnectionLifetime` ，以避免過時的 DNS 問題。</span><span class="sxs-lookup"><span data-stu-id="574a1-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="574a1-311">Cookie</span><span class="sxs-lookup"><span data-stu-id="574a1-311">Cookies</span></span>

<span data-ttu-id="574a1-312">集區 `HttpMessageHandler` 實例會產生 `CookieContainer` 共用的物件。</span><span class="sxs-lookup"><span data-stu-id="574a1-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="574a1-313">非預期 `CookieContainer` 的物件共用通常會導致不正確的程式碼。</span><span class="sxs-lookup"><span data-stu-id="574a1-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="574a1-314">針對需要的應用程式 cookie ，請考慮下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="574a1-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="574a1-315">停用自動 cookie 處理</span><span class="sxs-lookup"><span data-stu-id="574a1-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="574a1-316">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="574a1-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="574a1-317">呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以停用自動 cookie 處理：</span><span class="sxs-lookup"><span data-stu-id="574a1-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="574a1-318">記錄</span><span class="sxs-lookup"><span data-stu-id="574a1-318">Logging</span></span>

<span data-ttu-id="574a1-319">透過 `IHttpClientFactory` 建立的用戶端會記錄所有要求的記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="574a1-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="574a1-320">在記錄設定中啟用適當的資訊層級，以查看預設記錄檔訊息。</span><span class="sxs-lookup"><span data-stu-id="574a1-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="574a1-321">額外的記錄功能，例如要求標頭的記錄，只會包含在追蹤層級。</span><span class="sxs-lookup"><span data-stu-id="574a1-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="574a1-322">用於每個用戶端的記錄檔分類包含用戶端的名稱。</span><span class="sxs-lookup"><span data-stu-id="574a1-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="574a1-323">例如，名為 *MyNamedClient* 的用戶端會記錄類別為 "HttpClient" 的訊息。**MyNamedClient**。LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="574a1-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="574a1-324">後面加上 *LogicalHandler* 的訊息發生在要求處理常式管線之外。</span><span class="sxs-lookup"><span data-stu-id="574a1-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="574a1-325">在要求中，訊息會在管線中任何其他處理常式處理它之前就記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="574a1-326">在回應中，訊息會在任何其他管線處理常式收到回應之後記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="574a1-327">記錄也會發生在要求處理常式管線之內。</span><span class="sxs-lookup"><span data-stu-id="574a1-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="574a1-328">在 *MyNamedClient* 範例中，這些訊息會記錄在記錄檔類別中為 "HttpClient"。**MyNamedClient**。ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="574a1-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="574a1-329">針對要求，這會發生在所有其他處理常式都已執行，且在傳送要求之前立即執行。</span><span class="sxs-lookup"><span data-stu-id="574a1-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="574a1-330">在回應中，此記錄會包含回應傳回通過處理常式管線之前的狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="574a1-331">在管線內外啟用記錄，可讓您檢查其他管線處理常式所做的變更。</span><span class="sxs-lookup"><span data-stu-id="574a1-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="574a1-332">這可能包括對要求標頭的變更或回應狀態碼的變更。</span><span class="sxs-lookup"><span data-stu-id="574a1-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="574a1-333">在記錄類別中包含用戶端的名稱，可讓您針對特定的已命名用戶端進行記錄篩選。</span><span class="sxs-lookup"><span data-stu-id="574a1-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="574a1-334">設定 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="574a1-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="574a1-335">可能需要控制用戶端使用之內部 `HttpMessageHandler` 的組態。</span><span class="sxs-lookup"><span data-stu-id="574a1-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="574a1-336">新增具名或具型別用戶端時，會傳回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="574a1-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="574a1-337"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 擴充方法可以用來定義委派。</span><span class="sxs-lookup"><span data-stu-id="574a1-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="574a1-338">委派是用來建立及設定該用戶端所使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="574a1-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="574a1-339">在主控台應用程式中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="574a1-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="574a1-340">在主控台應用程式中，將下列套件參考新增至專案：</span><span class="sxs-lookup"><span data-stu-id="574a1-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="574a1-341">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="574a1-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="574a1-342">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="574a1-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="574a1-343">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="574a1-343">In the following example:</span></span>

* <span data-ttu-id="574a1-344"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主機的](xref:fundamentals/host/generic-host)服務容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="574a1-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="574a1-345">`MyService` 會從服務建立用戶端 Factory 執行個體，其可用來建立 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="574a1-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="574a1-346">`HttpClient` 會用來擷取網頁。</span><span class="sxs-lookup"><span data-stu-id="574a1-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="574a1-347">`Main` 會建立範圍來執行服務的 `GetPage` 方法，並將網頁內容的前 500 個字元寫入至主控台。</span><span class="sxs-lookup"><span data-stu-id="574a1-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="574a1-348">標頭傳播中介軟體</span><span class="sxs-lookup"><span data-stu-id="574a1-348">Header propagation middleware</span></span>

<span data-ttu-id="574a1-349">標頭傳播是 ASP.NET Core 中介軟體，用來將傳入要求的 HTTP 標頭傳播至傳出 HTTP 用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="574a1-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="574a1-350">若要使用標頭傳播：</span><span class="sxs-lookup"><span data-stu-id="574a1-350">To use header propagation:</span></span>

* <span data-ttu-id="574a1-351">參考 [AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 套件。</span><span class="sxs-lookup"><span data-stu-id="574a1-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="574a1-352">設定中介軟體和 `HttpClient` `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="574a1-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="574a1-353">用戶端會在輸出要求中包含已設定的標頭：</span><span class="sxs-lookup"><span data-stu-id="574a1-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="574a1-354">其他資源</span><span class="sxs-lookup"><span data-stu-id="574a1-354">Additional resources</span></span>

* [<span data-ttu-id="574a1-355">使用 HttpClientFactory 實作復原 HTTP 要求</span><span class="sxs-lookup"><span data-stu-id="574a1-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="574a1-356">使用 HttpClientFactory 和 Polly 原則以指數輪詢實作 HTTP 呼叫重試</span><span class="sxs-lookup"><span data-stu-id="574a1-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="574a1-357">實作斷路器模式</span><span class="sxs-lookup"><span data-stu-id="574a1-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="574a1-358">如何在 .NET 中序列化和還原序列化 JSON</span><span class="sxs-lookup"><span data-stu-id="574a1-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="574a1-359">作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="574a1-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="574a1-360"><xref:System.Net.Http.IHttpClientFactory> 可以註冊及用來在應用程式中設定和建立 <xref:System.Net.Http.HttpClient> 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="574a1-361">它提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="574a1-361">It offers the following benefits:</span></span>

* <span data-ttu-id="574a1-362">提供一個集中位置以便命名和設定邏輯 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="574a1-363">例如，您可以註冊並設定 *github* 用戶端以存取 [github](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="574a1-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="574a1-364">預設用戶端可以註冊用於其他用途。</span><span class="sxs-lookup"><span data-stu-id="574a1-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="574a1-365">透過委派 `HttpClient` 中的處理常式來撰寫外寄中介軟體的概念，並提供延伸模組以便 Polly 架構中介軟體利用外寄中介軟體。</span><span class="sxs-lookup"><span data-stu-id="574a1-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="574a1-366">管理基礎 `HttpClientMessageHandler` 執行個體的共用和存留期，以避免在手動管理 `HttpClient` 存留期時，發生的常見 DNS 問題。</span><span class="sxs-lookup"><span data-stu-id="574a1-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="574a1-367">針對透過處理站所建立之用戶端傳送的所有要求，新增可設定的記錄體驗 (透過 `ILogger`)。</span><span class="sxs-lookup"><span data-stu-id="574a1-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="574a1-368">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="574a1-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="574a1-369">耗用量模式</span><span class="sxs-lookup"><span data-stu-id="574a1-369">Consumption patterns</span></span>

<span data-ttu-id="574a1-370">有數種方式可將 `IHttpClientFactory` 用於應用程式：</span><span class="sxs-lookup"><span data-stu-id="574a1-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="574a1-371">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="574a1-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="574a1-372">具名用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="574a1-373">具型別用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="574a1-374">產生的用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="574a1-375">它們全都不會嚴格優先於另一個。</span><span class="sxs-lookup"><span data-stu-id="574a1-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="574a1-376">最好的方法取決於應用程式的條件約束。</span><span class="sxs-lookup"><span data-stu-id="574a1-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="574a1-377">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="574a1-377">Basic usage</span></span>

<span data-ttu-id="574a1-378">`IHttpClientFactory` 可以藉由在 `Startup.ConfigureServices` 方法內的 `IServiceCollection` 上呼叫 `AddHttpClient` 擴充方法來註冊。</span><span class="sxs-lookup"><span data-stu-id="574a1-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="574a1-379">註冊之後，程式碼可以接受可透過相依性 `IHttpClientFactory` [插入 (DI) ](xref:fundamentals/dependency-injection)插入的任何位置服務。</span><span class="sxs-lookup"><span data-stu-id="574a1-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="574a1-380">`IHttpClientFactory`可以用來建立 `HttpClient` 實例：</span><span class="sxs-lookup"><span data-stu-id="574a1-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="574a1-381">以這種方式使用 `IHttpClientFactory` 是重構現有應用程式的好方法。</span><span class="sxs-lookup"><span data-stu-id="574a1-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="574a1-382">它對 `HttpClient` 的使用方式沒有任何影響。</span><span class="sxs-lookup"><span data-stu-id="574a1-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="574a1-383">在目前建立 `HttpClient` 執行個體的位置，將那些項目取代為呼叫 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>。</span><span class="sxs-lookup"><span data-stu-id="574a1-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="574a1-384">具名用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-384">Named clients</span></span>

<span data-ttu-id="574a1-385">如果應用程式需要使用多個不同的 `HttpClient`，且每個都有不同的設定，可以選擇使用 **具名用戶端**。</span><span class="sxs-lookup"><span data-stu-id="574a1-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="574a1-386">具名 `HttpClient` 的組態可以在 `Startup.ConfigureServices` 中註冊時指定。</span><span class="sxs-lookup"><span data-stu-id="574a1-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="574a1-387">在上述程式碼中， `AddHttpClient` 會呼叫，並提供名稱 *github*。</span><span class="sxs-lookup"><span data-stu-id="574a1-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="574a1-388">此用戶端已套用一些預設組態&mdash;即使用 GitHub API 所需的基底位址和兩個標頭。</span><span class="sxs-lookup"><span data-stu-id="574a1-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="574a1-389">每次呼叫 `CreateClient` 時，會建立 `HttpClient` 的新執行個體並呼叫組態動作。</span><span class="sxs-lookup"><span data-stu-id="574a1-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="574a1-390">若要使用具名用戶端，可以傳遞字串參數至 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="574a1-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="574a1-391">指定要建立之用戶端的名稱：</span><span class="sxs-lookup"><span data-stu-id="574a1-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="574a1-392">在上述程式碼中，要求不需要指定主機名稱。</span><span class="sxs-lookup"><span data-stu-id="574a1-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="574a1-393">它可以只傳遞路徑，因為已使用為用戶端設定的基底位址。</span><span class="sxs-lookup"><span data-stu-id="574a1-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="574a1-394">具型別用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-394">Typed clients</span></span>

<span data-ttu-id="574a1-395">具型別用戶端：</span><span class="sxs-lookup"><span data-stu-id="574a1-395">Typed clients:</span></span>

* <span data-ttu-id="574a1-396">提供與具名用戶端相同的功能，而不需使用字串作為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="574a1-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="574a1-397">取用用戶端時提供 IntelliSense 和編譯器說明。</span><span class="sxs-lookup"><span data-stu-id="574a1-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="574a1-398">提供單一位置來設定特定的 `HttpClient` 並與其互動。</span><span class="sxs-lookup"><span data-stu-id="574a1-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="574a1-399">例如，單一的具型別用戶端可能用於單一的後端端點，並封裝處理該端點的所有邏輯。</span><span class="sxs-lookup"><span data-stu-id="574a1-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="574a1-400">使用 DI 且可在應用程式中需要之處插入。</span><span class="sxs-lookup"><span data-stu-id="574a1-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="574a1-401">具型別用戶端會 `HttpClient` 在其函式中接受參數：</span><span class="sxs-lookup"><span data-stu-id="574a1-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="574a1-402">在上述程式碼中，組態會移到具型別用戶端。</span><span class="sxs-lookup"><span data-stu-id="574a1-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="574a1-403">`HttpClient` 物件會公開為公用屬性。</span><span class="sxs-lookup"><span data-stu-id="574a1-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="574a1-404">您可定義 API 特定的方法，其公開 `HttpClient` 功能。</span><span class="sxs-lookup"><span data-stu-id="574a1-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="574a1-405">`GetAspNetDocsIssues` 方法會封裝從 GitHub 存放庫查詢和剖析最新開啟問題所需的程式碼。</span><span class="sxs-lookup"><span data-stu-id="574a1-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="574a1-406">若要註冊具型別用戶端，泛型 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 擴充方法可用於 `Startup.ConfigureServices` 內，並指定具型別用戶端類別：</span><span class="sxs-lookup"><span data-stu-id="574a1-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="574a1-407">具型別用戶端會向 DI 註冊為暫時性。</span><span class="sxs-lookup"><span data-stu-id="574a1-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="574a1-408">具型別用戶端可以直接插入並使用：</span><span class="sxs-lookup"><span data-stu-id="574a1-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="574a1-409">想要的話，具型別用戶端的組態可以在 `Startup.ConfigureServices` 中註冊時指定，而不是在具型別用戶端的建構函式中：</span><span class="sxs-lookup"><span data-stu-id="574a1-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="574a1-410">可以將 `HttpClient` 完全封裝在具型別用戶端內。</span><span class="sxs-lookup"><span data-stu-id="574a1-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="574a1-411">可以提供在內部呼叫 `HttpClient` 執行個體的公用方法，而不將它公開為屬性。</span><span class="sxs-lookup"><span data-stu-id="574a1-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="574a1-412">在上述程式碼，`HttpClient` 儲存為私用欄位。</span><span class="sxs-lookup"><span data-stu-id="574a1-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="574a1-413">進行外部呼叫的所有存取都會經歷 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="574a1-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="574a1-414">產生的用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-414">Generated clients</span></span>

<span data-ttu-id="574a1-415">`IHttpClientFactory` 可和其他協力廠商程式庫一起使用，例如 [Refit](https://github.com/paulcbetts/refit)。</span><span class="sxs-lookup"><span data-stu-id="574a1-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="574a1-416">Refit 是適用於 .NET 的 REST 程式庫。</span><span class="sxs-lookup"><span data-stu-id="574a1-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="574a1-417">它將 REST API 轉換為即時介面。</span><span class="sxs-lookup"><span data-stu-id="574a1-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="574a1-418">介面的實作由 `RestService` 動態產生，並使用 `HttpClient` 進行外部 HTTP 呼叫。</span><span class="sxs-lookup"><span data-stu-id="574a1-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="574a1-419">定義介面及回覆來代表外部 API 和其回應：</span><span class="sxs-lookup"><span data-stu-id="574a1-419">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="574a1-420">可以新增具型別用戶端，使用 Refit 產生實作：</span><span class="sxs-lookup"><span data-stu-id="574a1-420">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="574a1-421">定義的介面可在需要時使用，並搭配 DI 與 Refit 所提供的實作：</span><span class="sxs-lookup"><span data-stu-id="574a1-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="574a1-422">外寄要求中介軟體</span><span class="sxs-lookup"><span data-stu-id="574a1-422">Outgoing request middleware</span></span>

<span data-ttu-id="574a1-423">`HttpClient` 已經有委派可針對外寄 HTTP 要求連結在一起的處理常式的概念。</span><span class="sxs-lookup"><span data-stu-id="574a1-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="574a1-424">`IHttpClientFactory` 可讓您輕鬆地定義要套用於每個具名用戶端的處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="574a1-425">它支援註冊和鏈結多個處理常式，以建置外寄要求中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="574a1-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="574a1-426">這些處理常式每個都可以在外寄要求之前和之後執行工作。</span><span class="sxs-lookup"><span data-stu-id="574a1-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="574a1-427">此模式與 ASP.NET Core 中的輸入中介軟體管線相似。</span><span class="sxs-lookup"><span data-stu-id="574a1-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="574a1-428">模式提供一個機制來管理 HTTP 要求的跨領域關注，包括快取、錯誤處理、序列化和記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="574a1-429">若要建立處理常式，請定義衍生自 <xref:System.Net.Http.DelegatingHandler> 的類別。</span><span class="sxs-lookup"><span data-stu-id="574a1-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="574a1-430">覆寫 `SendAsync` 方法，以在將要求傳遞至管線中的下一個處理常式之前執行程式碼：</span><span class="sxs-lookup"><span data-stu-id="574a1-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="574a1-431">上述程式碼定義一個基本處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="574a1-432">它會檢查以查看要求上是否已包含 `X-API-KEY` 標頭。</span><span class="sxs-lookup"><span data-stu-id="574a1-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="574a1-433">如果遺漏標頭，它可以避免 HTTP 呼叫，並傳回適當的回應。</span><span class="sxs-lookup"><span data-stu-id="574a1-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="574a1-434">在註冊期間，可以將一或多個處理常式新增至的設定 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="574a1-435">這項工作是透過 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的擴充方法完成。</span><span class="sxs-lookup"><span data-stu-id="574a1-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="574a1-436">在上述程式碼，`ValidateHeaderHandler` 已向 DI 註冊。</span><span class="sxs-lookup"><span data-stu-id="574a1-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="574a1-437">`IHttpClientFactory` 會為每個處理常式建立個別的 DI 範圍。</span><span class="sxs-lookup"><span data-stu-id="574a1-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="574a1-438">處理常式可相依於任何範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="574a1-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="574a1-439">處置處理常式時，會處置處理常式所相依的服務。</span><span class="sxs-lookup"><span data-stu-id="574a1-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="574a1-440">註冊之後，便可以呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，並傳入處理常式的類型。</span><span class="sxs-lookup"><span data-stu-id="574a1-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="574a1-441">可以遵循應該執行的順序來註冊多個處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="574a1-442">每個處理常式會包裝下一個處理常式，直到最終 `HttpClientHandler` 執行要求：</span><span class="sxs-lookup"><span data-stu-id="574a1-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="574a1-443">使用下列其中一種方式來與訊息處理常式共用個別要求狀態：</span><span class="sxs-lookup"><span data-stu-id="574a1-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="574a1-444">使用 `HttpRequestMessage.Properties` 將資料傳遞到處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="574a1-445">使用 `IHttpContextAccessor` 來存取目前的要求。</span><span class="sxs-lookup"><span data-stu-id="574a1-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="574a1-446">建立自訂 `AsyncLocal` 儲存體物件以傳遞資料。</span><span class="sxs-lookup"><span data-stu-id="574a1-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="574a1-447">使用 Polly 為基礎的處理常式</span><span class="sxs-lookup"><span data-stu-id="574a1-447">Use Polly-based handlers</span></span>

<span data-ttu-id="574a1-448">`IHttpClientFactory` 整合受歡迎的協力廠商程式庫，稱為 [Polly](https://github.com/App-vNext/Polly)。</span><span class="sxs-lookup"><span data-stu-id="574a1-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="574a1-449">Polly 是適用於 .NET 的完整恢復功能和暫時性錯誤處理程式庫。</span><span class="sxs-lookup"><span data-stu-id="574a1-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="574a1-450">它可讓開發人員以流暢且執行緒安全的方式表達原則，例如重試、斷路器、逾時、艙隔離與後援。</span><span class="sxs-lookup"><span data-stu-id="574a1-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="574a1-451">提供擴充方法來啟用使用 Polly 原則搭配設定的 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="574a1-452">Polly 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="574a1-452">The Polly extensions:</span></span>

* <span data-ttu-id="574a1-453">支援將以 Polly 為基礎的處理常式新增至用戶端。</span><span class="sxs-lookup"><span data-stu-id="574a1-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="574a1-454">可在安裝 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 套件後使用。</span><span class="sxs-lookup"><span data-stu-id="574a1-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="574a1-455">該套件並未包含在 ASP.NET Core 共用架構中。</span><span class="sxs-lookup"><span data-stu-id="574a1-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="574a1-456">處理暫時性錯誤</span><span class="sxs-lookup"><span data-stu-id="574a1-456">Handle transient faults</span></span>

<span data-ttu-id="574a1-457">大部分的錯誤發生在外部 HTTP 呼叫是暫時性的時候。</span><span class="sxs-lookup"><span data-stu-id="574a1-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="574a1-458">包含一個便利的擴充方法，稱為 `AddTransientHttpErrorPolicy`，它可允許定義原則來處理暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="574a1-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="574a1-459">使用此延伸模組方法設定的原則，會處理 `HttpRequestException`、HTTP 5xx 回應和 HTTP 408 回應。</span><span class="sxs-lookup"><span data-stu-id="574a1-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="574a1-460">`AddTransientHttpErrorPolicy` 延伸模組可用於 `Startup.ConfigureServices` 內。</span><span class="sxs-lookup"><span data-stu-id="574a1-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="574a1-461">延伸模組能提供 `PolicyBuilder` 物件的存取，該物件已設定來處理代表可能暫時性錯誤的錯誤：</span><span class="sxs-lookup"><span data-stu-id="574a1-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="574a1-462">在上述程式碼，已定義了 `WaitAndRetryAsync` 原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="574a1-463">失敗的要求會重試最多三次，並且在嘗試之間會有 600 毫秒的延遲時間。</span><span class="sxs-lookup"><span data-stu-id="574a1-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="574a1-464">動態選取原則</span><span class="sxs-lookup"><span data-stu-id="574a1-464">Dynamically select policies</span></span>

<span data-ttu-id="574a1-465">有額外的擴充方法可用來新增 Polly 為基礎的處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="574a1-466">其中一個這類延伸模組是 `AddPolicyHandler`，它有多個多載。</span><span class="sxs-lookup"><span data-stu-id="574a1-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="574a1-467">一個多載可讓您在定義要套用的原則時，檢查要求：</span><span class="sxs-lookup"><span data-stu-id="574a1-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="574a1-468">在上述程式碼中，如果外寄要求是 HTTP GET，就會套用 10 秒逾時。</span><span class="sxs-lookup"><span data-stu-id="574a1-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="574a1-469">任何其他 HTTP 方法會使用 30 秒逾時。</span><span class="sxs-lookup"><span data-stu-id="574a1-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="574a1-470">新增多個 Polly 處理常式</span><span class="sxs-lookup"><span data-stu-id="574a1-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="574a1-471">通常會建立巢狀 Polly 原則，以提供增強的功能：</span><span class="sxs-lookup"><span data-stu-id="574a1-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="574a1-472">在上述範例中，會新增兩個處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="574a1-473">第一個使用 `AddTransientHttpErrorPolicy` 延伸模組來新增重試原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="574a1-474">失敗的要求會重試最多三次。</span><span class="sxs-lookup"><span data-stu-id="574a1-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="574a1-475">第二個 `AddTransientHttpErrorPolicy` 呼叫會新增斷路器原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="574a1-476">如果循序發生五次失敗的嘗試，進一步的外部要求會遭到封鎖 30 秒。</span><span class="sxs-lookup"><span data-stu-id="574a1-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="574a1-477">斷路器原則可設定狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="574a1-478">透過此用戶端的所有呼叫都會共用相同的線路狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="574a1-479">從 Polly 登錄新增原則</span><span class="sxs-lookup"><span data-stu-id="574a1-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="574a1-480">管理定期使用原則的一個方法是定義一次，並向 `PolicyRegistry` 註冊它們。</span><span class="sxs-lookup"><span data-stu-id="574a1-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="574a1-481">提供了擴充方法，可以使用來自登錄的原則新增處理常式：</span><span class="sxs-lookup"><span data-stu-id="574a1-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="574a1-482">在上述程式碼中，當 `PolicyRegistry` 新增至 `ServiceCollection` 時，註冊了兩個原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="574a1-483">為了使用來自登錄的原則，使用了 `AddPolicyHandlerFromRegistry` 方法，並傳遞要套用的原則名稱。</span><span class="sxs-lookup"><span data-stu-id="574a1-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="574a1-484">關於 `IHttpClientFactory` Polly 整合的詳細資訊，可以在 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) 上找到。</span><span class="sxs-lookup"><span data-stu-id="574a1-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="574a1-485">HttpClient 和存留期管理</span><span class="sxs-lookup"><span data-stu-id="574a1-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="574a1-486">每次在 `IHttpClientFactory` 上呼叫 `CreateClient` 時，都會傳回新的 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="574a1-487">每個具名用戶端都有一個 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="574a1-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="574a1-488">處理站會管理 `HttpMessageHandler` 執行個體的存留期。</span><span class="sxs-lookup"><span data-stu-id="574a1-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="574a1-489">`IHttpClientFactory` 會將處理站所建立的 `HttpMessageHandler` 執行個體放入集區以減少資源耗用量。</span><span class="sxs-lookup"><span data-stu-id="574a1-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="574a1-490">建立新的 `HttpClient` 執行個體時，如果其存留期間尚未過期，`HttpMessageHandler` 執行個體可從集區重複使用。</span><span class="sxs-lookup"><span data-stu-id="574a1-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="574a1-491">將處理常式放入集區非常實用，因為處理常式通常會管理自己專屬的底層 HTTP 連線。</span><span class="sxs-lookup"><span data-stu-id="574a1-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="574a1-492">建立比所需數目更多的處理常式，可能會導致連線延遲。</span><span class="sxs-lookup"><span data-stu-id="574a1-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="574a1-493">有些處理常式也會保持連線無限期地開啟，這可能導致處理常式無法對 DNS 變更回應。</span><span class="sxs-lookup"><span data-stu-id="574a1-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="574a1-494">預設處理常式存留時間為兩分鐘。</span><span class="sxs-lookup"><span data-stu-id="574a1-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="574a1-495">可以針對每個具名用戶端覆寫預設值。</span><span class="sxs-lookup"><span data-stu-id="574a1-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="574a1-496">若要覆寫它，請在建立用戶端時所傳回的 `IHttpClientBuilder` 上呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="574a1-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="574a1-497">不需要處置用戶端。</span><span class="sxs-lookup"><span data-stu-id="574a1-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="574a1-498">處置會取消傳出的要求，並保證指定的 `HttpClient` 執行個體在呼叫 <xref:System.IDisposable.Dispose*> 之後無法使用。</span><span class="sxs-lookup"><span data-stu-id="574a1-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="574a1-499">`IHttpClientFactory` 會追蹤並處置 `HttpClient` 執行個體使用的資源。</span><span class="sxs-lookup"><span data-stu-id="574a1-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="574a1-500">`HttpClient` 執行個體通常可視為 .NET 物件，不需要處置。</span><span class="sxs-lookup"><span data-stu-id="574a1-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="574a1-501">在開始使用 `IHttpClientFactory` 之前，讓單一 `HttpClient` 執行個體維持一段較長的時間，是很常使用的模式。</span><span class="sxs-lookup"><span data-stu-id="574a1-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="574a1-502">在移轉到 `IHttpClientFactory` 之後，就不再需要此模式。</span><span class="sxs-lookup"><span data-stu-id="574a1-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="574a1-503">IHttpClientFactory 的替代方案</span><span class="sxs-lookup"><span data-stu-id="574a1-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="574a1-504">`IHttpClientFactory`在啟用 DI 的應用程式中使用，可避免：</span><span class="sxs-lookup"><span data-stu-id="574a1-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="574a1-505">共用實例的資源耗盡問題 `HttpMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="574a1-506">定期迴圈實例的過時 DNS 問題 `HttpMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="574a1-507">有其他方法可以解決上述問題，方法是使用長期的 <xref:System.Net.Http.SocketsHttpHandler> 實例。</span><span class="sxs-lookup"><span data-stu-id="574a1-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="574a1-508">在 `SocketsHttpHandler` 應用程式啟動時建立的實例，並在應用程式的存留期內使用它。</span><span class="sxs-lookup"><span data-stu-id="574a1-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="574a1-509"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>根據 DNS 重新整理時間設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="574a1-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="574a1-510">`HttpClient`視需要使用建立實例 `new HttpClient(handler, disposeHandler: false)` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="574a1-511">上述方法可解決以類似的方式解決的資源管理問題 `IHttpClientFactory` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="574a1-512">`SocketsHttpHandler`共用實例之間的 `HttpClient` 連接。</span><span class="sxs-lookup"><span data-stu-id="574a1-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="574a1-513">這種共用可防止通訊端耗盡。</span><span class="sxs-lookup"><span data-stu-id="574a1-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="574a1-514">會 `SocketsHttpHandler` 根據來迴圈連接 `PooledConnectionLifetime` ，以避免過時的 DNS 問題。</span><span class="sxs-lookup"><span data-stu-id="574a1-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="574a1-515">Cookie</span><span class="sxs-lookup"><span data-stu-id="574a1-515">Cookies</span></span>

<span data-ttu-id="574a1-516">集區 `HttpMessageHandler` 實例會產生 `CookieContainer` 共用的物件。</span><span class="sxs-lookup"><span data-stu-id="574a1-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="574a1-517">非預期 `CookieContainer` 的物件共用通常會導致不正確的程式碼。</span><span class="sxs-lookup"><span data-stu-id="574a1-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="574a1-518">針對需要的應用程式 cookie ，請考慮下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="574a1-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="574a1-519">停用自動 cookie 處理</span><span class="sxs-lookup"><span data-stu-id="574a1-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="574a1-520">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="574a1-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="574a1-521">呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以停用自動 cookie 處理：</span><span class="sxs-lookup"><span data-stu-id="574a1-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="574a1-522">記錄</span><span class="sxs-lookup"><span data-stu-id="574a1-522">Logging</span></span>

<span data-ttu-id="574a1-523">透過 `IHttpClientFactory` 建立的用戶端會記錄所有要求的記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="574a1-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="574a1-524">在記錄設定中啟用適當的資訊層級，以查看預設記錄檔訊息。</span><span class="sxs-lookup"><span data-stu-id="574a1-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="574a1-525">額外的記錄功能，例如要求標頭的記錄，只會包含在追蹤層級。</span><span class="sxs-lookup"><span data-stu-id="574a1-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="574a1-526">用於每個用戶端的記錄檔分類包含用戶端的名稱。</span><span class="sxs-lookup"><span data-stu-id="574a1-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="574a1-527">例如，名為 *MyNamedClient* 的用戶端會記錄類別為的訊息 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="574a1-528">後面加上 *LogicalHandler* 的訊息發生在要求處理常式管線之外。</span><span class="sxs-lookup"><span data-stu-id="574a1-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="574a1-529">在要求中，訊息會在管線中任何其他處理常式處理它之前就記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="574a1-530">在回應中，訊息會在任何其他管線處理常式收到回應之後記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="574a1-531">記錄也會發生在要求處理常式管線之內。</span><span class="sxs-lookup"><span data-stu-id="574a1-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="574a1-532">在 *MyNamedClient* 範例中，那些訊息是針對記錄檔分類 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 而記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="574a1-533">對於要求，這是發生在所有其他處理常式都已執行之後，並且緊接在網路上傳送要求之前。</span><span class="sxs-lookup"><span data-stu-id="574a1-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="574a1-534">在回應中，此記錄會包含回應傳回通過處理常式管線之前的狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="574a1-535">在管線內外啟用記錄，可讓您檢查其他管線處理常式所做的變更。</span><span class="sxs-lookup"><span data-stu-id="574a1-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="574a1-536">例如，這可能包括要求標頭的變更，或是回應狀態碼的變更。</span><span class="sxs-lookup"><span data-stu-id="574a1-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="574a1-537">在記錄分類中包含用戶端的名稱，可讓您在需要時進行特定具名用戶端的記錄檔篩選。</span><span class="sxs-lookup"><span data-stu-id="574a1-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="574a1-538">設定 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="574a1-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="574a1-539">可能需要控制用戶端使用之內部 `HttpMessageHandler` 的組態。</span><span class="sxs-lookup"><span data-stu-id="574a1-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="574a1-540">新增具名或具型別用戶端時，會傳回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="574a1-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="574a1-541"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 擴充方法可以用來定義委派。</span><span class="sxs-lookup"><span data-stu-id="574a1-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="574a1-542">委派是用來建立及設定該用戶端所使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="574a1-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="574a1-543">在主控台應用程式中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="574a1-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="574a1-544">在主控台應用程式中，將下列套件參考新增至專案：</span><span class="sxs-lookup"><span data-stu-id="574a1-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="574a1-545">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="574a1-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="574a1-546">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="574a1-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="574a1-547">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="574a1-547">In the following example:</span></span>

* <span data-ttu-id="574a1-548"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主機的](xref:fundamentals/host/generic-host)服務容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="574a1-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="574a1-549">`MyService` 會從服務建立用戶端 Factory 執行個體，其可用來建立 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="574a1-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="574a1-550">`HttpClient` 會用來擷取網頁。</span><span class="sxs-lookup"><span data-stu-id="574a1-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="574a1-551">`Main` 會建立範圍來執行服務的 `GetPage` 方法，並將網頁內容的前 500 個字元寫入至主控台。</span><span class="sxs-lookup"><span data-stu-id="574a1-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="574a1-552">其他資源</span><span class="sxs-lookup"><span data-stu-id="574a1-552">Additional resources</span></span>

* [<span data-ttu-id="574a1-553">使用 HttpClientFactory 實作復原 HTTP 要求</span><span class="sxs-lookup"><span data-stu-id="574a1-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="574a1-554">使用 HttpClientFactory 和 Polly 原則以指數輪詢實作 HTTP 呼叫重試</span><span class="sxs-lookup"><span data-stu-id="574a1-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="574a1-555">實作斷路器模式</span><span class="sxs-lookup"><span data-stu-id="574a1-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="574a1-556">作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="574a1-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="574a1-557"><xref:System.Net.Http.IHttpClientFactory> 可以註冊及用來在應用程式中設定和建立 <xref:System.Net.Http.HttpClient> 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="574a1-558">它提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="574a1-558">It offers the following benefits:</span></span>

* <span data-ttu-id="574a1-559">提供一個集中位置以便命名和設定邏輯 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="574a1-560">例如，您可以註冊並設定 *github* 用戶端以存取 [github](https://github.com/)。</span><span class="sxs-lookup"><span data-stu-id="574a1-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="574a1-561">預設用戶端可以註冊用於其他用途。</span><span class="sxs-lookup"><span data-stu-id="574a1-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="574a1-562">透過委派 `HttpClient` 中的處理常式來撰寫外寄中介軟體的概念，並提供延伸模組以便 Polly 架構中介軟體利用外寄中介軟體。</span><span class="sxs-lookup"><span data-stu-id="574a1-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="574a1-563">管理基礎 `HttpClientMessageHandler` 執行個體的共用和存留期，以避免在手動管理 `HttpClient` 存留期時，發生的常見 DNS 問題。</span><span class="sxs-lookup"><span data-stu-id="574a1-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="574a1-564">針對透過處理站所建立之用戶端傳送的所有要求，新增可設定的記錄體驗 (透過 `ILogger`)。</span><span class="sxs-lookup"><span data-stu-id="574a1-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="574a1-565">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="574a1-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="574a1-566">必要條件</span><span class="sxs-lookup"><span data-stu-id="574a1-566">Prerequisites</span></span>

<span data-ttu-id="574a1-567">以 .NET Framework 為目標的專案，需要安裝 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="574a1-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="574a1-568">以 .NET Core 為目標且參考 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 的專案，已包含 `Microsoft.Extensions.Http` 套件。</span><span class="sxs-lookup"><span data-stu-id="574a1-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="574a1-569">耗用量模式</span><span class="sxs-lookup"><span data-stu-id="574a1-569">Consumption patterns</span></span>

<span data-ttu-id="574a1-570">有數種方式可將 `IHttpClientFactory` 用於應用程式：</span><span class="sxs-lookup"><span data-stu-id="574a1-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="574a1-571">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="574a1-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="574a1-572">具名用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="574a1-573">具型別用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="574a1-574">產生的用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="574a1-575">它們全都不會嚴格優先於另一個。</span><span class="sxs-lookup"><span data-stu-id="574a1-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="574a1-576">最好的方法取決於應用程式的條件約束。</span><span class="sxs-lookup"><span data-stu-id="574a1-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="574a1-577">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="574a1-577">Basic usage</span></span>

<span data-ttu-id="574a1-578">`IHttpClientFactory` 可以藉由在 `Startup.ConfigureServices` 方法內的 `IServiceCollection` 上呼叫 `AddHttpClient` 擴充方法來註冊。</span><span class="sxs-lookup"><span data-stu-id="574a1-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="574a1-579">註冊之後，程式碼可以接受可透過相依性 `IHttpClientFactory` [插入 (DI) ](xref:fundamentals/dependency-injection)插入的任何位置服務。</span><span class="sxs-lookup"><span data-stu-id="574a1-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="574a1-580">`IHttpClientFactory`可以用來建立 `HttpClient` 實例：</span><span class="sxs-lookup"><span data-stu-id="574a1-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="574a1-581">以這種方式使用 `IHttpClientFactory` 是重構現有應用程式的好方法。</span><span class="sxs-lookup"><span data-stu-id="574a1-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="574a1-582">它對 `HttpClient` 的使用方式沒有任何影響。</span><span class="sxs-lookup"><span data-stu-id="574a1-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="574a1-583">在目前建立 `HttpClient` 執行個體的位置，將那些項目取代為呼叫 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>。</span><span class="sxs-lookup"><span data-stu-id="574a1-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="574a1-584">具名用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-584">Named clients</span></span>

<span data-ttu-id="574a1-585">如果應用程式需要使用多個不同的 `HttpClient`，且每個都有不同的設定，可以選擇使用 **具名用戶端**。</span><span class="sxs-lookup"><span data-stu-id="574a1-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="574a1-586">具名 `HttpClient` 的組態可以在 `Startup.ConfigureServices` 中註冊時指定。</span><span class="sxs-lookup"><span data-stu-id="574a1-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="574a1-587">在上述程式碼中， `AddHttpClient` 會呼叫，並提供名稱 *github*。</span><span class="sxs-lookup"><span data-stu-id="574a1-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="574a1-588">此用戶端已套用一些預設組態&mdash;即使用 GitHub API 所需的基底位址和兩個標頭。</span><span class="sxs-lookup"><span data-stu-id="574a1-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="574a1-589">每次呼叫 `CreateClient` 時，會建立 `HttpClient` 的新執行個體並呼叫組態動作。</span><span class="sxs-lookup"><span data-stu-id="574a1-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="574a1-590">若要使用具名用戶端，可以傳遞字串參數至 `CreateClient`。</span><span class="sxs-lookup"><span data-stu-id="574a1-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="574a1-591">指定要建立之用戶端的名稱：</span><span class="sxs-lookup"><span data-stu-id="574a1-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="574a1-592">在上述程式碼中，要求不需要指定主機名稱。</span><span class="sxs-lookup"><span data-stu-id="574a1-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="574a1-593">它可以只傳遞路徑，因為已使用為用戶端設定的基底位址。</span><span class="sxs-lookup"><span data-stu-id="574a1-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="574a1-594">具型別用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-594">Typed clients</span></span>

<span data-ttu-id="574a1-595">具型別用戶端：</span><span class="sxs-lookup"><span data-stu-id="574a1-595">Typed clients:</span></span>

* <span data-ttu-id="574a1-596">提供與具名用戶端相同的功能，而不需使用字串作為索引鍵。</span><span class="sxs-lookup"><span data-stu-id="574a1-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="574a1-597">取用用戶端時提供 IntelliSense 和編譯器說明。</span><span class="sxs-lookup"><span data-stu-id="574a1-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="574a1-598">提供單一位置來設定特定的 `HttpClient` 並與其互動。</span><span class="sxs-lookup"><span data-stu-id="574a1-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="574a1-599">例如，單一的具型別用戶端可能用於單一的後端端點，並封裝處理該端點的所有邏輯。</span><span class="sxs-lookup"><span data-stu-id="574a1-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="574a1-600">使用 DI 且可在應用程式中需要之處插入。</span><span class="sxs-lookup"><span data-stu-id="574a1-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="574a1-601">具型別用戶端會 `HttpClient` 在其函式中接受參數：</span><span class="sxs-lookup"><span data-stu-id="574a1-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="574a1-602">在上述程式碼中，組態會移到具型別用戶端。</span><span class="sxs-lookup"><span data-stu-id="574a1-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="574a1-603">`HttpClient` 物件會公開為公用屬性。</span><span class="sxs-lookup"><span data-stu-id="574a1-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="574a1-604">您可定義 API 特定的方法，其公開 `HttpClient` 功能。</span><span class="sxs-lookup"><span data-stu-id="574a1-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="574a1-605">`GetAspNetDocsIssues` 方法會封裝從 GitHub 存放庫查詢和剖析最新開啟問題所需的程式碼。</span><span class="sxs-lookup"><span data-stu-id="574a1-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="574a1-606">若要註冊具型別用戶端，泛型 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 擴充方法可用於 `Startup.ConfigureServices` 內，並指定具型別用戶端類別：</span><span class="sxs-lookup"><span data-stu-id="574a1-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="574a1-607">具型別用戶端會向 DI 註冊為暫時性。</span><span class="sxs-lookup"><span data-stu-id="574a1-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="574a1-608">具型別用戶端可以直接插入並使用：</span><span class="sxs-lookup"><span data-stu-id="574a1-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="574a1-609">想要的話，具型別用戶端的組態可以在 `Startup.ConfigureServices` 中註冊時指定，而不是在具型別用戶端的建構函式中：</span><span class="sxs-lookup"><span data-stu-id="574a1-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="574a1-610">可以將 `HttpClient` 完全封裝在具型別用戶端內。</span><span class="sxs-lookup"><span data-stu-id="574a1-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="574a1-611">可以提供在內部呼叫 `HttpClient` 執行個體的公用方法，而不將它公開為屬性。</span><span class="sxs-lookup"><span data-stu-id="574a1-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="574a1-612">在上述程式碼，`HttpClient` 儲存為私用欄位。</span><span class="sxs-lookup"><span data-stu-id="574a1-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="574a1-613">進行外部呼叫的所有存取都會經歷 `GetRepos` 方法。</span><span class="sxs-lookup"><span data-stu-id="574a1-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="574a1-614">產生的用戶端</span><span class="sxs-lookup"><span data-stu-id="574a1-614">Generated clients</span></span>

<span data-ttu-id="574a1-615">`IHttpClientFactory` 可和其他協力廠商程式庫一起使用，例如 [Refit](https://github.com/paulcbetts/refit)。</span><span class="sxs-lookup"><span data-stu-id="574a1-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="574a1-616">Refit 是適用於 .NET 的 REST 程式庫。</span><span class="sxs-lookup"><span data-stu-id="574a1-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="574a1-617">它將 REST API 轉換為即時介面。</span><span class="sxs-lookup"><span data-stu-id="574a1-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="574a1-618">介面的實作由 `RestService` 動態產生，並使用 `HttpClient` 進行外部 HTTP 呼叫。</span><span class="sxs-lookup"><span data-stu-id="574a1-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="574a1-619">定義介面及回覆來代表外部 API 和其回應：</span><span class="sxs-lookup"><span data-stu-id="574a1-619">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="574a1-620">可以新增具型別用戶端，使用 Refit 產生實作：</span><span class="sxs-lookup"><span data-stu-id="574a1-620">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="574a1-621">定義的介面可在需要時使用，並搭配 DI 與 Refit 所提供的實作：</span><span class="sxs-lookup"><span data-stu-id="574a1-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="574a1-622">外寄要求中介軟體</span><span class="sxs-lookup"><span data-stu-id="574a1-622">Outgoing request middleware</span></span>

<span data-ttu-id="574a1-623">`HttpClient` 已經有委派可針對外寄 HTTP 要求連結在一起的處理常式的概念。</span><span class="sxs-lookup"><span data-stu-id="574a1-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="574a1-624">`IHttpClientFactory` 可讓您輕鬆地定義要套用於每個具名用戶端的處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="574a1-625">它支援註冊和鏈結多個處理常式，以建置外寄要求中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="574a1-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="574a1-626">這些處理常式每個都可以在外寄要求之前和之後執行工作。</span><span class="sxs-lookup"><span data-stu-id="574a1-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="574a1-627">此模式與 ASP.NET Core 中的輸入中介軟體管線相似。</span><span class="sxs-lookup"><span data-stu-id="574a1-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="574a1-628">模式提供一個機制來管理 HTTP 要求的跨領域關注，包括快取、錯誤處理、序列化和記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="574a1-629">若要建立處理常式，請定義衍生自 <xref:System.Net.Http.DelegatingHandler> 的類別。</span><span class="sxs-lookup"><span data-stu-id="574a1-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="574a1-630">覆寫 `SendAsync` 方法，以在將要求傳遞至管線中的下一個處理常式之前執行程式碼：</span><span class="sxs-lookup"><span data-stu-id="574a1-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="574a1-631">上述程式碼定義一個基本處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="574a1-632">它會檢查以查看要求上是否已包含 `X-API-KEY` 標頭。</span><span class="sxs-lookup"><span data-stu-id="574a1-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="574a1-633">如果遺漏標頭，它可以避免 HTTP 呼叫，並傳回適當的回應。</span><span class="sxs-lookup"><span data-stu-id="574a1-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="574a1-634">在註冊期間，可以將一或多個處理常式新增至的設定 `HttpClient` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="574a1-635">這項工作是透過 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的擴充方法完成。</span><span class="sxs-lookup"><span data-stu-id="574a1-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="574a1-636">在上述程式碼，`ValidateHeaderHandler` 已向 DI 註冊。</span><span class="sxs-lookup"><span data-stu-id="574a1-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="574a1-637">處理常式 **必須** 在 DI 中註冊為暫時性服務，無限定範圍。</span><span class="sxs-lookup"><span data-stu-id="574a1-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="574a1-638">如果處理常式已註冊為範圍服務，而且處理常式所相依的任何服務都是可處置的：</span><span class="sxs-lookup"><span data-stu-id="574a1-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="574a1-639">處理常式的服務可能會在處理常式超出範圍之前加以處置。</span><span class="sxs-lookup"><span data-stu-id="574a1-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="574a1-640">已處置的處理常式服務會導致處理常式失敗。</span><span class="sxs-lookup"><span data-stu-id="574a1-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="574a1-641">註冊之後，便可以呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，並傳入處理常式類型。</span><span class="sxs-lookup"><span data-stu-id="574a1-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="574a1-642">可以遵循應該執行的順序來註冊多個處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="574a1-643">每個處理常式會包裝下一個處理常式，直到最終 `HttpClientHandler` 執行要求：</span><span class="sxs-lookup"><span data-stu-id="574a1-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="574a1-644">使用下列其中一種方式來與訊息處理常式共用個別要求狀態：</span><span class="sxs-lookup"><span data-stu-id="574a1-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="574a1-645">使用 `HttpRequestMessage.Properties` 將資料傳遞到處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="574a1-646">使用 `IHttpContextAccessor` 來存取目前的要求。</span><span class="sxs-lookup"><span data-stu-id="574a1-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="574a1-647">建立自訂 `AsyncLocal` 儲存體物件以傳遞資料。</span><span class="sxs-lookup"><span data-stu-id="574a1-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="574a1-648">使用 Polly 為基礎的處理常式</span><span class="sxs-lookup"><span data-stu-id="574a1-648">Use Polly-based handlers</span></span>

<span data-ttu-id="574a1-649">`IHttpClientFactory` 整合受歡迎的協力廠商程式庫，稱為 [Polly](https://github.com/App-vNext/Polly)。</span><span class="sxs-lookup"><span data-stu-id="574a1-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="574a1-650">Polly 是適用於 .NET 的完整恢復功能和暫時性錯誤處理程式庫。</span><span class="sxs-lookup"><span data-stu-id="574a1-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="574a1-651">它可讓開發人員以流暢且執行緒安全的方式表達原則，例如重試、斷路器、逾時、艙隔離與後援。</span><span class="sxs-lookup"><span data-stu-id="574a1-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="574a1-652">提供擴充方法來啟用使用 Polly 原則搭配設定的 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="574a1-653">Polly 延伸模組：</span><span class="sxs-lookup"><span data-stu-id="574a1-653">The Polly extensions:</span></span>

* <span data-ttu-id="574a1-654">支援將以 Polly 為基礎的處理常式新增至用戶端。</span><span class="sxs-lookup"><span data-stu-id="574a1-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="574a1-655">可在安裝 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 套件後使用。</span><span class="sxs-lookup"><span data-stu-id="574a1-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="574a1-656">該套件並未包含在 ASP.NET Core 共用架構中。</span><span class="sxs-lookup"><span data-stu-id="574a1-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="574a1-657">處理暫時性錯誤</span><span class="sxs-lookup"><span data-stu-id="574a1-657">Handle transient faults</span></span>

<span data-ttu-id="574a1-658">大部分的錯誤發生在外部 HTTP 呼叫是暫時性的時候。</span><span class="sxs-lookup"><span data-stu-id="574a1-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="574a1-659">包含一個便利的擴充方法，稱為 `AddTransientHttpErrorPolicy`，它可允許定義原則來處理暫時性錯誤。</span><span class="sxs-lookup"><span data-stu-id="574a1-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="574a1-660">使用此延伸模組方法設定的原則，會處理 `HttpRequestException`、HTTP 5xx 回應和 HTTP 408 回應。</span><span class="sxs-lookup"><span data-stu-id="574a1-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="574a1-661">`AddTransientHttpErrorPolicy` 延伸模組可用於 `Startup.ConfigureServices` 內。</span><span class="sxs-lookup"><span data-stu-id="574a1-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="574a1-662">延伸模組能提供 `PolicyBuilder` 物件的存取，該物件已設定來處理代表可能暫時性錯誤的錯誤：</span><span class="sxs-lookup"><span data-stu-id="574a1-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="574a1-663">在上述程式碼，已定義了 `WaitAndRetryAsync` 原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="574a1-664">失敗的要求會重試最多三次，並且在嘗試之間會有 600 毫秒的延遲時間。</span><span class="sxs-lookup"><span data-stu-id="574a1-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="574a1-665">動態選取原則</span><span class="sxs-lookup"><span data-stu-id="574a1-665">Dynamically select policies</span></span>

<span data-ttu-id="574a1-666">有額外的擴充方法可用來新增 Polly 為基礎的處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="574a1-667">其中一個這類延伸模組是 `AddPolicyHandler`，它有多個多載。</span><span class="sxs-lookup"><span data-stu-id="574a1-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="574a1-668">一個多載可讓您在定義要套用的原則時，檢查要求：</span><span class="sxs-lookup"><span data-stu-id="574a1-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="574a1-669">在上述程式碼中，如果外寄要求是 HTTP GET，就會套用 10 秒逾時。</span><span class="sxs-lookup"><span data-stu-id="574a1-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="574a1-670">任何其他 HTTP 方法會使用 30 秒逾時。</span><span class="sxs-lookup"><span data-stu-id="574a1-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="574a1-671">新增多個 Polly 處理常式</span><span class="sxs-lookup"><span data-stu-id="574a1-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="574a1-672">通常會建立巢狀 Polly 原則，以提供增強的功能：</span><span class="sxs-lookup"><span data-stu-id="574a1-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="574a1-673">在上述範例中，會新增兩個處理常式。</span><span class="sxs-lookup"><span data-stu-id="574a1-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="574a1-674">第一個使用 `AddTransientHttpErrorPolicy` 延伸模組來新增重試原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="574a1-675">失敗的要求會重試最多三次。</span><span class="sxs-lookup"><span data-stu-id="574a1-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="574a1-676">第二個 `AddTransientHttpErrorPolicy` 呼叫會新增斷路器原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="574a1-677">如果循序發生五次失敗的嘗試，進一步的外部要求會遭到封鎖 30 秒。</span><span class="sxs-lookup"><span data-stu-id="574a1-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="574a1-678">斷路器原則可設定狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="574a1-679">透過此用戶端的所有呼叫都會共用相同的線路狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="574a1-680">從 Polly 登錄新增原則</span><span class="sxs-lookup"><span data-stu-id="574a1-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="574a1-681">管理定期使用原則的一個方法是定義一次，並向 `PolicyRegistry` 註冊它們。</span><span class="sxs-lookup"><span data-stu-id="574a1-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="574a1-682">提供了擴充方法，可以使用來自登錄的原則新增處理常式：</span><span class="sxs-lookup"><span data-stu-id="574a1-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="574a1-683">在上述程式碼中，當 `PolicyRegistry` 新增至 `ServiceCollection` 時，註冊了兩個原則。</span><span class="sxs-lookup"><span data-stu-id="574a1-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="574a1-684">為了使用來自登錄的原則，使用了 `AddPolicyHandlerFromRegistry` 方法，並傳遞要套用的原則名稱。</span><span class="sxs-lookup"><span data-stu-id="574a1-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="574a1-685">關於 `IHttpClientFactory` Polly 整合的詳細資訊，可以在 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) 上找到。</span><span class="sxs-lookup"><span data-stu-id="574a1-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="574a1-686">HttpClient 和存留期管理</span><span class="sxs-lookup"><span data-stu-id="574a1-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="574a1-687">每次在 `IHttpClientFactory` 上呼叫 `CreateClient` 時，都會傳回新的 `HttpClient` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="574a1-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="574a1-688">每個具名用戶端都有一個 <xref:System.Net.Http.HttpMessageHandler>。</span><span class="sxs-lookup"><span data-stu-id="574a1-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="574a1-689">處理站會管理 `HttpMessageHandler` 執行個體的存留期。</span><span class="sxs-lookup"><span data-stu-id="574a1-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="574a1-690">`IHttpClientFactory` 會將處理站所建立的 `HttpMessageHandler` 執行個體放入集區以減少資源耗用量。</span><span class="sxs-lookup"><span data-stu-id="574a1-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="574a1-691">建立新的 `HttpClient` 執行個體時，如果其存留期間尚未過期，`HttpMessageHandler` 執行個體可從集區重複使用。</span><span class="sxs-lookup"><span data-stu-id="574a1-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="574a1-692">將處理常式放入集區非常實用，因為處理常式通常會管理自己專屬的底層 HTTP 連線。</span><span class="sxs-lookup"><span data-stu-id="574a1-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="574a1-693">建立比所需數目更多的處理常式，可能會導致連線延遲。</span><span class="sxs-lookup"><span data-stu-id="574a1-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="574a1-694">有些處理常式也會保持連線無限期地開啟，這可能導致處理常式無法對 DNS 變更回應。</span><span class="sxs-lookup"><span data-stu-id="574a1-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="574a1-695">預設處理常式存留時間為兩分鐘。</span><span class="sxs-lookup"><span data-stu-id="574a1-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="574a1-696">可以針對每個具名用戶端覆寫預設值。</span><span class="sxs-lookup"><span data-stu-id="574a1-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="574a1-697">若要覆寫它，請在建立用戶端時所傳回的 `IHttpClientBuilder` 上呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：</span><span class="sxs-lookup"><span data-stu-id="574a1-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="574a1-698">不需要處置用戶端。</span><span class="sxs-lookup"><span data-stu-id="574a1-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="574a1-699">處置會取消傳出的要求，並保證指定的 `HttpClient` 執行個體在呼叫 <xref:System.IDisposable.Dispose*> 之後無法使用。</span><span class="sxs-lookup"><span data-stu-id="574a1-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="574a1-700">`IHttpClientFactory` 會追蹤並處置 `HttpClient` 執行個體使用的資源。</span><span class="sxs-lookup"><span data-stu-id="574a1-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="574a1-701">`HttpClient` 執行個體通常可視為 .NET 物件，不需要處置。</span><span class="sxs-lookup"><span data-stu-id="574a1-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="574a1-702">在開始使用 `IHttpClientFactory` 之前，讓單一 `HttpClient` 執行個體維持一段較長的時間，是很常使用的模式。</span><span class="sxs-lookup"><span data-stu-id="574a1-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="574a1-703">在移轉到 `IHttpClientFactory` 之後，就不再需要此模式。</span><span class="sxs-lookup"><span data-stu-id="574a1-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="574a1-704">IHttpClientFactory 的替代方案</span><span class="sxs-lookup"><span data-stu-id="574a1-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="574a1-705">`IHttpClientFactory`在啟用 DI 的應用程式中使用，可避免：</span><span class="sxs-lookup"><span data-stu-id="574a1-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="574a1-706">共用實例的資源耗盡問題 `HttpMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="574a1-707">定期迴圈實例的過時 DNS 問題 `HttpMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="574a1-708">有其他方法可以解決上述問題，方法是使用長期的 <xref:System.Net.Http.SocketsHttpHandler> 實例。</span><span class="sxs-lookup"><span data-stu-id="574a1-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="574a1-709">在 `SocketsHttpHandler` 應用程式啟動時建立的實例，並在應用程式的存留期內使用它。</span><span class="sxs-lookup"><span data-stu-id="574a1-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="574a1-710"><xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>根據 DNS 重新整理時間設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="574a1-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="574a1-711">`HttpClient`視需要使用建立實例 `new HttpClient(handler, disposeHandler: false)` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="574a1-712">上述方法可解決以類似的方式解決的資源管理問題 `IHttpClientFactory` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="574a1-713">`SocketsHttpHandler`共用實例之間的 `HttpClient` 連接。</span><span class="sxs-lookup"><span data-stu-id="574a1-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="574a1-714">這種共用可防止通訊端耗盡。</span><span class="sxs-lookup"><span data-stu-id="574a1-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="574a1-715">會 `SocketsHttpHandler` 根據來迴圈連接 `PooledConnectionLifetime` ，以避免過時的 DNS 問題。</span><span class="sxs-lookup"><span data-stu-id="574a1-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="no-loccookies"></a><span data-ttu-id="574a1-716">Cookie</span><span class="sxs-lookup"><span data-stu-id="574a1-716">Cookies</span></span>

<span data-ttu-id="574a1-717">集區 `HttpMessageHandler` 實例會產生 `CookieContainer` 共用的物件。</span><span class="sxs-lookup"><span data-stu-id="574a1-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="574a1-718">非預期 `CookieContainer` 的物件共用通常會導致不正確的程式碼。</span><span class="sxs-lookup"><span data-stu-id="574a1-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="574a1-719">針對需要的應用程式 cookie ，請考慮下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="574a1-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="574a1-720">停用自動 cookie 處理</span><span class="sxs-lookup"><span data-stu-id="574a1-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="574a1-721">避免 `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="574a1-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="574a1-722">呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以停用自動 cookie 處理：</span><span class="sxs-lookup"><span data-stu-id="574a1-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="574a1-723">記錄</span><span class="sxs-lookup"><span data-stu-id="574a1-723">Logging</span></span>

<span data-ttu-id="574a1-724">透過 `IHttpClientFactory` 建立的用戶端會記錄所有要求的記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="574a1-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="574a1-725">在記錄設定中啟用適當的資訊層級，以查看預設記錄檔訊息。</span><span class="sxs-lookup"><span data-stu-id="574a1-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="574a1-726">額外的記錄功能，例如要求標頭的記錄，只會包含在追蹤層級。</span><span class="sxs-lookup"><span data-stu-id="574a1-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="574a1-727">用於每個用戶端的記錄檔分類包含用戶端的名稱。</span><span class="sxs-lookup"><span data-stu-id="574a1-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="574a1-728">例如，名為 *MyNamedClient* 的用戶端會記錄類別為的訊息 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 。</span><span class="sxs-lookup"><span data-stu-id="574a1-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="574a1-729">後面加上 *LogicalHandler* 的訊息發生在要求處理常式管線之外。</span><span class="sxs-lookup"><span data-stu-id="574a1-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="574a1-730">在要求中，訊息會在管線中任何其他處理常式處理它之前就記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="574a1-731">在回應中，訊息會在任何其他管線處理常式收到回應之後記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="574a1-732">記錄也會發生在要求處理常式管線之內。</span><span class="sxs-lookup"><span data-stu-id="574a1-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="574a1-733">在 *MyNamedClient* 範例中，那些訊息是針對記錄檔分類 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 而記錄。</span><span class="sxs-lookup"><span data-stu-id="574a1-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="574a1-734">對於要求，這是發生在所有其他處理常式都已執行之後，並且緊接在網路上傳送要求之前。</span><span class="sxs-lookup"><span data-stu-id="574a1-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="574a1-735">在回應中，此記錄會包含回應傳回通過處理常式管線之前的狀態。</span><span class="sxs-lookup"><span data-stu-id="574a1-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="574a1-736">在管線內外啟用記錄，可讓您檢查其他管線處理常式所做的變更。</span><span class="sxs-lookup"><span data-stu-id="574a1-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="574a1-737">例如，這可能包括要求標頭的變更，或是回應狀態碼的變更。</span><span class="sxs-lookup"><span data-stu-id="574a1-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="574a1-738">在記錄分類中包含用戶端的名稱，可讓您在需要時進行特定具名用戶端的記錄檔篩選。</span><span class="sxs-lookup"><span data-stu-id="574a1-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="574a1-739">設定 HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="574a1-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="574a1-740">可能需要控制用戶端使用之內部 `HttpMessageHandler` 的組態。</span><span class="sxs-lookup"><span data-stu-id="574a1-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="574a1-741">新增具名或具型別用戶端時，會傳回 `IHttpClientBuilder`。</span><span class="sxs-lookup"><span data-stu-id="574a1-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="574a1-742"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 擴充方法可以用來定義委派。</span><span class="sxs-lookup"><span data-stu-id="574a1-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="574a1-743">委派是用來建立及設定該用戶端所使用的主要 `HttpMessageHandler`：</span><span class="sxs-lookup"><span data-stu-id="574a1-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="574a1-744">在主控台應用程式中使用 IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="574a1-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="574a1-745">在主控台應用程式中，將下列套件參考新增至專案：</span><span class="sxs-lookup"><span data-stu-id="574a1-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="574a1-746">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="574a1-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="574a1-747">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="574a1-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="574a1-748">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="574a1-748">In the following example:</span></span>

* <span data-ttu-id="574a1-749"><xref:System.Net.Http.IHttpClientFactory> 已在[泛型主機的](xref:fundamentals/host/generic-host)服務容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="574a1-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="574a1-750">`MyService` 會從服務建立用戶端 Factory 執行個體，其可用來建立 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="574a1-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="574a1-751">`HttpClient` 會用來擷取網頁。</span><span class="sxs-lookup"><span data-stu-id="574a1-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="574a1-752">`Main` 會建立範圍來執行服務的 `GetPage` 方法，並將網頁內容的前 500 個字元寫入至主控台。</span><span class="sxs-lookup"><span data-stu-id="574a1-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="574a1-753">標頭傳播中介軟體</span><span class="sxs-lookup"><span data-stu-id="574a1-753">Header propagation middleware</span></span>

<span data-ttu-id="574a1-754">標頭傳播是一個支援仲介的中介軟體，可將來自傳入要求的 HTTP 標頭傳播至連出的 HTTP 用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="574a1-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="574a1-755">若要使用標頭傳播：</span><span class="sxs-lookup"><span data-stu-id="574a1-755">To use header propagation:</span></span>

* <span data-ttu-id="574a1-756">參考封裝 [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)的「支援的社區」埠。</span><span class="sxs-lookup"><span data-stu-id="574a1-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="574a1-757">ASP.NET Core 3.1 和更新版本支援 [AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)。</span><span class="sxs-lookup"><span data-stu-id="574a1-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="574a1-758">設定中介軟體和 `HttpClient` `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="574a1-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="574a1-759">用戶端會在輸出要求中包含已設定的標頭：</span><span class="sxs-lookup"><span data-stu-id="574a1-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="574a1-760">其他資源</span><span class="sxs-lookup"><span data-stu-id="574a1-760">Additional resources</span></span>

* [<span data-ttu-id="574a1-761">使用 HttpClientFactory 實作復原 HTTP 要求</span><span class="sxs-lookup"><span data-stu-id="574a1-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="574a1-762">使用 HttpClientFactory 和 Polly 原則以指數輪詢實作 HTTP 呼叫重試</span><span class="sxs-lookup"><span data-stu-id="574a1-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="574a1-763">實作斷路器模式</span><span class="sxs-lookup"><span data-stu-id="574a1-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
