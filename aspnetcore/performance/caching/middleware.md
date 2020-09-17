---
title: ASP.NET Core 中的回應快取中介軟體
author: rick-anderson
description: 了解如何設定和使用 ASP.NET Core 中的回應快取中介軟體。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/middleware
ms.openlocfilehash: 7fe9629e1c60a6156c69e546736049653a4229b7
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722640"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="39a0b-103">ASP.NET Core 中的回應快取中介軟體</span><span class="sxs-lookup"><span data-stu-id="39a0b-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="39a0b-104">作者：[John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="39a0b-104">By [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="39a0b-105">本文說明如何在 ASP.NET Core 應用程式中設定回應快取中介軟體。</span><span class="sxs-lookup"><span data-stu-id="39a0b-105">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="39a0b-106">中介軟體會決定何時可快取回應、儲存回應，以及提供快取的回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-106">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="39a0b-107">如需 HTTP 快取和屬性的簡介 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，請參閱 [回應](xref:performance/caching/response)快取。</span><span class="sxs-lookup"><span data-stu-id="39a0b-107">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="39a0b-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="39a0b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="39a0b-109">設定</span><span class="sxs-lookup"><span data-stu-id="39a0b-109">Configuration</span></span>

<span data-ttu-id="39a0b-110">回應快取中介軟體可透過共用架構隱含地提供 ASP.NET Core 的應用程式使用。</span><span class="sxs-lookup"><span data-stu-id="39a0b-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="39a0b-111">在中 `Startup.ConfigureServices` ，將回應快取中介軟體新增至服務集合：</span><span class="sxs-lookup"><span data-stu-id="39a0b-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="39a0b-112">將應用程式設定為使用具有擴充方法的中介軟體 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> ，這會將中介軟體新增至中的要求處理管線 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="39a0b-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <span data-ttu-id="39a0b-113"><xref:Owin.CorsExtensions.UseCors%2A><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>使用[CORS 中介軟體](xref:security/cors)之前，必須先呼叫。</span><span class="sxs-lookup"><span data-stu-id="39a0b-113"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using [CORS middleware](xref:security/cors).</span></span>

<span data-ttu-id="39a0b-114">範例應用程式會新增標頭，以控制後續要求的快取：</span><span class="sxs-lookup"><span data-stu-id="39a0b-114">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="39a0b-115">快取[控制](https://tools.ietf.org/html/rfc7234#section-5.2)：快取最多10秒的可快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-115">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="39a0b-116">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：只有在後續要求的接受編碼標頭符合原始要求的 [接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4) 標頭時，才會將中介軟體設定為提供快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-116">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="39a0b-117">上述的標頭不會寫入至回應，並會在控制器、動作或頁面上覆寫 Razor ：</span><span class="sxs-lookup"><span data-stu-id="39a0b-117">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="39a0b-118">具有 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="39a0b-118">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="39a0b-119">即使未設定屬性也適用。</span><span class="sxs-lookup"><span data-stu-id="39a0b-119">This applies even if a property isn't set.</span></span> <span data-ttu-id="39a0b-120">例如，省略 [VaryByHeader](./response.md#vary) 屬性將會從回應中移除對應的標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-120">For example, omitting the [VaryByHeader](./response.md#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="39a0b-121">回應快取中介軟體只會快取導致 200 (確定) 狀態碼的伺服器回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-121">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="39a0b-122">中介軟體會忽略任何其他回應（包括 [錯誤頁面](xref:fundamentals/error-handling)）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-122">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="39a0b-123">包含已驗證之用戶端內容的回應必須標示為無法快取，以防止中介軟體儲存和提供這些回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-123">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="39a0b-124">如需有關中介軟體如何判斷回應是否可快取的詳細資訊，請參閱快取的 [條件](#conditions-for-caching) 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-124">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="39a0b-125">選項</span><span class="sxs-lookup"><span data-stu-id="39a0b-125">Options</span></span>

<span data-ttu-id="39a0b-126">回應快取選項如下表所示。</span><span class="sxs-lookup"><span data-stu-id="39a0b-126">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="39a0b-127">選項</span><span class="sxs-lookup"><span data-stu-id="39a0b-127">Option</span></span> | <span data-ttu-id="39a0b-128">描述</span><span class="sxs-lookup"><span data-stu-id="39a0b-128">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="39a0b-129">回應主體的最大可快取大小（以位元組為單位）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-129">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="39a0b-130">預設值為 `64 * 1024 * 1024` (64 MB) 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-130">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="39a0b-131">回應快取中介軟體的大小限制（以位元組為單位）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-131">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="39a0b-132">預設值為 `100 * 1024 * 1024` (100 MB) 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-132">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="39a0b-133">判斷是否要在區分大小寫的路徑上快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-133">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="39a0b-134">預設值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="39a0b-134">The default value is `false`.</span></span> |

<span data-ttu-id="39a0b-135">下列範例會將中介軟體設定為：</span><span class="sxs-lookup"><span data-stu-id="39a0b-135">The following example configures the middleware to:</span></span>

* <span data-ttu-id="39a0b-136">主體大小小於或等於1024個位元組的快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-136">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="39a0b-137">依區分大小寫的路徑儲存回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-137">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="39a0b-138">例如， `/page1` 和 `/Page1` 會分開儲存。</span><span class="sxs-lookup"><span data-stu-id="39a0b-138">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="39a0b-139">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="39a0b-139">VaryByQueryKeys</span></span>

<span data-ttu-id="39a0b-140">使用 MVC/web API 控制器或 Razor 頁面頁面模型時， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 屬性會指定為回應快取設定適當標頭所需的參數。</span><span class="sxs-lookup"><span data-stu-id="39a0b-140">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="39a0b-141">`[ResponseCache]`嚴格要求中介軟體的屬性唯一參數是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，其不會對應到實際的 HTTP 標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-141">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="39a0b-142">如需詳細資訊，請參閱<xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="39a0b-142">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="39a0b-143">不使用屬性時 `[ResponseCache]` ，回應快取可以不同 `VaryByQueryKeys` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-143">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="39a0b-144"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接從[HttpCoNtext. 功能](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：</span><span class="sxs-lookup"><span data-stu-id="39a0b-144">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="39a0b-145">使用等於 in 的單一值 `*` `VaryByQueryKeys` ，會依所有要求查詢參數而改變快取。</span><span class="sxs-lookup"><span data-stu-id="39a0b-145">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="39a0b-146">回應快取中介軟體所使用的 HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="39a0b-146">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="39a0b-147">下表提供會影響回應快取的 HTTP 標頭的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="39a0b-147">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="39a0b-148">標頭</span><span class="sxs-lookup"><span data-stu-id="39a0b-148">Header</span></span> | <span data-ttu-id="39a0b-149">詳細資料</span><span class="sxs-lookup"><span data-stu-id="39a0b-149">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="39a0b-150">如果標頭存在，則不會快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-150">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="39a0b-151">中介軟體只會考慮以 cache 指示詞標記的快取回應 `public` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-151">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="39a0b-152">使用下列參數控制快取：</span><span class="sxs-lookup"><span data-stu-id="39a0b-152">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="39a0b-153">最大壽命</span><span class="sxs-lookup"><span data-stu-id="39a0b-153">max-age</span></span></li><li><span data-ttu-id="39a0b-154">最大過時&#8224;</span><span class="sxs-lookup"><span data-stu-id="39a0b-154">max-stale&#8224;</span></span></li><li><span data-ttu-id="39a0b-155">最小值-最新</span><span class="sxs-lookup"><span data-stu-id="39a0b-155">min-fresh</span></span></li><li><span data-ttu-id="39a0b-156">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="39a0b-156">must-revalidate</span></span></li><li><span data-ttu-id="39a0b-157">no-cache</span><span class="sxs-lookup"><span data-stu-id="39a0b-157">no-cache</span></span></li><li><span data-ttu-id="39a0b-158">無存放區</span><span class="sxs-lookup"><span data-stu-id="39a0b-158">no-store</span></span></li><li><span data-ttu-id="39a0b-159">僅限-快取</span><span class="sxs-lookup"><span data-stu-id="39a0b-159">only-if-cached</span></span></li><li><span data-ttu-id="39a0b-160">private</span><span class="sxs-lookup"><span data-stu-id="39a0b-160">private</span></span></li><li><span data-ttu-id="39a0b-161">公開</span><span class="sxs-lookup"><span data-stu-id="39a0b-161">public</span></span></li><li><span data-ttu-id="39a0b-162">s-maxage</span><span class="sxs-lookup"><span data-stu-id="39a0b-162">s-maxage</span></span></li><li><span data-ttu-id="39a0b-163">proxy-重新驗證&#8225;</span><span class="sxs-lookup"><span data-stu-id="39a0b-163">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="39a0b-164">&#8224;如果未指定任何限制 `max-stale` ，則中介軟體不會採取任何動作。</span><span class="sxs-lookup"><span data-stu-id="39a0b-164">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="39a0b-165">&#8225;`proxy-revalidate` 的效果與相同 `must-revalidate` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-165">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="39a0b-166">如需詳細資訊，請參閱 [RFC 7231：要求](https://tools.ietf.org/html/rfc7234#section-5.2.1)快取控制指示詞。</span><span class="sxs-lookup"><span data-stu-id="39a0b-166">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="39a0b-167">`Pragma: no-cache`要求中的標頭會產生與相同的效果 `Cache-Control: no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-167">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="39a0b-168">標頭中的相關指示詞會覆寫此標頭 `Cache-Control` （如果有的話）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-168">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="39a0b-169">考慮使用 HTTP/1.0 與舊版相容。</span><span class="sxs-lookup"><span data-stu-id="39a0b-169">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="39a0b-170">如果標頭存在，則不會快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-170">The response isn't cached if the header exists.</span></span> <span data-ttu-id="39a0b-171">要求處理管線中設定一或多個的中介軟體， cookie 可防止回應快取中介軟體快取回應 (例如，以[ cookie TempData 提供者為基礎](xref:fundamentals/app-state#tempdata)的) 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-171">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="39a0b-172">`Vary`標頭是用來根據另一個標頭來改變快取的回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-172">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="39a0b-173">例如，藉由包含標頭來編碼快取回應 `Vary: Accept-Encoding` ，此標頭會快取標頭和個別要求的回應 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-173">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="39a0b-174">永遠不會儲存標頭值為的回應 `*` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-174">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="39a0b-175">除非由其他標頭覆寫，否則不會儲存或取出此標頭所過時的回應 `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-175">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="39a0b-176">如果值不是 `*` ，且回應的值 `ETag` 不符合任何提供的值，則會從快取提供完整回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-176">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="39a0b-177">否則，將會提供 304 (未修改) 回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-177">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="39a0b-178">如果 `If-None-Match` 標頭不存在，則會在快取的回應日期比提供的值更新時，從快取中提供完整回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-178">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="39a0b-179">否則，就會提供 *304-未修改* 的回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-179">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="39a0b-180">從快取提供時， `Date` 如果未在原始回應中提供標頭，則會設定中介軟體的標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-180">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="39a0b-181">從快取提供時， `Content-Length` 如果未在原始回應中提供標頭，則會設定中介軟體的標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-181">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="39a0b-182">`Age`原始回應中傳送的標頭會被忽略。</span><span class="sxs-lookup"><span data-stu-id="39a0b-182">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="39a0b-183">提供快取回應時，中介軟體會計算新值。</span><span class="sxs-lookup"><span data-stu-id="39a0b-183">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="39a0b-184">快取遵循要求快取控制指示詞</span><span class="sxs-lookup"><span data-stu-id="39a0b-184">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="39a0b-185">中介軟體會遵循 [HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2)快取規格的規則。</span><span class="sxs-lookup"><span data-stu-id="39a0b-185">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="39a0b-186">這些規則需要快取來接受 `Cache-Control` 用戶端所傳送的有效標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-186">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="39a0b-187">在此規格下，用戶端可以使用 `no-cache` 標頭值提出要求，並強制服務器針對每個要求產生新的回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-187">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="39a0b-188">目前，使用中介軟體時，不會有開發人員控制這種快取行為，因為中介軟體會遵守官方快取規格。</span><span class="sxs-lookup"><span data-stu-id="39a0b-188">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="39a0b-189">若要更充分掌控快取行為，請探索 ASP.NET Core 的其他快取功能。</span><span class="sxs-lookup"><span data-stu-id="39a0b-189">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="39a0b-190">請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="39a0b-190">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="39a0b-191">疑難排解</span><span class="sxs-lookup"><span data-stu-id="39a0b-191">Troubleshooting</span></span>

<span data-ttu-id="39a0b-192">如果快取行為不符合預期，請確認回應可以快取，而且能夠從快取中提供服務。</span><span class="sxs-lookup"><span data-stu-id="39a0b-192">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="39a0b-193">檢查要求的連入標頭和回應的連出標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-193">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="39a0b-194">啟用 [記錄](xref:fundamentals/logging/index) 以協助進行調試。</span><span class="sxs-lookup"><span data-stu-id="39a0b-194">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="39a0b-195">在測試和疑難排解快取行為時，瀏覽器可能會設定要求標頭，以不需要的方式影響快取。</span><span class="sxs-lookup"><span data-stu-id="39a0b-195">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="39a0b-196">例如，瀏覽器可能會在重新整理 `Cache-Control` 頁面時，將標頭設定為 `no-cache` 或 `max-age=0` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-196">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="39a0b-197">下列工具可以明確地設定要求標頭，並建議用於測試快取：</span><span class="sxs-lookup"><span data-stu-id="39a0b-197">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="39a0b-198">Fiddler</span><span class="sxs-lookup"><span data-stu-id="39a0b-198">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="39a0b-199">Postman</span><span class="sxs-lookup"><span data-stu-id="39a0b-199">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="39a0b-200">快取的條件</span><span class="sxs-lookup"><span data-stu-id="39a0b-200">Conditions for caching</span></span>

* <span data-ttu-id="39a0b-201">要求必須產生 200 (確定) 狀態碼的伺服器回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-201">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="39a0b-202">要求方法必須是 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="39a0b-202">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="39a0b-203">在中 `Startup.Configure` ，回應快取中介軟體必須放在需要快取的中介軟體之前。</span><span class="sxs-lookup"><span data-stu-id="39a0b-203">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="39a0b-204">如需詳細資訊，請參閱<xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="39a0b-204">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="39a0b-205">`Authorization`標頭不得存在。</span><span class="sxs-lookup"><span data-stu-id="39a0b-205">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="39a0b-206">`Cache-Control` 標頭參數必須是有效的，而且回應必須標記 `public` 且未標記 `private` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-206">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="39a0b-207">`Pragma: no-cache`如果標頭不存在，標頭不能是存在的 `Cache-Control` ，因為標頭會 `Cache-Control` 覆寫 `Pragma` 標頭（如果有的話）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-207">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="39a0b-208">`Set-Cookie`標頭不得存在。</span><span class="sxs-lookup"><span data-stu-id="39a0b-208">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="39a0b-209">`Vary` 標頭參數必須有效且不等於 `*` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-209">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="39a0b-210">`Content-Length`標頭值 (如果設定的) 必須符合回應主體的大小。</span><span class="sxs-lookup"><span data-stu-id="39a0b-210">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="39a0b-211"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>未使用。</span><span class="sxs-lookup"><span data-stu-id="39a0b-211">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="39a0b-212">回應不得如 `Expires` 標頭和和快取指示詞所指定的過時 `max-age` `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-212">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="39a0b-213">回應緩衝處理必須成功。</span><span class="sxs-lookup"><span data-stu-id="39a0b-213">Response buffering must be successful.</span></span> <span data-ttu-id="39a0b-214">回應的大小必須小於已設定或預設值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-214">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="39a0b-215">回應的主體大小必須小於已設定或預設值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-215">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="39a0b-216">回應必須根據 [RFC 7234](https://tools.ietf.org/html/rfc7234) 規格來快取。</span><span class="sxs-lookup"><span data-stu-id="39a0b-216">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="39a0b-217">例如，指示詞 `no-store` 不能存在於要求或回應標頭欄位中。</span><span class="sxs-lookup"><span data-stu-id="39a0b-217">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="39a0b-218">請參閱第 *3 節：在* [RFC 7234](https://tools.ietf.org/html/rfc7234) 的快取中儲存回應以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="39a0b-218">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="39a0b-219">用來產生安全權杖以防止跨網站偽造要求 (CSRF) 攻擊的 Antiforgery 系統會將 `Cache-Control` 和 `Pragma` 標頭設定為， `no-cache` 如此就不會快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-219">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="39a0b-220">如需有關如何停用 HTML 表單元素之 antiforgery token 的詳細資訊，請參閱 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-220">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39a0b-221">其他資源</span><span class="sxs-lookup"><span data-stu-id="39a0b-221">Additional resources</span></span>

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

<span data-ttu-id="39a0b-222">本文說明如何在 ASP.NET Core 應用程式中設定回應快取中介軟體。</span><span class="sxs-lookup"><span data-stu-id="39a0b-222">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="39a0b-223">中介軟體會決定何時可快取回應、儲存回應，以及提供快取的回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-223">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="39a0b-224">如需 HTTP 快取和屬性的簡介 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，請參閱 [回應](xref:performance/caching/response)快取。</span><span class="sxs-lookup"><span data-stu-id="39a0b-224">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="39a0b-225">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="39a0b-225">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="39a0b-226">設定</span><span class="sxs-lookup"><span data-stu-id="39a0b-226">Configuration</span></span>

<span data-ttu-id="39a0b-227">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ，或將套件參考新增至 [AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 套件。</span><span class="sxs-lookup"><span data-stu-id="39a0b-227">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="39a0b-228">在中 `Startup.ConfigureServices` ，將回應快取中介軟體新增至服務集合：</span><span class="sxs-lookup"><span data-stu-id="39a0b-228">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="39a0b-229">將應用程式設定為使用具有擴充方法的中介軟體 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> ，這會將中介軟體新增至中的要求處理管線 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="39a0b-229">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="39a0b-230">範例應用程式會新增標頭，以控制後續要求的快取：</span><span class="sxs-lookup"><span data-stu-id="39a0b-230">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="39a0b-231">快取[控制](https://tools.ietf.org/html/rfc7234#section-5.2)：快取最多10秒的可快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-231">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="39a0b-232">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：只有在後續要求的接受編碼標頭符合原始要求的 [接受編碼](https://tools.ietf.org/html/rfc7231#section-5.3.4) 標頭時，才會將中介軟體設定為提供快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-232">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="39a0b-233">上述的標頭不會寫入至回應，並會在控制器、動作或頁面上覆寫 Razor ：</span><span class="sxs-lookup"><span data-stu-id="39a0b-233">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="39a0b-234">具有 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="39a0b-234">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="39a0b-235">即使未設定屬性也適用。</span><span class="sxs-lookup"><span data-stu-id="39a0b-235">This applies even if a property isn't set.</span></span> <span data-ttu-id="39a0b-236">例如，省略 [VaryByHeader](./response.md#vary) 屬性將會從回應中移除對應的標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-236">For example, omitting the [VaryByHeader](./response.md#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="39a0b-237">回應快取中介軟體只會快取導致 200 (確定) 狀態碼的伺服器回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-237">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="39a0b-238">中介軟體會忽略任何其他回應（包括 [錯誤頁面](xref:fundamentals/error-handling)）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-238">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="39a0b-239">包含已驗證之用戶端內容的回應必須標示為無法快取，以防止中介軟體儲存和提供這些回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-239">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="39a0b-240">如需有關中介軟體如何判斷回應是否可快取的詳細資訊，請參閱快取的 [條件](#conditions-for-caching) 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-240">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="39a0b-241">選項</span><span class="sxs-lookup"><span data-stu-id="39a0b-241">Options</span></span>

<span data-ttu-id="39a0b-242">回應快取選項如下表所示。</span><span class="sxs-lookup"><span data-stu-id="39a0b-242">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="39a0b-243">選項</span><span class="sxs-lookup"><span data-stu-id="39a0b-243">Option</span></span> | <span data-ttu-id="39a0b-244">描述</span><span class="sxs-lookup"><span data-stu-id="39a0b-244">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="39a0b-245">回應主體的最大可快取大小（以位元組為單位）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-245">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="39a0b-246">預設值為 `64 * 1024 * 1024` (64 MB) 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-246">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="39a0b-247">回應快取中介軟體的大小限制（以位元組為單位）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-247">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="39a0b-248">預設值為 `100 * 1024 * 1024` (100 MB) 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-248">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="39a0b-249">判斷是否要在區分大小寫的路徑上快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-249">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="39a0b-250">預設值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="39a0b-250">The default value is `false`.</span></span> |

<span data-ttu-id="39a0b-251">下列範例會將中介軟體設定為：</span><span class="sxs-lookup"><span data-stu-id="39a0b-251">The following example configures the middleware to:</span></span>

* <span data-ttu-id="39a0b-252">主體大小小於或等於1024個位元組的快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-252">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="39a0b-253">依區分大小寫的路徑儲存回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-253">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="39a0b-254">例如， `/page1` 和 `/Page1` 會分開儲存。</span><span class="sxs-lookup"><span data-stu-id="39a0b-254">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="39a0b-255">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="39a0b-255">VaryByQueryKeys</span></span>

<span data-ttu-id="39a0b-256">使用 MVC/web API 控制器或 Razor 頁面頁面模型時， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 屬性會指定為回應快取設定適當標頭所需的參數。</span><span class="sxs-lookup"><span data-stu-id="39a0b-256">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="39a0b-257">`[ResponseCache]`嚴格要求中介軟體的屬性唯一參數是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，其不會對應到實際的 HTTP 標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-257">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="39a0b-258">如需詳細資訊，請參閱<xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="39a0b-258">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="39a0b-259">不使用屬性時 `[ResponseCache]` ，回應快取可以不同 `VaryByQueryKeys` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-259">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="39a0b-260"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接從[HttpCoNtext. 功能](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：</span><span class="sxs-lookup"><span data-stu-id="39a0b-260">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="39a0b-261">使用等於 in 的單一值 `*` `VaryByQueryKeys` ，會依所有要求查詢參數而改變快取。</span><span class="sxs-lookup"><span data-stu-id="39a0b-261">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="39a0b-262">回應快取中介軟體所使用的 HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="39a0b-262">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="39a0b-263">下表提供會影響回應快取的 HTTP 標頭的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="39a0b-263">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="39a0b-264">標頭</span><span class="sxs-lookup"><span data-stu-id="39a0b-264">Header</span></span> | <span data-ttu-id="39a0b-265">詳細資料</span><span class="sxs-lookup"><span data-stu-id="39a0b-265">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="39a0b-266">如果標頭存在，則不會快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-266">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="39a0b-267">中介軟體只會考慮以 cache 指示詞標記的快取回應 `public` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-267">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="39a0b-268">使用下列參數控制快取：</span><span class="sxs-lookup"><span data-stu-id="39a0b-268">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="39a0b-269">最大壽命</span><span class="sxs-lookup"><span data-stu-id="39a0b-269">max-age</span></span></li><li><span data-ttu-id="39a0b-270">最大過時&#8224;</span><span class="sxs-lookup"><span data-stu-id="39a0b-270">max-stale&#8224;</span></span></li><li><span data-ttu-id="39a0b-271">最小值-最新</span><span class="sxs-lookup"><span data-stu-id="39a0b-271">min-fresh</span></span></li><li><span data-ttu-id="39a0b-272">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="39a0b-272">must-revalidate</span></span></li><li><span data-ttu-id="39a0b-273">no-cache</span><span class="sxs-lookup"><span data-stu-id="39a0b-273">no-cache</span></span></li><li><span data-ttu-id="39a0b-274">無存放區</span><span class="sxs-lookup"><span data-stu-id="39a0b-274">no-store</span></span></li><li><span data-ttu-id="39a0b-275">僅限-快取</span><span class="sxs-lookup"><span data-stu-id="39a0b-275">only-if-cached</span></span></li><li><span data-ttu-id="39a0b-276">private</span><span class="sxs-lookup"><span data-stu-id="39a0b-276">private</span></span></li><li><span data-ttu-id="39a0b-277">公開</span><span class="sxs-lookup"><span data-stu-id="39a0b-277">public</span></span></li><li><span data-ttu-id="39a0b-278">s-maxage</span><span class="sxs-lookup"><span data-stu-id="39a0b-278">s-maxage</span></span></li><li><span data-ttu-id="39a0b-279">proxy-重新驗證&#8225;</span><span class="sxs-lookup"><span data-stu-id="39a0b-279">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="39a0b-280">&#8224;如果未指定任何限制 `max-stale` ，則中介軟體不會採取任何動作。</span><span class="sxs-lookup"><span data-stu-id="39a0b-280">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="39a0b-281">&#8225;`proxy-revalidate` 的效果與相同 `must-revalidate` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-281">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="39a0b-282">如需詳細資訊，請參閱 [RFC 7231：要求](https://tools.ietf.org/html/rfc7234#section-5.2.1)快取控制指示詞。</span><span class="sxs-lookup"><span data-stu-id="39a0b-282">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="39a0b-283">`Pragma: no-cache`要求中的標頭會產生與相同的效果 `Cache-Control: no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-283">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="39a0b-284">標頭中的相關指示詞會覆寫此標頭 `Cache-Control` （如果有的話）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-284">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="39a0b-285">考慮使用 HTTP/1.0 與舊版相容。</span><span class="sxs-lookup"><span data-stu-id="39a0b-285">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="39a0b-286">如果標頭存在，則不會快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-286">The response isn't cached if the header exists.</span></span> <span data-ttu-id="39a0b-287">要求處理管線中設定一或多個的中介軟體， cookie 可防止回應快取中介軟體快取回應 (例如，以[ cookie TempData 提供者為基礎](xref:fundamentals/app-state#tempdata)的) 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-287">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="39a0b-288">`Vary`標頭是用來根據另一個標頭來改變快取的回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-288">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="39a0b-289">例如，藉由包含標頭來編碼快取回應 `Vary: Accept-Encoding` ，此標頭會快取標頭和個別要求的回應 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-289">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="39a0b-290">永遠不會儲存標頭值為的回應 `*` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-290">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="39a0b-291">除非由其他標頭覆寫，否則不會儲存或取出此標頭所過時的回應 `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-291">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="39a0b-292">如果值不是 `*` ，且回應的值 `ETag` 不符合任何提供的值，則會從快取提供完整回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-292">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="39a0b-293">否則，將會提供 304 (未修改) 回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-293">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="39a0b-294">如果 `If-None-Match` 標頭不存在，則會在快取的回應日期比提供的值更新時，從快取中提供完整回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-294">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="39a0b-295">否則，就會提供 *304-未修改* 的回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-295">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="39a0b-296">從快取提供時， `Date` 如果未在原始回應中提供標頭，則會設定中介軟體的標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-296">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="39a0b-297">從快取提供時， `Content-Length` 如果未在原始回應中提供標頭，則會設定中介軟體的標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-297">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="39a0b-298">`Age`原始回應中傳送的標頭會被忽略。</span><span class="sxs-lookup"><span data-stu-id="39a0b-298">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="39a0b-299">提供快取回應時，中介軟體會計算新值。</span><span class="sxs-lookup"><span data-stu-id="39a0b-299">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="39a0b-300">快取遵循要求快取控制指示詞</span><span class="sxs-lookup"><span data-stu-id="39a0b-300">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="39a0b-301">中介軟體會遵循 [HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2)快取規格的規則。</span><span class="sxs-lookup"><span data-stu-id="39a0b-301">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="39a0b-302">這些規則需要快取來接受 `Cache-Control` 用戶端所傳送的有效標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-302">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="39a0b-303">在此規格下，用戶端可以使用 `no-cache` 標頭值提出要求，並強制服務器針對每個要求產生新的回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-303">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="39a0b-304">目前，使用中介軟體時，不會有開發人員控制這種快取行為，因為中介軟體會遵守官方快取規格。</span><span class="sxs-lookup"><span data-stu-id="39a0b-304">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="39a0b-305">若要更充分掌控快取行為，請探索 ASP.NET Core 的其他快取功能。</span><span class="sxs-lookup"><span data-stu-id="39a0b-305">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="39a0b-306">請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="39a0b-306">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="39a0b-307">疑難排解</span><span class="sxs-lookup"><span data-stu-id="39a0b-307">Troubleshooting</span></span>

<span data-ttu-id="39a0b-308">如果快取行為不符合預期，請確認回應可以快取，而且能夠從快取中提供服務。</span><span class="sxs-lookup"><span data-stu-id="39a0b-308">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="39a0b-309">檢查要求的連入標頭和回應的連出標頭。</span><span class="sxs-lookup"><span data-stu-id="39a0b-309">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="39a0b-310">啟用 [記錄](xref:fundamentals/logging/index) 以協助進行調試。</span><span class="sxs-lookup"><span data-stu-id="39a0b-310">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="39a0b-311">在測試和疑難排解快取行為時，瀏覽器可能會設定要求標頭，以不需要的方式影響快取。</span><span class="sxs-lookup"><span data-stu-id="39a0b-311">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="39a0b-312">例如，瀏覽器可能會在重新整理 `Cache-Control` 頁面時，將標頭設定為 `no-cache` 或 `max-age=0` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-312">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="39a0b-313">下列工具可以明確地設定要求標頭，並建議用於測試快取：</span><span class="sxs-lookup"><span data-stu-id="39a0b-313">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="39a0b-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="39a0b-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="39a0b-315">Postman</span><span class="sxs-lookup"><span data-stu-id="39a0b-315">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="39a0b-316">快取的條件</span><span class="sxs-lookup"><span data-stu-id="39a0b-316">Conditions for caching</span></span>

* <span data-ttu-id="39a0b-317">要求必須產生 200 (確定) 狀態碼的伺服器回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-317">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="39a0b-318">要求方法必須是 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="39a0b-318">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="39a0b-319">在中 `Startup.Configure` ，回應快取中介軟體必須放在需要快取的中介軟體之前。</span><span class="sxs-lookup"><span data-stu-id="39a0b-319">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="39a0b-320">如需詳細資訊，請參閱<xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="39a0b-320">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="39a0b-321">`Authorization`標頭不得存在。</span><span class="sxs-lookup"><span data-stu-id="39a0b-321">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="39a0b-322">`Cache-Control` 標頭參數必須是有效的，而且回應必須標記 `public` 且未標記 `private` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-322">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="39a0b-323">`Pragma: no-cache`如果標頭不存在，標頭不能是存在的 `Cache-Control` ，因為標頭會 `Cache-Control` 覆寫 `Pragma` 標頭（如果有的話）。</span><span class="sxs-lookup"><span data-stu-id="39a0b-323">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="39a0b-324">`Set-Cookie`標頭不得存在。</span><span class="sxs-lookup"><span data-stu-id="39a0b-324">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="39a0b-325">`Vary` 標頭參數必須有效且不等於 `*` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-325">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="39a0b-326">`Content-Length`標頭值 (如果設定的) 必須符合回應主體的大小。</span><span class="sxs-lookup"><span data-stu-id="39a0b-326">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="39a0b-327"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>未使用。</span><span class="sxs-lookup"><span data-stu-id="39a0b-327">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="39a0b-328">回應不得如 `Expires` 標頭和和快取指示詞所指定的過時 `max-age` `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-328">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="39a0b-329">回應緩衝處理必須成功。</span><span class="sxs-lookup"><span data-stu-id="39a0b-329">Response buffering must be successful.</span></span> <span data-ttu-id="39a0b-330">回應的大小必須小於已設定或預設值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-330">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="39a0b-331">回應的主體大小必須小於已設定或預設值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-331">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="39a0b-332">回應必須根據 [RFC 7234](https://tools.ietf.org/html/rfc7234) 規格來快取。</span><span class="sxs-lookup"><span data-stu-id="39a0b-332">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="39a0b-333">例如，指示詞 `no-store` 不能存在於要求或回應標頭欄位中。</span><span class="sxs-lookup"><span data-stu-id="39a0b-333">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="39a0b-334">請參閱第 *3 節：在* [RFC 7234](https://tools.ietf.org/html/rfc7234) 的快取中儲存回應以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="39a0b-334">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="39a0b-335">用來產生安全權杖以防止跨網站偽造要求 (CSRF) 攻擊的 Antiforgery 系統會將 `Cache-Control` 和 `Pragma` 標頭設定為， `no-cache` 如此就不會快取回應。</span><span class="sxs-lookup"><span data-stu-id="39a0b-335">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="39a0b-336">如需有關如何停用 HTML 表單元素之 antiforgery token 的詳細資訊，請參閱 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="39a0b-336">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39a0b-337">其他資源</span><span class="sxs-lookup"><span data-stu-id="39a0b-337">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end