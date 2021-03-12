---
title: ASP.NET Core 中的回應快取中介軟體
author: rick-anderson
description: 了解如何設定和使用 ASP.NET Core 中的回應快取中介軟體。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/middleware
ms.openlocfilehash: 0f1f5dfcb9595270a9659a02141f7d1eba5c44ef
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587693"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>ASP.NET Core 中的回應快取中介軟體

作者：[John Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

本文說明如何設定 ASP.NET Core 應用程式中的回應快取中介軟體。 中介軟體會決定何時可快取回應、儲存回應，以及提供快取的回應。 如需 HTTP 快取和屬性的簡介 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，請參閱 [回應](xref:performance/caching/response)快取。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="configuration"></a>組態

回應快取中介軟體可透過共用架構隱含地提供給 ASP.NET Core 應用程式使用。

在中 `Startup.ConfigureServices` ，將回應快取中介軟體新增至服務集合：

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

將應用程式設定為使用具有擴充方法的中介軟體 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> ，這會將中介軟體新增至中的要求處理管線 `Startup.Configure` ：

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>使用[CORS 中介軟體](xref:security/cors)之前，必須先呼叫。

範例應用程式會新增標頭，以控制後續要求的快取：

* 快取[控制](https://tools.ietf.org/html/rfc7234#section-5.2)：快取最多10秒的可快取回應。
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：只有在後續要求的接受編碼標頭符合原始要求的 [接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4) 標頭時，才會將中介軟體設定為提供快取回應。

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

上述的標頭不會寫入至回應，並會在控制器、動作或頁面上覆寫 Razor ：

* 具有 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 屬性。 即使未設定屬性也適用。 例如，省略 [VaryByHeader](./response.md#vary) 屬性將會從回應中移除對應的標頭。

回應快取中介軟體只會快取導致 200 (確定) 狀態碼的伺服器回應。 中介軟體會忽略任何其他回應（包括 [錯誤頁面](xref:fundamentals/error-handling)）。

> [!WARNING]
> 包含已驗證之用戶端內容的回應必須標示為無法快取，以防止中介軟體儲存和提供這些回應。 如需有關中介軟體如何判斷回應是否可快取的詳細資訊，請參閱快取的 [條件](#conditions-for-caching) 。

## <a name="options"></a>選項

回應快取選項如下表所示。

| 選項 | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | 回應主體的最大可快取大小（以位元組為單位）。 預設值為 `64 * 1024 * 1024` (64 MB) 。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | 回應快取中介軟體的大小限制（以位元組為單位）。 預設值為 `100 * 1024 * 1024` (100 MB) 。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | 判斷是否要在區分大小寫的路徑上快取回應。 預設值是 `false`。 |

下列範例會將中介軟體設定為：

* 主體大小小於或等於1024個位元組的快取回應。
* 依區分大小寫的路徑儲存回應。 例如， `/page1` 和 `/Page1` 會分開儲存。

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

使用 MVC/web API 控制器或 Razor 頁面頁面模型時， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 屬性會指定為回應快取設定適當標頭所需的參數。 `[ResponseCache]`嚴格要求中介軟體的屬性唯一參數是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，其不會對應到實際的 HTTP 標頭。 如需詳細資訊，請參閱<xref:performance/caching/response#responsecache-attribute>。

不使用屬性時 `[ResponseCache]` ，回應快取可以不同 `VaryByQueryKeys` 。 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接從[HttpCoNtext. 功能](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

使用等於 in 的單一值 `*` `VaryByQueryKeys` ，會依所有要求查詢參數而改變快取。

## <a name="http-headers-used-by-response-caching-middleware"></a>回應快取中介軟體所使用的 HTTP 標頭

下表提供會影響回應快取的 HTTP 標頭的相關資訊。

| 標頭 | 詳細資料 |
| ------ | ------- |
| `Authorization` | 如果標頭存在，則不會快取回應。 |
| `Cache-Control` | 中介軟體只會考慮以 cache 指示詞標記的快取回應 `public` 。 使用下列參數控制快取：<ul><li>最大壽命</li><li>最大過時&#8224;</li><li>最小值-最新</li><li>must-revalidate</li><li>no-cache</li><li>無存放區</li><li>僅限-快取</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-重新驗證&#8225;</li></ul>&#8224;如果未指定任何限制 `max-stale` ，則中介軟體不會採取任何動作。<br>&#8225;`proxy-revalidate` 的效果與相同 `must-revalidate` 。<br><br>如需詳細資訊，請參閱 [RFC 7231：要求 Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2.1)指示詞。 |
| `Pragma` | `Pragma: no-cache`要求中的標頭會產生與相同的效果 `Cache-Control: no-cache` 。 標頭中的相關指示詞會覆寫此標頭 `Cache-Control` （如果有的話）。 考慮使用 HTTP/1.0 與舊版相容。 |
| `Set-Cookie` | 如果標頭存在，則不會快取回應。 要求處理管線中設定一或多個的中介軟體， cookie 可防止回應快取中介軟體快取回應 (例如，以[ cookie TempData 提供者為基礎](xref:fundamentals/app-state#tempdata)的) 。  |
| `Vary` | `Vary`標頭是用來根據另一個標頭來改變快取的回應。 例如，藉由包含標頭來編碼快取回應 `Vary: Accept-Encoding` ，此標頭會快取標頭和個別要求的回應 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。 永遠不會儲存標頭值為的回應 `*` 。 |
| `Expires` | 除非由其他標頭覆寫，否則不會儲存或取出此標頭所過時的回應 `Cache-Control` 。 |
| `If-None-Match` | 如果值不是 `*` ，且回應的值 `ETag` 不符合任何提供的值，則會從快取提供完整回應。 否則，將會提供 304 (未修改) 回應。 |
| `If-Modified-Since` | 如果 `If-None-Match` 標頭不存在，則會在快取的回應日期比提供的值更新時，從快取中提供完整回應。 否則，就會提供 *304-未修改* 的回應。 |
| `Date` | 從快取提供時， `Date` 如果未在原始回應中提供標頭，則會設定中介軟體的標頭。 |
| `Content-Length` | 從快取提供時， `Content-Length` 如果未在原始回應中提供標頭，則會設定中介軟體的標頭。 |
| `Age` | `Age`原始回應中傳送的標頭會被忽略。 提供快取回應時，中介軟體會計算新值。 |

## <a name="caching-respects-request-cache-control-directives"></a>快取遵循要求 Cache-Control 指示詞

中介軟體會遵循 [HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2)快取規格的規則。 這些規則需要快取來接受 `Cache-Control` 用戶端所傳送的有效標頭。 在此規格下，用戶端可以使用 `no-cache` 標頭值提出要求，並強制服務器針對每個要求產生新的回應。 目前，使用中介軟體時，不會有開發人員控制這種快取行為，因為中介軟體會遵守官方快取規格。

若要更充分掌控快取行為，請探索 ASP.NET Core 的其他快取功能。 請參閱下列主題：

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>疑難排解

如果快取行為不符合預期，請確認回應可以快取，而且能夠從快取中提供服務。 檢查要求的連入標頭和回應的連出標頭。 啟用 [記錄](xref:fundamentals/logging/index) 以協助進行調試。

在測試和疑難排解快取行為時，瀏覽器可能會設定要求標頭，以不需要的方式影響快取。 例如，瀏覽器可能會在重新整理 `Cache-Control` 頁面時，將標頭設定為 `no-cache` 或 `max-age=0` 。 下列工具可以明確地設定要求標頭，並建議用於測試快取：

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>快取的條件

* 要求必須產生 200 (確定) 狀態碼的伺服器回應。
* 要求方法必須是 GET 或 HEAD。
* 在中 `Startup.Configure` ，回應快取中介軟體必須放在需要快取的中介軟體之前。 如需詳細資訊，請參閱<xref:fundamentals/middleware/index>。
* `Authorization`標頭不得存在。
* `Cache-Control` 標頭參數必須是有效的，而且回應必須標記 `public` 且未標記 `private` 。
* `Pragma: no-cache`如果標頭不存在，標頭不能是存在的 `Cache-Control` ，因為標頭會 `Cache-Control` 覆寫 `Pragma` 標頭（如果有的話）。
* `Set-Cookie`標頭不得存在。
* `Vary` 標頭參數必須有效且不等於 `*` 。
* `Content-Length`標頭值 (如果設定的) 必須符合回應主體的大小。
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>未使用。
* 回應不得如 `Expires` 標頭和和快取指示詞所指定的過時 `max-age` `s-maxage` 。
* 回應緩衝處理必須成功。 回應的大小必須小於已設定或預設值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。 回應的主體大小必須小於已設定或預設值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。
* 回應必須根據 [RFC 7234](https://tools.ietf.org/html/rfc7234) 規格來快取。 例如，指示詞 `no-store` 不能存在於要求或回應標頭欄位中。 請參閱第 *3 節：在* [RFC 7234](https://tools.ietf.org/html/rfc7234) 的快取中儲存回應以取得詳細資料。

> [!NOTE]
> 用來產生安全權杖以防止跨網站偽造要求 (CSRF) 攻擊的 Antiforgery 系統會將 `Cache-Control` 和 `Pragma` 標頭設定為， `no-cache` 如此就不會快取回應。 如需有關如何停用 HTML 表單元素之 antiforgery token 的詳細資訊，請參閱 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本文說明如何設定 ASP.NET Core 應用程式中的回應快取中介軟體。 中介軟體會決定何時可快取回應、儲存回應，以及提供快取的回應。 如需 HTTP 快取和屬性的簡介 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，請參閱 [回應](xref:performance/caching/response)快取。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="configuration"></a>組態

使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ，或將套件參考新增至 [AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 套件。

在中 `Startup.ConfigureServices` ，將回應快取中介軟體新增至服務集合：

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

將應用程式設定為使用具有擴充方法的中介軟體 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> ，這會將中介軟體新增至中的要求處理管線 `Startup.Configure` ：

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

範例應用程式會新增標頭，以控制後續要求的快取：

* 快取[控制](https://tools.ietf.org/html/rfc7234#section-5.2)：快取最多10秒的可快取回應。
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：只有在後續要求的接受編碼標頭符合原始要求的 [接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4) 標頭時，才會將中介軟體設定為提供快取回應。

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

上述的標頭不會寫入至回應，並會在控制器、動作或頁面上覆寫 Razor ：

* 具有 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 屬性。 即使未設定屬性也適用。 例如，省略 [VaryByHeader](./response.md#vary) 屬性將會從回應中移除對應的標頭。

回應快取中介軟體只會快取導致 200 (確定) 狀態碼的伺服器回應。 中介軟體會忽略任何其他回應（包括 [錯誤頁面](xref:fundamentals/error-handling)）。

> [!WARNING]
> 包含已驗證之用戶端內容的回應必須標示為無法快取，以防止中介軟體儲存和提供這些回應。 如需有關中介軟體如何判斷回應是否可快取的詳細資訊，請參閱快取的 [條件](#conditions-for-caching) 。

## <a name="options"></a>選項

回應快取選項如下表所示。

| 選項 | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | 回應主體的最大可快取大小（以位元組為單位）。 預設值為 `64 * 1024 * 1024` (64 MB) 。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | 回應快取中介軟體的大小限制（以位元組為單位）。 預設值為 `100 * 1024 * 1024` (100 MB) 。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | 判斷是否要在區分大小寫的路徑上快取回應。 預設值是 `false`。 |

下列範例會將中介軟體設定為：

* 主體大小小於或等於1024個位元組的快取回應。
* 依區分大小寫的路徑儲存回應。 例如， `/page1` 和 `/Page1` 會分開儲存。

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

使用 MVC/web API 控制器或 Razor 頁面頁面模型時， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 屬性會指定為回應快取設定適當標頭所需的參數。 `[ResponseCache]`嚴格要求中介軟體的屬性唯一參數是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，其不會對應到實際的 HTTP 標頭。 如需詳細資訊，請參閱<xref:performance/caching/response#responsecache-attribute>。

不使用屬性時 `[ResponseCache]` ，回應快取可以不同 `VaryByQueryKeys` 。 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接從[HttpCoNtext. 功能](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

使用等於 in 的單一值 `*` `VaryByQueryKeys` ，會依所有要求查詢參數而改變快取。

## <a name="http-headers-used-by-response-caching-middleware"></a>回應快取中介軟體所使用的 HTTP 標頭

下表提供會影響回應快取的 HTTP 標頭的相關資訊。

| 標頭 | 詳細資料 |
| ------ | ------- |
| `Authorization` | 如果標頭存在，則不會快取回應。 |
| `Cache-Control` | 中介軟體只會考慮以 cache 指示詞標記的快取回應 `public` 。 使用下列參數控制快取：<ul><li>最大壽命</li><li>最大過時&#8224;</li><li>最小值-最新</li><li>must-revalidate</li><li>no-cache</li><li>無存放區</li><li>僅限-快取</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-重新驗證&#8225;</li></ul>&#8224;如果未指定任何限制 `max-stale` ，則中介軟體不會採取任何動作。<br>&#8225;`proxy-revalidate` 的效果與相同 `must-revalidate` 。<br><br>如需詳細資訊，請參閱 [RFC 7231：要求 Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2.1)指示詞。 |
| `Pragma` | `Pragma: no-cache`要求中的標頭會產生與相同的效果 `Cache-Control: no-cache` 。 標頭中的相關指示詞會覆寫此標頭 `Cache-Control` （如果有的話）。 考慮使用 HTTP/1.0 與舊版相容。 |
| `Set-Cookie` | 如果標頭存在，則不會快取回應。 要求處理管線中設定一或多個的中介軟體， cookie 可防止回應快取中介軟體快取回應 (例如，以[ cookie TempData 提供者為基礎](xref:fundamentals/app-state#tempdata)的) 。  |
| `Vary` | `Vary`標頭是用來根據另一個標頭來改變快取的回應。 例如，藉由包含標頭來編碼快取回應 `Vary: Accept-Encoding` ，此標頭會快取標頭和個別要求的回應 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。 永遠不會儲存標頭值為的回應 `*` 。 |
| `Expires` | 除非由其他標頭覆寫，否則不會儲存或取出此標頭所過時的回應 `Cache-Control` 。 |
| `If-None-Match` | 如果值不是 `*` ，且回應的值 `ETag` 不符合任何提供的值，則會從快取提供完整回應。 否則，將會提供 304 (未修改) 回應。 |
| `If-Modified-Since` | 如果 `If-None-Match` 標頭不存在，則會在快取的回應日期比提供的值更新時，從快取中提供完整回應。 否則，就會提供 *304-未修改* 的回應。 |
| `Date` | 從快取提供時， `Date` 如果未在原始回應中提供標頭，則會設定中介軟體的標頭。 |
| `Content-Length` | 從快取提供時， `Content-Length` 如果未在原始回應中提供標頭，則會設定中介軟體的標頭。 |
| `Age` | `Age`原始回應中傳送的標頭會被忽略。 提供快取回應時，中介軟體會計算新值。 |

## <a name="caching-respects-request-cache-control-directives"></a>快取遵循要求 Cache-Control 指示詞

中介軟體會遵循 [HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2)快取規格的規則。 這些規則需要快取來接受 `Cache-Control` 用戶端所傳送的有效標頭。 在此規格下，用戶端可以使用 `no-cache` 標頭值提出要求，並強制服務器針對每個要求產生新的回應。 目前，使用中介軟體時，不會有開發人員控制這種快取行為，因為中介軟體會遵守官方快取規格。

若要更充分掌控快取行為，請探索 ASP.NET Core 的其他快取功能。 請參閱下列主題：

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>疑難排解

如果快取行為不符合預期，請確認回應可以快取，而且能夠從快取中提供服務。 檢查要求的連入標頭和回應的連出標頭。 啟用 [記錄](xref:fundamentals/logging/index) 以協助進行調試。

在測試和疑難排解快取行為時，瀏覽器可能會設定要求標頭，以不需要的方式影響快取。 例如，瀏覽器可能會在重新整理 `Cache-Control` 頁面時，將標頭設定為 `no-cache` 或 `max-age=0` 。 下列工具可以明確地設定要求標頭，並建議用於測試快取：

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>快取的條件

* 要求必須產生 200 (確定) 狀態碼的伺服器回應。
* 要求方法必須是 GET 或 HEAD。
* 在中 `Startup.Configure` ，回應快取中介軟體必須放在需要快取的中介軟體之前。 如需詳細資訊，請參閱<xref:fundamentals/middleware/index>。
* `Authorization`標頭不得存在。
* `Cache-Control` 標頭參數必須是有效的，而且回應必須標記 `public` 且未標記 `private` 。
* `Pragma: no-cache`如果標頭不存在，標頭不能是存在的 `Cache-Control` ，因為標頭會 `Cache-Control` 覆寫 `Pragma` 標頭（如果有的話）。
* `Set-Cookie`標頭不得存在。
* `Vary` 標頭參數必須有效且不等於 `*` 。
* `Content-Length`標頭值 (如果設定的) 必須符合回應主體的大小。
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>未使用。
* 回應不得如 `Expires` 標頭和和快取指示詞所指定的過時 `max-age` `s-maxage` 。
* 回應緩衝處理必須成功。 回應的大小必須小於已設定或預設值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。 回應的主體大小必須小於已設定或預設值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。
* 回應必須根據 [RFC 7234](https://tools.ietf.org/html/rfc7234) 規格來快取。 例如，指示詞 `no-store` 不能存在於要求或回應標頭欄位中。 請參閱第 *3 節：在* [RFC 7234](https://tools.ietf.org/html/rfc7234) 的快取中儲存回應以取得詳細資料。

> [!NOTE]
> 用來產生安全權杖以防止跨網站偽造要求 (CSRF) 攻擊的 Antiforgery 系統會將 `Cache-Control` 和 `Pragma` 標頭設定為， `no-cache` 如此就不會快取回應。 如需有關如何停用 HTML 表單元素之 antiforgery token 的詳細資訊，請參閱 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end