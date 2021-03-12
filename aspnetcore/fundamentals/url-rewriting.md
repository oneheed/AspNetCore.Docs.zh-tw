---
title: ASP.NET Core 的 URL 重寫中介軟體
author: rick-anderson
description: 了解如何使用 ASP.NET Core 的 URL 重寫中介軟體，進行 URL 重寫與重新導向作業。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
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
uid: fundamentals/url-rewriting
ms.openlocfilehash: 44e6570090de9c5f3550d18a30fcde758fb16f85
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588519"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="3728e-103">ASP.NET Core 的 URL 重寫中介軟體</span><span class="sxs-lookup"><span data-stu-id="3728e-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="3728e-104">依 [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="3728e-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3728e-105">本文件介紹 URL 重寫，並提供如何在 ASP.NET Core 應用程式中使用 URL 重寫中介軟體的指示。</span><span class="sxs-lookup"><span data-stu-id="3728e-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="3728e-106">URL 重寫是指根據一或多個預先定義的規則來修改要求 URL 的動作。</span><span class="sxs-lookup"><span data-stu-id="3728e-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="3728e-107">URL 重寫會在資源位置和位址之間建立一個抽象層，讓位置和位址不那麼緊密連結。</span><span class="sxs-lookup"><span data-stu-id="3728e-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="3728e-108">下列幾種情況非常適合使用 URL 重寫：</span><span class="sxs-lookup"><span data-stu-id="3728e-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="3728e-109">暫時或永久性移動/取代伺服器資源，並為這些資源保持穩定的定位器時。</span><span class="sxs-lookup"><span data-stu-id="3728e-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="3728e-110">跨不同應用程式或跨單一應用程式各區域來分割要求處理作業。</span><span class="sxs-lookup"><span data-stu-id="3728e-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="3728e-111">移除、新增或重組傳入要求的 URL 區段。</span><span class="sxs-lookup"><span data-stu-id="3728e-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="3728e-112">針對搜尋引擎最佳化 (SEO) 來將公開 URL 最佳化。</span><span class="sxs-lookup"><span data-stu-id="3728e-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="3728e-113">允許使用易懂的公開 URL，來協助訪客預測要求資源所傳回的內容。</span><span class="sxs-lookup"><span data-stu-id="3728e-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="3728e-114">將不安全的要求重新導向至安全的端點。</span><span class="sxs-lookup"><span data-stu-id="3728e-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="3728e-115">防止直接連結，即外部網站透過將另一個網站的資產連結至本身的內容，來盜用該網站擁有的靜態資產。</span><span class="sxs-lookup"><span data-stu-id="3728e-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="3728e-116">URL 重寫可能會降低應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="3728e-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="3728e-117">如果可行的話，請限制規則的數目與複雜程度。</span><span class="sxs-lookup"><span data-stu-id="3728e-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="3728e-118">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3728e-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="3728e-119">URL 重新導向和 URL 重寫</span><span class="sxs-lookup"><span data-stu-id="3728e-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="3728e-120">乍看之下，「URL 重新導向」和「URL 重寫」在用語上的差異不太明顯，但卻對提供資源給用戶端方面有重要的影響。</span><span class="sxs-lookup"><span data-stu-id="3728e-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="3728e-121">ASP.NET Core 的 URL 重寫中介軟體可以同時滿足這兩種需求。</span><span class="sxs-lookup"><span data-stu-id="3728e-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="3728e-122">「URL 重新導向」牽涉有關用戶端的作業，其會指示用戶端到其他位址存取資源，而非用戶端原本要求的位址。</span><span class="sxs-lookup"><span data-stu-id="3728e-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="3728e-123">這項作業需要伺服器的來回行程。</span><span class="sxs-lookup"><span data-stu-id="3728e-123">This requires a round trip to the server.</span></span> <span data-ttu-id="3728e-124">當用戶端提出新的資源要求時，系統會將重新導向 URL 傳回給用戶端，並顯示在瀏覽器的網址列中。</span><span class="sxs-lookup"><span data-stu-id="3728e-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="3728e-125">若 `/resource` 重新導向 至 `/different-resource`，則伺服器會回應用戶端應前往 `/different-resource` 取得資源，並提供狀態碼，指出重新導向為暫時性或永久性。</span><span class="sxs-lookup"><span data-stu-id="3728e-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![伺服器上的 WebAPI 服務端點已暫時從第 1 版 (v1) 變更為第 2 版 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="3728e-131">將要求重新導向至不同的 URL 時，可以透過指定具狀態的回應，來指出該重新導向為永久性或暫時性：</span><span class="sxs-lookup"><span data-stu-id="3728e-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="3728e-132">如果資源具有新的永久 URL，且您希望告知用戶端所有未來的資源要求皆應使用這個新 URL，請使用 301 (已永久移動) 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="3728e-133">用戶端收到 301 狀態碼時，可能會快取並重複使用回應。</span><span class="sxs-lookup"><span data-stu-id="3728e-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="3728e-134">若重新導向為暫時性或很有可能變更時，請使用 302 (已找到) 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="3728e-135">302 狀態碼會指示用戶端不要儲存 URL 並在之後使用。</span><span class="sxs-lookup"><span data-stu-id="3728e-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="3728e-136">如需狀態碼的詳細資訊，請參閱 [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616：狀態碼定義)。</span><span class="sxs-lookup"><span data-stu-id="3728e-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="3728e-137">「URL 重寫」伺服器端作業，會從用戶端要求以外的其他資源位址提供資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="3728e-138">重寫 URL 並不需要伺服器的來回行程。</span><span class="sxs-lookup"><span data-stu-id="3728e-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="3728e-139">系統不會將重寫的 URL 傳回用戶端，也不會顯示在瀏覽器的網址列中。</span><span class="sxs-lookup"><span data-stu-id="3728e-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="3728e-140">若 `/resource`「重寫」至 `/different-resource`，則伺服器會於「內部」擷取資源，並在 `/different-resource` 傳回資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="3728e-141">雖然用戶端或許可以在重寫的 URL 處擷取資源，但用戶端並不會在提出要求與接收回應時得知資源位於重寫的 URL 處。</span><span class="sxs-lookup"><span data-stu-id="3728e-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![伺服器上的 WebAPI 服務端點已從第 1 版 (v1) 變更為第 2 版 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="3728e-146">URL 重寫範例應用程式</span><span class="sxs-lookup"><span data-stu-id="3728e-146">URL rewriting sample app</span></span>

<span data-ttu-id="3728e-147">您可以利用[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/)，來探索 URL 重寫中介軟體的功能。</span><span class="sxs-lookup"><span data-stu-id="3728e-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="3728e-148">範例應用程式會套用重新導向與重寫規則，還會示範多種情況下的重新導向或重寫 URL。</span><span class="sxs-lookup"><span data-stu-id="3728e-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="3728e-149">URL 重寫中介軟體的使用時機</span><span class="sxs-lookup"><span data-stu-id="3728e-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="3728e-150">當您無法使用以下方法時，請使用重寫中介軟體：</span><span class="sxs-lookup"><span data-stu-id="3728e-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="3728e-151">Windows Server 上的 IIS URL Rewrite module</span><span class="sxs-lookup"><span data-stu-id="3728e-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="3728e-152">Apache Server 上的 Apache mod_rewrite 模組</span><span class="sxs-lookup"><span data-stu-id="3728e-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="3728e-153">Nginx 上的 URL 重寫</span><span class="sxs-lookup"><span data-stu-id="3728e-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="3728e-154">此外也請在應用程式裝載於 [HTTP.sys 伺服器](xref:fundamentals/servers/httpsys) (之前稱為 WebListener) 時使用中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3728e-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="3728e-155">使用 IIS、Apache 和 Nginx 所提供伺服器型 URL 重寫技術的主要原因包括：</span><span class="sxs-lookup"><span data-stu-id="3728e-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="3728e-156">中介軟體不支援這些模組的完整功能。</span><span class="sxs-lookup"><span data-stu-id="3728e-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="3728e-157">有部分伺服器模組的功能不適用於 ASP.NET Core 專案，例如 IIS Rewrite Module 規則的 `IsFile` 和 `IsDirectory` 條件約束。</span><span class="sxs-lookup"><span data-stu-id="3728e-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="3728e-158">在這些情況下，請改為使用中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3728e-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="3728e-159">中介軟體的效能可能比不上模組的效能時。</span><span class="sxs-lookup"><span data-stu-id="3728e-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="3728e-160">如果要確實得知哪種方法會降低最多效能，或是降低的效能可以忽略的話，進行效能評定是唯一方法。</span><span class="sxs-lookup"><span data-stu-id="3728e-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="3728e-161">套件</span><span class="sxs-lookup"><span data-stu-id="3728e-161">Package</span></span>

<span data-ttu-id="3728e-162">URL 重寫中介軟體由 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 套件所提供，其會以隱含方式包含在 ASP.NET Core 應用程式中。</span><span class="sxs-lookup"><span data-stu-id="3728e-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="3728e-163">延伸模組和選項</span><span class="sxs-lookup"><span data-stu-id="3728e-163">Extension and options</span></span>

<span data-ttu-id="3728e-164">若要建立 URL 重寫和重新導向規則，您可以針對每個重寫規則的擴充方法建立 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="3728e-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="3728e-165">依據所需的處理順序來鏈結多個規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="3728e-166">系統會使用 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 將 `RewriteOptions` 新增至要求管線，並將其傳入 URL 重寫中介軟體：</span><span class="sxs-lookup"><span data-stu-id="3728e-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="3728e-167">將非 www 重新導向 www</span><span class="sxs-lookup"><span data-stu-id="3728e-167">Redirect non-www to www</span></span>

<span data-ttu-id="3728e-168">有三個選項可讓應用程式將非 `www` 要求重新導向 `www`：</span><span class="sxs-lookup"><span data-stu-id="3728e-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="3728e-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>：如果要求為非，則將要求永久重新導向至 `www` 子域 `www` 。</span><span class="sxs-lookup"><span data-stu-id="3728e-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="3728e-170">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 狀態代碼重新導向。</span><span class="sxs-lookup"><span data-stu-id="3728e-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="3728e-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>： `www` 如果連入要求為非，則將要求重新導向至子域 `www` 。</span><span class="sxs-lookup"><span data-stu-id="3728e-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="3728e-172">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 狀態代碼重新導向。</span><span class="sxs-lookup"><span data-stu-id="3728e-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="3728e-173">多載可讓您提供回應的狀態代碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="3728e-174">請使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 類別的欄位來進行狀態碼指派。</span><span class="sxs-lookup"><span data-stu-id="3728e-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="3728e-175">URL 重新導向</span><span class="sxs-lookup"><span data-stu-id="3728e-175">URL redirect</span></span>

<span data-ttu-id="3728e-176">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 將要求重新導向。</span><span class="sxs-lookup"><span data-stu-id="3728e-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="3728e-177">第一個參數會包含您的 Regex，以比對傳入的 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="3728e-178">第二個參數是取代字串。</span><span class="sxs-lookup"><span data-stu-id="3728e-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="3728e-179">第三個參數 (如果有的話) 會指定狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="3728e-180">如果您未指定狀態碼，則狀態碼會預設為 302 (已找到)，表示已暫時移動或取代資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="3728e-181">在啟用開發人員工具的瀏覽器中，使用 `/redirect-rule/1234/5678` 路徑提出範例應用程式的要求。</span><span class="sxs-lookup"><span data-stu-id="3728e-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="3728e-182">Regex 會比對 `redirect-rule/(.*)` 上的要求路徑，並會將路徑取代為 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="3728e-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="3728e-183">系統會將重新導向 URL 與 302 (已找到) 狀態碼傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3728e-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="3728e-184">瀏覽器在重新導向 URL 處提出新要求，該 URL 也會顯示在瀏覽器的網址列中。</span><span class="sxs-lookup"><span data-stu-id="3728e-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="3728e-185">因為範例應用程式與重新導向 URL 沒有任何相符規則：</span><span class="sxs-lookup"><span data-stu-id="3728e-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="3728e-186">第二個要求會從應用程式收到 200 (確定) 回應。</span><span class="sxs-lookup"><span data-stu-id="3728e-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="3728e-187">回應的本文會顯示重新導向 URL。</span><span class="sxs-lookup"><span data-stu-id="3728e-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="3728e-188">「重新導向」URL 時，會對伺服器進行來回行程。</span><span class="sxs-lookup"><span data-stu-id="3728e-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="3728e-189">建立重新導向規則時，請務必謹慎。</span><span class="sxs-lookup"><span data-stu-id="3728e-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="3728e-190">每向應用程式提出要求時 (包括重新導向後)，請評估您的重新導向規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="3728e-191">因為您有可能會不小心建立無限重新導向迴圈。</span><span class="sxs-lookup"><span data-stu-id="3728e-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="3728e-192">原始要求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="3728e-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="3728e-194">括弧內所含的運算式部分稱為「擷取群組」。</span><span class="sxs-lookup"><span data-stu-id="3728e-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="3728e-195">運算式的點 (`.`) 表示「比對任何字元」。</span><span class="sxs-lookup"><span data-stu-id="3728e-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="3728e-196">星號 (`*`) 表示「比對前置字元零或多次」。</span><span class="sxs-lookup"><span data-stu-id="3728e-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="3728e-197">因此，擷取群組 `(.*)` 會擷取 URL 的 `1234/5678` 最後這兩個路徑區段。</span><span class="sxs-lookup"><span data-stu-id="3728e-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="3728e-198">這個單一擷取群組會擷取您在要求 URL 中的 `redirect-rule/` 之後所提供的任何值。</span><span class="sxs-lookup"><span data-stu-id="3728e-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="3728e-199">系統會將擷取的群組以貨幣符號 (`$`) 後接擷取序號的形式，插入取代字串中。</span><span class="sxs-lookup"><span data-stu-id="3728e-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="3728e-200">使用 `$1` 可取得第一個擷取群組值、使用 `$2` 則會取得第二個，依您的 Regex 擷取群組順序以此類推。</span><span class="sxs-lookup"><span data-stu-id="3728e-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="3728e-201">在範例應用程式中，重新導向規則 Regex 只有一個擷取的群組，因此在取代字串中只有 `$1` 這一個插入的群組。</span><span class="sxs-lookup"><span data-stu-id="3728e-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="3728e-202">套用規則時，URL 會變成 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="3728e-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="3728e-203">將 URL 重新導向至安全端點</span><span class="sxs-lookup"><span data-stu-id="3728e-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="3728e-204">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 將 HTTP 要求重新導向至使用 HTTPS 通訊協定的相同主機與路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="3728e-205">如果未提供狀態碼，中介軟體會預設為 302 (已找到)。</span><span class="sxs-lookup"><span data-stu-id="3728e-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="3728e-206">若未提供連接埠：</span><span class="sxs-lookup"><span data-stu-id="3728e-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="3728e-207">中介軟體會預設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="3728e-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="3728e-208">配置會變更為 `https` (HTTPS 通訊協定)，且用戶端會在連接埠 443 上存取資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="3728e-209">下列範例示範了如何將狀態碼設為 301 (已永久移動)，並將連接埠變更為 5001。</span><span class="sxs-lookup"><span data-stu-id="3728e-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="3728e-210">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 將不安全的要求重新導向至使用安全 HTTPS 通訊協定 (在連接埠 443 上) 的相同主機與路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="3728e-211">中介軟體會將狀態碼設定為 301 (已永久移動)。</span><span class="sxs-lookup"><span data-stu-id="3728e-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="3728e-212">在不要求額外重新導向規則的情況下重新導向至安全的端點時，建議使用 HTTPS 重新導向中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3728e-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="3728e-213">如需詳細資訊，請參閱[強制執行 HTTPS](xref:security/enforcing-ssl#require-https) 主題。</span><span class="sxs-lookup"><span data-stu-id="3728e-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="3728e-214">範例應用程式可以示範如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="3728e-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="3728e-215">將擴充方法新增至 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="3728e-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="3728e-216">在任何 URL 位置，向應用程式提出不安全的要求。</span><span class="sxs-lookup"><span data-stu-id="3728e-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="3728e-217">關閉瀏覽器自我簽署憑證不受信任的安全性警告，或建立信任憑證的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3728e-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="3728e-218">使用 `AddRedirectToHttps(301, 5001)` 的原始要求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="3728e-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="3728e-220">使用 `AddRedirectToHttpsPermanent` 的原始要求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="3728e-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="3728e-222">URL 重寫</span><span class="sxs-lookup"><span data-stu-id="3728e-222">URL rewrite</span></span>

<span data-ttu-id="3728e-223">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 來建立 URL 重寫的規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="3728e-224">第一個參數會包含 Regex，以比對傳入的 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="3728e-225">第二個參數是取代字串。</span><span class="sxs-lookup"><span data-stu-id="3728e-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="3728e-226">第三個參數 `skipRemainingRules: {true|false}` 指出中介軟體是否要在套用目前規則時略過其他重寫規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="3728e-227">原始要求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="3728e-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="3728e-229">運算式開頭的插入號 (`^`) 表示，比對會從 URL 路徑的開頭開始。</span><span class="sxs-lookup"><span data-stu-id="3728e-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="3728e-230">在先前的重新導向規則範例 `redirect-rule/(.*)` 中，Regex 的開頭沒有插入號 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="3728e-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="3728e-231">因此，就算 `redirect-rule/` 前有任何字元也能成功比對。</span><span class="sxs-lookup"><span data-stu-id="3728e-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="3728e-232">路徑</span><span class="sxs-lookup"><span data-stu-id="3728e-232">Path</span></span>                               | <span data-ttu-id="3728e-233">比對</span><span class="sxs-lookup"><span data-stu-id="3728e-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="3728e-234">是</span><span class="sxs-lookup"><span data-stu-id="3728e-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="3728e-235">是</span><span class="sxs-lookup"><span data-stu-id="3728e-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="3728e-236">是</span><span class="sxs-lookup"><span data-stu-id="3728e-236">Yes</span></span>   |

<span data-ttu-id="3728e-237">`^rewrite-rule/(\d+)/(\d+)` 重寫規則只會比對開頭為 `rewrite-rule/` 的路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="3728e-238">請注意下表中的比對差異。</span><span class="sxs-lookup"><span data-stu-id="3728e-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="3728e-239">路徑</span><span class="sxs-lookup"><span data-stu-id="3728e-239">Path</span></span>                              | <span data-ttu-id="3728e-240">比對</span><span class="sxs-lookup"><span data-stu-id="3728e-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="3728e-241">是</span><span class="sxs-lookup"><span data-stu-id="3728e-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="3728e-242">否</span><span class="sxs-lookup"><span data-stu-id="3728e-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="3728e-243">否</span><span class="sxs-lookup"><span data-stu-id="3728e-243">No</span></span>    |

<span data-ttu-id="3728e-244">運算式的 `^rewrite-rule/` 部分之後，有 `(\d+)/(\d+)` 這兩個擷取群組。</span><span class="sxs-lookup"><span data-stu-id="3728e-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="3728e-245">`\d` 表示「比對數字」。</span><span class="sxs-lookup"><span data-stu-id="3728e-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="3728e-246">加號 (`+`) 表示「比對一或多個前置字元」。</span><span class="sxs-lookup"><span data-stu-id="3728e-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="3728e-247">因此，URL 必須包含某個數字，後接斜線與另一個數字。</span><span class="sxs-lookup"><span data-stu-id="3728e-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="3728e-248">這些擷取群組會以 `$1` 和 `$2` 形式插入重寫的 URL。</span><span class="sxs-lookup"><span data-stu-id="3728e-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="3728e-249">重寫規則的取代字串會將擷取的群組放入查詢字串中。</span><span class="sxs-lookup"><span data-stu-id="3728e-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="3728e-250">系統會重寫 `/rewrite-rule/1234/5678` 的要求路徑，以取得位於 `/rewritten?var1=1234&var2=5678` 的資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="3728e-251">如果原始要求上有查詢字串，則會在重寫 URL 時予以保留。</span><span class="sxs-lookup"><span data-stu-id="3728e-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="3728e-252">這麼做就不需要伺服器的來回行程，即可取得資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="3728e-253">如果資源存在，就會擷取該資源，並傳回 200 (確定) 狀態碼給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3728e-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="3728e-254">因為系統並未重新導向用戶端，因此瀏覽器網址列中的 URL 不會變更。</span><span class="sxs-lookup"><span data-stu-id="3728e-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="3728e-255">用戶端也無法偵測到伺服器上發生 URL 重寫作業。</span><span class="sxs-lookup"><span data-stu-id="3728e-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="3728e-256">因為比對規則是計算繁複的程序，且會增加應用程式的回應時間，所以請盡可能使用 `skipRemainingRules: true`。</span><span class="sxs-lookup"><span data-stu-id="3728e-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="3728e-257">如需最快速的應用程式回應：</span><span class="sxs-lookup"><span data-stu-id="3728e-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="3728e-258">排序重寫規則；從最常比對的規則排到最不常比對的規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="3728e-259">當出現符合項目且不需要任何額外的規則處理時，即略過剩下的規則處理。</span><span class="sxs-lookup"><span data-stu-id="3728e-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="3728e-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="3728e-260">Apache mod_rewrite</span></span>

<span data-ttu-id="3728e-261">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 來套用 Apache mod_rewrite 規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="3728e-262">請確認應用程式已部署規則檔。</span><span class="sxs-lookup"><span data-stu-id="3728e-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="3728e-263">如需 mod_rewrite 規則的詳細資訊和範例，請參閱 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="3728e-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="3728e-264">系統會使用 <xref:System.IO.StreamReader> 來讀取 *ApacheModRewrite.txt* 規則檔案的規則：</span><span class="sxs-lookup"><span data-stu-id="3728e-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="3728e-265">範例應用程式會將要求從 `/apache-mod-rules-redirect/(.\*)` 重新導向至 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="3728e-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="3728e-266">回應狀態碼為 302 (已找到)。</span><span class="sxs-lookup"><span data-stu-id="3728e-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="3728e-267">原始要求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="3728e-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="3728e-269">中介軟體可支援下列 Apache mod_rewrite 伺服器變數：</span><span class="sxs-lookup"><span data-stu-id="3728e-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="3728e-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="3728e-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="3728e-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="3728e-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="3728e-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="3728e-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="3728e-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="3728e-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="3728e-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="3728e-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="3728e-275">HTTP_HOST</span></span>
* <span data-ttu-id="3728e-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="3728e-276">HTTP_REFERER</span></span>
* <span data-ttu-id="3728e-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="3728e-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="3728e-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3728e-278">HTTPS</span></span>
* <span data-ttu-id="3728e-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="3728e-279">IPV6</span></span>
* <span data-ttu-id="3728e-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="3728e-280">QUERY_STRING</span></span>
* <span data-ttu-id="3728e-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="3728e-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="3728e-282">REMOTE_PORT</span></span>
* <span data-ttu-id="3728e-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="3728e-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="3728e-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="3728e-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="3728e-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="3728e-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="3728e-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="3728e-286">REQUEST_URI</span></span>
* <span data-ttu-id="3728e-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="3728e-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="3728e-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-288">SERVER_ADDR</span></span>
* <span data-ttu-id="3728e-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="3728e-289">SERVER_PORT</span></span>
* <span data-ttu-id="3728e-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="3728e-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="3728e-291">TIME</span><span class="sxs-lookup"><span data-stu-id="3728e-291">TIME</span></span>
* <span data-ttu-id="3728e-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="3728e-292">TIME_DAY</span></span>
* <span data-ttu-id="3728e-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="3728e-293">TIME_HOUR</span></span>
* <span data-ttu-id="3728e-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="3728e-294">TIME_MIN</span></span>
* <span data-ttu-id="3728e-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="3728e-295">TIME_MON</span></span>
* <span data-ttu-id="3728e-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="3728e-296">TIME_SEC</span></span>
* <span data-ttu-id="3728e-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="3728e-297">TIME_WDAY</span></span>
* <span data-ttu-id="3728e-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="3728e-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="3728e-299">IIS URL Rewrite Module 規則</span><span class="sxs-lookup"><span data-stu-id="3728e-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="3728e-300">若要使用與套用至 IIS URL Rewrite Module 一樣的規則集，請使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="3728e-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="3728e-301">請確認應用程式已部署規則檔。</span><span class="sxs-lookup"><span data-stu-id="3728e-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="3728e-302">在 Windows Server IIS 上執行時，請不要引導中介軟體使用應用程式的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3728e-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="3728e-303">使用 IIS 時，這些規則應該儲存在應用程式的 *web.config* 檔案外部，以免與 IIS Rewrite Module 發生衝突。</span><span class="sxs-lookup"><span data-stu-id="3728e-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="3728e-304">如需 IIS URL Rewrite Module 規則的詳細資訊和範例，請參閱 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (使用 URL Rewrite Module 2.0) 和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (URL Rewrite Module 組態參考)。</span><span class="sxs-lookup"><span data-stu-id="3728e-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="3728e-305">系統會使用 <xref:System.IO.StreamReader> 來讀取 *IISUrlRewrite.xml* 規則檔案的規則：</span><span class="sxs-lookup"><span data-stu-id="3728e-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="3728e-306">範例應用程式會將要求從 `/iis-rules-rewrite/(.*)` 重寫至 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="3728e-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="3728e-307">系統會將回應與 200 (確定) 狀態碼傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3728e-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="3728e-308">原始要求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="3728e-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="3728e-310">如果您具備使用中且已設定伺服器層級規則的 IIS Rewrite Module，但其會以非預期的方式影響應用程式，則您可以停用應用程式的 IIS Rewrite Module 。</span><span class="sxs-lookup"><span data-stu-id="3728e-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="3728e-311">如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="3728e-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="3728e-312">不支援的功能</span><span class="sxs-lookup"><span data-stu-id="3728e-312">Unsupported features</span></span>

<span data-ttu-id="3728e-313">中介軟體不支援下列 IIS URL 重寫模組功能：</span><span class="sxs-lookup"><span data-stu-id="3728e-313">The middleware doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="3728e-314">輸出規則</span><span class="sxs-lookup"><span data-stu-id="3728e-314">Outbound Rules</span></span>
* <span data-ttu-id="3728e-315">自訂伺服器變數</span><span class="sxs-lookup"><span data-stu-id="3728e-315">Custom Server Variables</span></span>
* <span data-ttu-id="3728e-316">萬用字元</span><span class="sxs-lookup"><span data-stu-id="3728e-316">Wildcards</span></span>
* <span data-ttu-id="3728e-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="3728e-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="3728e-318">支援的伺服器變數</span><span class="sxs-lookup"><span data-stu-id="3728e-318">Supported server variables</span></span>

<span data-ttu-id="3728e-319">中介軟體可支援下列 IIS URL Rewrite Module 伺服器變數：</span><span class="sxs-lookup"><span data-stu-id="3728e-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="3728e-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="3728e-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="3728e-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="3728e-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="3728e-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="3728e-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="3728e-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="3728e-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="3728e-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="3728e-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="3728e-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="3728e-325">HTTP_HOST</span></span>
* <span data-ttu-id="3728e-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="3728e-326">HTTP_REFERER</span></span>
* <span data-ttu-id="3728e-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="3728e-327">HTTP_URL</span></span>
* <span data-ttu-id="3728e-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="3728e-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="3728e-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3728e-329">HTTPS</span></span>
* <span data-ttu-id="3728e-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="3728e-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="3728e-331">QUERY_STRING</span></span>
* <span data-ttu-id="3728e-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="3728e-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="3728e-333">REMOTE_PORT</span></span>
* <span data-ttu-id="3728e-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="3728e-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="3728e-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="3728e-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="3728e-336">您也可以透過 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 取得 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="3728e-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="3728e-337">這種方法可讓重寫規則檔案的位置更有彈性。</span><span class="sxs-lookup"><span data-stu-id="3728e-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="3728e-338">請確認重寫規則檔案已部署到您所提供之路徑的伺服器。</span><span class="sxs-lookup"><span data-stu-id="3728e-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="3728e-339">以方法為基礎的規則</span><span class="sxs-lookup"><span data-stu-id="3728e-339">Method-based rule</span></span>

<span data-ttu-id="3728e-340">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 以在方法中實作您自己的規則邏輯。</span><span class="sxs-lookup"><span data-stu-id="3728e-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="3728e-341">`Add` 會公開 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用於您的方法中。</span><span class="sxs-lookup"><span data-stu-id="3728e-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="3728e-342">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)可判斷其他管線處理的執行方式。</span><span class="sxs-lookup"><span data-stu-id="3728e-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="3728e-343">請將值設定為下表中描述的其中一個 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 欄位。</span><span class="sxs-lookup"><span data-stu-id="3728e-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="3728e-344">重寫內容結果</span><span class="sxs-lookup"><span data-stu-id="3728e-344">Rewrite context result</span></span>               | <span data-ttu-id="3728e-345">動作</span><span class="sxs-lookup"><span data-stu-id="3728e-345">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="3728e-346">`RuleResult.ContinueRules` (預設)</span><span class="sxs-lookup"><span data-stu-id="3728e-346">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="3728e-347">繼續套用規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-347">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="3728e-348">停止套用規則，並傳送回應。</span><span class="sxs-lookup"><span data-stu-id="3728e-348">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="3728e-349">停止套用規則，並將內容傳送至下一個中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3728e-349">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="3728e-350">範例應用程式會示範一個方法，以重新導向對 *.xml* 結尾之路徑的要求。</span><span class="sxs-lookup"><span data-stu-id="3728e-350">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="3728e-351">若要求是針對 `/file.xml` 發出，則要求會重新導向至 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="3728e-351">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="3728e-352">狀態碼會設定為 301 (已永久移動)。</span><span class="sxs-lookup"><span data-stu-id="3728e-352">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="3728e-353">當瀏覽器針對 */xmlfiles/file.xml* 發出新的要求時，靜態檔案中介軟體會從 *wwwroot/xmlfiles* 資料夾將檔案提供給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3728e-353">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="3728e-354">若要重新導向，請明確設定回應的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-354">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="3728e-355">否則會傳回 200 (確定) 狀態碼，用戶端上也不會發生重新導向。</span><span class="sxs-lookup"><span data-stu-id="3728e-355">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="3728e-356">*RewriteRules.cs*：</span><span class="sxs-lookup"><span data-stu-id="3728e-356">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="3728e-357">這種方法也能重寫要求。</span><span class="sxs-lookup"><span data-stu-id="3728e-357">This approach can also rewrite requests.</span></span> <span data-ttu-id="3728e-358">範例應用程式示範了如何重寫任何文字檔要求的路徑，以從 *wwwroot* 資料夾提供 *file.txt* 文字檔。</span><span class="sxs-lookup"><span data-stu-id="3728e-358">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="3728e-359">靜態檔案中介軟體會根據更新的要求路徑來提供檔案：</span><span class="sxs-lookup"><span data-stu-id="3728e-359">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="3728e-360">*RewriteRules.cs*：</span><span class="sxs-lookup"><span data-stu-id="3728e-360">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="3728e-361">以 IRule 為基礎的規則</span><span class="sxs-lookup"><span data-stu-id="3728e-361">IRule-based rule</span></span>

<span data-ttu-id="3728e-362">利用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 來使用類別中實作 <xref:Microsoft.AspNetCore.Rewrite.IRule> 介面的專屬規則邏輯。</span><span class="sxs-lookup"><span data-stu-id="3728e-362">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="3728e-363">相較於使用以方法為基礎的規則方法，`IRule` 提供了更高彈性。</span><span class="sxs-lookup"><span data-stu-id="3728e-363">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="3728e-364">您的實作類別可以包含建構函式，以便您在其中傳入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的參數。</span><span class="sxs-lookup"><span data-stu-id="3728e-364">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="3728e-365">為了滿足若干條件，系統會檢查範例應用程式中的 `extension` 和 `newPath` 參數值。</span><span class="sxs-lookup"><span data-stu-id="3728e-365">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="3728e-366">`extension` 必須包含值，而且值必須是 *.png*、*.jpg* 或 *.gif*。</span><span class="sxs-lookup"><span data-stu-id="3728e-366">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="3728e-367">如果 `newPath` 無效，就會擲回 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="3728e-367">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="3728e-368">若要求是針對 *image.png* 發出，則要求會重新導向至 `/png-images/image.png`。</span><span class="sxs-lookup"><span data-stu-id="3728e-368">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="3728e-369">若要求是針對 *image.jpg* 發出，則要求會重新導向至 `/jpg-images/image.jpg`。</span><span class="sxs-lookup"><span data-stu-id="3728e-369">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="3728e-370">狀態碼會設定為 301 (已永久移動)，而 `context.Result` 會設定為停止處理規則，並傳送回應。</span><span class="sxs-lookup"><span data-stu-id="3728e-370">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="3728e-371">原始要求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="3728e-371">Original Request: `/image.png`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤 image.png 的要求和回應](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="3728e-373">原始要求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="3728e-373">Original Request: `/image.jpg`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤 image.jpg 的要求和回應](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="3728e-375">Regex 範例</span><span class="sxs-lookup"><span data-stu-id="3728e-375">Regex examples</span></span>

| <span data-ttu-id="3728e-376">目標</span><span class="sxs-lookup"><span data-stu-id="3728e-376">Goal</span></span> | <span data-ttu-id="3728e-377">Regex 字串及</span><span class="sxs-lookup"><span data-stu-id="3728e-377">Regex String &</span></span><br><span data-ttu-id="3728e-378">比對範例</span><span class="sxs-lookup"><span data-stu-id="3728e-378">Match Example</span></span> | <span data-ttu-id="3728e-379">取代字串及</span><span class="sxs-lookup"><span data-stu-id="3728e-379">Replacement String &</span></span><br><span data-ttu-id="3728e-380">輸出範例</span><span class="sxs-lookup"><span data-stu-id="3728e-380">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="3728e-381">將路徑重寫成查詢字串</span><span class="sxs-lookup"><span data-stu-id="3728e-381">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="3728e-382">移除斜線</span><span class="sxs-lookup"><span data-stu-id="3728e-382">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="3728e-383">強制使用斜線</span><span class="sxs-lookup"><span data-stu-id="3728e-383">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="3728e-384">避免重寫特定的要求</span><span class="sxs-lookup"><span data-stu-id="3728e-384">Avoid rewriting specific requests</span></span> | <span data-ttu-id="3728e-385">`^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="3728e-385">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="3728e-386">是：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="3728e-386">Yes: `/resource.htm`</span></span><br><span data-ttu-id="3728e-387">否：`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="3728e-387">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="3728e-388">重新排列 URL 區段</span><span class="sxs-lookup"><span data-stu-id="3728e-388">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="3728e-389">取代 URL 區段</span><span class="sxs-lookup"><span data-stu-id="3728e-389">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3728e-390">本文件介紹 URL 重寫，並提供如何在 ASP.NET Core 應用程式中使用 URL 重寫中介軟體的指示。</span><span class="sxs-lookup"><span data-stu-id="3728e-390">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="3728e-391">URL 重寫是指根據一或多個預先定義的規則來修改要求 URL 的動作。</span><span class="sxs-lookup"><span data-stu-id="3728e-391">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="3728e-392">URL 重寫會在資源位置和位址之間建立一個抽象層，讓位置和位址不那麼緊密連結。</span><span class="sxs-lookup"><span data-stu-id="3728e-392">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="3728e-393">下列幾種情況非常適合使用 URL 重寫：</span><span class="sxs-lookup"><span data-stu-id="3728e-393">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="3728e-394">暫時或永久性移動/取代伺服器資源，並為這些資源保持穩定的定位器時。</span><span class="sxs-lookup"><span data-stu-id="3728e-394">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="3728e-395">跨不同應用程式或跨單一應用程式各區域來分割要求處理作業。</span><span class="sxs-lookup"><span data-stu-id="3728e-395">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="3728e-396">移除、新增或重組傳入要求的 URL 區段。</span><span class="sxs-lookup"><span data-stu-id="3728e-396">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="3728e-397">針對搜尋引擎最佳化 (SEO) 來將公開 URL 最佳化。</span><span class="sxs-lookup"><span data-stu-id="3728e-397">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="3728e-398">允許使用易懂的公開 URL，來協助訪客預測要求資源所傳回的內容。</span><span class="sxs-lookup"><span data-stu-id="3728e-398">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="3728e-399">將不安全的要求重新導向至安全的端點。</span><span class="sxs-lookup"><span data-stu-id="3728e-399">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="3728e-400">防止直接連結，即外部網站透過將另一個網站的資產連結至本身的內容，來盜用該網站擁有的靜態資產。</span><span class="sxs-lookup"><span data-stu-id="3728e-400">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="3728e-401">URL 重寫可能會降低應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="3728e-401">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="3728e-402">如果可行的話，請限制規則的數目與複雜程度。</span><span class="sxs-lookup"><span data-stu-id="3728e-402">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="3728e-403">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3728e-403">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="3728e-404">URL 重新導向和 URL 重寫</span><span class="sxs-lookup"><span data-stu-id="3728e-404">URL redirect and URL rewrite</span></span>

<span data-ttu-id="3728e-405">乍看之下，「URL 重新導向」和「URL 重寫」在用語上的差異不太明顯，但卻對提供資源給用戶端方面有重要的影響。</span><span class="sxs-lookup"><span data-stu-id="3728e-405">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="3728e-406">ASP.NET Core 的 URL 重寫中介軟體可以同時滿足這兩種需求。</span><span class="sxs-lookup"><span data-stu-id="3728e-406">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="3728e-407">「URL 重新導向」牽涉有關用戶端的作業，其會指示用戶端到其他位址存取資源，而非用戶端原本要求的位址。</span><span class="sxs-lookup"><span data-stu-id="3728e-407">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="3728e-408">這項作業需要伺服器的來回行程。</span><span class="sxs-lookup"><span data-stu-id="3728e-408">This requires a round trip to the server.</span></span> <span data-ttu-id="3728e-409">當用戶端提出新的資源要求時，系統會將重新導向 URL 傳回給用戶端，並顯示在瀏覽器的網址列中。</span><span class="sxs-lookup"><span data-stu-id="3728e-409">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="3728e-410">若 `/resource` 重新導向 至 `/different-resource`，則伺服器會回應用戶端應前往 `/different-resource` 取得資源，並提供狀態碼，指出重新導向為暫時性或永久性。</span><span class="sxs-lookup"><span data-stu-id="3728e-410">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![伺服器上的 WebAPI 服務端點已暫時從第 1 版 (v1) 變更為第 2 版 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="3728e-416">將要求重新導向至不同的 URL 時，可以透過指定具狀態的回應，來指出該重新導向為永久性或暫時性：</span><span class="sxs-lookup"><span data-stu-id="3728e-416">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="3728e-417">如果資源具有新的永久 URL，且您希望告知用戶端所有未來的資源要求皆應使用這個新 URL，請使用 301 (已永久移動) 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-417">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="3728e-418">用戶端收到 301 狀態碼時，可能會快取並重複使用回應。</span><span class="sxs-lookup"><span data-stu-id="3728e-418">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="3728e-419">若重新導向為暫時性或很有可能變更時，請使用 302 (已找到) 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-419">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="3728e-420">302 狀態碼會指示用戶端不要儲存 URL 並在之後使用。</span><span class="sxs-lookup"><span data-stu-id="3728e-420">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="3728e-421">如需狀態碼的詳細資訊，請參閱 [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616：狀態碼定義)。</span><span class="sxs-lookup"><span data-stu-id="3728e-421">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="3728e-422">「URL 重寫」伺服器端作業，會從用戶端要求以外的其他資源位址提供資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-422">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="3728e-423">重寫 URL 並不需要伺服器的來回行程。</span><span class="sxs-lookup"><span data-stu-id="3728e-423">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="3728e-424">系統不會將重寫的 URL 傳回用戶端，也不會顯示在瀏覽器的網址列中。</span><span class="sxs-lookup"><span data-stu-id="3728e-424">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="3728e-425">若 `/resource`「重寫」至 `/different-resource`，則伺服器會於「內部」擷取資源，並在 `/different-resource` 傳回資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-425">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="3728e-426">雖然用戶端或許可以在重寫的 URL 處擷取資源，但用戶端並不會在提出要求與接收回應時得知資源位於重寫的 URL 處。</span><span class="sxs-lookup"><span data-stu-id="3728e-426">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![伺服器上的 WebAPI 服務端點已從第 1 版 (v1) 變更為第 2 版 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="3728e-431">URL 重寫範例應用程式</span><span class="sxs-lookup"><span data-stu-id="3728e-431">URL rewriting sample app</span></span>

<span data-ttu-id="3728e-432">您可以利用[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/)，來探索 URL 重寫中介軟體的功能。</span><span class="sxs-lookup"><span data-stu-id="3728e-432">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="3728e-433">範例應用程式會套用重新導向與重寫規則，還會示範多種情況下的重新導向或重寫 URL。</span><span class="sxs-lookup"><span data-stu-id="3728e-433">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="3728e-434">URL 重寫中介軟體的使用時機</span><span class="sxs-lookup"><span data-stu-id="3728e-434">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="3728e-435">當您無法使用以下方法時，請使用重寫中介軟體：</span><span class="sxs-lookup"><span data-stu-id="3728e-435">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="3728e-436">Windows Server 上的 IIS URL Rewrite module</span><span class="sxs-lookup"><span data-stu-id="3728e-436">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="3728e-437">Apache Server 上的 Apache mod_rewrite 模組</span><span class="sxs-lookup"><span data-stu-id="3728e-437">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="3728e-438">Nginx 上的 URL 重寫</span><span class="sxs-lookup"><span data-stu-id="3728e-438">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="3728e-439">此外也請在應用程式裝載於 [HTTP.sys 伺服器](xref:fundamentals/servers/httpsys) (之前稱為 WebListener) 時使用中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3728e-439">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="3728e-440">使用 IIS、Apache 和 Nginx 所提供伺服器型 URL 重寫技術的主要原因包括：</span><span class="sxs-lookup"><span data-stu-id="3728e-440">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="3728e-441">中介軟體不支援這些模組的完整功能。</span><span class="sxs-lookup"><span data-stu-id="3728e-441">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="3728e-442">有部分伺服器模組的功能不適用於 ASP.NET Core 專案，例如 IIS Rewrite Module 規則的 `IsFile` 和 `IsDirectory` 條件約束。</span><span class="sxs-lookup"><span data-stu-id="3728e-442">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="3728e-443">在這些情況下，請改為使用中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3728e-443">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="3728e-444">中介軟體的效能可能比不上模組的效能時。</span><span class="sxs-lookup"><span data-stu-id="3728e-444">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="3728e-445">如果要確實得知哪種方法會降低最多效能，或是降低的效能可以忽略的話，進行效能評定是唯一方法。</span><span class="sxs-lookup"><span data-stu-id="3728e-445">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="3728e-446">套件</span><span class="sxs-lookup"><span data-stu-id="3728e-446">Package</span></span>

<span data-ttu-id="3728e-447">若要在您的專案中包含中介軟體，請在包含 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 套件的專案檔中，將套件參考新增至 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="3728e-447">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="3728e-448">不使用 `Microsoft.AspNetCore.App` 中繼套件時，請將專案參考新增至 `Microsoft.AspNetCore.Rewrite` 套件。</span><span class="sxs-lookup"><span data-stu-id="3728e-448">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="3728e-449">延伸模組和選項</span><span class="sxs-lookup"><span data-stu-id="3728e-449">Extension and options</span></span>

<span data-ttu-id="3728e-450">若要建立 URL 重寫和重新導向規則，您可以針對每個重寫規則的擴充方法建立 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 類別的執行個體。</span><span class="sxs-lookup"><span data-stu-id="3728e-450">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="3728e-451">依據所需的處理順序來鏈結多個規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-451">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="3728e-452">系統會使用 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 將 `RewriteOptions` 新增至要求管線，並將其傳入 URL 重寫中介軟體：</span><span class="sxs-lookup"><span data-stu-id="3728e-452">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="3728e-453">將非 www 重新導向 www</span><span class="sxs-lookup"><span data-stu-id="3728e-453">Redirect non-www to www</span></span>

<span data-ttu-id="3728e-454">有三個選項可讓應用程式將非 `www` 要求重新導向 `www`：</span><span class="sxs-lookup"><span data-stu-id="3728e-454">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="3728e-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>：如果要求為非，則將要求永久重新導向至 `www` 子域 `www` 。</span><span class="sxs-lookup"><span data-stu-id="3728e-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="3728e-456">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 狀態代碼重新導向。</span><span class="sxs-lookup"><span data-stu-id="3728e-456">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="3728e-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>： `www` 如果連入要求為非，則將要求重新導向至子域 `www` 。</span><span class="sxs-lookup"><span data-stu-id="3728e-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="3728e-458">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 狀態代碼重新導向。</span><span class="sxs-lookup"><span data-stu-id="3728e-458">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="3728e-459">多載可讓您提供回應的狀態代碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-459">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="3728e-460">請使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 類別的欄位來進行狀態碼指派。</span><span class="sxs-lookup"><span data-stu-id="3728e-460">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="3728e-461">URL 重新導向</span><span class="sxs-lookup"><span data-stu-id="3728e-461">URL redirect</span></span>

<span data-ttu-id="3728e-462">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 將要求重新導向。</span><span class="sxs-lookup"><span data-stu-id="3728e-462">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="3728e-463">第一個參數會包含您的 Regex，以比對傳入的 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-463">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="3728e-464">第二個參數是取代字串。</span><span class="sxs-lookup"><span data-stu-id="3728e-464">The second parameter is the replacement string.</span></span> <span data-ttu-id="3728e-465">第三個參數 (如果有的話) 會指定狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-465">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="3728e-466">如果您未指定狀態碼，則狀態碼會預設為 302 (已找到)，表示已暫時移動或取代資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-466">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="3728e-467">在啟用開發人員工具的瀏覽器中，使用 `/redirect-rule/1234/5678` 路徑提出範例應用程式的要求。</span><span class="sxs-lookup"><span data-stu-id="3728e-467">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="3728e-468">Regex 會比對 `redirect-rule/(.*)` 上的要求路徑，並會將路徑取代為 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="3728e-468">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="3728e-469">系統會將重新導向 URL 與 302 (已找到) 狀態碼傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3728e-469">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="3728e-470">瀏覽器在重新導向 URL 處提出新要求，該 URL 也會顯示在瀏覽器的網址列中。</span><span class="sxs-lookup"><span data-stu-id="3728e-470">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="3728e-471">因為範例應用程式與重新導向 URL 沒有任何相符規則：</span><span class="sxs-lookup"><span data-stu-id="3728e-471">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="3728e-472">第二個要求會從應用程式收到 200 (確定) 回應。</span><span class="sxs-lookup"><span data-stu-id="3728e-472">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="3728e-473">回應的本文會顯示重新導向 URL。</span><span class="sxs-lookup"><span data-stu-id="3728e-473">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="3728e-474">「重新導向」URL 時，會對伺服器進行來回行程。</span><span class="sxs-lookup"><span data-stu-id="3728e-474">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="3728e-475">建立重新導向規則時，請務必謹慎。</span><span class="sxs-lookup"><span data-stu-id="3728e-475">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="3728e-476">每向應用程式提出要求時 (包括重新導向後)，請評估您的重新導向規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-476">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="3728e-477">因為您有可能會不小心建立無限重新導向迴圈。</span><span class="sxs-lookup"><span data-stu-id="3728e-477">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="3728e-478">原始要求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="3728e-478">Original Request: `/redirect-rule/1234/5678`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="3728e-480">括弧內所含的運算式部分稱為「擷取群組」。</span><span class="sxs-lookup"><span data-stu-id="3728e-480">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="3728e-481">運算式的點 (`.`) 表示「比對任何字元」。</span><span class="sxs-lookup"><span data-stu-id="3728e-481">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="3728e-482">星號 (`*`) 表示「比對前置字元零或多次」。</span><span class="sxs-lookup"><span data-stu-id="3728e-482">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="3728e-483">因此，擷取群組 `(.*)` 會擷取 URL 的 `1234/5678` 最後這兩個路徑區段。</span><span class="sxs-lookup"><span data-stu-id="3728e-483">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="3728e-484">這個單一擷取群組會擷取您在要求 URL 中的 `redirect-rule/` 之後所提供的任何值。</span><span class="sxs-lookup"><span data-stu-id="3728e-484">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="3728e-485">系統會將擷取的群組以貨幣符號 (`$`) 後接擷取序號的形式，插入取代字串中。</span><span class="sxs-lookup"><span data-stu-id="3728e-485">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="3728e-486">使用 `$1` 可取得第一個擷取群組值、使用 `$2` 則會取得第二個，依您的 Regex 擷取群組順序以此類推。</span><span class="sxs-lookup"><span data-stu-id="3728e-486">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="3728e-487">在範例應用程式中，重新導向規則 Regex 只有一個擷取的群組，因此在取代字串中只有 `$1` 這一個插入的群組。</span><span class="sxs-lookup"><span data-stu-id="3728e-487">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="3728e-488">套用規則時，URL 會變成 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="3728e-488">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="3728e-489">將 URL 重新導向至安全端點</span><span class="sxs-lookup"><span data-stu-id="3728e-489">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="3728e-490">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 將 HTTP 要求重新導向至使用 HTTPS 通訊協定的相同主機與路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-490">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="3728e-491">如果未提供狀態碼，中介軟體會預設為 302 (已找到)。</span><span class="sxs-lookup"><span data-stu-id="3728e-491">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="3728e-492">若未提供連接埠：</span><span class="sxs-lookup"><span data-stu-id="3728e-492">If the port isn't supplied:</span></span>

* <span data-ttu-id="3728e-493">中介軟體會預設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="3728e-493">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="3728e-494">配置會變更為 `https` (HTTPS 通訊協定)，且用戶端會在連接埠 443 上存取資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-494">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="3728e-495">下列範例示範了如何將狀態碼設為 301 (已永久移動)，並將連接埠變更為 5001。</span><span class="sxs-lookup"><span data-stu-id="3728e-495">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="3728e-496">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 將不安全的要求重新導向至使用安全 HTTPS 通訊協定 (在連接埠 443 上) 的相同主機與路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-496">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="3728e-497">中介軟體會將狀態碼設定為 301 (已永久移動)。</span><span class="sxs-lookup"><span data-stu-id="3728e-497">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="3728e-498">在不要求額外重新導向規則的情況下重新導向至安全的端點時，建議使用 HTTPS 重新導向中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3728e-498">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="3728e-499">如需詳細資訊，請參閱[強制執行 HTTPS](xref:security/enforcing-ssl#require-https) 主題。</span><span class="sxs-lookup"><span data-stu-id="3728e-499">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="3728e-500">範例應用程式可以示範如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="3728e-500">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="3728e-501">將擴充方法新增至 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="3728e-501">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="3728e-502">在任何 URL 位置，向應用程式提出不安全的要求。</span><span class="sxs-lookup"><span data-stu-id="3728e-502">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="3728e-503">關閉瀏覽器自我簽署憑證不受信任的安全性警告，或建立信任憑證的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="3728e-503">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="3728e-504">使用 `AddRedirectToHttps(301, 5001)` 的原始要求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="3728e-504">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="3728e-506">使用 `AddRedirectToHttpsPermanent` 的原始要求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="3728e-506">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="3728e-508">URL 重寫</span><span class="sxs-lookup"><span data-stu-id="3728e-508">URL rewrite</span></span>

<span data-ttu-id="3728e-509">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 來建立 URL 重寫的規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-509">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="3728e-510">第一個參數會包含 Regex，以比對傳入的 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-510">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="3728e-511">第二個參數是取代字串。</span><span class="sxs-lookup"><span data-stu-id="3728e-511">The second parameter is the replacement string.</span></span> <span data-ttu-id="3728e-512">第三個參數 `skipRemainingRules: {true|false}` 指出中介軟體是否要在套用目前規則時略過其他重寫規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-512">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="3728e-513">原始要求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="3728e-513">Original Request: `/rewrite-rule/1234/5678`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="3728e-515">運算式開頭的插入號 (`^`) 表示，比對會從 URL 路徑的開頭開始。</span><span class="sxs-lookup"><span data-stu-id="3728e-515">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="3728e-516">在先前的重新導向規則範例 `redirect-rule/(.*)` 中，Regex 的開頭沒有插入號 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="3728e-516">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="3728e-517">因此，就算 `redirect-rule/` 前有任何字元也能成功比對。</span><span class="sxs-lookup"><span data-stu-id="3728e-517">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="3728e-518">路徑</span><span class="sxs-lookup"><span data-stu-id="3728e-518">Path</span></span>                               | <span data-ttu-id="3728e-519">比對</span><span class="sxs-lookup"><span data-stu-id="3728e-519">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="3728e-520">是</span><span class="sxs-lookup"><span data-stu-id="3728e-520">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="3728e-521">是</span><span class="sxs-lookup"><span data-stu-id="3728e-521">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="3728e-522">是</span><span class="sxs-lookup"><span data-stu-id="3728e-522">Yes</span></span>   |

<span data-ttu-id="3728e-523">`^rewrite-rule/(\d+)/(\d+)` 重寫規則只會比對開頭為 `rewrite-rule/` 的路徑。</span><span class="sxs-lookup"><span data-stu-id="3728e-523">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="3728e-524">請注意下表中的比對差異。</span><span class="sxs-lookup"><span data-stu-id="3728e-524">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="3728e-525">路徑</span><span class="sxs-lookup"><span data-stu-id="3728e-525">Path</span></span>                              | <span data-ttu-id="3728e-526">比對</span><span class="sxs-lookup"><span data-stu-id="3728e-526">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="3728e-527">是</span><span class="sxs-lookup"><span data-stu-id="3728e-527">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="3728e-528">否</span><span class="sxs-lookup"><span data-stu-id="3728e-528">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="3728e-529">否</span><span class="sxs-lookup"><span data-stu-id="3728e-529">No</span></span>    |

<span data-ttu-id="3728e-530">運算式的 `^rewrite-rule/` 部分之後，有 `(\d+)/(\d+)` 這兩個擷取群組。</span><span class="sxs-lookup"><span data-stu-id="3728e-530">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="3728e-531">`\d` 表示「比對數字」。</span><span class="sxs-lookup"><span data-stu-id="3728e-531">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="3728e-532">加號 (`+`) 表示「比對一或多個前置字元」。</span><span class="sxs-lookup"><span data-stu-id="3728e-532">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="3728e-533">因此，URL 必須包含某個數字，後接斜線與另一個數字。</span><span class="sxs-lookup"><span data-stu-id="3728e-533">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="3728e-534">這些擷取群組會以 `$1` 和 `$2` 形式插入重寫的 URL。</span><span class="sxs-lookup"><span data-stu-id="3728e-534">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="3728e-535">重寫規則的取代字串會將擷取的群組放入查詢字串中。</span><span class="sxs-lookup"><span data-stu-id="3728e-535">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="3728e-536">系統會重寫 `/rewrite-rule/1234/5678` 的要求路徑，以取得位於 `/rewritten?var1=1234&var2=5678` 的資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-536">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="3728e-537">如果原始要求上有查詢字串，則會在重寫 URL 時予以保留。</span><span class="sxs-lookup"><span data-stu-id="3728e-537">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="3728e-538">這麼做就不需要伺服器的來回行程，即可取得資源。</span><span class="sxs-lookup"><span data-stu-id="3728e-538">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="3728e-539">如果資源存在，就會擷取該資源，並傳回 200 (確定) 狀態碼給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3728e-539">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="3728e-540">因為系統並未重新導向用戶端，因此瀏覽器網址列中的 URL 不會變更。</span><span class="sxs-lookup"><span data-stu-id="3728e-540">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="3728e-541">用戶端也無法偵測到伺服器上發生 URL 重寫作業。</span><span class="sxs-lookup"><span data-stu-id="3728e-541">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="3728e-542">因為比對規則是計算繁複的程序，且會增加應用程式的回應時間，所以請盡可能使用 `skipRemainingRules: true`。</span><span class="sxs-lookup"><span data-stu-id="3728e-542">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="3728e-543">如需最快速的應用程式回應：</span><span class="sxs-lookup"><span data-stu-id="3728e-543">For the fastest app response:</span></span>
>
> * <span data-ttu-id="3728e-544">排序重寫規則；從最常比對的規則排到最不常比對的規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-544">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="3728e-545">當出現符合項目且不需要任何額外的規則處理時，即略過剩下的規則處理。</span><span class="sxs-lookup"><span data-stu-id="3728e-545">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="3728e-546">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="3728e-546">Apache mod_rewrite</span></span>

<span data-ttu-id="3728e-547">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 來套用 Apache mod_rewrite 規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-547">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="3728e-548">請確認應用程式已部署規則檔。</span><span class="sxs-lookup"><span data-stu-id="3728e-548">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="3728e-549">如需 mod_rewrite 規則的詳細資訊和範例，請參閱 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="3728e-549">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="3728e-550">系統會使用 <xref:System.IO.StreamReader> 來讀取 *ApacheModRewrite.txt* 規則檔案的規則：</span><span class="sxs-lookup"><span data-stu-id="3728e-550">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="3728e-551">範例應用程式會將要求從 `/apache-mod-rules-redirect/(.\*)` 重新導向至 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="3728e-551">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="3728e-552">回應狀態碼為 302 (已找到)。</span><span class="sxs-lookup"><span data-stu-id="3728e-552">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="3728e-553">原始要求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="3728e-553">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="3728e-555">中介軟體可支援下列 Apache mod_rewrite 伺服器變數：</span><span class="sxs-lookup"><span data-stu-id="3728e-555">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="3728e-556">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-556">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="3728e-557">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="3728e-557">HTTP_ACCEPT</span></span>
* <span data-ttu-id="3728e-558">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="3728e-558">HTTP_CONNECTION</span></span>
* <span data-ttu-id="3728e-559">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="3728e-559">HTTP_COOKIE</span></span>
* <span data-ttu-id="3728e-560">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="3728e-560">HTTP_FORWARDED</span></span>
* <span data-ttu-id="3728e-561">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="3728e-561">HTTP_HOST</span></span>
* <span data-ttu-id="3728e-562">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="3728e-562">HTTP_REFERER</span></span>
* <span data-ttu-id="3728e-563">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="3728e-563">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="3728e-564">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3728e-564">HTTPS</span></span>
* <span data-ttu-id="3728e-565">IPV6</span><span class="sxs-lookup"><span data-stu-id="3728e-565">IPV6</span></span>
* <span data-ttu-id="3728e-566">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="3728e-566">QUERY_STRING</span></span>
* <span data-ttu-id="3728e-567">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-567">REMOTE_ADDR</span></span>
* <span data-ttu-id="3728e-568">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="3728e-568">REMOTE_PORT</span></span>
* <span data-ttu-id="3728e-569">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="3728e-569">REQUEST_FILENAME</span></span>
* <span data-ttu-id="3728e-570">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="3728e-570">REQUEST_METHOD</span></span>
* <span data-ttu-id="3728e-571">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="3728e-571">REQUEST_SCHEME</span></span>
* <span data-ttu-id="3728e-572">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="3728e-572">REQUEST_URI</span></span>
* <span data-ttu-id="3728e-573">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="3728e-573">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="3728e-574">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-574">SERVER_ADDR</span></span>
* <span data-ttu-id="3728e-575">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="3728e-575">SERVER_PORT</span></span>
* <span data-ttu-id="3728e-576">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="3728e-576">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="3728e-577">TIME</span><span class="sxs-lookup"><span data-stu-id="3728e-577">TIME</span></span>
* <span data-ttu-id="3728e-578">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="3728e-578">TIME_DAY</span></span>
* <span data-ttu-id="3728e-579">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="3728e-579">TIME_HOUR</span></span>
* <span data-ttu-id="3728e-580">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="3728e-580">TIME_MIN</span></span>
* <span data-ttu-id="3728e-581">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="3728e-581">TIME_MON</span></span>
* <span data-ttu-id="3728e-582">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="3728e-582">TIME_SEC</span></span>
* <span data-ttu-id="3728e-583">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="3728e-583">TIME_WDAY</span></span>
* <span data-ttu-id="3728e-584">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="3728e-584">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="3728e-585">IIS URL Rewrite Module 規則</span><span class="sxs-lookup"><span data-stu-id="3728e-585">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="3728e-586">若要使用與套用至 IIS URL Rewrite Module 一樣的規則集，請使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="3728e-586">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="3728e-587">請確認應用程式已部署規則檔。</span><span class="sxs-lookup"><span data-stu-id="3728e-587">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="3728e-588">在 Windows Server IIS 上執行時，請不要引導中介軟體使用應用程式的 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3728e-588">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="3728e-589">使用 IIS 時，這些規則應該儲存在應用程式的 *web.config* 檔案外部，以免與 IIS Rewrite Module 發生衝突。</span><span class="sxs-lookup"><span data-stu-id="3728e-589">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="3728e-590">如需 IIS URL Rewrite Module 規則的詳細資訊和範例，請參閱 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (使用 URL Rewrite Module 2.0) 和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (URL Rewrite Module 組態參考)。</span><span class="sxs-lookup"><span data-stu-id="3728e-590">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="3728e-591">系統會使用 <xref:System.IO.StreamReader> 來讀取 *IISUrlRewrite.xml* 規則檔案的規則：</span><span class="sxs-lookup"><span data-stu-id="3728e-591">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="3728e-592">範例應用程式會將要求從 `/iis-rules-rewrite/(.*)` 重寫至 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="3728e-592">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="3728e-593">系統會將回應與 200 (確定) 狀態碼傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3728e-593">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="3728e-594">原始要求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="3728e-594">Original Request: `/iis-rules-rewrite/1234`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤要求和回應](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="3728e-596">如果您具備使用中且已設定伺服器層級規則的 IIS Rewrite Module，但其會以非預期的方式影響應用程式，則您可以停用應用程式的 IIS Rewrite Module 。</span><span class="sxs-lookup"><span data-stu-id="3728e-596">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="3728e-597">如需詳細資訊，請參閱[停用 IIS 模組](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="3728e-597">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="3728e-598">不支援的功能</span><span class="sxs-lookup"><span data-stu-id="3728e-598">Unsupported features</span></span>

<span data-ttu-id="3728e-599">與 ASP.NET Core 2.x 一起發行的中介軟體不支援下列 IIS URL Rewrite Module 功能：</span><span class="sxs-lookup"><span data-stu-id="3728e-599">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="3728e-600">輸出規則</span><span class="sxs-lookup"><span data-stu-id="3728e-600">Outbound Rules</span></span>
* <span data-ttu-id="3728e-601">自訂伺服器變數</span><span class="sxs-lookup"><span data-stu-id="3728e-601">Custom Server Variables</span></span>
* <span data-ttu-id="3728e-602">萬用字元</span><span class="sxs-lookup"><span data-stu-id="3728e-602">Wildcards</span></span>
* <span data-ttu-id="3728e-603">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="3728e-603">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="3728e-604">支援的伺服器變數</span><span class="sxs-lookup"><span data-stu-id="3728e-604">Supported server variables</span></span>

<span data-ttu-id="3728e-605">中介軟體可支援下列 IIS URL Rewrite Module 伺服器變數：</span><span class="sxs-lookup"><span data-stu-id="3728e-605">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="3728e-606">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="3728e-606">CONTENT_LENGTH</span></span>
* <span data-ttu-id="3728e-607">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="3728e-607">CONTENT_TYPE</span></span>
* <span data-ttu-id="3728e-608">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="3728e-608">HTTP_ACCEPT</span></span>
* <span data-ttu-id="3728e-609">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="3728e-609">HTTP_CONNECTION</span></span>
* <span data-ttu-id="3728e-610">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="3728e-610">HTTP_COOKIE</span></span>
* <span data-ttu-id="3728e-611">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="3728e-611">HTTP_HOST</span></span>
* <span data-ttu-id="3728e-612">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="3728e-612">HTTP_REFERER</span></span>
* <span data-ttu-id="3728e-613">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="3728e-613">HTTP_URL</span></span>
* <span data-ttu-id="3728e-614">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="3728e-614">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="3728e-615">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3728e-615">HTTPS</span></span>
* <span data-ttu-id="3728e-616">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-616">LOCAL_ADDR</span></span>
* <span data-ttu-id="3728e-617">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="3728e-617">QUERY_STRING</span></span>
* <span data-ttu-id="3728e-618">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="3728e-618">REMOTE_ADDR</span></span>
* <span data-ttu-id="3728e-619">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="3728e-619">REMOTE_PORT</span></span>
* <span data-ttu-id="3728e-620">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="3728e-620">REQUEST_FILENAME</span></span>
* <span data-ttu-id="3728e-621">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="3728e-621">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="3728e-622">您也可以透過 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 取得 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="3728e-622">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="3728e-623">這種方法可讓重寫規則檔案的位置更有彈性。</span><span class="sxs-lookup"><span data-stu-id="3728e-623">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="3728e-624">請確認重寫規則檔案已部署到您所提供之路徑的伺服器。</span><span class="sxs-lookup"><span data-stu-id="3728e-624">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="3728e-625">以方法為基礎的規則</span><span class="sxs-lookup"><span data-stu-id="3728e-625">Method-based rule</span></span>

<span data-ttu-id="3728e-626">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 以在方法中實作您自己的規則邏輯。</span><span class="sxs-lookup"><span data-stu-id="3728e-626">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="3728e-627">`Add` 會公開 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用於您的方法中。</span><span class="sxs-lookup"><span data-stu-id="3728e-627">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="3728e-628">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)可判斷其他管線處理的執行方式。</span><span class="sxs-lookup"><span data-stu-id="3728e-628">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="3728e-629">請將值設定為下表中描述的其中一個 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 欄位。</span><span class="sxs-lookup"><span data-stu-id="3728e-629">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="3728e-630">重寫內容結果</span><span class="sxs-lookup"><span data-stu-id="3728e-630">Rewrite context result</span></span>               | <span data-ttu-id="3728e-631">動作</span><span class="sxs-lookup"><span data-stu-id="3728e-631">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="3728e-632">`RuleResult.ContinueRules` (預設)</span><span class="sxs-lookup"><span data-stu-id="3728e-632">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="3728e-633">繼續套用規則。</span><span class="sxs-lookup"><span data-stu-id="3728e-633">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="3728e-634">停止套用規則，並傳送回應。</span><span class="sxs-lookup"><span data-stu-id="3728e-634">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="3728e-635">停止套用規則，並將內容傳送至下一個中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3728e-635">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="3728e-636">範例應用程式會示範一個方法，以重新導向對 *.xml* 結尾之路徑的要求。</span><span class="sxs-lookup"><span data-stu-id="3728e-636">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="3728e-637">若要求是針對 `/file.xml` 發出，則要求會重新導向至 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="3728e-637">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="3728e-638">狀態碼會設定為 301 (已永久移動)。</span><span class="sxs-lookup"><span data-stu-id="3728e-638">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="3728e-639">當瀏覽器針對 */xmlfiles/file.xml* 發出新的要求時，靜態檔案中介軟體會從 *wwwroot/xmlfiles* 資料夾將檔案提供給用戶端。</span><span class="sxs-lookup"><span data-stu-id="3728e-639">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="3728e-640">若要重新導向，請明確設定回應的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="3728e-640">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="3728e-641">否則會傳回 200 (確定) 狀態碼，用戶端上也不會發生重新導向。</span><span class="sxs-lookup"><span data-stu-id="3728e-641">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="3728e-642">*RewriteRules.cs*：</span><span class="sxs-lookup"><span data-stu-id="3728e-642">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="3728e-643">這種方法也能重寫要求。</span><span class="sxs-lookup"><span data-stu-id="3728e-643">This approach can also rewrite requests.</span></span> <span data-ttu-id="3728e-644">範例應用程式示範了如何重寫任何文字檔要求的路徑，以從 *wwwroot* 資料夾提供 *file.txt* 文字檔。</span><span class="sxs-lookup"><span data-stu-id="3728e-644">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="3728e-645">靜態檔案中介軟體會根據更新的要求路徑來提供檔案：</span><span class="sxs-lookup"><span data-stu-id="3728e-645">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="3728e-646">*RewriteRules.cs*：</span><span class="sxs-lookup"><span data-stu-id="3728e-646">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="3728e-647">以 IRule 為基礎的規則</span><span class="sxs-lookup"><span data-stu-id="3728e-647">IRule-based rule</span></span>

<span data-ttu-id="3728e-648">利用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 來使用類別中實作 <xref:Microsoft.AspNetCore.Rewrite.IRule> 介面的專屬規則邏輯。</span><span class="sxs-lookup"><span data-stu-id="3728e-648">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="3728e-649">相較於使用以方法為基礎的規則方法，`IRule` 提供了更高彈性。</span><span class="sxs-lookup"><span data-stu-id="3728e-649">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="3728e-650">您的實作類別可以包含建構函式，以便您在其中傳入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的參數。</span><span class="sxs-lookup"><span data-stu-id="3728e-650">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="3728e-651">為了滿足若干條件，系統會檢查範例應用程式中的 `extension` 和 `newPath` 參數值。</span><span class="sxs-lookup"><span data-stu-id="3728e-651">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="3728e-652">`extension` 必須包含值，而且值必須是 *.png*、*.jpg* 或 *.gif*。</span><span class="sxs-lookup"><span data-stu-id="3728e-652">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="3728e-653">如果 `newPath` 無效，就會擲回 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="3728e-653">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="3728e-654">若要求是針對 *image.png* 發出，則要求會重新導向至 `/png-images/image.png`。</span><span class="sxs-lookup"><span data-stu-id="3728e-654">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="3728e-655">若要求是針對 *image.jpg* 發出，則要求會重新導向至 `/jpg-images/image.jpg`。</span><span class="sxs-lookup"><span data-stu-id="3728e-655">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="3728e-656">狀態碼會設定為 301 (已永久移動)，而 `context.Result` 會設定為停止處理規則，並傳送回應。</span><span class="sxs-lookup"><span data-stu-id="3728e-656">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="3728e-657">原始要求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="3728e-657">Original Request: `/image.png`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤 image.png 的要求和回應](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="3728e-659">原始要求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="3728e-659">Original Request: `/image.jpg`</span></span>

![瀏覽器視窗，其中的開發人員工具會追蹤 image.jpg 的要求和回應](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="3728e-661">Regex 範例</span><span class="sxs-lookup"><span data-stu-id="3728e-661">Regex examples</span></span>

| <span data-ttu-id="3728e-662">目標</span><span class="sxs-lookup"><span data-stu-id="3728e-662">Goal</span></span> | <span data-ttu-id="3728e-663">Regex 字串及</span><span class="sxs-lookup"><span data-stu-id="3728e-663">Regex String &</span></span><br><span data-ttu-id="3728e-664">比對範例</span><span class="sxs-lookup"><span data-stu-id="3728e-664">Match Example</span></span> | <span data-ttu-id="3728e-665">取代字串及</span><span class="sxs-lookup"><span data-stu-id="3728e-665">Replacement String &</span></span><br><span data-ttu-id="3728e-666">輸出範例</span><span class="sxs-lookup"><span data-stu-id="3728e-666">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="3728e-667">將路徑重寫成查詢字串</span><span class="sxs-lookup"><span data-stu-id="3728e-667">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="3728e-668">移除斜線</span><span class="sxs-lookup"><span data-stu-id="3728e-668">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="3728e-669">強制使用斜線</span><span class="sxs-lookup"><span data-stu-id="3728e-669">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="3728e-670">避免重寫特定的要求</span><span class="sxs-lookup"><span data-stu-id="3728e-670">Avoid rewriting specific requests</span></span> | <span data-ttu-id="3728e-671">`^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="3728e-671">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="3728e-672">是：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="3728e-672">Yes: `/resource.htm`</span></span><br><span data-ttu-id="3728e-673">否：`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="3728e-673">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="3728e-674">重新排列 URL 區段</span><span class="sxs-lookup"><span data-stu-id="3728e-674">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="3728e-675">取代 URL 區段</span><span class="sxs-lookup"><span data-stu-id="3728e-675">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3728e-676">其他資源</span><span class="sxs-lookup"><span data-stu-id="3728e-676">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="3728e-677">.NET 中的規則運算式</span><span class="sxs-lookup"><span data-stu-id="3728e-677">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="3728e-678">正則運算式語言-快速參考</span><span class="sxs-lookup"><span data-stu-id="3728e-678">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="3728e-679">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="3728e-679">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* <span data-ttu-id="3728e-680">[Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (使用 URL Rewrite Module 2.0 (適用於 IIS))</span><span class="sxs-lookup"><span data-stu-id="3728e-680">[Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)</span></span>
* <span data-ttu-id="3728e-681">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (URL Rewrite Module 組態參考)</span><span class="sxs-lookup"><span data-stu-id="3728e-681">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span></span>
* [<span data-ttu-id="3728e-682">IIS URL Rewrite Module 論壇</span><span class="sxs-lookup"><span data-stu-id="3728e-682">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* <span data-ttu-id="3728e-683">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en) (保持精簡的 URL 結構)</span><span class="sxs-lookup"><span data-stu-id="3728e-683">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)</span></span>
* <span data-ttu-id="3728e-684">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/) (10 個重寫 URL 的祕訣與技巧)</span><span class="sxs-lookup"><span data-stu-id="3728e-684">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)</span></span>
* <span data-ttu-id="3728e-685">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html) (是否要使用斜線)</span><span class="sxs-lookup"><span data-stu-id="3728e-685">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)</span></span>
