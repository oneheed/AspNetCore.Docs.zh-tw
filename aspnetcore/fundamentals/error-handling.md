---
title: 處理 ASP.NET Core 中的錯誤
author: rick-anderson
description: 了解如何處理 ASP.NET Core 應用程式中的錯誤。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: e65983fb1a440057283111ea5a79a79b765607b7
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751687"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="06a76-103">處理 ASP.NET Core 中的錯誤</span><span class="sxs-lookup"><span data-stu-id="06a76-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="06a76-104">[Kirk Larkin](https://twitter.com/serpent5)、 [Tom Dykstra](https://github.com/tdykstra/)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="06a76-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="06a76-105">本文涵蓋 ASP.NET Core web 應用程式中處理錯誤的常見方法。</span><span class="sxs-lookup"><span data-stu-id="06a76-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="06a76-106">請參閱 <xref:web-api/handle-errors> ，以取得 Web api。</span><span class="sxs-lookup"><span data-stu-id="06a76-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="06a76-107">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="06a76-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="06a76-108"> ([下載方式](xref:index#how-to-download-a-sample)。在測試範例應用程式時，) F12 瀏覽器開發人員工具上的 [網路] 索引標籤很有用。</span><span class="sxs-lookup"><span data-stu-id="06a76-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="06a76-109">開發人員例外狀況頁面</span><span class="sxs-lookup"><span data-stu-id="06a76-109">Developer Exception Page</span></span>

<span data-ttu-id="06a76-110">「開發人員例外狀況頁面」會顯示要求例外狀況的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="06a76-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="06a76-111">ASP.NET Core 範本會產生下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="06a76-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="06a76-112">上述反白顯示的程式碼可在 [開發環境](xref:fundamentals/environments)中執行應用程式時，啟用開發人員例外狀況頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="06a76-113">範本 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> 會提早放置於中介軟體管線中，讓它可以攔截接下來中介軟體中擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="06a76-114">上述程式碼只會在應用程式于開發環境中執行時，**才** 啟用開發人員例外狀況頁面 \*。</span><span class="sxs-lookup"><span data-stu-id="06a76-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="06a76-115">當應用程式在生產環境中執行時，不應公開顯示詳細的例外狀況資訊。</span><span class="sxs-lookup"><span data-stu-id="06a76-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="06a76-116">如需設定環境的詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="06a76-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="06a76-117">開發人員例外狀況頁面包含下列有關例外狀況和要求的資訊：</span><span class="sxs-lookup"><span data-stu-id="06a76-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="06a76-118">_ 堆疊追蹤</span><span class="sxs-lookup"><span data-stu-id="06a76-118">_ Stack trace</span></span>
* <span data-ttu-id="06a76-119">查詢字串參數（如果有的話）</span><span class="sxs-lookup"><span data-stu-id="06a76-119">Query string parameters if any</span></span>
* <span data-ttu-id="06a76-120">Cookie如果有的話</span><span class="sxs-lookup"><span data-stu-id="06a76-120">Cookies if any</span></span>
* <span data-ttu-id="06a76-121">標題</span><span class="sxs-lookup"><span data-stu-id="06a76-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="06a76-122">例外處理常式頁面</span><span class="sxs-lookup"><span data-stu-id="06a76-122">Exception handler page</span></span>

<span data-ttu-id="06a76-123">若要針對 [生產環境](xref:fundamentals/environments)設定自訂錯誤處理頁面，請呼叫 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 。</span><span class="sxs-lookup"><span data-stu-id="06a76-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="06a76-124">此例外狀況處理中介軟體：</span><span class="sxs-lookup"><span data-stu-id="06a76-124">This exception handling middleware:</span></span>

* <span data-ttu-id="06a76-125">攔截並記錄例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="06a76-126">使用指定的路徑，在替代管線中重新執行要求。</span><span class="sxs-lookup"><span data-stu-id="06a76-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="06a76-127">如果回應已啟動，就不會重新執行要求。</span><span class="sxs-lookup"><span data-stu-id="06a76-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="06a76-128">範本產生的程式碼會使用路徑重新執行要求 `/Error` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="06a76-129">在下列範例中，會 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 在非開發環境中加入例外狀況處理中介軟體：</span><span class="sxs-lookup"><span data-stu-id="06a76-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="06a76-130">RazorPages 應用程式範本會 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> `ErrorModel` 在 [ *pages* ] 資料夾中提供錯誤頁面 () 和類別 () 。</span><span class="sxs-lookup"><span data-stu-id="06a76-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="06a76-131">在 MVC 應用程式中，專案範本包含 `Error` 動作方法和首頁控制器的錯誤視圖。</span><span class="sxs-lookup"><span data-stu-id="06a76-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="06a76-132">例外狀況處理中介軟體會使用 *原始* HTTP 方法重新執行要求。</span><span class="sxs-lookup"><span data-stu-id="06a76-132">The exception handling middleware re-executes the request using the *original* HTTP method.</span></span> <span data-ttu-id="06a76-133">如果錯誤處理常式端點受限於一組特定的 HTTP 方法，則只會針對這些 HTTP 方法執行。</span><span class="sxs-lookup"><span data-stu-id="06a76-133">If an error handler endpoint is restricted to a specific set of HTTP methods, it runs only for those HTTP methods.</span></span> <span data-ttu-id="06a76-134">例如，使用屬性的 MVC 控制器動作只會 `[HttpGet]` 執行 GET 要求。</span><span class="sxs-lookup"><span data-stu-id="06a76-134">For example, an MVC controller action that uses the `[HttpGet]` attribute runs only for GET requests.</span></span> <span data-ttu-id="06a76-135">為了確保 *所有要求都* 到達自訂錯誤處理頁面，請不要將它們限制為一組特定的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="06a76-135">To ensure that *all* requests reach the custom error handling page, don't restrict them to a specific set of HTTP methods.</span></span>

<span data-ttu-id="06a76-136">若要根據原始的 HTTP 方法，以不同的方式處理例外狀況：</span><span class="sxs-lookup"><span data-stu-id="06a76-136">To handle exceptions differently based on the original HTTP method:</span></span>

* <span data-ttu-id="06a76-137">若為 Razor 頁面，請建立多個處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="06a76-137">For Razor Pages, create multiple handler methods.</span></span> <span data-ttu-id="06a76-138">例如，使用 `OnGet` 來處理 GET 例外狀況，並使用 `OnPost` 來處理 POST 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-138">For example, use `OnGet` to handle GET exceptions and use `OnPost` to handle POST exceptions.</span></span>
* <span data-ttu-id="06a76-139">針對 MVC，將 HTTP 動詞屬性套用至多個動作。</span><span class="sxs-lookup"><span data-stu-id="06a76-139">For MVC, apply HTTP verb attributes to multiple actions.</span></span> <span data-ttu-id="06a76-140">例如，使用 `[HttpGet]` 來處理 GET 例外狀況，並使用 `[HttpPost]` 來處理 POST 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-140">For example, use `[HttpGet]` to handle GET exceptions and use `[HttpPost]` to handle POST exceptions.</span></span>

<span data-ttu-id="06a76-141">若要允許未經驗證的使用者查看自訂錯誤處理頁面，請確定它支援匿名存取。</span><span class="sxs-lookup"><span data-stu-id="06a76-141">To allow unauthenticated users to view the custom error handling page, ensure that it supports anonymous access.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="06a76-142">存取例外狀況</span><span class="sxs-lookup"><span data-stu-id="06a76-142">Access the exception</span></span>

<span data-ttu-id="06a76-143">用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 來存取例外狀況和錯誤處理常式中的原始要求路徑。</span><span class="sxs-lookup"><span data-stu-id="06a76-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="06a76-144">下列程式碼會加入 `ExceptionMessage` ASP.NET Core 範本所產生的預設 *Pages/Error. .cs* ：</span><span class="sxs-lookup"><span data-stu-id="06a76-144">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="06a76-145">請 **勿** 提供敏感性的錯誤資訊給用戶端。</span><span class="sxs-lookup"><span data-stu-id="06a76-145">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="06a76-146">提供錯誤有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="06a76-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="06a76-147">若要測試 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="06a76-147">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="06a76-148">將環境設定為生產環境。</span><span class="sxs-lookup"><span data-stu-id="06a76-148">Set the environment to production.</span></span>
* <span data-ttu-id="06a76-149">從 `webBuilder.UseStartup<Startup>();` *Program.cs* 中移除批註。</span><span class="sxs-lookup"><span data-stu-id="06a76-149">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="06a76-150">在首頁上選取 [ **觸發例外** 狀況]。</span><span class="sxs-lookup"><span data-stu-id="06a76-150">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="06a76-151">例外處理常式 Lambda</span><span class="sxs-lookup"><span data-stu-id="06a76-151">Exception handler lambda</span></span>

<span data-ttu-id="06a76-152">[自訂例外處理常式頁面](#exception-handler-page)的替代方法，便是將 Lambda 提供給 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>。</span><span class="sxs-lookup"><span data-stu-id="06a76-152">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="06a76-153">使用 lambda 可讓您在傳回回應之前存取錯誤。</span><span class="sxs-lookup"><span data-stu-id="06a76-153">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="06a76-154">下列程式碼會使用 lambda 來處理例外狀況：</span><span class="sxs-lookup"><span data-stu-id="06a76-154">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="06a76-155">請 **勿** 從 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 提供錯誤資訊給用戶端。</span><span class="sxs-lookup"><span data-stu-id="06a76-155">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="06a76-156">提供錯誤有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="06a76-156">Serving errors is a security risk.</span></span>

<span data-ttu-id="06a76-157">測試 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的例外狀況處理 lambda：</span><span class="sxs-lookup"><span data-stu-id="06a76-157">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="06a76-158">將環境設定為生產環境。</span><span class="sxs-lookup"><span data-stu-id="06a76-158">Set the environment to production.</span></span>
* <span data-ttu-id="06a76-159">從 `webBuilder.UseStartup<StartupLambda>();` *Program.cs* 中移除批註。</span><span class="sxs-lookup"><span data-stu-id="06a76-159">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="06a76-160">在首頁上選取 [ **觸發例外** 狀況]。</span><span class="sxs-lookup"><span data-stu-id="06a76-160">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="06a76-161">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="06a76-161">UseStatusCodePages</span></span>

<span data-ttu-id="06a76-162">根據預設，ASP.NET Core 的應用程式不會提供 HTTP 錯誤狀態碼（例如 *404-找不* 到）的狀態字碼頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-162">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="06a76-163">當應用程式遇到沒有主體的 HTTP 400-599 錯誤狀態碼時，會傳回狀態碼和空白的回應主體。</span><span class="sxs-lookup"><span data-stu-id="06a76-163">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="06a76-164">若要提供狀態字碼頁面，請使用狀態字碼頁面中介軟體。</span><span class="sxs-lookup"><span data-stu-id="06a76-164">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="06a76-165">若要針對常見的錯誤狀態碼啟用預設的純文字處理常式，請呼叫 `Startup.Configure` 方法中的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：</span><span class="sxs-lookup"><span data-stu-id="06a76-165">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="06a76-166">`UseStatusCodePages`要求處理中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="06a76-166">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="06a76-167">例如，在 `UseStatusCodePages` 靜態檔案中介軟體和端點中介軟體之前呼叫。</span><span class="sxs-lookup"><span data-stu-id="06a76-167">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="06a76-168">`UseStatusCodePages`未使用時，流覽至不含端點的 URL 會傳回瀏覽器相依的錯誤訊息，指出找不到端點。</span><span class="sxs-lookup"><span data-stu-id="06a76-168">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="06a76-169">例如，流覽至 `Home/Privacy2` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-169">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="06a76-170">當 `UseStatusCodePages` 呼叫時，瀏覽器會傳回：</span><span class="sxs-lookup"><span data-stu-id="06a76-170">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="06a76-171">`UseStatusCodePages` 通常不會在生產環境中使用，因為它會傳回不適合用戶的訊息。</span><span class="sxs-lookup"><span data-stu-id="06a76-171">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="06a76-172">若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試：</span><span class="sxs-lookup"><span data-stu-id="06a76-172">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="06a76-173">將環境設定為生產環境。</span><span class="sxs-lookup"><span data-stu-id="06a76-173">Set the environment to production.</span></span>
* <span data-ttu-id="06a76-174">從 `webBuilder.UseStartup<StartupUseStatusCodePages>();` *Program.cs* 中移除批註。</span><span class="sxs-lookup"><span data-stu-id="06a76-174">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="06a76-175">選取首頁首頁上的連結。</span><span class="sxs-lookup"><span data-stu-id="06a76-175">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="06a76-176">狀態字碼頁面中介軟體 **不** 會攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-176">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="06a76-177">若要提供自訂錯誤處理頁面，請使用 [ [例外狀況處理常式] 頁面](#exception-handler-page)。</span><span class="sxs-lookup"><span data-stu-id="06a76-177">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="06a76-178">具格式字串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="06a76-178">UseStatusCodePages with format string</span></span>

<span data-ttu-id="06a76-179">若要自訂回應內容類型和文字，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用內容類型和格式字串：</span><span class="sxs-lookup"><span data-stu-id="06a76-179">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="06a76-180">在上述程式碼中， `{0}` 是錯誤碼的預留位置。</span><span class="sxs-lookup"><span data-stu-id="06a76-180">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="06a76-181">`UseStatusCodePages` 使用格式字串時，通常不會在生產環境中使用，因為它會傳回不適合用戶的訊息。</span><span class="sxs-lookup"><span data-stu-id="06a76-181">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="06a76-182">若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試，請 `webBuilder.UseStartup<StartupFormat>();` 從 *Program.cs* 中移除批註。</span><span class="sxs-lookup"><span data-stu-id="06a76-182">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="06a76-183">具 Lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="06a76-183">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="06a76-184">若要指定自訂錯誤處理和回應撰寫程式碼，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用 Lambda 運算式：</span><span class="sxs-lookup"><span data-stu-id="06a76-184">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="06a76-185">`UseStatusCodePages` 使用 lambda 時，通常不會在生產環境中使用，因為它會傳回不適合用戶的訊息。</span><span class="sxs-lookup"><span data-stu-id="06a76-185">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="06a76-186">若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試，請 `webBuilder.UseStartup<StartupStatusLambda>();` 從 *Program.cs* 中移除批註。</span><span class="sxs-lookup"><span data-stu-id="06a76-186">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="06a76-187">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="06a76-187">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="06a76-188"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="06a76-188">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="06a76-189">傳送「302 - 已找到」[](https://developer.mozilla.org/docs/Web/HTTP/Status/302)狀態碼傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="06a76-189">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="06a76-190">將用戶端重新導向至 URL 範本中提供的錯誤處理端點。</span><span class="sxs-lookup"><span data-stu-id="06a76-190">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="06a76-191">錯誤處理端點通常會顯示錯誤資訊，並傳回 HTTP 200。</span><span class="sxs-lookup"><span data-stu-id="06a76-191">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="06a76-192">URL 範本可以包含 `{0}` 狀態碼的預留位置，如上述程式碼所示。</span><span class="sxs-lookup"><span data-stu-id="06a76-192">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="06a76-193">如果 URL 範本的開頭是 (波狀符號 `~`) ， `~` 會由應用程式取代 `PathBase` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-193">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="06a76-194">在應用程式中指定端點時，請建立端點的 MVC 視圖或 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-194">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="06a76-195">如需 Razor 頁面範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的[pages/MyStatusCode。](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)</span><span class="sxs-lookup"><span data-stu-id="06a76-195">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="06a76-196">此方法通常是在下列應用程式相關情況下使用：</span><span class="sxs-lookup"><span data-stu-id="06a76-196">This method is commonly used when the app:</span></span>

* <span data-ttu-id="06a76-197">應用程式應將用戶端重新導向至不同的端點時 (通常是在由其他應用程式處理錯誤的情況下)。</span><span class="sxs-lookup"><span data-stu-id="06a76-197">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="06a76-198">針對 Web 應用程式，用戶端的瀏覽器網址列會反映重新導向後的端點。</span><span class="sxs-lookup"><span data-stu-id="06a76-198">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="06a76-199">應用程式不應該保留並傳回原始狀態碼與初始重新導向回應時。</span><span class="sxs-lookup"><span data-stu-id="06a76-199">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="06a76-200">若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試，請 `webBuilder.UseStartup<StartupSCredirect>();` 從 *Program.cs* 中移除批註。</span><span class="sxs-lookup"><span data-stu-id="06a76-200">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="06a76-201">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="06a76-201">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="06a76-202"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="06a76-202">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="06a76-203">傳回原始狀態碼給用戶端。</span><span class="sxs-lookup"><span data-stu-id="06a76-203">Returns the original status code to the client.</span></span>
* <span data-ttu-id="06a76-204">可透過使用替代路徑重新執行要求管線來產生回應本文。</span><span class="sxs-lookup"><span data-stu-id="06a76-204">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="06a76-205">如果已指定應用程式內的端點，請建立端點的 MVC 視圖或 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-205">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="06a76-206">請確定先放置，才能將 `UseStatusCodePagesWithReExecute` `UseRouting` 要求重新路由至狀態頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-206">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="06a76-207">如需 Razor 頁面範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的[pages/MyStatusCode2。](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)</span><span class="sxs-lookup"><span data-stu-id="06a76-207">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="06a76-208">此方法通常是在下列應用程式相關情況下使用：</span><span class="sxs-lookup"><span data-stu-id="06a76-208">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="06a76-209">在不重新導向至其他端點的情況下處理要求。</span><span class="sxs-lookup"><span data-stu-id="06a76-209">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="06a76-210">針對 Web 應用程式，用戶端的瀏覽器網址列會反映原始要求的端點。</span><span class="sxs-lookup"><span data-stu-id="06a76-210">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="06a76-211">保留並傳回原始狀態碼與回應時。</span><span class="sxs-lookup"><span data-stu-id="06a76-211">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="06a76-212">URL 和查詢字串範本可能包含 `{0}` 狀態碼的預留位置。</span><span class="sxs-lookup"><span data-stu-id="06a76-212">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="06a76-213">URL 範本的開頭必須是 `/` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-213">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="06a76-214">處理錯誤的端點可以取得產生該錯誤的原始 URL，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="06a76-214">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="06a76-215">如需 Razor 頁面範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的[pages/MyStatusCode2。](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)</span><span class="sxs-lookup"><span data-stu-id="06a76-215">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="06a76-216">若要 `UseStatusCodePages` 在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中進行測試，請 `webBuilder.UseStartup<StartupSCreX>();` 從 *Program.cs* 中移除批註。</span><span class="sxs-lookup"><span data-stu-id="06a76-216">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="06a76-217">停用狀態碼頁面</span><span class="sxs-lookup"><span data-stu-id="06a76-217">Disable status code pages</span></span>

<span data-ttu-id="06a76-218">若要停用 MVC 控制器或動作方法的狀態碼頁面，請使用 [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="06a76-218">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="06a76-219">若要在 Razor 頁面處理常式方法或 MVC 控制器中停用特定要求的狀態字碼頁面，請使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> ：</span><span class="sxs-lookup"><span data-stu-id="06a76-219">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="06a76-220">例外狀況處理程式碼</span><span class="sxs-lookup"><span data-stu-id="06a76-220">Exception-handling code</span></span>

<span data-ttu-id="06a76-221">例外狀況處理頁面中的程式碼也可能會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-221">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="06a76-222">生產錯誤頁面應該經過徹底的測試，並特別小心避免擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-222">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="06a76-223">回應標頭</span><span class="sxs-lookup"><span data-stu-id="06a76-223">Response headers</span></span>

<span data-ttu-id="06a76-224">一旦傳送回應的標頭之後：</span><span class="sxs-lookup"><span data-stu-id="06a76-224">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="06a76-225">應用程式就無法變更回應的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="06a76-225">The app can't change the response's status code.</span></span>
* <span data-ttu-id="06a76-226">無法執行任何例外狀況頁面或處理常式。</span><span class="sxs-lookup"><span data-stu-id="06a76-226">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="06a76-227">回應必須完成，否則會中止連線。</span><span class="sxs-lookup"><span data-stu-id="06a76-227">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="06a76-228">伺服器例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="06a76-228">Server exception handling</span></span>

<span data-ttu-id="06a76-229">除了應用程式中的例外狀況處理邏輯之外， [HTTP 伺服器的執行](xref:fundamentals/servers/index) 也可以處理一些例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-229">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="06a76-230">如果伺服器在回應標頭傳送之前攔截到例外狀況，伺服器會傳送 `500 - Internal Server Error` 回應，而不會有回應主體。</span><span class="sxs-lookup"><span data-stu-id="06a76-230">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="06a76-231">如果伺服器在回應標頭傳送之後攔截到例外狀況，伺服器會關閉連線。</span><span class="sxs-lookup"><span data-stu-id="06a76-231">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="06a76-232">應用程式未處理的要求會由伺服器處理。</span><span class="sxs-lookup"><span data-stu-id="06a76-232">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="06a76-233">當伺服器處理要求時，任何發生的例外狀況均由伺服器的例外狀況處理功能來處理。</span><span class="sxs-lookup"><span data-stu-id="06a76-233">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="06a76-234">應用程式的自訂錯誤頁面、例外狀況處理中介軟體或篩選條件並不會影響此行為。</span><span class="sxs-lookup"><span data-stu-id="06a76-234">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="06a76-235">啟動例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="06a76-235">Startup exception handling</span></span>

<span data-ttu-id="06a76-236">只有裝載層可以處理應用程式啟動期間發生的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-236">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="06a76-237">可以將主機設定為會[擷取啟動錯誤](xref:fundamentals/host/web-host#capture-startup-errors)和[擷取詳細錯誤](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="06a76-237">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="06a76-238">只有在錯誤是於主機位址/連接埠繫結之後發生的情況下，裝載層才能顯示已擷取之啟動錯誤的錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-238">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="06a76-239">如果繫結失敗：</span><span class="sxs-lookup"><span data-stu-id="06a76-239">If binding fails:</span></span>

* <span data-ttu-id="06a76-240">裝載層會記錄重大例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-240">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="06a76-241">Dotnet 會處理損毀狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-241">The dotnet process crashes.</span></span>
* <span data-ttu-id="06a76-242">當 HTTP 伺服器是 [Kestrel](xref:fundamentals/servers/kestrel) 時，不會顯示任何錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-242">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="06a76-243">在 [IIS](/iis) (或 Azure App Service) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上執行時，如果無法啟動處理序，[模組](xref:host-and-deploy/aspnet-core-module)會傳回 *502.5 - 處理序失敗*。</span><span class="sxs-lookup"><span data-stu-id="06a76-243">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="06a76-244">如需詳細資訊，請參閱<xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="06a76-244">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="06a76-245">資料庫錯誤頁面</span><span class="sxs-lookup"><span data-stu-id="06a76-245">Database error page</span></span>

<span data-ttu-id="06a76-246">資料庫開發人員頁面例外狀況篩選 `AddDatabaseDeveloperPageExceptionFilter` 會捕捉與資料庫相關的例外狀況，這些例外狀況可以使用 Entity Framework Core 遷移來解決。</span><span class="sxs-lookup"><span data-stu-id="06a76-246">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="06a76-247">當這些例外狀況發生時，會產生 HTML 回應，並提供解決問題的可能動作詳細資料。</span><span class="sxs-lookup"><span data-stu-id="06a76-247">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="06a76-248">此頁面只會在開發環境中啟用。</span><span class="sxs-lookup"><span data-stu-id="06a76-248">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="06a76-249">Razor指定個別使用者帳戶時，ASP.NET Core Pages 範本會產生下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="06a76-249">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="06a76-250">例外狀況篩選條件</span><span class="sxs-lookup"><span data-stu-id="06a76-250">Exception filters</span></span>

<span data-ttu-id="06a76-251">在 MVC 應用程式中，您能以全域設定例外狀況篩選條件，或是以每個控制器或每個動作為基礎的方式設定。</span><span class="sxs-lookup"><span data-stu-id="06a76-251">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="06a76-252">在 Razor 頁面應用程式中，可以設定全域或依頁面的模型。</span><span class="sxs-lookup"><span data-stu-id="06a76-252">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="06a76-253">這些篩選器會處理在控制器動作或其他篩選準則執行期間發生的任何未處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-253">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="06a76-254">如需詳細資訊，請參閱<xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="06a76-254">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="06a76-255">例外狀況篩選準則適用于捕捉 MVC 動作內發生的例外狀況，但是它們並不像內建的 [例外狀況處理中介軟體](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)那麼有彈性 `UseExceptionHandler` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-255">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="06a76-256">`UseExceptionHandler`除非您需要根據選擇的 MVC 動作，以不同的方式執行錯誤處理，否則建議使用。</span><span class="sxs-lookup"><span data-stu-id="06a76-256">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="06a76-257">模型狀態錯誤</span><span class="sxs-lookup"><span data-stu-id="06a76-257">Model state errors</span></span>

<span data-ttu-id="06a76-258">如需如何處理模型狀態錯誤的相關資訊，請參閱[模型繫結](xref:mvc/models/model-binding)和[模型驗證](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="06a76-258">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06a76-259">其他資源</span><span class="sxs-lookup"><span data-stu-id="06a76-259">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="06a76-260">由  [Tom Dykstra](https://github.com/tdykstra/)和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="06a76-260">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="06a76-261">本文涵蓋 ASP.NET Core web 應用程式中處理錯誤的常見方法。</span><span class="sxs-lookup"><span data-stu-id="06a76-261">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="06a76-262">請參閱 <xref:web-api/handle-errors> ，以取得 Web api。</span><span class="sxs-lookup"><span data-stu-id="06a76-262">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="06a76-263">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="06a76-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="06a76-264"> ([如何下載](xref:index#how-to-download-a-sample)。 ) </span><span class="sxs-lookup"><span data-stu-id="06a76-264">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="06a76-265">開發人員例外狀況頁面</span><span class="sxs-lookup"><span data-stu-id="06a76-265">Developer Exception Page</span></span>

<span data-ttu-id="06a76-266">「開發人員例外狀況頁面」會顯示要求例外狀況的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="06a76-266">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="06a76-267">ASP.NET Core 範本會產生下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="06a76-267">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="06a76-268">當應用程式在 [開發環境](xref:fundamentals/environments)中執行時，上述程式碼會啟用開發人員例外狀況頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-268">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="06a76-269">範本會在 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> 任何中介軟體之前放置，因此例外狀況會在接下來的中介軟體中捕捉。</span><span class="sxs-lookup"><span data-stu-id="06a76-269">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="06a76-270">上述 **程式碼只會在應用程式于開發環境中執行時，才會** 啟用開發人員例外狀況頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-270">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="06a76-271">當應用程式在生產環境中執行時，不應公開顯示詳細的例外狀況資訊。</span><span class="sxs-lookup"><span data-stu-id="06a76-271">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="06a76-272">如需設定環境的詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="06a76-272">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="06a76-273">開發人員例外狀況頁面包含下列有關例外狀況和要求的資訊：</span><span class="sxs-lookup"><span data-stu-id="06a76-273">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="06a76-274">堆疊追蹤</span><span class="sxs-lookup"><span data-stu-id="06a76-274">Stack trace</span></span>
* <span data-ttu-id="06a76-275">查詢字串參數（如果有的話）</span><span class="sxs-lookup"><span data-stu-id="06a76-275">Query string parameters if any</span></span>
* <span data-ttu-id="06a76-276">Cookie如果有的話</span><span class="sxs-lookup"><span data-stu-id="06a76-276">Cookies if any</span></span>
* <span data-ttu-id="06a76-277">標題</span><span class="sxs-lookup"><span data-stu-id="06a76-277">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="06a76-278">例外處理常式頁面</span><span class="sxs-lookup"><span data-stu-id="06a76-278">Exception handler page</span></span>

<span data-ttu-id="06a76-279">若要針對生產環境設定自訂錯誤處理頁面，請使用例外狀況處理中介軟體。</span><span class="sxs-lookup"><span data-stu-id="06a76-279">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="06a76-280">中介軟體：</span><span class="sxs-lookup"><span data-stu-id="06a76-280">The middleware:</span></span>

* <span data-ttu-id="06a76-281">攔截並記錄例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-281">Catches and logs exceptions.</span></span>
* <span data-ttu-id="06a76-282">可在所指示頁面或控制器的替代管線中重新執行要求。</span><span class="sxs-lookup"><span data-stu-id="06a76-282">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="06a76-283">如果回應已啟動，就不會重新執行要求。</span><span class="sxs-lookup"><span data-stu-id="06a76-283">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="06a76-284">範本產生的程式碼會重新執行要求的 `/Error` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-284">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="06a76-285">在下列範例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 會在非開發環境中加入例外狀況處理中介軟體：</span><span class="sxs-lookup"><span data-stu-id="06a76-285">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="06a76-286">RazorPages 應用程式範本會 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> `ErrorModel` 在 [ *pages* ] 資料夾中提供錯誤頁面 () 和類別 () 。</span><span class="sxs-lookup"><span data-stu-id="06a76-286">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="06a76-287">在 MVC 應用程式中，專案範本會在 Home 控制器中包含錯誤動作方法和錯誤視圖。</span><span class="sxs-lookup"><span data-stu-id="06a76-287">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="06a76-288">請勿將錯誤處理常式動作方法標記為 HTTP 方法屬性，例如 `HttpGet` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-288">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="06a76-289">明確的動詞命令可防止某些要求取得方法。</span><span class="sxs-lookup"><span data-stu-id="06a76-289">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="06a76-290">如果未經驗證的使用者應該會看到錯誤視圖，則允許匿名存取方法。</span><span class="sxs-lookup"><span data-stu-id="06a76-290">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="06a76-291">存取例外狀況</span><span class="sxs-lookup"><span data-stu-id="06a76-291">Access the exception</span></span>

<span data-ttu-id="06a76-292">使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 來存取例外狀況或和錯誤處理常式控制器或頁面中的原始要求路徑：</span><span class="sxs-lookup"><span data-stu-id="06a76-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="06a76-293">請 **勿** 提供敏感性的錯誤資訊給用戶端。</span><span class="sxs-lookup"><span data-stu-id="06a76-293">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="06a76-294">提供錯誤有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="06a76-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="06a76-295">若要觸發先前的例外狀況處理頁面，請將環境設定為生產環境，並強制執行例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-295">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="06a76-296">例外處理常式 Lambda</span><span class="sxs-lookup"><span data-stu-id="06a76-296">Exception handler lambda</span></span>

<span data-ttu-id="06a76-297">[自訂例外處理常式頁面](#exception-handler-page)的替代方法，便是將 Lambda 提供給 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>。</span><span class="sxs-lookup"><span data-stu-id="06a76-297">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="06a76-298">使用 lambda 可讓您在傳回回應之前存取錯誤。</span><span class="sxs-lookup"><span data-stu-id="06a76-298">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="06a76-299">以下是將 Lambda 用於例外狀況處理的範例：</span><span class="sxs-lookup"><span data-stu-id="06a76-299">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="06a76-300">在上述程式碼中， `await context.Response.WriteAsync(new string(' ', 512));` 會加入，因此 Internet Explorer 瀏覽器會顯示錯誤訊息，而不是 IE 錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="06a76-300">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="06a76-301">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/16144)。</span><span class="sxs-lookup"><span data-stu-id="06a76-301">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="06a76-302">請 **勿** 從 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 提供錯誤資訊給用戶端。</span><span class="sxs-lookup"><span data-stu-id="06a76-302">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="06a76-303">提供錯誤有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="06a76-303">Serving errors is a security risk.</span></span>

<span data-ttu-id="06a76-304">若要在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) \(英文\) 中查看例外狀況處理 Lambda 的結果，請使用 `ProdEnvironment` 和 `ErrorHandlerLambda` 前置處理器指示詞，並選取首頁上的 [觸發例外狀況]。</span><span class="sxs-lookup"><span data-stu-id="06a76-304">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="06a76-305">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="06a76-305">UseStatusCodePages</span></span>

<span data-ttu-id="06a76-306">根據預設，ASP.NET Core 應用程式不會提供 HTTP 狀態碼 (例如「404 - 找不到」) 等狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-306">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="06a76-307">應用程式會傳回狀態碼和空白回應主體。</span><span class="sxs-lookup"><span data-stu-id="06a76-307">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="06a76-308">若要提供狀態碼頁面，請使用狀態碼頁面中介軟體。</span><span class="sxs-lookup"><span data-stu-id="06a76-308">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="06a76-309">中介軟體是由 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 套件提供。</span><span class="sxs-lookup"><span data-stu-id="06a76-309">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="06a76-310">若要針對常見的錯誤狀態碼啟用預設的純文字處理常式，請呼叫 `Startup.Configure` 方法中的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：</span><span class="sxs-lookup"><span data-stu-id="06a76-310">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="06a76-311">要求處理中介軟體 (例如靜態檔案中介軟體和 MVC 中介軟體) 之前，應該先呼叫 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="06a76-311">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="06a76-312">`UseStatusCodePages`未使用時，流覽至不含端點的 URL 會傳回瀏覽器相依的錯誤訊息，指出找不到端點。</span><span class="sxs-lookup"><span data-stu-id="06a76-312">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="06a76-313">例如，流覽至 `Home/Privacy2` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-313">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="06a76-314">當 `UseStatusCodePages` 呼叫時，瀏覽器會傳回：</span><span class="sxs-lookup"><span data-stu-id="06a76-314">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="06a76-315">具格式字串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="06a76-315">UseStatusCodePages with format string</span></span>

<span data-ttu-id="06a76-316">若要自訂回應內容類型和文字，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用內容類型和格式字串：</span><span class="sxs-lookup"><span data-stu-id="06a76-316">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="06a76-317">具 Lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="06a76-317">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="06a76-318">若要指定自訂錯誤處理和回應撰寫程式碼，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用 Lambda 運算式：</span><span class="sxs-lookup"><span data-stu-id="06a76-318">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="06a76-319">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="06a76-319">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="06a76-320"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="06a76-320">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="06a76-321">傳送「302 - 已找到」狀態碼傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="06a76-321">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="06a76-322">將用戶端重新導向到 URL 範本中提供的位置。</span><span class="sxs-lookup"><span data-stu-id="06a76-322">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="06a76-323">URL 範本可以針對狀態碼包含 `{0}` 預留位置，如範例所示。</span><span class="sxs-lookup"><span data-stu-id="06a76-323">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="06a76-324">如果 URL 範本的開頭是 (波狀符號 `~`) ， `~` 會由應用程式取代 `PathBase` 。</span><span class="sxs-lookup"><span data-stu-id="06a76-324">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="06a76-325">如果您指向應用程式內的端點，請建立端點的 MVC 視圖或 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-325">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="06a76-326">如需 Razor 頁面範例，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 *pages/StatusCode. cshtml。*</span><span class="sxs-lookup"><span data-stu-id="06a76-326">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="06a76-327">此方法通常是在下列應用程式相關情況下使用：</span><span class="sxs-lookup"><span data-stu-id="06a76-327">This method is commonly used when the app:</span></span>

* <span data-ttu-id="06a76-328">應用程式應將用戶端重新導向至不同的端點時 (通常是在由其他應用程式處理錯誤的情況下)。</span><span class="sxs-lookup"><span data-stu-id="06a76-328">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="06a76-329">針對 Web 應用程式，用戶端的瀏覽器網址列會反映重新導向後的端點。</span><span class="sxs-lookup"><span data-stu-id="06a76-329">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="06a76-330">應用程式不應該保留並傳回原始狀態碼與初始重新導向回應時。</span><span class="sxs-lookup"><span data-stu-id="06a76-330">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="06a76-331">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="06a76-331">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="06a76-332"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="06a76-332">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="06a76-333">傳回原始狀態碼給用戶端。</span><span class="sxs-lookup"><span data-stu-id="06a76-333">Returns the original status code to the client.</span></span>
* <span data-ttu-id="06a76-334">可透過使用替代路徑重新執行要求管線來產生回應本文。</span><span class="sxs-lookup"><span data-stu-id="06a76-334">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="06a76-335">如果您指向應用程式內的端點，請建立端點的 MVC 視圖或 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-335">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="06a76-336">請確定先放置，才能將 `UseStatusCodePagesWithReExecute` `UseRouting` 要求重新路由至狀態頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-336">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="06a76-337">如需 Razor 頁面範例，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 *pages/StatusCode. cshtml。*</span><span class="sxs-lookup"><span data-stu-id="06a76-337">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="06a76-338">此方法通常是在下列應用程式相關情況下使用：</span><span class="sxs-lookup"><span data-stu-id="06a76-338">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="06a76-339">在不重新導向至其他端點的情況下處理要求。</span><span class="sxs-lookup"><span data-stu-id="06a76-339">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="06a76-340">針對 Web 應用程式，用戶端的瀏覽器網址列會反映原始要求的端點。</span><span class="sxs-lookup"><span data-stu-id="06a76-340">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="06a76-341">保留並傳回原始狀態碼與回應時。</span><span class="sxs-lookup"><span data-stu-id="06a76-341">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="06a76-342">URL 和查詢字串範本可能會包含該狀態碼的預留位置 (`{0}`)。</span><span class="sxs-lookup"><span data-stu-id="06a76-342">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="06a76-343">URL 範本的開頭必須是斜線 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="06a76-343">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="06a76-344">在路徑中使用預留位置時，請確認端點 (頁面或控制器) 可以處理路徑線段。</span><span class="sxs-lookup"><span data-stu-id="06a76-344">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="06a76-345">例如，錯誤的 Razor 頁面應接受具有指示詞的選擇性路徑區段值 `@page` ：</span><span class="sxs-lookup"><span data-stu-id="06a76-345">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="06a76-346">處理錯誤的端點可以取得產生該錯誤的原始 URL，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="06a76-346">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="06a76-347">停用狀態碼頁面</span><span class="sxs-lookup"><span data-stu-id="06a76-347">Disable status code pages</span></span>

<span data-ttu-id="06a76-348">若要停用 MVC 控制器或動作方法的狀態字碼頁面，請使用 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="06a76-348">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="06a76-349">若要在 Razor 頁面處理常式方法或 MVC 控制器中停用特定要求的狀態字碼頁面，請使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> ：</span><span class="sxs-lookup"><span data-stu-id="06a76-349">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="06a76-350">例外狀況處理程式碼</span><span class="sxs-lookup"><span data-stu-id="06a76-350">Exception-handling code</span></span>

<span data-ttu-id="06a76-351">例外狀況處理頁面中的程式碼，可擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-351">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="06a76-352">一般來說，較好的做法是讓生產環境的錯誤頁面由純靜態內容組成。</span><span class="sxs-lookup"><span data-stu-id="06a76-352">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="06a76-353">回應標頭</span><span class="sxs-lookup"><span data-stu-id="06a76-353">Response headers</span></span>

<span data-ttu-id="06a76-354">一旦傳送回應的標頭之後：</span><span class="sxs-lookup"><span data-stu-id="06a76-354">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="06a76-355">應用程式就無法變更回應的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="06a76-355">The app can't change the response's status code.</span></span>
* <span data-ttu-id="06a76-356">無法執行任何例外狀況頁面或處理常式。</span><span class="sxs-lookup"><span data-stu-id="06a76-356">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="06a76-357">回應必須完成，否則會中止連線。</span><span class="sxs-lookup"><span data-stu-id="06a76-357">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="06a76-358">伺服器例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="06a76-358">Server exception handling</span></span>

<span data-ttu-id="06a76-359">除了應用程式中的例外狀況處理邏輯之外，[HTTP 伺服器實作](xref:fundamentals/servers/index)也可以處理一些例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-359">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="06a76-360">如果伺服器在回應標頭傳送之前攔截到例外狀況，伺服器會傳送「500 - 內部伺服器錯誤」回應，且沒有回應本文。</span><span class="sxs-lookup"><span data-stu-id="06a76-360">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="06a76-361">如果伺服器在回應標頭傳送之後攔截到例外狀況，伺服器會關閉連線。</span><span class="sxs-lookup"><span data-stu-id="06a76-361">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="06a76-362">應用程式未處理的要求會由伺服器來處理。</span><span class="sxs-lookup"><span data-stu-id="06a76-362">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="06a76-363">當伺服器處理要求時，任何發生的例外狀況均由伺服器的例外狀況處理功能來處理。</span><span class="sxs-lookup"><span data-stu-id="06a76-363">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="06a76-364">應用程式的自訂錯誤頁面、例外狀況處理中介軟體或篩選條件並不會影響此行為。</span><span class="sxs-lookup"><span data-stu-id="06a76-364">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="06a76-365">啟動例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="06a76-365">Startup exception handling</span></span>

<span data-ttu-id="06a76-366">只有裝載層可以處理應用程式啟動期間發生的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-366">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="06a76-367">可以將主機設定為會[擷取啟動錯誤](xref:fundamentals/host/web-host#capture-startup-errors)和[擷取詳細錯誤](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="06a76-367">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="06a76-368">只有在錯誤是於主機位址/連接埠繫結之後發生的情況下，裝載層才能顯示已擷取之啟動錯誤的錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-368">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="06a76-369">如果繫結失敗：</span><span class="sxs-lookup"><span data-stu-id="06a76-369">If binding fails:</span></span>

* <span data-ttu-id="06a76-370">裝載層會記錄重大例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-370">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="06a76-371">Dotnet 會處理損毀狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-371">The dotnet process crashes.</span></span>
* <span data-ttu-id="06a76-372">當 HTTP 伺服器是 [Kestrel](xref:fundamentals/servers/kestrel) 時，不會顯示任何錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="06a76-372">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="06a76-373">在 [IIS](/iis) (或 Azure App Service) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上執行時，如果無法啟動處理序，[模組](xref:host-and-deploy/aspnet-core-module)會傳回 *502.5 - 處理序失敗*。</span><span class="sxs-lookup"><span data-stu-id="06a76-373">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="06a76-374">如需詳細資訊，請參閱<xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="06a76-374">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="06a76-375">資料庫錯誤頁面</span><span class="sxs-lookup"><span data-stu-id="06a76-375">Database error page</span></span>

<span data-ttu-id="06a76-376">資料庫錯誤頁面中介軟體會捕捉資料庫相關的例外狀況，這些例外狀況可使用 Entity Framework 遷移來解決。</span><span class="sxs-lookup"><span data-stu-id="06a76-376">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="06a76-377">發生這些例外狀況時，系統會產生具解決該問題之可能動作詳細資料的 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="06a76-377">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="06a76-378">此頁面僅應該於開發環境中啟用。</span><span class="sxs-lookup"><span data-stu-id="06a76-378">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="06a76-379">將程式碼加入 `Startup.Configure` 來啟用該頁面：</span><span class="sxs-lookup"><span data-stu-id="06a76-379">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="06a76-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> 需要 [AspNetCore Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="06a76-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="06a76-381">例外狀況篩選條件</span><span class="sxs-lookup"><span data-stu-id="06a76-381">Exception filters</span></span>

<span data-ttu-id="06a76-382">在 MVC 應用程式中，您能以全域設定例外狀況篩選條件，或是以每個控制器或每個動作為基礎的方式設定。</span><span class="sxs-lookup"><span data-stu-id="06a76-382">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="06a76-383">在 Razor 頁面應用程式中，可以設定全域或依頁面的模型。</span><span class="sxs-lookup"><span data-stu-id="06a76-383">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="06a76-384">這些篩選會處理在控制器動作或其他篩選條件執行期間發生但的任何未處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="06a76-384">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="06a76-385">如需詳細資訊，請參閱<xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="06a76-385">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="06a76-386">例外狀況篩選條件適合用來截獲 MVC 動作中發生的例外狀況，但是它們並不像例外狀況處理中介軟體那麼有彈性。</span><span class="sxs-lookup"><span data-stu-id="06a76-386">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="06a76-387">我們建議使用中介軟體。</span><span class="sxs-lookup"><span data-stu-id="06a76-387">We recommend using the middleware.</span></span> <span data-ttu-id="06a76-388">請只在需要根據已選擇的 MVC 動作執行不同的錯誤處理時，才使用篩選條件。</span><span class="sxs-lookup"><span data-stu-id="06a76-388">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="06a76-389">模型狀態錯誤</span><span class="sxs-lookup"><span data-stu-id="06a76-389">Model state errors</span></span>

<span data-ttu-id="06a76-390">如需如何處理模型狀態錯誤的相關資訊，請參閱[模型繫結](xref:mvc/models/model-binding)和[模型驗證](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="06a76-390">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06a76-391">其他資源</span><span class="sxs-lookup"><span data-stu-id="06a76-391">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
