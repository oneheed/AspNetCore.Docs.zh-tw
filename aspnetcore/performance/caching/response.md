---
title: ASP.NET 核心中的回應快取
author: rick-anderson
description: 了解如何使用回應快取來降低頻寬需求，並提升 ASP.NET Core 應用程式的效能。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
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
uid: performance/caching/response
ms.openlocfilehash: 539ddb118279adb3a53394cdb0c2e5169092ebc0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589227"
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="3efaa-103">ASP.NET 核心中的回應快取</span><span class="sxs-lookup"><span data-stu-id="3efaa-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="3efaa-104">[John Luo](https://github.com/JunTaoLuo)、 [Rick Anderson](https://twitter.com/RickAndMSFT)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3efaa-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3efaa-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/response/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3efaa-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3efaa-106">回應快取可減少用戶端或 proxy 對 web 伺服器發出的要求數目。</span><span class="sxs-lookup"><span data-stu-id="3efaa-106">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="3efaa-107">回應快取也會減少 web 伺服器產生回應所執行的工作量。</span><span class="sxs-lookup"><span data-stu-id="3efaa-107">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="3efaa-108">回應快取是由標頭所控制，這些標頭會指定您希望用戶端、proxy 和中介軟體快取回應的方式。</span><span class="sxs-lookup"><span data-stu-id="3efaa-108">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="3efaa-109">[ResponseCache 屬性](#responsecache-attribute)會參與設定回應快取標頭。</span><span class="sxs-lookup"><span data-stu-id="3efaa-109">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="3efaa-110">用戶端和中繼 proxy 應接受在 [HTTP 1.1](https://tools.ietf.org/html/rfc7234)快取規格下快取回應的標頭。</span><span class="sxs-lookup"><span data-stu-id="3efaa-110">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="3efaa-111">若為遵循 HTTP 1.1 快取規格的伺服器端快取，請使用回應快取 [中介軟體](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="3efaa-111">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="3efaa-112">中介軟體可以使用 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 屬性來影響伺服器端快取行為。</span><span class="sxs-lookup"><span data-stu-id="3efaa-112">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="3efaa-113">以 HTTP 為基礎的回應快取</span><span class="sxs-lookup"><span data-stu-id="3efaa-113">HTTP-based response caching</span></span>

<span data-ttu-id="3efaa-114">[HTTP 1.1](https://tools.ietf.org/html/rfc7234)快取規格描述了網際網路快取的行為。</span><span class="sxs-lookup"><span data-stu-id="3efaa-114">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="3efaa-115">用於快取的主要 HTTP 標頭是快取 [控制項](https://tools.ietf.org/html/rfc7234#section-5.2)，用來指定快取指示 *詞。*</span><span class="sxs-lookup"><span data-stu-id="3efaa-115">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="3efaa-116">指示詞會控制快取行為，因為要求會從用戶端傳送到伺服器，而回應會將其從伺服器傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3efaa-116">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="3efaa-117">要求和回應會在 proxy 伺服器之間移動，而 proxy 伺服器也必須符合 HTTP 1.1 快取規格。</span><span class="sxs-lookup"><span data-stu-id="3efaa-117">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="3efaa-118">`Cache-Control`下表顯示一般指示詞。</span><span class="sxs-lookup"><span data-stu-id="3efaa-118">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="3efaa-119">指示詞</span><span class="sxs-lookup"><span data-stu-id="3efaa-119">Directive</span></span>                                                       | <span data-ttu-id="3efaa-120">動作</span><span class="sxs-lookup"><span data-stu-id="3efaa-120">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="3efaa-121">public</span><span class="sxs-lookup"><span data-stu-id="3efaa-121">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="3efaa-122">快取可以儲存回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-122">A cache may store the response.</span></span> |
| [<span data-ttu-id="3efaa-123">私人</span><span class="sxs-lookup"><span data-stu-id="3efaa-123">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="3efaa-124">共用快取不得儲存回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-124">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="3efaa-125">私用快取可能會儲存並重複使用回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-125">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="3efaa-126">最大壽命</span><span class="sxs-lookup"><span data-stu-id="3efaa-126">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="3efaa-127">用戶端不會接受其存留期大於指定秒數的回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-127">The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="3efaa-128">範例： `max-age=60` (60 秒) ， `max-age=2592000` (1 個月) </span><span class="sxs-lookup"><span data-stu-id="3efaa-128">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="3efaa-129">no-cache</span><span class="sxs-lookup"><span data-stu-id="3efaa-129">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="3efaa-130">**在要求上**：快取不能使用預存回應來滿足要求。</span><span class="sxs-lookup"><span data-stu-id="3efaa-130">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="3efaa-131">源伺服器會重新產生用戶端的回應，中介軟體會更新其快取中的預存回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-131">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="3efaa-132">**回應時**：回應不得用於後續要求，而不需要在源伺服器上進行驗證。</span><span class="sxs-lookup"><span data-stu-id="3efaa-132">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="3efaa-133">無存放區</span><span class="sxs-lookup"><span data-stu-id="3efaa-133">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="3efaa-134">**要求時**：快取不得儲存要求。</span><span class="sxs-lookup"><span data-stu-id="3efaa-134">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="3efaa-135">**回應時**：快取不得儲存回應的任何部分。</span><span class="sxs-lookup"><span data-stu-id="3efaa-135">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="3efaa-136">下表顯示在快取中扮演角色的其他快取標頭。</span><span class="sxs-lookup"><span data-stu-id="3efaa-136">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="3efaa-137">標頭</span><span class="sxs-lookup"><span data-stu-id="3efaa-137">Header</span></span>                                                     | <span data-ttu-id="3efaa-138">函式</span><span class="sxs-lookup"><span data-stu-id="3efaa-138">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="3efaa-139">Age</span><span class="sxs-lookup"><span data-stu-id="3efaa-139">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="3efaa-140">從源伺服器上產生或成功驗證回應以來的時間量預估（以秒為單位）。</span><span class="sxs-lookup"><span data-stu-id="3efaa-140">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="3efaa-141">到期</span><span class="sxs-lookup"><span data-stu-id="3efaa-141">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="3efaa-142">經過一段時間之後，就會將回應視為過時。</span><span class="sxs-lookup"><span data-stu-id="3efaa-142">The time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="3efaa-143">Pragma</span><span class="sxs-lookup"><span data-stu-id="3efaa-143">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="3efaa-144">存在於與 HTTP/1.0 快取之間的相容性，以設定 `no-cache` 行為。</span><span class="sxs-lookup"><span data-stu-id="3efaa-144">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="3efaa-145">如果 `Cache-Control` 標頭存在，則 `Pragma` 會忽略標頭。</span><span class="sxs-lookup"><span data-stu-id="3efaa-145">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="3efaa-146">不同</span><span class="sxs-lookup"><span data-stu-id="3efaa-146">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="3efaa-147">指定除非快取 `Vary` 回應的原始要求和新要求中的所有標頭欄位都相符，否則不得傳送快取回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-147">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="3efaa-148">以 HTTP 為基礎的快取遵循要求 Cache-Control 指示詞</span><span class="sxs-lookup"><span data-stu-id="3efaa-148">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="3efaa-149">[Cache-Control 標頭的 HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2)快取規格需要快取，才能接受 `Cache-Control` 用戶端所傳送的有效標頭。</span><span class="sxs-lookup"><span data-stu-id="3efaa-149">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="3efaa-150">用戶端可以使用 `no-cache` 標頭值提出要求，並強制服務器針對每個要求產生新的回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-150">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="3efaa-151">如果您考慮 HTTP 快取的目標，一律接受用戶端 `Cache-Control` 要求標頭會有意義。</span><span class="sxs-lookup"><span data-stu-id="3efaa-151">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="3efaa-152">在官方規格下，快取的目的是要降低跨用戶端、proxy 和伺服器網路滿足要求的延遲和網路額外負荷。</span><span class="sxs-lookup"><span data-stu-id="3efaa-152">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="3efaa-153">它不一定是控制源伺服器負載的方式。</span><span class="sxs-lookup"><span data-stu-id="3efaa-153">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="3efaa-154">使用回應快取 [中介軟體](xref:performance/caching/middleware) 時，不會有開發人員控制這種快取行為，因為中介軟體會遵守官方快取規格。</span><span class="sxs-lookup"><span data-stu-id="3efaa-154">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="3efaa-155">對[中介軟體進行規劃的增強功能](https://github.com/dotnet/AspNetCore/issues/2612)，可讓您設定中介軟體 `Cache-Control` 在決定提供快取回應時忽略要求的標頭。</span><span class="sxs-lookup"><span data-stu-id="3efaa-155">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="3efaa-156">規劃的增強功能可讓您有機會更妥善控制伺服器負載。</span><span class="sxs-lookup"><span data-stu-id="3efaa-156">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="3efaa-157">ASP.NET Core 中的其他快取技術</span><span class="sxs-lookup"><span data-stu-id="3efaa-157">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="3efaa-158">記憶體內部快取</span><span class="sxs-lookup"><span data-stu-id="3efaa-158">In-memory caching</span></span>

<span data-ttu-id="3efaa-159">記憶體中的快取會使用伺服器記憶體來儲存快取的資料。</span><span class="sxs-lookup"><span data-stu-id="3efaa-159">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="3efaa-160">這種類型的快取適用于單一伺服器或使用「 *粘滯話*」的多部伺服器。</span><span class="sxs-lookup"><span data-stu-id="3efaa-160">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="3efaa-161">「粘滯話」表示來自用戶端的要求一律會路由傳送至相同的伺服器進行處理。</span><span class="sxs-lookup"><span data-stu-id="3efaa-161">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="3efaa-162">如需詳細資訊，請參閱<xref:performance/caching/memory>。</span><span class="sxs-lookup"><span data-stu-id="3efaa-162">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="3efaa-163">分散式快取</span><span class="sxs-lookup"><span data-stu-id="3efaa-163">Distributed Cache</span></span>

<span data-ttu-id="3efaa-164">當應用程式裝載于雲端或伺服器陣列時，請使用分散式快取將資料儲存在記憶體中。</span><span class="sxs-lookup"><span data-stu-id="3efaa-164">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="3efaa-165">快取會在處理要求的伺服器間共用。</span><span class="sxs-lookup"><span data-stu-id="3efaa-165">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="3efaa-166">如果用戶端的快取資料可供使用，用戶端可以提交由群組中的任何伺服器所處理的要求。</span><span class="sxs-lookup"><span data-stu-id="3efaa-166">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="3efaa-167">ASP.NET Core 適用于 SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)和 [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 分散式快取。</span><span class="sxs-lookup"><span data-stu-id="3efaa-167">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="3efaa-168">如需詳細資訊，請參閱<xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="3efaa-168">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="3efaa-169">快取標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="3efaa-169">Cache Tag Helper</span></span>

<span data-ttu-id="3efaa-170">使用快取標籤協助程式從 MVC 視圖或頁面快取內容 Razor 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-170">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="3efaa-171">快取標籤協助程式會使用記憶體內部快取來儲存資料。</span><span class="sxs-lookup"><span data-stu-id="3efaa-171">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="3efaa-172">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="3efaa-172">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="3efaa-173">分散式快取標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="3efaa-173">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="3efaa-174">使用分散式快取標籤協助程式 Razor ，在分散式雲端或 web 伺服陣列案例中，從 MVC 視圖或頁面快取內容。</span><span class="sxs-lookup"><span data-stu-id="3efaa-174">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="3efaa-175">分散式快取標籤協助程式會使用 SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)或 [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 來儲存資料。</span><span class="sxs-lookup"><span data-stu-id="3efaa-175">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="3efaa-176">如需詳細資訊，請參閱<xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="3efaa-176">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="3efaa-177">ResponseCache 屬性</span><span class="sxs-lookup"><span data-stu-id="3efaa-177">ResponseCache attribute</span></span>

<span data-ttu-id="3efaa-178"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>指定在回應快取中設定適當標頭所需的參數。</span><span class="sxs-lookup"><span data-stu-id="3efaa-178">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="3efaa-179">針對包含已驗證用戶端資訊的內容停用快取。</span><span class="sxs-lookup"><span data-stu-id="3efaa-179">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="3efaa-180">只有根據使用者的身分識別或使用者是否已登入而不會變更的內容，才應啟用快取。</span><span class="sxs-lookup"><span data-stu-id="3efaa-180">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="3efaa-181"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 依指定的查詢索引鍵清單值，來改變儲存的回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-181"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="3efaa-182">當提供單一值時 `*` ，中介軟體會依所有要求查詢字串參數來改變回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-182">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="3efaa-183">必須啟用回應快取[中介軟體](xref:performance/caching/middleware)才能設定 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 屬性。</span><span class="sxs-lookup"><span data-stu-id="3efaa-183">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="3efaa-184">否則會擲回執行時間例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3efaa-184">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="3efaa-185">屬性沒有對應的 HTTP 標頭 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-185">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="3efaa-186">屬性是由回應快取中介軟體所處理的 HTTP 功能。</span><span class="sxs-lookup"><span data-stu-id="3efaa-186">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="3efaa-187">若要讓中介軟體提供快取的回應，查詢字串和查詢字串值必須符合先前的要求。</span><span class="sxs-lookup"><span data-stu-id="3efaa-187">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="3efaa-188">例如，請考慮下表所示的要求和結果順序。</span><span class="sxs-lookup"><span data-stu-id="3efaa-188">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="3efaa-189">要求</span><span class="sxs-lookup"><span data-stu-id="3efaa-189">Request</span></span>                          | <span data-ttu-id="3efaa-190">結果</span><span class="sxs-lookup"><span data-stu-id="3efaa-190">Result</span></span>                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | <span data-ttu-id="3efaa-191">從伺服器傳回。</span><span class="sxs-lookup"><span data-stu-id="3efaa-191">Returned from the server.</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="3efaa-192">從中介軟體傳回。</span><span class="sxs-lookup"><span data-stu-id="3efaa-192">Returned from middleware.</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="3efaa-193">從伺服器傳回。</span><span class="sxs-lookup"><span data-stu-id="3efaa-193">Returned from the server.</span></span> |

<span data-ttu-id="3efaa-194">第一個要求是由伺服器傳回，並快取在中介軟體中。</span><span class="sxs-lookup"><span data-stu-id="3efaa-194">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="3efaa-195">因為查詢字串符合先前的要求，所以中介軟體會傳回第二個要求。</span><span class="sxs-lookup"><span data-stu-id="3efaa-195">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="3efaa-196">第三個要求不在中介軟體快取中，因為查詢字串值不符合先前的要求。</span><span class="sxs-lookup"><span data-stu-id="3efaa-196">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="3efaa-197"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>用來透過) a 設定和建立 (<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-197">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="3efaa-198">會 `ResponseCacheFilter` 執行更新適當 HTTP 標頭和回應功能的工作。</span><span class="sxs-lookup"><span data-stu-id="3efaa-198">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="3efaa-199">篩選準則：</span><span class="sxs-lookup"><span data-stu-id="3efaa-199">The filter:</span></span>

* <span data-ttu-id="3efaa-200">移除、和的任何現有標頭 `Vary` `Cache-Control` `Pragma` 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-200">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="3efaa-201">根據中設定的屬性寫出適當的標頭 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-201">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="3efaa-202">如果已設定，則更新回應快取 HTTP 功能 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-202">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="3efaa-203">不同</span><span class="sxs-lookup"><span data-stu-id="3efaa-203">Vary</span></span>

<span data-ttu-id="3efaa-204">只有在設定屬性時，才會寫入此標頭 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-204">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="3efaa-205">屬性設定為 `Vary` 屬性的值。</span><span class="sxs-lookup"><span data-stu-id="3efaa-205">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="3efaa-206">下列範例會使用 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 屬性：</span><span class="sxs-lookup"><span data-stu-id="3efaa-206">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="3efaa-207">使用範例應用程式，以瀏覽器的網路工具來查看回應標頭。</span><span class="sxs-lookup"><span data-stu-id="3efaa-207">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="3efaa-208">下列回應標頭會隨著 Cache1 頁面回應傳送：</span><span class="sxs-lookup"><span data-stu-id="3efaa-208">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="3efaa-209">NoStore 和 Location。無</span><span class="sxs-lookup"><span data-stu-id="3efaa-209">NoStore and Location.None</span></span>

<span data-ttu-id="3efaa-210"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 覆寫大部分的其他屬性。</span><span class="sxs-lookup"><span data-stu-id="3efaa-210"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="3efaa-211">當這個屬性設定為時 `true` ， `Cache-Control` 標頭會設定為 `no-store` 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-211">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="3efaa-212">如果 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 設定為 `None` ：</span><span class="sxs-lookup"><span data-stu-id="3efaa-212">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="3efaa-213">`Cache-Control` 設定為 `no-store,no-cache`。</span><span class="sxs-lookup"><span data-stu-id="3efaa-213">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="3efaa-214">`Pragma` 設定為 `no-cache`。</span><span class="sxs-lookup"><span data-stu-id="3efaa-214">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="3efaa-215">如果 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 是 `false` <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ，而且是 `None` 、和，則 `Cache-Control` `Pragma` 設定為 `no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-215">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="3efaa-216"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 通常會 `true` 針對錯誤頁面設定為。</span><span class="sxs-lookup"><span data-stu-id="3efaa-216"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="3efaa-217">範例應用程式中的 [Cache2] 頁面會產生回應標頭，以指示用戶端不要儲存回應。</span><span class="sxs-lookup"><span data-stu-id="3efaa-217">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="3efaa-218">範例應用程式會傳回 Cache2 頁面，其中包含下列標頭：</span><span class="sxs-lookup"><span data-stu-id="3efaa-218">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="3efaa-219">位置和持續時間</span><span class="sxs-lookup"><span data-stu-id="3efaa-219">Location and Duration</span></span>

<span data-ttu-id="3efaa-220">若要啟用快取， <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 必須設為正值，而且 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 必須是 `Any` (預設) 或 `Client` 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-220">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="3efaa-221">架構會將 `Cache-Control` 標頭設定為位置值，後面接著 `max-age` 回應的。</span><span class="sxs-lookup"><span data-stu-id="3efaa-221">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="3efaa-222"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>的選項 `Any` 和會 `Client` 分別轉譯為 `Cache-Control` 和的標頭值 `public` `private` 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-222"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="3efaa-223">如同 [NoStore 和 Location. None](#nostore-and-locationnone) 區段中所述，設定 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 以 `None` 將 `Cache-Control` 和 `Pragma` 標頭設定為 `no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-223">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="3efaa-224">`Location.Any` (`Cache-Control` 設定為 `public`) 表示 *用戶端或任何中繼 proxy* 可能會快取此值，包括回應快取 [中介軟體](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="3efaa-224">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="3efaa-225">`Location.Client` (`Cache-Control` 設定為 `private`) 表示 *只有用戶端* 可以快取該值。</span><span class="sxs-lookup"><span data-stu-id="3efaa-225">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="3efaa-226">沒有中繼快取應該快取值，包括回應快取 [中介軟體](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="3efaa-226">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="3efaa-227">快取控制標頭只會提供用戶端和中繼 proxy 的指引，以及快取回應的方式。</span><span class="sxs-lookup"><span data-stu-id="3efaa-227">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="3efaa-228">不保證用戶端和 proxy 會採用 [HTTP 1.1](https://tools.ietf.org/html/rfc7234)快取規格。</span><span class="sxs-lookup"><span data-stu-id="3efaa-228">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="3efaa-229">[回應快取中介軟體](xref:performance/caching/middleware) 一律遵循規格所配置的快取規則。</span><span class="sxs-lookup"><span data-stu-id="3efaa-229">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="3efaa-230">下列範例顯示來自範例應用程式的 Cache3 頁面模型，以及藉由設定 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 和保留預設值所產生的標頭 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ：</span><span class="sxs-lookup"><span data-stu-id="3efaa-230">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="3efaa-231">範例應用程式會傳回包含下列標頭的 Cache3 頁面：</span><span class="sxs-lookup"><span data-stu-id="3efaa-231">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="3efaa-232">快取設定檔</span><span class="sxs-lookup"><span data-stu-id="3efaa-232">Cache profiles</span></span>

<span data-ttu-id="3efaa-233">快取設定檔可設定為在中設定 MVC/Pages 時的選項，而不是複製許多控制器動作屬性上的回應快取設定 Razor `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="3efaa-233">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3efaa-234">在參考的快取設定檔中找到的值會當做的預設值使用 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> ，並由屬性上指定的任何屬性覆寫。</span><span class="sxs-lookup"><span data-stu-id="3efaa-234">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="3efaa-235">設定快取設定檔。</span><span class="sxs-lookup"><span data-stu-id="3efaa-235">Set up a cache profile.</span></span> <span data-ttu-id="3efaa-236">下列範例顯示範例應用程式中的30秒快取設定檔 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="3efaa-236">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response/samples/3.x/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="3efaa-237">範例應用程式的 Cache4 頁面模型會參考快取 `Default30` 設定檔：</span><span class="sxs-lookup"><span data-stu-id="3efaa-237">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="3efaa-238"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>可套用至：</span><span class="sxs-lookup"><span data-stu-id="3efaa-238">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* <span data-ttu-id="3efaa-239">Razor 頁面：無法將屬性套用至處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="3efaa-239">Razor Pages: Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="3efaa-240">MVC 控制器。</span><span class="sxs-lookup"><span data-stu-id="3efaa-240">MVC controllers.</span></span>
* <span data-ttu-id="3efaa-241">MVC 動作方法：方法層級屬性會覆寫類別層級屬性中指定的設定。</span><span class="sxs-lookup"><span data-stu-id="3efaa-241">MVC action methods: Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="3efaa-242">快取設定檔會將產生的標頭套用至 Cache4 頁面回應 `Default30` ：</span><span class="sxs-lookup"><span data-stu-id="3efaa-242">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="3efaa-243">其他資源</span><span class="sxs-lookup"><span data-stu-id="3efaa-243">Additional resources</span></span>

* [<span data-ttu-id="3efaa-244">在快取中儲存回應</span><span class="sxs-lookup"><span data-stu-id="3efaa-244">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="3efaa-245">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="3efaa-245">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
