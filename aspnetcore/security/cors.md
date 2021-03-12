---
title: 在 ASP.NET Core 中啟用 (CORS) 的跨原始來源要求
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 應用程式中允許或拒絕跨原始來源要求的標準。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 7afa8105e0ab007153d5c3e8238765d4e9f22641
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586796"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>在 ASP.NET Core 中啟用 (CORS) 的跨原始來源要求

::: moniker range=">= aspnetcore-3.0"

由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)

本文說明如何在 ASP.NET Core 應用程式中啟用 CORS。

瀏覽器安全性可防止網頁提出要求，而不是與提供網頁的不同網域。 這項限制稱為 *相同原始來源原則*。 相同來源原則可防止惡意網站從另一個網站讀取敏感性資料。 有時，您可能會想要允許其他網站對您的應用程式進行跨原始來源的要求。 如需詳細資訊，請參閱 [MOZILLA CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。

[跨原始資源分享](https://www.w3.org/TR/cors/) (CORS) ：

* 是一種 W3C 標準，可讓伺服器放寬相同的原始原則。
* 不 **是安全性** 功能，CORS 放寬安全性。 API 不會藉由允許 CORS 來更安全。 如需詳細資訊，請參閱 [CORS 的運作方式](#how-cors)。
* 允許伺服器明確允許某些跨原始來源的要求，同時拒絕其他要求。
* 比先前的技術（例如 [JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更有彈性。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="same-origin"></a>相同的來源

如果兩個 Url 具有相同的配置、主機和埠 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，其來源相同。

這兩個 Url 的來源相同：

* `https://example.com/foo.html`
* `https://example.com/bar.html`

這些 Url 的來源與前兩個 Url 不同：

* `https://example.net`：不同的網域
* `https://www.example.com/foo.html`：不同的子域
* `http://example.com/foo.html`：不同的配置
* `https://example.com:9000/foo.html`：不同的埠

## <a name="enable-cors"></a>啟用 CORS

有三種方式可以啟用 CORS：

* 在中介軟體中使用 [命名原則](#np) 或 [預設原則](#dp)。
* 使用 [端點路由](#ecors)。
* 使用 [[EnableCors]](#attr) 屬性。

使用具有命名原則的 [[EnableCors]](#attr) 屬性，可提供最精細的控制項來限制支援 CORS 的端點。

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A> 使用時，必須先呼叫 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> `UseResponseCaching` 。

下列各節將詳細說明每種方法。

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>具有命名原則和中介軟體的 CORS

CORS 中介軟體會處理跨原始來源的要求。 下列程式碼會將 CORS 原則套用至具有指定來源的所有應用程式端點：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

上述程式碼：

* 將原則名稱設定為 `_myAllowSpecificOrigins` 。 原則名稱是任意的。
* 呼叫 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 擴充方法，並指定  `_myAllowSpecificOrigins` CORS 原則。 `UseCors` 新增 CORS 中介軟體。 的呼叫 `UseCors` 必須放在之後 `UseRouting` ，但在之前 `UseAuthorization` 。 如需詳細資訊，請參閱 [中介軟體順序](xref:fundamentals/middleware/index#middleware-order)。
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的呼叫。 Lambda 接受 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 物件。 設定[選項](#cors-policy-options)（例如 `WithOrigins` ）將在本文稍後說明。
* 啟用 `_myAllowSpecificOrigins` 所有控制器端點的 CORS 原則。 請參閱 [端點路由](#ecors) ，以將 CORS 原則套用至特定端點。
* 使用回應快取 [中介軟體](xref:performance/caching/middleware)時，請 <xref:Owin.CorsExtensions.UseCors%2A> 先呼叫 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> 。

使用端點路由時，CORS 中介軟體 **必須** 設定為在對和的呼叫之間執行 `UseRouting` `UseEndpoints` 。

請參閱 [測試 CORS](#testc) ，以取得測試程式碼類似上述程式碼的指示。

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法呼叫會將 CORS 服務新增至應用程式的服務容器：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

如需詳細資訊，請參閱本檔中的 [CORS 原則選項](#cpo) 。

這些 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 方法可以連結，如下列程式碼所示：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

注意：指定的 URL **不** 能包含尾端斜線 (`/`) 。 如果 URL 以終止 `/` ，則比較會 `false` 傳回，而且不會傳回任何標頭。

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>使用預設原則和中介軟體的 CORS

下列反白顯示的程式碼會啟用預設 CORS 原則：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

上述程式碼會將預設 CORS 原則套用至所有控制器端點。

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>使用端點路由來啟用 Cors

使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援 [自動預檢要求](#apf)。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/20709) 和 [使用端點路由測試 CORS 和 [HttpOptions]](#tcer)。

使用端點路由時，您可以使用一組擴充方法，根據每個端點來啟用 CORS <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

在上述程式碼中：

* `app.UseCors` 啟用 CORS 中介軟體。 由於尚未設定預設原則，因此 `app.UseCors()` 單獨不會啟用 CORS。
* `/echo`和控制器端點允許使用指定原則的跨原始來源要求。
* `/echo2`和 Razor Pages 端點 **不** 允許跨原始來源的要求，因為未指定預設原則。

[[DisableCors]](#dc) **屬性不會停用** 端點路由所啟用的 CORS `RequireCors` 。

如需測試程式碼的相關指示，請參閱 [使用端點路由與 [HttpOptions]](#tcer) 。

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>使用屬性來啟用 CORS

使用 [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 屬性來啟用 CORS，並只將命名原則套用至需要 cors 的端點，可提供最精細的控制。

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)屬性提供全域套用 CORS 的替代方案。 `[EnableCors]`屬性會針對選取的端點啟用 CORS，而不是所有端點：

* `[EnableCors]` 指定預設原則。
* `[EnableCors("{Policy String}")]` 指定命名原則。

`[EnableCors]`屬性可套用至：

* Razor 網頁 `PageModel`
* 控制器
* 控制器動作方法

您可以使用屬性，將不同的原則套用至控制器、頁面模型或動作方法 `[EnableCors]` 。 當 `[EnableCors]` 屬性套用至控制器、頁面模型或動作方法，並在中介軟體啟用 CORS 時，會套用 **這兩個** 原則。 **建議您不要結合原則。使用** `[EnableCors]` **屬性或中介軟體，而不是在相同的應用程式中。**

下列程式碼會將不同的原則套用至每個方法：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

下列程式碼會建立兩個 CORS 原則：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

針對限制 CORS 要求的最精細控制：

* `[EnableCors("MyPolicy")]`與命名原則搭配使用。
* 請勿定義預設原則。
* 請勿使用 [端點路由](#ecors)。

下一節中的程式碼符合上述清單。

請參閱 [測試 CORS](#testc) ，以取得測試程式碼類似上述程式碼的指示。

<a name="dc"></a>

### <a name="disable-cors"></a>停用 CORS

[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **屬性不會停用**[端點路由](#ecors)已啟用的 CORS。

下列程式碼會定義 CORS 原則 `"MyPolicy"` ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

下列程式碼會停用動作的 CORS `GetValues2` ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

上述程式碼：

* 不會使用 [端點路由](#ecors)來啟用 CORS。
* 未定義 [預設 CORS 原則](#dp)。
* 使用 [[EnableCors ( "MyPolicy" ) ]](#attr) 來啟用 `"MyPolicy"` 控制器的 CORS 原則。
* 停用方法的 CORS `GetValues2` 。

請參閱 [測試 CORS](#testc) 以取得測試上述程式碼的指示。

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 原則選項

本節說明可在 CORS 原則中設定的各種選項：

* [設定允許的來源](#set-the-allowed-origins)
* [設定允許的 HTTP 方法](#set-the-allowed-http-methods)
* [設定允許的要求標頭](#set-the-allowed-request-headers)
* [設定公開的回應標頭](#set-the-exposed-response-headers)
* [跨原始來源要求中的認證](#credentials-in-cross-origin-requests)
* [設定預檢到期時間](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中呼叫 `Startup.ConfigureServices` 。 對於某些選項而言，先閱讀「 [CORS 的運作方式](#how-cors) 」一節可能很有説明。

## <a name="set-the-allowed-origins"></a>設定允許的來源

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允許所有來源中具有任何配置 (或) 的 CORS 要求 `http` `https` 。 `AllowAnyOrigin` 不安全，因為 *任何網站* 都可以對應用程式進行跨原始來源的要求。

> [!NOTE]
> 指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的設定，而且可能會導致跨網站要求偽造。 當以這兩種方法設定應用程式時，CORS 服務會傳回不正確 CORS 回應。

`AllowAnyOrigin` 影響預檢要求和 `Access-Control-Allow-Origin` 標頭。 如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：將原則的屬性設定為函式 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> ，以在評估是否允許原始來源時，允許原始來源符合已設定的萬用字元網域。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>設定允許的 HTTP 方法

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 允許任何 HTTP 方法：
* 影響預檢要求和 `Access-Control-Allow-Methods` 標頭。 如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。

### <a name="set-the-allowed-request-headers"></a>設定允許的要求標頭

若要允許在 CORS 要求中傳送特定標頭（稱為「 [作者要求標頭](https://xhr.spec.whatwg.org/#request)」），請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 並指定允許的標頭：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

若要允許所有 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader` 影響預檢要求和 [存取控制要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 標頭標頭。 如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。

`WithHeaders`只有當傳送的標頭 `Access-Control-Request-Headers` 完全符合中所述的標頭時，才可能符合所指定之特定標頭的 CORS 中介軟體原則 `WithHeaders` 。

例如，假設應用程式設定如下：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS 中介軟體會拒絕具有下列要求標頭的預檢要求，因為 `Content-Language` ([HeaderNames ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) 未列在 `WithHeaders` ：

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

應用程式會傳回 *200 OK* 回應，但不會傳送 CORS 標頭。 因此，瀏覽器不會嘗試跨原始來源要求。

### <a name="set-the-exposed-response-headers"></a>設定公開的回應標頭

根據預設，瀏覽器不會將所有回應標頭公開給應用程式。 如需詳細資訊，請參閱 [W3C 跨原始資源分享 (術語) ：簡單的回應標頭](https://www.w3.org/TR/cors/#simple-response-header)。

預設可用的回應標頭為：

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 規格會呼叫這些標頭 *簡單的回應標頭*。 若要讓應用程式使用其他標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>跨原始來源要求中的認證

認證需要 CORS 要求中的特殊處理。 根據預設，瀏覽器不會傳送具有跨原始來源要求的認證。 認證包括 cookie s 和 HTTP 驗證配置。 若要傳送具有跨原始來源要求的認證，用戶端必須設定 `XMLHttpRequest.withCredentials` 為 `true` 。

`XMLHttpRequest`直接使用：

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

使用 jQuery：

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

使用 [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

伺服器必須允許認證。 若要允許跨原始來源認證，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

HTTP 回應包含 `Access-Control-Allow-Credentials` 標頭，它會告知瀏覽器伺服器允許跨原始來源要求的認證。

如果瀏覽器傳送認證但回應不包含有效的 `Access-Control-Allow-Credentials` 標頭，則瀏覽器不會公開應用程式的回應，而且跨原始來源要求會失敗。

允許跨原始來源的認證有安全性風險。 另一個網域的網站可以代表使用者將已登入使用者的認證傳送給應用程式，而不需要使用者的知識。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 規格也指出 `"*"` 如果標頭存在，設定來源 (所有來源) 無效 `Access-Control-Allow-Credentials` 。

<a name="pref"></a>

## <a name="preflight-requests"></a>預檢要求

針對某些 CORS 要求，瀏覽器會先傳送額外的 [選項](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 要求，再進行實際要求。 此要求稱為 [預檢要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)。 如果下列 **所有** 條件都成立，則瀏覽器可以略過預檢要求：

* 要求方法為 GET、HEAD 或 POST。
* 應用程式不會設定 `Accept` 、、 `Accept-Language` `Content-Language` 、 `Content-Type` 或以外的要求標頭 `Last-Event-ID` 。
* `Content-Type`如果設定，標頭會有下列其中一個值：
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

針對用戶端要求所設定之要求標頭的規則，會套用至應用程式透過在物件上呼叫所設定的標頭 `setRequestHeader` `XMLHttpRequest` 。 CORS 規格會呼叫這些標頭的 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)。 此規則不適用於瀏覽器可以設定的標頭，例如 `User-Agent` 、 `Host` 或 `Content-Length` 。

以下是類似于本檔之 [測試 CORS](#testc)區段中 **[Put test]** 按鈕所提出之預檢要求的範例回應。

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

預檢要求會使用 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 方法。 它可能包含下列標頭：

* [存取控制-要求-方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)：將用於實際要求的 HTTP 方法。
* [存取控制-要求-標頭](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)：應用程式在實際要求上設定的要求標頭清單。 如先前所述，這並不包括瀏覽器所設定的標頭，例如 `User-Agent` 。
* [存取控制-允許方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

如果預檢要求遭到拒絕，應用程式就會傳回 `200 OK` 回應，但不會設定 CORS 標頭。 因此，瀏覽器不會嘗試跨原始來源要求。 如需拒絕預檢要求的範例，請參閱本檔的「 [測試 CORS](#testc) 」一節。

使用 F12 工具，主控台應用程式會根據瀏覽器顯示類似下列其中一項的錯誤：

* Firefox：跨原始來源要求遭到封鎖：相同的原始原則不允許在中讀取遠端資源 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` 。  (原因： CORS 要求未成功) 。 [深入了解](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* 以 Chromium 為基礎： ' ' 來源 ' ' 的 fetch 存取 https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net 已被 CORS 原則封鎖：預檢要求的回應未通過存取控制檢查：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。 如果不透明回應適合您的需求，請將要求的模式設定為 'no-cors' 以在停用 CORS 之下擷取資源。

若要允許特定標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

若要允許所有 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

瀏覽器的設定方式不一致 `Access-Control-Request-Headers` 。 如果其中一項：

* 標頭會設定為以外的任何其他 `"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 呼叫：至少包含 `Accept` 、 `Content-Type` 和 `Origin` ，以及您想要支援的任何自訂標頭。

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>自動預檢要求碼

套用 CORS 原則的時機為：

* `app.UseCors`在中呼叫全域 `Startup.Configure` 。
* 使用 `[EnableCors]` 屬性。

ASP.NET Core 會回應預檢 OPTIONS 要求。

使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援自動預檢要求。

本檔的「 [測試 CORS](#testc) 」區段會示範此行為。

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] 預檢要求的屬性

使用適當的原則啟用 CORS 時，ASP.NET Core 通常會自動回應 CORS 預檢要求。 在某些情況下，可能不會發生這種情況。 例如，搭配 [端點路由使用 CORS](#ecors)。

下列程式碼會使用 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 屬性來建立選項要求的端點：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

請參閱 [使用端點路由測試 CORS 和 [HttpOptions]](#tcer) ，以取得測試上述程式碼的指示。

### <a name="set-the-preflight-expiration-time"></a>設定預檢到期時間

`Access-Control-Max-Age`標頭會指定可以快取預檢要求回應的時間長度。 若要設定此標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 的運作方式

本節說明在 HTTP 訊息層級的 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 要求中發生的情況。

* CORS **不** 是安全性功能。 CORS 是一種 W3C 標準，可讓伺服器放寬相同的原始原則。
  * 例如，惡意執行者可以針對您的網站使用 [跨網站腳本 (XSS) ](xref:security/cross-site-scripting) ，並對其已啟用 CORS 的網站執行跨網站要求以竊取資訊。
* API 不會藉由允許 CORS 而更安全。
  * 用戶端 (瀏覽器) 來強制執行 CORS。 伺服器會執行要求並傳迴響應，這是傳回錯誤並封鎖回應的用戶端。 例如，下列任何一項工具都會顯示伺服器回應：
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * 在網址列中輸入 URL 的網頁瀏覽器。
* 這是伺服器允許瀏覽器執行跨原始 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 或 [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 要求的方法，否則會被禁止。
  * 沒有 CORS 的瀏覽器無法進行跨原始來源的要求。 在 CORS 之前，會使用 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 來規避這種限制。 JSONP 不會使用 XHR，它會使用 `<script>` 標記來接收回應。 允許跨原始來源載入腳本。

[CORS 規格](https://www.w3.org/TR/cors/)引進了數個新的 HTTP 標頭，可啟用跨原始來源要求。 如果瀏覽器支援 CORS，則會自動為跨原始來源要求設定這些標頭。 不需要自訂 JavaScript 程式碼來啟用 CORS。

已部署[範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)上的[PUT 測試按鈕](https://cors3.azurewebsites.net/test)

以下是從 [ [值](https://cors3.azurewebsites.net/) 測試] 按鈕到的跨原始來源要求範例 `https://cors1.azurewebsites.net/api/values` 。 `Origin`標頭：

* 提供提出要求的網站網域。
* 是必要的，而且必須與主機不同。

**一般標頭**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**回應標頭**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**要求標頭**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

在 `OPTIONS` 要求中，伺服器會在回應中設定 **回應標頭** `Access-Control-Allow-Origin: {allowed origin}` 標頭。 例如，已部署的 [範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) 按鈕 `OPTIONS` 要求包含下列標頭：

**一般標頭**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**回應標頭**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**要求標頭**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

在先前的 **回應標頭** 中，伺服器會在回應中設定 [存取控制-允許原始](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 的標頭。 `https://cors1.azurewebsites.net`此標頭的值符合 `Origin` 要求中的標頭。

如果 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> 呼叫，則 `Access-Control-Allow-Origin: *` 會傳回萬用字元值。 `AllowAnyOrigin` 允許任何來源。

如果回應不包含 `Access-Control-Allow-Origin` 標頭，則跨原始來源要求會失敗。 具體而言，瀏覽器不允許要求。 即使伺服器傳回成功的回應，瀏覽器也不會將回應提供給用戶端應用程式。

<a name="options"></a>

### <a name="display-options-requests"></a>顯示選項要求

根據預設，Chrome 和 Edge 瀏覽器不會在 F12 工具的 [網路] 索引標籤上顯示選項要求。 若要在這些瀏覽器中顯示選項要求：

* `chrome://flags/#out-of-blink-cors` 或 `edge://flags/#out-of-blink-cors`
* 停用旗標。
* 重新 啟動。

Firefox 預設會顯示選項要求。

## <a name="cors-in-iis"></a>IIS 中的 CORS

部署至 IIS 時，如果伺服器未設定為允許匿名存取，CORS 必須在 Windows 驗證之前執行。 若要支援此案例，必須為應用程式安裝及設定 [IIS CORS 模組](https://www.iis.net/downloads/microsoft/iis-cors-module) 。

<a name="testc"></a>

## <a name="test-cors"></a>測試 CORS

[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)具有測試 CORS 的程式碼。 請參閱[如何下載](xref:index#how-to-download-a-sample)。 範例是已新增頁面的 API 專案 Razor ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` 只能用來測試範例應用程式，類似于 [下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)。

以下 `ValuesController` 提供測試的端點：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) 由 [Rick.DocRouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 套件提供，並顯示路由資訊。

使用下列其中一種方法來測試上述的範例程式碼：

* 使用已部署的範例應用程式 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) 。 不需要下載範例。
* `dotnet run`使用的預設 URL 執行範例 `https://localhost:5001` 。
* 從 Visual Studio 執行範例，並將埠設為44398，以取得的 URL `https://localhost:44398` 。

使用瀏覽器搭配 F12 工具：

* 選取 [ **值** ] 按鈕，並檢查 [ **網路** ] 索引標籤中的標頭。
* 選取 [ **PUT 測試** ] 按鈕。 如需顯示選項要求的指示，請參閱 [顯示選項要求](#options) 。 **PUT 測試** 會建立兩個要求，也就是選項預檢要求和 PUT 要求。
* 選取 **`GetValues2 [DisableCors]`** 按鈕以觸發失敗的 CORS 要求。 如檔中所述，回應會傳回200成功，但不會發出 CORS 要求。 選取 [ **主控台** ] 索引標籤，以查看 CORS 錯誤。 視瀏覽器而定，會顯示類似下列的錯誤：

     `'https://cors1.azurewebsites.net/api/values/GetValues2'`CORS 原則已封鎖從來源提取的存取權 `'https://cors3.azurewebsites.net'` ：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。 如果不透明回應適合您的需求，請將要求的模式設定為 'no-cors' 以在停用 CORS 之下擷取資源。
     
啟用 CORS 的端點可以使用工具（例如 [捲曲](https://curl.haxx.se/)、 [Fiddler](https://www.telerik.com/fiddler)或 [Postman](https://www.getpostman.com/)）進行測試。 使用工具時，標頭所指定的要求來源必須與 `Origin` 接收要求的主機不同。 如果 *要求不是* 以標頭的值為基礎 `Origin` ：

* CORS 中介軟體不需要處理要求。
* 回應中不會傳回 CORS 標頭。

下列命令會使用 `curl` 來發出具有下列資訊的選項要求：

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>使用端點路由和 [HttpOptions] 測試 CORS

使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援 [自動預檢要求](#apf)。 請考慮使用 [端點路由來啟用 CORS](#ecors)的下列程式碼：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

以下 `TodoItems1Controller` 提供測試的端點：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

從已部署[範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)的[測試頁面](https://cors1.azurewebsites.net/test?number=1)測試上述程式碼。

**Delete [EnableCors]** 和 **GET [EnableCors]** 按鈕會成功，因為端點具有 `[EnableCors]` 並回應預檢要求。 其他端點失敗。 **GET** 按鈕失敗，因為 [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)會傳送：

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

以下 `TodoItems2Controller` 提供類似的端點，但包含可回應 OPTIONS 要求的明確程式碼：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

從已部署範例的 [測試頁面](https://cors1.azurewebsites.net/test?number=2) 測試上述程式碼。 在 [ **控制器** ] 下拉式清單中，選取 [ **預檢** ]，然後 **設定 [控制器**]。 端點的所有 CORS 呼叫都會 `TodoItems2Controller` 成功。

## <a name="additional-resources"></a>其他資源

* [跨原始資源分享 (CORS) ](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [開始使用 IIS CORS 模組](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本文說明如何在 ASP.NET Core 應用程式中啟用 CORS。

瀏覽器安全性可防止網頁提出要求，而不是與提供網頁的不同網域。 這項限制稱為 *相同原始來源原則*。 相同來源原則可防止惡意網站從另一個網站讀取敏感性資料。 有時，您可能會想要允許其他網站對您的應用程式進行跨原始來源要求。 如需詳細資訊，請參閱 [MOZILLA CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。

[跨原始資源分享](https://www.w3.org/TR/cors/) (CORS) ：

* 是一種 W3C 標準，可讓伺服器放寬相同的原始原則。
* 不 **是安全性** 功能，CORS 放寬安全性。 API 不會藉由允許 CORS 來更安全。 如需詳細資訊，請參閱 [CORS 的運作方式](#how-cors)。
* 允許伺服器明確允許某些跨原始來源的要求，同時拒絕其他要求。
* 比先前的技術（例如 [JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更有彈性。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="same-origin"></a>相同的來源

如果兩個 Url 具有相同的配置、主機和埠 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，其來源相同。

這兩個 Url 的來源相同：

* `https://example.com/foo.html`
* `https://example.com/bar.html`

這些 Url 的來源與前兩個 Url 不同：

* `https://example.net`：不同的網域
* `https://www.example.com/foo.html`：不同的子域
* `http://example.com/foo.html`：不同的配置
* `https://example.com:9000/foo.html`：不同的埠

比較來源時，Internet Explorer 不會考慮該埠。

## <a name="cors-with-named-policy-and-middleware"></a>具有命名原則和中介軟體的 CORS

CORS 中介軟體會處理跨原始來源的要求。 下列程式碼會針對具有指定來源的整個應用程式啟用 CORS：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

上述程式碼：

* 將原則名稱設定為 " \_ myAllowSpecificOrigins"。 原則名稱是任意的。
* 呼叫 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 擴充方法，以啟用 CORS。
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的呼叫。 Lambda 接受 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 物件。 設定[選項](#cors-policy-options)（例如 `WithOrigins` ）將在本文稍後說明。

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法呼叫會將 CORS 服務新增至應用程式的服務容器：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

如需詳細資訊，請參閱本檔中的 [CORS 原則選項](#cpo) 。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以鏈方法，如下列程式碼所示：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

注意： URL **不** 能包含尾端斜線 (`/`) 。 如果 URL 以終止 `/` ，則比較會 `false` 傳回，而且不會傳回任何標頭。

下列程式碼會透過 CORS 中介軟體將 CORS 原則套用至所有應用程式端點：
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
注意： `UseCors` 必須先呼叫 `UseMvc` 。

請參閱在頁面 [ Razor 、控制器和動作方法中啟用 cors](#ecors) ，以在頁面/控制器/動作層級套用 cors 原則。

請參閱 [測試 CORS](#test) ，以取得測試程式碼類似上述程式碼的指示。

## <a name="enable-cors-with-attributes"></a>使用屬性來啟用 CORS

[ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)屬性提供全域套用 CORS 的替代方案。 `[EnableCors]`屬性會針對選取的端點啟用 CORS，而不是所有端點。

使用 `[EnableCors]` 指定預設原則，並 `[EnableCors("{Policy String}")]` 指定原則。

`[EnableCors]`屬性可套用至：

* Razor 網頁 `PageModel`
* 控制器
* 控制器動作方法

您可以使用屬性，將不同的原則套用至控制器/頁面模型/動作  `[EnableCors]` 。 當 `[EnableCors]` 屬性套用至控制器/頁面模型/動作方法，且在中介軟體中啟用 CORS 時，會套用 **這兩個** 原則。 建議 **不要** 結合原則。 使用 `[EnableCors]` 屬性或中介軟體， **而非兩者**。 使用時 `[EnableCors]` ，請勿定義預設原則。

下列程式碼會將不同的原則套用至每個方法：

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

下列程式碼會建立 CORS 預設原則和名為的原則 `"AnotherPolicy"` ：

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>停用 CORS

[ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)屬性會停用控制器/頁面模型/動作的 CORS。

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 原則選項

本節說明可在 CORS 原則中設定的各種選項：

* [設定允許的來源](#set-the-allowed-origins)
* [設定允許的 HTTP 方法](#set-the-allowed-http-methods)
* [設定允許的要求標頭](#set-the-allowed-request-headers)
* [設定公開的回應標頭](#set-the-exposed-response-headers)
* [跨原始來源要求中的認證](#credentials-in-cross-origin-requests)
* [設定預檢到期時間](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中呼叫 `Startup.ConfigureServices` 。 對於某些選項而言，先閱讀「 [CORS 的運作方式](#how-cors) 」一節可能很有説明。

## <a name="set-the-allowed-origins"></a>設定允許的來源

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允許所有來源中具有任何配置 (或) 的 CORS 要求 `http` `https` 。 `AllowAnyOrigin` 不安全，因為 *任何網站* 都可以對應用程式進行跨原始來源的要求。

> [!NOTE]
> 指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的設定，而且可能會導致跨網站要求偽造。 針對安全的應用程式，如果用戶端必須授權自己存取伺服器資源，請指定確切的來源清單。

`AllowAnyOrigin` 影響預檢要求和 `Access-Control-Allow-Origin` 標頭。 如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：將原則的屬性設定為函式 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> ，以在評估是否允許原始來源時，允許原始來源符合已設定的萬用字元網域。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>設定允許的 HTTP 方法

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 允許任何 HTTP 方法：
* 影響預檢要求和 `Access-Control-Allow-Methods` 標頭。 如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。

### <a name="set-the-allowed-request-headers"></a>設定允許的要求標頭

若要允許在 CORS 要求中傳送特定標頭（稱為「 *作者要求標頭*」），請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 並指定允許的標頭：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

若要允許所有作者要求標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

此設定會影響預檢要求和 `Access-Control-Request-Headers` 標頭。 如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。

CORS 中介軟體一律允許傳送中的四個標頭， `Access-Control-Request-Headers` 不論 c 中設定的值為何。 此標頭清單包括：

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

例如，假設應用程式設定如下：

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS 中介軟體會使用下列要求標頭成功回應預檢要求，因為 `Content-Language` 一律允許：

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>設定公開的回應標頭

根據預設，瀏覽器不會將所有回應標頭公開給應用程式。 如需詳細資訊，請參閱 [W3C 跨原始資源分享 (術語) ：簡單的回應標頭](https://www.w3.org/TR/cors/#simple-response-header)。

預設可用的回應標頭為：

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 規格會呼叫這些標頭 *簡單的回應標頭*。 若要讓應用程式使用其他標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>跨原始來源要求中的認證

認證需要 CORS 要求中的特殊處理。 根據預設，瀏覽器不會傳送具有跨原始來源要求的認證。 認證包括 cookie s 和 HTTP 驗證配置。 若要傳送具有跨原始來源要求的認證，用戶端必須設定 `XMLHttpRequest.withCredentials` 為 `true` 。

`XMLHttpRequest`直接使用：

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

使用 jQuery：

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

使用 [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

伺服器必須允許認證。 若要允許跨原始來源認證，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 回應包含 `Access-Control-Allow-Credentials` 標頭，它會告知瀏覽器伺服器允許跨原始來源要求的認證。

如果瀏覽器傳送認證但回應不包含有效的 `Access-Control-Allow-Credentials` 標頭，則瀏覽器不會公開應用程式的回應，而且跨原始來源要求會失敗。

允許跨原始來源的認證有安全性風險。 另一個網域的網站可以代表使用者將已登入使用者的認證傳送給應用程式，而不需要使用者的知識。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 規格也指出 `"*"` 如果標頭存在，設定來源 (所有來源) 無效 `Access-Control-Allow-Credentials` 。

### <a name="preflight-requests"></a>預檢要求

針對某些 CORS 要求，瀏覽器會先傳送額外的要求，再進行實際要求。 此要求稱為 *預檢要求*。 如果下列條件成立，瀏覽器可以略過預檢要求：

* 要求方法為 GET、HEAD 或 POST。
* 應用程式不會設定 `Accept` 、、 `Accept-Language` `Content-Language` 、 `Content-Type` 或以外的要求標頭 `Last-Event-ID` 。
* `Content-Type`如果設定，標頭會有下列其中一個值：
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

針對用戶端要求所設定之要求標頭的規則，會套用至應用程式透過在物件上呼叫所設定的標頭 `setRequestHeader` `XMLHttpRequest` 。 CORS 規格會呼叫這些標頭的 *作者要求標頭*。 此規則不適用於瀏覽器可以設定的標頭，例如 `User-Agent` 、 `Host` 或 `Content-Length` 。

以下是預檢要求的範例：

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

進入飛行前要求會使用 HTTP OPTIONS 方法。 它包含兩個特殊標頭：

* `Access-Control-Request-Method`：將用於實際要求的 HTTP 方法。
* `Access-Control-Request-Headers`：應用程式在實際要求上設定的要求標頭清單。 如先前所述，這並不包括瀏覽器所設定的標頭，例如 `User-Agent` 。

<!-- I think this needs to be removed, was put here accidently -->

使用適當的原則啟用 CORS 時，ASP.NET Core 通常會自動回應 CORS 預檢要求。 請參閱 [預檢要求的 [HttpOptions] 屬性](#pro)。

CORS 預檢要求可能包含 `Access-Control-Request-Headers` 標頭，這會向伺服器指出與實際要求一起傳送的標頭。

若要允許特定標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

若要允許所有作者要求標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

瀏覽器的設定方式並不完全一致 `Access-Control-Request-Headers` 。 如果您將標頭設定為 `"*"` (或使用) 以外的任何 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 一個，您應該至少包含 `Accept` 、 `Content-Type` 和 `Origin` ，以及您想要支援的任何自訂標頭。

以下是預檢要求的範例回應， (假設伺服器允許要求) ：

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

回應包含的 `Access-Control-Allow-Methods` 標頭會列出允許的方法以及選擇性的 `Access-Control-Allow-Headers` 標頭，以列出允許的標頭。 如果預檢要求成功，則瀏覽器會傳送實際的要求。

如果預檢要求遭到拒絕，應用程式會傳回 *200 OK* 回應，但不會傳送 CORS 標頭。 因此，瀏覽器不會嘗試跨原始來源要求。

### <a name="set-the-preflight-expiration-time"></a>設定預檢到期時間

`Access-Control-Max-Age`標頭會指定可以快取預檢要求回應的時間長度。 若要設定此標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 的運作方式

本節說明在 HTTP 訊息層級的 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 要求中發生的情況。

* CORS **不** 是安全性功能。 CORS 是一種 W3C 標準，可讓伺服器放寬相同的原始原則。
  * 例如，惡意的動作專案可能會針對您的網站使用 [防止跨網站腳本 (XSS) ](xref:security/cross-site-scripting) ，並對其已啟用 CORS 的網站執行跨網站要求以竊取資訊。
* 藉由允許 CORS，您的 API 不會更安全。
  * 用戶端 (瀏覽器) 來強制執行 CORS。 伺服器會執行要求並傳迴響應，這是傳回錯誤並封鎖回應的用戶端。 例如，下列任何一項工具都會顯示伺服器回應：
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * 在網址列中輸入 URL 的網頁瀏覽器。
* 這是伺服器允許瀏覽器執行跨原始 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 或 [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 要求的方法，否則會被禁止。
  * 沒有 CORS 的瀏覽器 () 無法進行跨原始來源的要求。 在 CORS 之前，會使用 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 來規避這種限制。 JSONP 不會使用 XHR，它會使用 `<script>` 標記來接收回應。 允許跨原始來源載入腳本。

[CORS 規格](https://www.w3.org/TR/cors/)引進了數個新的 HTTP 標頭，可啟用跨原始來源要求。 如果瀏覽器支援 CORS，則會自動為跨原始來源要求設定這些標頭。 不需要自訂 JavaScript 程式碼來啟用 CORS。

以下是跨原始來源要求的範例。 `Origin`標頭會提供提出要求的網站網域。 `Origin`標頭是必要的，而且必須與主機不同。

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

如果伺服器允許此要求，則會 `Access-Control-Allow-Origin` 在回應中設定標頭。 此標頭的值 `Origin` 會符合要求中的標頭，或是萬用字元值 `"*"` ，表示允許任何來源：

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

如果回應不包含 `Access-Control-Allow-Origin` 標頭，則跨原始來源要求會失敗。 具體而言，瀏覽器不允許要求。 即使伺服器傳回成功的回應，瀏覽器也不會將回應提供給用戶端應用程式。

<a name="test"></a>

## <a name="test-cors"></a>測試 CORS

若要測試 CORS：

1. [建立 API 專案](xref:tutorials/first-web-api)。 或者，您也可以 [下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors)。
1. 請使用本檔中的其中一種方法來啟用 CORS。 例如：

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` 只能用來測試範例應用程式，類似于 [下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)。

1. 建立 web 應用程式專案 (Razor 頁面或 MVC) 。 此範例會使用 Razor 頁面。 您可以在與 API 專案相同的解決方案中建立 web 應用程式。
1. 將下列反白顯示的程式碼加入至 *索引的 cshtml* 檔案：

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 在上述程式碼中，將取代為已 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 部署應用程式的 URL。
1. 部署 API 專案。 例如， [部署至 Azure](xref:host-and-deploy/azure-apps/index)。
1. Razor從桌面上執行頁面或 MVC 應用程式，然後按一下 [**測試**] 按鈕。 使用 F12 工具來檢查錯誤訊息。
1. 從移除 localhost 來源 `WithOrigins` ，並部署應用程式。 或者，使用不同的埠執行用戶端應用程式。 例如，從 Visual Studio 執行。
1. 使用用戶端應用程式進行測試。 CORS 失敗會傳回錯誤，但無法使用 JavaScript 的錯誤訊息。 使用 F12 工具中的 [主控台] 索引標籤，以查看錯誤。 視瀏覽器而定，您會在 F12 工具主控台中收到 (的錯誤) 如下所示：

   * 使用 Microsoft Edge：

     **SEC7120： [CORS] 在 `https://localhost:44375` `https://localhost:44375` 的跨來源資源的存取控制-允許來源回應標頭中找不到來源 `https://webapi.azurewebsites.net/api/values/1`**

   * 使用 Chrome：

     **`https://webapi.azurewebsites.net/api/values/1`CORS 原則已封鎖從來源對 XMLHttpRequest 的存取 `https://localhost:44375` ：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。**
     
啟用 CORS 的端點可以使用工具（例如 [Fiddler](https://www.telerik.com/fiddler) 或 [Postman](https://www.getpostman.com/)）進行測試。 使用工具時，標頭所指定的要求來源必須與 `Origin` 接收要求的主機不同。 如果 *要求不是* 以標頭的值為基礎 `Origin` ：

* CORS 中介軟體不需要處理要求。
* 回應中不會傳回 CORS 標頭。

## <a name="cors-in-iis"></a>IIS 中的 CORS

部署至 IIS 時，如果伺服器未設定為允許匿名存取，CORS 必須在 Windows 驗證之前執行。 若要支援此案例，必須為應用程式安裝及設定 [IIS CORS 模組](https://www.iis.net/downloads/microsoft/iis-cors-module) 。

## <a name="additional-resources"></a>其他資源

* [跨原始資源分享 (CORS) ](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [開始使用 IIS CORS 模組](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
