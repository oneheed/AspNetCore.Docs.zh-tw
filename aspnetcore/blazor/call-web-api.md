---
title: 從 ASP.NET Core 呼叫 web API Blazor WebAssembly
author: guardrex
description: 瞭解如何 Blazor WebAssembly 使用 JSON 協助程式從應用程式呼叫 WEB API，包括建立跨原始資源分享 (CORS) 要求。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 85b3ded6ec25310a573e99cbedf0df005d92bdbe
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93234409"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a>從 ASP.NET Core 呼叫 web API Blazor

[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)和[Juan De la cruz 一起探討](https://github.com/juandelacruz23)

> [!NOTE]
> 本主題適用于 Blazor WebAssembly 。 [Blazor Server](xref:blazor/hosting-models#blazor-server) 應用程式會使用 <xref:System.Net.Http.HttpClient> 實例（通常是使用建立）來呼叫 Web api <xref:System.Net.Http.IHttpClientFactory> 。 如需適用于的指引 Blazor Server ，請參閱 <xref:fundamentals/http-requests> 。

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 應用程式會使用預先設定的服務來呼叫 web Api <xref:System.Net.Http.HttpClient> 。 撰寫要求（可包含 JavaScript [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 選項），使用 Blazor JSON 協助程式或搭配 <xref:System.Net.Http.HttpRequestMessage> 。 <xref:System.Net.Http.HttpClient>應用程式中的服務著重于將 Blazor WebAssembly 要求傳回給來源伺服器。 本主題中的指導方針僅適用于 Blazor WebAssembly 應用程式。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) ：選取 `BlazorWebAssemblySample` 應用程式。

請參閱範例應用程式中的下列元件 `BlazorWebAssemblySample` ：

*  () 呼叫 Web API `Pages/CallWebAPI.razor`
* HTTP 要求測試器 (`Components/HTTPRequestTester.razor`) 

## <a name="packages"></a>套件

參考 [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) 專案檔中的 NuGet 套件。

## <a name="add-the-httpclient-service"></a>新增 HttpClient 服務

在中 `Program.Main` ，新增 <xref:System.Net.Http.HttpClient> 服務（如果尚未存在）：

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient 和 JSON 協助程式

在 Blazor WebAssembly 應用程式中，可做為預先設定的服務，以向 [`HttpClient`](xref:fundamentals/http-requests) 源伺服器提出要求。

Blazor Server依預設，應用程式不會包含 <xref:System.Net.Http.HttpClient> 服務。 <xref:System.Net.Http.HttpClient>使用[ `HttpClient` 工廠基礎結構](xref:fundamentals/http-requests)，提供給應用程式。

<xref:System.Net.Http.HttpClient> 此外，也會使用 JSON 協助程式來呼叫協力廠商 web API 端點。 <xref:System.Net.Http.HttpClient> 是使用瀏覽器 [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 來執行，並且受限於其限制，包括強制執行相同的原始原則。

用戶端的基底位址會設定為來源伺服器的位址。 使用指示詞插入 <xref:System.Net.Http.HttpClient> 實例 [`@inject`](xref:mvc/views/razor#inject) ：

```razor
@using System.Net.Http
@inject HttpClient Http
```

在下列範例中，Todo web API 會處理 (CRUD) 作業的建立、讀取、更新和刪除作業。 這些範例是以 `TodoItem` 儲存下列各類的類別為基礎：

* ID (`Id` ， `long`) ：專案的唯一識別碼。
* 名稱 (`Name` ， `string`) ：專案的名稱。
* 狀態 (`IsComplete` ， `bool`) ：是否已完成 Todo 專案的指示。

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON helper 方法會在下列範例中將要求傳送至 URI (web API) 並處理回應：

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>：傳送 HTTP GET 要求，並剖析 JSON 回應主體來建立物件。

  在下列程式碼中， `todoItems` 元件會顯示。 `GetTodoItems`當元件 () 轉譯完成時，就會觸發方法 [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) 。 如需完整範例，請參閱範例應用程式。

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>：傳送 HTTP POST 要求（包括 JSON 編碼的內容），並剖析 JSON 回應主體來建立物件。

  在下列程式碼中， `newItemName` 是由元件的繫結項目提供。 藉 `AddItem` 由選取元素來觸發方法 `<button>` 。 如需完整範例，請參閱範例應用程式。

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  呼叫以傳回 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> 。 若要從回應訊息還原序列化 JSON 內容，請使用 `ReadFromJsonAsync<T>` 擴充方法：
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>：傳送 HTTP PUT 要求，包括 JSON 編碼的內容。

  在下列程式碼中， `editItem` 和的值 `Name` `IsCompleted` 是由元件的繫結項目提供。 `Id`當專案在 UI 的另一個部分中選取並被呼叫時，會設定專案 `EditItem` 。 藉 `SaveItem` 由選取 [儲存] 元素來觸發方法 `<button>` 。 如需完整範例，請參閱範例應用程式。

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  呼叫以傳回 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> 。 若要從回應訊息還原序列化 JSON 內容，請使用 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 擴充方法：
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> 包含用來傳送 HTTP 要求和接收 HTTP 回應的額外擴充方法。 <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> 用來將 HTTP DELETE 要求傳送至 web API。

在下列程式碼中，Delete `<button>` 元素會呼叫 `DeleteItem` 方法。 綁定 `<input>` 項會提供 `id` 要刪除之專案的。 如需完整範例，請參閱範例應用程式。

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a>使用 IHttpClientFactory 命名的 HttpClient

<xref:System.Net.Http.IHttpClientFactory> 支援服務以及命名的設定 <xref:System.Net.Http.HttpClient> 。

參考 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 專案檔中的 NuGet 套件。

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData` 元件 (`Pages/FetchData.razor`) ：

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a>具類型的 HttpClient

具型別會 <xref:System.Net.Http.HttpClient> 使用一或多個應用程式的 <xref:System.Net.Http.HttpClient> 實例（預設或命名）來傳回一或多個 web API 端點的資料。

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
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
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

元件會插入型別 <xref:System.Net.Http.HttpClient> 以呼叫 WEB API。

`FetchData` 元件 (`Pages/FetchData.razor`) ：

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient` 以及 `HttpRequestMessage` 使用 FETCH API 要求選項

在應用程式的 WebAssembly 上執行時 Blazor WebAssembly ， [`HttpClient`](xref:fundamentals/http-requests) ([API 檔](xref:System.Net.Http.HttpClient)) 並 <xref:System.Net.Http.HttpRequestMessage> 可用於自訂要求。 例如，您可以指定 HTTP 方法和要求標頭。 下列元件會 `POST` 要求伺服器上的待辦事項清單 API 端點，並顯示回應主體：

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

.NET WebAssembly 的執行會 <xref:System.Net.Http.HttpClient> 使用 [WindowOrWorkerGlobalScope ( # B1 ](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。 Fetch 可讓您設定數個 [要求特定的選項](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。 

您可以使用 <xref:System.Net.Http.HttpRequestMessage> 下表所示的擴充方法來設定 HTTP 提取要求選項。

| 擴充方法 | Fetch 要求屬性 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

您可以使用更通用的擴充方法來設定其他選項 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 。
 
HTTP 回應通常會在 Blazor WebAssembly 應用程式中進行緩衝處理，以啟用對回應內容進行同步讀取的支援。 若要啟用對回應串流的支援，請 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 對要求使用擴充方法。

若要在跨原始來源要求中包含認證，請使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 擴充方法：

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

如需有關 Fetch API 選項的詳細資訊，請參閱 [MDN web 檔： WindowOrWorkerGlobalScope。提取 ( # A1：參數](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。

## <a name="handle-errors"></a>處理錯誤

當與 web API 互動時發生錯誤，可由開發人員程式碼處理。 例如， <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 需要來自伺服器 API 的 JSON 回應，以及 `Content-Type` 的 `application/json` 。 如果回應不是 JSON 格式，則內容驗證會擲回 <xref:System.NotSupportedException> 。

在下列範例中，氣象預測資料要求的 URI 端點拼寫錯誤。 URI 應該會 `WeatherForecast` 在呼叫中顯示，但 `WeatherForcast` (遺漏 "e" ) 。

<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>呼叫需要傳回 JSON，但是伺服器會傳回 HTML，以取得伺服器上未處理的例外狀況，以及 `Content-Type` 的 `text/html` 。 伺服器上發生未處理的例外狀況，因為找不到路徑，且中介軟體無法提供要求的頁面或觀點。

在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 用戶端上， <xref:System.NotSupportedException> 會在回應內容驗證為非 JSON 時擲回。 例外狀況會在區塊中 `catch` 攔截，其中自訂邏輯可能會記錄錯誤，或向使用者呈現易記的錯誤訊息：

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> 上述範例僅供示範之用。 Web API 伺服器應用程式可以設定為傳回 JSON，即使端點不存在，或伺服器上發生未處理的例外狀況。

如需詳細資訊，請參閱 <xref:blazor/fundamentals/handle-errors> 。

## <a name="cross-origin-resource-sharing-cors"></a>跨原始來源資源分享 (CORS)

瀏覽器安全性可防止網頁向與提供網頁的不同網域提出要求。 這項限制稱為 *相同原始來源原則*。 相同來源原則可防止惡意網站從另一個網站讀取敏感性資料。 若要從瀏覽器向具有不同來源的端點提出要求， *端點* 必須啟用 [跨原始來源資源分享 (CORS)](https://www.w3.org/TR/cors/)。

[ Blazor WebAssembly (Blazor WebAssemblySample) 範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)會示範如何在呼叫 Web API 元件中使用 CORS (`Pages/CallWebAPI.razor`) 。

如需在應用程式中使用安全要求進行 CORS 的詳細資訊 Blazor ，請參閱 <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> 。

如需搭配 ASP.NET Core apps 之 CORS 的一般資訊，請參閱 <xref:security/cors> 。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/security/webassembly/additional-scenarios>：包含使用的涵蓋範圍 <xref:System.Net.Http.HttpClient> ，以提出安全的 WEB API 要求。
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS 端點設定](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [在 W3C (CORS) 的跨原始資源分享](https://www.w3.org/TR/cors/)
