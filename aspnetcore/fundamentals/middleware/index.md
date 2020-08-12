---
title: ASP.NET Core 中介軟體
author: rick-anderson
description: 了解 ASP.NET Core 中介軟體和要求管線。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/index
ms.openlocfilehash: a9f158bf875da75afbccc1a6d226bc842fa1c62c
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130505"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="86026-103">ASP.NET Core 中介軟體</span><span class="sxs-lookup"><span data-stu-id="86026-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86026-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Steve Smith](https://ardalis.com/) 撰寫</span><span class="sxs-lookup"><span data-stu-id="86026-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="86026-105">中介軟體為組成應用程式管線的軟體，用以處理要求與回應。</span><span class="sxs-lookup"><span data-stu-id="86026-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="86026-106">每個元件：</span><span class="sxs-lookup"><span data-stu-id="86026-106">Each component:</span></span>

* <span data-ttu-id="86026-107">可選擇是否要將要求傳送到管線中的下一個元件。</span><span class="sxs-lookup"><span data-stu-id="86026-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="86026-108">可以下一個元件的前後執行工作。</span><span class="sxs-lookup"><span data-stu-id="86026-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="86026-109">要求委派用於建置要求管線，</span><span class="sxs-lookup"><span data-stu-id="86026-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="86026-110">其會處理每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="86026-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="86026-111">要求委派的設定方式為使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="86026-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="86026-112">您可將個別要求委派指定為內嵌匿名方法 (在內嵌中介軟體中呼叫)，或於可重複使用的類別中加以定義。</span><span class="sxs-lookup"><span data-stu-id="86026-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="86026-113">這些可重複使用的類別及內嵌匿名方法皆為「中介軟體」**，也稱為「中介軟體元件」**。</span><span class="sxs-lookup"><span data-stu-id="86026-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="86026-114">要求管線中的每個中介軟體元件負責叫用管線中下一個元件，或對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="86026-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="86026-115">當中介軟體短路時，稱為「終端中介軟體」\*\*，因為它會防止接下來的中介軟體處理要求。</span><span class="sxs-lookup"><span data-stu-id="86026-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="86026-116"><xref:migration/http-modules> 說明 ASP.NET Core 和 ASP.NET 4.x 之間的要求管線差異，並提供更多中介軟體範例。</span><span class="sxs-lookup"><span data-stu-id="86026-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="86026-117">使用 IApplicationBuilder 建立中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="86026-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="86026-118">ASP.NET Core 要求管線由要求委派序列組成，並會一個接著一個呼叫。</span><span class="sxs-lookup"><span data-stu-id="86026-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="86026-119">下圖說明此概念。</span><span class="sxs-lookup"><span data-stu-id="86026-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="86026-120">執行緒遵循黑色箭號執行。</span><span class="sxs-lookup"><span data-stu-id="86026-120">The thread of execution follows the black arrows.</span></span>

![要求處理模式說明要求抵達，經過三個中介軟體所處理，然後應用程式送出回應。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="86026-124">每一個委派皆能在下個委派的前後執行作業。</span><span class="sxs-lookup"><span data-stu-id="86026-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="86026-125">處理例外狀況的委派必須提前在管線中呼叫，以便其可與管線後續階段中所發生的例外狀況達成一致。</span><span class="sxs-lookup"><span data-stu-id="86026-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="86026-126">最簡潔的 ASP.NET Core 應用程式會設定單一要求委派來處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="86026-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="86026-127">此情況不包含實際要求管線。</span><span class="sxs-lookup"><span data-stu-id="86026-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="86026-128">反之，系統會呼叫單一匿名函式來回應每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="86026-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="86026-129">將多個要求委派鏈結在一起的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>。</span><span class="sxs-lookup"><span data-stu-id="86026-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="86026-130">`next` 參數代表管線中的下個委派。</span><span class="sxs-lookup"><span data-stu-id="86026-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="86026-131">您可以「不」\*\* 呼叫「下一個」\*\* 參數來對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="86026-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="86026-132">您通常可以在下個委派的前後執行動作，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="86026-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="86026-133">當委派不將要求傳遞到下一個委派時，這就是所謂*讓要求管線短路*。</span><span class="sxs-lookup"><span data-stu-id="86026-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="86026-134">因為最少運算可避免不必要的工作，所以經常使用。</span><span class="sxs-lookup"><span data-stu-id="86026-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="86026-135">例如，[靜態檔案中介軟體](xref:fundamentals/static-files)可以做為*終端中介軟體*使用，方式是處理靜態檔案的要求，並對剩餘的管線執行短路。</span><span class="sxs-lookup"><span data-stu-id="86026-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="86026-136">在中介軟體之前新增到管線且終結進一步處理的中介軟體在其 `next.Invoke` 陳述式之後仍然處理程式碼。</span><span class="sxs-lookup"><span data-stu-id="86026-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="86026-137">不過，查看下列有關嘗試寫入已傳送之回應的警告。</span><span class="sxs-lookup"><span data-stu-id="86026-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="86026-138">請不要在回應已傳送給用戶端之後呼叫 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="86026-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="86026-139">回應啟動後，變更為 <xref:Microsoft.AspNetCore.Http.HttpResponse> 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="86026-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="86026-140">例如，[設定標頭和狀態碼會擲回例外狀況](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started)。</span><span class="sxs-lookup"><span data-stu-id="86026-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="86026-141">若在呼叫 `next` 後寫入回應本文：</span><span class="sxs-lookup"><span data-stu-id="86026-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="86026-142">可能導致違反通訊協定。</span><span class="sxs-lookup"><span data-stu-id="86026-142">May cause a protocol violation.</span></span> <span data-ttu-id="86026-143">例如，寫入超過指定 `Content-Length` 的內容。</span><span class="sxs-lookup"><span data-stu-id="86026-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="86026-144">可能損毀本文格式。</span><span class="sxs-lookup"><span data-stu-id="86026-144">May corrupt the body format.</span></span> <span data-ttu-id="86026-145">例如，將 HTML 頁尾寫入 CSS 檔。</span><span class="sxs-lookup"><span data-stu-id="86026-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="86026-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是實用的提示，可表示是否已傳送標頭 (或) 是否已寫入本文。</span><span class="sxs-lookup"><span data-stu-id="86026-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="86026-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>委派不會收到 `next` 參數。</span><span class="sxs-lookup"><span data-stu-id="86026-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="86026-148">第一個 `Run` 委派一律是 terminal，並終止管線。</span><span class="sxs-lookup"><span data-stu-id="86026-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="86026-149">`Run`是慣例。</span><span class="sxs-lookup"><span data-stu-id="86026-149">`Run` is a convention.</span></span> <span data-ttu-id="86026-150">某些中介軟體元件可能會公開在 `Run[Middleware]` 管線結束時執行的方法：</span><span class="sxs-lookup"><span data-stu-id="86026-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="86026-151">在上述範例中， `Run` 委派會寫入 `"Hello from 2nd delegate."` 至回應，然後終止管線。</span><span class="sxs-lookup"><span data-stu-id="86026-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="86026-152">如果 `Use` `Run` 在委派之後加入另一個或委派 `Run` ，則不會呼叫它。</span><span class="sxs-lookup"><span data-stu-id="86026-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="86026-153">中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="86026-153">Middleware order</span></span>

<span data-ttu-id="86026-154">下圖顯示 ASP.NET Core MVC 和頁面應用程式的完整要求處理管線 Razor 。</span><span class="sxs-lookup"><span data-stu-id="86026-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="86026-155">您可以查看在一般應用程式中，如何排序現有的中介軟體，以及新增自訂中介軟體的位置。</span><span class="sxs-lookup"><span data-stu-id="86026-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="86026-156">您可以完整控制如何重新排序現有的中介軟體，或在您的案例中插入新的自訂中介軟體。</span><span class="sxs-lookup"><span data-stu-id="86026-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core 中介軟體管線](index/_static/middleware-pipeline.svg)

<span data-ttu-id="86026-158">上圖中的**端點**中介軟體會執行對應應用程式類型 &mdash; MVC 或頁面的篩選準則管線 Razor 。</span><span class="sxs-lookup"><span data-stu-id="86026-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core 篩選器管線](index/_static/mvc-endpoint.svg)

<span data-ttu-id="86026-160">`Startup.Configure` 方法內中介軟體元件的新增順序可定義在要求時叫用中介軟體元件的順序及回應的反向順序。</span><span class="sxs-lookup"><span data-stu-id="86026-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="86026-161">順序對於安全性、效能和功能非常**重要**。</span><span class="sxs-lookup"><span data-stu-id="86026-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="86026-162">下列 `Startup.Configure` 方法會以建議的順序新增安全性相關中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="86026-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="86026-163">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="86026-163">In the preceding code:</span></span>

* <span data-ttu-id="86026-164">使用[個別使用者帳戶](xref:security/authentication/identity)建立新的 web 應用程式時，未新增的中介軟體會加上批註。</span><span class="sxs-lookup"><span data-stu-id="86026-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="86026-165">並非每個中介軟體都必須以這種完全相同的循序執行，但也有許多。</span><span class="sxs-lookup"><span data-stu-id="86026-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="86026-166">例如：</span><span class="sxs-lookup"><span data-stu-id="86026-166">For example:</span></span>
  * <span data-ttu-id="86026-167">`UseCors`、 `UseAuthentication` 和 `UseAuthorization` 必須以顯示的循序執行。</span><span class="sxs-lookup"><span data-stu-id="86026-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="86026-168">`UseCors`目前必須在 `UseResponseCaching` [此 bug](https://github.com/dotnet/aspnetcore/issues/23218)之前進行。</span><span class="sxs-lookup"><span data-stu-id="86026-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="86026-169">下列 `Startup.Configure` 方法會新增適用於一般應用程式案例的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="86026-169">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="86026-170">例外狀況/錯誤處理</span><span class="sxs-lookup"><span data-stu-id="86026-170">Exception/error handling</span></span>
   * <span data-ttu-id="86026-171">當應用程式在開發環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="86026-171">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="86026-172">開發人員例外狀況頁面中介軟體 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 會回報應用程式執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="86026-172">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="86026-173">資料庫錯誤頁面中介軟體會報告資料庫執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="86026-173">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="86026-174">當應用程式在生產環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="86026-174">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="86026-175">例外狀況處理常式中介軟體 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 會攔截在下列中介軟體中擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="86026-175">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="86026-176">HTTP 靜態傳輸安全性通訊協定 (HSTS) 中介軟體 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 會新增 `Strict-Transport-Security` 標頭。</span><span class="sxs-lookup"><span data-stu-id="86026-176">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="86026-177">HTTPS 重新導向中介軟體 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 會將 HTTP 要求重新導向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="86026-177">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="86026-178">靜態檔案中介軟體 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 會傳回靜態檔案並縮短進一步的要求處理時間。</span><span class="sxs-lookup"><span data-stu-id="86026-178">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="86026-179">Cookie原則中介軟體 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) 符合歐盟一般資料保護規定 (GDPR) 法規的應用程式。</span><span class="sxs-lookup"><span data-stu-id="86026-179">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="86026-180">路由中介軟體 (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) 來路由傳送要求。</span><span class="sxs-lookup"><span data-stu-id="86026-180">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="86026-181">驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 會嘗試在允許使用者存取安全資源之前先驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="86026-181">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="86026-182">授權中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) 授權使用者存取安全的資源。</span><span class="sxs-lookup"><span data-stu-id="86026-182">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="86026-183">工作階段中介軟體 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 會建立並維護工作階段狀態。</span><span class="sxs-lookup"><span data-stu-id="86026-183">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="86026-184">如果應用程式使用會話狀態，請在 Cookie 原則中介軟體之後和 MVC 中介軟體之前呼叫會話中介軟體。</span><span class="sxs-lookup"><span data-stu-id="86026-184">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="86026-185">端點路由中介軟體 (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 具有 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) ，以將 Razor 頁面端點新增至要求管線。</span><span class="sxs-lookup"><span data-stu-id="86026-185">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="86026-186">在上面的範例程式碼中，每個中介軟體擴充方法都會透過 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空間在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公開。</span><span class="sxs-lookup"><span data-stu-id="86026-186">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="86026-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是第一個新增到管道的中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="86026-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="86026-188">因此，例外處理常式中介軟體會攔截後續呼叫中發生的所有例外狀況。</span><span class="sxs-lookup"><span data-stu-id="86026-188">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="86026-189">靜態檔案中介軟體會提前在管線中呼叫，以便其無須逐一處理剩餘的元件，就能處理要求及執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="86026-189">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="86026-190">靜態檔案中介軟體**不**提供授權檢查。</span><span class="sxs-lookup"><span data-stu-id="86026-190">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="86026-191">靜態檔案中介軟體所提供的任何檔案（包括*wwwroot*底下的檔案）皆可公開使用。</span><span class="sxs-lookup"><span data-stu-id="86026-191">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="86026-192">如需保護靜態檔案的方法，請參閱 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="86026-192">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="86026-193">若靜態檔案中介軟體未處理要求，該要求會繼續傳遞給執行驗證的驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="86026-193">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="86026-194">驗證不會對未經驗證的要求執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="86026-194">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="86026-195">雖然驗證中介軟體會驗證要求，但只有在 MVC 選取特定 Razor 頁面或 MVC 控制器和動作之後，才會執行授權 (和拒絕) 。</span><span class="sxs-lookup"><span data-stu-id="86026-195">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="86026-196">下列範例示範靜態檔案中介軟體在回應壓縮中介軟體之前處理靜態檔案要求之前的靜態檔案順序。</span><span class="sxs-lookup"><span data-stu-id="86026-196">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="86026-197">靜態檔案並不會以此中介軟體順序壓縮。</span><span class="sxs-lookup"><span data-stu-id="86026-197">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="86026-198">Razor頁面回應可以壓縮。</span><span class="sxs-lookup"><span data-stu-id="86026-198">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="86026-199">對於單一頁面應用程式 (Spa) ，SPA 中介軟體 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> 通常是在中介軟體管線中的最後一個。</span><span class="sxs-lookup"><span data-stu-id="86026-199">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="86026-200">SPA 中介軟體最後一步：</span><span class="sxs-lookup"><span data-stu-id="86026-200">The SPA middleware comes last:</span></span>

* <span data-ttu-id="86026-201">以允許所有其他中介軟體先回應相符的要求。</span><span class="sxs-lookup"><span data-stu-id="86026-201">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="86026-202">允許 Spa 與用戶端路由針對伺服器應用程式無法辨識的所有路由執行。</span><span class="sxs-lookup"><span data-stu-id="86026-202">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="86026-203">如需 Spa 的詳細資訊，請參閱[回應](xref:spa/react)和[角度](xref:spa/angular)專案範本的指南。</span><span class="sxs-lookup"><span data-stu-id="86026-203">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="86026-204">轉送的標頭中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="86026-204">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="86026-205">分支中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="86026-205">Branch the middleware pipeline</span></span>

<span data-ttu-id="86026-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 擴充方法則是用來分支管線的慣例。</span><span class="sxs-lookup"><span data-stu-id="86026-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="86026-207">`Map` 會依據指定要求路徑的相符項目將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="86026-207">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="86026-208">如果要求路徑以指定路徑為開頭，則會執行分支。</span><span class="sxs-lookup"><span data-stu-id="86026-208">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="86026-209">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="86026-209">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="86026-210">要求</span><span class="sxs-lookup"><span data-stu-id="86026-210">Request</span></span>             | <span data-ttu-id="86026-211">回應</span><span class="sxs-lookup"><span data-stu-id="86026-211">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="86026-212">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="86026-212">localhost:1234</span></span>      | <span data-ttu-id="86026-213">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86026-213">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="86026-214">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="86026-214">localhost:1234/map1</span></span> | <span data-ttu-id="86026-215">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="86026-215">Map Test 1</span></span>                   |
| <span data-ttu-id="86026-216">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="86026-216">localhost:1234/map2</span></span> | <span data-ttu-id="86026-217">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="86026-217">Map Test 2</span></span>                   |
| <span data-ttu-id="86026-218">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="86026-218">localhost:1234/map3</span></span> | <span data-ttu-id="86026-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86026-219">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="86026-220">使用 `Map` 時，會將相符的路徑線段從 `HttpRequest.Path` 移除，並附加至每個要求的 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="86026-220">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="86026-221">`Map` 支援巢狀項目，例如：</span><span class="sxs-lookup"><span data-stu-id="86026-221">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="86026-222">`Map` 也可以一次比對多個線段：</span><span class="sxs-lookup"><span data-stu-id="86026-222">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="86026-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 會依據指定述詞的結果將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="86026-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="86026-224">`Func<HttpContext, bool>` 類型的任何述詞皆可用來將要求對應至管線的新分支。</span><span class="sxs-lookup"><span data-stu-id="86026-224">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="86026-225">下列範例會使用述詞來偵測查詢字串變數 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="86026-225">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="86026-226">下表顯示使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應：</span><span class="sxs-lookup"><span data-stu-id="86026-226">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="86026-227">要求</span><span class="sxs-lookup"><span data-stu-id="86026-227">Request</span></span>                       | <span data-ttu-id="86026-228">回應</span><span class="sxs-lookup"><span data-stu-id="86026-228">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="86026-229">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="86026-229">localhost:1234</span></span>                | <span data-ttu-id="86026-230">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86026-230">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="86026-231">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="86026-231">localhost:1234/?branch=master</span></span> | <span data-ttu-id="86026-232">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="86026-232">Branch used = master</span></span>         |

<span data-ttu-id="86026-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A>也會根據給定述詞的結果來分支要求管線。</span><span class="sxs-lookup"><span data-stu-id="86026-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="86026-234">與不同的 `MapWhen` 是，這個分支會重新加入主要管線（如果它不是短路或包含終端機中介軟體）：</span><span class="sxs-lookup"><span data-stu-id="86026-234">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="86026-235">在上述範例中，回應為「來自主要管線的 Hello」。</span><span class="sxs-lookup"><span data-stu-id="86026-235">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="86026-236">會針對所有要求寫入。</span><span class="sxs-lookup"><span data-stu-id="86026-236">is written for all requests.</span></span> <span data-ttu-id="86026-237">如果要求包含查詢字串變數，則 `branch` 會在重新加入主要管線之前記錄其值。</span><span class="sxs-lookup"><span data-stu-id="86026-237">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="86026-238">內建的中介軟體</span><span class="sxs-lookup"><span data-stu-id="86026-238">Built-in middleware</span></span>

<span data-ttu-id="86026-239">ASP.NET Core 隨附下列中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="86026-239">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="86026-240">「順序」\*\* 欄說明 中介軟體在要求處理管線中的位置，以及中介軟體可終止要求處理的情況。</span><span class="sxs-lookup"><span data-stu-id="86026-240">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="86026-241">當中介軟體將要求處理管線短路並防止接下來的下游中介軟體處理要求時，這就是所謂的「終端中介軟體」\*\*。</span><span class="sxs-lookup"><span data-stu-id="86026-241">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="86026-242">如需詳細資訊，請參閱[使用 IApplicationBuilder 建立中介軟體管線](#create-a-middleware-pipeline-with-iapplicationbuilder)。</span><span class="sxs-lookup"><span data-stu-id="86026-242">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="86026-243">中介軟體</span><span class="sxs-lookup"><span data-stu-id="86026-243">Middleware</span></span> | <span data-ttu-id="86026-244">描述</span><span class="sxs-lookup"><span data-stu-id="86026-244">Description</span></span> | <span data-ttu-id="86026-245">單</span><span class="sxs-lookup"><span data-stu-id="86026-245">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="86026-246">驗證</span><span class="sxs-lookup"><span data-stu-id="86026-246">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="86026-247">提供驗證支援。</span><span class="sxs-lookup"><span data-stu-id="86026-247">Provides authentication support.</span></span> | <span data-ttu-id="86026-248">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="86026-248">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="86026-249">OAuth 回呼的終端機。</span><span class="sxs-lookup"><span data-stu-id="86026-249">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="86026-250">授權</span><span class="sxs-lookup"><span data-stu-id="86026-250">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="86026-251">提供授權支援。</span><span class="sxs-lookup"><span data-stu-id="86026-251">Provides authorization support.</span></span> | <span data-ttu-id="86026-252">緊接在驗證中介軟體之後。</span><span class="sxs-lookup"><span data-stu-id="86026-252">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="86026-253">Cookie策略</span><span class="sxs-lookup"><span data-stu-id="86026-253">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="86026-254">追蹤使用者對儲存個人資訊的同意，並強制執列欄位的最低標準 cookie ，例如 `secure` 和 `SameSite` 。</span><span class="sxs-lookup"><span data-stu-id="86026-254">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="86026-255">在發出的中介軟體之前 cookie 。</span><span class="sxs-lookup"><span data-stu-id="86026-255">Before middleware that issues cookies.</span></span> <span data-ttu-id="86026-256">範例：驗證、工作階段、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="86026-256">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="86026-257">CORS</span><span class="sxs-lookup"><span data-stu-id="86026-257">CORS</span></span>](xref:security/cors) | <span data-ttu-id="86026-258">設定跨原始來源資源共用。</span><span class="sxs-lookup"><span data-stu-id="86026-258">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="86026-259">在使用 CORS 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-259">Before components that use CORS.</span></span> <span data-ttu-id="86026-260">`UseCors`目前必須在 `UseResponseCaching` [此 bug](https://github.com/dotnet/aspnetcore/issues/23218)之前進行。</span><span class="sxs-lookup"><span data-stu-id="86026-260">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="86026-261">診斷</span><span class="sxs-lookup"><span data-stu-id="86026-261">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="86026-262">提供開發人員例外狀況頁面、例外狀況處理、狀態字碼頁，以及新應用程式的預設網頁的數個個別中介軟體。</span><span class="sxs-lookup"><span data-stu-id="86026-262">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="86026-263">在產生錯誤的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-263">Before components that generate errors.</span></span> <span data-ttu-id="86026-264">終端機的例外狀況，或為新的應用程式提供預設的網頁。</span><span class="sxs-lookup"><span data-stu-id="86026-264">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="86026-265">轉送的標頭</span><span class="sxs-lookup"><span data-stu-id="86026-265">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="86026-266">將設為 Proxy 的標頭轉送到目前要求。</span><span class="sxs-lookup"><span data-stu-id="86026-266">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="86026-267">在使用更新方法的欄位之前。</span><span class="sxs-lookup"><span data-stu-id="86026-267">Before components that consume the updated fields.</span></span> <span data-ttu-id="86026-268">範例：配置、主機，用戶端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="86026-268">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="86026-269">健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="86026-269">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="86026-270">檢查 ASP.NET Core 應用程式及其相依性的健康狀態，例如檢查資料庫可用性。</span><span class="sxs-lookup"><span data-stu-id="86026-270">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="86026-271">若某項要求與健康狀態檢查端點相符，則會是終端機。</span><span class="sxs-lookup"><span data-stu-id="86026-271">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="86026-272">標頭傳播</span><span class="sxs-lookup"><span data-stu-id="86026-272">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="86026-273">將 HTTP 標頭從傳入要求傳播到傳出的 HTTP 用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="86026-273">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="86026-274">HTTP 方法覆寫</span><span class="sxs-lookup"><span data-stu-id="86026-274">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="86026-275">允許傳入的 POST 要求覆寫方法。</span><span class="sxs-lookup"><span data-stu-id="86026-275">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="86026-276">在使用更新方法的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-276">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="86026-277">HTTPS 重新導向</span><span class="sxs-lookup"><span data-stu-id="86026-277">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="86026-278">將所有 HTTP 要求都重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="86026-278">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="86026-279">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-279">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="86026-280">HTTP 嚴格的傳輸安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="86026-280">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="86026-281">增強安全性的中介軟體，可新增特殊的回應標頭。</span><span class="sxs-lookup"><span data-stu-id="86026-281">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="86026-282">在傳送回應前和修改要求的元件後。</span><span class="sxs-lookup"><span data-stu-id="86026-282">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="86026-283">範例：轉送的標頭、URL 重寫。</span><span class="sxs-lookup"><span data-stu-id="86026-283">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="86026-284">MVC</span><span class="sxs-lookup"><span data-stu-id="86026-284">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="86026-285">處理 MVC/Pages 的要求 Razor 。</span><span class="sxs-lookup"><span data-stu-id="86026-285">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="86026-286">若要求符合路由則終止。</span><span class="sxs-lookup"><span data-stu-id="86026-286">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="86026-287">OWIN</span><span class="sxs-lookup"><span data-stu-id="86026-287">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="86026-288">以 OWIN 為基礎之應用程式、伺服器和中介軟體的 Interop。</span><span class="sxs-lookup"><span data-stu-id="86026-288">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="86026-289">若 OWIN 中介軟體完全處理要求則終止。</span><span class="sxs-lookup"><span data-stu-id="86026-289">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="86026-290">回應快取</span><span class="sxs-lookup"><span data-stu-id="86026-290">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="86026-291">提供快取回應的支援。</span><span class="sxs-lookup"><span data-stu-id="86026-291">Provides support for caching responses.</span></span> | <span data-ttu-id="86026-292">在需要快取的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-292">Before components that require caching.</span></span> <span data-ttu-id="86026-293">`UseCORS`必須早于 `UseResponseCaching` 。</span><span class="sxs-lookup"><span data-stu-id="86026-293">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="86026-294">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="86026-294">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="86026-295">提供壓縮回應的支援。</span><span class="sxs-lookup"><span data-stu-id="86026-295">Provides support for compressing responses.</span></span> | <span data-ttu-id="86026-296">在需要壓縮的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-296">Before components that require compression.</span></span> |
| [<span data-ttu-id="86026-297">要求當地語系化</span><span class="sxs-lookup"><span data-stu-id="86026-297">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="86026-298">提供當地語系化支援。</span><span class="sxs-lookup"><span data-stu-id="86026-298">Provides localization support.</span></span> | <span data-ttu-id="86026-299">在偵測當地語系化的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-299">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="86026-300">端點路由</span><span class="sxs-lookup"><span data-stu-id="86026-300">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="86026-301">定義並限制要求路由。</span><span class="sxs-lookup"><span data-stu-id="86026-301">Defines and constrains request routes.</span></span> | <span data-ttu-id="86026-302">比對路由的終端機。</span><span class="sxs-lookup"><span data-stu-id="86026-302">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="86026-303">SPA</span><span class="sxs-lookup"><span data-stu-id="86026-303">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="86026-304">藉由傳回單一頁面應用程式 (SPA 的預設頁面，處理來自中介軟體鏈中此點的所有要求) </span><span class="sxs-lookup"><span data-stu-id="86026-304">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="86026-305">在鏈晚期，讓提供靜態檔案、MVC 動作等的其他中介軟體優先。</span><span class="sxs-lookup"><span data-stu-id="86026-305">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="86026-306">工作階段</span><span class="sxs-lookup"><span data-stu-id="86026-306">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="86026-307">提供管理使用者工作階段的支援。</span><span class="sxs-lookup"><span data-stu-id="86026-307">Provides support for managing user sessions.</span></span> | <span data-ttu-id="86026-308">在需要工作階段的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-308">Before components that require Session.</span></span> | 
| [<span data-ttu-id="86026-309">靜態檔案</span><span class="sxs-lookup"><span data-stu-id="86026-309">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="86026-310">支援靜態檔案的提供和目錄瀏覽。</span><span class="sxs-lookup"><span data-stu-id="86026-310">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="86026-311">若要求符合檔案則終止。</span><span class="sxs-lookup"><span data-stu-id="86026-311">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="86026-312">URL 重寫</span><span class="sxs-lookup"><span data-stu-id="86026-312">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="86026-313">提供重寫 URL 及重新導向要求的支援。</span><span class="sxs-lookup"><span data-stu-id="86026-313">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="86026-314">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-314">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="86026-315">WebSocket</span><span class="sxs-lookup"><span data-stu-id="86026-315">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="86026-316">啟用 WebSockets 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="86026-316">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="86026-317">在接受 WebSocket 要求的必要元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-317">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="86026-318">其他資源</span><span class="sxs-lookup"><span data-stu-id="86026-318">Additional resources</span></span>

* <span data-ttu-id="86026-319">[存留期和註冊選項](xref:fundamentals/dependency-injection#lifetime-and-registration-options)包含中介軟體的完整範例，內含*範圍*、*暫時性*和*單一*存留期服務。</span><span class="sxs-lookup"><span data-stu-id="86026-319">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped*, *transient*, and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="86026-320">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Steve Smith](https://ardalis.com/) 撰寫</span><span class="sxs-lookup"><span data-stu-id="86026-320">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="86026-321">中介軟體為組成應用程式管線的軟體，用以處理要求與回應。</span><span class="sxs-lookup"><span data-stu-id="86026-321">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="86026-322">每個元件：</span><span class="sxs-lookup"><span data-stu-id="86026-322">Each component:</span></span>

* <span data-ttu-id="86026-323">可選擇是否要將要求傳送到管線中的下一個元件。</span><span class="sxs-lookup"><span data-stu-id="86026-323">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="86026-324">可以下一個元件的前後執行工作。</span><span class="sxs-lookup"><span data-stu-id="86026-324">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="86026-325">要求委派用於建置要求管線，</span><span class="sxs-lookup"><span data-stu-id="86026-325">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="86026-326">其會處理每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="86026-326">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="86026-327">要求委派的設定方式為使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="86026-327">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="86026-328">您可將個別要求委派指定為內嵌匿名方法 (在內嵌中介軟體中呼叫)，或於可重複使用的類別中加以定義。</span><span class="sxs-lookup"><span data-stu-id="86026-328">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="86026-329">這些可重複使用的類別及內嵌匿名方法皆為「中介軟體」**，也稱為「中介軟體元件」**。</span><span class="sxs-lookup"><span data-stu-id="86026-329">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="86026-330">要求管線中的每個中介軟體元件負責叫用管線中下一個元件，或對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="86026-330">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="86026-331">當中介軟體短路時，稱為「終端中介軟體」\*\*，因為它會防止接下來的中介軟體處理要求。</span><span class="sxs-lookup"><span data-stu-id="86026-331">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="86026-332"><xref:migration/http-modules> 說明 ASP.NET Core 和 ASP.NET 4.x 之間的要求管線差異，並提供更多中介軟體範例。</span><span class="sxs-lookup"><span data-stu-id="86026-332"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="86026-333">使用 IApplicationBuilder 建立中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="86026-333">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="86026-334">ASP.NET Core 要求管線由要求委派序列組成，並會一個接著一個呼叫。</span><span class="sxs-lookup"><span data-stu-id="86026-334">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="86026-335">下圖說明此概念。</span><span class="sxs-lookup"><span data-stu-id="86026-335">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="86026-336">執行緒遵循黑色箭號執行。</span><span class="sxs-lookup"><span data-stu-id="86026-336">The thread of execution follows the black arrows.</span></span>

![要求處理模式說明要求抵達，經過三個中介軟體所處理，然後應用程式送出回應。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="86026-340">每一個委派皆能在下個委派的前後執行作業。</span><span class="sxs-lookup"><span data-stu-id="86026-340">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="86026-341">處理例外狀況的委派必須提前在管線中呼叫，以便其可與管線後續階段中所發生的例外狀況達成一致。</span><span class="sxs-lookup"><span data-stu-id="86026-341">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="86026-342">最簡潔的 ASP.NET Core 應用程式會設定單一要求委派來處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="86026-342">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="86026-343">此情況不包含實際要求管線。</span><span class="sxs-lookup"><span data-stu-id="86026-343">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="86026-344">反之，系統會呼叫單一匿名函式來回應每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="86026-344">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="86026-345">第一個 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委派會終止管線。</span><span class="sxs-lookup"><span data-stu-id="86026-345">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="86026-346">將多個要求委派鏈結在一起的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>。</span><span class="sxs-lookup"><span data-stu-id="86026-346">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="86026-347">`next` 參數代表管線中的下個委派。</span><span class="sxs-lookup"><span data-stu-id="86026-347">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="86026-348">您可以「不」\*\* 呼叫「下一個」\*\* 參數來對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="86026-348">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="86026-349">您通常可以在下個委派的前後執行動作，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="86026-349">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="86026-350">當委派不將要求傳遞到下一個委派時，這就是所謂*讓要求管線短路*。</span><span class="sxs-lookup"><span data-stu-id="86026-350">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="86026-351">因為最少運算可避免不必要的工作，所以經常使用。</span><span class="sxs-lookup"><span data-stu-id="86026-351">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="86026-352">例如，[靜態檔案中介軟體](xref:fundamentals/static-files)可以做為*終端中介軟體*使用，方式是處理靜態檔案的要求，並對剩餘的管線執行短路。</span><span class="sxs-lookup"><span data-stu-id="86026-352">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="86026-353">在中介軟體之前新增到管線且終結進一步處理的中介軟體在其 `next.Invoke` 陳述式之後仍然處理程式碼。</span><span class="sxs-lookup"><span data-stu-id="86026-353">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="86026-354">不過，查看下列有關嘗試寫入已傳送之回應的警告。</span><span class="sxs-lookup"><span data-stu-id="86026-354">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="86026-355">請不要在回應已傳送給用戶端之後呼叫 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="86026-355">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="86026-356">回應啟動後，變更為 <xref:Microsoft.AspNetCore.Http.HttpResponse> 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="86026-356">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="86026-357">例如，變更設定標頭、狀態碼等都會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="86026-357">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="86026-358">若在呼叫 `next` 後寫入回應本文：</span><span class="sxs-lookup"><span data-stu-id="86026-358">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="86026-359">可能導致違反通訊協定。</span><span class="sxs-lookup"><span data-stu-id="86026-359">May cause a protocol violation.</span></span> <span data-ttu-id="86026-360">例如，寫入超過指定 `Content-Length` 的內容。</span><span class="sxs-lookup"><span data-stu-id="86026-360">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="86026-361">可能損毀本文格式。</span><span class="sxs-lookup"><span data-stu-id="86026-361">May corrupt the body format.</span></span> <span data-ttu-id="86026-362">例如，將 HTML 頁尾寫入 CSS 檔。</span><span class="sxs-lookup"><span data-stu-id="86026-362">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="86026-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是實用的提示，可表示是否已傳送標頭 (或) 是否已寫入本文。</span><span class="sxs-lookup"><span data-stu-id="86026-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="86026-364">中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="86026-364">Middleware order</span></span>

<span data-ttu-id="86026-365">`Startup.Configure` 方法內中介軟體元件的新增順序可定義在要求時叫用中介軟體元件的順序及回應的反向順序。</span><span class="sxs-lookup"><span data-stu-id="86026-365">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="86026-366">順序對於安全性、效能和功能非常**重要**。</span><span class="sxs-lookup"><span data-stu-id="86026-366">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="86026-367">下列 `Startup.Configure` 方法會以建議的順序新增安全性相關中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="86026-367">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="86026-368">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="86026-368">In the preceding code:</span></span>

* <span data-ttu-id="86026-369">使用[個別使用者帳戶](xref:security/authentication/identity)建立新的 web 應用程式時，未新增的中介軟體會加上批註。</span><span class="sxs-lookup"><span data-stu-id="86026-369">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="86026-370">並非每個中介軟體都必須以這種完全相同的循序執行，但也有許多。</span><span class="sxs-lookup"><span data-stu-id="86026-370">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="86026-371">例如， `UseCors` 和 `UseAuthentication` 必須以顯示的循序執行。</span><span class="sxs-lookup"><span data-stu-id="86026-371">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="86026-372">下列 `Startup.Configure` 方法會新增適用於一般應用程式案例的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="86026-372">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="86026-373">例外狀況/錯誤處理</span><span class="sxs-lookup"><span data-stu-id="86026-373">Exception/error handling</span></span>
   * <span data-ttu-id="86026-374">當應用程式在開發環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="86026-374">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="86026-375">開發人員例外狀況頁面中介軟體 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 會回報應用程式執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="86026-375">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="86026-376">資料錯誤頁面中介軟體 (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) 會回報資料庫執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="86026-376">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="86026-377">當應用程式在生產環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="86026-377">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="86026-378">例外狀況處理常式中介軟體 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 會攔截在下列中介軟體中擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="86026-378">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="86026-379">HTTP 靜態傳輸安全性通訊協定 (HSTS) 中介軟體 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 會新增 `Strict-Transport-Security` 標頭。</span><span class="sxs-lookup"><span data-stu-id="86026-379">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="86026-380">HTTPS 重新導向中介軟體 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 會將 HTTP 要求重新導向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="86026-380">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="86026-381">靜態檔案中介軟體 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 會傳回靜態檔案並縮短進一步的要求處理時間。</span><span class="sxs-lookup"><span data-stu-id="86026-381">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="86026-382">Cookie原則中介軟體 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) 符合歐盟一般資料保護規定 (GDPR) 法規的應用程式。</span><span class="sxs-lookup"><span data-stu-id="86026-382">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="86026-383">驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 會嘗試在允許使用者存取安全資源之前先驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="86026-383">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="86026-384">工作階段中介軟體 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 會建立並維護工作階段狀態。</span><span class="sxs-lookup"><span data-stu-id="86026-384">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="86026-385">如果應用程式使用會話狀態，請在 Cookie 原則中介軟體之後和 MVC 中介軟體之前呼叫會話中介軟體。</span><span class="sxs-lookup"><span data-stu-id="86026-385">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="86026-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) 以將 MVC 新增到要求管線。</span><span class="sxs-lookup"><span data-stu-id="86026-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="86026-387">在上面的範例程式碼中，每個中介軟體擴充方法都會透過 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空間在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公開。</span><span class="sxs-lookup"><span data-stu-id="86026-387">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="86026-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是第一個新增到管道的中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="86026-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="86026-389">因此，例外處理常式中介軟體會攔截後續呼叫中發生的所有例外狀況。</span><span class="sxs-lookup"><span data-stu-id="86026-389">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="86026-390">靜態檔案中介軟體會提前在管線中呼叫，以便其無須逐一處理剩餘的元件，就能處理要求及執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="86026-390">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="86026-391">靜態檔案中介軟體**不**提供授權檢查。</span><span class="sxs-lookup"><span data-stu-id="86026-391">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="86026-392">靜態檔案中介軟體所提供的任何檔案（包括*wwwroot*底下的檔案）皆可公開使用。</span><span class="sxs-lookup"><span data-stu-id="86026-392">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="86026-393">如需保護靜態檔案的方法，請參閱 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="86026-393">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="86026-394">若靜態檔案中介軟體未處理要求，該要求會繼續傳遞給執行驗證的驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="86026-394">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="86026-395">驗證不會對未經驗證的要求執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="86026-395">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="86026-396">雖然驗證中介軟體會驗證要求，但只有在 MVC 選取特定 Razor 頁面或 MVC 控制器和動作之後，才會執行授權 (和拒絕) 。</span><span class="sxs-lookup"><span data-stu-id="86026-396">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="86026-397">下列範例示範靜態檔案中介軟體在回應壓縮中介軟體之前處理靜態檔案要求之前的靜態檔案順序。</span><span class="sxs-lookup"><span data-stu-id="86026-397">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="86026-398">靜態檔案並不會以此中介軟體順序壓縮。</span><span class="sxs-lookup"><span data-stu-id="86026-398">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="86026-399">來自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> 的 MVC 回應可壓縮。</span><span class="sxs-lookup"><span data-stu-id="86026-399">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="86026-400">Use、Run 與 Map</span><span class="sxs-lookup"><span data-stu-id="86026-400">Use, Run, and Map</span></span>

<span data-ttu-id="86026-401">設定 HTTP 管線的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 及 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>。</span><span class="sxs-lookup"><span data-stu-id="86026-401">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="86026-402">`Use` 方法可對管線執行最少運算 (亦即，如果其不呼叫 `next` 要求委派的情況下)。</span><span class="sxs-lookup"><span data-stu-id="86026-402">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="86026-403">`Run` 是一種慣例，且有些中介軟體元件可能將執行於管線尾端的 `Run[Middleware]` 方法公開。</span><span class="sxs-lookup"><span data-stu-id="86026-403">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="86026-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 擴充方法則是用來分支管線的慣例。</span><span class="sxs-lookup"><span data-stu-id="86026-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="86026-405">`Map` 會依據指定要求路徑的相符項目將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="86026-405">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="86026-406">如果要求路徑以指定路徑為開頭，則會執行分支。</span><span class="sxs-lookup"><span data-stu-id="86026-406">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="86026-407">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="86026-407">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="86026-408">要求</span><span class="sxs-lookup"><span data-stu-id="86026-408">Request</span></span>             | <span data-ttu-id="86026-409">回應</span><span class="sxs-lookup"><span data-stu-id="86026-409">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="86026-410">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="86026-410">localhost:1234</span></span>      | <span data-ttu-id="86026-411">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86026-411">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="86026-412">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="86026-412">localhost:1234/map1</span></span> | <span data-ttu-id="86026-413">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="86026-413">Map Test 1</span></span>                   |
| <span data-ttu-id="86026-414">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="86026-414">localhost:1234/map2</span></span> | <span data-ttu-id="86026-415">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="86026-415">Map Test 2</span></span>                   |
| <span data-ttu-id="86026-416">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="86026-416">localhost:1234/map3</span></span> | <span data-ttu-id="86026-417">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86026-417">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="86026-418">使用 `Map` 時，會將相符的路徑線段從 `HttpRequest.Path` 移除，並附加至每個要求的 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="86026-418">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="86026-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 會依據指定述詞的結果將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="86026-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="86026-420">`Func<HttpContext, bool>` 類型的任何述詞皆可用來將要求對應至管線的新分支。</span><span class="sxs-lookup"><span data-stu-id="86026-420">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="86026-421">下列範例會使用述詞來偵測查詢字串變數 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="86026-421">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="86026-422">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="86026-422">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="86026-423">要求</span><span class="sxs-lookup"><span data-stu-id="86026-423">Request</span></span>                       | <span data-ttu-id="86026-424">回應</span><span class="sxs-lookup"><span data-stu-id="86026-424">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="86026-425">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="86026-425">localhost:1234</span></span>                | <span data-ttu-id="86026-426">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86026-426">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="86026-427">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="86026-427">localhost:1234/?branch=master</span></span> | <span data-ttu-id="86026-428">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="86026-428">Branch used = master</span></span>         |

<span data-ttu-id="86026-429">`Map` 支援巢狀項目，例如：</span><span class="sxs-lookup"><span data-stu-id="86026-429">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="86026-430">`Map` 也可以一次比對多個線段：</span><span class="sxs-lookup"><span data-stu-id="86026-430">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="86026-431">內建的中介軟體</span><span class="sxs-lookup"><span data-stu-id="86026-431">Built-in middleware</span></span>

<span data-ttu-id="86026-432">ASP.NET Core 隨附下列中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="86026-432">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="86026-433">「順序」\*\* 欄說明 中介軟體在要求處理管線中的位置，以及中介軟體可終止要求處理的情況。</span><span class="sxs-lookup"><span data-stu-id="86026-433">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="86026-434">當中介軟體將要求處理管線短路並防止接下來的下游中介軟體處理要求時，這就是所謂的「終端中介軟體」\*\*。</span><span class="sxs-lookup"><span data-stu-id="86026-434">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="86026-435">如需詳細資訊，請參閱[使用 IApplicationBuilder 建立中介軟體管線](#create-a-middleware-pipeline-with-iapplicationbuilder)。</span><span class="sxs-lookup"><span data-stu-id="86026-435">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="86026-436">中介軟體</span><span class="sxs-lookup"><span data-stu-id="86026-436">Middleware</span></span> | <span data-ttu-id="86026-437">描述</span><span class="sxs-lookup"><span data-stu-id="86026-437">Description</span></span> | <span data-ttu-id="86026-438">單</span><span class="sxs-lookup"><span data-stu-id="86026-438">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="86026-439">驗證</span><span class="sxs-lookup"><span data-stu-id="86026-439">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="86026-440">提供驗證支援。</span><span class="sxs-lookup"><span data-stu-id="86026-440">Provides authentication support.</span></span> | <span data-ttu-id="86026-441">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="86026-441">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="86026-442">OAuth 回呼的終端機。</span><span class="sxs-lookup"><span data-stu-id="86026-442">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="86026-443">Cookie策略</span><span class="sxs-lookup"><span data-stu-id="86026-443">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="86026-444">追蹤使用者對儲存個人資訊的同意，並強制執列欄位的最低標準 cookie ，例如 `secure` 和 `SameSite` 。</span><span class="sxs-lookup"><span data-stu-id="86026-444">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="86026-445">在發出的中介軟體之前 cookie 。</span><span class="sxs-lookup"><span data-stu-id="86026-445">Before middleware that issues cookies.</span></span> <span data-ttu-id="86026-446">範例：驗證、工作階段、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="86026-446">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="86026-447">CORS</span><span class="sxs-lookup"><span data-stu-id="86026-447">CORS</span></span>](xref:security/cors) | <span data-ttu-id="86026-448">設定跨原始來源資源共用。</span><span class="sxs-lookup"><span data-stu-id="86026-448">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="86026-449">在使用 CORS 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-449">Before components that use CORS.</span></span> |
| [<span data-ttu-id="86026-450">診斷</span><span class="sxs-lookup"><span data-stu-id="86026-450">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="86026-451">提供開發人員例外狀況頁面、例外狀況處理、狀態字碼頁，以及新應用程式的預設網頁的數個個別中介軟體。</span><span class="sxs-lookup"><span data-stu-id="86026-451">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="86026-452">在產生錯誤的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-452">Before components that generate errors.</span></span> <span data-ttu-id="86026-453">終端機的例外狀況，或為新的應用程式提供預設的網頁。</span><span class="sxs-lookup"><span data-stu-id="86026-453">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="86026-454">轉送的標頭</span><span class="sxs-lookup"><span data-stu-id="86026-454">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="86026-455">將設為 Proxy 的標頭轉送到目前要求。</span><span class="sxs-lookup"><span data-stu-id="86026-455">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="86026-456">在使用更新方法的欄位之前。</span><span class="sxs-lookup"><span data-stu-id="86026-456">Before components that consume the updated fields.</span></span> <span data-ttu-id="86026-457">範例：配置、主機，用戶端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="86026-457">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="86026-458">健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="86026-458">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="86026-459">檢查 ASP.NET Core 應用程式及其相依性的健康狀態，例如檢查資料庫可用性。</span><span class="sxs-lookup"><span data-stu-id="86026-459">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="86026-460">若某項要求與健康狀態檢查端點相符，則會是終端機。</span><span class="sxs-lookup"><span data-stu-id="86026-460">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="86026-461">HTTP 方法覆寫</span><span class="sxs-lookup"><span data-stu-id="86026-461">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="86026-462">允許傳入的 POST 要求覆寫方法。</span><span class="sxs-lookup"><span data-stu-id="86026-462">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="86026-463">在使用更新方法的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-463">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="86026-464">HTTPS 重新導向</span><span class="sxs-lookup"><span data-stu-id="86026-464">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="86026-465">將所有 HTTP 要求都重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="86026-465">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="86026-466">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-466">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="86026-467">HTTP 嚴格的傳輸安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="86026-467">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="86026-468">增強安全性的中介軟體，可新增特殊的回應標頭。</span><span class="sxs-lookup"><span data-stu-id="86026-468">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="86026-469">在傳送回應前和修改要求的元件後。</span><span class="sxs-lookup"><span data-stu-id="86026-469">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="86026-470">範例：轉送的標頭、URL 重寫。</span><span class="sxs-lookup"><span data-stu-id="86026-470">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="86026-471">MVC</span><span class="sxs-lookup"><span data-stu-id="86026-471">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="86026-472">處理 MVC/Pages 的要求 Razor 。</span><span class="sxs-lookup"><span data-stu-id="86026-472">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="86026-473">若要求符合路由則終止。</span><span class="sxs-lookup"><span data-stu-id="86026-473">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="86026-474">OWIN</span><span class="sxs-lookup"><span data-stu-id="86026-474">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="86026-475">以 OWIN 為基礎之應用程式、伺服器和中介軟體的 Interop。</span><span class="sxs-lookup"><span data-stu-id="86026-475">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="86026-476">若 OWIN 中介軟體完全處理要求則終止。</span><span class="sxs-lookup"><span data-stu-id="86026-476">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="86026-477">回應快取</span><span class="sxs-lookup"><span data-stu-id="86026-477">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="86026-478">提供快取回應的支援。</span><span class="sxs-lookup"><span data-stu-id="86026-478">Provides support for caching responses.</span></span> | <span data-ttu-id="86026-479">在需要快取的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-479">Before components that require caching.</span></span> |
| [<span data-ttu-id="86026-480">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="86026-480">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="86026-481">提供壓縮回應的支援。</span><span class="sxs-lookup"><span data-stu-id="86026-481">Provides support for compressing responses.</span></span> | <span data-ttu-id="86026-482">在需要壓縮的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-482">Before components that require compression.</span></span> |
| [<span data-ttu-id="86026-483">要求當地語系化</span><span class="sxs-lookup"><span data-stu-id="86026-483">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="86026-484">提供當地語系化支援。</span><span class="sxs-lookup"><span data-stu-id="86026-484">Provides localization support.</span></span> | <span data-ttu-id="86026-485">在偵測當地語系化的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-485">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="86026-486">端點路由</span><span class="sxs-lookup"><span data-stu-id="86026-486">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="86026-487">定義並限制要求路由。</span><span class="sxs-lookup"><span data-stu-id="86026-487">Defines and constrains request routes.</span></span> | <span data-ttu-id="86026-488">比對路由的終端機。</span><span class="sxs-lookup"><span data-stu-id="86026-488">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="86026-489">工作階段</span><span class="sxs-lookup"><span data-stu-id="86026-489">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="86026-490">提供管理使用者工作階段的支援。</span><span class="sxs-lookup"><span data-stu-id="86026-490">Provides support for managing user sessions.</span></span> | <span data-ttu-id="86026-491">在需要工作階段的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-491">Before components that require Session.</span></span> |
| [<span data-ttu-id="86026-492">靜態檔案</span><span class="sxs-lookup"><span data-stu-id="86026-492">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="86026-493">支援靜態檔案的提供和目錄瀏覽。</span><span class="sxs-lookup"><span data-stu-id="86026-493">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="86026-494">若要求符合檔案則終止。</span><span class="sxs-lookup"><span data-stu-id="86026-494">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="86026-495">URL 重寫</span><span class="sxs-lookup"><span data-stu-id="86026-495">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="86026-496">提供重寫 URL 及重新導向要求的支援。</span><span class="sxs-lookup"><span data-stu-id="86026-496">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="86026-497">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-497">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="86026-498">WebSocket</span><span class="sxs-lookup"><span data-stu-id="86026-498">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="86026-499">啟用 WebSockets 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="86026-499">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="86026-500">在接受 WebSocket 要求的必要元件之前。</span><span class="sxs-lookup"><span data-stu-id="86026-500">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="86026-501">其他資源</span><span class="sxs-lookup"><span data-stu-id="86026-501">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
