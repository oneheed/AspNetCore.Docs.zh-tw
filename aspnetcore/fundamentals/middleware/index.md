---
title: ASP.NET Core 中介軟體
author: rick-anderson
description: 了解 ASP.NET Core 中介軟體和要求管線。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: 15d011e88ab291173668a0b6dc5f46e97fdfeff0
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605707"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="5ae54-103">ASP.NET Core 中介軟體</span><span class="sxs-lookup"><span data-stu-id="5ae54-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5ae54-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Steve Smith](https://ardalis.com/) 撰寫</span><span class="sxs-lookup"><span data-stu-id="5ae54-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5ae54-105">中介軟體為組成應用程式管線的軟體，用以處理要求與回應。</span><span class="sxs-lookup"><span data-stu-id="5ae54-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="5ae54-106">每個元件：</span><span class="sxs-lookup"><span data-stu-id="5ae54-106">Each component:</span></span>

* <span data-ttu-id="5ae54-107">可選擇是否要將要求傳送到管線中的下一個元件。</span><span class="sxs-lookup"><span data-stu-id="5ae54-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="5ae54-108">可以下一個元件的前後執行工作。</span><span class="sxs-lookup"><span data-stu-id="5ae54-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="5ae54-109">要求委派用於建置要求管線，</span><span class="sxs-lookup"><span data-stu-id="5ae54-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="5ae54-110">其會處理每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="5ae54-111">要求委派的設定方式為使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="5ae54-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="5ae54-112">您可將個別要求委派指定為內嵌匿名方法 (在內嵌中介軟體中呼叫)，或於可重複使用的類別中加以定義。</span><span class="sxs-lookup"><span data-stu-id="5ae54-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="5ae54-113">這些可重複使用的類別及內嵌匿名方法皆為「中介軟體」，也稱為「中介軟體元件」。</span><span class="sxs-lookup"><span data-stu-id="5ae54-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="5ae54-114">要求管線中的每個中介軟體元件負責叫用管線中下一個元件，或對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="5ae54-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="5ae54-115">當中介軟體短路時，稱為「終端中介軟體」，因為它會防止接下來的中介軟體處理要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="5ae54-116"><xref:migration/http-modules> 說明 ASP.NET Core 和 ASP.NET 4.x 之間的要求管線差異，並提供更多中介軟體範例。</span><span class="sxs-lookup"><span data-stu-id="5ae54-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="5ae54-117">使用 IApplicationBuilder 建立中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="5ae54-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="5ae54-118">ASP.NET Core 要求管線由要求委派序列組成，並會一個接著一個呼叫。</span><span class="sxs-lookup"><span data-stu-id="5ae54-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="5ae54-119">下圖說明此概念。</span><span class="sxs-lookup"><span data-stu-id="5ae54-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="5ae54-120">執行緒遵循黑色箭號執行。</span><span class="sxs-lookup"><span data-stu-id="5ae54-120">The thread of execution follows the black arrows.</span></span>

![要求處理模式說明要求抵達，經過三個中介軟體所處理，然後應用程式送出回應。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="5ae54-124">每一個委派皆能在下個委派的前後執行作業。</span><span class="sxs-lookup"><span data-stu-id="5ae54-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="5ae54-125">處理例外狀況的委派必須提前在管線中呼叫，以便其可與管線後續階段中所發生的例外狀況達成一致。</span><span class="sxs-lookup"><span data-stu-id="5ae54-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="5ae54-126">最簡潔的 ASP.NET Core 應用程式會設定單一要求委派來處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="5ae54-127">此情況不包含實際要求管線。</span><span class="sxs-lookup"><span data-stu-id="5ae54-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="5ae54-128">反之，系統會呼叫單一匿名函式來回應每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="5ae54-129">將多個要求委派鏈結在一起的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>。</span><span class="sxs-lookup"><span data-stu-id="5ae54-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="5ae54-130">`next` 參數代表管線中的下個委派。</span><span class="sxs-lookup"><span data-stu-id="5ae54-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="5ae54-131">您可以「不」呼叫「下一個」參數來對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="5ae54-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="5ae54-132">您通常可以在下個委派的前後執行動作，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="5ae54-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="5ae54-133">當委派不將要求傳遞到下一個委派時，這就是所謂 *讓要求管線短路*。</span><span class="sxs-lookup"><span data-stu-id="5ae54-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="5ae54-134">因為最少運算可避免不必要的工作，所以經常使用。</span><span class="sxs-lookup"><span data-stu-id="5ae54-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="5ae54-135">例如，[靜態檔案中介軟體](xref:fundamentals/static-files)可以做為 *終端中介軟體* 使用，方式是處理靜態檔案的要求，並對剩餘的管線執行短路。</span><span class="sxs-lookup"><span data-stu-id="5ae54-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="5ae54-136">在中介軟體之前新增到管線且終結進一步處理的中介軟體在其 `next.Invoke` 陳述式之後仍然處理程式碼。</span><span class="sxs-lookup"><span data-stu-id="5ae54-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="5ae54-137">不過，查看下列有關嘗試寫入已傳送之回應的警告。</span><span class="sxs-lookup"><span data-stu-id="5ae54-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="5ae54-138">請不要在回應已傳送給用戶端之後呼叫 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="5ae54-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="5ae54-139">回應啟動後，變更為 <xref:Microsoft.AspNetCore.Http.HttpResponse> 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="5ae54-140">例如， [設定標頭和狀態碼會擲回例外狀況](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started)。</span><span class="sxs-lookup"><span data-stu-id="5ae54-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="5ae54-141">若在呼叫 `next` 後寫入回應本文：</span><span class="sxs-lookup"><span data-stu-id="5ae54-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="5ae54-142">可能導致違反通訊協定。</span><span class="sxs-lookup"><span data-stu-id="5ae54-142">May cause a protocol violation.</span></span> <span data-ttu-id="5ae54-143">例如，寫入超過指定 `Content-Length` 的內容。</span><span class="sxs-lookup"><span data-stu-id="5ae54-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="5ae54-144">可能損毀本文格式。</span><span class="sxs-lookup"><span data-stu-id="5ae54-144">May corrupt the body format.</span></span> <span data-ttu-id="5ae54-145">例如，將 HTML 頁尾寫入 CSS 檔。</span><span class="sxs-lookup"><span data-stu-id="5ae54-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="5ae54-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是實用的提示，可表示是否已傳送標頭 (或) 是否已寫入本文。</span><span class="sxs-lookup"><span data-stu-id="5ae54-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="5ae54-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委派不會收到 `next` 參數。</span><span class="sxs-lookup"><span data-stu-id="5ae54-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="5ae54-148">第一個 `Run` 委派一律是 terminal 並終止管線。</span><span class="sxs-lookup"><span data-stu-id="5ae54-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="5ae54-149">`Run` 是慣例。</span><span class="sxs-lookup"><span data-stu-id="5ae54-149">`Run` is a convention.</span></span> <span data-ttu-id="5ae54-150">某些中介軟體元件可能會公開在 `Run[Middleware]` 管線結尾執行的方法：</span><span class="sxs-lookup"><span data-stu-id="5ae54-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="5ae54-151">在上述範例中， `Run` 委派會寫入 `"Hello from 2nd delegate."` 至回應，然後終止管線。</span><span class="sxs-lookup"><span data-stu-id="5ae54-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="5ae54-152">如果 `Use` `Run` 在委派之後加入另一個或委派 `Run` ，則不會呼叫它。</span><span class="sxs-lookup"><span data-stu-id="5ae54-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="5ae54-153">中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="5ae54-153">Middleware order</span></span>

<span data-ttu-id="5ae54-154">下圖顯示 ASP.NET Core MVC 和 Pages 應用程式的完整要求處理管線 Razor 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="5ae54-155">您可以在一般的應用程式中查看如何排序現有的中介軟體，以及新增自訂中介軟體的位置。</span><span class="sxs-lookup"><span data-stu-id="5ae54-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="5ae54-156">您可以完整控制如何重新排列現有的中介軟體，或在您的案例中插入新的自訂中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5ae54-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET 核心中介軟體管線](index/_static/middleware-pipeline.svg)

<span data-ttu-id="5ae54-158">上圖中的 **端點** 中介軟體會針對對應的應用程式類型 &mdash; MVC 或頁面執行篩選準則管線 Razor 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core 篩選管線](index/_static/mvc-endpoint.svg)

<span data-ttu-id="5ae54-160">`Startup.Configure` 方法內中介軟體元件的新增順序可定義在要求時叫用中介軟體元件的順序及回應的反向順序。</span><span class="sxs-lookup"><span data-stu-id="5ae54-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="5ae54-161">對安全性、效能和功能而言，順序是非常 **重要** 的。</span><span class="sxs-lookup"><span data-stu-id="5ae54-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="5ae54-162">下列 `Startup.Configure` 方法會以一般建議的順序新增安全性相關的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="5ae54-162">The following `Startup.Configure` method adds security-related middleware components in the typical recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="5ae54-163">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="5ae54-163">In the preceding code:</span></span>

* <span data-ttu-id="5ae54-164">以 [個別使用者帳戶](xref:security/authentication/identity) 建立新的 web 應用程式時，未新增的中介軟體會以批註方式出現。</span><span class="sxs-lookup"><span data-stu-id="5ae54-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="5ae54-165">並非每個中介軟體都必須以這種確切的順序進行，但有很多。</span><span class="sxs-lookup"><span data-stu-id="5ae54-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="5ae54-166">例如：</span><span class="sxs-lookup"><span data-stu-id="5ae54-166">For example:</span></span>
  * <span data-ttu-id="5ae54-167">`UseCors`、 `UseAuthentication` 和 `UseAuthorization` 必須依照顯示的順序進行。</span><span class="sxs-lookup"><span data-stu-id="5ae54-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="5ae54-168">`UseCors` 目前必須在 `UseResponseCaching` [此 bug](https://github.com/dotnet/aspnetcore/issues/23218)之前執行。</span><span class="sxs-lookup"><span data-stu-id="5ae54-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="5ae54-169">在某些案例中，中介軟體會有不同的順序。</span><span class="sxs-lookup"><span data-stu-id="5ae54-169">In some scenarios, middleware will have different ordering.</span></span> <span data-ttu-id="5ae54-170">例如，快取和壓縮順序是特定案例，而且有多個有效的排序。</span><span class="sxs-lookup"><span data-stu-id="5ae54-170">For example, caching and compression ordering is scenario specific, and there's multiple valid orderings.</span></span> <span data-ttu-id="5ae54-171">例如：</span><span class="sxs-lookup"><span data-stu-id="5ae54-171">For example:</span></span>

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

<span data-ttu-id="5ae54-172">使用上述程式碼時，CPU 可能會藉由快取壓縮的回應來儲存，但您可能最終會使用不同的壓縮演算法（例如 gzip 或 brotli）來快取多個資源的標記法。</span><span class="sxs-lookup"><span data-stu-id="5ae54-172">With the preceding code, CPU could be saved by caching the compressed response, but you might end up caching multiple representations of a resource using different compression algorithms such as gzip or brotli.</span></span>

<span data-ttu-id="5ae54-173">下列順序會結合靜態檔案，以允許快取壓縮的靜態檔案：</span><span class="sxs-lookup"><span data-stu-id="5ae54-173">The following ordering combines static files to allow caching compressed static files:</span></span>

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
app.UseStaticFiles();
```

<span data-ttu-id="5ae54-174">下列 `Startup.Configure` 方法會新增適用於一般應用程式案例的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="5ae54-174">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="5ae54-175">例外狀況/錯誤處理</span><span class="sxs-lookup"><span data-stu-id="5ae54-175">Exception/error handling</span></span>
   * <span data-ttu-id="5ae54-176">當應用程式在開發環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="5ae54-176">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="5ae54-177">開發人員例外狀況頁面中介軟體 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 會回報應用程式執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="5ae54-177">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="5ae54-178">資料庫錯誤頁面中介軟體報告資料庫執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="5ae54-178">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="5ae54-179">當應用程式在生產環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="5ae54-179">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="5ae54-180">例外狀況處理常式中介軟體 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 會攔截在下列中介軟體中擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-180">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="5ae54-181">HTTP 靜態傳輸安全性通訊協定 (HSTS) 中介軟體 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 會新增 `Strict-Transport-Security` 標頭。</span><span class="sxs-lookup"><span data-stu-id="5ae54-181">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="5ae54-182">HTTPS 重新導向中介軟體 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 會將 HTTP 要求重新導向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="5ae54-182">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="5ae54-183">靜態檔案中介軟體 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 會傳回靜態檔案並縮短進一步的要求處理時間。</span><span class="sxs-lookup"><span data-stu-id="5ae54-183">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="5ae54-184">Cookie 原則中介軟體 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) 符合歐盟一般資料保護規定的應用程式 (GDPR) 規定。</span><span class="sxs-lookup"><span data-stu-id="5ae54-184">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="5ae54-185">路由中介軟體 (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) 來路由傳送要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-185">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="5ae54-186">驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 會嘗試在允許使用者存取安全資源之前先驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="5ae54-186">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="5ae54-187">授權中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) 會授權使用者存取安全資源。</span><span class="sxs-lookup"><span data-stu-id="5ae54-187">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="5ae54-188">工作階段中介軟體 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 會建立並維護工作階段狀態。</span><span class="sxs-lookup"><span data-stu-id="5ae54-188">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="5ae54-189">如果應用程式使用會話狀態，請在 Cookie 原則中介軟體和 MVC 中介軟體之前呼叫會話中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5ae54-189">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="5ae54-190">端點路由中介軟體 (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 有 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) ，以將 Razor 頁面端點新增至要求管線。</span><span class="sxs-lookup"><span data-stu-id="5ae54-190">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="5ae54-191">在上面的範例程式碼中，每個中介軟體擴充方法都會透過 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空間在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公開。</span><span class="sxs-lookup"><span data-stu-id="5ae54-191">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="5ae54-192"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是第一個新增到管道的中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="5ae54-192"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="5ae54-193">因此，例外處理常式中介軟體會攔截後續呼叫中發生的所有例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-193">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="5ae54-194">靜態檔案中介軟體會提前在管線中呼叫，以便其無須逐一處理剩餘的元件，就能處理要求及執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="5ae54-194">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="5ae54-195">靜態檔案中介軟體 **不提供任何** 授權檢查。</span><span class="sxs-lookup"><span data-stu-id="5ae54-195">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="5ae54-196">靜態檔案中介軟體所提供的任何檔案（包括 *wwwroot* 下的檔案）都可以公開使用。</span><span class="sxs-lookup"><span data-stu-id="5ae54-196">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="5ae54-197">如需保護靜態檔案的方法，請參閱 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="5ae54-197">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="5ae54-198">若靜態檔案中介軟體未處理要求，該要求會繼續傳遞給執行驗證的驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="5ae54-198">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="5ae54-199">驗證不會對未經驗證的要求執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="5ae54-199">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="5ae54-200">雖然驗證中介軟體會驗證要求，但只有在 MVC 選取特定 Razor 頁面或 mvc 控制器和動作之後，才會發生授權 (和拒絕) 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-200">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="5ae54-201">下列範例示範靜態檔案中介軟體在回應壓縮中介軟體之前處理靜態檔案要求之前的靜態檔案順序。</span><span class="sxs-lookup"><span data-stu-id="5ae54-201">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="5ae54-202">靜態檔案並不會以此中介軟體順序壓縮。</span><span class="sxs-lookup"><span data-stu-id="5ae54-202">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="5ae54-203">您 Razor 可以壓縮頁面回應。</span><span class="sxs-lookup"><span data-stu-id="5ae54-203">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseRouting();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="5ae54-204">針對 (Spa) 的單一頁面應用程式，SPA 中介軟體 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> 通常會最後出現在中介軟體管線中。</span><span class="sxs-lookup"><span data-stu-id="5ae54-204">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="5ae54-205">SPA 中介軟體最後有：</span><span class="sxs-lookup"><span data-stu-id="5ae54-205">The SPA middleware comes last:</span></span>

* <span data-ttu-id="5ae54-206">以允許所有其他中介軟體先回應相符的要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-206">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="5ae54-207">若要允許具有用戶端路由的 Spa 執行伺服器應用程式無法辨識的所有路由。</span><span class="sxs-lookup"><span data-stu-id="5ae54-207">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="5ae54-208">如需 Spa 的詳細資訊，請參閱「 [回應](xref:spa/react) 」和「 [角度](xref:spa/angular) 」專案範本的指南。</span><span class="sxs-lookup"><span data-stu-id="5ae54-208">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="5ae54-209">轉送標頭中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="5ae54-209">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="5ae54-210">分支中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="5ae54-210">Branch the middleware pipeline</span></span>

<span data-ttu-id="5ae54-211"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 擴充方法則是用來分支管線的慣例。</span><span class="sxs-lookup"><span data-stu-id="5ae54-211"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="5ae54-212">`Map` 會依據指定要求路徑的相符項目將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="5ae54-212">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="5ae54-213">如果要求路徑以指定路徑為開頭，則會執行分支。</span><span class="sxs-lookup"><span data-stu-id="5ae54-213">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="5ae54-214">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="5ae54-214">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="5ae54-215">要求</span><span class="sxs-lookup"><span data-stu-id="5ae54-215">Request</span></span>             | <span data-ttu-id="5ae54-216">回應</span><span class="sxs-lookup"><span data-stu-id="5ae54-216">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="5ae54-217">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="5ae54-217">localhost:1234</span></span>      | <span data-ttu-id="5ae54-218">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="5ae54-218">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="5ae54-219">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="5ae54-219">localhost:1234/map1</span></span> | <span data-ttu-id="5ae54-220">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="5ae54-220">Map Test 1</span></span>                   |
| <span data-ttu-id="5ae54-221">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="5ae54-221">localhost:1234/map2</span></span> | <span data-ttu-id="5ae54-222">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="5ae54-222">Map Test 2</span></span>                   |
| <span data-ttu-id="5ae54-223">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="5ae54-223">localhost:1234/map3</span></span> | <span data-ttu-id="5ae54-224">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="5ae54-224">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="5ae54-225">使用 `Map` 時，會將相符的路徑線段從 `HttpRequest.Path` 移除，並附加至每個要求的 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="5ae54-225">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="5ae54-226">`Map` 支援巢狀項目，例如：</span><span class="sxs-lookup"><span data-stu-id="5ae54-226">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="5ae54-227">`Map` 也可以一次比對多個線段：</span><span class="sxs-lookup"><span data-stu-id="5ae54-227">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="5ae54-228"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 會依據指定述詞的結果將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="5ae54-228"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="5ae54-229">`Func<HttpContext, bool>` 類型的任何述詞皆可用來將要求對應至管線的新分支。</span><span class="sxs-lookup"><span data-stu-id="5ae54-229">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="5ae54-230">下列範例會使用述詞來偵測查詢字串變數 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="5ae54-230">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="5ae54-231">下表顯示使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應：</span><span class="sxs-lookup"><span data-stu-id="5ae54-231">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="5ae54-232">要求</span><span class="sxs-lookup"><span data-stu-id="5ae54-232">Request</span></span>                       | <span data-ttu-id="5ae54-233">回應</span><span class="sxs-lookup"><span data-stu-id="5ae54-233">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="5ae54-234">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="5ae54-234">localhost:1234</span></span>                | <span data-ttu-id="5ae54-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="5ae54-235">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="5ae54-236">localhost： 1234/？分支 = main</span><span class="sxs-lookup"><span data-stu-id="5ae54-236">localhost:1234/?branch=main</span></span> | <span data-ttu-id="5ae54-237">使用的分支 = main</span><span class="sxs-lookup"><span data-stu-id="5ae54-237">Branch used = main</span></span>         |

<span data-ttu-id="5ae54-238"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> 也會根據指定述詞的結果來分支要求管線。</span><span class="sxs-lookup"><span data-stu-id="5ae54-238"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="5ae54-239">與不同的 `MapWhen` 是，此分支會重新加入至主要管線（如果它不是短路或包含終端中介軟體）：</span><span class="sxs-lookup"><span data-stu-id="5ae54-239">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=18-19)]

<span data-ttu-id="5ae54-240">在上述範例中，回應為「來自主要管線的 Hello」。</span><span class="sxs-lookup"><span data-stu-id="5ae54-240">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="5ae54-241">是針對所有要求所撰寫。</span><span class="sxs-lookup"><span data-stu-id="5ae54-241">is written for all requests.</span></span> <span data-ttu-id="5ae54-242">如果要求包含查詢字串變數，則 `branch` 會在重新加入主要管線之前記錄其值。</span><span class="sxs-lookup"><span data-stu-id="5ae54-242">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="5ae54-243">內建的中介軟體</span><span class="sxs-lookup"><span data-stu-id="5ae54-243">Built-in middleware</span></span>

<span data-ttu-id="5ae54-244">ASP.NET Core 隨附下列中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="5ae54-244">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="5ae54-245">「順序」欄說明 中介軟體在要求處理管線中的位置，以及中介軟體可終止要求處理的情況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-245">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="5ae54-246">當中介軟體將要求處理管線短路並防止接下來的下游中介軟體處理要求時，這就是所謂的「終端中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="5ae54-246">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="5ae54-247">如需詳細資訊，請參閱[使用 IApplicationBuilder 建立中介軟體管線](#create-a-middleware-pipeline-with-iapplicationbuilder)。</span><span class="sxs-lookup"><span data-stu-id="5ae54-247">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="5ae54-248">中介軟體</span><span class="sxs-lookup"><span data-stu-id="5ae54-248">Middleware</span></span> | <span data-ttu-id="5ae54-249">Description</span><span class="sxs-lookup"><span data-stu-id="5ae54-249">Description</span></span> | <span data-ttu-id="5ae54-250">單</span><span class="sxs-lookup"><span data-stu-id="5ae54-250">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="5ae54-251">驗證</span><span class="sxs-lookup"><span data-stu-id="5ae54-251">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="5ae54-252">提供驗證支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-252">Provides authentication support.</span></span> | <span data-ttu-id="5ae54-253">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-253">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="5ae54-254">OAuth 回呼的終端機。</span><span class="sxs-lookup"><span data-stu-id="5ae54-254">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="5ae54-255">授權</span><span class="sxs-lookup"><span data-stu-id="5ae54-255">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="5ae54-256">提供授權支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-256">Provides authorization support.</span></span> | <span data-ttu-id="5ae54-257">緊接在驗證中介軟體之後。</span><span class="sxs-lookup"><span data-stu-id="5ae54-257">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="5ae54-258">Cookie 政策</span><span class="sxs-lookup"><span data-stu-id="5ae54-258">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="5ae54-259">追蹤使用者同意以儲存個人資訊，並強制執列欄位的最小標準 cookie ，例如 `secure` 和 `SameSite` 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-259">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="5ae54-260">在發出的中介軟體之前 cookie 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-260">Before middleware that issues cookies.</span></span> <span data-ttu-id="5ae54-261">範例：驗證、工作階段、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="5ae54-261">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="5ae54-262">CORS</span><span class="sxs-lookup"><span data-stu-id="5ae54-262">CORS</span></span>](xref:security/cors) | <span data-ttu-id="5ae54-263">設定跨原始來源資源共用。</span><span class="sxs-lookup"><span data-stu-id="5ae54-263">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="5ae54-264">在使用 CORS 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-264">Before components that use CORS.</span></span> <span data-ttu-id="5ae54-265">`UseCors` 目前必須在 `UseResponseCaching` [此 bug](https://github.com/dotnet/aspnetcore/issues/23218)之前執行。</span><span class="sxs-lookup"><span data-stu-id="5ae54-265">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="5ae54-266">診斷</span><span class="sxs-lookup"><span data-stu-id="5ae54-266">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="5ae54-267">有幾個不同的中介軟體，可提供開發人員例外狀況頁面、例外狀況處理、狀態字碼頁面，以及新應用程式的預設網頁。</span><span class="sxs-lookup"><span data-stu-id="5ae54-267">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="5ae54-268">在產生錯誤的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-268">Before components that generate errors.</span></span> <span data-ttu-id="5ae54-269">例外狀況的終端機，或提供新應用程式的預設網頁。</span><span class="sxs-lookup"><span data-stu-id="5ae54-269">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="5ae54-270">轉寄的標頭</span><span class="sxs-lookup"><span data-stu-id="5ae54-270">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="5ae54-271">將設為 Proxy 的標頭轉送到目前要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-271">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="5ae54-272">在使用更新方法的欄位之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-272">Before components that consume the updated fields.</span></span> <span data-ttu-id="5ae54-273">範例：配置、主機，用戶端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="5ae54-273">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="5ae54-274">健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="5ae54-274">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="5ae54-275">檢查 ASP.NET Core 應用程式及其相依性的健康狀態，例如檢查資料庫可用性。</span><span class="sxs-lookup"><span data-stu-id="5ae54-275">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="5ae54-276">若某項要求與健康狀態檢查端點相符，則會是終端機。</span><span class="sxs-lookup"><span data-stu-id="5ae54-276">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="5ae54-277">標頭傳播</span><span class="sxs-lookup"><span data-stu-id="5ae54-277">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="5ae54-278">將來自傳入要求的 HTTP 標頭傳播至傳出 HTTP 用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-278">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="5ae54-279">HTTP 方法覆寫</span><span class="sxs-lookup"><span data-stu-id="5ae54-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="5ae54-280">允許傳入的 POST 要求覆寫方法。</span><span class="sxs-lookup"><span data-stu-id="5ae54-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="5ae54-281">在使用更新方法的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="5ae54-282">HTTPS 重新導向</span><span class="sxs-lookup"><span data-stu-id="5ae54-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="5ae54-283">將所有 HTTP 要求都重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="5ae54-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="5ae54-284">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="5ae54-285">HTTP 嚴格的傳輸安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="5ae54-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="5ae54-286">增強安全性的中介軟體，可新增特殊的回應標頭。</span><span class="sxs-lookup"><span data-stu-id="5ae54-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="5ae54-287">在傳送回應前和修改要求的元件後。</span><span class="sxs-lookup"><span data-stu-id="5ae54-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="5ae54-288">範例：轉送的標頭、URL 重寫。</span><span class="sxs-lookup"><span data-stu-id="5ae54-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="5ae54-289">MVC</span><span class="sxs-lookup"><span data-stu-id="5ae54-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="5ae54-290">使用 MVC/Pages 處理要求 Razor 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="5ae54-291">若要求符合路由則終止。</span><span class="sxs-lookup"><span data-stu-id="5ae54-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="5ae54-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="5ae54-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="5ae54-293">以 OWIN 為基礎之應用程式、伺服器和中介軟體的 Interop。</span><span class="sxs-lookup"><span data-stu-id="5ae54-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="5ae54-294">若 OWIN 中介軟體完全處理要求則終止。</span><span class="sxs-lookup"><span data-stu-id="5ae54-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="5ae54-295">回應快取</span><span class="sxs-lookup"><span data-stu-id="5ae54-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="5ae54-296">提供快取回應的支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-296">Provides support for caching responses.</span></span> | <span data-ttu-id="5ae54-297">在需要快取的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-297">Before components that require caching.</span></span> <span data-ttu-id="5ae54-298">`UseCORS` 必須在前面 `UseResponseCaching` 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-298">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="5ae54-299">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="5ae54-299">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="5ae54-300">提供壓縮回應的支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-300">Provides support for compressing responses.</span></span> | <span data-ttu-id="5ae54-301">在需要壓縮的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-301">Before components that require compression.</span></span> |
| [<span data-ttu-id="5ae54-302">要求當地語系化</span><span class="sxs-lookup"><span data-stu-id="5ae54-302">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="5ae54-303">提供當地語系化支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-303">Provides localization support.</span></span> | <span data-ttu-id="5ae54-304">在偵測當地語系化的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-304">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="5ae54-305">端點路由</span><span class="sxs-lookup"><span data-stu-id="5ae54-305">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="5ae54-306">定義並限制要求路由。</span><span class="sxs-lookup"><span data-stu-id="5ae54-306">Defines and constrains request routes.</span></span> | <span data-ttu-id="5ae54-307">比對路由的終端機。</span><span class="sxs-lookup"><span data-stu-id="5ae54-307">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="5ae54-308">SPA</span><span class="sxs-lookup"><span data-stu-id="5ae54-308">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="5ae54-309">藉由將單一頁面應用程式的預設頁面傳回 (SPA，在中介軟體鏈中處理來自此點的所有要求) </span><span class="sxs-lookup"><span data-stu-id="5ae54-309">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="5ae54-310">在鏈中延遲，讓其他中介軟體可提供靜態檔案、MVC 動作等等，都優先。</span><span class="sxs-lookup"><span data-stu-id="5ae54-310">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="5ae54-311">工作階段</span><span class="sxs-lookup"><span data-stu-id="5ae54-311">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="5ae54-312">提供管理使用者工作階段的支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-312">Provides support for managing user sessions.</span></span> | <span data-ttu-id="5ae54-313">在需要工作階段的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-313">Before components that require Session.</span></span> | 
| [<span data-ttu-id="5ae54-314">靜態檔案</span><span class="sxs-lookup"><span data-stu-id="5ae54-314">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="5ae54-315">支援靜態檔案的提供和目錄瀏覽。</span><span class="sxs-lookup"><span data-stu-id="5ae54-315">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="5ae54-316">若要求符合檔案則終止。</span><span class="sxs-lookup"><span data-stu-id="5ae54-316">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="5ae54-317">URL 重寫</span><span class="sxs-lookup"><span data-stu-id="5ae54-317">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="5ae54-318">提供重寫 URL 及重新導向要求的支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-318">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="5ae54-319">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-319">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="5ae54-320">WebSocket</span><span class="sxs-lookup"><span data-stu-id="5ae54-320">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="5ae54-321">啟用 WebSockets 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="5ae54-321">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="5ae54-322">在接受 WebSocket 要求的必要元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-322">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="5ae54-323">其他資源</span><span class="sxs-lookup"><span data-stu-id="5ae54-323">Additional resources</span></span>

* <span data-ttu-id="5ae54-324">[存留期和註冊選項](xref:fundamentals/dependency-injection#lifetime-and-registration-options) 包含具有 *範圍*、 *暫時性* 和 *單一* 存留期服務之中介軟體的完整範例。</span><span class="sxs-lookup"><span data-stu-id="5ae54-324">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped*, *transient*, and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5ae54-325">由 [Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Steve Smith](https://ardalis.com/) 撰寫</span><span class="sxs-lookup"><span data-stu-id="5ae54-325">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5ae54-326">中介軟體為組成應用程式管線的軟體，用以處理要求與回應。</span><span class="sxs-lookup"><span data-stu-id="5ae54-326">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="5ae54-327">每個元件：</span><span class="sxs-lookup"><span data-stu-id="5ae54-327">Each component:</span></span>

* <span data-ttu-id="5ae54-328">可選擇是否要將要求傳送到管線中的下一個元件。</span><span class="sxs-lookup"><span data-stu-id="5ae54-328">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="5ae54-329">可以下一個元件的前後執行工作。</span><span class="sxs-lookup"><span data-stu-id="5ae54-329">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="5ae54-330">要求委派用於建置要求管線，</span><span class="sxs-lookup"><span data-stu-id="5ae54-330">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="5ae54-331">其會處理每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-331">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="5ae54-332">要求委派的設定方式為使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="5ae54-332">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="5ae54-333">您可將個別要求委派指定為內嵌匿名方法 (在內嵌中介軟體中呼叫)，或於可重複使用的類別中加以定義。</span><span class="sxs-lookup"><span data-stu-id="5ae54-333">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="5ae54-334">這些可重複使用的類別及內嵌匿名方法皆為「中介軟體」，也稱為「中介軟體元件」。</span><span class="sxs-lookup"><span data-stu-id="5ae54-334">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="5ae54-335">要求管線中的每個中介軟體元件負責叫用管線中下一個元件，或對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="5ae54-335">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="5ae54-336">當中介軟體短路時，稱為「終端中介軟體」，因為它會防止接下來的中介軟體處理要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-336">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="5ae54-337"><xref:migration/http-modules> 說明 ASP.NET Core 和 ASP.NET 4.x 之間的要求管線差異，並提供更多中介軟體範例。</span><span class="sxs-lookup"><span data-stu-id="5ae54-337"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="5ae54-338">使用 IApplicationBuilder 建立中介軟體管線</span><span class="sxs-lookup"><span data-stu-id="5ae54-338">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="5ae54-339">ASP.NET Core 要求管線由要求委派序列組成，並會一個接著一個呼叫。</span><span class="sxs-lookup"><span data-stu-id="5ae54-339">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="5ae54-340">下圖說明此概念。</span><span class="sxs-lookup"><span data-stu-id="5ae54-340">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="5ae54-341">執行緒遵循黑色箭號執行。</span><span class="sxs-lookup"><span data-stu-id="5ae54-341">The thread of execution follows the black arrows.</span></span>

![要求處理模式說明要求抵達，經過三個中介軟體所處理，然後應用程式送出回應。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="5ae54-345">每一個委派皆能在下個委派的前後執行作業。</span><span class="sxs-lookup"><span data-stu-id="5ae54-345">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="5ae54-346">處理例外狀況的委派必須提前在管線中呼叫，以便其可與管線後續階段中所發生的例外狀況達成一致。</span><span class="sxs-lookup"><span data-stu-id="5ae54-346">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="5ae54-347">最簡潔的 ASP.NET Core 應用程式會設定單一要求委派來處理所有要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-347">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="5ae54-348">此情況不包含實際要求管線。</span><span class="sxs-lookup"><span data-stu-id="5ae54-348">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="5ae54-349">反之，系統會呼叫單一匿名函式來回應每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-349">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="5ae54-350">第一個 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委派會終止管線。</span><span class="sxs-lookup"><span data-stu-id="5ae54-350">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="5ae54-351">將多個要求委派鏈結在一起的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>。</span><span class="sxs-lookup"><span data-stu-id="5ae54-351">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="5ae54-352">`next` 參數代表管線中的下個委派。</span><span class="sxs-lookup"><span data-stu-id="5ae54-352">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="5ae54-353">您可以「不」呼叫「下一個」參數來對管線執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="5ae54-353">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="5ae54-354">您通常可以在下個委派的前後執行動作，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="5ae54-354">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="5ae54-355">當委派不將要求傳遞到下一個委派時，這就是所謂 *讓要求管線短路*。</span><span class="sxs-lookup"><span data-stu-id="5ae54-355">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="5ae54-356">因為最少運算可避免不必要的工作，所以經常使用。</span><span class="sxs-lookup"><span data-stu-id="5ae54-356">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="5ae54-357">例如，[靜態檔案中介軟體](xref:fundamentals/static-files)可以做為 *終端中介軟體* 使用，方式是處理靜態檔案的要求，並對剩餘的管線執行短路。</span><span class="sxs-lookup"><span data-stu-id="5ae54-357">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="5ae54-358">在中介軟體之前新增到管線且終結進一步處理的中介軟體在其 `next.Invoke` 陳述式之後仍然處理程式碼。</span><span class="sxs-lookup"><span data-stu-id="5ae54-358">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="5ae54-359">不過，查看下列有關嘗試寫入已傳送之回應的警告。</span><span class="sxs-lookup"><span data-stu-id="5ae54-359">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="5ae54-360">請不要在回應已傳送給用戶端之後呼叫 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="5ae54-360">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="5ae54-361">回應啟動後，變更為 <xref:Microsoft.AspNetCore.Http.HttpResponse> 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-361">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="5ae54-362">例如，變更設定標頭、狀態碼等都會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-362">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="5ae54-363">若在呼叫 `next` 後寫入回應本文：</span><span class="sxs-lookup"><span data-stu-id="5ae54-363">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="5ae54-364">可能導致違反通訊協定。</span><span class="sxs-lookup"><span data-stu-id="5ae54-364">May cause a protocol violation.</span></span> <span data-ttu-id="5ae54-365">例如，寫入超過指定 `Content-Length` 的內容。</span><span class="sxs-lookup"><span data-stu-id="5ae54-365">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="5ae54-366">可能損毀本文格式。</span><span class="sxs-lookup"><span data-stu-id="5ae54-366">May corrupt the body format.</span></span> <span data-ttu-id="5ae54-367">例如，將 HTML 頁尾寫入 CSS 檔。</span><span class="sxs-lookup"><span data-stu-id="5ae54-367">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="5ae54-368"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是實用的提示，可表示是否已傳送標頭 (或) 是否已寫入本文。</span><span class="sxs-lookup"><span data-stu-id="5ae54-368"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="5ae54-369">中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="5ae54-369">Middleware order</span></span>

<span data-ttu-id="5ae54-370">`Startup.Configure` 方法內中介軟體元件的新增順序可定義在要求時叫用中介軟體元件的順序及回應的反向順序。</span><span class="sxs-lookup"><span data-stu-id="5ae54-370">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="5ae54-371">對安全性、效能和功能而言，順序是非常 **重要** 的。</span><span class="sxs-lookup"><span data-stu-id="5ae54-371">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="5ae54-372">下列方法會依 `Startup.Configure` 建議的順序新增安全性相關的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="5ae54-372">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="5ae54-373">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="5ae54-373">In the preceding code:</span></span>

* <span data-ttu-id="5ae54-374">以 [個別使用者帳戶](xref:security/authentication/identity) 建立新的 web 應用程式時，未新增的中介軟體會以批註方式出現。</span><span class="sxs-lookup"><span data-stu-id="5ae54-374">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="5ae54-375">並非每個中介軟體都必須以這種確切的順序進行，但有很多。</span><span class="sxs-lookup"><span data-stu-id="5ae54-375">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="5ae54-376">例如， `UseCors` 和 `UseAuthentication` 必須依照顯示的順序進行。</span><span class="sxs-lookup"><span data-stu-id="5ae54-376">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="5ae54-377">下列 `Startup.Configure` 方法會新增適用於一般應用程式案例的中介軟體元件：</span><span class="sxs-lookup"><span data-stu-id="5ae54-377">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="5ae54-378">例外狀況/錯誤處理</span><span class="sxs-lookup"><span data-stu-id="5ae54-378">Exception/error handling</span></span>
   * <span data-ttu-id="5ae54-379">當應用程式在開發環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="5ae54-379">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="5ae54-380">開發人員例外狀況頁面中介軟體 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 會回報應用程式執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="5ae54-380">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="5ae54-381">資料錯誤頁面中介軟體 (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) 會回報資料庫執行階段錯誤。</span><span class="sxs-lookup"><span data-stu-id="5ae54-381">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="5ae54-382">當應用程式在生產環境中執行時：</span><span class="sxs-lookup"><span data-stu-id="5ae54-382">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="5ae54-383">例外狀況處理常式中介軟體 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 會攔截在下列中介軟體中擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-383">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="5ae54-384">HTTP 靜態傳輸安全性通訊協定 (HSTS) 中介軟體 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 會新增 `Strict-Transport-Security` 標頭。</span><span class="sxs-lookup"><span data-stu-id="5ae54-384">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="5ae54-385">HTTPS 重新導向中介軟體 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 會將 HTTP 要求重新導向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="5ae54-385">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="5ae54-386">靜態檔案中介軟體 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 會傳回靜態檔案並縮短進一步的要求處理時間。</span><span class="sxs-lookup"><span data-stu-id="5ae54-386">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="5ae54-387">Cookie 原則中介軟體 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) 符合歐盟一般資料保護規定的應用程式 (GDPR) 規定。</span><span class="sxs-lookup"><span data-stu-id="5ae54-387">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="5ae54-388">驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 會嘗試在允許使用者存取安全資源之前先驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="5ae54-388">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="5ae54-389">工作階段中介軟體 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 會建立並維護工作階段狀態。</span><span class="sxs-lookup"><span data-stu-id="5ae54-389">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="5ae54-390">如果應用程式使用會話狀態，請在 Cookie 原則中介軟體和 MVC 中介軟體之前呼叫會話中介軟體。</span><span class="sxs-lookup"><span data-stu-id="5ae54-390">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="5ae54-391">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) 以將 MVC 新增到要求管線。</span><span class="sxs-lookup"><span data-stu-id="5ae54-391">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="5ae54-392">在上面的範例程式碼中，每個中介軟體擴充方法都會透過 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空間在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公開。</span><span class="sxs-lookup"><span data-stu-id="5ae54-392">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="5ae54-393"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是第一個新增到管道的中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="5ae54-393"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="5ae54-394">因此，例外處理常式中介軟體會攔截後續呼叫中發生的所有例外狀況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-394">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="5ae54-395">靜態檔案中介軟體會提前在管線中呼叫，以便其無須逐一處理剩餘的元件，就能處理要求及執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="5ae54-395">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="5ae54-396">靜態檔案中介軟體 **不提供任何** 授權檢查。</span><span class="sxs-lookup"><span data-stu-id="5ae54-396">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="5ae54-397">靜態檔案中介軟體所提供的任何檔案（包括 *wwwroot* 下的檔案）都可以公開使用。</span><span class="sxs-lookup"><span data-stu-id="5ae54-397">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="5ae54-398">如需保護靜態檔案的方法，請參閱 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="5ae54-398">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="5ae54-399">若靜態檔案中介軟體未處理要求，該要求會繼續傳遞給執行驗證的驗證中介軟體 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="5ae54-399">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="5ae54-400">驗證不會對未經驗證的要求執行最少運算。</span><span class="sxs-lookup"><span data-stu-id="5ae54-400">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="5ae54-401">雖然驗證中介軟體會驗證要求，但只有在 MVC 選取特定 Razor 頁面或 mvc 控制器和動作之後，才會發生授權 (和拒絕) 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-401">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="5ae54-402">下列範例示範靜態檔案中介軟體在回應壓縮中介軟體之前處理靜態檔案要求之前的靜態檔案順序。</span><span class="sxs-lookup"><span data-stu-id="5ae54-402">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="5ae54-403">靜態檔案並不會以此中介軟體順序壓縮。</span><span class="sxs-lookup"><span data-stu-id="5ae54-403">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="5ae54-404">來自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> 的 MVC 回應可壓縮。</span><span class="sxs-lookup"><span data-stu-id="5ae54-404">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="5ae54-405">Use、Run 與 Map</span><span class="sxs-lookup"><span data-stu-id="5ae54-405">Use, Run, and Map</span></span>

<span data-ttu-id="5ae54-406">設定 HTTP 管線的方法是使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 及 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>。</span><span class="sxs-lookup"><span data-stu-id="5ae54-406">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="5ae54-407">`Use` 方法可對管線執行最少運算 (亦即，如果其不呼叫 `next` 要求委派的情況下)。</span><span class="sxs-lookup"><span data-stu-id="5ae54-407">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="5ae54-408">`Run` 是一種慣例，且有些中介軟體元件可能將執行於管線尾端的 `Run[Middleware]` 方法公開。</span><span class="sxs-lookup"><span data-stu-id="5ae54-408">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="5ae54-409"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 擴充方法則是用來分支管線的慣例。</span><span class="sxs-lookup"><span data-stu-id="5ae54-409"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="5ae54-410">`Map` 會依據指定要求路徑的相符項目將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="5ae54-410">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="5ae54-411">如果要求路徑以指定路徑為開頭，則會執行分支。</span><span class="sxs-lookup"><span data-stu-id="5ae54-411">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="5ae54-412">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="5ae54-412">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="5ae54-413">要求</span><span class="sxs-lookup"><span data-stu-id="5ae54-413">Request</span></span>             | <span data-ttu-id="5ae54-414">回應</span><span class="sxs-lookup"><span data-stu-id="5ae54-414">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="5ae54-415">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="5ae54-415">localhost:1234</span></span>      | <span data-ttu-id="5ae54-416">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="5ae54-416">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="5ae54-417">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="5ae54-417">localhost:1234/map1</span></span> | <span data-ttu-id="5ae54-418">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="5ae54-418">Map Test 1</span></span>                   |
| <span data-ttu-id="5ae54-419">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="5ae54-419">localhost:1234/map2</span></span> | <span data-ttu-id="5ae54-420">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="5ae54-420">Map Test 2</span></span>                   |
| <span data-ttu-id="5ae54-421">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="5ae54-421">localhost:1234/map3</span></span> | <span data-ttu-id="5ae54-422">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="5ae54-422">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="5ae54-423">使用 `Map` 時，會將相符的路徑線段從 `HttpRequest.Path` 移除，並附加至每個要求的 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="5ae54-423">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="5ae54-424"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 會依據指定述詞的結果將要求管線分支。</span><span class="sxs-lookup"><span data-stu-id="5ae54-424"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="5ae54-425">`Func<HttpContext, bool>` 類型的任何述詞皆可用來將要求對應至管線的新分支。</span><span class="sxs-lookup"><span data-stu-id="5ae54-425">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="5ae54-426">下列範例會使用述詞來偵測查詢字串變數 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="5ae54-426">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="5ae54-427">下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。</span><span class="sxs-lookup"><span data-stu-id="5ae54-427">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="5ae54-428">要求</span><span class="sxs-lookup"><span data-stu-id="5ae54-428">Request</span></span>                       | <span data-ttu-id="5ae54-429">回應</span><span class="sxs-lookup"><span data-stu-id="5ae54-429">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="5ae54-430">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="5ae54-430">localhost:1234</span></span>                | <span data-ttu-id="5ae54-431">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="5ae54-431">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="5ae54-432">localhost： 1234/？分支 = main</span><span class="sxs-lookup"><span data-stu-id="5ae54-432">localhost:1234/?branch=main</span></span> | <span data-ttu-id="5ae54-433">使用的分支 = main</span><span class="sxs-lookup"><span data-stu-id="5ae54-433">Branch used = main</span></span>         |

<span data-ttu-id="5ae54-434">`Map` 支援巢狀項目，例如：</span><span class="sxs-lookup"><span data-stu-id="5ae54-434">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="5ae54-435">`Map` 也可以一次比對多個線段：</span><span class="sxs-lookup"><span data-stu-id="5ae54-435">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="5ae54-436">內建的中介軟體</span><span class="sxs-lookup"><span data-stu-id="5ae54-436">Built-in middleware</span></span>

<span data-ttu-id="5ae54-437">ASP.NET Core 隨附下列中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="5ae54-437">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="5ae54-438">「順序」欄說明 中介軟體在要求處理管線中的位置，以及中介軟體可終止要求處理的情況。</span><span class="sxs-lookup"><span data-stu-id="5ae54-438">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="5ae54-439">當中介軟體將要求處理管線短路並防止接下來的下游中介軟體處理要求時，這就是所謂的「終端中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="5ae54-439">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="5ae54-440">如需詳細資訊，請參閱[使用 IApplicationBuilder 建立中介軟體管線](#create-a-middleware-pipeline-with-iapplicationbuilder)。</span><span class="sxs-lookup"><span data-stu-id="5ae54-440">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="5ae54-441">中介軟體</span><span class="sxs-lookup"><span data-stu-id="5ae54-441">Middleware</span></span> | <span data-ttu-id="5ae54-442">Description</span><span class="sxs-lookup"><span data-stu-id="5ae54-442">Description</span></span> | <span data-ttu-id="5ae54-443">單</span><span class="sxs-lookup"><span data-stu-id="5ae54-443">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="5ae54-444">驗證</span><span class="sxs-lookup"><span data-stu-id="5ae54-444">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="5ae54-445">提供驗證支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-445">Provides authentication support.</span></span> | <span data-ttu-id="5ae54-446">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-446">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="5ae54-447">OAuth 回呼的終端機。</span><span class="sxs-lookup"><span data-stu-id="5ae54-447">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="5ae54-448">Cookie 政策</span><span class="sxs-lookup"><span data-stu-id="5ae54-448">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="5ae54-449">追蹤使用者同意以儲存個人資訊，並強制執列欄位的最小標準 cookie ，例如 `secure` 和 `SameSite` 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-449">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="5ae54-450">在發出的中介軟體之前 cookie 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-450">Before middleware that issues cookies.</span></span> <span data-ttu-id="5ae54-451">範例：驗證、工作階段、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="5ae54-451">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="5ae54-452">CORS</span><span class="sxs-lookup"><span data-stu-id="5ae54-452">CORS</span></span>](xref:security/cors) | <span data-ttu-id="5ae54-453">設定跨原始來源資源共用。</span><span class="sxs-lookup"><span data-stu-id="5ae54-453">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="5ae54-454">在使用 CORS 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-454">Before components that use CORS.</span></span> |
| [<span data-ttu-id="5ae54-455">診斷</span><span class="sxs-lookup"><span data-stu-id="5ae54-455">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="5ae54-456">有幾個不同的中介軟體，可提供開發人員例外狀況頁面、例外狀況處理、狀態字碼頁面，以及新應用程式的預設網頁。</span><span class="sxs-lookup"><span data-stu-id="5ae54-456">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="5ae54-457">在產生錯誤的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-457">Before components that generate errors.</span></span> <span data-ttu-id="5ae54-458">例外狀況的終端機，或提供新應用程式的預設網頁。</span><span class="sxs-lookup"><span data-stu-id="5ae54-458">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="5ae54-459">轉寄的標頭</span><span class="sxs-lookup"><span data-stu-id="5ae54-459">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="5ae54-460">將設為 Proxy 的標頭轉送到目前要求。</span><span class="sxs-lookup"><span data-stu-id="5ae54-460">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="5ae54-461">在使用更新方法的欄位之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-461">Before components that consume the updated fields.</span></span> <span data-ttu-id="5ae54-462">範例：配置、主機，用戶端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="5ae54-462">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="5ae54-463">健康情況檢查</span><span class="sxs-lookup"><span data-stu-id="5ae54-463">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="5ae54-464">檢查 ASP.NET Core 應用程式及其相依性的健康狀態，例如檢查資料庫可用性。</span><span class="sxs-lookup"><span data-stu-id="5ae54-464">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="5ae54-465">若某項要求與健康狀態檢查端點相符，則會是終端機。</span><span class="sxs-lookup"><span data-stu-id="5ae54-465">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="5ae54-466">HTTP 方法覆寫</span><span class="sxs-lookup"><span data-stu-id="5ae54-466">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="5ae54-467">允許傳入的 POST 要求覆寫方法。</span><span class="sxs-lookup"><span data-stu-id="5ae54-467">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="5ae54-468">在使用更新方法的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-468">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="5ae54-469">HTTPS 重新導向</span><span class="sxs-lookup"><span data-stu-id="5ae54-469">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="5ae54-470">將所有 HTTP 要求都重新導向至 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="5ae54-470">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="5ae54-471">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-471">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="5ae54-472">HTTP 嚴格的傳輸安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="5ae54-472">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="5ae54-473">增強安全性的中介軟體，可新增特殊的回應標頭。</span><span class="sxs-lookup"><span data-stu-id="5ae54-473">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="5ae54-474">在傳送回應前和修改要求的元件後。</span><span class="sxs-lookup"><span data-stu-id="5ae54-474">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="5ae54-475">範例：轉送的標頭、URL 重寫。</span><span class="sxs-lookup"><span data-stu-id="5ae54-475">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="5ae54-476">MVC</span><span class="sxs-lookup"><span data-stu-id="5ae54-476">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="5ae54-477">使用 MVC/Pages 處理要求 Razor 。</span><span class="sxs-lookup"><span data-stu-id="5ae54-477">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="5ae54-478">若要求符合路由則終止。</span><span class="sxs-lookup"><span data-stu-id="5ae54-478">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="5ae54-479">OWIN</span><span class="sxs-lookup"><span data-stu-id="5ae54-479">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="5ae54-480">以 OWIN 為基礎之應用程式、伺服器和中介軟體的 Interop。</span><span class="sxs-lookup"><span data-stu-id="5ae54-480">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="5ae54-481">若 OWIN 中介軟體完全處理要求則終止。</span><span class="sxs-lookup"><span data-stu-id="5ae54-481">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="5ae54-482">回應快取</span><span class="sxs-lookup"><span data-stu-id="5ae54-482">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="5ae54-483">提供快取回應的支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-483">Provides support for caching responses.</span></span> | <span data-ttu-id="5ae54-484">在需要快取的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-484">Before components that require caching.</span></span> |
| [<span data-ttu-id="5ae54-485">回應壓縮</span><span class="sxs-lookup"><span data-stu-id="5ae54-485">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="5ae54-486">提供壓縮回應的支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-486">Provides support for compressing responses.</span></span> | <span data-ttu-id="5ae54-487">在需要壓縮的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-487">Before components that require compression.</span></span> |
| [<span data-ttu-id="5ae54-488">要求當地語系化</span><span class="sxs-lookup"><span data-stu-id="5ae54-488">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="5ae54-489">提供當地語系化支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-489">Provides localization support.</span></span> | <span data-ttu-id="5ae54-490">在偵測當地語系化的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-490">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="5ae54-491">端點路由</span><span class="sxs-lookup"><span data-stu-id="5ae54-491">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="5ae54-492">定義並限制要求路由。</span><span class="sxs-lookup"><span data-stu-id="5ae54-492">Defines and constrains request routes.</span></span> | <span data-ttu-id="5ae54-493">比對路由的終端機。</span><span class="sxs-lookup"><span data-stu-id="5ae54-493">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="5ae54-494">工作階段</span><span class="sxs-lookup"><span data-stu-id="5ae54-494">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="5ae54-495">提供管理使用者工作階段的支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-495">Provides support for managing user sessions.</span></span> | <span data-ttu-id="5ae54-496">在需要工作階段的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-496">Before components that require Session.</span></span> |
| [<span data-ttu-id="5ae54-497">靜態檔案</span><span class="sxs-lookup"><span data-stu-id="5ae54-497">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="5ae54-498">支援靜態檔案的提供和目錄瀏覽。</span><span class="sxs-lookup"><span data-stu-id="5ae54-498">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="5ae54-499">若要求符合檔案則終止。</span><span class="sxs-lookup"><span data-stu-id="5ae54-499">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="5ae54-500">URL 重寫</span><span class="sxs-lookup"><span data-stu-id="5ae54-500">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="5ae54-501">提供重寫 URL 及重新導向要求的支援。</span><span class="sxs-lookup"><span data-stu-id="5ae54-501">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="5ae54-502">在使用 URL 的元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-502">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="5ae54-503">WebSocket</span><span class="sxs-lookup"><span data-stu-id="5ae54-503">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="5ae54-504">啟用 WebSockets 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="5ae54-504">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="5ae54-505">在接受 WebSocket 要求的必要元件之前。</span><span class="sxs-lookup"><span data-stu-id="5ae54-505">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="5ae54-506">其他資源</span><span class="sxs-lookup"><span data-stu-id="5ae54-506">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
