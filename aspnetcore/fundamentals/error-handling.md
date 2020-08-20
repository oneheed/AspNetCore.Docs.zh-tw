---
title: 處理 ASP.NET Core 中的錯誤
author: rick-anderson
description: 了解如何處理 ASP.NET Core 應用程式中的錯誤。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: a1f40bdcdd4f2472aa86b311bfd9302e6aa8adc0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635095"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="03bae-103">處理 ASP.NET Core 中的錯誤</span><span class="sxs-lookup"><span data-stu-id="03bae-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="03bae-104">作者：[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="03bae-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="03bae-105">本文涵蓋 ASP.NET Core web 應用程式中處理錯誤的常見方法。</span><span class="sxs-lookup"><span data-stu-id="03bae-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="03bae-106">請參閱 <xref:web-api/handle-errors> ，以取得 Web api。</span><span class="sxs-lookup"><span data-stu-id="03bae-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="03bae-107">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="03bae-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="03bae-108"> ([如何下載](xref:index#how-to-download-a-sample)) 。本文包含如何在範例應用程式中將預處理器指示詞設定 (`#if` 、 `#endif` `#define`) ，以啟用不同案例的指示。</span><span class="sxs-lookup"><span data-stu-id="03bae-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="03bae-109">開發人員例外狀況頁面</span><span class="sxs-lookup"><span data-stu-id="03bae-109">Developer Exception Page</span></span>

<span data-ttu-id="03bae-110">「開發人員例外狀況頁面」\*\* 會顯示要求例外狀況的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="03bae-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="03bae-111">此頁面由元件提供，此 `Microsoft.AspNetCore.Diagnostics` 元件位於[ `Microsoft.AspNetCore.App` 共用架構](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="03bae-111">The page is made available by the `Microsoft.AspNetCore.Diagnostics` assembly, which is in the [`Microsoft.AspNetCore.App` shared framework](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="03bae-112">當應用程式在開發[環境](xref:fundamentals/environments)中執行時，新增程式碼到 `Startup.Configure` 方法以啟用頁面：</span><span class="sxs-lookup"><span data-stu-id="03bae-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="03bae-113">將對 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> 的呼叫放置於任何您想要攔截例外狀況的中介軟體之前。</span><span class="sxs-lookup"><span data-stu-id="03bae-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="03bae-114">**只有當應用程式在開發環境中執行時，才**啟用開發人員例外狀況頁面。</span><span class="sxs-lookup"><span data-stu-id="03bae-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="03bae-115">當應用程式在生產環境中執行時，您不會想要公開共用例外狀況的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="03bae-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="03bae-116">如需設定環境的詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="03bae-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="03bae-117">此頁面包含下列和例外狀況與要求有關的資訊：</span><span class="sxs-lookup"><span data-stu-id="03bae-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="03bae-118">堆疊追蹤</span><span class="sxs-lookup"><span data-stu-id="03bae-118">Stack trace</span></span>
* <span data-ttu-id="03bae-119">查詢字串參數 (如果有的話)</span><span class="sxs-lookup"><span data-stu-id="03bae-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="03bae-120">Cookie (是否有任何) </span><span class="sxs-lookup"><span data-stu-id="03bae-120">Cookies (if any)</span></span>
* <span data-ttu-id="03bae-121">標題</span><span class="sxs-lookup"><span data-stu-id="03bae-121">Headers</span></span>

<span data-ttu-id="03bae-122">若要在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) \(英文\) 中查看開發人員例外狀況頁面，請使用 `DevEnvironment` 前置處理器指示詞，並選取首頁上的 [觸發例外狀況]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="03bae-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="03bae-123">例外處理常式頁面</span><span class="sxs-lookup"><span data-stu-id="03bae-123">Exception handler page</span></span>

<span data-ttu-id="03bae-124">若要針對生產環境設定自訂錯誤處理頁面，請使用例外狀況處理中介軟體。</span><span class="sxs-lookup"><span data-stu-id="03bae-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="03bae-125">中介軟體：</span><span class="sxs-lookup"><span data-stu-id="03bae-125">The middleware:</span></span>

* <span data-ttu-id="03bae-126">攔截並記錄例外狀況。</span><span class="sxs-lookup"><span data-stu-id="03bae-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="03bae-127">可在所指示頁面或控制器的替代管線中重新執行要求。</span><span class="sxs-lookup"><span data-stu-id="03bae-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="03bae-128">如果回應已啟動，就不會重新執行要求。</span><span class="sxs-lookup"><span data-stu-id="03bae-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="03bae-129">在下列範例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 會在非開發環境中加入例外狀況處理中介軟體：</span><span class="sxs-lookup"><span data-stu-id="03bae-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="03bae-130">RazorPages 應用程式範本會 *.cshtml* <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> `ErrorModel` 在 [ *pages* ] 資料夾中提供錯誤頁面 () 和類別 () 。</span><span class="sxs-lookup"><span data-stu-id="03bae-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="03bae-131">針對 MVC 應用程式，專案範本會包含 Error 動作方法和 Error 檢視。</span><span class="sxs-lookup"><span data-stu-id="03bae-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="03bae-132">以下為動作方法：</span><span class="sxs-lookup"><span data-stu-id="03bae-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="03bae-133">請勿將錯誤處理常式動作方法標記為 HTTP 方法屬性，例如 `HttpGet` 。</span><span class="sxs-lookup"><span data-stu-id="03bae-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="03bae-134">明確的動詞命令可防止某些要求取得方法。</span><span class="sxs-lookup"><span data-stu-id="03bae-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="03bae-135">允許匿名存取方法，以便未經驗證的使用者能夠收到錯誤檢視。</span><span class="sxs-lookup"><span data-stu-id="03bae-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="03bae-136">存取例外狀況</span><span class="sxs-lookup"><span data-stu-id="03bae-136">Access the exception</span></span>

<span data-ttu-id="03bae-137">使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 來存取例外狀況或和錯誤處理常式控制器或頁面中的原始要求路徑：</span><span class="sxs-lookup"><span data-stu-id="03bae-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="03bae-138">請**勿**提供敏感性的錯誤資訊給用戶端。</span><span class="sxs-lookup"><span data-stu-id="03bae-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="03bae-139">提供錯誤有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="03bae-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="03bae-140">若要在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) \(英文\) 中查看例外狀況處理頁面，請使用 `ProdEnvironment` 和 `ErrorHandlerPage` 前置處理器指示詞，並選取首頁上的 [觸發例外狀況]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="03bae-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="03bae-141">例外處理常式 Lambda</span><span class="sxs-lookup"><span data-stu-id="03bae-141">Exception handler lambda</span></span>

<span data-ttu-id="03bae-142">[自訂例外處理常式頁面](#exception-handler-page)的替代方法，便是將 Lambda 提供給 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>。</span><span class="sxs-lookup"><span data-stu-id="03bae-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="03bae-143">使用 lambda 可讓您在傳回回應之前存取錯誤。</span><span class="sxs-lookup"><span data-stu-id="03bae-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="03bae-144">以下是將 Lambda 用於例外狀況處理的範例：</span><span class="sxs-lookup"><span data-stu-id="03bae-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="03bae-145">在上述程式碼中， `await context.Response.WriteAsync(new string(' ', 512));` 會加入，因此 Internet Explorer 瀏覽器會顯示錯誤訊息，而不是 IE 錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="03bae-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="03bae-146">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/16144)。</span><span class="sxs-lookup"><span data-stu-id="03bae-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="03bae-147">請**勿**從 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 提供錯誤資訊給用戶端。</span><span class="sxs-lookup"><span data-stu-id="03bae-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="03bae-148">提供錯誤有安全性風險。</span><span class="sxs-lookup"><span data-stu-id="03bae-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="03bae-149">若要在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) \(英文\) 中查看例外狀況處理 Lambda 的結果，請使用 `ProdEnvironment` 和 `ErrorHandlerLambda` 前置處理器指示詞，並選取首頁上的 [觸發例外狀況]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="03bae-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="03bae-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="03bae-150">UseStatusCodePages</span></span>

<span data-ttu-id="03bae-151">根據預設，ASP.NET Core 應用程式不會提供 HTTP 狀態碼 (例如「404 - 找不到」\*\*) 等狀態碼頁面。</span><span class="sxs-lookup"><span data-stu-id="03bae-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="03bae-152">應用程式會傳回狀態碼和空白回應主體。</span><span class="sxs-lookup"><span data-stu-id="03bae-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="03bae-153">若要提供狀態碼頁面，請使用狀態碼頁面中介軟體。</span><span class="sxs-lookup"><span data-stu-id="03bae-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="03bae-154">該中介軟體是由 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) \(英文\) 套件所提供，其位於 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="03bae-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="03bae-155">若要針對常見的錯誤狀態碼啟用預設的純文字處理常式，請呼叫 `Startup.Configure` 方法中的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：</span><span class="sxs-lookup"><span data-stu-id="03bae-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="03bae-156">要求處理中介軟體 (例如靜態檔案中介軟體和 MVC 中介軟體) 之前，應該先呼叫 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="03bae-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="03bae-157">以下是預設處理常式所有顯示文字的範例：</span><span class="sxs-lookup"><span data-stu-id="03bae-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="03bae-158">若要在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) \(英文\) 中查看其中一種狀態碼頁面格式，請使用其中一個以 `StatusCodePages` 為前置處理器指示詞，並選取首頁上的 [觸發 404]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="03bae-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="03bae-159">具格式字串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="03bae-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="03bae-160">若要自訂回應內容類型和文字，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用內容類型和格式字串：</span><span class="sxs-lookup"><span data-stu-id="03bae-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="03bae-161">具 Lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="03bae-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="03bae-162">若要指定自訂錯誤處理和回應撰寫程式碼，請使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 的多載，其會採用 Lambda 運算式：</span><span class="sxs-lookup"><span data-stu-id="03bae-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="03bae-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="03bae-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="03bae-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="03bae-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="03bae-165">傳送「302 - 已找到」\*\* 狀態碼傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="03bae-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="03bae-166">將用戶端重新導向到 URL 範本中提供的位置。</span><span class="sxs-lookup"><span data-stu-id="03bae-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="03bae-167">URL 範本可以針對狀態碼包含 `{0}` 預留位置，如範例所示。</span><span class="sxs-lookup"><span data-stu-id="03bae-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="03bae-168">如果 URL 範本是以波狀符號 (~) 為開頭，該波狀符號會被應用程式的 `PathBase`取代。</span><span class="sxs-lookup"><span data-stu-id="03bae-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="03bae-169">如果您指向應用程式內的端點，請建立端點的 MVC 視圖或 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="03bae-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="03bae-170">如需 Razor 頁面範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的*pages/StatusCode. cshtml。*</span><span class="sxs-lookup"><span data-stu-id="03bae-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="03bae-171">此方法通常是在下列應用程式相關情況下使用：</span><span class="sxs-lookup"><span data-stu-id="03bae-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="03bae-172">應用程式應將用戶端重新導向至不同的端點時 (通常是在由其他應用程式處理錯誤的情況下)。</span><span class="sxs-lookup"><span data-stu-id="03bae-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="03bae-173">針對 Web 應用程式，用戶端的瀏覽器網址列會反映重新導向後的端點。</span><span class="sxs-lookup"><span data-stu-id="03bae-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="03bae-174">應用程式不應該保留並傳回原始狀態碼與初始重新導向回應時。</span><span class="sxs-lookup"><span data-stu-id="03bae-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="03bae-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="03bae-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="03bae-176"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="03bae-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="03bae-177">傳回原始狀態碼給用戶端。</span><span class="sxs-lookup"><span data-stu-id="03bae-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="03bae-178">可透過使用替代路徑重新執行要求管線來產生回應本文。</span><span class="sxs-lookup"><span data-stu-id="03bae-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="03bae-179">如果您指向應用程式內的端點，請建立端點的 MVC 視圖或 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="03bae-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="03bae-180">請確定先放置，才能將 `UseStatusCodePagesWithReExecute` `UseRouting` 要求重新路由至狀態頁面。</span><span class="sxs-lookup"><span data-stu-id="03bae-180">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="03bae-181">如需 Razor 頁面範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的*pages/StatusCode. cshtml。*</span><span class="sxs-lookup"><span data-stu-id="03bae-181">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="03bae-182">此方法通常是在下列應用程式相關情況下使用：</span><span class="sxs-lookup"><span data-stu-id="03bae-182">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="03bae-183">在不重新導向至其他端點的情況下處理要求。</span><span class="sxs-lookup"><span data-stu-id="03bae-183">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="03bae-184">針對 Web 應用程式，用戶端的瀏覽器網址列會反映原始要求的端點。</span><span class="sxs-lookup"><span data-stu-id="03bae-184">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="03bae-185">保留並傳回原始狀態碼與回應時。</span><span class="sxs-lookup"><span data-stu-id="03bae-185">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="03bae-186">URL 和查詢字串範本可能會包含該狀態碼的預留位置 (`{0}`)。</span><span class="sxs-lookup"><span data-stu-id="03bae-186">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="03bae-187">URL 範本的開頭必須是斜線 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="03bae-187">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="03bae-188">在路徑中使用預留位置時，請確認端點 (頁面或控制器) 可以處理路徑線段。</span><span class="sxs-lookup"><span data-stu-id="03bae-188">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="03bae-189">例如，錯誤的 Razor 頁面應接受具有指示詞的選擇性路徑區段值 `@page` ：</span><span class="sxs-lookup"><span data-stu-id="03bae-189">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="03bae-190">處理錯誤的端點可以取得產生該錯誤的原始 URL，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="03bae-190">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="03bae-191">停用狀態碼頁面</span><span class="sxs-lookup"><span data-stu-id="03bae-191">Disable status code pages</span></span>

<span data-ttu-id="03bae-192">若要停用 MVC 控制器或動作方法的狀態字碼頁面，請使用 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="03bae-192">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="03bae-193">若要在 Razor 頁面處理常式方法或 MVC 控制器中停用特定要求的狀態字碼頁面，請使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> ：</span><span class="sxs-lookup"><span data-stu-id="03bae-193">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="03bae-194">例外狀況處理程式碼</span><span class="sxs-lookup"><span data-stu-id="03bae-194">Exception-handling code</span></span>

<span data-ttu-id="03bae-195">例外狀況處理頁面中的程式碼，可擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="03bae-195">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="03bae-196">一般來說，較好的做法是讓生產環境的錯誤頁面由純靜態內容組成。</span><span class="sxs-lookup"><span data-stu-id="03bae-196">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="03bae-197">回應標頭</span><span class="sxs-lookup"><span data-stu-id="03bae-197">Response headers</span></span>

<span data-ttu-id="03bae-198">一旦傳送回應的標頭之後：</span><span class="sxs-lookup"><span data-stu-id="03bae-198">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="03bae-199">應用程式就無法變更回應的狀態碼。</span><span class="sxs-lookup"><span data-stu-id="03bae-199">The app can't change the response's status code.</span></span>
* <span data-ttu-id="03bae-200">無法執行任何例外狀況頁面或處理常式。</span><span class="sxs-lookup"><span data-stu-id="03bae-200">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="03bae-201">回應必須完成，否則會中止連線。</span><span class="sxs-lookup"><span data-stu-id="03bae-201">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="03bae-202">伺服器例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="03bae-202">Server exception handling</span></span>

<span data-ttu-id="03bae-203">除了應用程式中的例外狀況處理邏輯之外，[HTTP 伺服器實作](xref:fundamentals/servers/index)也可以處理一些例外狀況。</span><span class="sxs-lookup"><span data-stu-id="03bae-203">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="03bae-204">如果伺服器在回應標頭傳送之前攔截到例外狀況，伺服器會傳送「500 - 內部伺服器錯誤」\*\* 回應，且沒有回應本文。</span><span class="sxs-lookup"><span data-stu-id="03bae-204">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="03bae-205">如果伺服器在回應標頭傳送之後攔截到例外狀況，伺服器會關閉連線。</span><span class="sxs-lookup"><span data-stu-id="03bae-205">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="03bae-206">應用程式未處理的要求會由伺服器來處理。</span><span class="sxs-lookup"><span data-stu-id="03bae-206">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="03bae-207">當伺服器處理要求時，任何發生的例外狀況均由伺服器的例外狀況處理功能來處理。</span><span class="sxs-lookup"><span data-stu-id="03bae-207">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="03bae-208">應用程式的自訂錯誤頁面、例外狀況處理中介軟體或篩選條件並不會影響此行為。</span><span class="sxs-lookup"><span data-stu-id="03bae-208">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="03bae-209">啟動例外狀況處理</span><span class="sxs-lookup"><span data-stu-id="03bae-209">Startup exception handling</span></span>

<span data-ttu-id="03bae-210">只有裝載層可以處理應用程式啟動期間發生的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="03bae-210">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="03bae-211">可以將主機設定為會[擷取啟動錯誤](xref:fundamentals/host/web-host#capture-startup-errors)和[擷取詳細錯誤](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="03bae-211">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="03bae-212">只有在錯誤是於主機位址/連接埠繫結之後發生的情況下，裝載層才能顯示已擷取之啟動錯誤的錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="03bae-212">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="03bae-213">如果繫結失敗：</span><span class="sxs-lookup"><span data-stu-id="03bae-213">If binding fails:</span></span>

* <span data-ttu-id="03bae-214">裝載層會記錄重大例外狀況。</span><span class="sxs-lookup"><span data-stu-id="03bae-214">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="03bae-215">Dotnet 會處理損毀狀況。</span><span class="sxs-lookup"><span data-stu-id="03bae-215">The dotnet process crashes.</span></span>
* <span data-ttu-id="03bae-216">當 HTTP 伺服器是 [Kestrel](xref:fundamentals/servers/kestrel) 時，不會顯示任何錯誤頁面。</span><span class="sxs-lookup"><span data-stu-id="03bae-216">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="03bae-217">在 [IIS](/iis) (或 Azure App Service) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上執行時，如果無法啟動處理序，[模組](xref:host-and-deploy/aspnet-core-module)會傳回 *502.5 - 處理序失敗*。</span><span class="sxs-lookup"><span data-stu-id="03bae-217">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="03bae-218">如需詳細資訊，請參閱<xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="03bae-218">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="03bae-219">資料庫錯誤頁面</span><span class="sxs-lookup"><span data-stu-id="03bae-219">Database error page</span></span>

<span data-ttu-id="03bae-220">資料庫錯誤頁面中介軟體會捕捉資料庫相關的例外狀況，這些例外狀況可使用 Entity Framework 遷移來解決。</span><span class="sxs-lookup"><span data-stu-id="03bae-220">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="03bae-221">發生這些例外狀況時，系統會產生具解決該問題之可能動作詳細資料的 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="03bae-221">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="03bae-222">此頁面僅應該於開發環境中啟用。</span><span class="sxs-lookup"><span data-stu-id="03bae-222">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="03bae-223">將程式碼加入 `Startup.Configure` 來啟用該頁面：</span><span class="sxs-lookup"><span data-stu-id="03bae-223">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="03bae-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> 需要 [AspNetCore Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="03bae-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="03bae-225">例外狀況篩選條件</span><span class="sxs-lookup"><span data-stu-id="03bae-225">Exception filters</span></span>

<span data-ttu-id="03bae-226">在 MVC 應用程式中，您能以全域設定例外狀況篩選條件，或是以每個控制器或每個動作為基礎的方式設定。</span><span class="sxs-lookup"><span data-stu-id="03bae-226">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="03bae-227">在 Razor 頁面應用程式中，可以設定全域或依頁面的模型。</span><span class="sxs-lookup"><span data-stu-id="03bae-227">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="03bae-228">這些篩選會處理在控制器動作或其他篩選條件執行期間發生但的任何未處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="03bae-228">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="03bae-229">如需詳細資訊，請參閱<xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="03bae-229">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="03bae-230">例外狀況篩選條件適合用來截獲 MVC 動作中發生的例外狀況，但是它們並不像例外狀況處理中介軟體那麼有彈性。</span><span class="sxs-lookup"><span data-stu-id="03bae-230">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="03bae-231">我們建議使用中介軟體。</span><span class="sxs-lookup"><span data-stu-id="03bae-231">We recommend using the middleware.</span></span> <span data-ttu-id="03bae-232">請只在需要根據已選擇的 MVC 動作執行不同的錯誤處理時，才使用篩選條件。</span><span class="sxs-lookup"><span data-stu-id="03bae-232">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="03bae-233">模型狀態錯誤</span><span class="sxs-lookup"><span data-stu-id="03bae-233">Model state errors</span></span>

<span data-ttu-id="03bae-234">如需如何處理模型狀態錯誤的相關資訊，請參閱[模型繫結](xref:mvc/models/model-binding)和[模型驗證](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="03bae-234">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03bae-235">其他資源</span><span class="sxs-lookup"><span data-stu-id="03bae-235">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
