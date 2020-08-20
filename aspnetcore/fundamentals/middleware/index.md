---
title: ASP.NET Core 中介軟體
author: rick-anderson
description: 了解 ASP.NET Core 中介軟體和要求管線。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: 32a4e54a46f062f3ff45d0b840237be53406dbdb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630883"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="8256c-103">ASP.NET Core 中介軟體</span><span class="sxs-lookup"><span data-stu-id="8256c-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8256c-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Steve Smith](https://ardalis.com/) 撰寫</span><span class="sxs-lookup"><span data-stu-id="8256c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8256c-105">中介軟體為組成應用程式管線的軟體，用以處理要求與回應。</span><span class="sxs-lookup"><span data-stu-id="8256c-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="8256c-106">每個元件：</span><span class="sxs-lookup"><span data-stu-id="8256c-106">Each component:</span></span>

* <span data-ttu-id="8256c-107">可選擇是否要將要求傳送到管線中的下一個元件。</span><span class="sxs-lookup"><span data-stu-id="8256c-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="8256c-108">可以下一個元件的前後執行工作。</span><span class="sxs-lookup"><span data-stu-id="8256c-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="8256c-109">要求委派用於建置要求管線，</span><span class="sxs-lookup"><span data-stu-id="8256c-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="8256c-110">其會處理每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="8256c-111">要求委派的設定方式為使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="8256c-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="8256c-112">您可將個別要求委派指定為內嵌匿名方法 (在內嵌中介軟體中呼叫)，或於可重複使用的類別中加以定義。</span><span class="sxs-lookup"><span data-stu-id="8256c-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="8256c-113">這些可重複使用的類別及內嵌匿名方法皆為「中介軟體」**，也稱為「中介軟體元件」**。</span><span class="sxs-lookup"><span data-stu-id="8256c-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="8256c-114">要求管線中的每個中介軟體元件負責叫用管線中下一個元件，或對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="8256c-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="8256c-115">當中介軟體短路時，稱為「終端中介軟體」\*\*，因為它會防止接下來的中介軟體處理要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="8256c-116"><xref:migration/http-modules> 說明 ASP.NET Core 和 ASP.NET 4.x 之間的要求管線差異，並提供更多中介軟體範例。</span><span class="sxs-lookup"><span data-stu-id="8256c-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="8256c-117">使用 IApplicationBuilder 建立中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="8256c-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="8256c-118">ASP.NET Core 要求管線由要求委派序列組成，並會一個接著一個呼叫。</span><span class="sxs-lookup"><span data-stu-id="8256c-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="8256c-119">下圖說明此概念。</span><span class="sxs-lookup"><span data-stu-id="8256c-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="8256c-120">執行緒遵循黑色箭號執行。</span><span class="sxs-lookup"><span data-stu-id="8256c-120">The thread of execution follows the black arrows.</span></span>

![要求處理模式說明要求抵達，經過三個中介軟體所處理，然後應用程式送出回應。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="8256c-124">每一個委派皆能在下個委派的前後執行作業。</span><span class="sxs-lookup"><span data-stu-id="8256c-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="8256c-125">處理例外狀況的委派必須提前在管線中呼叫，以便其可與管線後續階段中所發生的例外狀況達成一致。</span><span class="sxs-lookup"><span data-stu-id="8256c-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="8256c-126">最簡潔的 ASP.NET Core 應用程式會設定單一要求委派來處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="8256c-127">此情況不包含實際要求管線。</span><span class="sxs-lookup"><span data-stu-id="8256c-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="8256c-128">反之，系統會呼叫單一匿名函式來回應每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="8256c-129">將多個要求委派鏈結在一起的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>。</span><span class="sxs-lookup"><span data-stu-id="8256c-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="8256c-130">`next` 參數代表管線中的下個委派。</span><span class="sxs-lookup"><span data-stu-id="8256c-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="8256c-131">您可以「不」\*\* 呼叫「下一個」\*\* 參數來對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="8256c-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="8256c-132">您通常可以在下個委派的前後執行動作，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="8256c-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="8256c-133">當委派不將要求傳遞到下一個委派時，這就是所謂*讓要求管線短路*。</span><span class="sxs-lookup"><span data-stu-id="8256c-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="8256c-134">因為最少運算可避免不必要的工作，所以經常使用。</span><span class="sxs-lookup"><span data-stu-id="8256c-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="8256c-135">例如，[靜態檔案中介軟體](xref:fundamentals/static-files)可以做為*終端中介軟體*使用，方式是處理靜態檔案的要求，並對剩餘的管線執行短路。</span><span class="sxs-lookup"><span data-stu-id="8256c-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="8256c-136">在中介軟體之前新增到管線且終結進一步處理的中介軟體在其 `next.Invoke` 陳述式之後仍然處理程式碼。</span><span class="sxs-lookup"><span data-stu-id="8256c-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="8256c-137">不過，查看下列有關嘗試寫入已傳送之回應的警告。</span><span class="sxs-lookup"><span data-stu-id="8256c-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="8256c-138">請不要在回應已傳送給用戶端之後呼叫 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="8256c-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="8256c-139">回應啟動後，變更為 <xref:Microsoft.AspNetCore.Http.HttpResponse> 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8256c-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="8256c-140">例如， [設定標頭和狀態碼會擲回例外狀況](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started)。</span><span class="sxs-lookup"><span data-stu-id="8256c-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="8256c-141">若在呼叫 `next` 後寫入回應本文：</span><span class="sxs-lookup"><span data-stu-id="8256c-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="8256c-142">可能導致違反通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8256c-142">May cause a protocol violation.</span></span> <span data-ttu-id="8256c-143">例如，寫入超過指定 `Content-Length` 的內容。</span><span class="sxs-lookup"><span data-stu-id="8256c-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="8256c-144">可能損毀本文格式。</span><span class="sxs-lookup"><span data-stu-id="8256c-144">May corrupt the body format.</span></span> <span data-ttu-id="8256c-145">例如，將 HTML 頁尾寫入 CSS 檔。</span><span class="sxs-lookup"><span data-stu-id="8256c-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="8256c-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是實用的提示，可表示是否已傳送標頭 (或) 是否已寫入本文。</span><span class="sxs-lookup"><span data-stu-id="8256c-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="8256c-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委派不會收到 `next` 參數。</span><span class="sxs-lookup"><span data-stu-id="8256c-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="8256c-148">第一個 `Run` 委派一律是 terminal 並終止管線。</span><span class="sxs-lookup"><span data-stu-id="8256c-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="8256c-149">`Run` 是慣例。</span><span class="sxs-lookup"><span data-stu-id="8256c-149">`Run` is a convention.</span></span> <span data-ttu-id="8256c-150">某些中介軟體元件可能會公開在 `Run[Middleware]` 管線結尾執行的方法：</span><span class="sxs-lookup"><span data-stu-id="8256c-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8256c-151">在上述範例中， `Run` 委派會寫入 `"Hello from 2nd delegate."` 至回應，然後終止管線。</span><span class="sxs-lookup"><span data-stu-id="8256c-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="8256c-152">如果 `Use` `Run` 在委派之後加入另一個或委派 `Run` ，則不會呼叫它。</span><span class="sxs-lookup"><span data-stu-id="8256c-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="8256c-153">中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="8256c-153">Middleware order</span></span>

<span data-ttu-id="8256c-154">下圖顯示 ASP.NET Core MVC 和 Pages 應用程式的完整要求處理管線 Razor 。</span><span class="sxs-lookup"><span data-stu-id="8256c-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="8256c-155">您可以在一般的應用程式中查看如何排序現有的中介軟體，以及新增自訂中介軟體的位置。</span><span class="sxs-lookup"><span data-stu-id="8256c-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="8256c-156">您可以完整控制如何重新排列現有的中介軟體，或在您的案例中插入新的自訂中介軟體。</span><span class="sxs-lookup"><span data-stu-id="8256c-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core 中介軟體管線](index/_static/middleware-pipeline.svg)

<span data-ttu-id="8256c-158">上圖中的 **端點** 中介軟體會針對對應的應用程式類型 &mdash; MVC 或頁面執行篩選準則管線 Razor 。</span><span class="sxs-lookup"><span data-stu-id="8256c-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core 篩選準則管線](index/_static/mvc-endpoint.svg)

<span data-ttu-id="8256c-160">`Startup.Configure` 方法內中介軟體元件的新增順序可定義在要求時叫用中介軟體元件的順序及回應的反向順序。</span><span class="sxs-lookup"><span data-stu-id="8256c-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="8256c-161">對安全性、效能和功能而言，順序是非常 **重要** 的。</span><span class="sxs-lookup"><span data-stu-id="8256c-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="8256c-162">下列方法會依 `Startup.Configure` 建議的順序新增安全性相關的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="8256c-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="8256c-163">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="8256c-163">In the preceding code:</span></span>

* <span data-ttu-id="8256c-164">以 [個別使用者帳戶](xref:security/authentication/identity) 建立新的 web 應用程式時，未新增的中介軟體會以批註方式出現。</span><span class="sxs-lookup"><span data-stu-id="8256c-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="8256c-165">並非每個中介軟體都必須以這種確切的順序進行，但有很多。</span><span class="sxs-lookup"><span data-stu-id="8256c-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="8256c-166">例如：</span><span class="sxs-lookup"><span data-stu-id="8256c-166">For example:</span></span>
  * <span data-ttu-id="8256c-167">`UseCors`、 `UseAuthentication` 和 `UseAuthorization` 必須依照顯示的順序進行。</span><span class="sxs-lookup"><span data-stu-id="8256c-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="8256c-168">`UseCors` 目前必須在 `UseResponseCaching` [此 bug](https://github.com/dotnet/aspnetcore/issues/23218)之前執行。</span><span class="sxs-lookup"><span data-stu-id="8256c-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="8256c-169">下列 `Startup.Configure` 方法會新增適用於一般應用程式案例的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="8256c-169">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="8256c-170">例外狀況/錯誤處理</span><span class="sxs-lookup"><span data-stu-id="8256c-170">Exception/error handling</span></span>
   * <span data-ttu-id="8256c-171">當應用程式在開發環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="8256c-171">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="8256c-172">開發人員例外狀況頁面中介軟體 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 會回報應用程式執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="8256c-172">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="8256c-173">資料庫錯誤頁面中介軟體報告資料庫執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="8256c-173">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="8256c-174">當應用程式在生產環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="8256c-174">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="8256c-175">例外狀況處理常式中介軟體 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 會攔截在下列中介軟體中擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8256c-175">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="8256c-176">HTTP 靜態傳輸安全性通訊協定 (HSTS) 中介軟體 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 會新增 `Strict-Transport-Security` 標頭。</span><span class="sxs-lookup"><span data-stu-id="8256c-176">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="8256c-177">HTTPS 重新導向中介軟體 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 會將 HTTP 要求重新導向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8256c-177">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="8256c-178">靜態檔案中介軟體 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 會傳回靜態檔案並縮短進一步的要求處理時間。</span><span class="sxs-lookup"><span data-stu-id="8256c-178">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="8256c-179">Cookie 原則中介軟體 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) 符合歐盟一般資料保護規定 (GDPR) 規定的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8256c-179">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="8256c-180">路由中介軟體 (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) 來路由傳送要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-180">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="8256c-181">驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 會嘗試在允許使用者存取安全資源之前先驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="8256c-181">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="8256c-182">授權中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) 會授權使用者存取安全資源。</span><span class="sxs-lookup"><span data-stu-id="8256c-182">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="8256c-183">工作階段中介軟體 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 會建立並維護工作階段狀態。</span><span class="sxs-lookup"><span data-stu-id="8256c-183">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="8256c-184">如果應用程式使用會話狀態，請在 Cookie 原則中介軟體和 MVC 中介軟體之前呼叫會話中介軟體。</span><span class="sxs-lookup"><span data-stu-id="8256c-184">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="8256c-185">端點路由中介軟體 (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 有 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) ，以將 Razor 頁面端點新增至要求管線。</span><span class="sxs-lookup"><span data-stu-id="8256c-185">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="8256c-186">在上面的範例程式碼中，每個中介軟體擴充方法都會透過 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空間在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公開。</span><span class="sxs-lookup"><span data-stu-id="8256c-186">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="8256c-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是第一個新增到管道的中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="8256c-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="8256c-188">因此，例外處理常式中介軟體會攔截後續呼叫中發生的所有例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8256c-188">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="8256c-189">靜態檔案中介軟體會提前在管線中呼叫，以便其無須逐一處理剩餘的元件，就能處理要求及執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="8256c-189">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="8256c-190">靜態檔案中介軟體 **不提供任何** 授權檢查。</span><span class="sxs-lookup"><span data-stu-id="8256c-190">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="8256c-191">靜態檔案中介軟體所提供的任何檔案（包括 *wwwroot*下的檔案）都可以公開使用。</span><span class="sxs-lookup"><span data-stu-id="8256c-191">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="8256c-192">如需保護靜態檔案的方法，請參閱 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="8256c-192">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="8256c-193">若靜態檔案中介軟體未處理要求，該要求會繼續傳遞給執行驗證的驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="8256c-193">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="8256c-194">驗證不會對未經驗證的要求執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="8256c-194">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="8256c-195">雖然驗證中介軟體會驗證要求，但只有在 MVC 選取特定 Razor 頁面或 mvc 控制器和動作之後，才會發生授權 (和拒絕) 。</span><span class="sxs-lookup"><span data-stu-id="8256c-195">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="8256c-196">下列範例示範靜態檔案中介軟體在回應壓縮中介軟體之前處理靜態檔案要求之前的靜態檔案順序。</span><span class="sxs-lookup"><span data-stu-id="8256c-196">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="8256c-197">靜態檔案並不會以此中介軟體順序壓縮。</span><span class="sxs-lookup"><span data-stu-id="8256c-197">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="8256c-198">您 Razor 可以壓縮頁面回應。</span><span class="sxs-lookup"><span data-stu-id="8256c-198">The Razor Pages responses can be compressed.</span></span>

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

<span data-ttu-id="8256c-199">針對 (Spa) 的單一頁面應用程式，SPA 中介軟體 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> 通常會最後出現在中介軟體管線中。</span><span class="sxs-lookup"><span data-stu-id="8256c-199">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="8256c-200">SPA 中介軟體最後有：</span><span class="sxs-lookup"><span data-stu-id="8256c-200">The SPA middleware comes last:</span></span>

* <span data-ttu-id="8256c-201">以允許所有其他中介軟體先回應相符的要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-201">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="8256c-202">若要允許具有用戶端路由的 Spa 執行伺服器應用程式無法辨識的所有路由。</span><span class="sxs-lookup"><span data-stu-id="8256c-202">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="8256c-203">如需 Spa 的詳細資訊，請參閱「 [回應](xref:spa/react) 」和「 [角度](xref:spa/angular) 」專案範本的指南。</span><span class="sxs-lookup"><span data-stu-id="8256c-203">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="8256c-204">轉送標頭中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="8256c-204">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="8256c-205">分支中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="8256c-205">Branch the middleware pipeline</span></span>

<span data-ttu-id="8256c-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 擴充方法則是用來分支管線的慣例。</span><span class="sxs-lookup"><span data-stu-id="8256c-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="8256c-207">`Map` 會依據指定要求路徑的相符項目將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="8256c-207">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="8256c-208">如果要求路徑以指定路徑為開頭，則會執行分支。</span><span class="sxs-lookup"><span data-stu-id="8256c-208">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="8256c-209">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="8256c-209">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="8256c-210">要求</span><span class="sxs-lookup"><span data-stu-id="8256c-210">Request</span></span>             | <span data-ttu-id="8256c-211">回應</span><span class="sxs-lookup"><span data-stu-id="8256c-211">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="8256c-212">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8256c-212">localhost:1234</span></span>      | <span data-ttu-id="8256c-213">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8256c-213">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8256c-214">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="8256c-214">localhost:1234/map1</span></span> | <span data-ttu-id="8256c-215">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="8256c-215">Map Test 1</span></span>                   |
| <span data-ttu-id="8256c-216">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="8256c-216">localhost:1234/map2</span></span> | <span data-ttu-id="8256c-217">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="8256c-217">Map Test 2</span></span>                   |
| <span data-ttu-id="8256c-218">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="8256c-218">localhost:1234/map3</span></span> | <span data-ttu-id="8256c-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8256c-219">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="8256c-220">使用 `Map` 時，會將相符的路徑線段從 `HttpRequest.Path` 移除，並附加至每個要求的 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="8256c-220">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="8256c-221">`Map` 支援巢狀項目，例如：</span><span class="sxs-lookup"><span data-stu-id="8256c-221">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="8256c-222">`Map` 也可以一次比對多個線段：</span><span class="sxs-lookup"><span data-stu-id="8256c-222">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="8256c-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 會依據指定述詞的結果將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="8256c-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="8256c-224">`Func<HttpContext, bool>` 類型的任何述詞皆可用來將要求對應至管線的新分支。</span><span class="sxs-lookup"><span data-stu-id="8256c-224">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="8256c-225">下列範例會使用述詞來偵測查詢字串變數 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="8256c-225">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="8256c-226">下表顯示使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應：</span><span class="sxs-lookup"><span data-stu-id="8256c-226">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="8256c-227">要求</span><span class="sxs-lookup"><span data-stu-id="8256c-227">Request</span></span>                       | <span data-ttu-id="8256c-228">回應</span><span class="sxs-lookup"><span data-stu-id="8256c-228">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="8256c-229">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8256c-229">localhost:1234</span></span>                | <span data-ttu-id="8256c-230">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8256c-230">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8256c-231">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="8256c-231">localhost:1234/?branch=master</span></span> | <span data-ttu-id="8256c-232">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="8256c-232">Branch used = master</span></span>         |

<span data-ttu-id="8256c-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> 也會根據指定述詞的結果來分支要求管線。</span><span class="sxs-lookup"><span data-stu-id="8256c-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="8256c-234">與不同的 `MapWhen` 是，此分支會重新加入至主要管線（如果它不是短路或包含終端中介軟體）：</span><span class="sxs-lookup"><span data-stu-id="8256c-234">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="8256c-235">在上述範例中，回應為「來自主要管線的 Hello」。</span><span class="sxs-lookup"><span data-stu-id="8256c-235">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="8256c-236">是針對所有要求所撰寫。</span><span class="sxs-lookup"><span data-stu-id="8256c-236">is written for all requests.</span></span> <span data-ttu-id="8256c-237">如果要求包含查詢字串變數，則 `branch` 會在重新加入主要管線之前記錄其值。</span><span class="sxs-lookup"><span data-stu-id="8256c-237">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="8256c-238">內建的中介軟體</span><span class="sxs-lookup"><span data-stu-id="8256c-238">Built-in middleware</span></span>

<span data-ttu-id="8256c-239">ASP.NET Core 隨附下列中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="8256c-239">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="8256c-240">「順序」\*\* 欄說明 中介軟體在要求處理管線中的位置，以及中介軟體可終止要求處理的情況。</span><span class="sxs-lookup"><span data-stu-id="8256c-240">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="8256c-241">當中介軟體將要求處理管線短路並防止接下來的下游中介軟體處理要求時，這就是所謂的「終端中介軟體」\*\*。</span><span class="sxs-lookup"><span data-stu-id="8256c-241">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="8256c-242">如需詳細資訊，請參閱[使用 IApplicationBuilder 建立中介軟體管線](#create-a-middleware-pipeline-with-iapplicationbuilder)。</span><span class="sxs-lookup"><span data-stu-id="8256c-242">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="8256c-243">中介軟體</span><span class="sxs-lookup"><span data-stu-id="8256c-243">Middleware</span></span> | <span data-ttu-id="8256c-244">描述</span><span class="sxs-lookup"><span data-stu-id="8256c-244">Description</span></span> | <span data-ttu-id="8256c-245">單</span><span class="sxs-lookup"><span data-stu-id="8256c-245">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="8256c-246">驗證</span><span class="sxs-lookup"><span data-stu-id="8256c-246">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="8256c-247">提供驗證支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-247">Provides authentication support.</span></span> | <span data-ttu-id="8256c-248">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-248">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="8256c-249">OAuth 回呼的終端機。</span><span class="sxs-lookup"><span data-stu-id="8256c-249">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="8256c-250">授權</span><span class="sxs-lookup"><span data-stu-id="8256c-250">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="8256c-251">提供授權支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-251">Provides authorization support.</span></span> | <span data-ttu-id="8256c-252">緊接在驗證中介軟體之後。</span><span class="sxs-lookup"><span data-stu-id="8256c-252">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="8256c-253">Cookie 政策</span><span class="sxs-lookup"><span data-stu-id="8256c-253">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="8256c-254">追蹤使用者同意以儲存個人資訊，並強制執列欄位的最小標準 cookie ，例如 `secure` 和 `SameSite` 。</span><span class="sxs-lookup"><span data-stu-id="8256c-254">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="8256c-255">在發出的中介軟體之前 cookie 。</span><span class="sxs-lookup"><span data-stu-id="8256c-255">Before middleware that issues cookies.</span></span> <span data-ttu-id="8256c-256">範例：驗證、工作階段、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="8256c-256">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="8256c-257">CORS</span><span class="sxs-lookup"><span data-stu-id="8256c-257">CORS</span></span>](xref:security/cors) | <span data-ttu-id="8256c-258">設定跨原始來源資源共用。</span><span class="sxs-lookup"><span data-stu-id="8256c-258">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="8256c-259">在使用 CORS 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-259">Before components that use CORS.</span></span> <span data-ttu-id="8256c-260">`UseCors` 目前必須在 `UseResponseCaching` [此 bug](https://github.com/dotnet/aspnetcore/issues/23218)之前執行。</span><span class="sxs-lookup"><span data-stu-id="8256c-260">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="8256c-261">診斷</span><span class="sxs-lookup"><span data-stu-id="8256c-261">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="8256c-262">有幾個不同的中介軟體，可提供開發人員例外狀況頁面、例外狀況處理、狀態字碼頁面，以及新應用程式的預設網頁。</span><span class="sxs-lookup"><span data-stu-id="8256c-262">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="8256c-263">在產生錯誤的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-263">Before components that generate errors.</span></span> <span data-ttu-id="8256c-264">例外狀況的終端機，或提供新應用程式的預設網頁。</span><span class="sxs-lookup"><span data-stu-id="8256c-264">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="8256c-265">轉寄的標頭</span><span class="sxs-lookup"><span data-stu-id="8256c-265">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="8256c-266">將設為 Proxy 的標頭轉送到目前要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-266">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="8256c-267">在使用更新方法的欄位之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-267">Before components that consume the updated fields.</span></span> <span data-ttu-id="8256c-268">範例：配置、主機，用戶端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="8256c-268">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="8256c-269">健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="8256c-269">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="8256c-270">檢查 ASP.NET Core 應用程式及其相依性的健康狀態，例如檢查資料庫可用性。</span><span class="sxs-lookup"><span data-stu-id="8256c-270">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="8256c-271">若某項要求與健康狀態檢查端點相符，則會是終端機。</span><span class="sxs-lookup"><span data-stu-id="8256c-271">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="8256c-272">標頭傳播</span><span class="sxs-lookup"><span data-stu-id="8256c-272">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="8256c-273">將來自傳入要求的 HTTP 標頭傳播至傳出 HTTP 用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-273">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="8256c-274">HTTP 方法覆寫</span><span class="sxs-lookup"><span data-stu-id="8256c-274">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="8256c-275">允許傳入的 POST 要求覆寫方法。</span><span class="sxs-lookup"><span data-stu-id="8256c-275">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="8256c-276">在使用更新方法的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-276">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="8256c-277">HTTPS 重新導向</span><span class="sxs-lookup"><span data-stu-id="8256c-277">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="8256c-278">將所有 HTTP 要求都重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8256c-278">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="8256c-279">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-279">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8256c-280">HTTP 嚴格的傳輸安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="8256c-280">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="8256c-281">增強安全性的中介軟體，可新增特殊的回應標頭。</span><span class="sxs-lookup"><span data-stu-id="8256c-281">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="8256c-282">在傳送回應前和修改要求的元件後。</span><span class="sxs-lookup"><span data-stu-id="8256c-282">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="8256c-283">範例：轉送的標頭、URL 重寫。</span><span class="sxs-lookup"><span data-stu-id="8256c-283">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="8256c-284">MVC</span><span class="sxs-lookup"><span data-stu-id="8256c-284">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="8256c-285">使用 MVC/Pages 處理要求 Razor 。</span><span class="sxs-lookup"><span data-stu-id="8256c-285">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="8256c-286">若要求符合路由則終止。</span><span class="sxs-lookup"><span data-stu-id="8256c-286">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="8256c-287">OWIN</span><span class="sxs-lookup"><span data-stu-id="8256c-287">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="8256c-288">以 OWIN 為基礎之應用程式、伺服器和中介軟體的 Interop。</span><span class="sxs-lookup"><span data-stu-id="8256c-288">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="8256c-289">若 OWIN 中介軟體完全處理要求則終止。</span><span class="sxs-lookup"><span data-stu-id="8256c-289">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="8256c-290">回應快取</span><span class="sxs-lookup"><span data-stu-id="8256c-290">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="8256c-291">提供快取回應的支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-291">Provides support for caching responses.</span></span> | <span data-ttu-id="8256c-292">在需要快取的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-292">Before components that require caching.</span></span> <span data-ttu-id="8256c-293">`UseCORS` 必須在前面 `UseResponseCaching` 。</span><span class="sxs-lookup"><span data-stu-id="8256c-293">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="8256c-294">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="8256c-294">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="8256c-295">提供壓縮回應的支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-295">Provides support for compressing responses.</span></span> | <span data-ttu-id="8256c-296">在需要壓縮的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-296">Before components that require compression.</span></span> |
| [<span data-ttu-id="8256c-297">要求當地語系化</span><span class="sxs-lookup"><span data-stu-id="8256c-297">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="8256c-298">提供當地語系化支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-298">Provides localization support.</span></span> | <span data-ttu-id="8256c-299">在偵測當地語系化的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-299">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="8256c-300">端點路由</span><span class="sxs-lookup"><span data-stu-id="8256c-300">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="8256c-301">定義並限制要求路由。</span><span class="sxs-lookup"><span data-stu-id="8256c-301">Defines and constrains request routes.</span></span> | <span data-ttu-id="8256c-302">比對路由的終端機。</span><span class="sxs-lookup"><span data-stu-id="8256c-302">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="8256c-303">SPA</span><span class="sxs-lookup"><span data-stu-id="8256c-303">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="8256c-304">藉由將單一頁面應用程式的預設頁面傳回 (SPA，在中介軟體鏈中處理來自此點的所有要求) </span><span class="sxs-lookup"><span data-stu-id="8256c-304">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="8256c-305">在鏈中延遲，讓其他中介軟體可提供靜態檔案、MVC 動作等等，都優先。</span><span class="sxs-lookup"><span data-stu-id="8256c-305">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="8256c-306">工作階段</span><span class="sxs-lookup"><span data-stu-id="8256c-306">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="8256c-307">提供管理使用者工作階段的支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-307">Provides support for managing user sessions.</span></span> | <span data-ttu-id="8256c-308">在需要工作階段的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-308">Before components that require Session.</span></span> | 
| [<span data-ttu-id="8256c-309">靜態檔案</span><span class="sxs-lookup"><span data-stu-id="8256c-309">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="8256c-310">支援靜態檔案的提供和目錄瀏覽。</span><span class="sxs-lookup"><span data-stu-id="8256c-310">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="8256c-311">若要求符合檔案則終止。</span><span class="sxs-lookup"><span data-stu-id="8256c-311">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="8256c-312">URL 重寫</span><span class="sxs-lookup"><span data-stu-id="8256c-312">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="8256c-313">提供重寫 URL 及重新導向要求的支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-313">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="8256c-314">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-314">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8256c-315">WebSocket</span><span class="sxs-lookup"><span data-stu-id="8256c-315">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="8256c-316">啟用 WebSockets 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8256c-316">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="8256c-317">在接受 WebSocket 要求的必要元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-317">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8256c-318">其他資源</span><span class="sxs-lookup"><span data-stu-id="8256c-318">Additional resources</span></span>

* <span data-ttu-id="8256c-319">[存留期和註冊選項](xref:fundamentals/dependency-injection#lifetime-and-registration-options) 包含具有 *範圍*、 *暫時性*和 *單一* 存留期服務之中介軟體的完整範例。</span><span class="sxs-lookup"><span data-stu-id="8256c-319">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped*, *transient*, and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8256c-320">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Steve Smith](https://ardalis.com/) 撰寫</span><span class="sxs-lookup"><span data-stu-id="8256c-320">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8256c-321">中介軟體為組成應用程式管線的軟體，用以處理要求與回應。</span><span class="sxs-lookup"><span data-stu-id="8256c-321">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="8256c-322">每個元件：</span><span class="sxs-lookup"><span data-stu-id="8256c-322">Each component:</span></span>

* <span data-ttu-id="8256c-323">可選擇是否要將要求傳送到管線中的下一個元件。</span><span class="sxs-lookup"><span data-stu-id="8256c-323">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="8256c-324">可以下一個元件的前後執行工作。</span><span class="sxs-lookup"><span data-stu-id="8256c-324">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="8256c-325">要求委派用於建置要求管線，</span><span class="sxs-lookup"><span data-stu-id="8256c-325">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="8256c-326">其會處理每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-326">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="8256c-327">要求委派的設定方式為使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="8256c-327">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="8256c-328">您可將個別要求委派指定為內嵌匿名方法 (在內嵌中介軟體中呼叫)，或於可重複使用的類別中加以定義。</span><span class="sxs-lookup"><span data-stu-id="8256c-328">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="8256c-329">這些可重複使用的類別及內嵌匿名方法皆為「中介軟體」**，也稱為「中介軟體元件」**。</span><span class="sxs-lookup"><span data-stu-id="8256c-329">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="8256c-330">要求管線中的每個中介軟體元件負責叫用管線中下一個元件，或對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="8256c-330">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="8256c-331">當中介軟體短路時，稱為「終端中介軟體」\*\*，因為它會防止接下來的中介軟體處理要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-331">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="8256c-332"><xref:migration/http-modules> 說明 ASP.NET Core 和 ASP.NET 4.x 之間的要求管線差異，並提供更多中介軟體範例。</span><span class="sxs-lookup"><span data-stu-id="8256c-332"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="8256c-333">使用 IApplicationBuilder 建立中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="8256c-333">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="8256c-334">ASP.NET Core 要求管線由要求委派序列組成，並會一個接著一個呼叫。</span><span class="sxs-lookup"><span data-stu-id="8256c-334">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="8256c-335">下圖說明此概念。</span><span class="sxs-lookup"><span data-stu-id="8256c-335">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="8256c-336">執行緒遵循黑色箭號執行。</span><span class="sxs-lookup"><span data-stu-id="8256c-336">The thread of execution follows the black arrows.</span></span>

![要求處理模式說明要求抵達，經過三個中介軟體所處理，然後應用程式送出回應。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="8256c-340">每一個委派皆能在下個委派的前後執行作業。</span><span class="sxs-lookup"><span data-stu-id="8256c-340">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="8256c-341">處理例外狀況的委派必須提前在管線中呼叫，以便其可與管線後續階段中所發生的例外狀況達成一致。</span><span class="sxs-lookup"><span data-stu-id="8256c-341">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="8256c-342">最簡潔的 ASP.NET Core 應用程式會設定單一要求委派來處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-342">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="8256c-343">此情況不包含實際要求管線。</span><span class="sxs-lookup"><span data-stu-id="8256c-343">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="8256c-344">反之，系統會呼叫單一匿名函式來回應每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-344">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="8256c-345">第一個 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委派會終止管線。</span><span class="sxs-lookup"><span data-stu-id="8256c-345">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="8256c-346">將多個要求委派鏈結在一起的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>。</span><span class="sxs-lookup"><span data-stu-id="8256c-346">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="8256c-347">`next` 參數代表管線中的下個委派。</span><span class="sxs-lookup"><span data-stu-id="8256c-347">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="8256c-348">您可以「不」\*\* 呼叫「下一個」\*\* 參數來對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="8256c-348">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="8256c-349">您通常可以在下個委派的前後執行動作，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="8256c-349">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="8256c-350">當委派不將要求傳遞到下一個委派時，這就是所謂*讓要求管線短路*。</span><span class="sxs-lookup"><span data-stu-id="8256c-350">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="8256c-351">因為最少運算可避免不必要的工作，所以經常使用。</span><span class="sxs-lookup"><span data-stu-id="8256c-351">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="8256c-352">例如，[靜態檔案中介軟體](xref:fundamentals/static-files)可以做為*終端中介軟體*使用，方式是處理靜態檔案的要求，並對剩餘的管線執行短路。</span><span class="sxs-lookup"><span data-stu-id="8256c-352">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="8256c-353">在中介軟體之前新增到管線且終結進一步處理的中介軟體在其 `next.Invoke` 陳述式之後仍然處理程式碼。</span><span class="sxs-lookup"><span data-stu-id="8256c-353">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="8256c-354">不過，查看下列有關嘗試寫入已傳送之回應的警告。</span><span class="sxs-lookup"><span data-stu-id="8256c-354">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="8256c-355">請不要在回應已傳送給用戶端之後呼叫 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="8256c-355">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="8256c-356">回應啟動後，變更為 <xref:Microsoft.AspNetCore.Http.HttpResponse> 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8256c-356">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="8256c-357">例如，變更設定標頭、狀態碼等都會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8256c-357">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="8256c-358">若在呼叫 `next` 後寫入回應本文：</span><span class="sxs-lookup"><span data-stu-id="8256c-358">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="8256c-359">可能導致違反通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8256c-359">May cause a protocol violation.</span></span> <span data-ttu-id="8256c-360">例如，寫入超過指定 `Content-Length` 的內容。</span><span class="sxs-lookup"><span data-stu-id="8256c-360">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="8256c-361">可能損毀本文格式。</span><span class="sxs-lookup"><span data-stu-id="8256c-361">May corrupt the body format.</span></span> <span data-ttu-id="8256c-362">例如，將 HTML 頁尾寫入 CSS 檔。</span><span class="sxs-lookup"><span data-stu-id="8256c-362">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="8256c-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是實用的提示，可表示是否已傳送標頭 (或) 是否已寫入本文。</span><span class="sxs-lookup"><span data-stu-id="8256c-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="8256c-364">中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="8256c-364">Middleware order</span></span>

<span data-ttu-id="8256c-365">`Startup.Configure` 方法內中介軟體元件的新增順序可定義在要求時叫用中介軟體元件的順序及回應的反向順序。</span><span class="sxs-lookup"><span data-stu-id="8256c-365">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="8256c-366">對安全性、效能和功能而言，順序是非常 **重要** 的。</span><span class="sxs-lookup"><span data-stu-id="8256c-366">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="8256c-367">下列方法會依 `Startup.Configure` 建議的順序新增安全性相關的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="8256c-367">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="8256c-368">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="8256c-368">In the preceding code:</span></span>

* <span data-ttu-id="8256c-369">以 [個別使用者帳戶](xref:security/authentication/identity) 建立新的 web 應用程式時，未新增的中介軟體會以批註方式出現。</span><span class="sxs-lookup"><span data-stu-id="8256c-369">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="8256c-370">並非每個中介軟體都必須以這種確切的順序進行，但有很多。</span><span class="sxs-lookup"><span data-stu-id="8256c-370">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="8256c-371">例如， `UseCors` 和 `UseAuthentication` 必須依照顯示的順序進行。</span><span class="sxs-lookup"><span data-stu-id="8256c-371">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="8256c-372">下列 `Startup.Configure` 方法會新增適用於一般應用程式案例的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="8256c-372">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="8256c-373">例外狀況/錯誤處理</span><span class="sxs-lookup"><span data-stu-id="8256c-373">Exception/error handling</span></span>
   * <span data-ttu-id="8256c-374">當應用程式在開發環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="8256c-374">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="8256c-375">開發人員例外狀況頁面中介軟體 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 會回報應用程式執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="8256c-375">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="8256c-376">資料錯誤頁面中介軟體 (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) 會回報資料庫執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="8256c-376">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="8256c-377">當應用程式在生產環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="8256c-377">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="8256c-378">例外狀況處理常式中介軟體 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 會攔截在下列中介軟體中擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8256c-378">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="8256c-379">HTTP 靜態傳輸安全性通訊協定 (HSTS) 中介軟體 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 會新增 `Strict-Transport-Security` 標頭。</span><span class="sxs-lookup"><span data-stu-id="8256c-379">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="8256c-380">HTTPS 重新導向中介軟體 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 會將 HTTP 要求重新導向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8256c-380">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="8256c-381">靜態檔案中介軟體 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 會傳回靜態檔案並縮短進一步的要求處理時間。</span><span class="sxs-lookup"><span data-stu-id="8256c-381">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="8256c-382">Cookie 原則中介軟體 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) 符合歐盟一般資料保護規定 (GDPR) 規定的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8256c-382">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="8256c-383">驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 會嘗試在允許使用者存取安全資源之前先驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="8256c-383">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="8256c-384">工作階段中介軟體 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 會建立並維護工作階段狀態。</span><span class="sxs-lookup"><span data-stu-id="8256c-384">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="8256c-385">如果應用程式使用會話狀態，請在 Cookie 原則中介軟體和 MVC 中介軟體之前呼叫會話中介軟體。</span><span class="sxs-lookup"><span data-stu-id="8256c-385">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="8256c-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) 以將 MVC 新增到要求管線。</span><span class="sxs-lookup"><span data-stu-id="8256c-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="8256c-387">在上面的範例程式碼中，每個中介軟體擴充方法都會透過 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空間在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公開。</span><span class="sxs-lookup"><span data-stu-id="8256c-387">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="8256c-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是第一個新增到管道的中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="8256c-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="8256c-389">因此，例外處理常式中介軟體會攔截後續呼叫中發生的所有例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8256c-389">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="8256c-390">靜態檔案中介軟體會提前在管線中呼叫，以便其無須逐一處理剩餘的元件，就能處理要求及執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="8256c-390">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="8256c-391">靜態檔案中介軟體 **不提供任何** 授權檢查。</span><span class="sxs-lookup"><span data-stu-id="8256c-391">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="8256c-392">靜態檔案中介軟體所提供的任何檔案（包括 *wwwroot*下的檔案）都可以公開使用。</span><span class="sxs-lookup"><span data-stu-id="8256c-392">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="8256c-393">如需保護靜態檔案的方法，請參閱 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="8256c-393">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="8256c-394">若靜態檔案中介軟體未處理要求，該要求會繼續傳遞給執行驗證的驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="8256c-394">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="8256c-395">驗證不會對未經驗證的要求執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="8256c-395">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="8256c-396">雖然驗證中介軟體會驗證要求，但只有在 MVC 選取特定 Razor 頁面或 mvc 控制器和動作之後，才會發生授權 (和拒絕) 。</span><span class="sxs-lookup"><span data-stu-id="8256c-396">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="8256c-397">下列範例示範靜態檔案中介軟體在回應壓縮中介軟體之前處理靜態檔案要求之前的靜態檔案順序。</span><span class="sxs-lookup"><span data-stu-id="8256c-397">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="8256c-398">靜態檔案並不會以此中介軟體順序壓縮。</span><span class="sxs-lookup"><span data-stu-id="8256c-398">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="8256c-399">來自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> 的 MVC 回應可壓縮。</span><span class="sxs-lookup"><span data-stu-id="8256c-399">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="8256c-400">Use、Run 與 Map</span><span class="sxs-lookup"><span data-stu-id="8256c-400">Use, Run, and Map</span></span>

<span data-ttu-id="8256c-401">設定 HTTP 管線的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 及 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>。</span><span class="sxs-lookup"><span data-stu-id="8256c-401">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="8256c-402">`Use` 方法可對管線執行最少運算 (亦即，如果其不呼叫 `next` 要求委派的情況下)。</span><span class="sxs-lookup"><span data-stu-id="8256c-402">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="8256c-403">`Run` 是一種慣例，且有些中介軟體元件可能將執行於管線尾端的 `Run[Middleware]` 方法公開。</span><span class="sxs-lookup"><span data-stu-id="8256c-403">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="8256c-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 擴充方法則是用來分支管線的慣例。</span><span class="sxs-lookup"><span data-stu-id="8256c-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="8256c-405">`Map` 會依據指定要求路徑的相符項目將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="8256c-405">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="8256c-406">如果要求路徑以指定路徑為開頭，則會執行分支。</span><span class="sxs-lookup"><span data-stu-id="8256c-406">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="8256c-407">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="8256c-407">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="8256c-408">要求</span><span class="sxs-lookup"><span data-stu-id="8256c-408">Request</span></span>             | <span data-ttu-id="8256c-409">回應</span><span class="sxs-lookup"><span data-stu-id="8256c-409">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="8256c-410">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8256c-410">localhost:1234</span></span>      | <span data-ttu-id="8256c-411">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8256c-411">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8256c-412">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="8256c-412">localhost:1234/map1</span></span> | <span data-ttu-id="8256c-413">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="8256c-413">Map Test 1</span></span>                   |
| <span data-ttu-id="8256c-414">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="8256c-414">localhost:1234/map2</span></span> | <span data-ttu-id="8256c-415">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="8256c-415">Map Test 2</span></span>                   |
| <span data-ttu-id="8256c-416">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="8256c-416">localhost:1234/map3</span></span> | <span data-ttu-id="8256c-417">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8256c-417">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="8256c-418">使用 `Map` 時，會將相符的路徑線段從 `HttpRequest.Path` 移除，並附加至每個要求的 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="8256c-418">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="8256c-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 會依據指定述詞的結果將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="8256c-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="8256c-420">`Func<HttpContext, bool>` 類型的任何述詞皆可用來將要求對應至管線的新分支。</span><span class="sxs-lookup"><span data-stu-id="8256c-420">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="8256c-421">下列範例會使用述詞來偵測查詢字串變數 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="8256c-421">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="8256c-422">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="8256c-422">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="8256c-423">要求</span><span class="sxs-lookup"><span data-stu-id="8256c-423">Request</span></span>                       | <span data-ttu-id="8256c-424">回應</span><span class="sxs-lookup"><span data-stu-id="8256c-424">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="8256c-425">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8256c-425">localhost:1234</span></span>                | <span data-ttu-id="8256c-426">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8256c-426">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8256c-427">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="8256c-427">localhost:1234/?branch=master</span></span> | <span data-ttu-id="8256c-428">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="8256c-428">Branch used = master</span></span>         |

<span data-ttu-id="8256c-429">`Map` 支援巢狀項目，例如：</span><span class="sxs-lookup"><span data-stu-id="8256c-429">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="8256c-430">`Map` 也可以一次比對多個線段：</span><span class="sxs-lookup"><span data-stu-id="8256c-430">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="8256c-431">內建的中介軟體</span><span class="sxs-lookup"><span data-stu-id="8256c-431">Built-in middleware</span></span>

<span data-ttu-id="8256c-432">ASP.NET Core 隨附下列中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="8256c-432">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="8256c-433">「順序」\*\* 欄說明 中介軟體在要求處理管線中的位置，以及中介軟體可終止要求處理的情況。</span><span class="sxs-lookup"><span data-stu-id="8256c-433">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="8256c-434">當中介軟體將要求處理管線短路並防止接下來的下游中介軟體處理要求時，這就是所謂的「終端中介軟體」\*\*。</span><span class="sxs-lookup"><span data-stu-id="8256c-434">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="8256c-435">如需詳細資訊，請參閱[使用 IApplicationBuilder 建立中介軟體管線](#create-a-middleware-pipeline-with-iapplicationbuilder)。</span><span class="sxs-lookup"><span data-stu-id="8256c-435">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="8256c-436">中介軟體</span><span class="sxs-lookup"><span data-stu-id="8256c-436">Middleware</span></span> | <span data-ttu-id="8256c-437">描述</span><span class="sxs-lookup"><span data-stu-id="8256c-437">Description</span></span> | <span data-ttu-id="8256c-438">單</span><span class="sxs-lookup"><span data-stu-id="8256c-438">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="8256c-439">驗證</span><span class="sxs-lookup"><span data-stu-id="8256c-439">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="8256c-440">提供驗證支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-440">Provides authentication support.</span></span> | <span data-ttu-id="8256c-441">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-441">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="8256c-442">OAuth 回呼的終端機。</span><span class="sxs-lookup"><span data-stu-id="8256c-442">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="8256c-443">Cookie 政策</span><span class="sxs-lookup"><span data-stu-id="8256c-443">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="8256c-444">追蹤使用者同意以儲存個人資訊，並強制執列欄位的最小標準 cookie ，例如 `secure` 和 `SameSite` 。</span><span class="sxs-lookup"><span data-stu-id="8256c-444">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="8256c-445">在發出的中介軟體之前 cookie 。</span><span class="sxs-lookup"><span data-stu-id="8256c-445">Before middleware that issues cookies.</span></span> <span data-ttu-id="8256c-446">範例：驗證、工作階段、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="8256c-446">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="8256c-447">CORS</span><span class="sxs-lookup"><span data-stu-id="8256c-447">CORS</span></span>](xref:security/cors) | <span data-ttu-id="8256c-448">設定跨原始來源資源共用。</span><span class="sxs-lookup"><span data-stu-id="8256c-448">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="8256c-449">在使用 CORS 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-449">Before components that use CORS.</span></span> |
| [<span data-ttu-id="8256c-450">診斷</span><span class="sxs-lookup"><span data-stu-id="8256c-450">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="8256c-451">有幾個不同的中介軟體，可提供開發人員例外狀況頁面、例外狀況處理、狀態字碼頁面，以及新應用程式的預設網頁。</span><span class="sxs-lookup"><span data-stu-id="8256c-451">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="8256c-452">在產生錯誤的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-452">Before components that generate errors.</span></span> <span data-ttu-id="8256c-453">例外狀況的終端機，或提供新應用程式的預設網頁。</span><span class="sxs-lookup"><span data-stu-id="8256c-453">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="8256c-454">轉寄的標頭</span><span class="sxs-lookup"><span data-stu-id="8256c-454">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="8256c-455">將設為 Proxy 的標頭轉送到目前要求。</span><span class="sxs-lookup"><span data-stu-id="8256c-455">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="8256c-456">在使用更新方法的欄位之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-456">Before components that consume the updated fields.</span></span> <span data-ttu-id="8256c-457">範例：配置、主機，用戶端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="8256c-457">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="8256c-458">健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="8256c-458">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="8256c-459">檢查 ASP.NET Core 應用程式及其相依性的健康狀態，例如檢查資料庫可用性。</span><span class="sxs-lookup"><span data-stu-id="8256c-459">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="8256c-460">若某項要求與健康狀態檢查端點相符，則會是終端機。</span><span class="sxs-lookup"><span data-stu-id="8256c-460">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="8256c-461">HTTP 方法覆寫</span><span class="sxs-lookup"><span data-stu-id="8256c-461">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="8256c-462">允許傳入的 POST 要求覆寫方法。</span><span class="sxs-lookup"><span data-stu-id="8256c-462">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="8256c-463">在使用更新方法的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-463">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="8256c-464">HTTPS 重新導向</span><span class="sxs-lookup"><span data-stu-id="8256c-464">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="8256c-465">將所有 HTTP 要求都重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8256c-465">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="8256c-466">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-466">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8256c-467">HTTP 嚴格的傳輸安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="8256c-467">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="8256c-468">增強安全性的中介軟體，可新增特殊的回應標頭。</span><span class="sxs-lookup"><span data-stu-id="8256c-468">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="8256c-469">在傳送回應前和修改要求的元件後。</span><span class="sxs-lookup"><span data-stu-id="8256c-469">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="8256c-470">範例：轉送的標頭、URL 重寫。</span><span class="sxs-lookup"><span data-stu-id="8256c-470">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="8256c-471">MVC</span><span class="sxs-lookup"><span data-stu-id="8256c-471">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="8256c-472">使用 MVC/Pages 處理要求 Razor 。</span><span class="sxs-lookup"><span data-stu-id="8256c-472">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="8256c-473">若要求符合路由則終止。</span><span class="sxs-lookup"><span data-stu-id="8256c-473">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="8256c-474">OWIN</span><span class="sxs-lookup"><span data-stu-id="8256c-474">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="8256c-475">以 OWIN 為基礎之應用程式、伺服器和中介軟體的 Interop。</span><span class="sxs-lookup"><span data-stu-id="8256c-475">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="8256c-476">若 OWIN 中介軟體完全處理要求則終止。</span><span class="sxs-lookup"><span data-stu-id="8256c-476">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="8256c-477">回應快取</span><span class="sxs-lookup"><span data-stu-id="8256c-477">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="8256c-478">提供快取回應的支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-478">Provides support for caching responses.</span></span> | <span data-ttu-id="8256c-479">在需要快取的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-479">Before components that require caching.</span></span> |
| [<span data-ttu-id="8256c-480">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="8256c-480">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="8256c-481">提供壓縮回應的支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-481">Provides support for compressing responses.</span></span> | <span data-ttu-id="8256c-482">在需要壓縮的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-482">Before components that require compression.</span></span> |
| [<span data-ttu-id="8256c-483">要求當地語系化</span><span class="sxs-lookup"><span data-stu-id="8256c-483">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="8256c-484">提供當地語系化支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-484">Provides localization support.</span></span> | <span data-ttu-id="8256c-485">在偵測當地語系化的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-485">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="8256c-486">端點路由</span><span class="sxs-lookup"><span data-stu-id="8256c-486">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="8256c-487">定義並限制要求路由。</span><span class="sxs-lookup"><span data-stu-id="8256c-487">Defines and constrains request routes.</span></span> | <span data-ttu-id="8256c-488">比對路由的終端機。</span><span class="sxs-lookup"><span data-stu-id="8256c-488">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="8256c-489">工作階段</span><span class="sxs-lookup"><span data-stu-id="8256c-489">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="8256c-490">提供管理使用者工作階段的支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-490">Provides support for managing user sessions.</span></span> | <span data-ttu-id="8256c-491">在需要工作階段的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-491">Before components that require Session.</span></span> |
| [<span data-ttu-id="8256c-492">靜態檔案</span><span class="sxs-lookup"><span data-stu-id="8256c-492">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="8256c-493">支援靜態檔案的提供和目錄瀏覽。</span><span class="sxs-lookup"><span data-stu-id="8256c-493">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="8256c-494">若要求符合檔案則終止。</span><span class="sxs-lookup"><span data-stu-id="8256c-494">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="8256c-495">URL 重寫</span><span class="sxs-lookup"><span data-stu-id="8256c-495">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="8256c-496">提供重寫 URL 及重新導向要求的支援。</span><span class="sxs-lookup"><span data-stu-id="8256c-496">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="8256c-497">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-497">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8256c-498">WebSocket</span><span class="sxs-lookup"><span data-stu-id="8256c-498">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="8256c-499">啟用 WebSockets 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8256c-499">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="8256c-500">在接受 WebSocket 要求的必要元件之前。</span><span class="sxs-lookup"><span data-stu-id="8256c-500">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8256c-501">其他資源</span><span class="sxs-lookup"><span data-stu-id="8256c-501">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
