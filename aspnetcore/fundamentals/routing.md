---
title: ASP.NET Core 中的路由
author: rick-anderson
description: 探索 ASP.NET Core 路由如何負責比對 HTTP 要求和分派至可執行檔端點。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
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
uid: fundamentals/routing
ms.openlocfilehash: e3dd7168e6974f63fa963d3732bc5df41814c70e
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491612"
---
# <a name="routing-in-aspnet-core"></a>ASP.NET Core 中的路由

[Ryan Nowak](https://github.com/rynowak)、 [Kirk Larkin](https://twitter.com/serpent5)和[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

路由會負責比對傳入的 HTTP 要求，並將這些要求分派至應用程式的可執行端點。 [端點](#endpoint) 是應用程式可執行檔要求處理常式代碼的單位。 端點會定義在應用程式中，並在應用程式啟動時設定。 端點比對程式可以從要求的 URL 中解壓縮值，並為要求處理提供這些值。 使用應用程式中的端點資訊，路由也能夠產生對應至端點的 Url。

應用程式可以使用下列內容來設定路由：

- 控制器
- Razor 頁面
- SignalR
- gRPC 服務
- 啟用端點的 [中介軟體](xref:fundamentals/middleware/index) ，例如 [健康情況檢查](xref:host-and-deploy/health-checks)。
- 使用路由註冊的委派和 lambda。

本檔涵蓋 ASP.NET Core 路由的低層級詳細資料。 如需設定路由的相關資訊：

* 若為控制器，請參閱 <xref:mvc/controllers/routing> 。
* 如需 Razor 頁面慣例，請參閱 <xref:razor-pages/razor-pages-conventions> 。

本檔中所述的端點路由系統適用于 ASP.NET Core 3.0 和更新版本。 如需以上一個路由系統為基礎的相關資訊 <xref:Microsoft.AspNetCore.Routing.IRouter> ，請使用下列其中一種方法來選取 ASP.NET Core 2.1 版本：

* 先前版本的版本選取器。
* 選取 [ [ASP.NET Core 2.1 路由](?view=aspnetcore-2.1)]。

[查看或下載範例程式碼](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([如何下載](xref:index#how-to-download-a-sample)) 

這份檔的下載範例是由特定類別啟用 `Startup` 。 若要執行特定範例，請修改 *Program.cs* 以呼叫所需的 `Startup` 類別。

## <a name="routing-basics"></a>路由的基本概念

所有 ASP.NET Core 範本都包含產生的程式碼中的路由。 路由是在的 [中介軟體](xref:fundamentals/middleware/index) 管線中註冊 `Startup.Configure` 。

下列程式碼顯示路由的基本範例：

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

路由會使用一對由和註冊的中介軟體 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ：

* `UseRouting` 將路由對應新增至中介軟體管線。 此中介軟體會查看應用程式中定義的端點集合，並根據要求選取 [最符合](#urlm) 的條件。
* `UseEndpoints` 將端點執行新增至中介軟體管線。 它會執行與所選端點相關聯的委派。

上述範例包含使用[MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*)方法 *，將單一路由傳送至程式碼*端點：

* 當 HTTP `GET` 要求傳送至根 URL 時 `/` ：
  * 顯示的要求委派會執行。
  * `Hello World!` 會寫入 HTTP 回應。 根據預設，根 URL `/` 為 `https://localhost:5001/` 。
* 如果要求方法不是 `GET` 或根 URL 不是，則 `/` 不符合任何路由，而且會傳回 HTTP 404。

### <a name="endpoint"></a>端點

<a name="endpoint"></a>

`MapGet`方法是用來定義**端點**。 端點可以是：

* 選取此選項，藉由比對 URL 和 HTTP 方法。
* 執行委派。

應用程式可比對和執行的端點會在中設定 `UseEndpoints` 。 例如，、 <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> 和類似的 [方法](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) 會將要求委派連接至路由系統。
您可以使用其他方法，將 ASP.NET Core framework 功能連接到路由系統：
- [對應 Razor 頁面的頁面 Razor](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [控制器的 MapControllers](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub \<THub>SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [\<TService>適用于 gRPC 的 MapGrpcService](xref:grpc/aspnetcore)

下列範例顯示使用更精密的路由範本路由：

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

字串 `/hello/{name:alpha}` 是 **路由範本**。 它是用來設定端點的相符方式。 在此情況下，範本會符合：

* 如下的 URL： `/hello/Ryan`
* 開頭為字母字元序列的任何 URL 路徑 `/hello/` 。  `:alpha` 套用僅符合字母字元的路由條件約束。 本檔稍後會說明[路由條件約束](#route-constraint-reference)。

URL 路徑的第二個區段 `{name:alpha}` ：

* 系結至 `name` 參數。
* 會被捕獲並儲存在 [HttpRequest. RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*)中。

本檔中所述的端點路由系統是 ASP.NET Core 3.0 的新功能。 不過，所有版本的 ASP.NET Core 都支援相同的一組路由範本功能和路由條件約束。

下列範例顯示使用健康情況 [檢查](xref:host-and-deploy/health-checks) 和授權的路由：

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

上述範例示範如何：

* 授權中介軟體可以搭配路由使用。
* 端點可以用來設定授權行為。

<xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*>呼叫會新增健康情況檢查端點。 連結 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> 至此呼叫會將授權原則附加至端點。

呼叫 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 並 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> 新增驗證和授權中介軟體。 這些中介軟體會在和之間放置， <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> `UseEndpoints` 如此它們才能：

* 查看所選取的端點 `UseRouting` 。
* 先套用授權原則，再將其 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 分派至端點。

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>端點中繼資料

在上述範例中，有兩個端點，但只有健康情況檢查端點已附加授權原則。 如果要求符合健康狀態檢查端點， `/healthz` 則會執行授權檢查。 這會示範端點可以附加額外的資料。 這項額外的資料稱為端點 **中繼資料**：

* 中繼資料可以由路由感知中介軟體處理。
* 中繼資料可以是任何 .NET 型別。

## <a name="routing-concepts"></a>路由概念

路由系統會藉由新增強大的 **端點** 概念，在中介軟體管線之上建立。 端點代表應用程式功能的單位，與路由、授權和任意數量的 ASP.NET Core 系統之間不同。

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>ASP.NET Core 端點定義

ASP.NET Core 端點為：

* 可執行檔：有 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> 。
* 可擴充：具有 [中繼資料](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) 集合。
* 可選取：有 [路由資訊](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)。
* 可列舉：透過從 DI 抓取來列出端點的集合 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> 。 [DI](xref:fundamentals/dependency-injection)

下列程式碼示範如何取出和檢查符合目前要求的端點：

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

您可以從取出端點（如果已選取） `HttpContext` 。 可以檢查它的屬性。 端點物件是不可變的，無法在建立之後修改。 最常見的端點類型是 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint> 。 `RouteEndpoint` 包含可讓路由系統選取的資訊。

在上述程式碼中， [應用程式。使用](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) 會設定內嵌 [中介軟體](xref:fundamentals/middleware/index)。

<a name="mt"></a>

下列程式碼顯示，視管線中呼叫的位置而定 `app.Use` ，可能不會有端點：

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

上述範例會新增 `Console.WriteLine` 語句，以顯示是否已選取端點。 為了清楚起見，此範例會將顯示名稱指派給所提供的 `/` 端點。

使用顯示的 URL 來執行此程式碼 `/` ：

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

使用任何其他 URL 來執行此程式碼會顯示：

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

此輸出示範：

* 在呼叫之前，端點一律為 null `UseRouting` 。
* 如果找到相符的，則在和之間的端點為非 `UseRouting` null <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 。
* `UseEndpoints`當找到相符的情況時，中介軟體會是**終端**機。 本檔稍後會定義[終端中介軟體](#tm)。
* `UseEndpoints`只有在找不到相符的情況時，才會執行中介軟體。

`UseRouting`中介軟體會使用[task.setendpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*)方法，將端點附加至目前的內容。 您可以 `UseRouting` 使用自訂邏輯來取代中介軟體，但仍能獲得使用端點的優點。 端點是低層級的基本類型，像是中介軟體，並不會與路由執行結合。 大部分的應用程式都不需要以 `UseRouting` 自訂邏輯取代。

`UseEndpoints`中介軟體是設計來與 `UseRouting` 中介軟體一起使用。 執行端點的核心邏輯並不複雜。 使用 <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> 取出端點，然後叫用其 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> 屬性。

下列程式碼會示範中介軟體如何影響或回應路由：

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

上述範例會示範兩個重要概念：

* 中介軟體可以先執行，才能 `UseRouting` 修改路由運作所依據的資料。
    * 通常在路由之前出現的中介軟體會修改要求的某些屬性，例如 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 、 <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> 或 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> 。
* 中介軟體可以在和之間執行， `UseRouting` <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 以便在端點執行之前處理路由的結果。
    * 在和之間執行的中介軟體 `UseRouting` `UseEndpoints` ：
      * 通常會檢查中繼資料來瞭解端點。
      * 通常會依照和來進行安全性決策 `UseAuthorization` `UseCors` 。
    * 中介軟體和中繼資料的組合可讓您設定每個端點的原則。

上述程式碼顯示支援每個端點原則的自訂中介軟體範例。 中介軟體會將敏感性資料的存取權 *審核記錄* 寫入主控台。 中介軟體可以設定為使用中繼資料來 *審核* 端點 `AuditPolicyAttribute` 。 這個範例會示範 *選擇性* 模式，其中只會審核標示為機密的端點。 例如，您可以反向定義此邏輯，並針對未標示為安全的所有專案進行審核。 端點中繼資料系統很有彈性。 您可以使用任何符合使用案例的方式來設計這個邏輯。

上述範例程式碼旨在示範端點的基本概念。 **此範例不適合用于生產用途**。 更完整的 *audit 記錄* 中介軟體版本將會：

* 記錄到檔案或資料庫。
* 包含詳細資料，例如使用者、IP 位址、機密端點的名稱等。

稽核原則中繼資料 `AuditPolicyAttribute` 定義為，可 `Attribute` 讓您更輕鬆地使用以類別為基礎的架構，例如控制器和 SignalR 。 使用程式 *代碼的路由*時：

* 中繼資料會與建立器 API 連接。
* 以類別為基礎的架構，會在建立端點時，在對應的方法和類別上包含所有屬性。

元資料類型的最佳作法是將它們定義為介面或屬性。 介面和屬性可讓您重複使用程式碼。 中繼資料系統具有彈性，且不會強加任何限制。

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>比較終端中介軟體和路由

下列程式碼範例會對照使用中介軟體與使用路由的方式進行對比：

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

顯示的中介軟體樣式 `Approach 1:` 為 **終端中介軟體**。 它稱為「終端中介軟體」，因為它會執行相符的作業：

* 上述範例中的比對作業 `Path == "/"` 適用于中介軟體和 `Path == "/Movie"` 路由。
* 當相符成功時，它會執行一些功能並傳回，而不是叫用 `next` 中介軟體。

它稱為「終端中介軟體」，因為它會終止搜尋、執行一些功能，然後傳回。

比較終端中介軟體和路由：
* 這兩種方法都允許終止處理管線：
    * 中介軟體會藉由傳回而非叫用來終止管線 `next` 。
    * 端點一律為終端機。
* 終端中介軟體可將中介軟體放置於管線中的任意位置：
    * 端點會在的位置執行 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 。
* 終端中介軟體可讓任意程式碼判斷中介軟體何時符合：
    * 自訂路由比對程式碼可能很冗長，而且難以正確寫入。
    * 路由為一般應用程式提供簡單的解決方案。 大部分的應用程式都不需要自訂路由對應程式碼。
* 具有中介軟體的端點介面 `UseAuthorization` ，例如和 `UseCors` 。
    * 使用終端中介軟體搭配 `UseAuthorization` 或 `UseCors` 需要手動與授權系統互動。

[端點](#endpoint)會定義兩者：

* 用來處理要求的委派。
* 任意中繼資料的集合。 中繼資料是用來根據附加至每個端點的原則和設定，來實行跨領域考慮。

終端中介軟體可以是有效的工具，但可能需要：

* 大量的編碼和測試。
* 手動與其他系統整合，以達成所需的彈性層級。

在撰寫終端中介軟體之前，請考慮與路由整合。

現有的終端中介軟體與 [地圖](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) 整合，或 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 通常可以轉換成路由感知端點。 [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) 示範路由器的模式：
* 在上撰寫擴充方法 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> 。
* 使用建立內嵌中介軟體管線 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*> 。
* 將中介軟體附加至新的管線。 在此案例中為 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>。
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> 的中介軟體管線 <xref:Microsoft.AspNetCore.Http.RequestDelegate> 。
* 呼叫 `Map` 並提供新的中介軟體管線。
* 從擴充方法傳回所提供的產生器物件 `Map` 。

下列程式碼示範如何使用 [MapHealthChecks](xref:host-and-deploy/health-checks)：

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

上述範例示範如何傳回產生器物件很重要。 傳回 builder 物件可讓應用程式開發人員設定原則，例如端點的授權。 在此範例中，健康情況檢查中介軟體沒有與授權系統的直接整合。

中繼資料系統是為了回應使用終端中介軟體的擴充性作者所遇到的問題而建立的。 每個中介軟體都會有問題，使其本身與授權系統整合。

<a name="urlm"></a>

### <a name="url-matching"></a>URL 比對

* 這是路由將傳入要求對應至 [端點](#endpoint)的進程。
* 是以 URL 路徑和標頭中的資料為基礎。
* 可以擴充以考慮要求中的任何資料。

當路由中介軟體執行時，它會 `Endpoint` 從目前的要求將和路由值設定為的 [要求功能](xref:fundamentals/request-features) <xref:Microsoft.AspNetCore.Http.HttpContext> ：

* 呼叫 [HttpCoNtext GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) 會取得端點。
* `HttpRequest.RouteValues` 會取得路由值的集合。

在路由中介軟體之後執行的[中介軟體](xref:fundamentals/middleware/index)可以檢查端點並採取動作。 例如，授權中介軟體可以詢問端點的元資料集合，以取得授權原則。 執行要求處理管線中的所有中介軟體之後，會叫用所選端點的委派。

端點路由中的路由系統負責制定所有分派決策。 因為中介軟體會根據選取的端點套用原則，所以請務必：

* 任何可能會影響分派的決策或安全性原則的應用程式，都是在路由系統內進行。

> [!WARNING]
> 為了回溯相容性，當執行控制器或 Razor Pages 端點委派時， [>routecoNtext.routedata](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的屬性會根據到目前為止執行的要求處理，設定為適當的值。
>
> `RouteContext`未來的版本將會將類型標示為過時：
>
> * 遷移 `RouteData.Values` 至 `HttpRequest.RouteValues` 。
> * 遷移 `RouteData.DataTokens` 以從端點中繼資料取出 [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) 。

URL 比對會在一組可設定的階段中運作。 在每個階段中，輸出是一組相符專案。 下一個階段可以進一步縮小相符專案集。 路由執行不保證符合端點的處理順序。 系統會一次處理**所有**可能的相符專案。 URL 比對階段會依下列順序發生。 ASP.NET Core：

1. 針對一組端點和其路由範本處理 URL 路徑，以收集 **所有** 相符專案。
1. 採用上述清單，並移除套用路由條件約束失敗的相符專案。
1. 採用上述清單，並移除 [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) 實例失敗的相符專案。
1. 使用 [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) 從上述清單進行最後的決策。

端點清單會根據下列各項設定優先順序：

* [RouteEndpoint 順序](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [路由範本優先順序](#rtp)

在到達之前，所有相符的端點都會在每個階段中處理 <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> 。 `EndpointSelector`是最後一個階段。 它會從相符專案中選擇最高優先順序的端點，使其成為最符合專案。 如果有其他相符專案具有與最相符的相同優先權，則會擲回不明確的 match 例外狀況。

路由優先順序是根據 **更明確** 的路由範本，以較高的優先順序來計算。 例如，請考慮範本 `/hello` 和 `/{message}` ：

* 兩者都符合 URL 路徑 `/hello` 。
* `/hello`  更明確，因此優先順序較高。

一般而言，路由優先順序是針對實務中所使用的 URL 配置種類選擇最符合性的絕佳工作。 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order>只有在必要時才使用，以避免混淆。

由於路由提供的擴充性種類，路由系統不可能事先計算不明確的路由。 請考慮範例，例如路由範本 `/{message:alpha}` 和 `/{message:int}` ：

* `alpha`條件約束只符合字母字元。
* `int`條件約束只符合數位。
* 這些範本具有相同的路由優先順序，但是沒有相符的單一 URL。
* 如果路由系統在啟動時回報了不明確的錯誤，則會封鎖此有效的使用案例。

> [!WARNING]
>
> 內的作業順序 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 不會影響路由的行為，但有一個例外狀況。 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 並根據叫用順序 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 值的順序，自動指派訂單值給其端點。 這會模擬控制器的長時間行為，而不會有路由系統提供與舊版路由執行相同的保證。
>
> 在舊版的路由執行中，您可以執行與路由處理順序相依性的路由擴充性。 ASP.NET Core 3.0 和更新版本中的端點路由：
> 
> * 沒有路由的概念。
> * 不提供排序保證。 所有端點會一次處理。

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>路由範本優先順序和端點選取順序

[路由範本優先順序](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) 是一種系統，會根據特定的方式將值指派給每個路由範本。 路由範本優先順序：

* 避免在常見案例中調整端點順序的需求。
* 嘗試符合一般意義的路由行為預期。

例如，請考慮範本 `/Products/List` 和 `/Products/{id}` 。 假設 `/Products/List` 是比 URL 路徑更相符的結果，是合理的做法 `/Products/{id}` `/Products/List` 。 的運作方式是因為常 `/List` 值區段被視為具有比參數區段更好的優先順序 `/{id}` 。

優先順序運作方式的詳細資料，與路由範本的定義方式有關：

* 具有更多區段的範本會被視為更具體。
* 具有常值文字的區段會被視為比參數區段更明確的部分。
* 具有條件約束的參數區段會被視為更明確的參數區段，而不會有。
* 複雜區段會被視為具有條件約束的參數區段。
* Catch-all 參數是最不明確的參數。 請參閱[路由範本參考](#rtr)中的**全部攔截**，以取得有關 catch 所有路由的重要資訊。

如需確切值的參考，請參閱 [GitHub 上的原始程式碼](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) 。

<a name="lg"></a>

### <a name="url-generation-concepts"></a>URL 產生概念

URL 產生：

* 這是路由可以根據一組路由值來建立 URL 路徑的進程。
* 允許端點和存取它們的 Url 之間的邏輯分隔。

端點路由包含 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API。 `LinkGenerator` 是可從 [DI](xref:fundamentals/dependency-injection)取得的單一服務。 `LinkGenerator`API 可以在執行要求的內容之外使用。 [IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper)和依賴的案例（例如標籤協助程式 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 、HTML 協助程式和[動作結果](xref:mvc/controllers/actions)）會在[Tag Helpers](xref:mvc/views/tag-helpers/intro)內部使用 `LinkGenerator` API 來提供連結產生功能。

連結產生器背後支援的概念為「位址」**** 和「位址配置」****。 位址配置可讓您判斷應考慮用於連結產生的端點。 例如，許多使用者都熟悉的路由名稱和路由值案例，會將控制器和 Razor 頁面實作為位址配置。

連結產生器可以透過下列擴充方法連結至控制器和 Razor 頁面：

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

這些方法的多載接受包含的引數 `HttpContext` 。 這些方法在功能上相當於 [Url、Action](xref:System.Web.Mvc.UrlHelper.Action*) 和 [url。頁面](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)，但提供額外的彈性和選項。

這些 `GetPath*` 方法最類似于 `Url.Action` 和 `Url.Page` ，因為它們會產生包含絕對路徑的 URI。 `GetUri*` 方法一律會產生包含配置和主機的絕對 URI。 接受 `HttpContext` 的方法會在執行要求的內容中產生 URI。 除非覆寫，否則會使用來自執行要求的 [環境](#ambient) 路由值、URL 基底路徑、配置和主機。

呼叫 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 並指定一個位址。 執行下列兩個步驟來產生 URI：

1. 將位址繫結至符合該位址的端點清單。
1. 評估每個端點的 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern>，直到找到符合所提供值的路由模式。 產生的輸出會與提供給連結產生器的其他 URI 組件合併並傳回。

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法支援適用於任何位址類型的標準連結產生功能。 使用連結產生器最方便的方式，是透過執行特定網址類別型作業的擴充方法：

| 擴充方法 | 說明 |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 根據提供的值產生具有絕對路徑的 URI。 |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 根據提供的值產生絕對 URI。             |

> [!WARNING]
> 注意呼叫 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列影響：
>
> * 使用 `GetUri*` 擴充方法，並注意應用程式組態不會驗證傳入要求的 `Host` 標頭。 如果連 `Host` 入要求的標頭未經過驗證，則可以將不受信任的要求輸入傳送回視圖或頁面中 uri 的用戶端。 建議所有生產應用程式將其伺服器設定為驗證 `Host` 標頭是否為已知有效值。
>
> * 使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>，並注意與 `Map` 或 `MapWhen` 搭配使用的中介軟體。 `Map*` 會變更執行要求的基底路徑，這會影響連結產生的輸出。 所有的 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允許指定基底路徑。 指定空的基底路徑，以復原對連結產生的 `Map*` 影響。

### <a name="middleware-example"></a>中介軟體範例

在下列範例中，中介軟體會使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 來建立動作方法的連結，以列出商店產品。 使用連結產生器，方法是將它插入類別，然後呼叫 `GenerateLink` 可供應用程式中的任何類別使用：

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>路由範本參考

內 `{}` 的權杖會定義路由參數，這些參數會在路由相符時系結。 路由區段中可以定義一個以上的路由參數，但路由參數必須以常值分隔。 例如，`{controller=Home}{action=Index}` 不是有效的路由，因為 `{controller}` 與 `{action}` 之間沒有任何常值。  路由參數必須有名稱，而且可能會指定其他屬性。

路由參數之外的常值文字 (例如，`{id}`) 和路徑分隔符號 `/` 必須符合 URL 中的文字。 文字比對不區分大小寫，而且會根據 URL 路徑的已解碼標記法。 若要比對常值路由參數分隔符號 `{` 或 `}` ，請重複字元來將分隔符號取消。 例如， `{{` 或 `}}` 。

星號 `*` 或雙星號 `**` ：

* 可以用來作為路由參數的前置詞，以系結至 URI 的其餘部分。
* 稱為 **全部攔截** 參數。 例如，`blog/{**slug}`：
  * 比對以開頭的任何 URI， `/blog` 並在後面加上任何值。
  * 下列值 `/blog` 會指派給 [資訊](https://developer.mozilla.org/docs/Glossary/Slug) 區路由值。

[!INCLUDE[](~/includes/catchall.md)]

全部擷取參數也可以符合空字串。

當使用路由來產生 URL （包括路徑分隔符號）時，全部攔截參數會將適當的字元進行轉義 `/` 。 例如，路由值為 `{ path = "my/path" }` 的路由 `foo/{*path}` 會產生 `foo/my%2Fpath`。 請注意逸出的斜線。 若要反覆存取路徑分隔符號字元，請使用 `**` 路由參數前置詞。 具有 `{ path = "my/path" }` 的路由 `foo/{**path}` 會產生 `foo/my/path`。

URL 模式嘗試擷取具有選擇性副檔名的檔案名稱時，具有其他考量。 以範本 `files/{filename}.{ext?}` 為例。 當 `filename` 和 `ext` 都存在值時，就會填入這兩個值。 如果 `filename` URL 中只有存在的值，則路由會相符，因為尾端 `.` 是選擇性的。 下列 URL 符合此路由：

* `/files/myFile.txt`
* `/files/myFile`

路由參數可能有「預設值」****，指定方法是在參數名稱之後指定預設值，並以等號 (`=`) 分隔。 例如，`{controller=Home}` 定義 `Home` 作為 `controller` 的預設值。 如果 URL 中沒有用於參數的任何值，則會使用預設值。 路由參數是選擇性的，方法是將問號 (`?`) 附加到參數名稱的結尾。 例如，`id?`。 選擇性值與預設路由參數之間的差異如下：

* 具有預設值的路由參數一定會產生值。
* 選擇性參數只有在要求 URL 提供值時才會有值。

路由參數可能具有條件約束，這些條件約束必須符合與 URL 繫結的路由值。 在 `:` 路由參數名稱之後加入和條件約束名稱，會在路由參數上指定內嵌條件約束。 如果條件約束需要引數，這些引數會在條件約束名稱後面以括弧 `(...)` 括住。 您可以附加另一個和條件約束名稱，以指定多個 *內嵌條件約束* `:` 。

條件約束名稱和引述會傳遞至 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服務來建立 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的執行個體，以用於 URL 處理。 例如，路由範本 `blog/{article:minlength(10)}` 指定具有引數 `10` 的 `minlength` 條件約束。 如需路由條件約束詳細資訊和架構所提供的條件約束清單，請參閱[路由條件約束參考](#route-constraint-reference)一節。

路由參數也可以具有參數轉換器。 參數轉換程式會在產生連結並比對動作和頁面至 Url 時，轉換參數的值。 如同條件約束，您可以在 `:` 路由參數名稱之後加入和轉換程式名稱，以內嵌方式將參數轉換器新增至路由參數。 例如，路由範本 `blog/{article:slugify}` 會指定 `slugify` 轉換器。 如需參數轉換器的詳細資訊，請參閱[參數轉器參考](#parameter-transformer-reference)一節。

下表示范範例路由範本及其行為：

| 路由範本                           | 範例比對 URI    | 要求 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | 只比對單一路徑 `/hello`。                                     |
| `{Page=Home}`                            | `/`                     | 比對並將 `Page` 設定為 `Home`。                                         |
| `{Page=Home}`                            | `/Contact`              | 比對並將 `Page` 設定為 `Contact`。                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | 對應至 `Products` 控制器和 `List` 動作。                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 會對應至 `Products`  `Details` 設定為123的控制器和動作 `id` 。 |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | 對應至 `Home` 控制器和 `Index` 方法。 已忽略 `id`。        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | 對應至 `Products` 控制器和 `Index` 方法。 已忽略 `id`。        |

使用範本通常是最簡單的路由方式。 條件約束和預設值也可以在路由範本外部指定。

### <a name="complex-segments"></a>複雜區段

複雜區段的處理方式是在 [非貪婪](#greedy) 的情況下，由右至左比對常值分隔符號。 例如， `[Route("/a{b}c{d}")]` 是複雜區段。
複雜的區段會以特定的方式運作，必須瞭解才能成功使用它們。 本章節中的範例會示範當分隔符號文字未出現在參數值內部時，複雜區段只會很適合。 使用 [RegEx](/dotnet/standard/base-types/regular-expressions) ，然後以手動方式將值解壓縮，對較複雜的案例而言是必要的。

[!INCLUDE[](~/includes/regex.md)]

這是路由使用範本和 URL 路徑執行的步驟摘要 `/a{b}c{d}` `/abcd` 。 `|`用來協助視覺化演算法的運作方式：

* 第一個常值（由右至左）為 `c` 。 這 `/abcd` 會從右側搜尋並尋找 `/ab|c|d` 。
* 右邊 () 的所有內容 `d` 現在都會與 route 參數相符 `{d}` 。
* 下一個常值（由右至左）為 `a` 。 `/ab|c|d`搜尋從我們離職開始，然後 `a` 找到 `/|a|b|c|d` 。
* 右邊 () 的值 `b` 現在會符合 route 參數 `{b}` 。
* 沒有剩餘的文字和剩餘的路由範本，因此這是相符的。

以下是使用相同範本和 URL 路徑的負面案例範例 `/a{b}c{d}` `/aabcd` 。 `|`用來協助視覺化演算法的運作方式。 此案例不是相符的，這會透過相同的演算法來說明：
* 第一個常值（由右至左）為 `c` 。 這 `/aabcd` 會從右側搜尋並尋找 `/aab|c|d` 。
* 右邊 () 的所有內容 `d` 現在都會與 route 參數相符 `{d}` 。
* 下一個常值（由右至左）為 `a` 。 `/aab|c|d`搜尋從我們離職開始，然後 `a` 找到 `/a|a|b|c|d` 。
* 右邊 () 的值 `b` 現在會符合 route 參數 `{b}` 。
* 此時有剩餘 `a` 的文字，但演算法已用完路由範本來剖析，因此這不相符。

因為比對演算法 [是非貪婪](#greedy)的：

* 它會比對每個步驟中可能的最小文字數量。
* 在參數值內出現分隔符號值的任何情況下，都會導致不相符。

正則運算式對比對行為提供更多的控制權。

<a name="greedy"></a>

貪婪比對（也知道 [延遲](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)比對）符合最大的可能字串。 非貪婪符合最小的可能字串。

## <a name="route-constraint-reference"></a>路由條件約束參考

路由條件約束執行時機是出現符合傳入 URL 的項目，並將 URL 路徑語彙基元化成路由值時。 路由條件約束通常會檢查透過路由範本相關聯的路由值，並針對該值是否可接受，做出 true 或 false 的決策。 某些路由條件約束會使用路由值以外的資料，以考慮是否可以路由要求。 例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以依據其 HTTP 指令動詞接受或拒絕要求。 條件約束可用於路由要求和連結產生。

> [!WARNING]
> 請勿針對輸入驗證使用條件約束。 如果條件約束用於輸入驗證，則不正確輸入會導致找不到的 `404` 回應。 不正確輸入應產生 `400` 錯誤的要求，並顯示適當的錯誤訊息。 路由條件約束會用來釐清類似的路由，而不是用來驗證特定路由的輸入。

下表示范範例路由條件約束及其預期行為：

| constraint (條件約束) | 範例 | 範例相符項目 | 附註 |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | 符合任何整數 |
| `bool` | `{active:bool}` | `true`, `FALSE` | 符合 `true` 或 `false` 。 不區分大小寫 |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 符合非變異 `DateTime` 文化特性中的有效值。 請參閱前面的警告。 |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 符合非變異 `decimal` 文化特性中的有效值。 請參閱前面的警告。|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 符合非變異 `double` 文化特性中的有效值。 請參閱前面的警告。|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 符合非變異 `float` 文化特性中的有效值。 請參閱前面的警告。|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | 符合有效的 `Guid` 值 |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 符合有效的 `long` 值 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字串必須至少有 4 個字元 |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 字串不能超過 8 個字元 |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字串長度必須剛好是 12 個字元 |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字串長度必須至少有 8 個字元，但不能超過 16 個字元 |
| `min(value)` | `{age:min(18)}` | `19` | 整數值必須至少為 18 |
| `max(value)` | `{age:max(120)}` | `91` | 整數值不能超過 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 整數值必須至少為 18，但不能超過 120 |
| `alpha` | `{name:alpha}` | `Rick` | 字串必須包含一或多個字母字元，且不區分 `a` - `z` 大小寫。 |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字串必須符合正則運算式。 請參閱定義正則運算式的秘訣。 |
| `required` | `{name:required}` | `Rick` | 用來強制執行在 URL 產生期間呈現非參數值 |

[!INCLUDE[](~/includes/regex.md)]

可以將多個冒號分隔的條件約束套用至單一參數。 例如，下列條件約束會將參數限制在 1 或更大的整數值：

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> 驗證 URL 和轉換成 CLR 類型的路由條件約束一律會使用不變的文化特性。 例如，轉換成 CLR 類型 `int` 或 `DateTime` 。 這些條件約束假設 URL 不可當地語系化。 架構提供的路由條件約束不會修改路由值中儲存的值。 所有從 URL 剖析而來的路由值會儲存為字串。 例如，`float` 條件約束會嘗試將路由值轉換成浮點數，但轉換的值只能用來確認它可以轉換成浮點數。

### <a name="regular-expressions-in-constraints"></a>條件約束中的正則運算式

[!INCLUDE[](~/includes/regex.md)]

您可以使用路由條件約束，將正則運算式指定為內嵌條件約束 `regex(...)` 。 系列中的方法 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 也接受條件約束的物件常值。 如果使用該表單，則會將字串值視為正則運算式。

下列程式碼會使用內嵌的 RegEx 條件約束：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

下列程式碼會使用物件常值來指定 RegEx 條件約束：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

ASP.NET Core 架構將 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` 新增至規則運算式建構函式。 如需這些成員的說明，請參閱 <xref:System.Text.RegularExpressions.RegexOptions>。

正則運算式使用的分隔符號和標記類似于路由和 c # 語言所使用的標記。 規則運算式的語彙基元必須逸出。 若要 `^\d{3}-\d{2}-\d{4}$` 在內嵌條件約束中使用正則運算式，請使用下列其中一項：

* 將 `\` 字串中提供的字元取代為 c # 原始檔中的字元，以將 `\\` `\` 字串 escape 字元換用。
* [逐字字串常](/dotnet/csharp/language-reference/keywords/string)值。

若要將路由參數分隔符號 `{` 、 `}` 、 `[` 、 `]` ，請將運算式中的字元加倍，例如，、、 `{{` `}}` `[[` 、 `]]` 。 下表顯示正則運算式及其轉義版本：

| 規則運算式    | 已轉義的正則運算式     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

路由中使用的正則運算式通常以 `^` 字元開頭，並符合字串的開始位置。 運算式的結尾通常是 `$` 字元，並符合字串的結尾。 `^`和 `$` 字元可確保正則運算式符合整個路由參數值。 如果沒有 `^` 和 `$` 字元，正則運算式會比對字串內的任何子字串，這通常是不必要的。 下表提供範例，並說明它們符合或無法符合的原因：

| 運算是   | String    | 相符項目 | 註解               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 是   | 子字串相符項目     |
| `[a-z]{2}`   | 123abc456 | 是   | 子字串相符項目     |
| `[a-z]{2}`   | mz        | 是   | 符合運算式    |
| `[a-z]{2}`   | MZ        | 是   | 不區分大小寫    |
| `^[a-z]{2}$` | hello     | 否    | 請參閱上述的 `^` 和 `$` |
| `^[a-z]{2}$` | 123abc456 | 否    | 請參閱上述的 `^` 和 `$` |

如需規則運算式語法的詳細資訊，請參閱 [.NET Framework 規則運算式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。

若要將參數限制為一組已知的可能值，請使用規則運算式。 例如，`{action:regex(^(list|get|create)$)}` 只會將 `action` 路由值與 `list`、`get` 或 `create` 相符。 如果已傳入條件約束字典，字串 `^(list|get|create)$` 則是對等項目。 在條件約束字典中傳遞不符合其中一個已知條件約束的條件約束，也會被視為正則運算式。 在不符合其中一個已知條件約束的範本中傳遞的條件約束，不會被視為正則運算式。

### <a name="custom-route-constraints"></a>自訂路由條件約束

您可以藉由實作為介面來建立自訂路由條件約束 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 。 `IRouteConstraint`介面包含 <xref:System.Web.Routing.IRouteConstraint.Match*> ， `true` 如果滿足條件約束，則會傳回， `false` 否則會傳回。

自訂路由條件約束很少需要。 在執行自訂路由條件約束之前，請考慮使用替代方法，例如模型系結。

ASP.NET Core [條件約束](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) 資料夾提供建立條件約束的良好範例。 例如， [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18)。

若要使用自訂 `IRouteConstraint` ，必須向 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 服務容器中的應用程式註冊路由條件約束類型。 `ConstraintMap` 是一個目錄，它將路由限制式機碼對應到可驗證那些限制式的 `IRouteConstraint` 實作。 更新應用程式的 `ConstraintMap` 時，可在 `Startup.ConfigureServices` 中於進行 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼叫時更新，或透過使用 `services.Configure<RouteOptions>` 直接設定 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 來更新。 例如：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

上述條件約束適用于下列程式碼：

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

的執行 `MyCustomConstraint` 會防止套用 `0` 至路由參數：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

上述程式碼：

* 防止 `0` 在 `{id}` 路由區段中。
* 會顯示，以提供執行自訂條件約束的基本範例。 它不應該用於生產應用程式。

下列程式碼是一個更好的方法，可防止 `id` 包含的進行 `0` 處理：

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

上述程式碼在此方法中具有下列優點 `MyCustomConstraint` ：

* 它不需要自訂條件約束。
* 當 route 參數包含時，它會傳回更具描述性的錯誤 `0` 。

## <a name="parameter-transformer-reference"></a>參數轉換器參考

參數轉換程式：

* 使用產生連結時執行 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 。
* 實作 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>。
* 是使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 進行設定的。
* 採用參數的路由值，並將它轉換為新的字串值。
* 導致在所產生連結中使用已轉換的值。

例如，具有 `Url.Action(new { article = "MyTestArticle" })` 之路由模式 `blog\{article:slugify}` 中的自訂 `slugify` 參數轉換器，會產生 `blog\my-test-article`。

請考慮下列 `IOutboundParameterTransformer` 實行：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

若要在路由模式中使用參數轉換，請使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 在中進行設定 `Startup.ConfigureServices` ：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

ASP.NET Core framework 會使用參數轉換器來轉換端點解析的 URI。 例如，參數轉換器會轉換用來符合 `area` 、、和的路由 `controller` 值 `action` `page` 。

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

使用上述的路由範本，此動作 `SubscriptionManagementController.GetAll` 會與 URI 相符 `/subscription-management/get-all` 。 參數轉換器不會變更用來產生連結的路由值。 例如，`Url.Action("GetAll", "SubscriptionManagement")` 會輸出 `/subscription-management/get-all`。

ASP.NET Core 提供使用參數轉換器搭配產生之路由的 API 慣例：

* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName>MVC 慣例會將指定的參數轉換程式套用至應用程式中的所有屬性路由。 參數轉換程式會在被取代時轉換屬性路由語彙基元。 如需詳細資訊，請參閱[使用參數轉換程式自訂語彙基元取代](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。
* Razor 頁面會使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API 慣例。 此慣例會將指定的參數轉換器套用至所有自動探索到的 Razor 頁面。 參數轉換程式會轉換頁面路由的資料夾和檔案名區段 Razor 。 如需詳細資訊，請參閱[使用參數轉換程式自訂頁面路由](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。

<a name="ugr"></a>

## <a name="url-generation-reference"></a>URL 產生參考

本章節包含 URL 產生所執行之演算法的參考。 在實務上，最複雜的 URL 產生範例會使用控制器或 Razor 頁面。 如需其他資訊，請參閱  [控制器中的路由](xref:mvc/controllers/routing) 。

URL 產生進程的開頭是呼叫 [LinkGenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) 或類似的方法。 提供的方法有位址、一組路由值，以及有關目前要求的選擇性資訊 `HttpContext` 。

第一個步驟是使用位址，以符合網址類別型的來解析一組候選端點 [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) 。

一旦位址配置找到一組候選項目之後，就會反復排序和處理端點，直到 URL 產生作業成功為止。 URL **產生不會檢查是否** 有多義性，傳回的第一個結果是最後的結果。

### <a name="troubleshooting-url-generation-with-logging"></a>使用記錄針對 URL 產生進行疑難排解

針對 URL 產生進行疑難排解的第一個步驟，是將的記錄層級設定 `Microsoft.AspNetCore.Routing` 為 `TRACE` 。 `LinkGenerator` 記錄有關其處理的許多詳細資料，對於疑難排解問題可能很有用。

如需 URL 產生的詳細資訊，請參閱 [url 產生參考](#ugr) 。

### <a name="addresses"></a>位址

位址是 URL 產生中用來將連結產生器的呼叫系結至一組候選端點的概念。

位址是一種可延伸的概念，預設會隨附兩個執行：

* 使用 *端點名稱* (`string`) 作為位址：
    * 提供類似于 MVC 之路由名稱的功能。
    * 使用 <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> 元資料類型。
    * 針對所有已註冊端點的中繼資料解析提供的字串。
    * 如果多個端點使用相同的名稱，則會在啟動時擲回例外狀況。
    * 建議用於在控制器和頁面之外使用一般用途 Razor 。
* 使用 *路由值* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) 作為位址：
    * 提供類似于控制器和 Razor 頁面舊版 URL 產生的功能。
    * 非常複雜，可進行擴充和調試。
    * 提供所使用的實作為、標籤協助程式 `IUrlHelper` 、HTML 協助程式、動作結果等。

位址配置的角色是將位址和相符端點之間的關聯設為任意準則：

* 端點名稱配置會執行基本的字典查閱。
* 路由值配置具有複雜的集合演算法的最佳子集。

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>環境值和明確值

從目前的要求中，路由會存取目前要求的路由值 `HttpContext.Request.RouteValues` 。 與目前要求相關聯的值稱為 **環境值**。 為了清楚起見，檔是指將傳入方法的路由值視為 **明確值**。

下列範例會顯示環境值和明確值。 它會從目前的要求和明確的值提供環境 `{ id = 17, }` 值：

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

上述程式碼：

* 傳回 `/Widget/Index/17`。
* 取得 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> Via [DI](xref:fundamentals/dependency-injection)。

下列程式碼不提供任何環境值和明確的 `{ controller = "Home", action = "Subscribe", id = 17, }` 值：

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

上述方法會傳回 `/Home/Subscribe/17`

中的下列程式碼會傳回 `WidgetController` `/Widget/Subscribe/17` ：

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

下列程式碼會從目前要求中的環境值和明確的值提供控制器 `{ action = "Edit", id = 17, }` ：

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

在上述程式碼中：

* `/Gadget/Edit/17` 傳回。
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> 取得 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 。
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
使用動作方法的絕對路徑產生 URL。 URL 包含指定的 `action` 名稱和 `route` 值。

下列程式碼會從目前的要求和明確的值提供環境 `{ page = "./Edit, id = 17, }` 值：

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

`url` `/Edit/17` 當 [編輯] Razor 頁面包含下列頁面指示詞時，上述程式碼會設定為：

 `@page "{id:int}"`

如果編輯頁面未包含 `"{id:int}"` 路由範本， `url` 則為 `/Edit?id=17` 。

<xref:Microsoft.AspNetCore.Mvc.IUrlHelper>除了此處所述的規則之外，MVC 的行為還會增加一層複雜性：

* `IUrlHelper` 一律提供來自目前要求的路由值做為環境值。
* 除非由開發人員覆寫，否則[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*)一律會將目前的 `action` 和 `controller` 路由值複製為明確值。
* 除非覆寫，否則[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)一律會將目前的 `page` 路由值複製為明確值。 <!--by the user-->
* `IUrlHelper.Page` 除非覆寫，否則一律以明確值覆寫目前的 `handler` 路由值 `null` 。

使用者通常會因為環境值的行為詳細資料而感到驚訝，因為 MVC 似乎不會遵循自己的規則。 基於歷程記錄和相容性的理由，某些路由值（例如 `action` 、 `controller` 、 `page` 和） `handler` 有自己的特殊案例行為。

提供的對等功能 `LinkGenerator.GetPathByAction` 會 `LinkGenerator.GetPathByPage` `IUrlHelper` 針對相容性複製這些異常。

### <a name="url-generation-process"></a>URL 產生進程

一旦找到候選端點集合之後，URL 產生演算法：

* 反復處理端點。
* 傳回第一個成功的結果。

此程式中的第一個步驟是呼叫 **路由值失效**。  路由值失效是路由用來決定應該使用環境值的路由值，以及應忽略哪些路由值的處理常式。 會考慮每個環境的值，並結合明確的值，或忽略此值。

考慮環境值角色的最佳方式，就是在某些常見的情況下，他們會嘗試儲存應用程式開發人員的輸入。 傳統上，環境值很有説明的情況與 MVC 相關：

* 連結至相同控制器中的另一個動作時，不需要指定控制器名稱。
* 連結到相同區域中的另一個控制器時，不需要指定區功能變數名稱稱。
* 連結至相同的動作方法時，不需要指定路由值。
* 連結到應用程式的另一個部分時，您不會想要在應用程式的該部分中執行沒有意義的路由值。

呼叫或傳回傳回 `LinkGenerator` `IUrlHelper` 的 `null` 原因通常是因為不了解路由值失效。 明確指定更多路由值以查看是否能解決問題，以針對路由值失效進行疑難排解。

路由值失效的運作方式是假設應用程式的 URL 配置為階層式，且階層是由左至右形成的。 請考慮使用基本控制器路由範本 `{controller}/{action}/{id?}` ，以直覺瞭解這在實務上的運作方式。 值的 **變更** 會使出現在右邊的所有路由值 **失效** 。 這會反映階層的假設。 如果應用程式具有的環境值 `id` ，且此作業為指定了不同的值 `controller` ：

* `id` 因為是左邊的，所以不會重複使用 `{controller}` `{id?}` 。

示範此原則的一些範例：

* 如果明確的值包含的值 `id` ，的環境值會被 `id` 忽略。 您 `controller` 可以使用和的環境值 `action` 。
* 如果明確的值包含的值 `action` ，的任何環境值 `action` 都會被忽略。 您可以使用的環境值 `controller` 。 如果的明確值與 `action` 的環境值不同 `action` ，將 `id` 不會使用此值。  如果的明確值與 `action` 的環境值相同 `action` ，則 `id` 可以使用此值。
* 如果明確的值包含的值 `controller` ，的任何環境值 `controller` 都會被忽略。 如果的明確值與 `controller` 的環境值不同 `controller` ，將 `action` 不會使用和 `id` 值。 如果的明確值與 `controller` 的環境值相同 `controller` ，則 `action` `id` 可以使用和值。

此程式會因為屬性路由和專用慣例路由的存在而變得更複雜。 控制器慣例路由，例如 `{controller}/{action}/{id?}` 使用路由參數指定階層。 適用于控制器和頁面的 [專用傳統路由](xref:mvc/controllers/routing#dcr) 和 [屬性路由](xref:mvc/controllers/routing#ar) Razor ：

* 有路由值的階層。
* 它們不會出現在範本中。

在這些情況下，URL 產生會定義 **必要的值** 概念。 控制器和頁面所建立的端點 Razor 都具有指定的值，可讓路由值失效運作。

路由值失效演算法的詳細資料：

* 必要的值名稱會與路由參數合併，然後由左至右處理。
* 針對每個參數，會比較環境值和明確值：
    * 如果環境值和明確值相同，進程會繼續執行。
    * 如果環境值存在且明確值不是，則會在產生 URL 時使用環境值。
    * 如果環境值不存在且明確的值為，則會拒絕環境值和所有後續的環境值。
    * 如果有環境值和明確的值，而且這兩個值不同，則會拒絕環境值和所有後續的環境值。

此時，URL 產生作業已準備好評估路由條件約束。 接受的值集合會與提供給條件約束的參數預設值結合。 如果所有條件約束都通過，則作業會繼續進行。

接下來，可以使用 **接受的值** 來展開路由範本。 路由範本的處理：

* 從左至右。
* 每個參數都有接受的值取代。
* 使用下列特殊案例：
  * 如果接受的值遺漏值，而且參數有預設值，則會使用預設值。
  * 如果接受的值遺漏值，而且參數是選擇性的，則會繼續處理。
  * 如果遺漏選擇性參數右邊的任何路由參數都有值，則作業會失敗。
  * <!-- review default-valued parameters optional parameters --> 可能的話，會折迭連續的預設值參數和選擇性參數。

明確提供但不符合路由區段的值會新增至查詢字串。 下表顯示使用路由範本 `{controller}/{action}/{id?}` 時的結果。

| 環境值                     | 明確值                        | 結果                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>路由值失效的問題

從 ASP.NET Core 3.0 開始，舊版 ASP.NET Core 中使用的某些 URL 產生配置，不適用於 URL 產生。 ASP.NET Core 小組計畫在未來的版本中新增功能來解決這些需求。 現在，最好的解決方法是使用舊版路由。

下列程式碼顯示路由不支援的 URL 產生配置範例。

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

在上述程式碼中， `culture` 會使用 route 參數進行當地語系化。 希望將 `culture` 參數一律接受為環境值。 不過， `culture` 因為所需的值運作方式，所以不接受參數作為環境值：

* 在 `"default"` 路由範本中， `culture` 路由參數是左邊的 `controller` ，因此的變更將 `controller` 不會失效 `culture` 。
* 在 `"blog"` 路由範本中， `culture` 路由參數會被視為 `controller` 出現在必要值中的右邊。

## <a name="configuring-endpoint-metadata"></a>設定端點中繼資料

下列連結提供設定端點中繼資料的相關資訊：

* [使用端點路由來啟用 Cors](xref:security/cors#enable-cors-with-endpoint-routing)
* 使用自訂屬性的[IAuthorizationPolicyProvider 範例](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider) `[MinimumAgeAuthorize]`
* [使用 [授權] 屬性測試驗證](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [選取具有 [授權] 屬性的配置](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [使用 [授權] 屬性套用原則](xref:security/authorization/policies#apply-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>使用 RequireHost 的路由中的主機相符

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> 將條件約束套用至需要指定主機的路由。 `RequireHost`或[[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute)參數可以是：

* Host： `www.domain.com` ，符合 `www.domain.com` 任何埠。
* 具有萬用字元的主機： `*.domain.com` 、 `www.domain.com` 比 `subdomain.domain.com` 對、或 `www.subdomain.domain.com` 在任何埠上。
* 埠：與 `*:5000` 任何主機相符的埠5000。
* 主機和埠： `www.domain.com:5000` 或 `*.domain.com:5000` 符合主機和埠。

您可以使用或來指定多個參數 `RequireHost` `[Host]` 。 條件約束符合任何參數的有效主機。 例如， `[Host("domain.com", "*.domain.com")]` 符合 `domain.com` 、 `www.domain.com` 和 `subdomain.domain.com` 。

下列程式碼會使用 `RequireHost` 來要求路由上指定的主機：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

下列程式碼會使用 `[Host]` 控制器上的屬性來要求任何指定的主機：

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

當 `[Host]` 屬性同時套用至控制器和動作方法時：

* 使用動作上的屬性。
* 控制器屬性會被忽略。

## <a name="performance-guidance-for-routing"></a>路由的效能指引

大部分的路由會在 ASP.NET Core 3.0 中更新，以提升效能。

當應用程式發生效能問題時，通常會將路由視為問題。 考慮路由的原因是，控制器和 Razor 頁面等架構會報告在架構中的記錄訊息內花費的時間量。 當控制器所報告的時間與要求的總時間之間有顯著的差異時：

* 開發人員會將其應用程式程式碼消除為問題的根源。
* 通常會假設路由是原因。

路由是使用上千個端點測試的效能。 一般的應用程式不太可能只會因為太大而遇到效能問題。 緩慢路由效能最常見的根本原因，通常是行為錯誤的自訂中介軟體。

下列程式碼範例示範縮小延遲來源的基本技術：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

到時間路由：

* 將每個中介軟體與先前程式碼中顯示的計時中介軟體複本交錯。
* 加入唯一識別碼，將計時資料與程式碼產生關聯。

這是在很重要的情況下縮小延遲的基本方法，例如，超過 `10ms` 。  減去 `Time 2` `Time 1` 中介軟體內花費的時間 `UseRouting` 。

下列程式碼會對先前的計時程式碼使用更精簡的方法：

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>可能昂貴的路由功能

下列清單提供對路由功能的一些深入解析，相較于基本路由範本，這些功能相當昂貴：

* 正則運算式：可以撰寫很複雜的正則運算式，或具有少量輸入的長時間執行時間。

*  () 的複雜區段 `{x}-{y}-{z}` ： 
  * 比剖析一般 URL 路徑區段更昂貴。
  * 會產生更多的子字串。
  * ASP.NET Core 3.0 路由效能更新中未更新複雜區段邏輯。

* 同步資料存取：許多複雜的應用程式在其路由中都有資料庫存取權。 ASP.NET Core 2.2 和較早的路由可能無法提供支援資料庫存取路由的適當擴充性點。 例如， <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 和 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 是同步的。 和等擴充點 <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> 都是非同步。

## <a name="guidance-for-library-authors"></a>程式庫作者指南

本節包含程式庫作者在路由上建立的指引。 這些詳細資料的目的是要確保應用程式開發人員使用延伸路由的程式庫和架構有良好的體驗。

### <a name="define-endpoints"></a>定義端點

若要建立使用路由進行 URL 比對的架構，請先定義以之上建立的使用者體驗 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 。

在之上**進行**build <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> 。 這可讓使用者使用其他 ASP.NET Core 功能來撰寫您的架構，而不會造成混淆。 每個 ASP.NET Core 範本都包含路由。 假設路由存在且熟悉使用者。

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**確實** 從呼叫傳回密封的具象型別 `MapMyFramework(...)` <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> 。 大部分 `Map...` 的 framework 方法都遵循此模式。 `IEndpointConventionBuilder`介面：

* 允許中繼資料的可組合性。
* 的目標是各種擴充方法。

宣告您自己的型別可讓您將自己的架構專屬功能新增至建立器。 您可以包裝架構宣告的產生器，並將呼叫轉寄給它。

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**請考慮** 撰寫您自己 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> 的。 `EndpointDataSource` 這是用來宣告和更新端點集合的低層級基本類型。 `EndpointDataSource` 是控制器和頁面所使用的強大 API Razor 。

路由測試有一個基本的不可更新資料來源 [範例](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) 。

預設**不**嘗試註冊 `EndpointDataSource` 。 要求使用者在中註冊您的架構 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 。 路由的原理是，預設不會包含任何專案，而且這 `UseEndpoints` 是註冊端點的位置。

### <a name="creating-routing-integrated-middleware"></a>建立路由整合中介軟體

**請考慮** 將元資料類型定義為介面。

**請盡可能** 使用元資料類型做為類別和方法上的屬性。

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

控制器和頁面等架構 Razor 支援將中繼資料屬性套用至類型和方法。 如果您宣告元資料類型：

* 讓它們可作為 [屬性](/dotnet/csharp/programming-guide/concepts/attributes/)存取。
* 大部分的使用者都很熟悉套用屬性。

將元資料類型宣告為介面，可增加另一層彈性：

* 介面是可組合的。
* 開發人員可以宣告自己的類型，以結合多個原則。

**請確定可以** 覆寫中繼資料，如下列範例所示：

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

遵循這些指導方針的最佳方式是避免定義 **標記中繼資料**：

* 請不要只尋找元資料類型是否存在。
* 在中繼資料上定義屬性，並檢查屬性。

元資料集合已排序，並支援依優先順序覆寫。 在控制器的案例中，動作方法上的中繼資料最特定。

**讓中間** 件在使用和沒有路由的情況下很有用。

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization();
});
```

作為此指導方針的範例，請考慮使用 `UseAuthorization` 中介軟體。 授權中介軟體可讓您傳入回原則。 <!-- shown where?  (shown here) --> 若有指定，則會同時套用至這兩個原則：

* 沒有指定原則的端點。
* 不符合端點的要求。

這使得授權中介軟體在路由的內容之外很有用。 授權中介軟體可用於傳統中介軟體程式設計。

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

路由負責將要求 Uri 對應至端點，並將傳入要求分派至這些端點。 路由定義於應用程式，並在該應用程式啟動時進行設定。 路由可以選擇性地從要求中所包含的 URL 擷取值，然後這些值就可用於處理要求。 使用應用程式中的路由資訊，路由也能夠產生對應至端點的 Url。

若要使用 ASP.NET Core 2.2 中的最新路由情節，請將[相容性版本](xref:mvc/compatibility-version)指定為 `Startup.ConfigureServices` 中的 MVC 服務註冊：

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 選項會決定路由功能應該在內部使用以端點為基礎的邏輯，或以 <xref:Microsoft.AspNetCore.Routing.IRouter> 為基礎的邏輯 (適用於 ASP.NET Core 2.1 或更舊版本)。 當相容性版本設定為 2.2 或更新版本時，預設值為 `true`。 將值設定為 `false` 可使用舊版路由邏輯：

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

如需以 <xref:Microsoft.AspNetCore.Routing.IRouter> 為基礎之路由的詳細資訊，請參閱[本主題的 ASP.NET Core 2.1 版本](?view=aspnetcore-2.1)。

> [!IMPORTANT]
> 本文件涵蓋低階的 ASP.NET Core 路由。 如需 ASP.NET Core MVC 路由的資訊，請參閱 <xref:mvc/controllers/routing>。 如需頁面中路由慣例的詳細資訊 Razor ，請參閱 <xref:razor-pages/razor-pages-conventions> 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="routing-basics"></a>路由的基本概念

大部分應用程式都應該選擇基本的描述性路由傳送配置，讓 URL 可讀且有意義。 預設慣例路由 `{controller=Home}/{action=Index}/{id?}`：

* 支援基本的描述性路由配置。
* 適合作為 UI 型應用程式的起點。

開發人員通常會在使用 [屬性路由](xref:mvc/controllers/routing#attribute-routing) 或專用慣例路由的特殊情況下，將額外的簡易路由新增到應用程式的高流量區域中。 特殊化的情況範例包括、blog 和電子商務端點。

Web API 應該使用屬性路由傳送來將應用程式功能模型建構為作業由 HTTP 指令動詞代表的資源集合。 這表示相同邏輯資源上的許多作業（例如 GET 和 POST）都會使用相同的 URL。 屬性路由提供仔細設計 API 公用端點配置所需的控制層級。

Razor 頁面應用程式會使用預設的傳統路由，在應用程式的 *Pages* 資料夾中提供命名資源。 有其他慣例可讓您自訂 Razor 頁面路由行為。 如需詳細資訊，請參閱 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。

URL 產生支援允許在不需要硬式編碼的 URL 來連結應用程式的情況下開發應用程式。 這項支援可讓您從基本路由設定開始，並在決定應用程式資源配置之後修改路由。

路由會使用 *端點* (`Endpoint`) 來代表應用程式中的邏輯端點。

端點會定義用來處理要求的一項委派及一個任意中繼資料集合。 中繼資料可根據附加至每個端點的原則和組態，來實作跨領域關注。

路由系統具有下列特性：

* 使用路由範本語法以 Token 化路由參數來定義路由。
* 允許傳統式和屬性式端點組態。
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 可用來判斷 URL 參數是否包含對指定端點條件約束有效的值。
* 應用程式模型（例如 MVC/ Razor Pages）會註冊其所有端點，這些端點具有可預測的路由案例執行。
* 路由實作會在中介軟體管線需要時制定路由決策。
* 在路由中介軟體之後出現的中介軟體可以檢查指定要求 URI 的路由中介軟體端點決策。
* 您可以針對中介軟體管線中任何位置的應用程式，列舉其中的所有端點。
* 應用程式可以根據端點資訊使用路由來產生 URL (例如，針對重新導向或連結)，因此避免硬式編碼的 URL，這有助於可維護性。
* URL 是根據支援任意擴充性的位址所產生：

  * 您可以在任何位置使用[相依性插入 (DI)](xref:fundamentals/dependency-injection) 來解析連結產生器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)，以產生 URL。
  * 如果無法透過 DI 使用連結產生器 API，<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 會提供方法來建立 URL。

> [!NOTE]
> 當 ASP.NET Core 2.2 中的端點路由發行時，端點連結僅限於 MVC/ Razor 頁面動作和頁面。 未來版本將規劃擴充端點連結功能。

路由會透過 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 類別連線到[中介軟體](xref:fundamentals/middleware/index)管線。 [ASP.NET CORE mvc](xref:mvc/overview) 會將路由新增至中介軟體管線，作為其設定的一部分，並處理 MVC 和 Razor 頁面應用程式中的路由。 若要了解如何使用路由作為獨立元件，請參閱[使用路由中介軟體](#use-routing-middleware)一節。

### <a name="url-matching"></a>URL 比對

URL 比對是路由用來將傳入要求分派給「端點」** 的處理序。 這個處理序是基於 URL 路徑中的資料，但是可以擴展為考慮要求中的任何資料。 分派要求給不同處理常式的能力，是調整應用程式大小和複雜度的關鍵。

端點路由中的路由系統負責制定所有分派決策。 由於中介軟體會根據所選取的端點來套用原則，因此請務必在路由系統內制定可能影響分派或應用安全性原則的任何決策。

執行端點委派時，會根據到目前為止所執行的要求處理，將 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的屬性設定為適當的值。

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是「路由值」** 的字典，而路由值產生自路由。 這些值通常是透過將 URL 語彙基元化來決定，可以用來接受使用者輸入，或在應用程式內做出進一步的分派決策。

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是其他資料的屬性包，而這些資料與相符路由相關。 提供了 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 來支援與每個路由建立關聯的狀態資料，因此應用程式可以依據符合哪一個路由來制定決策。 這些是開發人員定義的值，**不會**以任何方式影響路由的行為。 此外，儲藏在 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以是任何類型，對比之下，[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 則必須可轉換成字串或可從字串轉換。

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是成功符合要求的參與路由清單。 路由可以用巢狀方式置於彼此內部。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 屬性會透過導致產生相符項目的路由邏輯樹狀結構反映路徑。 一般而言，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一個項目是路由集合，應該用於產生 URL。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最後一個項目是相符的路由處理常式。

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>使用 LinkGenerator 產生 URL

URL 產生是路由可用來依據一組路由值建立 URL 路徑的處理序。 這可讓您在端點和存取它們的 URL 之間建立邏輯分隔。

端點路由包含連結產生器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)。 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 是可從 [DI](xref:fundamentals/dependency-injection)取出的單一服務。 您可以在執行要求內容外部使用此 API。 MVC 的 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 及依賴 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的情節 (例如[標籤協助程式](xref:mvc/views/tag-helpers/intro)、HTML 協助程式和[動作結果](xref:mvc/controllers/actions)) 均使用連結產生器來提供連結產生功能。

連結產生器背後支援的概念為「位址」** 和「位址配置」**。 位址配置可讓您判斷應考慮用於連結產生的端點。 例如，許多使用者熟悉的路由名稱和路由值案例 Razor 會實作為位址配置。

連結產生器可以透過下列擴充方法，連結至 MVC/ Razor 頁面動作和頁面：

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

這些方法的多載接受包含 `HttpContext` 的引數。 這些方法的功能等同於 `Url.Action` 和 `Url.Page`，但提供更多彈性和選項。

`GetPath*` 方法與 `Url.Action` 和 `Url.Page` 最類似，因為它們會產生包含絕對路徑的 URI。 `GetUri*` 方法一律會產生包含配置和主機的絕對 URI。 接受 `HttpContext` 的方法會在執行要求的內容中產生 URI。 除非遭到覆寫，否則會使用來自執行要求的環境路由值、URL 基底路徑、配置和主機。

呼叫 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 並指定一個位址。 執行下列兩個步驟來產生 URI：

1. 將位址繫結至符合該位址的端點清單。
1. 評估每個端點的 `RoutePattern`，直到找到符合所提供值的路由模式。 產生的輸出會與提供給連結產生器的其他 URI 組件合併並傳回。

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法支援適用於任何位址類型的標準連結產生功能。 使用連結產生器的最便利方式是透過執行特定位址類型作業的擴充方法。

| 擴充方法   | 說明                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 根據提供的值產生具有絕對路徑的 URI。 |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 根據提供的值產生絕對 URI。             |

> [!WARNING]
> 注意呼叫 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列影響：
>
> * 使用 `GetUri*` 擴充方法，並注意應用程式組態不會驗證傳入要求的 `Host` 標頭。 如果傳入要求的 `Host` 標頭未經驗證，則可能將未受信任的要求輸入傳回檢視/頁面 URI 中的用戶端。 建議所有生產應用程式將其伺服器設定為驗證 `Host` 標頭是否為已知有效值。
>
> * 使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>，並注意與 `Map` 或 `MapWhen` 搭配使用的中介軟體。 `Map*` 會變更執行要求的基底路徑，這會影響連結產生的輸出。 所有的 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允許指定基底路徑。 請一律指定空白基底路徑來恢復 `Map*` 對連結產生的影響。

## <a name="differences-from-earlier-versions-of-routing"></a>與舊版路由的差異

ASP.NET Core 2.2 或更新版本中的端點路由與 ASP.NET Core 中的舊版路由之間有一些差異：

* 端點路由系統不支援以 <xref:Microsoft.AspNetCore.Routing.IRouter> 為基礎的擴充性，包括從 <xref:Microsoft.AspNetCore.Routing.Route> 繼承。

* 端點路由不支援 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。 使用 2.1 [相容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 繼續使用相容性填充碼。

* 端點路由與使用傳統路由時所產生 URI 大小寫的行為不同。

  請考慮下列預設路由範本：

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  假設您使用下列路由來產生動作連結：

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  使用以 <xref:Microsoft.AspNetCore.Routing.IRouter> 為基礎的路由，此程式碼會產生 `/blog/ReadPost/17` 的 URI，其遵守所提供路由值的大小寫。 ASP.NET Core 2.2 或更新版本中的端點路由會產生 `/Blog/ReadPost/17` ("Blog" 為大寫)。 端點路由提供 `IOutboundParameterTransformer` 介面，可用來全域自訂此行為，或為對應 URL 套用不同的慣例。

  如需詳細資訊，請參閱[參數轉換器參考](#parameter-transformer-reference)一節。

* Razor當您嘗試連結至不存在的控制器/動作或頁面時，MVC/頁面與傳統路由所使用的連結產生會有不同的行為。

  請考慮下列預設路由範本：

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  假設您使用預設範本和下列程式碼來產生動作連結：

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  使用以 `IRouter` 為基礎的路由，結果一律為 `/Blog/ReadPost/17`，即使 `BlogController` 不存在或沒有 `ReadPost` 動作方法也一樣。 如預期，如果動作方法存在，則 ASP.NET Core 2.2 或更新版本中的端點路由會產生 `/Blog/ReadPost/17`。 不過，如果動作不存在，則端點路由會產生空字串。** 就概念而言，如果動作不存在，則端點路由不會假設端點存在。

* 連結產生「環境值失效演算法」** 在搭配端點路由使用時會有不同的行為。

  「環境值失效」** 是一種演算法，會從目前執行的要求 (環境值) 決定可用於連結產生作業的路由值。 傳統路由一律會在連結至其他動作時，使額外的路由值失效。 在 ASP.NET Core 2.2 版以前，屬性路由沒有此行為。 在舊版的 ASP.NET Core 中，連結至使用相同路由參數名稱的其他動作會導致連結產生錯誤。 在 ASP.NET Core 2.2 或更新版本中，這兩種路由形式都會在連結至其他動作時使值失效。

  請考慮 ASP.NET Core 2.1 或更舊版本中的下列範例。 連結至其他動作 (或其他頁面) 時，路由值可能會不適當地重複使用。

  在 */Pages/Store/Product.cshtml* 中：

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  在 */Pages/Login.cshtml* 中：

  ```cshtml
  @page "{id?}"
  ```

  如果 URI 在 ASP.NET Core 2.1 或更舊版本中為 `/Store/Product/18`，則 `@Url.Page("/Login")` 在 Store/Info 頁面中產生的連結為 `/Login/18`。 這會重複使用 `id` 值 18，即使連結目的地是完全不同的應用程式組件也一樣。 `/Login` 頁面內容中的 `id` 路由值可能是使用者識別碼值，而不是市集產品識別碼值。

  在 ASP.NET Core 2.2 或更新版本中的端點路由中，結果為 `/Login`。 當連結的目的地是不同的動作或頁面時，不會重複使用環境值。

* 往返路由參數語法：使用雙星號 (`**`) catch-all 參數語法時，斜線不會經過編碼。

  在連結產生期間，除了斜線，路由系統還會對雙星號 (`**`) catch-all 參數 (例如 `{**myparametername}`) 中擷取的值進行編碼。 ASP.NET Core 2.2 或更新版本中以 `IRouter` 為基礎的路由支援雙星號 catch-all。

  舊版 ASP.NET Core 中的單一星號 catch-all (`{*myparametername}`) 參數語法仍會受到支援，而且斜線會經過編碼。

  | 路由              | 以下列項目產生的連結：<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (斜線會經過編碼)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>中介軟體範例

在下列範例中，中介軟體使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 建立列出市集產品的動作方法連結。 應用程式中的任何類別都可以使用連結產生器，方法是將它插入類別並呼叫 `GenerateLink`。

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>建立路由

大部分的應用程式會藉由呼叫 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或其中一個 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定義的類似擴充方法來定建立路由。 任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 擴充方法都會建立 <xref:Microsoft.AspNetCore.Routing.Route> 的執行個體，並將它新增至路由集合。

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由處理常式參數。 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 只會新增 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 所處理的路由。 若要深入了解 MVC 中的路由功能，請參閱 <xref:mvc/controllers/routing>。

下列程式碼範例是典型 ASP.NET Core MVC 路由定義所使用的 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 呼叫範例：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

此範本會比對 URL 路徑，並擷取路由值。 例如，路徑 `/Products/Details/17` 會產生下列路由值：`{ controller = Products, action = Details, id = 17 }`。

路由值是透過將 URL 路徑分割成區段，並比對每個區段與路由範本中的「路由參數」** 名稱來判定。 路由參數為具名。 參數是透過以括弧 `{ ... }` 括住參數名稱來定義。

上述範本也可以比對 URL 路徑 `/` 並產生值 `{ controller = Home, action = Index }`。 發生這種情況是因為 `{controller}` 和 `{action}` 路由參數有預設值，而 `id` 路由參數為選擇性參數。 路由參數名稱之後緊接著值的等號 (`=`) 會定義參數預設值。 路由參數名稱之後的問號 (`?`) 會定義選擇性參數。

在路由相符時，具有預設值的路由參數一定** 會產生路由值。 如果沒有對應的 URL 路徑區段，選擇性參數不會產生路由值。 如需路由範本情節和語法的詳細描述，請參閱[路由範本參考](#route-template-reference)一節。

在下列範例中，路由參數定義 `{id:int}` 會定義 `id` 路由參數的[路由條件約束](#route-constraint-reference)：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

此範本會符合 `/Products/Details/17` 等 URL 路徑，但不符合 `/Products/Details/Apples`。 路由條件約束會實作 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，並檢查路由值以進行驗證。 在此範例中，路由值 `id` 必須可以轉換為整數。 如需架構所提供之路由條件約束的說明，請參閱[路由條件約束參考](#route-constraint-reference)。

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 的其他多載接受 `constraints`、`dataTokens` 和 `defaults` 的值。 這些參數通常是用來傳遞匿名類型的物件，其中匿名類型的屬性名稱符合路由參數名稱。

下列 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 範例會建立對等的路由：

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> 對於簡單的路由而言，定義條件約束和預設的內嵌語法可能很方便。 不過，內嵌語法不支援某些情節 (例如資料語彙基元)。

下列範例將示範一些其他情節：

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

上述範本會比對 `/Blog/All-About-Routing/Introduction` 等 URL 路徑，並擷取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。 即使範本中沒有任何對應的路由參數，路由也會產生 `controller` 和 `action` 的預設路由值。 預設值可以在路由範本中指定。 `article` 路由參數透過在路由參數名稱之前加上雙星號 (`**`) 來定義為 *catch-all*。 全部擷取路由參數會擷取 URL 路徑的其餘部分，而且也可以符合空字串。

下列範例會新增路由條件約束和資料語彙基元：

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

上述範本會比對 `/en-US/Products/5` 等 URL 路徑，並擷取值 `{ controller = Products, action = Details, id = 5 }` 和資料語彙基元 `{ locale = en-US }`。

![[區域變數] 視窗語彙基元](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>路由類別 URL 產生

<xref:Microsoft.AspNetCore.Routing.Route> 類別也可以結合一組路由值與其路由範本來產生 URL。 這在邏輯上是比對 URL 路徑的反向處理序。

> [!TIP]
> 若要進一步了解 URL 產生，請假設您想要產生的 URL，並考慮路由範本將會如何比對該 URL。 產生的值為何？ 這與 URL 產生在 <xref:Microsoft.AspNetCore.Routing.Route> 類別中的運作方式大致相同。

下列範例使用一般 ASP.NET Core MVC 預設路由：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

路由值為 `{ controller = Products, action = List }` 時，會產生 URL `/Products/List`。 路由值會取代對應的路由參數，以形成 URL 路徑。 由於 `id` 是選擇性路由參數，因此沒有 `id` 值也可以成功產生 URL。

路由值為 `{ controller = Home, action = Index }` 時，會產生 URL `/`。 所提供的路由值符合預設值，因此可以放心地省略這些預設值對應的區段。

這兩個產生的 URL 會使用下列路由定義 (`/Home/Index` 和 `/`) 反覆存取，並產生用來產生 URL 的相同路由值。

> [!NOTE]
> 使用 ASP.NET Core MVC 的應用程式應該使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 來產生 URL，而不是直接呼叫路由。

如需 URL 產生的詳細資訊，請參閱 [URI 產生參考](#url-generation-reference)一節。

## <a name="use-routing-middleware"></a>使用路由中介軟體

參考應用程式專案檔中的 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)。

在 `Startup.ConfigureServices` 中，將路由新增至服務容器：

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

路由必須設定在 `Startup.Configure` 方法中。 範例應用程式使用下列 API：

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>：僅符合 HTTP GET 要求。
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

下表顯示使用指定 URL 的回應。

| URI                    | 回應                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/`      | 要求失敗，沒有相符項目。              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | 要求失敗，僅符合 HTTP GET。 |
| `GET /hello/Joe/Smith` | 要求失敗，沒有相符項目。              |

架構會提供一組擴充方法來建立路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)：

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

`Map[Verb]` 方法會使用條件約束，將路由限制為方法名稱中的 HTTP 指令動詞。 如需範例，請參閱 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 與 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。

## <a name="route-template-reference"></a>路由範本參考

大括弧 (`{ ... }`) 內的語彙基元定義路由相符時會繫結的「路由參數」**。 您可以在路由區段中定義多個路由參數，但其必須以常值分隔。 例如，`{controller=Home}{action=Index}` 不是有效的路由，因為 `{controller}` 與 `{action}` 之間沒有任何常值。 這些路由參數必須有一個名稱，並且可以指定其他屬性。

路由參數之外的常值文字 (例如，`{id}`) 和路徑分隔符號 `/` 必須符合 URL 中的文字。 文字比對會區分大小寫，並以 URL 路徑的已解碼表示法為基礎。 若要比對常值路由參數分隔符號 (`{` 或 `}`)，請重複字元 (`{{` 或 `}}`) 來將分隔符號逸出。

URL 模式嘗試擷取具有選擇性副檔名的檔案名稱時，具有其他考量。 以範本 `files/{filename}.{ext?}` 為例。 當 `filename` 和 `ext` 都存在值時，就會填入這兩個值。 如果 URL 中只有 `filename` 存在值，由於結尾的句點 (`.`) 是選擇性項目，因此路由相符。 下列 URL 符合此路由：

* `/files/myFile.txt`
* `/files/myFile`

您可以使用一個星號 (`*`) 或雙星號 (`**`) 作為路由參數的前置詞，以繫結至 URI 的其餘部分。 這稱為 *catch-all* 參數。 例如，`blog/{**slug}` 符合以 `/blog` 開頭且其後有任何值 (這會指派給 `slug` 路由值) 的所有 URI。 全部擷取參數也可以符合空字串。

當使用路由產生 URL (包括路徑分隔符號 (`/`) 字元) 時，catch-all 參數會逸出適當的字元。 例如，路由值為 `{ path = "my/path" }` 的路由 `foo/{*path}` 會產生 `foo/my%2Fpath`。 請注意逸出的斜線。 若要反覆存取路徑分隔符號字元，請使用 `**` 路由參數前置詞。 具有 `{ path = "my/path" }` 的路由 `foo/{**path}` 會產生 `foo/my/path`。

路由參數可能有「預設值」**，指定方法是在參數名稱之後指定預設值，並以等號 (`=`) 分隔。 例如，`{controller=Home}` 定義 `Home` 作為 `controller` 的預設值。 如果 URL 中沒有用於參數的任何值，則會使用預設值。 路由參數也可以設為選擇性，方法是在參數名稱結尾附加問號 (`?`)，如 `id?` 中所示。 選擇性值與預設路由參數之間的差異在於，具有預設值的路由參數一定會產生值&mdash;選擇性參數只有在要求 URL 提供值時才會有值。

路由參數可能具有條件約束，這些條件約束必須符合與 URL 繫結的路由值。 在路由參數名稱之後新增分號 (`:`) 和條件約束名稱，即可指定路由參數的「內嵌條件約束」**。 如果條件約束需要引數，這些引數會在條件約束名稱後面以括弧 (`(...)`) 括住。 指定多個內嵌條件約束的方法是附加另一個冒號 (`:`) 和條件約束名稱。

條件約束名稱和引述會傳遞至 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服務來建立 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的執行個體，以用於 URL 處理。 例如，路由範本 `blog/{article:minlength(10)}` 指定具有引數 `10` 的 `minlength` 條件約束。 如需路由條件約束詳細資訊和架構所提供的條件約束清單，請參閱[路由條件約束參考](#route-constraint-reference)一節。

路由參數也可以具有參數轉換器，以在產生連結及根據 URL 比對動作和頁面時，轉換參數的值。 與條件約束類似，可以透過在路徑參數名稱後面新增冒號 (`:`) 和轉換器名稱，將參數轉換器內嵌新增至路徑參數。 例如，路由範本 `blog/{article:slugify}` 會指定 `slugify` 轉換器。 如需參數轉換器的詳細資訊，請參閱[參數轉器參考](#parameter-transformer-reference)一節。

下表示範範例路由範本及其行為。

| 路由範本                           | 範例比對 URI    | 要求 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | 只比對單一路徑 `/hello`。                                     |
| `{Page=Home}`                            | `/`                     | 比對並將 `Page` 設定為 `Home`。                                         |
| `{Page=Home}`                            | `/Contact`              | 比對並將 `Page` 設定為 `Contact`。                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | 對應至 `Products` 控制器和 `List` 動作。                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 對應至 `Products` 控制器和 `Details` 動作 (`id` 設定為 123)。 |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | 對應至 `Home` 控制器和 `Index` 方法 (會忽略 `id`)。        |

使用範本通常是最簡單的路由方式。 條件約束和預設值也可以在路由範本外部指定。

> [!TIP]
> 啟用[記錄](xref:fundamentals/logging/index)以查看內建路由實作 (例如 <xref:Microsoft.AspNetCore.Routing.Route>) 如何符合要求。

## <a name="reserved-routing-names"></a>保留的路由名稱

下列關鍵字是保留的名稱，不能用作路由名稱或參數：

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>路由條件約束參考

路由條件約束執行時機是出現符合傳入 URL 的項目，並將 URL 路徑語彙基元化成路由值時。 路由條件約束通常會透過路由範本檢查相關聯的路由值，並對是否可接受值做出是/否決策。 某些路由條件約束會使用路由值以外的資料，以考慮是否可以路由要求。 例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以依據其 HTTP 指令動詞接受或拒絕要求。 條件約束可用於路由要求和連結產生。

> [!WARNING]
> 請勿針對**輸入驗證**使用條件約束。 如果針對**輸入驗證**使用條件約束，則無效的輸入會導致產生「404 - 找不到」** 回應，而不是「400 - 錯誤要求」** 與適當的錯誤訊息。 路由條件約束會用來**釐清**類似的路由，而不是用來驗證特定路由的輸入。

下表示範範例路由條件約束及其預期行為。

| 條件約束 | 範例 | 範例相符項目 | 附註 |
|------------|---------|-----------------|-------|
| `int` | `{id:int}` | `123456789`, `-123456789` | 符合任何整數。|
| `bool` | `{active:bool}` | `true`, `FALSE` | 符合 `true` 或 `false` 。 不區分大小寫。|
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 符合非變異 `DateTime` 文化特性中的有效值。 請參閱前面的警告。|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 符合非變異 `decimal` 文化特性中的有效值。 請參閱前面的警告。|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 符合非變異 `double` 文化特性中的有效值。 請參閱前面的警告。|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 符合非變異 `float` 文化特性中的有效值。 請參閱前面的警告。|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 符合有效的 `Guid` 值。|
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 符合有效的 `long` 值。|
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字串必須至少有4個字元。|
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 字串最多8個字元。|
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字串的長度必須剛好為12個字元。|
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字串必須至少為8個字元，最多16個字元。|
| `min(value)` | `{age:min(18)}` | `19` | 整數值必須至少為18。|
| `max(value)` | `{age:max(120)}` | `91` | 整數值最大值120。|
| `range(min,max)` | `{age:range(18,120)}` | `91` | 整數值必須至少為18，最大值為120。|
| `alpha` | `{name:alpha}` | `Rick` | 字串必須由一或多個字母字元組成 `a` - `z` 。 不區分大小寫。|
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字串必須符合正則運算式。 請參閱定義正則運算式的秘訣。|
| `required` | `{name:required}` | `Rick` | 用來強制在 URL 產生期間存在非參數值。|

以冒號分隔的多個條件約束，可以套用至單一參數。 例如，下列條件約束會將參數限制在 1 或更大的整數值：

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> 確認 URL 可以轉換成 CLR 類型的路由條件約束 (例如 `int` 或 `DateTime`) 一律使用不因國別而異的文化特性。 這些條件約束假設 URL 不可當地語系化。 架構提供的路由條件約束不會修改路由值中儲存的值。 所有從 URL 剖析而來的路由值會儲存為字串。 例如，`float` 條件約束會嘗試將路由值轉換成浮點數，但轉換的值只能用來確認它可以轉換成浮點數。

## <a name="regular-expressions"></a>規則運算式

ASP.NET Core 架構將 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` 新增至規則運算式建構函式。 如需這些成員的說明，請參閱 <xref:System.Text.RegularExpressions.RegexOptions>。

正則運算式使用的分隔符號和標記類似于路由和 c # 語言所使用的標記。 規則運算式的語彙基元必須逸出。 若要在路由中使用正則運算式 `^\d{3}-\d{2}-\d{4}$` ：

* 運算式必須 `\` 在字串中提供以雙反斜線字元形式在原始程式碼中提供的單一反斜線字元 `\\` 。
* 正則運算式必須是我們才能將 `\\` `\` 字串 escape 字元換用。
* `\\`使用[逐字字串常](/dotnet/csharp/language-reference/keywords/string)值時，不需要正則運算式。

若要將路由參數分隔符號 `{` 、 `}` 、 `[` 、 `]` ，請將運算式中的字元 `{{` `}` `[[` `]]` （，）加倍。 下表顯示正則運算式和已轉義的版本：

| 規則運算式    | 逸出的規則運算式     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

路由中使用的正則運算式通常會以插入 `^` 號字元開頭，並符合字串的開始位置。 運算式通常以貨幣符號 `$` 字元結尾，且字串的結尾相符。 `^` 和 `$` 字元可確保規則運算式符合整個路由參數值。 若不使用 `^` 與 `$` 字元，規則運算式會比對字串內的所有部分字串，這通常不是您想要的結果。 下表提供範例，並說明它們符合或無法符合的原因。

| 運算是   | String    | 相符項目 | 註解               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 是   | 子字串相符項目     |
| `[a-z]{2}`   | 123abc456 | 是   | 子字串相符項目     |
| `[a-z]{2}`   | mz        | 是   | 符合運算式    |
| `[a-z]{2}`   | MZ        | 是   | 不區分大小寫    |
| `^[a-z]{2}$` | hello     | 否    | 請參閱上述的 `^` 和 `$` |
| `^[a-z]{2}$` | 123abc456 | 否    | 請參閱上述的 `^` 和 `$` |

如需規則運算式語法的詳細資訊，請參閱 [.NET Framework 規則運算式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。

若要將參數限制為一組已知的可能值，請使用規則運算式。 例如，`{action:regex(^(list|get|create)$)}` 只會將 `action` 路由值與 `list`、`get` 或 `create` 相符。 如果已傳入條件約束字典，字串 `^(list|get|create)$` 則是對等項目。 已傳入條件約束字典 (未內嵌在範本內) 的條件約束，即使不符合其中一個已知的條件約束，也會被視為規則運算式。

## <a name="custom-route-constraints"></a>自訂路由條件約束

除了內建的路由限制式之外，自訂路由限制式也可以透過實作 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 介面來建立。 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 介面包含單一方法 `Match`，此方法會在滿足限制式時傳回 `true`，否則會傳回 `false`。

若要使用自訂 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，路由限制式型別必須必須向應用程式的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> (在應用程式的服務容器中) 註冊。 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是一個目錄，它將路由限制式機碼對應到可驗證那些限制式的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 實作。 更新應用程式的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 時，可在 `Startup.ConfigureServices` 中於進行 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼叫時更新，或透過使用 `services.Configure<RouteOptions>` 直接設定 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 來更新。 例如：

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

限制式接著能以一般方式套用到路由 (使用註冊限制式型別時使用名稱)。 例如：

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>參數轉換器參考

參數轉換程式：

* 會在為 <xref:Microsoft.AspNetCore.Routing.Route> 產生連結時執行。
* 實作 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。
* 是使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 進行設定的。
* 採用參數的路由值，並將它轉換為新的字串值。
* 導致在所產生連結中使用已轉換的值。

例如，具有 `Url.Action(new { article = "MyTestArticle" })` 之路由模式 `blog\{article:slugify}` 中的自訂 `slugify` 參數轉換器，會產生 `blog\my-test-article`。

若要在路由模式中使用參數轉換器，請先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 進行設定：

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

架構會使用參數轉換器來轉換端點解析的 URI。 例如，ASP.NET Core MVC 會使用參數轉換器，轉換用於比對 `area`、`controller`、`action` 和 `page` 的路由值。

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

使用上述的路由，動作 `SubscriptionManagementController.GetAll` 會與URI `/subscription-management/get-all` 比對。 參數轉換器不會變更用來產生連結的路由值。 例如，`Url.Action("GetAll", "SubscriptionManagement")` 會輸出 `/subscription-management/get-all`。

ASP.NET Core 針對搭配產生的路由使用參數轉換程式提供了 API 慣例：

* ASP.NET Core MVC 有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 慣例。 此慣例會將所指定參數轉換程式套用到應用程式中的所有屬性路由。 參數轉換程式會在被取代時轉換屬性路由語彙基元。 如需詳細資訊，請參閱[使用參數轉換程式自訂語彙基元取代](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。
* Razor 頁面具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 慣例。 此慣例會將指定的參數轉換器套用至所有自動探索到的 Razor 頁面。 參數轉換程式會轉換頁面路由的資料夾和檔案名區段 Razor 。 如需詳細資訊，請參閱[使用參數轉換程式自訂頁面路由](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。

## <a name="url-generation-reference"></a>URL 產生參考

下列範例示範如何在指定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情況下產生路由的連結。

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

在上述範例的結尾產生的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 是 `/package/create/123`。 字典提供「追蹤套件路由」範本 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。 如需詳細資訊，請參閱[使用路由中介軟體](#use-routing-middleware)一節或[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的範例程式碼。

<xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 建構函式的第二個參數是「環境值」** 的集合。 環境值便於使用，因為它們會限制開發人員必須在要求內容中指定的值數目。 目前要求的目前路由值被視為用於連結產生的環境值。 在 ASP.NET Core MVC 應用程式 `HomeController` 的 `About` 動作中，您不需要指定控制器路由值以連結到 `Index` 動作&mdash;會使用 `Home` 的環境值。

不符合參數的環境值會予以忽略。 當明確提供的值覆寫環境值時，也會忽略環境值。 URL 中的比對是從左到右。

明確提供但不符合路由區段的值會新增至查詢字串。 下表顯示使用路由範本 `{controller}/{action}/{id?}` 時的結果。

| 環境值                     | 明確值                        | 結果                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

如果路由具有未對應至參數的預設值，且該值會明確提供，它必須符合預設值：

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

連結產生只有在提供了 `controller` 與 `action` 的相符值時，才會產生此路由的連結。

## <a name="complex-segments"></a>複雜區段

複雜區段 (例如，`[Route("/x{token}y")]`) 會透過以非窮盡的方式，由右至左比對常值來處理。 請參閱[此程式碼](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解如何比對複雜區段的詳細解釋。 [程式法範例](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)不是由 ASP.NET Core 使用，但它提供一個好的複雜區段解釋。
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

路由功能負責將要求 URI 對應至路由處理常式，並分派傳入要求。 路由定義於應用程式，並在該應用程式啟動時進行設定。 路由可以選擇性地從要求中所包含的 URL 擷取值，然後這些值就可用於處理要求。 使用應用程式中已設定的路由，路由功能將能夠產生對應至路由處理常式的 URL。

若要使用 ASP.NET Core 2.1 中的最新路由情節，請將[相容性版本](xref:mvc/compatibility-version)指定為 `Startup.ConfigureServices` 中的 MVC 服務註冊：

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> 本文件涵蓋低階的 ASP.NET Core 路由。 如需 ASP.NET Core MVC 路由的資訊，請參閱 <xref:mvc/controllers/routing>。 如需頁面中路由慣例的詳細資訊 Razor ，請參閱 <xref:razor-pages/razor-pages-conventions> 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="routing-basics"></a>路由的基本概念

大部分應用程式都應該選擇基本的描述性路由傳送配置，讓 URL 可讀且有意義。 預設慣例路由 `{controller=Home}/{action=Index}/{id?}`：

* 支援基本的描述性路由配置。
* 適合作為 UI 型應用程式的起點。

在特殊情況下，開發人員通常會使用[屬性路由](xref:mvc/controllers/routing#attribute-routing)或專屬的慣例路由，新增額外的簡潔路由到應用程式的高流量區域 (例如，部落格和電子商務端點)。

Web API 應該使用屬性路由傳送來將應用程式功能模型建構為作業由 HTTP 指令動詞代表的資源集合。 這表示相同邏輯資源上的許多作業 (例如，GET、POST) 都會使用相同的 URL。 屬性路由提供仔細設計 API 公用端點配置所需的控制層級。

Razor 頁面應用程式會使用預設的傳統路由，在應用程式的 *Pages* 資料夾中提供命名資源。 有其他慣例可讓您自訂 Razor 頁面路由行為。 如需詳細資訊，請參閱 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。

URL 產生支援允許在不需要硬式編碼的 URL 來連結應用程式的情況下開發應用程式。 這項支援可讓您從基本路由設定開始，並在決定應用程式資源配置之後修改路由。

路由會使用的路由 <xref:Microsoft.AspNetCore.Routing.IRouter> 執行：

* 將傳入要求對應至「路由處理常式」**。
* 產生用於回應的 URL。

根據預設，應用程式有一個路由集合。 當要求抵達時，集合中的路由會依其存在於集合的順序進行處理。 此架構會嘗試對集合中的每個路由呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法，藉以比對傳入要求 URL 與集合中的路由。 回應可以根據路由資訊使用路由來產生 URL (例如，針對重新導向或連結)，因此避免硬式編碼的 URL，這有助於可維護性。

路由系統具有下列特性：

* 使用路由範本語法以 Token 化路由參數來定義路由。
* 允許傳統式和屬性式端點組態。
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 可用來判斷 URL 參數是否包含對指定端點條件約束有效的值。
* 應用程式模型（例如 MVC/ Razor Pages）會註冊其所有路由，其具有可預測的路由案例執行。
* 回應可以根據路由資訊使用路由來產生 URL (例如，針對重新導向或連結)，因此避免硬式編碼的 URL，這有助於可維護性。
* URL 是根據支援任意擴充性的路由所產生。 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 提供方法來建立 URL。
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
路由會透過 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 類別連線到[中介軟體](xref:fundamentals/middleware/index)管線。 [ASP.NET CORE mvc](xref:mvc/overview) 會將路由新增至中介軟體管線，作為其設定的一部分，並處理 MVC 和 Razor 頁面應用程式中的路由。 若要了解如何使用路由作為獨立元件，請參閱[使用路由中介軟體](#use-routing-middleware)一節。

### <a name="url-matching"></a>URL 比對

URL 比對是路由用來將傳入要求分派給「處理常式」** 的處理序。 這個處理序是基於 URL 路徑中的資料，但是可以擴展為考慮要求中的任何資料。 分派要求給不同處理常式的能力，是調整應用程式大小和複雜度的關鍵。

傳入要求將進入 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>，而後者會依序在每個路由上呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法。 <xref:Microsoft.AspNetCore.Routing.IRouter> 執行個體可將 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) 設定為非 Null 的 <xref:Microsoft.AspNetCore.Http.RequestDelegate>，來選擇是否要「處理」** 要求。 如果路由為要求設定了處理常式，則路由處理會停止，且會叫用該處理常式來處理要求。 如果找不到處理要求的路由處理常式，中介軟體會將要求傳遞給要求管線中的下一個中介軟體。

<xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的主要輸入是與目前要求建立關聯的 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)。 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 和 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) 是在比對路由之後設定的輸出。

呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的比對也會根據到目前為止所執行的要求處理，將 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的屬性設定為適當的值。

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是「路由值」** 的字典，而路由值產生自路由。 這些值通常是透過將 URL 語彙基元化來決定，可以用來接受使用者輸入，或在應用程式內做出進一步的分派決策。

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是其他資料的屬性包，而這些資料與相符路由相關。 提供了 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 來支援與每個路由建立關聯的狀態資料，因此應用程式可以依據符合哪一個路由來制定決策。 這些是開發人員定義的值，**不會**以任何方式影響路由的行為。 此外，儲藏在 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以是任何類型，對比之下，[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 則必須可轉換成字串或可從字串轉換。

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是成功符合要求的參與路由清單。 路由可以用巢狀方式置於彼此內部。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 屬性會透過導致產生相符項目的路由邏輯樹狀結構反映路徑。 一般而言，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一個項目是路由集合，應該用於產生 URL。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最後一個項目是相符的路由處理常式。

<a name="lg"></a>

### <a name="url-generation"></a>URL 產生

URL 產生是路由可用來依據一組路由值建立 URL 路徑的處理序。 這可讓您在路由處理常式和存取它們的 URL 之間建立邏輯分隔。

URL 產生遵循類似的反覆執行處理序，但開頭是呼叫路由集合 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法的使用者或架構程式碼。 每個「路由」** 會依序呼叫其 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法，直到傳回非 Null 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData> 為止。

<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的主要輸入是：

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

路由主要使用 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 和 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 所提供的路由值，以決定是否可能產生 URL，以及要包含哪些值。 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 是比對目前要求所產生的路由值集合。 相反地，<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 是指定如何產生目前作業所需之 URL 的路由值。 提供了 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>，以防路由應該取得服務或與目前內容建立關聯的其他資料。

> [!TIP]
> 將 [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) 視為 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) 的覆寫項目集合。 URL 產生會嘗試重複使用來自目前要求的路由值，讓您使用相同路由或路由值產生連結的 URL。

<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的輸出是 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。 <xref:Microsoft.AspNetCore.Routing.VirtualPathData> 是 <xref:Microsoft.AspNetCore.Routing.RouteData> 的平行處理。 <xref:Microsoft.AspNetCore.Routing.VirtualPathData> 包含用於輸出 URL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>，以及一些路由應該設定的其他屬性。

[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 屬性包含路由所產生的「虛擬路徑」**。 視您需求的不同，可能需要進一步處理路徑。 如果您想要以 HTML 呈現產生的 URL，請在前面加上應用程式的基底路徑。

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) 是成功產生 URL 的路由參考。

[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 屬性是其他資料的字典，而這些資料與產生 URL 的路由相關。 這是 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 的平行處理。

### <a name="create-routes"></a>建立路由

路由提供 <xref:Microsoft.AspNetCore.Routing.Route> 類別作為 <xref:Microsoft.AspNetCore.Routing.IRouter> 的標準實作。 在呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 時，<xref:Microsoft.AspNetCore.Routing.Route> 會使用「路由範本」** 語法來定義將比對 URL 路徑的模式。 在呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 時，<xref:Microsoft.AspNetCore.Routing.Route> 會使用相同的路由範本來產生 URL。

大部分的應用程式會藉由呼叫 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或其中一個 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定義的類似擴充方法來定建立路由。 任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 擴充方法都會建立 <xref:Microsoft.AspNetCore.Routing.Route> 的執行個體，並將它新增至路由集合。

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由處理常式參數。 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 只會新增 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 所處理的路由。 預設處理常式為 `IRouter`，該處理常式可能無法處理要求。 例如，ASP.NET Core MVC 通常會設定為預設處理常式，只處理符合可用控制器和動作的要求。 若要深入了解 MVC 中的路由功能，請參閱 <xref:mvc/controllers/routing>。

下列程式碼範例是典型 ASP.NET Core MVC 路由定義所使用的 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 呼叫範例：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

此範本會比對 URL 路徑，並擷取路由值。 例如，路徑 `/Products/Details/17` 會產生下列路由值：`{ controller = Products, action = Details, id = 17 }`。

路由值是透過將 URL 路徑分割成區段，並比對每個區段與路由範本中的「路由參數」** 名稱來判定。 路由參數為具名。 參數是透過以括弧 `{ ... }` 括住參數名稱來定義。

上述範本也可以比對 URL 路徑 `/` 並產生值 `{ controller = Home, action = Index }`。 發生這種情況是因為 `{controller}` 和 `{action}` 路由參數有預設值，而 `id` 路由參數為選擇性參數。 路由參數名稱之後緊接著值的等號 (`=`) 會定義參數預設值。 路由參數名稱之後的問號 (`?`) 會定義選擇性參數。

在路由相符時，具有預設值的路由參數一定** 會產生路由值。 如果沒有對應的 URL 路徑區段，選擇性參數不會產生路由值。 如需路由範本情節和語法的詳細描述，請參閱[路由範本參考](#route-template-reference)一節。

在下列範例中，路由參數定義 `{id:int}` 會定義 `id` 路由參數的[路由條件約束](#route-constraint-reference)：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

此範本會符合 `/Products/Details/17` 等 URL 路徑，但不符合 `/Products/Details/Apples`。 路由條件約束會實作 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，並檢查路由值以進行驗證。 在此範例中，路由值 `id` 必須可以轉換為整數。 如需架構所提供之路由條件約束的說明，請參閱[路由條件約束參考](#route-constraint-reference)。

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 的其他多載接受 `constraints`、`dataTokens` 和 `defaults` 的值。 這些參數通常是用來傳遞匿名類型的物件，其中匿名類型的屬性名稱符合路由參數名稱。

下列 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 範例會建立對等的路由：

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> 對於簡單的路由而言，定義條件約束和預設的內嵌語法可能很方便。 不過，內嵌語法不支援某些情節 (例如資料語彙基元)。

下列範例將示範一些其他情節：

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

上述範本會比對 `/Blog/All-About-Routing/Introduction` 等 URL 路徑，並擷取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。 即使範本中沒有任何對應的路由參數，路由也會產生 `controller` 和 `action` 的預設路由值。 預設值可以在路由範本中指定。 `article` 路由參數透過在路由參數名稱之前加上一個星號 (`*`) 來定義為 *catch-all*。 全部擷取路由參數會擷取 URL 路徑的其餘部分，而且也可以符合空字串。

下列範例會新增路由條件約束和資料語彙基元：

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

上述範本會比對 `/en-US/Products/5` 等 URL 路徑，並擷取值 `{ controller = Products, action = Details, id = 5 }` 和資料語彙基元 `{ locale = en-US }`。

![[區域變數] 視窗語彙基元](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>路由類別 URL 產生

<xref:Microsoft.AspNetCore.Routing.Route> 類別也可以結合一組路由值與其路由範本來產生 URL。 這在邏輯上是比對 URL 路徑的反向處理序。

> [!TIP]
> 若要進一步了解 URL 產生，請假設您想要產生的 URL，並考慮路由範本將會如何比對該 URL。 產生的值為何？ 這與 URL 產生在 <xref:Microsoft.AspNetCore.Routing.Route> 類別中的運作方式大致相同。

下列範例使用一般 ASP.NET Core MVC 預設路由：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

路由值為 `{ controller = Products, action = List }` 時，會產生 URL `/Products/List`。 路由值會取代對應的路由參數，以形成 URL 路徑。 由於 `id` 是選擇性路由參數，因此沒有 `id` 值也可以成功產生 URL。

路由值為 `{ controller = Home, action = Index }` 時，會產生 URL `/`。 所提供的路由值符合預設值，因此可以放心地省略這些預設值對應的區段。

這兩個產生的 URL 會使用下列路由定義 (`/Home/Index` 和 `/`) 反覆存取，並產生用來產生 URL 的相同路由值。

> [!NOTE]
> 使用 ASP.NET Core MVC 的應用程式應該使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 來產生 URL，而不是直接呼叫路由。

如需 URL 產生的詳細資訊，請參閱 [URI 產生參考](#url-generation-reference)一節。

## <a name="use-routing-middleware"></a>使用路由中介軟體

參考應用程式專案檔中的 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)。

在 `Startup.ConfigureServices` 中，將路由新增至服務容器：

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

路由必須設定在 `Startup.Configure` 方法中。 範例應用程式使用下列 API：

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>：僅符合 HTTP GET 要求。
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

下表顯示使用指定 URL 的回應。

| URI                    | 回應                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/`      | 要求失敗，沒有相符項目。              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | 要求失敗，僅符合 HTTP GET。 |
| `GET /hello/Joe/Smith` | 要求失敗，沒有相符項目。              |

如果您要設定單一路由，請呼叫傳入 `IRouter` 執行個體的 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>。 您不需要使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder>。

架構會提供一組擴充方法來建立路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)：

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

其中一些列出的方法 (例如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>) 需要 <xref:Microsoft.AspNetCore.Http.RequestDelegate>。 路由相符時，<xref:Microsoft.AspNetCore.Http.RequestDelegate> 會作為「路由處理常式」** 使用。 此系列中的其他方法允許設定中介軟體管線，以作為路由處理常式使用。 如果 `Map*` 方法不接受處理常式 (例如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>)，則會使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>。

`Map[Verb]` 方法會使用條件約束，將路由限制為方法名稱中的 HTTP 指令動詞。 如需範例，請參閱 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 與 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。

## <a name="route-template-reference"></a>路由範本參考

大括弧 (`{ ... }`) 內的語彙基元定義路由相符時會繫結的「路由參數」**。 您可以在路由區段中定義多個路由參數，但其必須以常值分隔。 例如，`{controller=Home}{action=Index}` 不是有效的路由，因為 `{controller}` 與 `{action}` 之間沒有任何常值。 這些路由參數必須有一個名稱，並且可以指定其他屬性。

路由參數之外的常值文字 (例如，`{id}`) 和路徑分隔符號 `/` 必須符合 URL 中的文字。 文字比對會區分大小寫，並以 URL 路徑的已解碼表示法為基礎。 若要比對常值路由參數分隔符號 (`{` 或 `}`)，請重複字元 (`{{` 或 `}}`) 來將分隔符號逸出。

URL 模式嘗試擷取具有選擇性副檔名的檔案名稱時，具有其他考量。 以範本 `files/{filename}.{ext?}` 為例。 當 `filename` 和 `ext` 都存在值時，就會填入這兩個值。 如果 URL 中只有 `filename` 存在值，由於結尾的句點 (`.`) 是選擇性項目，因此路由相符。 下列 URL 符合此路由：

* `/files/myFile.txt`
* `/files/myFile`

您可以使用星號 (`*`) 作為路由參數的前置詞，以繫結至 URI 的其餘部分。 這稱為「全部擷取」** 參數。 例如，`blog/{*slug}` 符合以 `/blog` 開頭且其後有任何值 (這會指派給 `slug` 路由值) 的所有 URI。 全部擷取參數也可以符合空字串。

當使用路由產生 URL (包括路徑分隔符號 (`/`) 字元) 時，catch-all 參數會逸出適當的字元。 例如，路由值為 `{ path = "my/path" }` 的路由 `foo/{*path}` 會產生 `foo/my%2Fpath`。 請注意逸出的斜線。

路由參數可能有「預設值」**，指定方法是在參數名稱之後指定預設值，並以等號 (`=`) 分隔。 例如，`{controller=Home}` 定義 `Home` 作為 `controller` 的預設值。 如果 URL 中沒有用於參數的任何值，則會使用預設值。 路由參數也可以設為選擇性，方法是在參數名稱結尾附加問號 (`?`)，如 `id?` 中所示。 選擇性值與預設路由參數之間的差異在於，具有預設值的路由參數一定會產生值&mdash;選擇性參數只有在要求 URL 提供值時才會有值。

路由參數可能具有條件約束，這些條件約束必須符合與 URL 繫結的路由值。 在路由參數名稱之後新增分號 (`:`) 和條件約束名稱，即可指定路由參數的「內嵌條件約束」**。 如果條件約束需要引數，這些引數會在條件約束名稱後面以括弧 (`(...)`) 括住。 指定多個內嵌條件約束的方法是附加另一個冒號 (`:`) 和條件約束名稱。

條件約束名稱和引述會傳遞至 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服務來建立 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的執行個體，以用於 URL 處理。 例如，路由範本 `blog/{article:minlength(10)}` 指定具有引數 `10` 的 `minlength` 條件約束。 如需路由條件約束詳細資訊和架構所提供的條件約束清單，請參閱[路由條件約束參考](#route-constraint-reference)一節。

下表示範範例路由範本及其行為。

| 路由範本                           | 範例比對 URI    | 要求 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | 只比對單一路徑 `/hello`。                                     |
| `{Page=Home}`                            | `/`                     | 比對並將 `Page` 設定為 `Home`。                                         |
| `{Page=Home}`                            | `/Contact`              | 比對並將 `Page` 設定為 `Contact`。                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | 對應至 `Products` 控制器和 `List` 動作。                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 對應至 `Products` 控制器和 `Details` 動作 (`id` 設定為 123)。 |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | 對應至 `Home` 控制器和 `Index` 方法 (會忽略 `id`)。        |

使用範本通常是最簡單的路由方式。 條件約束和預設值也可以在路由範本外部指定。

> [!TIP]
> 啟用[記錄](xref:fundamentals/logging/index)以查看內建路由實作 (例如 <xref:Microsoft.AspNetCore.Routing.Route>) 如何符合要求。

## <a name="route-constraint-reference"></a>路由條件約束參考

路由條件約束執行時機是出現符合傳入 URL 的項目，並將 URL 路徑語彙基元化成路由值時。 路由條件約束通常會透過路由範本檢查相關聯的路由值，並對是否可接受值做出是/否決策。 某些路由條件約束會使用路由值以外的資料，以考慮是否可以路由要求。 例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以依據其 HTTP 指令動詞接受或拒絕要求。 條件約束可用於路由要求和連結產生。

> [!WARNING]
> 請勿針對**輸入驗證**使用條件約束。 如果針對**輸入驗證**使用條件約束，則無效的輸入會導致產生「404 - 找不到」** 回應，而不是「400 - 錯誤要求」** 與適當的錯誤訊息。 路由條件約束會用來**釐清**類似的路由，而不是用來驗證特定路由的輸入。

下表示範範例路由條件約束及其預期行為。

| constraint (條件約束) | 範例 | 範例相符項目 | 附註 |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | 符合任何整數 |
| `bool` | `{active:bool}` | `true`, `FALSE` | 符合 `true` 或 `false` (不區分大小寫) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 符合非變異 `DateTime` 文化特性中的有效值。 請參閱前面的警告。|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 符合非變異 `decimal` 文化特性中的有效值。 請參閱前面的警告。|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 符合非變異 `double` 文化特性中的有效值。 請參閱前面的警告。|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 符合非變異 `float` 文化特性中的有效值。 請參閱前面的警告。|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 符合有效的 `Guid` 值 |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | 符合有效的 `long` 值 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字串必須至少有 4 個字元 |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 字串不能超過 8 個字元 |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字串長度必須剛好是 12 個字元 |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字串長度必須至少有 8 個字元，但不能超過 16 個字元 |
| `min(value)` | `{age:min(18)}` | `19` | 整數值必須至少為 18 |
| `max(value)` | `{age:max(120)}` | `91` | 整數值不能超過 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 整數值必須至少為 18，但不能超過 120 |
| `alpha` | `{name:alpha}` | `Rick` | 字串必須包含一或多個字母字元 (`a`-`z`，不區分大小寫) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字串必須符合規則運算式 (請參閱有關定義規則運算式的提示) |
| `required` | `{name:required}` | `Rick` | 用來強制執行在 URL 產生期間呈現非參數值 |

以冒號分隔的多個條件約束，可以套用至單一參數。 例如，下列條件約束會將參數限制在 1 或更大的整數值：

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> 確認 URL 可以轉換成 CLR 類型的路由條件約束 (例如 `int` 或 `DateTime`) 一律使用不因國別而異的文化特性。 這些條件約束假設 URL 不可當地語系化。 架構提供的路由條件約束不會修改路由值中儲存的值。 所有從 URL 剖析而來的路由值會儲存為字串。 例如，`float` 條件約束會嘗試將路由值轉換成浮點數，但轉換的值只能用來確認它可以轉換成浮點數。

## <a name="regular-expressions"></a>規則運算式

ASP.NET Core 架構將 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` 新增至規則運算式建構函式。 如需這些成員的說明，請參閱 <xref:System.Text.RegularExpressions.RegexOptions>。

規則運算式使用的分隔符號和語彙基元，類似於路由和 C# 語言所使用的分隔符號和語彙基元。 規則運算式的語彙基元必須逸出。 若要在路由中使用規則運算式 `^\d{3}-\d{2}-\d{4}$`，運算式必須以字串中所提供的 `\` (單一反斜線) 字元作為 C# 原始程式檔中的 `\\` (雙反斜線) 字元，才能逸出 `\` 字串逸出字元 (除非使用[逐字字串常值](/dotnet/csharp/language-reference/keywords/string))。 若要逸出路由參數分隔符號字元 (`{`、`}`、`[`、`]`)，請在運算式中使用雙字元 (`{{`、`}`、`[[`、`]]`). 下表顯示規則運算式和逸出的版本。

| 規則運算式    | 逸出的規則運算式     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

路由中所使用的規則運算式通常以插入號 (`^`) 字元開頭，並符合字串的開始位置。 運算式通常以貨幣符號 (`$`) 字元結尾，並符合字串的結尾。 `^` 和 `$` 字元可確保規則運算式符合整個路由參數值。 若不使用 `^` 與 `$` 字元，規則運算式會比對字串內的所有部分字串，這通常不是您想要的結果。 下表提供範例，並說明它們符合或無法符合的原因。

| 運算是   | String    | 相符項目 | 註解               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 是   | 子字串相符項目     |
| `[a-z]{2}`   | 123abc456 | 是   | 子字串相符項目     |
| `[a-z]{2}`   | mz        | 是   | 符合運算式    |
| `[a-z]{2}`   | MZ        | 是   | 不區分大小寫    |
| `^[a-z]{2}$` | hello     | 否    | 請參閱上述的 `^` 和 `$` |
| `^[a-z]{2}$` | 123abc456 | 否    | 請參閱上述的 `^` 和 `$` |

如需規則運算式語法的詳細資訊，請參閱 [.NET Framework 規則運算式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。

若要將參數限制為一組已知的可能值，請使用規則運算式。 例如，`{action:regex(^(list|get|create)$)}` 只會將 `action` 路由值與 `list`、`get` 或 `create` 相符。 如果已傳入條件約束字典，字串 `^(list|get|create)$` 則是對等項目。 已傳入條件約束字典 (未內嵌在範本內) 的條件約束，即使不符合其中一個已知的條件約束，也會被視為規則運算式。

## <a name="custom-route-constraints"></a>自訂路由限制式

除了內建的路由限制式之外，自訂路由限制式也可以透過實作 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 介面來建立。 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 介面包含單一方法 `Match`，此方法會在滿足限制式時傳回 `true`，否則會傳回 `false`。

若要使用自訂 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，路由限制式型別必須必須向應用程式的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> (在應用程式的服務容器中) 註冊。 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是一個目錄，它將路由限制式機碼對應到可驗證那些限制式的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 實作。 更新應用程式的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 時，可在 `Startup.ConfigureServices` 中於進行 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼叫時更新，或透過使用 `services.Configure<RouteOptions>` 直接設定 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 來更新。 例如：

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

限制式接著能以一般方式套用到路由 (使用註冊限制式型別時使用名稱)。 例如：

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>URL 產生參考

下列範例示範如何在指定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情況下產生路由的連結。

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

在上述範例的結尾產生的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 是 `/package/create/123`。 字典提供「追蹤套件路由」範本 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。 如需詳細資訊，請參閱[使用路由中介軟體](#use-routing-middleware)一節或[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的範例程式碼。

<xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 建構函式的第二個參數是「環境值」** 的集合。 環境值便於使用，因為它們會限制開發人員必須在要求內容中指定的值數目。 目前要求的目前路由值被視為用於連結產生的環境值。 在 ASP.NET Core MVC 應用程式 `HomeController` 的 `About` 動作中，您不需要指定控制器路由值以連結到 `Index` 動作&mdash;會使用 `Home` 的環境值。

不符合參數的環境值會予以忽略。 當明確提供的值覆寫環境值時，也會忽略環境值。 URL 中的比對是從左到右。

明確提供但不符合路由區段的值會新增至查詢字串。 下表顯示使用路由範本 `{controller}/{action}/{id?}` 時的結果。

| 環境值                     | 明確值                        | 結果                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

如果路由具有未對應至參數的預設值，且該值會明確提供，它必須符合預設值：

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

連結產生只有在提供了 `controller` 與 `action` 的相符值時，才會產生此路由的連結。

## <a name="complex-segments"></a>複雜區段

複雜區段 (例如，`[Route("/x{token}y")]`) 會透過以非窮盡的方式，由右至左比對常值來處理。 請參閱[此程式碼](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解如何比對複雜區段的詳細解釋。 [程式法範例](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)不是由 ASP.NET Core 使用，但它提供一個好的複雜區段解釋。

::: moniker-end
