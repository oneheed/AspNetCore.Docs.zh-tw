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
ms.openlocfilehash: b057e5e08b8a4d0f9bcd68f92102cad309655acc
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413505"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="3afe3-103">在 ASP.NET Core 中啟用 (CORS) 的跨原始來源要求</span><span class="sxs-lookup"><span data-stu-id="3afe3-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3afe3-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="3afe3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="3afe3-105">本文說明如何在 ASP.NET Core 應用程式中啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="3afe3-106">瀏覽器安全性可防止網頁提出要求，而不是與提供網頁的不同網域。</span><span class="sxs-lookup"><span data-stu-id="3afe3-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="3afe3-107">這項限制稱為 *相同原始來源原則*。</span><span class="sxs-lookup"><span data-stu-id="3afe3-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="3afe3-108">相同來源原則可防止惡意網站從另一個網站讀取敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="3afe3-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="3afe3-109">有時，您可能會想要允許其他網站對您的應用程式進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="3afe3-110">如需詳細資訊，請參閱 [MOZILLA CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="3afe3-111">[跨原始資源分享](https://www.w3.org/TR/cors/) (CORS) ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="3afe3-112">是一種 W3C 標準，可讓伺服器放寬相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="3afe3-113">不 **是安全性** 功能，CORS 放寬安全性。</span><span class="sxs-lookup"><span data-stu-id="3afe3-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="3afe3-114">API 不會藉由允許 CORS 來更安全。</span><span class="sxs-lookup"><span data-stu-id="3afe3-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="3afe3-115">如需詳細資訊，請參閱 [CORS 的運作方式](#how-cors)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="3afe3-116">允許伺服器明確允許某些跨原始來源的要求，同時拒絕其他要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="3afe3-117">比先前的技術（例如 [JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更有彈性。</span><span class="sxs-lookup"><span data-stu-id="3afe3-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="3afe3-118">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3afe3-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="3afe3-119">相同的來源</span><span class="sxs-lookup"><span data-stu-id="3afe3-119">Same origin</span></span>

<span data-ttu-id="3afe3-120">如果兩個 Url 具有相同的配置、主機和埠 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，其來源相同。</span><span class="sxs-lookup"><span data-stu-id="3afe3-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="3afe3-121">這兩個 Url 的來源相同：</span><span class="sxs-lookup"><span data-stu-id="3afe3-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="3afe3-122">這些 Url 的來源與前兩個 Url 不同：</span><span class="sxs-lookup"><span data-stu-id="3afe3-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="3afe3-123">`https://example.net`：不同的網域</span><span class="sxs-lookup"><span data-stu-id="3afe3-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="3afe3-124">`https://www.example.com/foo.html`：不同的子域</span><span class="sxs-lookup"><span data-stu-id="3afe3-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="3afe3-125">`http://example.com/foo.html`：不同的配置</span><span class="sxs-lookup"><span data-stu-id="3afe3-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="3afe3-126">`https://example.com:9000/foo.html`：不同的埠</span><span class="sxs-lookup"><span data-stu-id="3afe3-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="3afe3-127">啟用 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-127">Enable CORS</span></span>

<span data-ttu-id="3afe3-128">有三種方式可以啟用 CORS：</span><span class="sxs-lookup"><span data-stu-id="3afe3-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="3afe3-129">在中介軟體中使用 [命名原則](#np) 或 [預設原則](#dp)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="3afe3-130">使用 [端點路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="3afe3-131">使用 [[EnableCors]](#attr) 屬性。</span><span class="sxs-lookup"><span data-stu-id="3afe3-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="3afe3-132">使用具有命名原則的 [[EnableCors]](#attr) 屬性，可提供最精細的控制項來限制支援 CORS 的端點。</span><span class="sxs-lookup"><span data-stu-id="3afe3-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="3afe3-133"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> 必須以正確的順序呼叫。</span><span class="sxs-lookup"><span data-stu-id="3afe3-133"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> must be called in the correct order.</span></span> <span data-ttu-id="3afe3-134">如需詳細資訊，請參閱 [中介軟體順序](xref:fundamentals/middleware/index#middleware-order)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-134">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span> <span data-ttu-id="3afe3-135">例如， `UseCors` <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> 在使用時，必須先呼叫 `UseResponseCaching` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-135">For example, `UseCors` must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="3afe3-136">下列各節將詳細說明每種方法。</span><span class="sxs-lookup"><span data-stu-id="3afe3-136">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="3afe3-137">具有命名原則和中介軟體的 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-137">CORS with named policy and middleware</span></span>

<span data-ttu-id="3afe3-138">CORS 中介軟體會處理跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-138">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="3afe3-139">下列程式碼會將 CORS 原則套用至具有指定來源的所有應用程式端點：</span><span class="sxs-lookup"><span data-stu-id="3afe3-139">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="3afe3-140">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="3afe3-140">The preceding code:</span></span>

* <span data-ttu-id="3afe3-141">將原則名稱設定為 `_myAllowSpecificOrigins` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-141">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="3afe3-142">原則名稱是任意的。</span><span class="sxs-lookup"><span data-stu-id="3afe3-142">The policy name is arbitrary.</span></span>
* <span data-ttu-id="3afe3-143">呼叫 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 擴充方法，並指定  `_myAllowSpecificOrigins` CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-143">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="3afe3-144">`UseCors` 新增 CORS 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3afe3-144">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="3afe3-145">的呼叫 `UseCors` 必須放在之後 `UseRouting` ，但在之前 `UseAuthorization` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-145">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="3afe3-146">如需詳細資訊，請參閱 [中介軟體順序](xref:fundamentals/middleware/index#middleware-order)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-146">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="3afe3-147"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的呼叫。</span><span class="sxs-lookup"><span data-stu-id="3afe3-147">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="3afe3-148">Lambda 接受 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 物件。</span><span class="sxs-lookup"><span data-stu-id="3afe3-148">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="3afe3-149">設定[選項](#cors-policy-options)（例如 `WithOrigins` ）將在本文稍後說明。</span><span class="sxs-lookup"><span data-stu-id="3afe3-149">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="3afe3-150">啟用 `_myAllowSpecificOrigins` 所有控制器端點的 CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-150">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="3afe3-151">請參閱 [端點路由](#ecors) ，以將 CORS 原則套用至特定端點。</span><span class="sxs-lookup"><span data-stu-id="3afe3-151">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="3afe3-152">使用回應快取 [中介軟體](xref:performance/caching/middleware)時，請 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> 先呼叫 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-152">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="3afe3-153">使用端點路由時，CORS 中介軟體 **必須** 設定為在對和的呼叫之間執行 `UseRouting` `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-153">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="3afe3-154">請參閱 [測試 CORS](#testc) ，以取得測試程式碼類似上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="3afe3-154">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="3afe3-155"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法呼叫會將 CORS 服務新增至應用程式的服務容器：</span><span class="sxs-lookup"><span data-stu-id="3afe3-155">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="3afe3-156">如需詳細資訊，請參閱本檔中的 [CORS 原則選項](#cpo) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-156">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="3afe3-157">這些 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 方法可以連結，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="3afe3-157">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="3afe3-158">注意：指定的 URL **不** 能包含尾端斜線 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-158">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="3afe3-159">如果 URL 以終止 `/` ，則比較會 `false` 傳回，而且不會傳回任何標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-159">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="3afe3-160">使用預設原則和中介軟體的 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-160">CORS with default policy and middleware</span></span>

<span data-ttu-id="3afe3-161">下列反白顯示的程式碼會啟用預設 CORS 原則：</span><span class="sxs-lookup"><span data-stu-id="3afe3-161">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="3afe3-162">上述程式碼會將預設 CORS 原則套用至所有控制器端點。</span><span class="sxs-lookup"><span data-stu-id="3afe3-162">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="3afe3-163">使用端點路由來啟用 Cors</span><span class="sxs-lookup"><span data-stu-id="3afe3-163">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="3afe3-164">使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援 [自動預檢要求](#apf)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-164">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="3afe3-165">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/20709) 和 [使用端點路由測試 CORS 和 [HttpOptions]](#tcer)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-165">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="3afe3-166">使用端點路由時，您可以使用一組擴充方法，根據每個端點來啟用 CORS <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-166">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="3afe3-167">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="3afe3-167">In the preceding code:</span></span>

* <span data-ttu-id="3afe3-168">`app.UseCors` 啟用 CORS 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3afe3-168">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="3afe3-169">由於尚未設定預設原則，因此 `app.UseCors()` 單獨不會啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-169">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="3afe3-170">`/echo`和控制器端點允許使用指定原則的跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-170">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="3afe3-171">`/echo2`和 Razor Pages 端點 **不** 允許跨原始來源的要求，因為未指定預設原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-171">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="3afe3-172">[[DisableCors]](#dc) **屬性不會停用** 端點路由所啟用的 CORS `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-172">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="3afe3-173">如需測試程式碼的相關指示，請參閱 [使用端點路由與 [HttpOptions]](#tcer) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-173">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="3afe3-174">使用屬性來啟用 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-174">Enable CORS with attributes</span></span>

<span data-ttu-id="3afe3-175">使用 [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 屬性來啟用 CORS，並只將命名原則套用至需要 cors 的端點，可提供最精細的控制。</span><span class="sxs-lookup"><span data-stu-id="3afe3-175">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="3afe3-176">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)屬性提供全域套用 CORS 的替代方案。</span><span class="sxs-lookup"><span data-stu-id="3afe3-176">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="3afe3-177">`[EnableCors]`屬性會針對選取的端點啟用 CORS，而不是所有端點：</span><span class="sxs-lookup"><span data-stu-id="3afe3-177">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="3afe3-178">`[EnableCors]` 指定預設原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-178">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="3afe3-179">`[EnableCors("{Policy String}")]` 指定命名原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-179">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="3afe3-180">`[EnableCors]`屬性可套用至：</span><span class="sxs-lookup"><span data-stu-id="3afe3-180">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="3afe3-181">Razor 網頁 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="3afe3-181">Razor Page `PageModel`</span></span>
* <span data-ttu-id="3afe3-182">控制器</span><span class="sxs-lookup"><span data-stu-id="3afe3-182">Controller</span></span>
* <span data-ttu-id="3afe3-183">控制器動作方法</span><span class="sxs-lookup"><span data-stu-id="3afe3-183">Controller action method</span></span>

<span data-ttu-id="3afe3-184">您可以使用屬性，將不同的原則套用至控制器、頁面模型或動作方法 `[EnableCors]` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-184">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="3afe3-185">當 `[EnableCors]` 屬性套用至控制器、頁面模型或動作方法，並在中介軟體啟用 CORS 時，會套用 **這兩個** 原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-185">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="3afe3-186">**建議您不要結合原則。使用** `[EnableCors]` **屬性或中介軟體，而不是在相同的應用程式中。**</span><span class="sxs-lookup"><span data-stu-id="3afe3-186">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="3afe3-187">下列程式碼會將不同的原則套用至每個方法：</span><span class="sxs-lookup"><span data-stu-id="3afe3-187">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="3afe3-188">下列程式碼會建立兩個 CORS 原則：</span><span class="sxs-lookup"><span data-stu-id="3afe3-188">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="3afe3-189">針對限制 CORS 要求的最精細控制：</span><span class="sxs-lookup"><span data-stu-id="3afe3-189">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="3afe3-190">`[EnableCors("MyPolicy")]`與命名原則搭配使用。</span><span class="sxs-lookup"><span data-stu-id="3afe3-190">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="3afe3-191">請勿定義預設原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-191">Don't define a default policy.</span></span>
* <span data-ttu-id="3afe3-192">請勿使用 [端點路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-192">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="3afe3-193">下一節中的程式碼符合上述清單。</span><span class="sxs-lookup"><span data-stu-id="3afe3-193">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="3afe3-194">請參閱 [測試 CORS](#testc) ，以取得測試程式碼類似上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="3afe3-194">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="3afe3-195">停用 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-195">Disable CORS</span></span>

<span data-ttu-id="3afe3-196">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **屬性不會停用**[端點路由](#ecors)已啟用的 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-196">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="3afe3-197">下列程式碼會定義 CORS 原則 `"MyPolicy"` ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-197">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="3afe3-198">下列程式碼會停用動作的 CORS `GetValues2` ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-198">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="3afe3-199">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="3afe3-199">The preceding code:</span></span>

* <span data-ttu-id="3afe3-200">不會使用 [端點路由](#ecors)來啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-200">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="3afe3-201">未定義 [預設 CORS 原則](#dp)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-201">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="3afe3-202">使用 [[EnableCors ( "MyPolicy" ) ]](#attr) 來啟用 `"MyPolicy"` 控制器的 CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-202">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="3afe3-203">停用方法的 CORS `GetValues2` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-203">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="3afe3-204">請參閱 [測試 CORS](#testc) 以取得測試上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="3afe3-204">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="3afe3-205">CORS 原則選項</span><span class="sxs-lookup"><span data-stu-id="3afe3-205">CORS policy options</span></span>

<span data-ttu-id="3afe3-206">本節說明可在 CORS 原則中設定的各種選項：</span><span class="sxs-lookup"><span data-stu-id="3afe3-206">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="3afe3-207">設定允許的來源</span><span class="sxs-lookup"><span data-stu-id="3afe3-207">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="3afe3-208">設定允許的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="3afe3-208">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="3afe3-209">設定允許的要求標頭</span><span class="sxs-lookup"><span data-stu-id="3afe3-209">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="3afe3-210">設定公開的回應標頭</span><span class="sxs-lookup"><span data-stu-id="3afe3-210">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="3afe3-211">跨原始來源要求中的認證</span><span class="sxs-lookup"><span data-stu-id="3afe3-211">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="3afe3-212">設定預檢到期時間</span><span class="sxs-lookup"><span data-stu-id="3afe3-212">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="3afe3-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3afe3-214">對於某些選項而言，先閱讀「 [CORS 的運作方式](#how-cors) 」一節可能很有説明。</span><span class="sxs-lookup"><span data-stu-id="3afe3-214">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="3afe3-215">設定允許的來源</span><span class="sxs-lookup"><span data-stu-id="3afe3-215">Set the allowed origins</span></span>

<span data-ttu-id="3afe3-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允許所有來源中具有任何配置 (或) 的 CORS 要求 `http` `https` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="3afe3-217">`AllowAnyOrigin` 不安全，因為 *任何網站* 都可以對應用程式進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-217">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3afe3-218">指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的設定，而且可能會導致跨網站要求偽造。</span><span class="sxs-lookup"><span data-stu-id="3afe3-218">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="3afe3-219">當以這兩種方法設定應用程式時，CORS 服務會傳回不正確 CORS 回應。</span><span class="sxs-lookup"><span data-stu-id="3afe3-219">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="3afe3-220">`AllowAnyOrigin` 影響預檢要求和 `Access-Control-Allow-Origin` 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-220">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="3afe3-221">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="3afe3-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3afe3-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：將原則的屬性設定為函式 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> ，以在評估是否允許原始來源時，允許原始來源符合已設定的萬用字元網域。</span><span class="sxs-lookup"><span data-stu-id="3afe3-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="3afe3-223">設定允許的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="3afe3-223">Set the allowed HTTP methods</span></span>

<span data-ttu-id="3afe3-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="3afe3-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="3afe3-225">允許任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="3afe3-225">Allows any HTTP method:</span></span>
* <span data-ttu-id="3afe3-226">影響預檢要求和 `Access-Control-Allow-Methods` 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-226">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="3afe3-227">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="3afe3-227">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="3afe3-228">設定允許的要求標頭</span><span class="sxs-lookup"><span data-stu-id="3afe3-228">Set the allowed request headers</span></span>

<span data-ttu-id="3afe3-229">若要允許在 CORS 要求中傳送特定標頭（稱為「 [作者要求標頭](https://xhr.spec.whatwg.org/#request)」），請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 並指定允許的標頭：</span><span class="sxs-lookup"><span data-stu-id="3afe3-229">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="3afe3-230">若要允許所有 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-230">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="3afe3-231">`AllowAnyHeader` 影響預檢要求和 [存取控制要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 標頭標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-231">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="3afe3-232">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="3afe3-232">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3afe3-233">`WithHeaders`只有當傳送的標頭 `Access-Control-Request-Headers` 完全符合中所述的標頭時，才可能符合所指定之特定標頭的 CORS 中介軟體原則 `WithHeaders` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-233">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="3afe3-234">例如，假設應用程式設定如下：</span><span class="sxs-lookup"><span data-stu-id="3afe3-234">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="3afe3-235">CORS 中介軟體會拒絕具有下列要求標頭的預檢要求，因為 `Content-Language` ([HeaderNames ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) 未列在 `WithHeaders` ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-235">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="3afe3-236">應用程式會傳回 *200 OK* 回應，但不會傳送 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-236">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="3afe3-237">因此，瀏覽器不會嘗試跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-237">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="3afe3-238">設定公開的回應標頭</span><span class="sxs-lookup"><span data-stu-id="3afe3-238">Set the exposed response headers</span></span>

<span data-ttu-id="3afe3-239">根據預設，瀏覽器不會將所有回應標頭公開給應用程式。</span><span class="sxs-lookup"><span data-stu-id="3afe3-239">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="3afe3-240">如需詳細資訊，請參閱 [W3C 跨原始資源分享 (術語) ：簡單的回應標頭](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-240">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="3afe3-241">預設可用的回應標頭為：</span><span class="sxs-lookup"><span data-stu-id="3afe3-241">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="3afe3-242">CORS 規格會呼叫這些標頭 *簡單的回應標頭*。</span><span class="sxs-lookup"><span data-stu-id="3afe3-242">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="3afe3-243">若要讓應用程式使用其他標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-243">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="3afe3-244">跨原始來源要求中的認證</span><span class="sxs-lookup"><span data-stu-id="3afe3-244">Credentials in cross-origin requests</span></span>

<span data-ttu-id="3afe3-245">認證需要 CORS 要求中的特殊處理。</span><span class="sxs-lookup"><span data-stu-id="3afe3-245">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="3afe3-246">根據預設，瀏覽器不會傳送具有跨原始來源要求的認證。</span><span class="sxs-lookup"><span data-stu-id="3afe3-246">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="3afe3-247">認證包括 cookie s 和 HTTP 驗證配置。</span><span class="sxs-lookup"><span data-stu-id="3afe3-247">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="3afe3-248">若要傳送具有跨原始來源要求的認證，用戶端必須設定 `XMLHttpRequest.withCredentials` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-248">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="3afe3-249">`XMLHttpRequest`直接使用：</span><span class="sxs-lookup"><span data-stu-id="3afe3-249">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="3afe3-250">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="3afe3-250">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="3afe3-251">使用 [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="3afe3-251">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="3afe3-252">伺服器必須允許認證。</span><span class="sxs-lookup"><span data-stu-id="3afe3-252">The server must allow the credentials.</span></span> <span data-ttu-id="3afe3-253">若要允許跨原始來源認證，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-253">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="3afe3-254">HTTP 回應包含 `Access-Control-Allow-Credentials` 標頭，它會告知瀏覽器伺服器允許跨原始來源要求的認證。</span><span class="sxs-lookup"><span data-stu-id="3afe3-254">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="3afe3-255">如果瀏覽器傳送認證但回應不包含有效的 `Access-Control-Allow-Credentials` 標頭，則瀏覽器不會公開應用程式的回應，而且跨原始來源要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="3afe3-255">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="3afe3-256">允許跨原始來源的認證有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="3afe3-256">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="3afe3-257">另一個網域的網站可以代表使用者將已登入使用者的認證傳送給應用程式，而不需要使用者的知識。</span><span class="sxs-lookup"><span data-stu-id="3afe3-257">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="3afe3-258">CORS 規格也指出 `"*"` 如果標頭存在，設定來源 (所有來源) 無效 `Access-Control-Allow-Credentials` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-258">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="3afe3-259">預檢要求</span><span class="sxs-lookup"><span data-stu-id="3afe3-259">Preflight requests</span></span>

<span data-ttu-id="3afe3-260">針對某些 CORS 要求，瀏覽器會先傳送額外的 [選項](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 要求，再進行實際要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-260">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="3afe3-261">此要求稱為 [預檢要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-261">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="3afe3-262">如果下列 **所有** 條件都成立，則瀏覽器可以略過預檢要求：</span><span class="sxs-lookup"><span data-stu-id="3afe3-262">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="3afe3-263">要求方法為 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="3afe3-263">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="3afe3-264">應用程式不會設定 `Accept` 、、 `Accept-Language` `Content-Language` 、 `Content-Type` 或以外的要求標頭 `Last-Event-ID` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-264">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="3afe3-265">`Content-Type`如果設定，標頭會有下列其中一個值：</span><span class="sxs-lookup"><span data-stu-id="3afe3-265">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="3afe3-266">針對用戶端要求所設定之要求標頭的規則，會套用至應用程式透過在物件上呼叫所設定的標頭 `setRequestHeader` `XMLHttpRequest` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-266">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="3afe3-267">CORS 規格會呼叫這些標頭的 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-267">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="3afe3-268">此規則不適用於瀏覽器可以設定的標頭，例如 `User-Agent` 、 `Host` 或 `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-268">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="3afe3-269">以下是類似于本檔之 [測試 CORS](#testc)區段中 **[Put test]** 按鈕所提出之預檢要求的範例回應。</span><span class="sxs-lookup"><span data-stu-id="3afe3-269">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="3afe3-270">預檢要求會使用 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 方法。</span><span class="sxs-lookup"><span data-stu-id="3afe3-270">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="3afe3-271">它可能包含下列標頭：</span><span class="sxs-lookup"><span data-stu-id="3afe3-271">It may include the following headers:</span></span>

* <span data-ttu-id="3afe3-272">[存取控制-要求-方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)：將用於實際要求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="3afe3-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="3afe3-273">[存取控制-要求-標頭](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)：應用程式在實際要求上設定的要求標頭清單。</span><span class="sxs-lookup"><span data-stu-id="3afe3-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="3afe3-274">如先前所述，這並不包括瀏覽器所設定的標頭，例如 `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-274">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="3afe3-275">存取控制-允許方法</span><span class="sxs-lookup"><span data-stu-id="3afe3-275">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="3afe3-276">如果預檢要求遭到拒絕，應用程式就會傳回 `200 OK` 回應，但不會設定 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-276">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="3afe3-277">因此，瀏覽器不會嘗試跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-277">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="3afe3-278">如需拒絕預檢要求的範例，請參閱本檔的「 [測試 CORS](#testc) 」一節。</span><span class="sxs-lookup"><span data-stu-id="3afe3-278">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="3afe3-279">使用 F12 工具，主控台應用程式會根據瀏覽器顯示類似下列其中一項的錯誤：</span><span class="sxs-lookup"><span data-stu-id="3afe3-279">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="3afe3-280">Firefox：跨原始來源要求遭到封鎖：相同的原始原則不允許在中讀取遠端資源 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-280">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="3afe3-281"> (原因： CORS 要求未成功) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-281">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="3afe3-282">深入了解</span><span class="sxs-lookup"><span data-stu-id="3afe3-282">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="3afe3-283">以 Chromium 為基礎： ' ' 來源 ' ' 的 fetch 存取 https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net 已被 CORS 原則封鎖：預檢要求的回應未通過存取控制檢查：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-283">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="3afe3-284">如果不透明回應適合您的需求，請將要求的模式設定為 'no-cors' 以在停用 CORS 之下擷取資源。</span><span class="sxs-lookup"><span data-stu-id="3afe3-284">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="3afe3-285">若要允許特定標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-285">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="3afe3-286">若要允許所有 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-286">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="3afe3-287">瀏覽器的設定方式不一致 `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-287">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="3afe3-288">如果其中一項：</span><span class="sxs-lookup"><span data-stu-id="3afe3-288">If either:</span></span>

* <span data-ttu-id="3afe3-289">標頭會設定為以外的任何其他 `"*"`</span><span class="sxs-lookup"><span data-stu-id="3afe3-289">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="3afe3-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 呼叫：至少包含 `Accept` 、 `Content-Type` 和 `Origin` ，以及您想要支援的任何自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="3afe3-291">自動預檢要求碼</span><span class="sxs-lookup"><span data-stu-id="3afe3-291">Automatic preflight request code</span></span>

<span data-ttu-id="3afe3-292">套用 CORS 原則的時機為：</span><span class="sxs-lookup"><span data-stu-id="3afe3-292">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="3afe3-293">`app.UseCors`在中呼叫全域 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-293">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="3afe3-294">使用 `[EnableCors]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="3afe3-294">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="3afe3-295">ASP.NET Core 會回應預檢 OPTIONS 要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-295">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="3afe3-296">使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援自動預檢要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-296">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="3afe3-297">本檔的「 [測試 CORS](#testc) 」區段會示範此行為。</span><span class="sxs-lookup"><span data-stu-id="3afe3-297">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="3afe3-298">[HttpOptions] 預檢要求的屬性</span><span class="sxs-lookup"><span data-stu-id="3afe3-298">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="3afe3-299">使用適當的原則啟用 CORS 時，ASP.NET Core 通常會自動回應 CORS 預檢要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-299">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="3afe3-300">在某些情況下，可能不會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="3afe3-300">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="3afe3-301">例如，搭配 [端點路由使用 CORS](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-301">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="3afe3-302">下列程式碼會使用 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 屬性來建立選項要求的端點：</span><span class="sxs-lookup"><span data-stu-id="3afe3-302">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="3afe3-303">請參閱 [使用端點路由測試 CORS 和 [HttpOptions]](#tcer) ，以取得測試上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="3afe3-303">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="3afe3-304">設定預檢到期時間</span><span class="sxs-lookup"><span data-stu-id="3afe3-304">Set the preflight expiration time</span></span>

<span data-ttu-id="3afe3-305">`Access-Control-Max-Age`標頭會指定可以快取預檢要求回應的時間長度。</span><span class="sxs-lookup"><span data-stu-id="3afe3-305">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="3afe3-306">若要設定此標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-306">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="3afe3-307">CORS 的運作方式</span><span class="sxs-lookup"><span data-stu-id="3afe3-307">How CORS works</span></span>

<span data-ttu-id="3afe3-308">本節說明在 HTTP 訊息層級的 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 要求中發生的情況。</span><span class="sxs-lookup"><span data-stu-id="3afe3-308">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="3afe3-309">CORS **不** 是安全性功能。</span><span class="sxs-lookup"><span data-stu-id="3afe3-309">CORS is **not** a security feature.</span></span> <span data-ttu-id="3afe3-310">CORS 是一種 W3C 標準，可讓伺服器放寬相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-310">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="3afe3-311">例如，惡意執行者可以針對您的網站使用 [跨網站腳本 (XSS) ](xref:security/cross-site-scripting) ，並對其已啟用 CORS 的網站執行跨網站要求以竊取資訊。</span><span class="sxs-lookup"><span data-stu-id="3afe3-311">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="3afe3-312">API 不會藉由允許 CORS 而更安全。</span><span class="sxs-lookup"><span data-stu-id="3afe3-312">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="3afe3-313">用戶端 (瀏覽器) 來強制執行 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-313">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="3afe3-314">伺服器會執行要求並傳迴響應，這是傳回錯誤並封鎖回應的用戶端。</span><span class="sxs-lookup"><span data-stu-id="3afe3-314">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="3afe3-315">例如，下列任何一項工具都會顯示伺服器回應：</span><span class="sxs-lookup"><span data-stu-id="3afe3-315">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="3afe3-316">Fiddler</span><span class="sxs-lookup"><span data-stu-id="3afe3-316">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="3afe3-317">Postman</span><span class="sxs-lookup"><span data-stu-id="3afe3-317">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="3afe3-318">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="3afe3-318">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="3afe3-319">在網址列中輸入 URL 的網頁瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="3afe3-319">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="3afe3-320">這是伺服器允許瀏覽器執行跨原始 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 或 [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 要求的方法，否則會被禁止。</span><span class="sxs-lookup"><span data-stu-id="3afe3-320">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="3afe3-321">沒有 CORS 的瀏覽器無法進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-321">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="3afe3-322">在 CORS 之前，會使用 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 來規避這種限制。</span><span class="sxs-lookup"><span data-stu-id="3afe3-322">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="3afe3-323">JSONP 不會使用 XHR，它會使用 `<script>` 標記來接收回應。</span><span class="sxs-lookup"><span data-stu-id="3afe3-323">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="3afe3-324">允許跨原始來源載入腳本。</span><span class="sxs-lookup"><span data-stu-id="3afe3-324">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="3afe3-325">[CORS 規格](https://www.w3.org/TR/cors/)引進了數個新的 HTTP 標頭，可啟用跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-325">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="3afe3-326">如果瀏覽器支援 CORS，則會自動為跨原始來源要求設定這些標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-326">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="3afe3-327">不需要自訂 JavaScript 程式碼來啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-327">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="3afe3-328">已部署[範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)上的[PUT 測試按鈕](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="3afe3-328">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="3afe3-329">以下是從 [ [值](https://cors3.azurewebsites.net/) 測試] 按鈕到的跨原始來源要求範例 `https://cors1.azurewebsites.net/api/values` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-329">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="3afe3-330">`Origin`標頭：</span><span class="sxs-lookup"><span data-stu-id="3afe3-330">The `Origin` header:</span></span>

* <span data-ttu-id="3afe3-331">提供提出要求的網站網域。</span><span class="sxs-lookup"><span data-stu-id="3afe3-331">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="3afe3-332">是必要的，而且必須與主機不同。</span><span class="sxs-lookup"><span data-stu-id="3afe3-332">Is required and must be different from the host.</span></span>

<span data-ttu-id="3afe3-333">**一般標頭**</span><span class="sxs-lookup"><span data-stu-id="3afe3-333">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="3afe3-334">**回應標頭**</span><span class="sxs-lookup"><span data-stu-id="3afe3-334">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="3afe3-335">**要求標頭**</span><span class="sxs-lookup"><span data-stu-id="3afe3-335">**Request headers**</span></span>

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

<span data-ttu-id="3afe3-336">在 `OPTIONS` 要求中，伺服器會在回應中設定 **回應標頭** `Access-Control-Allow-Origin: {allowed origin}` 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-336">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="3afe3-337">例如，已部署的 [範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) 按鈕 `OPTIONS` 要求包含下列標頭：</span><span class="sxs-lookup"><span data-stu-id="3afe3-337">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="3afe3-338">**一般標頭**</span><span class="sxs-lookup"><span data-stu-id="3afe3-338">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="3afe3-339">**回應標頭**</span><span class="sxs-lookup"><span data-stu-id="3afe3-339">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="3afe3-340">**要求標頭**</span><span class="sxs-lookup"><span data-stu-id="3afe3-340">**Request headers**</span></span>

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

<span data-ttu-id="3afe3-341">在先前的 **回應標頭** 中，伺服器會在回應中設定 [存取控制-允許原始](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 的標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-341">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="3afe3-342">`https://cors1.azurewebsites.net`此標頭的值符合 `Origin` 要求中的標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-342">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="3afe3-343">如果 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> 呼叫，則 `Access-Control-Allow-Origin: *` 會傳回萬用字元值。</span><span class="sxs-lookup"><span data-stu-id="3afe3-343">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="3afe3-344">`AllowAnyOrigin` 允許任何來源。</span><span class="sxs-lookup"><span data-stu-id="3afe3-344">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="3afe3-345">如果回應不包含 `Access-Control-Allow-Origin` 標頭，則跨原始來源要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="3afe3-345">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="3afe3-346">具體而言，瀏覽器不允許要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-346">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="3afe3-347">即使伺服器傳回成功的回應，瀏覽器也不會將回應提供給用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="3afe3-347">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="3afe3-348">顯示選項要求</span><span class="sxs-lookup"><span data-stu-id="3afe3-348">Display OPTIONS requests</span></span>

<span data-ttu-id="3afe3-349">根據預設，Chrome 和 Edge 瀏覽器不會在 F12 工具的 [網路] 索引標籤上顯示選項要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-349">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="3afe3-350">若要在這些瀏覽器中顯示選項要求：</span><span class="sxs-lookup"><span data-stu-id="3afe3-350">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="3afe3-351">`chrome://flags/#out-of-blink-cors` 或 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="3afe3-351">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="3afe3-352">停用旗標。</span><span class="sxs-lookup"><span data-stu-id="3afe3-352">disable the flag.</span></span>
* <span data-ttu-id="3afe3-353">重新 啟動。</span><span class="sxs-lookup"><span data-stu-id="3afe3-353">restart.</span></span>

<span data-ttu-id="3afe3-354">Firefox 預設會顯示選項要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-354">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="3afe3-355">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-355">CORS in IIS</span></span>

<span data-ttu-id="3afe3-356">部署至 IIS 時，如果伺服器未設定為允許匿名存取，CORS 必須在 Windows 驗證之前執行。</span><span class="sxs-lookup"><span data-stu-id="3afe3-356">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="3afe3-357">若要支援此案例，必須為應用程式安裝及設定 [IIS CORS 模組](https://www.iis.net/downloads/microsoft/iis-cors-module) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-357">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="3afe3-358">測試 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-358">Test CORS</span></span>

<span data-ttu-id="3afe3-359">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)具有測試 CORS 的程式碼。</span><span class="sxs-lookup"><span data-stu-id="3afe3-359">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="3afe3-360">請參閱[如何下載](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-360">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="3afe3-361">範例是已新增頁面的 API 專案 Razor ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-361">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="3afe3-362">`WithOrigins("https://localhost:<port>");` 只能用來測試範例應用程式，類似于 [下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-362">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="3afe3-363">以下 `ValuesController` 提供測試的端點：</span><span class="sxs-lookup"><span data-stu-id="3afe3-363">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="3afe3-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) 由 [Rick.DocRouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 套件提供，並顯示路由資訊。</span><span class="sxs-lookup"><span data-stu-id="3afe3-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="3afe3-365">使用下列其中一種方法來測試上述的範例程式碼：</span><span class="sxs-lookup"><span data-stu-id="3afe3-365">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="3afe3-366">使用已部署的範例應用程式 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-366">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="3afe3-367">不需要下載範例。</span><span class="sxs-lookup"><span data-stu-id="3afe3-367">There is no need to download the sample.</span></span>
* <span data-ttu-id="3afe3-368">`dotnet run`使用的預設 URL 執行範例 `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-368">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="3afe3-369">從 Visual Studio 執行範例，並將埠設為44398，以取得的 URL `https://localhost:44398` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-369">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="3afe3-370">使用瀏覽器搭配 F12 工具：</span><span class="sxs-lookup"><span data-stu-id="3afe3-370">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="3afe3-371">選取 [ **值** ] 按鈕，並檢查 [ **網路** ] 索引標籤中的標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-371">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="3afe3-372">選取 [ **PUT 測試** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="3afe3-372">Select the **PUT test** button.</span></span> <span data-ttu-id="3afe3-373">如需顯示選項要求的指示，請參閱 [顯示選項要求](#options) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-373">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="3afe3-374">**PUT 測試** 會建立兩個要求，也就是選項預檢要求和 PUT 要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-374">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="3afe3-375">選取 **`GetValues2 [DisableCors]`** 按鈕以觸發失敗的 CORS 要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-375">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="3afe3-376">如檔中所述，回應會傳回200成功，但不會發出 CORS 要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-376">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="3afe3-377">選取 [ **主控台** ] 索引標籤，以查看 CORS 錯誤。</span><span class="sxs-lookup"><span data-stu-id="3afe3-377">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="3afe3-378">視瀏覽器而定，會顯示類似下列的錯誤：</span><span class="sxs-lookup"><span data-stu-id="3afe3-378">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="3afe3-379">`'https://cors1.azurewebsites.net/api/values/GetValues2'`CORS 原則已封鎖從來源提取的存取權 `'https://cors3.azurewebsites.net'` ：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-379">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="3afe3-380">如果不透明回應適合您的需求，請將要求的模式設定為 'no-cors' 以在停用 CORS 之下擷取資源。</span><span class="sxs-lookup"><span data-stu-id="3afe3-380">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="3afe3-381">啟用 CORS 的端點可以使用工具（例如 [捲曲](https://curl.haxx.se/)、 [Fiddler](https://www.telerik.com/fiddler)或 [Postman](https://www.getpostman.com/)）進行測試。</span><span class="sxs-lookup"><span data-stu-id="3afe3-381">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="3afe3-382">使用工具時，標頭所指定的要求來源必須與 `Origin` 接收要求的主機不同。</span><span class="sxs-lookup"><span data-stu-id="3afe3-382">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="3afe3-383">如果 *要求不是* 以標頭的值為基礎 `Origin` ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-383">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="3afe3-384">CORS 中介軟體不需要處理要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-384">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="3afe3-385">回應中不會傳回 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-385">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="3afe3-386">下列命令會使用 `curl` 來發出具有下列資訊的選項要求：</span><span class="sxs-lookup"><span data-stu-id="3afe3-386">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="3afe3-387">使用端點路由和 [HttpOptions] 測試 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-387">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="3afe3-388">使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援 [自動預檢要求](#apf)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-388">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="3afe3-389">請考慮使用 [端點路由來啟用 CORS](#ecors)的下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="3afe3-389">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="3afe3-390">以下 `TodoItems1Controller` 提供測試的端點：</span><span class="sxs-lookup"><span data-stu-id="3afe3-390">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="3afe3-391">從已部署[範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)的[測試頁面](https://cors1.azurewebsites.net/test?number=1)測試上述程式碼。</span><span class="sxs-lookup"><span data-stu-id="3afe3-391">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="3afe3-392">**Delete [EnableCors]** 和 **GET [EnableCors]** 按鈕會成功，因為端點具有 `[EnableCors]` 並回應預檢要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-392">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="3afe3-393">其他端點失敗。</span><span class="sxs-lookup"><span data-stu-id="3afe3-393">The other endpoints fails.</span></span> <span data-ttu-id="3afe3-394">**GET** 按鈕失敗，因為 [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)會傳送：</span><span class="sxs-lookup"><span data-stu-id="3afe3-394">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="3afe3-395">以下 `TodoItems2Controller` 提供類似的端點，但包含可回應 OPTIONS 要求的明確程式碼：</span><span class="sxs-lookup"><span data-stu-id="3afe3-395">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="3afe3-396">從已部署範例的 [測試頁面](https://cors1.azurewebsites.net/test?number=2) 測試上述程式碼。</span><span class="sxs-lookup"><span data-stu-id="3afe3-396">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="3afe3-397">在 [ **控制器** ] 下拉式清單中，選取 [ **預檢** ]，然後 **設定 [控制器**]。</span><span class="sxs-lookup"><span data-stu-id="3afe3-397">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="3afe3-398">端點的所有 CORS 呼叫都會 `TodoItems2Controller` 成功。</span><span class="sxs-lookup"><span data-stu-id="3afe3-398">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3afe3-399">其他資源</span><span class="sxs-lookup"><span data-stu-id="3afe3-399">Additional resources</span></span>

* [<span data-ttu-id="3afe3-400">跨原始資源分享 (CORS) </span><span class="sxs-lookup"><span data-stu-id="3afe3-400">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="3afe3-401">開始使用 IIS CORS 模組</span><span class="sxs-lookup"><span data-stu-id="3afe3-401">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3afe3-402">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3afe3-402">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3afe3-403">本文說明如何在 ASP.NET Core 應用程式中啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-403">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="3afe3-404">瀏覽器安全性可防止網頁提出要求，而不是與提供網頁的不同網域。</span><span class="sxs-lookup"><span data-stu-id="3afe3-404">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="3afe3-405">這項限制稱為 *相同原始來源原則*。</span><span class="sxs-lookup"><span data-stu-id="3afe3-405">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="3afe3-406">相同來源原則可防止惡意網站從另一個網站讀取敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="3afe3-406">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="3afe3-407">有時，您可能會想要允許其他網站對您的應用程式進行跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-407">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="3afe3-408">如需詳細資訊，請參閱 [MOZILLA CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-408">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="3afe3-409">[跨原始資源分享](https://www.w3.org/TR/cors/) (CORS) ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-409">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="3afe3-410">是一種 W3C 標準，可讓伺服器放寬相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-410">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="3afe3-411">不 **是安全性** 功能，CORS 放寬安全性。</span><span class="sxs-lookup"><span data-stu-id="3afe3-411">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="3afe3-412">API 不會藉由允許 CORS 來更安全。</span><span class="sxs-lookup"><span data-stu-id="3afe3-412">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="3afe3-413">如需詳細資訊，請參閱 [CORS 的運作方式](#how-cors)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-413">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="3afe3-414">允許伺服器明確允許某些跨原始來源的要求，同時拒絕其他要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-414">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="3afe3-415">比先前的技術（例如 [JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更有彈性。</span><span class="sxs-lookup"><span data-stu-id="3afe3-415">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="3afe3-416">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3afe3-416">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="3afe3-417">相同的來源</span><span class="sxs-lookup"><span data-stu-id="3afe3-417">Same origin</span></span>

<span data-ttu-id="3afe3-418">如果兩個 Url 具有相同的配置、主機和埠 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，其來源相同。</span><span class="sxs-lookup"><span data-stu-id="3afe3-418">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="3afe3-419">這兩個 Url 的來源相同：</span><span class="sxs-lookup"><span data-stu-id="3afe3-419">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="3afe3-420">這些 Url 的來源與前兩個 Url 不同：</span><span class="sxs-lookup"><span data-stu-id="3afe3-420">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="3afe3-421">`https://example.net`：不同的網域</span><span class="sxs-lookup"><span data-stu-id="3afe3-421">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="3afe3-422">`https://www.example.com/foo.html`：不同的子域</span><span class="sxs-lookup"><span data-stu-id="3afe3-422">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="3afe3-423">`http://example.com/foo.html`：不同的配置</span><span class="sxs-lookup"><span data-stu-id="3afe3-423">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="3afe3-424">`https://example.com:9000/foo.html`：不同的埠</span><span class="sxs-lookup"><span data-stu-id="3afe3-424">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="3afe3-425">比較來源時，Internet Explorer 不會考慮該埠。</span><span class="sxs-lookup"><span data-stu-id="3afe3-425">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="3afe3-426">具有命名原則和中介軟體的 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-426">CORS with named policy and middleware</span></span>

<span data-ttu-id="3afe3-427">CORS 中介軟體會處理跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-427">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="3afe3-428">下列程式碼會針對具有指定來源的整個應用程式啟用 CORS：</span><span class="sxs-lookup"><span data-stu-id="3afe3-428">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="3afe3-429">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="3afe3-429">The preceding code:</span></span>

* <span data-ttu-id="3afe3-430">將原則名稱設定為 " \_ myAllowSpecificOrigins"。</span><span class="sxs-lookup"><span data-stu-id="3afe3-430">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="3afe3-431">原則名稱是任意的。</span><span class="sxs-lookup"><span data-stu-id="3afe3-431">The policy name is arbitrary.</span></span>
* <span data-ttu-id="3afe3-432">呼叫 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 擴充方法，以啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-432">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="3afe3-433"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的呼叫。</span><span class="sxs-lookup"><span data-stu-id="3afe3-433">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="3afe3-434">Lambda 接受 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 物件。</span><span class="sxs-lookup"><span data-stu-id="3afe3-434">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="3afe3-435">設定[選項](#cors-policy-options)（例如 `WithOrigins` ）將在本文稍後說明。</span><span class="sxs-lookup"><span data-stu-id="3afe3-435">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="3afe3-436"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法呼叫會將 CORS 服務新增至應用程式的服務容器：</span><span class="sxs-lookup"><span data-stu-id="3afe3-436">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="3afe3-437">如需詳細資訊，請參閱本檔中的 [CORS 原則選項](#cpo) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-437">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="3afe3-438"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以鏈方法，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="3afe3-438">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="3afe3-439">注意： URL **不** 能包含尾端斜線 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-439">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="3afe3-440">如果 URL 以終止 `/` ，則比較會 `false` 傳回，而且不會傳回任何標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-440">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="3afe3-441">下列程式碼會透過 CORS 中介軟體將 CORS 原則套用至所有應用程式端點：</span><span class="sxs-lookup"><span data-stu-id="3afe3-441">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="3afe3-442">注意： `UseCors` 必須先呼叫 `UseMvc` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-442">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="3afe3-443">請參閱在頁面 [ Razor 、控制器和動作方法中啟用 cors](#ecors) ，以在頁面/控制器/動作層級套用 cors 原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-443">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="3afe3-444">請參閱 [測試 CORS](#test) ，以取得測試程式碼類似上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="3afe3-444">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="3afe3-445">使用屬性來啟用 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-445">Enable CORS with attributes</span></span>

<span data-ttu-id="3afe3-446">[ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)屬性提供全域套用 CORS 的替代方案。</span><span class="sxs-lookup"><span data-stu-id="3afe3-446">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="3afe3-447">`[EnableCors]`屬性會針對選取的端點啟用 CORS，而不是所有端點。</span><span class="sxs-lookup"><span data-stu-id="3afe3-447">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="3afe3-448">使用 `[EnableCors]` 指定預設原則，並 `[EnableCors("{Policy String}")]` 指定原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-448">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="3afe3-449">`[EnableCors]`屬性可套用至：</span><span class="sxs-lookup"><span data-stu-id="3afe3-449">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="3afe3-450">Razor 網頁 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="3afe3-450">Razor Page `PageModel`</span></span>
* <span data-ttu-id="3afe3-451">控制器</span><span class="sxs-lookup"><span data-stu-id="3afe3-451">Controller</span></span>
* <span data-ttu-id="3afe3-452">控制器動作方法</span><span class="sxs-lookup"><span data-stu-id="3afe3-452">Controller action method</span></span>

<span data-ttu-id="3afe3-453">您可以使用屬性，將不同的原則套用至控制器/頁面模型/動作  `[EnableCors]` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-453">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="3afe3-454">當 `[EnableCors]` 屬性套用至控制器/頁面模型/動作方法，且在中介軟體中啟用 CORS 時，會套用 **這兩個** 原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-454">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="3afe3-455">建議 **不要** 結合原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-455">We recommend **not** combining policies.</span></span> <span data-ttu-id="3afe3-456">使用 `[EnableCors]` 屬性或中介軟體， **而非兩者**。</span><span class="sxs-lookup"><span data-stu-id="3afe3-456">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="3afe3-457">使用時 `[EnableCors]` ，請勿定義預設原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-457">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="3afe3-458">下列程式碼會將不同的原則套用至每個方法：</span><span class="sxs-lookup"><span data-stu-id="3afe3-458">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="3afe3-459">下列程式碼會建立 CORS 預設原則和名為的原則 `"AnotherPolicy"` ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-459">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="3afe3-460">停用 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-460">Disable CORS</span></span>

<span data-ttu-id="3afe3-461">[ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)屬性會停用控制器/頁面模型/動作的 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-461">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="3afe3-462">CORS 原則選項</span><span class="sxs-lookup"><span data-stu-id="3afe3-462">CORS policy options</span></span>

<span data-ttu-id="3afe3-463">本節說明可在 CORS 原則中設定的各種選項：</span><span class="sxs-lookup"><span data-stu-id="3afe3-463">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="3afe3-464">設定允許的來源</span><span class="sxs-lookup"><span data-stu-id="3afe3-464">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="3afe3-465">設定允許的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="3afe3-465">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="3afe3-466">設定允許的要求標頭</span><span class="sxs-lookup"><span data-stu-id="3afe3-466">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="3afe3-467">設定公開的回應標頭</span><span class="sxs-lookup"><span data-stu-id="3afe3-467">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="3afe3-468">跨原始來源要求中的認證</span><span class="sxs-lookup"><span data-stu-id="3afe3-468">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="3afe3-469">設定預檢到期時間</span><span class="sxs-lookup"><span data-stu-id="3afe3-469">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="3afe3-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3afe3-471">對於某些選項而言，先閱讀「 [CORS 的運作方式](#how-cors) 」一節可能很有説明。</span><span class="sxs-lookup"><span data-stu-id="3afe3-471">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="3afe3-472">設定允許的來源</span><span class="sxs-lookup"><span data-stu-id="3afe3-472">Set the allowed origins</span></span>

<span data-ttu-id="3afe3-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允許所有來源中具有任何配置 (或) 的 CORS 要求 `http` `https` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="3afe3-474">`AllowAnyOrigin` 不安全，因為 *任何網站* 都可以對應用程式進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-474">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3afe3-475">指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的設定，而且可能會導致跨網站要求偽造。</span><span class="sxs-lookup"><span data-stu-id="3afe3-475">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="3afe3-476">針對安全的應用程式，如果用戶端必須授權自己存取伺服器資源，請指定確切的來源清單。</span><span class="sxs-lookup"><span data-stu-id="3afe3-476">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="3afe3-477">`AllowAnyOrigin` 影響預檢要求和 `Access-Control-Allow-Origin` 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-477">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="3afe3-478">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="3afe3-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3afe3-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：將原則的屬性設定為函式 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> ，以在評估是否允許原始來源時，允許原始來源符合已設定的萬用字元網域。</span><span class="sxs-lookup"><span data-stu-id="3afe3-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="3afe3-480">設定允許的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="3afe3-480">Set the allowed HTTP methods</span></span>

<span data-ttu-id="3afe3-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="3afe3-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="3afe3-482">允許任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="3afe3-482">Allows any HTTP method:</span></span>
* <span data-ttu-id="3afe3-483">影響預檢要求和 `Access-Control-Allow-Methods` 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-483">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="3afe3-484">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="3afe3-484">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="3afe3-485">設定允許的要求標頭</span><span class="sxs-lookup"><span data-stu-id="3afe3-485">Set the allowed request headers</span></span>

<span data-ttu-id="3afe3-486">若要允許在 CORS 要求中傳送特定標頭（稱為「 *作者要求標頭*」），請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 並指定允許的標頭：</span><span class="sxs-lookup"><span data-stu-id="3afe3-486">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="3afe3-487">若要允許所有作者要求標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-487">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="3afe3-488">此設定會影響預檢要求和 `Access-Control-Request-Headers` 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-488">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="3afe3-489">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="3afe3-489">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3afe3-490">CORS 中介軟體一律允許傳送中的四個標頭， `Access-Control-Request-Headers` 不論 c 中設定的值為何。</span><span class="sxs-lookup"><span data-stu-id="3afe3-490">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="3afe3-491">此標頭清單包括：</span><span class="sxs-lookup"><span data-stu-id="3afe3-491">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="3afe3-492">例如，假設應用程式設定如下：</span><span class="sxs-lookup"><span data-stu-id="3afe3-492">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="3afe3-493">CORS 中介軟體會使用下列要求標頭成功回應預檢要求，因為 `Content-Language` 一律允許：</span><span class="sxs-lookup"><span data-stu-id="3afe3-493">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="3afe3-494">設定公開的回應標頭</span><span class="sxs-lookup"><span data-stu-id="3afe3-494">Set the exposed response headers</span></span>

<span data-ttu-id="3afe3-495">根據預設，瀏覽器不會將所有回應標頭公開給應用程式。</span><span class="sxs-lookup"><span data-stu-id="3afe3-495">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="3afe3-496">如需詳細資訊，請參閱 [W3C 跨原始資源分享 (術語) ：簡單的回應標頭](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-496">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="3afe3-497">預設可用的回應標頭為：</span><span class="sxs-lookup"><span data-stu-id="3afe3-497">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="3afe3-498">CORS 規格會呼叫這些標頭 *簡單的回應標頭*。</span><span class="sxs-lookup"><span data-stu-id="3afe3-498">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="3afe3-499">若要讓應用程式使用其他標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-499">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="3afe3-500">跨原始來源要求中的認證</span><span class="sxs-lookup"><span data-stu-id="3afe3-500">Credentials in cross-origin requests</span></span>

<span data-ttu-id="3afe3-501">認證需要 CORS 要求中的特殊處理。</span><span class="sxs-lookup"><span data-stu-id="3afe3-501">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="3afe3-502">根據預設，瀏覽器不會傳送具有跨原始來源要求的認證。</span><span class="sxs-lookup"><span data-stu-id="3afe3-502">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="3afe3-503">認證包括 cookie s 和 HTTP 驗證配置。</span><span class="sxs-lookup"><span data-stu-id="3afe3-503">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="3afe3-504">若要傳送具有跨原始來源要求的認證，用戶端必須設定 `XMLHttpRequest.withCredentials` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-504">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="3afe3-505">`XMLHttpRequest`直接使用：</span><span class="sxs-lookup"><span data-stu-id="3afe3-505">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="3afe3-506">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="3afe3-506">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="3afe3-507">使用 [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="3afe3-507">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="3afe3-508">伺服器必須允許認證。</span><span class="sxs-lookup"><span data-stu-id="3afe3-508">The server must allow the credentials.</span></span> <span data-ttu-id="3afe3-509">若要允許跨原始來源認證，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-509">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="3afe3-510">HTTP 回應包含 `Access-Control-Allow-Credentials` 標頭，它會告知瀏覽器伺服器允許跨原始來源要求的認證。</span><span class="sxs-lookup"><span data-stu-id="3afe3-510">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="3afe3-511">如果瀏覽器傳送認證但回應不包含有效的 `Access-Control-Allow-Credentials` 標頭，則瀏覽器不會公開應用程式的回應，而且跨原始來源要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="3afe3-511">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="3afe3-512">允許跨原始來源的認證有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="3afe3-512">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="3afe3-513">另一個網域的網站可以代表使用者將已登入使用者的認證傳送給應用程式，而不需要使用者的知識。</span><span class="sxs-lookup"><span data-stu-id="3afe3-513">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="3afe3-514">CORS 規格也指出 `"*"` 如果標頭存在，設定來源 (所有來源) 無效 `Access-Control-Allow-Credentials` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-514">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="3afe3-515">預檢要求</span><span class="sxs-lookup"><span data-stu-id="3afe3-515">Preflight requests</span></span>

<span data-ttu-id="3afe3-516">針對某些 CORS 要求，瀏覽器會先傳送額外的要求，再進行實際要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-516">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="3afe3-517">此要求稱為 *預檢要求*。</span><span class="sxs-lookup"><span data-stu-id="3afe3-517">This request is called a *preflight request*.</span></span> <span data-ttu-id="3afe3-518">如果下列條件成立，瀏覽器可以略過預檢要求：</span><span class="sxs-lookup"><span data-stu-id="3afe3-518">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="3afe3-519">要求方法為 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="3afe3-519">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="3afe3-520">應用程式不會設定 `Accept` 、、 `Accept-Language` `Content-Language` 、 `Content-Type` 或以外的要求標頭 `Last-Event-ID` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-520">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="3afe3-521">`Content-Type`如果設定，標頭會有下列其中一個值：</span><span class="sxs-lookup"><span data-stu-id="3afe3-521">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="3afe3-522">針對用戶端要求所設定之要求標頭的規則，會套用至應用程式透過在物件上呼叫所設定的標頭 `setRequestHeader` `XMLHttpRequest` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-522">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="3afe3-523">CORS 規格會呼叫這些標頭的 *作者要求標頭*。</span><span class="sxs-lookup"><span data-stu-id="3afe3-523">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="3afe3-524">此規則不適用於瀏覽器可以設定的標頭，例如 `User-Agent` 、 `Host` 或 `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-524">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="3afe3-525">以下是預檢要求的範例：</span><span class="sxs-lookup"><span data-stu-id="3afe3-525">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="3afe3-526">進入飛行前要求會使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="3afe3-526">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="3afe3-527">它包含兩個特殊標頭：</span><span class="sxs-lookup"><span data-stu-id="3afe3-527">It includes two special headers:</span></span>

* <span data-ttu-id="3afe3-528">`Access-Control-Request-Method`：將用於實際要求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="3afe3-528">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="3afe3-529">`Access-Control-Request-Headers`：應用程式在實際要求上設定的要求標頭清單。</span><span class="sxs-lookup"><span data-stu-id="3afe3-529">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="3afe3-530">如先前所述，這並不包括瀏覽器所設定的標頭，例如 `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-530">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="3afe3-531">使用適當的原則啟用 CORS 時，ASP.NET Core 通常會自動回應 CORS 預檢要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-531">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="3afe3-532">請參閱 [預檢要求的 [HttpOptions] 屬性](#pro)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-532">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="3afe3-533">CORS 預檢要求可能包含 `Access-Control-Request-Headers` 標頭，這會向伺服器指出與實際要求一起傳送的標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-533">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="3afe3-534">若要允許特定標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-534">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="3afe3-535">若要允許所有作者要求標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-535">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="3afe3-536">瀏覽器的設定方式並不完全一致 `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-536">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="3afe3-537">如果您將標頭設定為 `"*"` (或使用) 以外的任何 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 一個，您應該至少包含 `Accept` 、 `Content-Type` 和 `Origin` ，以及您想要支援的任何自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-537">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="3afe3-538">以下是預檢要求的範例回應， (假設伺服器允許要求) ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-538">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="3afe3-539">回應包含的 `Access-Control-Allow-Methods` 標頭會列出允許的方法以及選擇性的 `Access-Control-Allow-Headers` 標頭，以列出允許的標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-539">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="3afe3-540">如果預檢要求成功，則瀏覽器會傳送實際的要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-540">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="3afe3-541">如果預檢要求遭到拒絕，應用程式會傳回 *200 OK* 回應，但不會傳送 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-541">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="3afe3-542">因此，瀏覽器不會嘗試跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-542">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="3afe3-543">設定預檢到期時間</span><span class="sxs-lookup"><span data-stu-id="3afe3-543">Set the preflight expiration time</span></span>

<span data-ttu-id="3afe3-544">`Access-Control-Max-Age`標頭會指定可以快取預檢要求回應的時間長度。</span><span class="sxs-lookup"><span data-stu-id="3afe3-544">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="3afe3-545">若要設定此標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-545">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="3afe3-546">CORS 的運作方式</span><span class="sxs-lookup"><span data-stu-id="3afe3-546">How CORS works</span></span>

<span data-ttu-id="3afe3-547">本節說明在 HTTP 訊息層級的 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 要求中發生的情況。</span><span class="sxs-lookup"><span data-stu-id="3afe3-547">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="3afe3-548">CORS **不** 是安全性功能。</span><span class="sxs-lookup"><span data-stu-id="3afe3-548">CORS is **not** a security feature.</span></span> <span data-ttu-id="3afe3-549">CORS 是一種 W3C 標準，可讓伺服器放寬相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="3afe3-549">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="3afe3-550">例如，惡意的動作專案可能會針對您的網站使用 [防止跨網站腳本 (XSS) ](xref:security/cross-site-scripting) ，並對其已啟用 CORS 的網站執行跨網站要求以竊取資訊。</span><span class="sxs-lookup"><span data-stu-id="3afe3-550">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="3afe3-551">藉由允許 CORS，您的 API 不會更安全。</span><span class="sxs-lookup"><span data-stu-id="3afe3-551">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="3afe3-552">用戶端 (瀏覽器) 來強制執行 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-552">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="3afe3-553">伺服器會執行要求並傳迴響應，這是傳回錯誤並封鎖回應的用戶端。</span><span class="sxs-lookup"><span data-stu-id="3afe3-553">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="3afe3-554">例如，下列任何一項工具都會顯示伺服器回應：</span><span class="sxs-lookup"><span data-stu-id="3afe3-554">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="3afe3-555">Fiddler</span><span class="sxs-lookup"><span data-stu-id="3afe3-555">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="3afe3-556">Postman</span><span class="sxs-lookup"><span data-stu-id="3afe3-556">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="3afe3-557">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="3afe3-557">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="3afe3-558">在網址列中輸入 URL 的網頁瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="3afe3-558">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="3afe3-559">這是伺服器允許瀏覽器執行跨原始 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 或 [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 要求的方法，否則會被禁止。</span><span class="sxs-lookup"><span data-stu-id="3afe3-559">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="3afe3-560">沒有 CORS 的瀏覽器 () 無法進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-560">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="3afe3-561">在 CORS 之前，會使用 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 來規避這種限制。</span><span class="sxs-lookup"><span data-stu-id="3afe3-561">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="3afe3-562">JSONP 不會使用 XHR，它會使用 `<script>` 標記來接收回應。</span><span class="sxs-lookup"><span data-stu-id="3afe3-562">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="3afe3-563">允許跨原始來源載入腳本。</span><span class="sxs-lookup"><span data-stu-id="3afe3-563">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="3afe3-564">[CORS 規格](https://www.w3.org/TR/cors/)引進了數個新的 HTTP 標頭，可啟用跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-564">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="3afe3-565">如果瀏覽器支援 CORS，則會自動為跨原始來源要求設定這些標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-565">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="3afe3-566">不需要自訂 JavaScript 程式碼來啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-566">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="3afe3-567">以下是跨原始來源要求的範例。</span><span class="sxs-lookup"><span data-stu-id="3afe3-567">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="3afe3-568">`Origin`標頭會提供提出要求的網站網域。</span><span class="sxs-lookup"><span data-stu-id="3afe3-568">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="3afe3-569">`Origin`標頭是必要的，而且必須與主機不同。</span><span class="sxs-lookup"><span data-stu-id="3afe3-569">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="3afe3-570">如果伺服器允許此要求，則會 `Access-Control-Allow-Origin` 在回應中設定標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-570">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="3afe3-571">此標頭的值 `Origin` 會符合要求中的標頭，或是萬用字元值 `"*"` ，表示允許任何來源：</span><span class="sxs-lookup"><span data-stu-id="3afe3-571">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="3afe3-572">如果回應不包含 `Access-Control-Allow-Origin` 標頭，則跨原始來源要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="3afe3-572">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="3afe3-573">具體而言，瀏覽器不允許要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-573">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="3afe3-574">即使伺服器傳回成功的回應，瀏覽器也不會將回應提供給用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="3afe3-574">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="3afe3-575">測試 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-575">Test CORS</span></span>

<span data-ttu-id="3afe3-576">若要測試 CORS：</span><span class="sxs-lookup"><span data-stu-id="3afe3-576">To test CORS:</span></span>

1. <span data-ttu-id="3afe3-577">[建立 API 專案](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-577">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="3afe3-578">或者，您也可以 [下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-578">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="3afe3-579">請使用本檔中的其中一種方法來啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3afe3-579">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="3afe3-580">例如：</span><span class="sxs-lookup"><span data-stu-id="3afe3-580">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="3afe3-581">`WithOrigins("https://localhost:<port>");` 只能用來測試範例應用程式，類似于 [下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-581">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="3afe3-582">建立 web 應用程式專案 (Razor 頁面或 MVC) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-582">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="3afe3-583">此範例會使用 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="3afe3-583">The sample uses Razor Pages.</span></span> <span data-ttu-id="3afe3-584">您可以在與 API 專案相同的解決方案中建立 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="3afe3-584">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="3afe3-585">將下列反白顯示的程式碼加入至 *索引的 cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="3afe3-585">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="3afe3-586">在上述程式碼中，將取代為已 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 部署應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="3afe3-586">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="3afe3-587">部署 API 專案。</span><span class="sxs-lookup"><span data-stu-id="3afe3-587">Deploy the API project.</span></span> <span data-ttu-id="3afe3-588">例如， [部署至 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="3afe3-588">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="3afe3-589">Razor從桌面上執行頁面或 MVC 應用程式，然後按一下 [**測試**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="3afe3-589">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="3afe3-590">使用 F12 工具來檢查錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="3afe3-590">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="3afe3-591">從移除 localhost 來源 `WithOrigins` ，並部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="3afe3-591">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="3afe3-592">或者，使用不同的埠執行用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="3afe3-592">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="3afe3-593">例如，從 Visual Studio 執行。</span><span class="sxs-lookup"><span data-stu-id="3afe3-593">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="3afe3-594">使用用戶端應用程式進行測試。</span><span class="sxs-lookup"><span data-stu-id="3afe3-594">Test with the client app.</span></span> <span data-ttu-id="3afe3-595">CORS 失敗會傳回錯誤，但無法使用 JavaScript 的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="3afe3-595">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="3afe3-596">使用 F12 工具中的 [主控台] 索引標籤，以查看錯誤。</span><span class="sxs-lookup"><span data-stu-id="3afe3-596">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="3afe3-597">視瀏覽器而定，您會在 F12 工具主控台中收到 (的錯誤) 如下所示：</span><span class="sxs-lookup"><span data-stu-id="3afe3-597">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="3afe3-598">使用 Microsoft Edge：</span><span class="sxs-lookup"><span data-stu-id="3afe3-598">Using Microsoft Edge:</span></span>

     <span data-ttu-id="3afe3-599">**SEC7120： [CORS] 在 `https://localhost:44375` `https://localhost:44375` 的跨來源資源的存取控制-允許來源回應標頭中找不到來源 `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="3afe3-599">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="3afe3-600">使用 Chrome：</span><span class="sxs-lookup"><span data-stu-id="3afe3-600">Using Chrome:</span></span>

     <span data-ttu-id="3afe3-601">**`https://webapi.azurewebsites.net/api/values/1`CORS 原則已封鎖從來源對 XMLHttpRequest 的存取 `https://localhost:44375` ：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。**</span><span class="sxs-lookup"><span data-stu-id="3afe3-601">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="3afe3-602">啟用 CORS 的端點可以使用工具（例如 [Fiddler](https://www.telerik.com/fiddler) 或 [Postman](https://www.getpostman.com/)）進行測試。</span><span class="sxs-lookup"><span data-stu-id="3afe3-602">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="3afe3-603">使用工具時，標頭所指定的要求來源必須與 `Origin` 接收要求的主機不同。</span><span class="sxs-lookup"><span data-stu-id="3afe3-603">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="3afe3-604">如果 *要求不是* 以標頭的值為基礎 `Origin` ：</span><span class="sxs-lookup"><span data-stu-id="3afe3-604">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="3afe3-605">CORS 中介軟體不需要處理要求。</span><span class="sxs-lookup"><span data-stu-id="3afe3-605">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="3afe3-606">回應中不會傳回 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="3afe3-606">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="3afe3-607">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="3afe3-607">CORS in IIS</span></span>

<span data-ttu-id="3afe3-608">部署至 IIS 時，如果伺服器未設定為允許匿名存取，CORS 必須在 Windows 驗證之前執行。</span><span class="sxs-lookup"><span data-stu-id="3afe3-608">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="3afe3-609">若要支援此案例，必須為應用程式安裝及設定 [IIS CORS 模組](https://www.iis.net/downloads/microsoft/iis-cors-module) 。</span><span class="sxs-lookup"><span data-stu-id="3afe3-609">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3afe3-610">其他資源</span><span class="sxs-lookup"><span data-stu-id="3afe3-610">Additional resources</span></span>

* [<span data-ttu-id="3afe3-611">跨原始資源分享 (CORS) </span><span class="sxs-lookup"><span data-stu-id="3afe3-611">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="3afe3-612">開始使用 IIS CORS 模組</span><span class="sxs-lookup"><span data-stu-id="3afe3-612">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
