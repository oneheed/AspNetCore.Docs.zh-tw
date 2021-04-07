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
ms.openlocfilehash: 2f0f7c5172e47ea4e44358191788c8153cbc2be5
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2021
ms.locfileid: "106563771"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>在 ASP.NET Core 中使用 IHttpClientFactory 發出 HTTP 要求

::: moniker range=">= aspnetcore-3.0"

[Kirk Larkin](https://github.com/serpent5)、 [Steve Gordon](https://github.com/stevejgordon)、 [Glenn Condron](https://github.com/glennc)和[Ryan Nowak](https://github.com/rynowak)。

<xref:System.Net.Http.IHttpClientFactory> 可以註冊及用來在應用程式中設定和建立 <xref:System.Net.Http.HttpClient> 執行個體。 `IHttpClientFactory` 提供下列優點：

* 提供一個集中位置以便命名和設定邏輯 `HttpClient` 執行個體。 例如，名為  *github* 的用戶端可以註冊並設定為存取 [github](https://github.com/)。 您可以註冊預設用戶端以進行一般存取。
* 在中委派處理常式，以會制訂傳出中介軟體的概念 `HttpClient` 。 提供 Polly 型中介軟體的擴充功能，以利用中的委派處理常式 `HttpClient` 。
* 管理基礎實例的共用和存留期 `HttpClientMessageHandler` 。 自動管理可避免在手動管理存留期時發生的常見 DNS (網域名稱系統) 問題 `HttpClient` 。
* 針對透過處理站所建立之用戶端傳送的所有要求，新增可設定的記錄體驗 (透過 `ILogger`)。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([如何下載](xref:index#how-to-download-a-sample))。

本主題的範例程式碼會用 <xref:System.Text.Json> 來還原序列化 HTTP 回應中傳回的 JSON 內容。 如需使用和的範例 `Json.NET` `ReadAsAsync<T>` ，請使用版本選擇器選取此主題的2.x 版本。

## <a name="consumption-patterns"></a>耗用量模式

有數種方式可將 `IHttpClientFactory` 用於應用程式：

* [基本使用方式](#basic-usage)
* [具名用戶端](#named-clients)
* [具型別用戶端](#typed-clients)
* [產生的用戶端](#generated-clients)

最佳方法取決於應用程式的需求。

### <a name="basic-usage"></a>基本使用方式

`IHttpClientFactory` 可以透過呼叫來註冊 `AddHttpClient` ：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

`IHttpClientFactory`可以使用相依性[插入 (DI) ](xref:fundamentals/dependency-injection)來要求。 下列程式碼會使用 `IHttpClientFactory` 來建立 `HttpClient` 實例：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

在 `IHttpClientFactory` 上述範例中使用 like，是重構現有應用程式的好方法。 它不會影響使用方式 `HttpClient` 。 在 `HttpClient` 現有應用程式中建立實例的位置，將這些發生的呼叫取代為的呼叫 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 。

### <a name="named-clients"></a>具名用戶端

在下列情況中，命名用戶端是不錯的選擇：

* 應用程式需要許多不同的用途 `HttpClient` 。
* 許多 `HttpClient` 具有不同的設定。

`HttpClient`在註冊期間，可以指定命名的設定 `Startup.ConfigureServices` ：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

在上述程式碼中，用戶端會以下列方式設定：

* 基底位址 `https://api.github.com/` 。
* 使用 GitHub API 時需要兩個標頭。

#### <a name="createclient"></a>CreateClient

每次 <xref:System.Net.Http.IHttpClientFactory.CreateClient*> 呼叫時：

* 建立新的實例 `HttpClient` 。
* 呼叫設定動作。

若要建立命名用戶端，請將其名稱傳遞至 `CreateClient` ：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

在上述程式碼中，要求不需要指定主機名稱。 由於使用為用戶端設定的基底位址，程式碼只可以傳遞路徑。

### <a name="typed-clients"></a>具型別用戶端

具型別用戶端：

* 提供與具名用戶端相同的功能，而不需使用字串作為索引鍵。
* 取用用戶端時提供 IntelliSense 和編譯器說明。
* 提供單一位置來設定特定的 `HttpClient` 並與其互動。 例如，可能會使用單一類型的用戶端：
  * 適用于單一後端端點。
  * 封裝處理端點的所有邏輯。
* 使用 DI，並可在應用程式需要的位置插入。

具型別用戶端會 `HttpClient` 在其函式中接受參數：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

在上述程式碼中：

* 設定會移至具類型的用戶端。
* `HttpClient` 物件會公開為公用屬性。

您可以建立可公開功能的 API 特定方法 `HttpClient` 。 例如，方法會 `GetAspNetDocsIssues` 封裝程式碼以取出開啟的問題。

下列程式碼會 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 在中呼叫 `Startup.ConfigureServices` 以註冊具類型的用戶端類別：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

具型別用戶端會向 DI 註冊為暫時性。 在上述程式碼中，將 `AddHttpClient` 註冊 `GitHubService` 為暫時性服務。 此註冊會使用 factory 方法來：

1. 建立 `HttpClient` 執行個體。
1. 建立的實例 `GitHubService` ，並將的實例傳遞至它的函式 `HttpClient` 。

具型別用戶端可以直接插入並使用：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

您可以在註冊期間指定具型別用戶端的設定 `Startup.ConfigureServices` ，而不是在具型別用戶端的函式中指定：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

`HttpClient`可以封裝在具類型的用戶端中。 您可以定義在內部呼叫實例的方法，而不是將它公開為屬性 `HttpClient` ：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

在上述程式碼中， `HttpClient` 會儲存在私用欄位中。 的存取 `HttpClient` 是由公用方法所存取 `GetRepos` 。

### <a name="generated-clients"></a>產生的用戶端

`IHttpClientFactory`可以搭配協力廠商程式庫（例如重新[調整）使用。](https://github.com/paulcbetts/refit) Refit 是適用於 .NET 的 REST 程式庫。 它將 REST API 轉換為即時介面。 介面的實作由 `RestService` 動態產生，並使用 `HttpClient` 進行外部 HTTP 呼叫。

定義介面及回覆來代表外部 API 和其回應：

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

可以新增具型別用戶端，使用 Refit 產生實作：

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

定義的介面可在需要時使用，並搭配 DI 與 Refit 所提供的實作：

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

## <a name="make-post-put-and-delete-requests"></a>提出 POST、PUT 和 DELETE 要求

在上述範例中，所有 HTTP 要求都會使用 GET HTTP 指令動詞。 `HttpClient` 也支援其他 HTTP 動詞命令，包括：

* POST
* PUT
* DELETE
* PATCH

如需支援之 HTTP 動詞命令的完整清單，請參閱 <xref:System.Net.Http.HttpMethod> 。

下列範例顯示如何提出 HTTP POST 要求：

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

在上述程式碼中， `CreateItemAsync` 方法如下：

* 使用將 `TodoItem` 參數序列化為 JSON `System.Text.Json` 。 這會使用的實例 <xref:System.Text.Json.JsonSerializerOptions> 來設定序列化進程。
* 建立的實例 <xref:System.Net.Http.StringContent> ，以封裝要在 HTTP 要求主體中傳送的序列化 JSON。
* 呼叫 <xref:System.Net.Http.HttpClient.PostAsync%2A> 以將 JSON 內容傳送至指定的 URL。 這是新增至 [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)的相對 URL。
* <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>如果回應狀態碼未指出成功，則呼叫會擲回例外狀況。

`HttpClient` 也支援其他類型的內容。 例如 <xref:System.Net.Http.MultipartContent> 和 <xref:System.Net.Http.StreamContent>。 如需支援之內容的完整清單，請參閱 <xref:System.Net.Http.HttpContent> 。

下列範例顯示 HTTP PUT 要求：

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

上述程式碼與 POST 範例非常類似。 `SaveItemAsync`方法會呼叫， <xref:System.Net.Http.HttpClient.PutAsync%2A> 而不是 `PostAsync` 。

下列範例顯示 HTTP DELETE 要求：

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

在上述程式碼中， `DeleteItemAsync` 方法會呼叫 <xref:System.Net.Http.HttpClient.DeleteAsync%2A> 。 因為 HTTP 刪除要求通常不包含任何主體，所以 `DeleteAsync` 方法不會提供接受實例的多載 `HttpContent` 。

若要深入瞭解如何搭配使用不同的 HTTP 動詞命令 `HttpClient` ，請參閱 <xref:System.Net.Http.HttpClient> 。

## <a name="outgoing-request-middleware"></a>外寄要求中介軟體

`HttpClient` 具有委派處理常式的概念，這些處理常式可以連結在一起以用於傳出的 HTTP 要求。 `IHttpClientFactory`:

  * 簡化定義要套用至每個已命名用戶端的處理常式。
  * 支援多個處理常式的註冊和連結，以建立外寄要求中介軟體管線。 這些處理常式都可以在傳出要求前後執行工作。 此模式：
  
    * 類似于 ASP.NET Core 中的輸入中介軟體管線。
    * 提供一種機制來管理 HTTP 要求的跨領域考慮，例如：
      * 快取
      * 錯誤處理
      * 序列化
      * logging

若要建立委派處理常式：

* 衍生自 <xref:System.Net.Http.DelegatingHandler> 。
* 覆寫 <xref:System.Net.Http.DelegatingHandler.SendAsync*>。 將要求傳遞至管線中的下一個處理常式之前，先執行程式碼：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

上述 `X-API-KEY` 程式碼會檢查標頭是否在要求中。 如果 `X-API-KEY` 遺失， <xref:System.Net.HttpStatusCode.BadRequest> 則會傳回。

您可以使用下列方式，將一個以上的處理常式新增至的設定 `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> ：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

在上述程式碼，`ValidateHeaderHandler` 已向 DI 註冊。 註冊之後，便可以呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，並傳入處理常式的類型。

可以遵循應該執行的順序來註冊多個處理常式。 每個處理常式會包裝下一個處理常式，直到最終 `HttpClientHandler` 執行要求：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a>在傳出要求中介軟體中使用 DI

當 `IHttpClientFactory` 建立新的委派處理常式時，它會使用 DI 來完成處理常式的函式參數。 `IHttpClientFactory` 為每個處理常式建立 **個別** 的 DI 範圍，當處理常式取用 *範圍* 服務時，可能會導致令人驚訝的行為。

例如，請考慮下列介面及其實作為作業（具有識別碼） `OperationId` ：

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

如其名所示， `IOperationScoped` 使用 *範圍* 存留期在 DI 註冊：

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

下列委派處理常式會使用和使用 `IOperationScoped` 來設定 `X-OPERATION-ID` 傳出要求的標頭：

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

在 [ [ `HttpRequestsSample` 下載](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)] 中，流覽 `/Operation` 並重新整理頁面。 每個要求的要求範圍值都有所變更，但處理常式範圍值只會每隔5秒變更一次。

處理常式可以視任何範圍的服務而定。 處置處理常式時，會處置處理常式所相依的服務。

使用下列其中一種方式來與訊息處理常式共用個別要求狀態：

* 使用 [HttpRequestMessage](xref:System.Net.Http.HttpRequestMessage.Properties)將資料傳遞到處理程式。
* 使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 來存取目前的要求。
* 建立自訂 <xref:System.Threading.AsyncLocal`1> 儲存體物件以傳遞資料。

## <a name="use-polly-based-handlers"></a>使用 Polly 為基礎的處理常式

`IHttpClientFactory` 整合協力廠商程式庫 [Polly](https://github.com/App-vNext/Polly)。 Polly 是適用於 .NET 的完整恢復功能和暫時性錯誤處理程式庫。 它可讓開發人員以流暢且執行緒安全的方式表達原則，例如重試、斷路器、逾時、艙隔離與後援。

提供擴充方法來啟用使用 Polly 原則搭配設定的 `HttpClient` 執行個體。 Polly 延伸模組支援將以 Polly 為基礎的處理常式新增至用戶端。 Polly 需要 [Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 套件。

### <a name="handle-transient-faults"></a>處理暫時性錯誤

當外部 HTTP 呼叫為暫時性時，通常會發生錯誤。 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 允許定義原則來處理暫時性錯誤。 使用設定的原則會 `AddTransientHttpErrorPolicy` 處理下列回應：

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy` 提供物件的存取權，此 `PolicyBuilder` 物件設定為處理代表可能暫時性錯誤的錯誤：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

在上述程式碼，已定義了 `WaitAndRetryAsync` 原則。 失敗的要求會重試最多三次，並且在嘗試之間會有 600 毫秒的延遲時間。

### <a name="dynamically-select-policies"></a>動態選取原則

提供擴充方法來新增以 Polly 為基礎的處理常式，例如 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> 。 下列多載會 `AddPolicyHandler` 檢查要求，以決定要套用的原則：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

在上述程式碼中，如果外寄要求是 HTTP GET，就會套用 10 秒逾時。 任何其他 HTTP 方法會使用 30 秒逾時。

### <a name="add-multiple-polly-handlers"></a>新增多個 Polly 處理常式

嵌套 Polly 原則是很常見的：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

在上述範例中：

* 會加入兩個處理常式。
* 第一個處理常式會使用 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> 來新增重試原則。 失敗的要求會重試最多三次。
* 第二次 `AddTransientHttpErrorPolicy` 呼叫會加入斷路器原則。 如果有5次失敗的嘗試順序，則會封鎖進一步的外部要求30秒。 斷路器原則可設定狀態。 透過此用戶端的所有呼叫都會共用相同的線路狀態。

### <a name="add-policies-from-the-polly-registry"></a>從 Polly 登錄新增原則

管理定期使用原則的一個方法是定義一次，並向 `PolicyRegistry` 註冊它們。

在下列程式碼中：

* 新增「一般」和「長」原則。
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> 從登錄新增「一般」和「長」原則。

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

如需和 Polly 整合的詳細資訊 `IHttpClientFactory` ，請參閱 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)。

## <a name="httpclient-and-lifetime-management"></a>HttpClient 和存留期管理

每次在 `IHttpClientFactory` 上呼叫 `CreateClient` 時，都會傳回新的 `HttpClient` 執行個體。 為 <xref:System.Net.Http.HttpMessageHandler> 每個命名的用戶端建立。 處理站會管理 `HttpMessageHandler` 執行個體的存留期。

`IHttpClientFactory` 會將處理站所建立的 `HttpMessageHandler` 執行個體放入集區以減少資源耗用量。 建立新的 `HttpClient` 執行個體時，如果其存留期間尚未過期，`HttpMessageHandler` 執行個體可從集區重複使用。

將處理常式放入集區非常實用，因為處理常式通常會管理自己專屬的底層 HTTP 連線。 建立比所需數目更多的處理常式，可能會導致連線延遲。 某些處理常式也會無限期地開啟連接，這可能會導致處理常式無法對 DNS (的網域名稱系統進行回應) 變更。

預設處理常式存留時間為兩分鐘。 您可以根據每個名稱的用戶端來覆寫預設值：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient` 實例通常可視為 **不** 需要處置的 .net 物件。 處置會取消傳出的要求，並保證指定的 `HttpClient` 執行個體在呼叫 <xref:System.IDisposable.Dispose*> 之後無法使用。 `IHttpClientFactory` 會追蹤並處置 `HttpClient` 執行個體使用的資源。

在開始使用 `IHttpClientFactory` 之前，讓單一 `HttpClient` 執行個體維持一段較長的時間，是很常使用的模式。 在移轉到 `IHttpClientFactory` 之後，就不再需要此模式。

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory 的替代方案

`IHttpClientFactory`在啟用 DI 的應用程式中使用，可避免：

* 共用實例的資源耗盡問題 `HttpMessageHandler` 。
* 定期迴圈實例的過時 DNS 問題 `HttpMessageHandler` 。

有其他方法可以解決上述問題，方法是使用長期的 <xref:System.Net.Http.SocketsHttpHandler> 實例。

- 在 `SocketsHttpHandler` 應用程式啟動時建立的實例，並在應用程式的存留期內使用它。
- <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>根據 DNS 重新整理時間設定為適當的值。
- `HttpClient`視需要使用建立實例 `new HttpClient(handler, disposeHandler: false)` 。

上述方法可解決以類似的方式解決的資源管理問題 `IHttpClientFactory` 。

- `SocketsHttpHandler`共用實例之間的 `HttpClient` 連接。 這種共用可防止通訊端耗盡。
- 會 `SocketsHttpHandler` 根據來迴圈連接 `PooledConnectionLifetime` ，以避免過時的 DNS 問題。

### <a name="cookies"></a>Cookie

集區 `HttpMessageHandler` 實例會產生 `CookieContainer` 共用的物件。 非預期 `CookieContainer` 的物件共用通常會導致不正確的程式碼。 針對需要的應用程式 cookie ，請考慮下列其中一項：

 - 停用自動 cookie 處理
 - 避免 `IHttpClientFactory`

呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以停用自動 cookie 處理：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>記錄

透過 `IHttpClientFactory` 建立的用戶端會記錄所有要求的記錄訊息。 在記錄設定中啟用適當的資訊層級，以查看預設記錄檔訊息。 額外的記錄功能，例如要求標頭的記錄，只會包含在追蹤層級。

用於每個用戶端的記錄檔分類包含用戶端的名稱。 例如，名為 *MyNamedClient* 的用戶端會記錄類別為 "HttpClient" 的訊息。**MyNamedClient**。LogicalHandler". 後面加上 *LogicalHandler* 的訊息發生在要求處理常式管線之外。 在要求中，訊息會在管線中任何其他處理常式處理它之前就記錄。 在回應中，訊息會在任何其他管線處理常式收到回應之後記錄。

記錄也會發生在要求處理常式管線之內。 在 *MyNamedClient* 範例中，這些訊息會記錄在記錄檔類別中為 "HttpClient"。**MyNamedClient**。ClientHandler". 針對要求，這會發生在所有其他處理常式都已執行，且在傳送要求之前立即執行。 在回應中，此記錄會包含回應傳回通過處理常式管線之前的狀態。

在管線內外啟用記錄，可讓您檢查其他管線處理常式所做的變更。 這可能包括對要求標頭的變更或回應狀態碼的變更。

在記錄類別中包含用戶端的名稱，可讓您針對特定的已命名用戶端進行記錄篩選。

## <a name="configure-the-httpmessagehandler"></a>設定 HttpMessageHandler

可能需要控制用戶端使用之內部 `HttpMessageHandler` 的組態。

新增具名或具型別用戶端時，會傳回 `IHttpClientBuilder`。 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 擴充方法可以用來定義委派。 委派是用來建立及設定該用戶端所使用的主要 `HttpMessageHandler`：

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>在主控台應用程式中使用 IHttpClientFactory

在主控台應用程式中，將下列套件參考新增至專案：

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

在下例中︰

* <xref:System.Net.Http.IHttpClientFactory> 已在[泛型主機的](xref:fundamentals/host/generic-host)服務容器中註冊。
* `MyService` 會從服務建立用戶端 Factory 執行個體，其可用來建立 `HttpClient`。 `HttpClient` 會用來擷取網頁。
* `Main` 會建立範圍來執行服務的 `GetPage` 方法，並將網頁內容的前 500 個字元寫入至主控台。

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>標頭傳播中介軟體

標頭傳播是 ASP.NET Core 中介軟體，用來將傳入要求的 HTTP 標頭傳播至傳出 HTTP 用戶端要求。 若要使用標頭傳播：

* 參考 [AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 套件。
* 設定中介軟體和 `HttpClient` `Startup` ：

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* 用戶端會在輸出要求中包含已設定的標頭：

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>其他資源

* [使用 HttpClientFactory 實作復原 HTTP 要求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [使用 HttpClientFactory 和 Polly 原則以指數輪詢實作 HTTP 呼叫重試](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [實作斷路器模式](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [如何在 .NET 中序列化和還原序列化 JSON](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory> 可以註冊及用來在應用程式中設定和建立 <xref:System.Net.Http.HttpClient> 執行個體。 它提供下列優點：

* 提供一個集中位置以便命名和設定邏輯 `HttpClient` 執行個體。 例如，您可以註冊並設定 *github* 用戶端以存取 [github](https://github.com/)。 預設用戶端可以註冊用於其他用途。
* 透過委派 `HttpClient` 中的處理常式來撰寫外寄中介軟體的概念，並提供延伸模組以便 Polly 架構中介軟體利用外寄中介軟體。
* 管理基礎 `HttpClientMessageHandler` 執行個體的共用和存留期，以避免在手動管理 `HttpClient` 存留期時，發生的常見 DNS 問題。
* 針對透過處理站所建立之用戶端傳送的所有要求，新增可設定的記錄體驗 (透過 `ILogger`)。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="consumption-patterns"></a>耗用量模式

有數種方式可將 `IHttpClientFactory` 用於應用程式：

* [基本使用方式](#basic-usage)
* [具名用戶端](#named-clients)
* [具型別用戶端](#typed-clients)
* [產生的用戶端](#generated-clients)

它們全都不會嚴格優先於另一個。 最好的方法取決於應用程式的條件約束。

### <a name="basic-usage"></a>基本使用方式

`IHttpClientFactory` 可以藉由在 `Startup.ConfigureServices` 方法內的 `IServiceCollection` 上呼叫 `AddHttpClient` 擴充方法來註冊。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

註冊之後，程式碼可以接受可透過相依性 `IHttpClientFactory` [插入 (DI) ](xref:fundamentals/dependency-injection)插入的任何位置服務。 `IHttpClientFactory`可以用來建立 `HttpClient` 實例：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

以這種方式使用 `IHttpClientFactory` 是重構現有應用程式的好方法。 它對 `HttpClient` 的使用方式沒有任何影響。 在目前建立 `HttpClient` 執行個體的位置，將那些項目取代為呼叫 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>。

### <a name="named-clients"></a>具名用戶端

如果應用程式需要使用多個不同的 `HttpClient`，且每個都有不同的設定，可以選擇使用 **具名用戶端**。 具名 `HttpClient` 的組態可以在 `Startup.ConfigureServices` 中註冊時指定。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

在上述程式碼中， `AddHttpClient` 會呼叫，並提供名稱 *github*。 此用戶端已套用一些預設組態&mdash;即使用 GitHub API 所需的基底位址和兩個標頭。

每次呼叫 `CreateClient` 時，會建立 `HttpClient` 的新執行個體並呼叫組態動作。

若要使用具名用戶端，可以傳遞字串參數至 `CreateClient`。 指定要建立之用戶端的名稱：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

在上述程式碼中，要求不需要指定主機名稱。 它可以只傳遞路徑，因為已使用為用戶端設定的基底位址。

### <a name="typed-clients"></a>具型別用戶端

具型別用戶端：

* 提供與具名用戶端相同的功能，而不需使用字串作為索引鍵。
* 取用用戶端時提供 IntelliSense 和編譯器說明。
* 提供單一位置來設定特定的 `HttpClient` 並與其互動。 例如，單一的具型別用戶端可能用於單一的後端端點，並封裝處理該端點的所有邏輯。
* 使用 DI 且可在應用程式中需要之處插入。

具型別用戶端會 `HttpClient` 在其函式中接受參數：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

在上述程式碼中，組態會移到具型別用戶端。 `HttpClient` 物件會公開為公用屬性。 您可定義 API 特定的方法，其公開 `HttpClient` 功能。 `GetAspNetDocsIssues` 方法會封裝從 GitHub 存放庫查詢和剖析最新開啟問題所需的程式碼。

若要註冊具型別用戶端，泛型 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 擴充方法可用於 `Startup.ConfigureServices` 內，並指定具型別用戶端類別：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

具型別用戶端會向 DI 註冊為暫時性。 具型別用戶端可以直接插入並使用：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

想要的話，具型別用戶端的組態可以在 `Startup.ConfigureServices` 中註冊時指定，而不是在具型別用戶端的建構函式中：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

可以將 `HttpClient` 完全封裝在具型別用戶端內。 可以提供在內部呼叫 `HttpClient` 執行個體的公用方法，而不將它公開為屬性。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

在上述程式碼，`HttpClient` 儲存為私用欄位。 進行外部呼叫的所有存取都會經歷 `GetRepos` 方法。

### <a name="generated-clients"></a>產生的用戶端

`IHttpClientFactory` 可和其他協力廠商程式庫一起使用，例如 [Refit](https://github.com/paulcbetts/refit)。 Refit 是適用於 .NET 的 REST 程式庫。 它將 REST API 轉換為即時介面。 介面的實作由 `RestService` 動態產生，並使用 `HttpClient` 進行外部 HTTP 呼叫。

定義介面及回覆來代表外部 API 和其回應：

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

可以新增具型別用戶端，使用 Refit 產生實作：

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

定義的介面可在需要時使用，並搭配 DI 與 Refit 所提供的實作：

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

## <a name="outgoing-request-middleware"></a>外寄要求中介軟體

`HttpClient` 已經有委派可針對外寄 HTTP 要求連結在一起的處理常式的概念。 `IHttpClientFactory` 可讓您輕鬆地定義要套用於每個具名用戶端的處理常式。 它支援註冊和鏈結多個處理常式，以建置外寄要求中介軟體管線。 這些處理常式每個都可以在外寄要求之前和之後執行工作。 此模式與 ASP.NET Core 中的輸入中介軟體管線相似。 模式提供一個機制來管理 HTTP 要求的跨領域關注，包括快取、錯誤處理、序列化和記錄。

若要建立處理常式，請定義衍生自 <xref:System.Net.Http.DelegatingHandler> 的類別。 覆寫 `SendAsync` 方法，以在將要求傳遞至管線中的下一個處理常式之前執行程式碼：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

上述程式碼定義一個基本處理常式。 它會檢查以查看要求上是否已包含 `X-API-KEY` 標頭。 如果遺漏標頭，它可以避免 HTTP 呼叫，並傳回適當的回應。

在註冊期間，可以將一或多個處理常式新增至的設定 `HttpClient` 。 這項工作是透過 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的擴充方法完成。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

在上述程式碼，`ValidateHeaderHandler` 已向 DI 註冊。 `IHttpClientFactory` 會為每個處理常式建立個別的 DI 範圍。 處理常式可相依於任何範圍的服務。 處置處理常式時，會處置處理常式所相依的服務。

註冊之後，便可以呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，並傳入處理常式的類型。

可以遵循應該執行的順序來註冊多個處理常式。 每個處理常式會包裝下一個處理常式，直到最終 `HttpClientHandler` 執行要求：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

使用下列其中一種方式來與訊息處理常式共用個別要求狀態：

* 使用 `HttpRequestMessage.Properties` 將資料傳遞到處理常式。
* 使用 `IHttpContextAccessor` 來存取目前的要求。
* 建立自訂 `AsyncLocal` 儲存體物件以傳遞資料。

## <a name="use-polly-based-handlers"></a>使用 Polly 為基礎的處理常式

`IHttpClientFactory` 整合受歡迎的協力廠商程式庫，稱為 [Polly](https://github.com/App-vNext/Polly)。 Polly 是適用於 .NET 的完整恢復功能和暫時性錯誤處理程式庫。 它可讓開發人員以流暢且執行緒安全的方式表達原則，例如重試、斷路器、逾時、艙隔離與後援。

提供擴充方法來啟用使用 Polly 原則搭配設定的 `HttpClient` 執行個體。 Polly 延伸模組：

* 支援將以 Polly 為基礎的處理常式新增至用戶端。
* 可在安裝 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 套件後使用。 該套件並未包含在 ASP.NET Core 共用架構中。

### <a name="handle-transient-faults"></a>處理暫時性錯誤

大部分的錯誤發生在外部 HTTP 呼叫是暫時性的時候。 包含一個便利的擴充方法，稱為 `AddTransientHttpErrorPolicy`，它可允許定義原則來處理暫時性錯誤。 使用此延伸模組方法設定的原則，會處理 `HttpRequestException`、HTTP 5xx 回應和 HTTP 408 回應。

`AddTransientHttpErrorPolicy` 延伸模組可用於 `Startup.ConfigureServices` 內。 延伸模組能提供 `PolicyBuilder` 物件的存取，該物件已設定來處理代表可能暫時性錯誤的錯誤：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

在上述程式碼，已定義了 `WaitAndRetryAsync` 原則。 失敗的要求會重試最多三次，並且在嘗試之間會有 600 毫秒的延遲時間。

### <a name="dynamically-select-policies"></a>動態選取原則

有額外的擴充方法可用來新增 Polly 為基礎的處理常式。 其中一個這類延伸模組是 `AddPolicyHandler`，它有多個多載。 一個多載可讓您在定義要套用的原則時，檢查要求：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

在上述程式碼中，如果外寄要求是 HTTP GET，就會套用 10 秒逾時。 任何其他 HTTP 方法會使用 30 秒逾時。

### <a name="add-multiple-polly-handlers"></a>新增多個 Polly 處理常式

通常會建立巢狀 Polly 原則，以提供增強的功能：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

在上述範例中，會新增兩個處理常式。 第一個使用 `AddTransientHttpErrorPolicy` 延伸模組來新增重試原則。 失敗的要求會重試最多三次。 第二個 `AddTransientHttpErrorPolicy` 呼叫會新增斷路器原則。 如果循序發生五次失敗的嘗試，進一步的外部要求會遭到封鎖 30 秒。 斷路器原則可設定狀態。 透過此用戶端的所有呼叫都會共用相同的線路狀態。

### <a name="add-policies-from-the-polly-registry"></a>從 Polly 登錄新增原則

管理定期使用原則的一個方法是定義一次，並向 `PolicyRegistry` 註冊它們。 提供了擴充方法，可以使用來自登錄的原則新增處理常式：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

在上述程式碼中，當 `PolicyRegistry` 新增至 `ServiceCollection` 時，註冊了兩個原則。 為了使用來自登錄的原則，使用了 `AddPolicyHandlerFromRegistry` 方法，並傳遞要套用的原則名稱。

關於 `IHttpClientFactory` Polly 整合的詳細資訊，可以在 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) 上找到。

## <a name="httpclient-and-lifetime-management"></a>HttpClient 和存留期管理

每次在 `IHttpClientFactory` 上呼叫 `CreateClient` 時，都會傳回新的 `HttpClient` 執行個體。 每個具名用戶端都有一個 <xref:System.Net.Http.HttpMessageHandler>。 處理站會管理 `HttpMessageHandler` 執行個體的存留期。

`IHttpClientFactory` 會將處理站所建立的 `HttpMessageHandler` 執行個體放入集區以減少資源耗用量。 建立新的 `HttpClient` 執行個體時，如果其存留期間尚未過期，`HttpMessageHandler` 執行個體可從集區重複使用。

將處理常式放入集區非常實用，因為處理常式通常會管理自己專屬的底層 HTTP 連線。 建立比所需數目更多的處理常式，可能會導致連線延遲。 有些處理常式也會保持連線無限期地開啟，這可能導致處理常式無法對 DNS 變更回應。

預設處理常式存留時間為兩分鐘。 可以針對每個具名用戶端覆寫預設值。 若要覆寫它，請在建立用戶端時所傳回的 `IHttpClientBuilder` 上呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

不需要處置用戶端。 處置會取消傳出的要求，並保證指定的 `HttpClient` 執行個體在呼叫 <xref:System.IDisposable.Dispose*> 之後無法使用。 `IHttpClientFactory` 會追蹤並處置 `HttpClient` 執行個體使用的資源。 `HttpClient` 執行個體通常可視為 .NET 物件，不需要處置。

在開始使用 `IHttpClientFactory` 之前，讓單一 `HttpClient` 執行個體維持一段較長的時間，是很常使用的模式。 在移轉到 `IHttpClientFactory` 之後，就不再需要此模式。

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory 的替代方案

`IHttpClientFactory`在啟用 DI 的應用程式中使用，可避免：

* 共用實例的資源耗盡問題 `HttpMessageHandler` 。
* 定期迴圈實例的過時 DNS 問題 `HttpMessageHandler` 。

有其他方法可以解決上述問題，方法是使用長期的 <xref:System.Net.Http.SocketsHttpHandler> 實例。

- 在 `SocketsHttpHandler` 應用程式啟動時建立的實例，並在應用程式的存留期內使用它。
- <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>根據 DNS 重新整理時間設定為適當的值。
- `HttpClient`視需要使用建立實例 `new HttpClient(handler, disposeHandler: false)` 。

上述方法可解決以類似的方式解決的資源管理問題 `IHttpClientFactory` 。

- `SocketsHttpHandler`共用實例之間的 `HttpClient` 連接。 這種共用可防止通訊端耗盡。
- 會 `SocketsHttpHandler` 根據來迴圈連接 `PooledConnectionLifetime` ，以避免過時的 DNS 問題。

### <a name="cookies"></a>Cookie

集區 `HttpMessageHandler` 實例會產生 `CookieContainer` 共用的物件。 非預期 `CookieContainer` 的物件共用通常會導致不正確的程式碼。 針對需要的應用程式 cookie ，請考慮下列其中一項：

 - 停用自動 cookie 處理
 - 避免 `IHttpClientFactory`

呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以停用自動 cookie 處理：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>記錄

透過 `IHttpClientFactory` 建立的用戶端會記錄所有要求的記錄訊息。 在記錄設定中啟用適當的資訊層級，以查看預設記錄檔訊息。 額外的記錄功能，例如要求標頭的記錄，只會包含在追蹤層級。

用於每個用戶端的記錄檔分類包含用戶端的名稱。 例如，名為 *MyNamedClient* 的用戶端會記錄類別為的訊息 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 。 後面加上 *LogicalHandler* 的訊息發生在要求處理常式管線之外。 在要求中，訊息會在管線中任何其他處理常式處理它之前就記錄。 在回應中，訊息會在任何其他管線處理常式收到回應之後記錄。

記錄也會發生在要求處理常式管線之內。 在 *MyNamedClient* 範例中，那些訊息是針對記錄檔分類 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 而記錄。 對於要求，這是發生在所有其他處理常式都已執行之後，並且緊接在網路上傳送要求之前。 在回應中，此記錄會包含回應傳回通過處理常式管線之前的狀態。

在管線內外啟用記錄，可讓您檢查其他管線處理常式所做的變更。 例如，這可能包括要求標頭的變更，或是回應狀態碼的變更。

在記錄分類中包含用戶端的名稱，可讓您在需要時進行特定具名用戶端的記錄檔篩選。

## <a name="configure-the-httpmessagehandler"></a>設定 HttpMessageHandler

可能需要控制用戶端使用之內部 `HttpMessageHandler` 的組態。

新增具名或具型別用戶端時，會傳回 `IHttpClientBuilder`。 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 擴充方法可以用來定義委派。 委派是用來建立及設定該用戶端所使用的主要 `HttpMessageHandler`：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>在主控台應用程式中使用 IHttpClientFactory

在主控台應用程式中，將下列套件參考新增至專案：

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

在下例中︰

* <xref:System.Net.Http.IHttpClientFactory> 已在[泛型主機的](xref:fundamentals/host/generic-host)服務容器中註冊。
* `MyService` 會從服務建立用戶端 Factory 執行個體，其可用來建立 `HttpClient`。 `HttpClient` 會用來擷取網頁。
* `Main` 會建立範圍來執行服務的 `GetPage` 方法，並將網頁內容的前 500 個字元寫入至主控台。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>其他資源

* [使用 HttpClientFactory 實作復原 HTTP 要求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [使用 HttpClientFactory 和 Polly 原則以指數輪詢實作 HTTP 呼叫重試](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [實作斷路器模式](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

作者：[Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak) 和 [Steve Gordon](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory> 可以註冊及用來在應用程式中設定和建立 <xref:System.Net.Http.HttpClient> 執行個體。 它提供下列優點：

* 提供一個集中位置以便命名和設定邏輯 `HttpClient` 執行個體。 例如，您可以註冊並設定 *github* 用戶端以存取 [github](https://github.com/)。 預設用戶端可以註冊用於其他用途。
* 透過委派 `HttpClient` 中的處理常式來撰寫外寄中介軟體的概念，並提供延伸模組以便 Polly 架構中介軟體利用外寄中介軟體。
* 管理基礎 `HttpClientMessageHandler` 執行個體的共用和存留期，以避免在手動管理 `HttpClient` 存留期時，發生的常見 DNS 問題。
* 針對透過處理站所建立之用戶端傳送的所有要求，新增可設定的記錄體驗 (透過 `ILogger`)。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/http-requests/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

以 .NET Framework 為目標的專案，需要安裝 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 套件。 以 .NET Core 為目標且參考 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 的專案，已包含 `Microsoft.Extensions.Http` 套件。

## <a name="consumption-patterns"></a>耗用量模式

有數種方式可將 `IHttpClientFactory` 用於應用程式：

* [基本使用方式](#basic-usage)
* [具名用戶端](#named-clients)
* [具型別用戶端](#typed-clients)
* [產生的用戶端](#generated-clients)

它們全都不會嚴格優先於另一個。 最好的方法取決於應用程式的條件約束。

### <a name="basic-usage"></a>基本使用方式

`IHttpClientFactory` 可以藉由在 `Startup.ConfigureServices` 方法內的 `IServiceCollection` 上呼叫 `AddHttpClient` 擴充方法來註冊。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

註冊之後，程式碼可以接受可透過相依性 `IHttpClientFactory` [插入 (DI) ](xref:fundamentals/dependency-injection)插入的任何位置服務。 `IHttpClientFactory`可以用來建立 `HttpClient` 實例：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

以這種方式使用 `IHttpClientFactory` 是重構現有應用程式的好方法。 它對 `HttpClient` 的使用方式沒有任何影響。 在目前建立 `HttpClient` 執行個體的位置，將那些項目取代為呼叫 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>。

### <a name="named-clients"></a>具名用戶端

如果應用程式需要使用多個不同的 `HttpClient`，且每個都有不同的設定，可以選擇使用 **具名用戶端**。 具名 `HttpClient` 的組態可以在 `Startup.ConfigureServices` 中註冊時指定。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

在上述程式碼中， `AddHttpClient` 會呼叫，並提供名稱 *github*。 此用戶端已套用一些預設組態&mdash;即使用 GitHub API 所需的基底位址和兩個標頭。

每次呼叫 `CreateClient` 時，會建立 `HttpClient` 的新執行個體並呼叫組態動作。

若要使用具名用戶端，可以傳遞字串參數至 `CreateClient`。 指定要建立之用戶端的名稱：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

在上述程式碼中，要求不需要指定主機名稱。 它可以只傳遞路徑，因為已使用為用戶端設定的基底位址。

### <a name="typed-clients"></a>具型別用戶端

具型別用戶端：

* 提供與具名用戶端相同的功能，而不需使用字串作為索引鍵。
* 取用用戶端時提供 IntelliSense 和編譯器說明。
* 提供單一位置來設定特定的 `HttpClient` 並與其互動。 例如，單一的具型別用戶端可能用於單一的後端端點，並封裝處理該端點的所有邏輯。
* 使用 DI 且可在應用程式中需要之處插入。

具型別用戶端會 `HttpClient` 在其函式中接受參數：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

在上述程式碼中，組態會移到具型別用戶端。 `HttpClient` 物件會公開為公用屬性。 您可定義 API 特定的方法，其公開 `HttpClient` 功能。 `GetAspNetDocsIssues` 方法會封裝從 GitHub 存放庫查詢和剖析最新開啟問題所需的程式碼。

若要註冊具型別用戶端，泛型 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 擴充方法可用於 `Startup.ConfigureServices` 內，並指定具型別用戶端類別：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

具型別用戶端會向 DI 註冊為暫時性。 具型別用戶端可以直接插入並使用：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

想要的話，具型別用戶端的組態可以在 `Startup.ConfigureServices` 中註冊時指定，而不是在具型別用戶端的建構函式中：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

可以將 `HttpClient` 完全封裝在具型別用戶端內。 可以提供在內部呼叫 `HttpClient` 執行個體的公用方法，而不將它公開為屬性。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

在上述程式碼，`HttpClient` 儲存為私用欄位。 進行外部呼叫的所有存取都會經歷 `GetRepos` 方法。

### <a name="generated-clients"></a>產生的用戶端

`IHttpClientFactory` 可和其他協力廠商程式庫一起使用，例如 [Refit](https://github.com/paulcbetts/refit)。 Refit 是適用於 .NET 的 REST 程式庫。 它將 REST API 轉換為即時介面。 介面的實作由 `RestService` 動態產生，並使用 `HttpClient` 進行外部 HTTP 呼叫。

定義介面及回覆來代表外部 API 和其回應：

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

可以新增具型別用戶端，使用 Refit 產生實作：

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

定義的介面可在需要時使用，並搭配 DI 與 Refit 所提供的實作：

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

## <a name="outgoing-request-middleware"></a>外寄要求中介軟體

`HttpClient` 已經有委派可針對外寄 HTTP 要求連結在一起的處理常式的概念。 `IHttpClientFactory` 可讓您輕鬆地定義要套用於每個具名用戶端的處理常式。 它支援註冊和鏈結多個處理常式，以建置外寄要求中介軟體管線。 這些處理常式每個都可以在外寄要求之前和之後執行工作。 此模式與 ASP.NET Core 中的輸入中介軟體管線相似。 模式提供一個機制來管理 HTTP 要求的跨領域關注，包括快取、錯誤處理、序列化和記錄。

若要建立處理常式，請定義衍生自 <xref:System.Net.Http.DelegatingHandler> 的類別。 覆寫 `SendAsync` 方法，以在將要求傳遞至管線中的下一個處理常式之前執行程式碼：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

上述程式碼定義一個基本處理常式。 它會檢查以查看要求上是否已包含 `X-API-KEY` 標頭。 如果遺漏標頭，它可以避免 HTTP 呼叫，並傳回適當的回應。

在註冊期間，可以將一或多個處理常式新增至的設定 `HttpClient` 。 這項工作是透過 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> 上的擴充方法完成。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

在上述程式碼，`ValidateHeaderHandler` 已向 DI 註冊。 處理常式 **必須** 在 DI 中註冊為暫時性服務，無限定範圍。 如果處理常式已註冊為範圍服務，而且處理常式所相依的任何服務都是可處置的：

* 處理常式的服務可能會在處理常式超出範圍之前加以處置。
* 已處置的處理常式服務會導致處理常式失敗。

註冊之後，便可以呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>，並傳入處理常式類型。

可以遵循應該執行的順序來註冊多個處理常式。 每個處理常式會包裝下一個處理常式，直到最終 `HttpClientHandler` 執行要求：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

使用下列其中一種方式來與訊息處理常式共用個別要求狀態：

* 使用 `HttpRequestMessage.Properties` 將資料傳遞到處理常式。
* 使用 `IHttpContextAccessor` 來存取目前的要求。
* 建立自訂 `AsyncLocal` 儲存體物件以傳遞資料。

## <a name="use-polly-based-handlers"></a>使用 Polly 為基礎的處理常式

`IHttpClientFactory` 整合受歡迎的協力廠商程式庫，稱為 [Polly](https://github.com/App-vNext/Polly)。 Polly 是適用於 .NET 的完整恢復功能和暫時性錯誤處理程式庫。 它可讓開發人員以流暢且執行緒安全的方式表達原則，例如重試、斷路器、逾時、艙隔離與後援。

提供擴充方法來啟用使用 Polly 原則搭配設定的 `HttpClient` 執行個體。 Polly 延伸模組：

* 支援將以 Polly 為基礎的處理常式新增至用戶端。
* 可在安裝 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 套件後使用。 該套件並未包含在 ASP.NET Core 共用架構中。

### <a name="handle-transient-faults"></a>處理暫時性錯誤

大部分的錯誤發生在外部 HTTP 呼叫是暫時性的時候。 包含一個便利的擴充方法，稱為 `AddTransientHttpErrorPolicy`，它可允許定義原則來處理暫時性錯誤。 使用此延伸模組方法設定的原則，會處理 `HttpRequestException`、HTTP 5xx 回應和 HTTP 408 回應。

`AddTransientHttpErrorPolicy` 延伸模組可用於 `Startup.ConfigureServices` 內。 延伸模組能提供 `PolicyBuilder` 物件的存取，該物件已設定來處理代表可能暫時性錯誤的錯誤：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

在上述程式碼，已定義了 `WaitAndRetryAsync` 原則。 失敗的要求會重試最多三次，並且在嘗試之間會有 600 毫秒的延遲時間。

### <a name="dynamically-select-policies"></a>動態選取原則

有額外的擴充方法可用來新增 Polly 為基礎的處理常式。 其中一個這類延伸模組是 `AddPolicyHandler`，它有多個多載。 一個多載可讓您在定義要套用的原則時，檢查要求：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

在上述程式碼中，如果外寄要求是 HTTP GET，就會套用 10 秒逾時。 任何其他 HTTP 方法會使用 30 秒逾時。

### <a name="add-multiple-polly-handlers"></a>新增多個 Polly 處理常式

通常會建立巢狀 Polly 原則，以提供增強的功能：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

在上述範例中，會新增兩個處理常式。 第一個使用 `AddTransientHttpErrorPolicy` 延伸模組來新增重試原則。 失敗的要求會重試最多三次。 第二個 `AddTransientHttpErrorPolicy` 呼叫會新增斷路器原則。 如果循序發生五次失敗的嘗試，進一步的外部要求會遭到封鎖 30 秒。 斷路器原則可設定狀態。 透過此用戶端的所有呼叫都會共用相同的線路狀態。

### <a name="add-policies-from-the-polly-registry"></a>從 Polly 登錄新增原則

管理定期使用原則的一個方法是定義一次，並向 `PolicyRegistry` 註冊它們。 提供了擴充方法，可以使用來自登錄的原則新增處理常式：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

在上述程式碼中，當 `PolicyRegistry` 新增至 `ServiceCollection` 時，註冊了兩個原則。 為了使用來自登錄的原則，使用了 `AddPolicyHandlerFromRegistry` 方法，並傳遞要套用的原則名稱。

關於 `IHttpClientFactory` Polly 整合的詳細資訊，可以在 [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) 上找到。

## <a name="httpclient-and-lifetime-management"></a>HttpClient 和存留期管理

每次在 `IHttpClientFactory` 上呼叫 `CreateClient` 時，都會傳回新的 `HttpClient` 執行個體。 每個具名用戶端都有一個 <xref:System.Net.Http.HttpMessageHandler>。 處理站會管理 `HttpMessageHandler` 執行個體的存留期。

`IHttpClientFactory` 會將處理站所建立的 `HttpMessageHandler` 執行個體放入集區以減少資源耗用量。 建立新的 `HttpClient` 執行個體時，如果其存留期間尚未過期，`HttpMessageHandler` 執行個體可從集區重複使用。

將處理常式放入集區非常實用，因為處理常式通常會管理自己專屬的底層 HTTP 連線。 建立比所需數目更多的處理常式，可能會導致連線延遲。 有些處理常式也會保持連線無限期地開啟，這可能導致處理常式無法對 DNS 變更回應。

預設處理常式存留時間為兩分鐘。 可以針對每個具名用戶端覆寫預設值。 若要覆寫它，請在建立用戶端時所傳回的 `IHttpClientBuilder` 上呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

不需要處置用戶端。 處置會取消傳出的要求，並保證指定的 `HttpClient` 執行個體在呼叫 <xref:System.IDisposable.Dispose*> 之後無法使用。 `IHttpClientFactory` 會追蹤並處置 `HttpClient` 執行個體使用的資源。 `HttpClient` 執行個體通常可視為 .NET 物件，不需要處置。

在開始使用 `IHttpClientFactory` 之前，讓單一 `HttpClient` 執行個體維持一段較長的時間，是很常使用的模式。 在移轉到 `IHttpClientFactory` 之後，就不再需要此模式。

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory 的替代方案

`IHttpClientFactory`在啟用 DI 的應用程式中使用，可避免：

* 共用實例的資源耗盡問題 `HttpMessageHandler` 。
* 定期迴圈實例的過時 DNS 問題 `HttpMessageHandler` 。

有其他方法可以解決上述問題，方法是使用長期的 <xref:System.Net.Http.SocketsHttpHandler> 實例。

- 在 `SocketsHttpHandler` 應用程式啟動時建立的實例，並在應用程式的存留期內使用它。
- <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>根據 DNS 重新整理時間設定為適當的值。
- `HttpClient`視需要使用建立實例 `new HttpClient(handler, disposeHandler: false)` 。

上述方法可解決以類似的方式解決的資源管理問題 `IHttpClientFactory` 。

- `SocketsHttpHandler`共用實例之間的 `HttpClient` 連接。 這種共用可防止通訊端耗盡。
- 會 `SocketsHttpHandler` 根據來迴圈連接 `PooledConnectionLifetime` ，以避免過時的 DNS 問題。

### <a name="cookies"></a>Cookie

集區 `HttpMessageHandler` 實例會產生 `CookieContainer` 共用的物件。 非預期 `CookieContainer` 的物件共用通常會導致不正確的程式碼。 針對需要的應用程式 cookie ，請考慮下列其中一項：

 - 停用自動 cookie 處理
 - 避免 `IHttpClientFactory`

呼叫 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 以停用自動 cookie 處理：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>記錄

透過 `IHttpClientFactory` 建立的用戶端會記錄所有要求的記錄訊息。 在記錄設定中啟用適當的資訊層級，以查看預設記錄檔訊息。 額外的記錄功能，例如要求標頭的記錄，只會包含在追蹤層級。

用於每個用戶端的記錄檔分類包含用戶端的名稱。 例如，名為 *MyNamedClient* 的用戶端會記錄類別為的訊息 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` 。 後面加上 *LogicalHandler* 的訊息發生在要求處理常式管線之外。 在要求中，訊息會在管線中任何其他處理常式處理它之前就記錄。 在回應中，訊息會在任何其他管線處理常式收到回應之後記錄。

記錄也會發生在要求處理常式管線之內。 在 *MyNamedClient* 範例中，那些訊息是針對記錄檔分類 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` 而記錄。 對於要求，這是發生在所有其他處理常式都已執行之後，並且緊接在網路上傳送要求之前。 在回應中，此記錄會包含回應傳回通過處理常式管線之前的狀態。

在管線內外啟用記錄，可讓您檢查其他管線處理常式所做的變更。 例如，這可能包括要求標頭的變更，或是回應狀態碼的變更。

在記錄分類中包含用戶端的名稱，可讓您在需要時進行特定具名用戶端的記錄檔篩選。

## <a name="configure-the-httpmessagehandler"></a>設定 HttpMessageHandler

可能需要控制用戶端使用之內部 `HttpMessageHandler` 的組態。

新增具名或具型別用戶端時，會傳回 `IHttpClientBuilder`。 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 擴充方法可以用來定義委派。 委派是用來建立及設定該用戶端所使用的主要 `HttpMessageHandler`：

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>在主控台應用程式中使用 IHttpClientFactory

在主控台應用程式中，將下列套件參考新增至專案：

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

在下例中︰

* <xref:System.Net.Http.IHttpClientFactory> 已在[泛型主機的](xref:fundamentals/host/generic-host)服務容器中註冊。
* `MyService` 會從服務建立用戶端 Factory 執行個體，其可用來建立 `HttpClient`。 `HttpClient` 會用來擷取網頁。
* `GetPage`會執行服務的方法，將網頁內容的前500個字元寫入至主控台。 如需從呼叫服務的詳細資訊 `Program.Main` ，請參閱 <xref:fundamentals/dependency-injection#call-services-from-main> 。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,22)]

## <a name="header-propagation-middleware"></a>標頭傳播中介軟體

標頭傳播是一個支援仲介的中介軟體，可將來自傳入要求的 HTTP 標頭傳播至連出的 HTTP 用戶端要求。 若要使用標頭傳播：

* 參考封裝 [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)的「支援的社區」埠。 ASP.NET Core 3.1 和更新版本支援 [AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)。

* 設定中介軟體和 `HttpClient` `Startup` ：

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* 用戶端會在輸出要求中包含已設定的標頭：

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>其他資源

* [使用 HttpClientFactory 實作復原 HTTP 要求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [使用 HttpClientFactory 和 Polly 原則以指數輪詢實作 HTTP 呼叫重試](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [實作斷路器模式](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
