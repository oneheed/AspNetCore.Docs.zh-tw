---
title: '在 ASP.NET Core 中啟用跨原始來源要求 (CORS) '
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 應用程式中允許或拒絕跨原始來源要求的標準。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: cebaa9ae65557ca5d938c5728882382830deca9d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629258"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="6d4c4-103">在 ASP.NET Core 中啟用跨原始來源要求 (CORS) </span><span class="sxs-lookup"><span data-stu-id="6d4c4-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6d4c4-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="6d4c4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="6d4c4-105">本文說明如何在 ASP.NET Core 應用程式中啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="6d4c4-106">瀏覽器安全性可防止網頁提出要求，而不是與提供網頁的不同網域。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="6d4c4-107">這項限制稱為 *相同原始來源原則*。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="6d4c4-108">相同來源原則可防止惡意網站從另一個網站讀取敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="6d4c4-109">有時，您可能會想要允許其他網站對您的應用程式進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="6d4c4-110">如需詳細資訊，請參閱 [MOZILLA CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="6d4c4-111">[跨原始資源分享](https://www.w3.org/TR/cors/) (CORS) ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="6d4c4-112">是一種 W3C 標準，可讓伺服器放寬相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="6d4c4-113">不 **是安全性** 功能，CORS 放寬安全性。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="6d4c4-114">API 不會藉由允許 CORS 來更安全。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="6d4c4-115">如需詳細資訊，請參閱 [CORS 的運作方式](#how-cors)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="6d4c4-116">允許伺服器明確允許某些跨原始來源的要求，同時拒絕其他要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="6d4c4-117">比先前的技術（例如 [JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更有彈性。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="6d4c4-118">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="6d4c4-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="6d4c4-119">相同的來源</span><span class="sxs-lookup"><span data-stu-id="6d4c4-119">Same origin</span></span>

<span data-ttu-id="6d4c4-120">如果兩個 Url 具有相同的配置、主機和埠 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，其來源相同。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="6d4c4-121">這兩個 Url 的來源相同：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="6d4c4-122">這些 Url 的來源與前兩個 Url 不同：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="6d4c4-123">`https://example.net`：不同的網域</span><span class="sxs-lookup"><span data-stu-id="6d4c4-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="6d4c4-124">`https://www.example.com/foo.html`：不同的子域</span><span class="sxs-lookup"><span data-stu-id="6d4c4-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="6d4c4-125">`http://example.com/foo.html`：不同的配置</span><span class="sxs-lookup"><span data-stu-id="6d4c4-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="6d4c4-126">`https://example.com:9000/foo.html`：不同的埠</span><span class="sxs-lookup"><span data-stu-id="6d4c4-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="6d4c4-127">啟用 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-127">Enable CORS</span></span>

<span data-ttu-id="6d4c4-128">有三種方式可以啟用 CORS：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="6d4c4-129">在中介軟體中使用 [命名原則](#np) 或 [預設原則](#dp)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="6d4c4-130">使用 [端點路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="6d4c4-131">使用 [[EnableCors]](#attr) 屬性。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="6d4c4-132">使用具有命名原則的 [[EnableCors]](#attr) 屬性，可提供最精細的控制項來限制支援 CORS 的端點。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="6d4c4-133"><xref:Owin.CorsExtensions.UseCors%2A> 使用時，必須先呼叫 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> `UseResponseCaching` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="6d4c4-134">下列各節將詳細說明每種方法。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="6d4c4-135">具有命名原則和中介軟體的 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="6d4c4-136">CORS 中介軟體會處理跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="6d4c4-137">下列程式碼會將 CORS 原則套用至具有指定來源的所有應用程式端點：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="6d4c4-138">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-138">The preceding code:</span></span>

* <span data-ttu-id="6d4c4-139">將原則名稱設定為 `_myAllowSpecificOrigins` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="6d4c4-140">原則名稱是任意的。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="6d4c4-141">呼叫 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 擴充方法，並指定  `_myAllowSpecificOrigins` CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="6d4c4-142">`UseCors` 新增 CORS 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="6d4c4-143">的呼叫 `UseCors` 必須放在之後 `UseRouting` ，但在之前 `UseAuthorization` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="6d4c4-144">如需詳細資訊，請參閱 [中介軟體順序](xref:fundamentals/middleware/index#middleware-order)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="6d4c4-145"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的呼叫。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="6d4c4-146">Lambda 接受 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 物件。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="6d4c4-147">設定[選項](#cors-policy-options)（例如 `WithOrigins` ）將在本文稍後說明。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="6d4c4-148">啟用 `_myAllowSpecificOrigins` 所有控制器端點的 CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="6d4c4-149">請參閱 [端點路由](#ecors) ，以將 CORS 原則套用至特定端點。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="6d4c4-150">使用回應快取 [中介軟體](xref:performance/caching/middleware)時，請 <xref:Owin.CorsExtensions.UseCors%2A> 先呼叫 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="6d4c4-151">使用端點路由時，CORS 中介軟體 **必須** 設定為在對和的呼叫之間執行 `UseRouting` `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="6d4c4-152">請參閱 [測試 CORS](#testc) ，以取得測試程式碼類似上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="6d4c4-153"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法呼叫會將 CORS 服務新增至應用程式的服務容器：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="6d4c4-154">如需詳細資訊，請參閱本檔中的 [CORS 原則選項](#cpo) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="6d4c4-155">這些 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 方法可以連結，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="6d4c4-156">注意：指定的 URL **不** 能包含尾端斜線 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="6d4c4-157">如果 URL 以終止 `/` ，則比較會 `false` 傳回，而且不會傳回任何標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="6d4c4-158">使用預設原則和中介軟體的 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="6d4c4-159">下列反白顯示的程式碼會啟用預設 CORS 原則：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="6d4c4-160">上述程式碼會將預設 CORS 原則套用至所有控制器端點。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="6d4c4-161">使用端點路由來啟用 Cors</span><span class="sxs-lookup"><span data-stu-id="6d4c4-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="6d4c4-162">使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援 [自動預檢要求](#apf)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="6d4c4-163">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/aspnetcore/issues/20709) 和 [使用端點路由測試 CORS 和 [HttpOptions]](#tcer)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="6d4c4-164">使用端點路由時，您可以使用一組擴充方法，根據每個端點來啟用 CORS <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="6d4c4-165">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-165">In the preceding code:</span></span>

* <span data-ttu-id="6d4c4-166">`app.UseCors` 啟用 CORS 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="6d4c4-167">由於尚未設定預設原則，因此 `app.UseCors()` 單獨不會啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="6d4c4-168">`/echo`和控制器端點允許使用指定原則的跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="6d4c4-169">`/echo2`和 Razor Pages 端點**不**允許跨原始來源的要求，因為未指定預設原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="6d4c4-170">[[DisableCors]](#dc) **屬性不會停用**端點路由所啟用的 CORS `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="6d4c4-171">如需測試程式碼的相關指示，請參閱 [使用端點路由與 [HttpOptions]](#tcer) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="6d4c4-172">使用屬性來啟用 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-172">Enable CORS with attributes</span></span>

<span data-ttu-id="6d4c4-173">使用 [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 屬性來啟用 CORS，並只將命名原則套用至需要 cors 的端點，可提供最精細的控制。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="6d4c4-174">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)屬性提供全域套用 CORS 的替代方案。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="6d4c4-175">`[EnableCors]`屬性會針對選取的端點啟用 CORS，而不是所有端點：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="6d4c4-176">`[EnableCors]` 指定預設原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="6d4c4-177">`[EnableCors("{Policy String}")]` 指定命名原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="6d4c4-178">`[EnableCors]`屬性可套用至：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="6d4c4-179">Razor 網頁 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="6d4c4-179">Razor Page `PageModel`</span></span>
* <span data-ttu-id="6d4c4-180">控制器</span><span class="sxs-lookup"><span data-stu-id="6d4c4-180">Controller</span></span>
* <span data-ttu-id="6d4c4-181">控制器動作方法</span><span class="sxs-lookup"><span data-stu-id="6d4c4-181">Controller action method</span></span>

<span data-ttu-id="6d4c4-182">您可以使用屬性，將不同的原則套用至控制器、頁面模型或動作方法 `[EnableCors]` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="6d4c4-183">當 `[EnableCors]` 屬性套用至控制器、頁面模型或動作方法，並在中介軟體啟用 CORS 時，會套用 **這兩個** 原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="6d4c4-184">**建議您不要結合原則。使用** `[EnableCors]` **屬性或中介軟體，而不是在相同的應用程式中。**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="6d4c4-185">下列程式碼會將不同的原則套用至每個方法：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="6d4c4-186">下列程式碼會建立兩個 CORS 原則：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="6d4c4-187">針對限制 CORS 要求的最精細控制：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="6d4c4-188">`[EnableCors("MyPolicy")]`與命名原則搭配使用。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="6d4c4-189">請勿定義預設原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-189">Don't define a default policy.</span></span>
* <span data-ttu-id="6d4c4-190">請勿使用 [端點路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="6d4c4-191">下一節中的程式碼符合上述清單。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="6d4c4-192">請參閱 [測試 CORS](#testc) ，以取得測試程式碼類似上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="6d4c4-193">停用 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-193">Disable CORS</span></span>

<span data-ttu-id="6d4c4-194">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **屬性不會停用**[端點路由](#ecors)已啟用的 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="6d4c4-195">下列程式碼會定義 CORS 原則 `"MyPolicy"` ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="6d4c4-196">下列程式碼會停用動作的 CORS `GetValues2` ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="6d4c4-197">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-197">The preceding code:</span></span>

* <span data-ttu-id="6d4c4-198">不會使用 [端點路由](#ecors)來啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="6d4c4-199">未定義 [預設 CORS 原則](#dp)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="6d4c4-200">使用 [[EnableCors ( "MyPolicy" ) ]](#attr) 來啟用 `"MyPolicy"` 控制器的 CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="6d4c4-201">停用方法的 CORS `GetValues2` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="6d4c4-202">請參閱 [測試 CORS](#testc) 以取得測試上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="6d4c4-203">CORS 原則選項</span><span class="sxs-lookup"><span data-stu-id="6d4c4-203">CORS policy options</span></span>

<span data-ttu-id="6d4c4-204">本節說明可在 CORS 原則中設定的各種選項：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="6d4c4-205">設定允許的來源</span><span class="sxs-lookup"><span data-stu-id="6d4c4-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="6d4c4-206">設定允許的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="6d4c4-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="6d4c4-207">設定允許的要求標頭</span><span class="sxs-lookup"><span data-stu-id="6d4c4-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="6d4c4-208">設定公開的回應標頭</span><span class="sxs-lookup"><span data-stu-id="6d4c4-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="6d4c4-209">跨原始來源要求中的認證</span><span class="sxs-lookup"><span data-stu-id="6d4c4-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="6d4c4-210">設定預檢到期時間</span><span class="sxs-lookup"><span data-stu-id="6d4c4-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="6d4c4-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6d4c4-212">對於某些選項而言，先閱讀「 [CORS 的運作方式](#how-cors) 」一節可能很有説明。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="6d4c4-213">設定允許的來源</span><span class="sxs-lookup"><span data-stu-id="6d4c4-213">Set the allowed origins</span></span>

<span data-ttu-id="6d4c4-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允許所有來源中具有任何配置 (或) 的 CORS 要求 `http` `https` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="6d4c4-215">`AllowAnyOrigin` 不安全，因為 *任何網站* 都可以對應用程式進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="6d4c4-216">指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的設定，而且可能會導致跨網站要求偽造。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="6d4c4-217">當以這兩種方法設定應用程式時，CORS 服務會傳回不正確 CORS 回應。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="6d4c4-218">`AllowAnyOrigin` 影響預檢要求和 `Access-Control-Allow-Origin` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="6d4c4-219">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="6d4c4-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：將原則的屬性設定為函式 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> ，以在評估是否允許原始來源時，允許原始來源符合已設定的萬用字元網域。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="6d4c4-221">設定允許的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="6d4c4-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="6d4c4-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="6d4c4-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="6d4c4-223">允許任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="6d4c4-224">影響預檢要求和 `Access-Control-Allow-Methods` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="6d4c4-225">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="6d4c4-226">設定允許的要求標頭</span><span class="sxs-lookup"><span data-stu-id="6d4c4-226">Set the allowed request headers</span></span>

<span data-ttu-id="6d4c4-227">若要允許在 CORS 要求中傳送特定標頭（稱為「 [作者要求標頭](https://xhr.spec.whatwg.org/#request)」），請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 並指定允許的標頭：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="6d4c4-228">若要允許所有 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="6d4c4-229">`AllowAnyHeader` 影響預檢要求和 [存取控制要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 標頭標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="6d4c4-230">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="6d4c4-231">`WithHeaders`只有當傳送的標頭 `Access-Control-Request-Headers` 完全符合中所述的標頭時，才可能符合所指定之特定標頭的 CORS 中介軟體原則 `WithHeaders` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="6d4c4-232">例如，假設應用程式設定如下：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="6d4c4-233">CORS 中介軟體會拒絕具有下列要求標頭的預檢要求，因為 `Content-Language` ([HeaderNames ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) 未列在 `WithHeaders` ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="6d4c4-234">應用程式會傳回 *200 OK* 回應，但不會傳送 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="6d4c4-235">因此，瀏覽器不會嘗試跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="6d4c4-236">設定公開的回應標頭</span><span class="sxs-lookup"><span data-stu-id="6d4c4-236">Set the exposed response headers</span></span>

<span data-ttu-id="6d4c4-237">根據預設，瀏覽器不會將所有回應標頭公開給應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="6d4c4-238">如需詳細資訊，請參閱 [W3C 跨原始資源分享 (術語) ：簡單的回應標頭](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="6d4c4-239">預設可用的回應標頭為：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="6d4c4-240">CORS 規格會呼叫這些標頭 *簡單的回應標頭*。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-240">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="6d4c4-241">若要讓應用程式使用其他標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="6d4c4-242">跨原始來源要求中的認證</span><span class="sxs-lookup"><span data-stu-id="6d4c4-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="6d4c4-243">認證需要 CORS 要求中的特殊處理。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="6d4c4-244">根據預設，瀏覽器不會傳送具有跨原始來源要求的認證。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="6d4c4-245">認證包括 cookie s 和 HTTP 驗證配置。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="6d4c4-246">若要傳送具有跨原始來源要求的認證，用戶端必須設定 `XMLHttpRequest.withCredentials` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="6d4c4-247">`XMLHttpRequest`直接使用：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="6d4c4-248">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="6d4c4-249">使用 [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="6d4c4-250">伺服器必須允許認證。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-250">The server must allow the credentials.</span></span> <span data-ttu-id="6d4c4-251">若要允許跨原始來源認證，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="6d4c4-252">HTTP 回應包含 `Access-Control-Allow-Credentials` 標頭，它會告知瀏覽器伺服器允許跨原始來源要求的認證。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="6d4c4-253">如果瀏覽器傳送認證但回應不包含有效的 `Access-Control-Allow-Credentials` 標頭，則瀏覽器不會公開應用程式的回應，而且跨原始來源要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="6d4c4-254">允許跨原始來源的認證有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="6d4c4-255">另一個網域的網站可以代表使用者將已登入使用者的認證傳送給應用程式，而不需要使用者的知識。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="6d4c4-256">CORS 規格也指出 `"*"` 如果標頭存在，設定來源 (所有來源) 無效 `Access-Control-Allow-Credentials` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="6d4c4-257">預檢要求</span><span class="sxs-lookup"><span data-stu-id="6d4c4-257">Preflight requests</span></span>

<span data-ttu-id="6d4c4-258">針對某些 CORS 要求，瀏覽器會先傳送額外的 [選項](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 要求，再進行實際要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="6d4c4-259">此要求稱為 [預檢要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="6d4c4-260">如果下列 **所有** 條件都成立，則瀏覽器可以略過預檢要求：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="6d4c4-261">要求方法為 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="6d4c4-262">應用程式不會設定 `Accept` 、、 `Accept-Language` `Content-Language` 、 `Content-Type` 或以外的要求標頭 `Last-Event-ID` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="6d4c4-263">`Content-Type`如果設定，標頭會有下列其中一個值：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="6d4c4-264">針對用戶端要求所設定之要求標頭的規則，會套用至應用程式透過在物件上呼叫所設定的標頭 `setRequestHeader` `XMLHttpRequest` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="6d4c4-265">CORS 規格會呼叫這些標頭的 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="6d4c4-266">此規則不適用於瀏覽器可以設定的標頭，例如 `User-Agent` 、 `Host` 或 `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="6d4c4-267">以下是類似于本檔之[測試 CORS](#testc)區段中 **[Put test]** 按鈕所提出之預檢要求的範例回應。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="6d4c4-268">預檢要求會使用 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 方法。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="6d4c4-269">它可能包含下列標頭：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-269">It may include the following headers:</span></span>

* <span data-ttu-id="6d4c4-270">[存取控制-要求-方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)：將用於實際要求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="6d4c4-271">[存取控制-要求-標頭](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)：應用程式在實際要求上設定的要求標頭清單。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="6d4c4-272">如先前所述，這並不包括瀏覽器所設定的標頭，例如 `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="6d4c4-273">存取控制-允許方法</span><span class="sxs-lookup"><span data-stu-id="6d4c4-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="6d4c4-274">如果預檢要求遭到拒絕，應用程式就會傳回 `200 OK` 回應，但不會設定 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="6d4c4-275">因此，瀏覽器不會嘗試跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="6d4c4-276">如需拒絕預檢要求的範例，請參閱本檔的「 [測試 CORS](#testc) 」一節。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="6d4c4-277">使用 F12 工具，主控台應用程式會根據瀏覽器顯示類似下列其中一項的錯誤：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="6d4c4-278">Firefox：跨原始來源要求遭到封鎖：相同的原始原則不允許在中讀取遠端資源 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="6d4c4-279"> (原因： CORS 要求未成功) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="6d4c4-280">深入了解</span><span class="sxs-lookup"><span data-stu-id="6d4c4-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="6d4c4-281">以 Chromium 為基礎： ' ' 來源 ' ' 的 fetch 存取 https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net 已被 CORS 原則封鎖：預檢要求的回應未通過存取控制檢查：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="6d4c4-282">如果不透明回應適合您的需求，請將要求的模式設定為 'no-cors' 以在停用 CORS 之下擷取資源。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="6d4c4-283">若要允許特定標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="6d4c4-284">若要允許所有 [作者要求標頭](https://www.w3.org/TR/cors/#author-request-headers)，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="6d4c4-285">瀏覽器的設定方式不一致 `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="6d4c4-286">如果其中一項：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-286">If either:</span></span>

* <span data-ttu-id="6d4c4-287">標頭會設定為以外的任何其他 `"*"`</span><span class="sxs-lookup"><span data-stu-id="6d4c4-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="6d4c4-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 呼叫：至少包含 `Accept` 、 `Content-Type` 和 `Origin` ，以及您想要支援的任何自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="6d4c4-289">自動預檢要求碼</span><span class="sxs-lookup"><span data-stu-id="6d4c4-289">Automatic preflight request code</span></span>

<span data-ttu-id="6d4c4-290">套用 CORS 原則的時機為：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="6d4c4-291">`app.UseCors`在中呼叫全域 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="6d4c4-292">使用 `[EnableCors]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="6d4c4-293">ASP.NET Core 回應預檢 OPTIONS 要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="6d4c4-294">使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援自動預檢要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="6d4c4-295">本檔的「 [測試 CORS](#testc) 」區段會示範此行為。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="6d4c4-296">[HttpOptions] 預檢要求的屬性</span><span class="sxs-lookup"><span data-stu-id="6d4c4-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="6d4c4-297">使用適當的原則啟用 CORS 時，ASP.NET Core 通常會自動回應 CORS 預檢要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="6d4c4-298">在某些情況下，可能不會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="6d4c4-299">例如，搭配 [端點路由使用 CORS](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="6d4c4-300">下列程式碼會使用 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 屬性來建立選項要求的端點：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="6d4c4-301">請參閱 [使用端點路由測試 CORS 和 [HttpOptions]](#tcer) ，以取得測試上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="6d4c4-302">設定預檢到期時間</span><span class="sxs-lookup"><span data-stu-id="6d4c4-302">Set the preflight expiration time</span></span>

<span data-ttu-id="6d4c4-303">`Access-Control-Max-Age`標頭會指定可以快取預檢要求回應的時間長度。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="6d4c4-304">若要設定此標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="6d4c4-305">CORS 的運作方式</span><span class="sxs-lookup"><span data-stu-id="6d4c4-305">How CORS works</span></span>

<span data-ttu-id="6d4c4-306">本節說明在 HTTP 訊息層級的 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 要求中發生的情況。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="6d4c4-307">CORS **不** 是安全性功能。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="6d4c4-308">CORS 是一種 W3C 標準，可讓伺服器放寬相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="6d4c4-309">例如，惡意執行者可以針對您的網站使用 [跨網站腳本 (XSS) ](xref:security/cross-site-scripting) ，並對其已啟用 CORS 的網站執行跨網站要求以竊取資訊。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="6d4c4-310">API 不會藉由允許 CORS 而更安全。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="6d4c4-311">用戶端 (瀏覽器) 來強制執行 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="6d4c4-312">伺服器會執行要求並傳迴響應，這是傳回錯誤並封鎖回應的用戶端。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="6d4c4-313">例如，下列任何一項工具都會顯示伺服器回應：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="6d4c4-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="6d4c4-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="6d4c4-315">Postman</span><span class="sxs-lookup"><span data-stu-id="6d4c4-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="6d4c4-316">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="6d4c4-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="6d4c4-317">在網址列中輸入 URL 的網頁瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="6d4c4-318">這是伺服器允許瀏覽器執行跨原始 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 或 [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 要求的方法，否則會被禁止。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="6d4c4-319">沒有 CORS 的瀏覽器無法進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="6d4c4-320">在 CORS 之前，會使用 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 來規避這種限制。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="6d4c4-321">JSONP 不會使用 XHR，它會使用 `<script>` 標記來接收回應。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="6d4c4-322">允許跨原始來源載入腳本。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="6d4c4-323">[CORS 規格](https://www.w3.org/TR/cors/)引進了數個新的 HTTP 標頭，可啟用跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="6d4c4-324">如果瀏覽器支援 CORS，則會自動為跨原始來源要求設定這些標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="6d4c4-325">不需要自訂 JavaScript 程式碼來啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="6d4c4-326">已部署[範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)上的[PUT 測試按鈕](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="6d4c4-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="6d4c4-327">以下是從 [ [值](https://cors3.azurewebsites.net/) 測試] 按鈕到的跨原始來源要求範例 `https://cors1.azurewebsites.net/api/values` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="6d4c4-328">`Origin`標頭：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-328">The `Origin` header:</span></span>

* <span data-ttu-id="6d4c4-329">提供提出要求的網站網域。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="6d4c4-330">是必要的，而且必須與主機不同。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="6d4c4-331">**一般標頭**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="6d4c4-332">**回應標頭**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="6d4c4-333">**要求標頭**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-333">**Request headers**</span></span>

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

<span data-ttu-id="6d4c4-334">在 `OPTIONS` 要求中，伺服器會在回應中設定 **回應標頭** `Access-Control-Allow-Origin: {allowed origin}` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="6d4c4-335">例如，已部署的 [範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) 按鈕 `OPTIONS` 要求包含下列標頭：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="6d4c4-336">**一般標頭**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="6d4c4-337">**回應標頭**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="6d4c4-338">**要求標頭**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-338">**Request headers**</span></span>

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

<span data-ttu-id="6d4c4-339">在先前的 **回應標頭**中，伺服器會在回應中設定 [存取控制-允許原始](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 的標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-339">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="6d4c4-340">`https://cors1.azurewebsites.net`此標頭的值符合 `Origin` 要求中的標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="6d4c4-341">如果 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> 呼叫，則 `Access-Control-Allow-Origin: *` 會傳回萬用字元值。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="6d4c4-342">`AllowAnyOrigin` 允許任何來源。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="6d4c4-343">如果回應不包含 `Access-Control-Allow-Origin` 標頭，則跨原始來源要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="6d4c4-344">具體而言，瀏覽器不允許要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="6d4c4-345">即使伺服器傳回成功的回應，瀏覽器也不會將回應提供給用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="6d4c4-346">顯示選項要求</span><span class="sxs-lookup"><span data-stu-id="6d4c4-346">Display OPTIONS requests</span></span>

<span data-ttu-id="6d4c4-347">根據預設，Chrome 和 Edge 瀏覽器不會在 F12 工具的 [網路] 索引標籤上顯示選項要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="6d4c4-348">若要在這些瀏覽器中顯示選項要求：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="6d4c4-349">`chrome://flags/#out-of-blink-cors` 或 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="6d4c4-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="6d4c4-350">停用旗標。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-350">disable the flag.</span></span>
* <span data-ttu-id="6d4c4-351">重新 啟動。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-351">restart.</span></span>

<span data-ttu-id="6d4c4-352">Firefox 預設會顯示選項要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="6d4c4-353">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-353">CORS in IIS</span></span>

<span data-ttu-id="6d4c4-354">部署至 IIS 時，如果伺服器未設定為允許匿名存取，CORS 必須在 Windows 驗證之前執行。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="6d4c4-355">若要支援此案例，必須為應用程式安裝及設定 [IIS CORS 模組](https://www.iis.net/downloads/microsoft/iis-cors-module) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="6d4c4-356">測試 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-356">Test CORS</span></span>

<span data-ttu-id="6d4c4-357">[範例下載](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)具有測試 CORS 的程式碼。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="6d4c4-358">請參閱[如何下載](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="6d4c4-359">範例是已新增頁面的 API 專案 Razor ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="6d4c4-360">`WithOrigins("https://localhost:<port>");` 只能用來測試範例應用程式，類似于 [下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="6d4c4-361">以下 `ValuesController` 提供測試的端點：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="6d4c4-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) 由 [Rick.DocRouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 套件提供，並顯示路由資訊。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="6d4c4-363">使用下列其中一種方法來測試上述的範例程式碼：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="6d4c4-364">使用已部署的範例應用程式 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="6d4c4-365">不需要下載範例。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="6d4c4-366">`dotnet run`使用的預設 URL 執行範例 `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="6d4c4-367">從 Visual Studio 中執行範例，並將的 URL 設定為 44398 `https://localhost:44398` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="6d4c4-368">使用瀏覽器搭配 F12 工具：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="6d4c4-369">選取 [ **值** ] 按鈕，並檢查 [ **網路** ] 索引標籤中的標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="6d4c4-370">選取 [ **PUT 測試** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-370">Select the **PUT test** button.</span></span> <span data-ttu-id="6d4c4-371">如需顯示選項要求的指示，請參閱 [顯示選項要求](#options) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="6d4c4-372">**PUT 測試**會建立兩個要求，也就是選項預檢要求和 PUT 要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="6d4c4-373">選取 **`GetValues2 [DisableCors]`** 按鈕以觸發失敗的 CORS 要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="6d4c4-374">如檔中所述，回應會傳回200成功，但不會發出 CORS 要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="6d4c4-375">選取 [ **主控台** ] 索引標籤，以查看 CORS 錯誤。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="6d4c4-376">視瀏覽器而定，會顯示類似下列的錯誤：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="6d4c4-377">`'https://cors1.azurewebsites.net/api/values/GetValues2'`CORS 原則已封鎖從來源提取的存取權 `'https://cors3.azurewebsites.net'` ：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="6d4c4-378">如果不透明回應適合您的需求，請將要求的模式設定為 'no-cors' 以在停用 CORS 之下擷取資源。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="6d4c4-379">啟用 CORS 的端點可以使用工具（例如 [捲曲](https://curl.haxx.se/)、 [Fiddler](https://www.telerik.com/fiddler)或 [Postman](https://www.getpostman.com/)）進行測試。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="6d4c4-380">使用工具時，標頭所指定的要求來源必須與 `Origin` 接收要求的主機不同。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="6d4c4-381">如果 *要求不是* 以標頭的值為基礎 `Origin` ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="6d4c4-382">CORS 中介軟體不需要處理要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="6d4c4-383">回應中不會傳回 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="6d4c4-384">下列命令會使用 `curl` 來發出具有下列資訊的選項要求：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="6d4c4-385">使用端點路由和 [HttpOptions] 測試 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="6d4c4-386">使用目前的每個端點來啟用 CORS `RequireCors` **不** 支援 [自動預檢要求](#apf)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="6d4c4-387">請考慮使用 [端點路由來啟用 CORS](#ecors)的下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="6d4c4-388">以下 `TodoItems1Controller` 提供測試的端點：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="6d4c4-389">從已部署[範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)的[測試頁面](https://cors1.azurewebsites.net/test?number=1)測試上述程式碼。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="6d4c4-390">**Delete [EnableCors]** 和**GET [EnableCors]** 按鈕會成功，因為端點具有 `[EnableCors]` 並回應預檢要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="6d4c4-391">其他端點失敗。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-391">The other endpoints fails.</span></span> <span data-ttu-id="6d4c4-392">**GET**按鈕失敗，因為[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)會傳送：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="6d4c4-393">以下 `TodoItems2Controller` 提供類似的端點，但包含可回應 OPTIONS 要求的明確程式碼：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="6d4c4-394">從已部署範例的 [測試頁面](https://cors1.azurewebsites.net/test?number=2) 測試上述程式碼。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="6d4c4-395">在 [ **控制器** ] 下拉式清單中，選取 [ **預檢** ]，然後 **設定 [控制器**]。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="6d4c4-396">端點的所有 CORS 呼叫都會 `TodoItems2Controller` 成功。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d4c4-397">其他資源</span><span class="sxs-lookup"><span data-stu-id="6d4c4-397">Additional resources</span></span>

* [<span data-ttu-id="6d4c4-398">跨原始來源資源分享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="6d4c4-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="6d4c4-399">開始使用 IIS CORS 模組</span><span class="sxs-lookup"><span data-stu-id="6d4c4-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6d4c4-400">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6d4c4-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6d4c4-401">本文說明如何在 ASP.NET Core 應用程式中啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="6d4c4-402">瀏覽器安全性可防止網頁提出要求，而不是與提供網頁的不同網域。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="6d4c4-403">這項限制稱為 *相同原始來源原則*。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-403">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="6d4c4-404">相同來源原則可防止惡意網站從另一個網站讀取敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="6d4c4-405">有時，您可能會想要允許其他網站對您的應用程式進行跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="6d4c4-406">如需詳細資訊，請參閱 [MOZILLA CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="6d4c4-407">[跨原始資源分享](https://www.w3.org/TR/cors/) (CORS) ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="6d4c4-408">是一種 W3C 標準，可讓伺服器放寬相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="6d4c4-409">不 **是安全性** 功能，CORS 放寬安全性。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="6d4c4-410">API 不會藉由允許 CORS 來更安全。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="6d4c4-411">如需詳細資訊，請參閱 [CORS 的運作方式](#how-cors)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="6d4c4-412">允許伺服器明確允許某些跨原始來源的要求，同時拒絕其他要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="6d4c4-413">比先前的技術（例如 [JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更有彈性。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="6d4c4-414">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="6d4c4-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="6d4c4-415">相同的來源</span><span class="sxs-lookup"><span data-stu-id="6d4c4-415">Same origin</span></span>

<span data-ttu-id="6d4c4-416">如果兩個 Url 具有相同的配置、主機和埠 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，其來源相同。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="6d4c4-417">這兩個 Url 的來源相同：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="6d4c4-418">這些 Url 的來源與前兩個 Url 不同：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="6d4c4-419">`https://example.net`：不同的網域</span><span class="sxs-lookup"><span data-stu-id="6d4c4-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="6d4c4-420">`https://www.example.com/foo.html`：不同的子域</span><span class="sxs-lookup"><span data-stu-id="6d4c4-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="6d4c4-421">`http://example.com/foo.html`：不同的配置</span><span class="sxs-lookup"><span data-stu-id="6d4c4-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="6d4c4-422">`https://example.com:9000/foo.html`：不同的埠</span><span class="sxs-lookup"><span data-stu-id="6d4c4-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="6d4c4-423">比較來源時，Internet Explorer 不會考慮埠。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="6d4c4-424">具有命名原則和中介軟體的 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="6d4c4-425">CORS 中介軟體會處理跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="6d4c4-426">下列程式碼會針對具有指定來源的整個應用程式啟用 CORS：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="6d4c4-427">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-427">The preceding code:</span></span>

* <span data-ttu-id="6d4c4-428">將原則名稱設定為 " \_ myAllowSpecificOrigins"。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="6d4c4-429">原則名稱是任意的。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="6d4c4-430">呼叫 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 擴充方法，以啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="6d4c4-431"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 運算式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的呼叫。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="6d4c4-432">Lambda 接受 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 物件。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="6d4c4-433">設定[選項](#cors-policy-options)（例如 `WithOrigins` ）將在本文稍後說明。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="6d4c4-434"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法呼叫會將 CORS 服務新增至應用程式的服務容器：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="6d4c4-435">如需詳細資訊，請參閱本檔中的 [CORS 原則選項](#cpo) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="6d4c4-436"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以鏈方法，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="6d4c4-437">注意： URL **不** 能包含尾端斜線 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="6d4c4-438">如果 URL 以終止 `/` ，則比較會 `false` 傳回，而且不會傳回任何標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="6d4c4-439">下列程式碼會透過 CORS 中介軟體將 CORS 原則套用至所有應用程式端點：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="6d4c4-440">注意： `UseCors` 必須先呼叫 `UseMvc` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="6d4c4-441">請參閱在頁面 [ Razor 、控制器和動作方法中啟用 cors](#ecors) ，以在頁面/控制器/動作層級套用 cors 原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="6d4c4-442">請參閱 [測試 CORS](#test) ，以取得測試程式碼類似上述程式碼的指示。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="6d4c4-443">使用屬性來啟用 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-443">Enable CORS with attributes</span></span>

<span data-ttu-id="6d4c4-444">[ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)屬性提供全域套用 CORS 的替代方案。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="6d4c4-445">`[EnableCors]`屬性會針對選取的端點啟用 CORS，而不是所有端點。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="6d4c4-446">使用 `[EnableCors]` 指定預設原則，並 `[EnableCors("{Policy String}")]` 指定原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="6d4c4-447">`[EnableCors]`屬性可套用至：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="6d4c4-448">Razor 網頁 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="6d4c4-448">Razor Page `PageModel`</span></span>
* <span data-ttu-id="6d4c4-449">控制器</span><span class="sxs-lookup"><span data-stu-id="6d4c4-449">Controller</span></span>
* <span data-ttu-id="6d4c4-450">控制器動作方法</span><span class="sxs-lookup"><span data-stu-id="6d4c4-450">Controller action method</span></span>

<span data-ttu-id="6d4c4-451">您可以使用屬性，將不同的原則套用至控制器/頁面模型/動作  `[EnableCors]` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="6d4c4-452">當 `[EnableCors]` 屬性套用至控制器/頁面模型/動作方法，且在中介軟體中啟用 CORS 時，會套用 **這兩個** 原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="6d4c4-453">建議 **不要** 結合原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="6d4c4-454">使用 `[EnableCors]` 屬性或中介軟體， **而非兩者**。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-454">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="6d4c4-455">使用時 `[EnableCors]` ，請勿**not**定義預設原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="6d4c4-456">下列程式碼會將不同的原則套用至每個方法：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="6d4c4-457">下列程式碼會建立 CORS 預設原則和名為的原則 `"AnotherPolicy"` ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="6d4c4-458">停用 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-458">Disable CORS</span></span>

<span data-ttu-id="6d4c4-459">[ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)屬性會停用控制器/頁面模型/動作的 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="6d4c4-460">CORS 原則選項</span><span class="sxs-lookup"><span data-stu-id="6d4c4-460">CORS policy options</span></span>

<span data-ttu-id="6d4c4-461">本節說明可在 CORS 原則中設定的各種選項：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="6d4c4-462">設定允許的來源</span><span class="sxs-lookup"><span data-stu-id="6d4c4-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="6d4c4-463">設定允許的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="6d4c4-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="6d4c4-464">設定允許的要求標頭</span><span class="sxs-lookup"><span data-stu-id="6d4c4-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="6d4c4-465">設定公開的回應標頭</span><span class="sxs-lookup"><span data-stu-id="6d4c4-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="6d4c4-466">跨原始來源要求中的認證</span><span class="sxs-lookup"><span data-stu-id="6d4c4-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="6d4c4-467">設定預檢到期時間</span><span class="sxs-lookup"><span data-stu-id="6d4c4-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="6d4c4-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6d4c4-469">對於某些選項而言，先閱讀「 [CORS 的運作方式](#how-cors) 」一節可能很有説明。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="6d4c4-470">設定允許的來源</span><span class="sxs-lookup"><span data-stu-id="6d4c4-470">Set the allowed origins</span></span>

<span data-ttu-id="6d4c4-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允許所有來源中具有任何配置 (或) 的 CORS 要求 `http` `https` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="6d4c4-472">`AllowAnyOrigin` 不安全，因為 *任何網站* 都可以對應用程式進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="6d4c4-473">指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的設定，而且可能會導致跨網站要求偽造。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="6d4c4-474">針對安全的應用程式，如果用戶端必須授權自己存取伺服器資源，請指定確切的來源清單。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="6d4c4-475">`AllowAnyOrigin` 影響預檢要求和 `Access-Control-Allow-Origin` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="6d4c4-476">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="6d4c4-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：將原則的屬性設定為函式 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> ，以在評估是否允許原始來源時，允許原始來源符合已設定的萬用字元網域。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="6d4c4-478">設定允許的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="6d4c4-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="6d4c4-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="6d4c4-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="6d4c4-480">允許任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="6d4c4-481">影響預檢要求和 `Access-Control-Allow-Methods` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="6d4c4-482">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="6d4c4-483">設定允許的要求標頭</span><span class="sxs-lookup"><span data-stu-id="6d4c4-483">Set the allowed request headers</span></span>

<span data-ttu-id="6d4c4-484">若要允許在 CORS 要求中傳送特定標頭（稱為「 *作者要求標頭*」），請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 並指定允許的標頭：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-484">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="6d4c4-485">若要允許所有作者要求標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="6d4c4-486">此設定會影響預檢要求和 `Access-Control-Request-Headers` 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="6d4c4-487">如需詳細資訊，請參閱 [預檢要求](#preflight-requests) 一節。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="6d4c4-488">CORS 中介軟體一律允許傳送中的四個標頭， `Access-Control-Request-Headers` 不論 c 中設定的值為何。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="6d4c4-489">此標頭清單包括：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="6d4c4-490">例如，假設應用程式設定如下：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="6d4c4-491">CORS 中介軟體會使用下列要求標頭成功回應預檢要求，因為 `Content-Language` 一律允許：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="6d4c4-492">設定公開的回應標頭</span><span class="sxs-lookup"><span data-stu-id="6d4c4-492">Set the exposed response headers</span></span>

<span data-ttu-id="6d4c4-493">根據預設，瀏覽器不會將所有回應標頭公開給應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="6d4c4-494">如需詳細資訊，請參閱 [W3C 跨原始資源分享 (術語) ：簡單的回應標頭](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="6d4c4-495">預設可用的回應標頭為：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="6d4c4-496">CORS 規格會呼叫這些標頭 *簡單的回應標頭*。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-496">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="6d4c4-497">若要讓應用程式使用其他標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="6d4c4-498">跨原始來源要求中的認證</span><span class="sxs-lookup"><span data-stu-id="6d4c4-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="6d4c4-499">認證需要 CORS 要求中的特殊處理。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="6d4c4-500">根據預設，瀏覽器不會傳送具有跨原始來源要求的認證。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="6d4c4-501">認證包括 cookie s 和 HTTP 驗證配置。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="6d4c4-502">若要傳送具有跨原始來源要求的認證，用戶端必須設定 `XMLHttpRequest.withCredentials` 為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="6d4c4-503">`XMLHttpRequest`直接使用：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="6d4c4-504">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="6d4c4-505">使用 [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="6d4c4-506">伺服器必須允許認證。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-506">The server must allow the credentials.</span></span> <span data-ttu-id="6d4c4-507">若要允許跨原始來源認證，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="6d4c4-508">HTTP 回應包含 `Access-Control-Allow-Credentials` 標頭，它會告知瀏覽器伺服器允許跨原始來源要求的認證。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="6d4c4-509">如果瀏覽器傳送認證但回應不包含有效的 `Access-Control-Allow-Credentials` 標頭，則瀏覽器不會公開應用程式的回應，而且跨原始來源要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="6d4c4-510">允許跨原始來源的認證有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="6d4c4-511">另一個網域的網站可以代表使用者將已登入使用者的認證傳送給應用程式，而不需要使用者的知識。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="6d4c4-512">CORS 規格也指出 `"*"` 如果標頭存在，設定來源 (所有來源) 無效 `Access-Control-Allow-Credentials` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="6d4c4-513">預檢要求</span><span class="sxs-lookup"><span data-stu-id="6d4c4-513">Preflight requests</span></span>

<span data-ttu-id="6d4c4-514">針對某些 CORS 要求，瀏覽器會先傳送額外的要求，再進行實際要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="6d4c4-515">此要求稱為 *預檢要求*。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-515">This request is called a *preflight request*.</span></span> <span data-ttu-id="6d4c4-516">如果下列條件成立，瀏覽器可以略過預檢要求：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="6d4c4-517">要求方法為 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="6d4c4-518">應用程式不會設定 `Accept` 、、 `Accept-Language` `Content-Language` 、 `Content-Type` 或以外的要求標頭 `Last-Event-ID` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="6d4c4-519">`Content-Type`如果設定，標頭會有下列其中一個值：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="6d4c4-520">針對用戶端要求所設定之要求標頭的規則，會套用至應用程式透過在物件上呼叫所設定的標頭 `setRequestHeader` `XMLHttpRequest` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="6d4c4-521">CORS 規格會呼叫這些標頭的 *作者要求標頭*。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-521">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="6d4c4-522">此規則不適用於瀏覽器可以設定的標頭，例如 `User-Agent` 、 `Host` 或 `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="6d4c4-523">以下是預檢要求的範例：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-523">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="6d4c4-524">進入飛行前要求會使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="6d4c4-525">它包含兩個特殊標頭：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-525">It includes two special headers:</span></span>

* <span data-ttu-id="6d4c4-526">`Access-Control-Request-Method`：將用於實際要求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="6d4c4-527">`Access-Control-Request-Headers`：應用程式在實際要求上設定的要求標頭清單。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="6d4c4-528">如先前所述，這並不包括瀏覽器所設定的標頭，例如 `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="6d4c4-529">使用適當的原則啟用 CORS 時，ASP.NET Core 通常會自動回應 CORS 預檢要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="6d4c4-530">請參閱 [預檢要求的 [HttpOptions] 屬性](#pro)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="6d4c4-531">CORS 預檢要求可能包含 `Access-Control-Request-Headers` 標頭，這會向伺服器指出與實際要求一起傳送的標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="6d4c4-532">若要允許特定標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="6d4c4-533">若要允許所有作者要求標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="6d4c4-534">瀏覽器的設定方式並不完全一致 `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="6d4c4-535">如果您將標頭設定為 `"*"` (或使用) 以外的任何 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 一個，您應該至少包含 `Accept` 、 `Content-Type` 和 `Origin` ，以及您想要支援的任何自訂標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="6d4c4-536">以下是預檢要求的範例回應， (假設伺服器允許要求) ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="6d4c4-537">回應包含的 `Access-Control-Allow-Methods` 標頭會列出允許的方法以及選擇性的 `Access-Control-Allow-Headers` 標頭，以列出允許的標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="6d4c4-538">如果預檢要求成功，則瀏覽器會傳送實際的要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="6d4c4-539">如果預檢要求遭到拒絕，應用程式會傳回 *200 OK* 回應，但不會傳送 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="6d4c4-540">因此，瀏覽器不會嘗試跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="6d4c4-541">設定預檢到期時間</span><span class="sxs-lookup"><span data-stu-id="6d4c4-541">Set the preflight expiration time</span></span>

<span data-ttu-id="6d4c4-542">`Access-Control-Max-Age`標頭會指定可以快取預檢要求回應的時間長度。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="6d4c4-543">若要設定此標頭，請呼叫 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="6d4c4-544">CORS 的運作方式</span><span class="sxs-lookup"><span data-stu-id="6d4c4-544">How CORS works</span></span>

<span data-ttu-id="6d4c4-545">本節說明在 HTTP 訊息層級的 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 要求中發生的情況。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="6d4c4-546">CORS **不** 是安全性功能。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="6d4c4-547">CORS 是一種 W3C 標準，可讓伺服器放寬相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="6d4c4-548">例如，惡意的動作專案可能會針對您的網站使用 [防止跨網站腳本 (XSS) ](xref:security/cross-site-scripting) ，並對其已啟用 CORS 的網站執行跨網站要求以竊取資訊。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="6d4c4-549">藉由允許 CORS，您的 API 不會更安全。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="6d4c4-550">用戶端 (瀏覽器) 來強制執行 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="6d4c4-551">伺服器會執行要求並傳迴響應，這是傳回錯誤並封鎖回應的用戶端。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="6d4c4-552">例如，下列任何一項工具都會顯示伺服器回應：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="6d4c4-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="6d4c4-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="6d4c4-554">Postman</span><span class="sxs-lookup"><span data-stu-id="6d4c4-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="6d4c4-555">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="6d4c4-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="6d4c4-556">在網址列中輸入 URL 的網頁瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="6d4c4-557">這是伺服器允許瀏覽器執行跨原始 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 或 [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 要求的方法，否則會被禁止。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="6d4c4-558">沒有 CORS 的瀏覽器 () 無法進行跨原始來源的要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="6d4c4-559">在 CORS 之前，會使用 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 來規避這種限制。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="6d4c4-560">JSONP 不會使用 XHR，它會使用 `<script>` 標記來接收回應。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="6d4c4-561">允許跨原始來源載入腳本。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="6d4c4-562">[CORS 規格](https://www.w3.org/TR/cors/)引進了數個新的 HTTP 標頭，可啟用跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="6d4c4-563">如果瀏覽器支援 CORS，則會自動為跨原始來源要求設定這些標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="6d4c4-564">不需要自訂 JavaScript 程式碼來啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="6d4c4-565">以下是跨原始來源要求的範例。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="6d4c4-566">`Origin`標頭會提供提出要求的網站網域。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="6d4c4-567">`Origin`標頭是必要的，而且必須與主機不同。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-567">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="6d4c4-568">如果伺服器允許此要求，則會 `Access-Control-Allow-Origin` 在回應中設定標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="6d4c4-569">此標頭的值 `Origin` 會符合要求中的標頭，或是萬用字元值 `"*"` ，表示允許任何來源：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="6d4c4-570">如果回應不包含 `Access-Control-Allow-Origin` 標頭，則跨原始來源要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="6d4c4-571">具體而言，瀏覽器不允許要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="6d4c4-572">即使伺服器傳回成功的回應，瀏覽器也不會將回應提供給用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="6d4c4-573">測試 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-573">Test CORS</span></span>

<span data-ttu-id="6d4c4-574">若要測試 CORS：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-574">To test CORS:</span></span>

1. <span data-ttu-id="6d4c4-575">[建立 API 專案](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="6d4c4-576">或者，您也可以 [下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="6d4c4-577">請使用本檔中的其中一種方法來啟用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="6d4c4-578">例如：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="6d4c4-579">`WithOrigins("https://localhost:<port>");` 只能用來測試範例應用程式，類似于 [下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="6d4c4-580">建立 web 應用程式專案 (Razor 頁面或 MVC) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="6d4c4-581">此範例會使用 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="6d4c4-582">您可以在與 API 專案相同的解決方案中建立 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="6d4c4-583">將下列反白顯示的程式碼加入至 *索引的 cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="6d4c4-584">在上述程式碼中，將取代為已 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 部署應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="6d4c4-585">部署 API 專案。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-585">Deploy the API project.</span></span> <span data-ttu-id="6d4c4-586">例如， [部署至 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="6d4c4-587">Razor從桌面上執行頁面或 MVC 應用程式，然後按一下 [**測試**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="6d4c4-588">使用 F12 工具來檢查錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="6d4c4-589">從移除 localhost 來源 `WithOrigins` ，並部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="6d4c4-590">或者，使用不同的埠執行用戶端應用程式。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="6d4c4-591">例如，從 Visual Studio 執行。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="6d4c4-592">使用用戶端應用程式進行測試。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-592">Test with the client app.</span></span> <span data-ttu-id="6d4c4-593">CORS 失敗會傳回錯誤，但無法使用 JavaScript 的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="6d4c4-594">使用 F12 工具中的 [主控台] 索引標籤，以查看錯誤。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="6d4c4-595">視瀏覽器而定，您會在 F12 工具主控台中收到 (的錯誤) 如下所示：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="6d4c4-596">使用 Microsoft Edge：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="6d4c4-597">**SEC7120： [CORS] 在 `https://localhost:44375` `https://localhost:44375` 的跨來源資源的存取控制-允許來源回應標頭中找不到來源 `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="6d4c4-598">使用 Chrome：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-598">Using Chrome:</span></span>

     <span data-ttu-id="6d4c4-599">**`https://webapi.azurewebsites.net/api/values/1`CORS 原則已封鎖從來源對 XMLHttpRequest 的存取 `https://localhost:44375` ：要求的資源上沒有 ' 存取控制-允許-來源 ' 標頭。**</span><span class="sxs-lookup"><span data-stu-id="6d4c4-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="6d4c4-600">啟用 CORS 的端點可以使用工具（例如 [Fiddler](https://www.telerik.com/fiddler) 或 [Postman](https://www.getpostman.com/)）進行測試。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="6d4c4-601">使用工具時，標頭所指定的要求來源必須與 `Origin` 接收要求的主機不同。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="6d4c4-602">如果 *要求不是* 以標頭的值為基礎 `Origin` ：</span><span class="sxs-lookup"><span data-stu-id="6d4c4-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="6d4c4-603">CORS 中介軟體不需要處理要求。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="6d4c4-604">回應中不會傳回 CORS 標頭。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="6d4c4-605">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="6d4c4-605">CORS in IIS</span></span>

<span data-ttu-id="6d4c4-606">部署至 IIS 時，如果伺服器未設定為允許匿名存取，CORS 必須在 Windows 驗證之前執行。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="6d4c4-607">若要支援此案例，必須為應用程式安裝及設定 [IIS CORS 模組](https://www.iis.net/downloads/microsoft/iis-cors-module) 。</span><span class="sxs-lookup"><span data-stu-id="6d4c4-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d4c4-608">其他資源</span><span class="sxs-lookup"><span data-stu-id="6d4c4-608">Additional resources</span></span>

* [<span data-ttu-id="6d4c4-609">跨原始來源資源分享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="6d4c4-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="6d4c4-610">開始使用 IIS CORS 模組</span><span class="sxs-lookup"><span data-stu-id="6d4c4-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
