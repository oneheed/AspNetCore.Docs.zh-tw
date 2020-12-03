---
title: ASP.NET Core 中的基本 JSON Api Route-to-code
author: jamesnk
description: 瞭解如何使用 Route-to-code 和 JSON 擴充方法來建立輕量的 json Web api。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556602"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>ASP.NET Core 中的基本 JSON Api Route-to-code

依 [James 牛頓](https://github.com/jamesnk)

ASP.NET Core 支援多種方法來建立 JSON web Api：

* [ASP.NET Core WEB API](xref:web-api/index) 提供完整的架構來建立 api。 藉由繼承來建立服務 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。 架構所提供的部分功能包括模型系結、驗證、內容協商、輸入和輸出格式，以及 OpenAPI。
* Route-to-code 是 ASP.NET Core web API 的非架構替代方案。 Route-to-code 將 [ASP.NET Core 路由](xref:fundamentals/routing) 直接連接到您的程式碼。 您的程式碼會從要求中讀取並寫入回應。 Route-to-code 沒有 web API 的 advanced 功能，但您也不需要進行任何設定，就能使用它。

Route-to-code 在建立小型和基本的 JSON web Api 時，是不錯的方法。

## <a name="create-json-web-apis"></a>建立 JSON web Api

ASP.NET Core 提供協助程式方法，可輕鬆建立 JSON web Api：

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> 檢查 `Content-Type` JSON 內容類型的標頭。
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 從要求讀取 JSON，並將它還原序列化為指定的類型。
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 將指定的值以 JSON 寫入至回應主體，並將回應內容類型設定為 `application/json` 。

以路由為基礎的輕量 JSON Api 是在 *Startup.cs* 中指定。 路由和 API 邏輯會在中設定 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 為應用程式要求管線的一部分。

### <a name="write-json-response"></a>寫入 JSON 回應

請考慮下列程式碼，以設定應用程式的 JSON API：

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

上述程式碼：

* 以路由範本的形式新增 HTTP GET API 端點 `/hello/{name:alpha}` 。
* 當路由相符時，API 會讀取要求的 `name` 路由值。
* 以 JSON 回應的形式寫入匿名型別 `WriteAsJsonAsync` 。

### <a name="read-json-request"></a>讀取 JSON 要求

`HasJsonContentType` 和 `ReadFromJsonAsync` 可以用來還原序列化以路由為基礎的 JSON API 中的 json 回應：

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

上述程式碼：

* 以路由範本的形式新增 HTTP POST API 端點 `/weather` 。
* 當路由相符時，會 `HasJsonContentType` 驗證要求內容類型。 非 JSON 內容類型會傳回415狀態碼。
* 如果內容類型為 JSON，則會將要求內容還原序列化 `ReadFromJsonAsync` 。

### <a name="configure-json-serialization"></a>設定 JSON 序列化

有兩種方式可以自訂 JSON 序列化：

* 您可以在方法中使用來設定預設序列化選項 <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` 。
* `WriteAsJsonAsync` 和 `ReadFromJsonAsync` 具有接受物件的多載 <xref:System.Text.Json.JsonSerializerOptions> 。 此選項物件會覆寫預設選項。

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>驗證和授權

Route-to-code 支援驗證和授權。 屬性（例如 `[Authorize]` 和 `[AllowAnonymous]` ）無法放在對應至要求委派的端點上。 相反地，會使用 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> 和擴充方法來新增授權中繼資料 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> 。

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>相依性插入

使用函式的相依性[插入 (DI) ](xref:fundamentals/dependency-injection)無法搭配使用 Route-to-code 。 Web API 會使用插入至函式的服務，為您建立一個控制器。 執行端點時不會建立類型，因此必須手動解決服務。

以路由為基礎的 Api 可以用 <xref:System.IServiceProvider> 來解析服務：

* 暫時性和範圍的存留期服務（例如 `DbContext` ）必須從端點的要求委派內的 [HttpCoNtext RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 解析。
* 您 `ILogger` 可以從 [IEndpointRouteBuilder ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)中解析單一存留期服務（例如）。 服務可以在要求委派之外解析，並且在端點之間共用。

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

廣泛使用 DI 的 Api 應該考慮使用支援 DI 的 ASP.NET Core 應用程式類型。 例如，ASP.NET Core web API。 使用控制器的函式插入服務，比手動解析服務更容易。

## <a name="api-project-structure"></a>API 專案結構

以路由為基礎的 Api 不一定要位於 *Startup.cs* 中。 Api 可以放在其他檔案中，並在啟動時對應 `UseEndpoints` 。 這種方法可減少啟動設定檔案的大小。

請考慮下列 `UserApi` 定義方法的靜態類別 `Map` 。 方法會對應以路由為基礎的 Api。

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

在 `Startup.Configure` 方法中， `Map` 會在中呼叫方法和其他類別的靜態方法 `UseEndpoints` ：

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>相較于 Web API，值得注意的遺漏功能

Route-to-code 是針對基本 JSON Api 所設計。 它並不支援 ASP.NET Core Web API 提供的許多先進功能。

未提供的功能 Route-to-code 包括：

* 模型繫結
* 模型驗證
* OpenAPI/Swagger
* 內容協商
* 函數相依性插入
* `ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807)) 

如果需要上述清單中的某些功能，請考慮使用 [ASP.NET Core WEB API](xref:web-api/index) 來建立 api。

## <a name="additional-resources"></a>其他資源

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
