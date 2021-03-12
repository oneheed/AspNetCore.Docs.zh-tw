---
title: 處理 ASP.NET Core web Api 中的錯誤
author: pranavkm
description: 瞭解如何使用 ASP.NET Core web Api 進行錯誤處理。
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: f9cc38f444bd3db826595b7de64db05792915a23
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585717"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="fc420-103">處理 ASP.NET Core web Api 中的錯誤</span><span class="sxs-lookup"><span data-stu-id="fc420-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="fc420-104">本文說明如何使用 ASP.NET Core web Api 處理和自訂錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="fc420-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="fc420-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/handle-errors/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="fc420-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="fc420-106">開發人員例外狀況頁面</span><span class="sxs-lookup"><span data-stu-id="fc420-106">Developer Exception Page</span></span>

<span data-ttu-id="fc420-107">[開發人員例外狀況頁面](xref:fundamentals/error-handling)是一個實用的工具，可取得伺服器錯誤的詳細堆疊追蹤。</span><span class="sxs-lookup"><span data-stu-id="fc420-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="fc420-108">它會使用 <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> 從 HTTP 管線捕獲同步和非同步例外狀況，並產生錯誤回應。</span><span class="sxs-lookup"><span data-stu-id="fc420-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="fc420-109">為了說明，請考慮下列控制器動作：</span><span class="sxs-lookup"><span data-stu-id="fc420-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="fc420-110">執行下列 `curl` 命令來測試上述動作：</span><span class="sxs-lookup"><span data-stu-id="fc420-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc420-111">在 ASP.NET Core 3.0 和更新版本中，如果用戶端不要求 HTML 格式的輸出，開發人員例外狀況頁面會顯示純文字回應。</span><span class="sxs-lookup"><span data-stu-id="fc420-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="fc420-112">下列輸出會出現：</span><span class="sxs-lookup"><span data-stu-id="fc420-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="fc420-113">若要改為顯示 HTML 格式的回應，請將 `Accept` HTTP 要求標頭設定為 `text/html` 媒體類型。</span><span class="sxs-lookup"><span data-stu-id="fc420-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="fc420-114">例如：</span><span class="sxs-lookup"><span data-stu-id="fc420-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="fc420-115">請考慮下列來自 HTTP 回應的摘錄：</span><span class="sxs-lookup"><span data-stu-id="fc420-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="fc420-116">在 ASP.NET Core 2.2 及更早版本中，開發人員例外狀況頁面會顯示 HTML 格式的回應。</span><span class="sxs-lookup"><span data-stu-id="fc420-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="fc420-117">例如，請考慮下列來自 HTTP 回應的摘錄：</span><span class="sxs-lookup"><span data-stu-id="fc420-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc420-118">HTML 格式的回應會在透過 Postman 之類的工具進行測試時變得很有用。</span><span class="sxs-lookup"><span data-stu-id="fc420-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="fc420-119">下列螢幕擷取畫面顯示 Postman 中的純文字和 HTML 格式的回應：</span><span class="sxs-lookup"><span data-stu-id="fc420-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Postman 中的開發人員例外狀況頁面測試](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="fc420-121">**只有當應用程式在開發環境中執行時，才** 啟用開發人員例外狀況頁面。</span><span class="sxs-lookup"><span data-stu-id="fc420-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="fc420-122">當應用程式在生產環境中執行時，請勿公開詳細的例外狀況資訊。</span><span class="sxs-lookup"><span data-stu-id="fc420-122">Don't share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="fc420-123">如需設定環境的詳細資訊，請參閱<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="fc420-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>
>
> <span data-ttu-id="fc420-124">請勿將錯誤處理常式動作方法標記為 HTTP 方法屬性，例如 `HttpGet` 。</span><span class="sxs-lookup"><span data-stu-id="fc420-124">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="fc420-125">明確動詞命令會防止某些要求到達動作方法。</span><span class="sxs-lookup"><span data-stu-id="fc420-125">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="fc420-126">如果未經驗證的使用者應該會看到錯誤，則允許匿名存取方法。</span><span class="sxs-lookup"><span data-stu-id="fc420-126">Allow anonymous access to the method if unauthenticated users should see the error.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="fc420-127">例外處理常式</span><span class="sxs-lookup"><span data-stu-id="fc420-127">Exception handler</span></span>

<span data-ttu-id="fc420-128">在非開發環境中，您可以使用 [例外狀況處理中介軟體](xref:fundamentals/error-handling) 來產生錯誤承載：</span><span class="sxs-lookup"><span data-stu-id="fc420-128">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="fc420-129">在中 `Startup.Configure` ，叫 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 用以使用中介軟體：</span><span class="sxs-lookup"><span data-stu-id="fc420-129">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="fc420-130">設定控制器動作以回應 `/error` 路由：</span><span class="sxs-lookup"><span data-stu-id="fc420-130">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="fc420-131">上述 `Error` 動作會將符合 [RFC 7807](https://tools.ietf.org/html/rfc7807)規範的承載傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="fc420-131">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="fc420-132">例外狀況處理中介軟體也可以在本機開發環境中提供更詳細的內容協商輸出。</span><span class="sxs-lookup"><span data-stu-id="fc420-132">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="fc420-133">使用下列步驟，在開發和生產環境中產生一致的裝載格式：</span><span class="sxs-lookup"><span data-stu-id="fc420-133">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="fc420-134">在中 `Startup.Configure` ，註冊環境特定的例外狀況處理中介軟體實例：</span><span class="sxs-lookup"><span data-stu-id="fc420-134">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="fc420-135">在上述程式碼中，會使用下列程式碼來註冊中介軟體：</span><span class="sxs-lookup"><span data-stu-id="fc420-135">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="fc420-136">`/error-local-development`開發環境中的路由。</span><span class="sxs-lookup"><span data-stu-id="fc420-136">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="fc420-137">`/error`在非開發環境中的路由。</span><span class="sxs-lookup"><span data-stu-id="fc420-137">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="fc420-138">將屬性路由套用至控制器動作：</span><span class="sxs-lookup"><span data-stu-id="fc420-138">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    <span data-ttu-id="fc420-139">上述程式碼會呼叫[ControllerBase](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A)來建立回應。 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails></span><span class="sxs-lookup"><span data-stu-id="fc420-139">The preceding code calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response.</span></span>

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="fc420-140">使用例外狀況來修改回應</span><span class="sxs-lookup"><span data-stu-id="fc420-140">Use exceptions to modify the response</span></span>

<span data-ttu-id="fc420-141">您可以從控制器外部修改回應內容。</span><span class="sxs-lookup"><span data-stu-id="fc420-141">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="fc420-142">在 ASP.NET 4.x Web API 中，有一種方法可以使用 <xref:System.Web.Http.HttpResponseException> 類型。</span><span class="sxs-lookup"><span data-stu-id="fc420-142">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="fc420-143">ASP.NET Core 不包含對等類型。</span><span class="sxs-lookup"><span data-stu-id="fc420-143">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="fc420-144">您 `HttpResponseException` 可以使用下列步驟來新增的支援：</span><span class="sxs-lookup"><span data-stu-id="fc420-144">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="fc420-145">建立名為的已知例外狀況類型 `HttpResponseException` ：</span><span class="sxs-lookup"><span data-stu-id="fc420-145">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="fc420-146">建立名為的動作篩選準則 `HttpResponseExceptionFilter` ：</span><span class="sxs-lookup"><span data-stu-id="fc420-146">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="fc420-147">上述篩選準則會指定 `Order` 最大整數值減10的。</span><span class="sxs-lookup"><span data-stu-id="fc420-147">The preceding filter specifies an `Order` of the maximum integer value minus 10.</span></span> <span data-ttu-id="fc420-148">這可讓其他篩選準則在管線結束時執行。</span><span class="sxs-lookup"><span data-stu-id="fc420-148">This allows other filters to run at the end of the pipeline.</span></span>

1. <span data-ttu-id="fc420-149">在中 `Startup.ConfigureServices` ，將動作篩選準則新增至篩選集合：</span><span class="sxs-lookup"><span data-stu-id="fc420-149">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="fc420-150">驗證失敗錯誤回應</span><span class="sxs-lookup"><span data-stu-id="fc420-150">Validation failure error response</span></span>

<span data-ttu-id="fc420-151">針對 web API 控制器， <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 當模型驗證失敗時，MVC 會以回應類型回應。</span><span class="sxs-lookup"><span data-stu-id="fc420-151">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="fc420-152">MVC 使用的結果 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> 來建立驗證失敗的錯誤回應。</span><span class="sxs-lookup"><span data-stu-id="fc420-152">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="fc420-153">下列範例會使用 factory 將預設回應類型變更為 <xref:Microsoft.AspNetCore.Mvc.SerializableError> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="fc420-153">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="fc420-154">用戶端錯誤回應</span><span class="sxs-lookup"><span data-stu-id="fc420-154">Client error response</span></span>

<span data-ttu-id="fc420-155">*錯誤結果* 定義為 HTTP 狀態碼為400或更高的結果。</span><span class="sxs-lookup"><span data-stu-id="fc420-155">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="fc420-156">針對 web API 控制器，MVC 會將錯誤結果轉換成結果 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 。</span><span class="sxs-lookup"><span data-stu-id="fc420-156">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="fc420-157">ASP.NET Core 2.1 會產生幾乎符合 RFC 7807 規範的問題詳細資料回應。</span><span class="sxs-lookup"><span data-stu-id="fc420-157">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="fc420-158">如果100% 合規性很重要，請將專案升級至 ASP.NET Core 2.2 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="fc420-158">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc420-159">您可以透過下列其中一種方式來設定錯誤回應：</span><span class="sxs-lookup"><span data-stu-id="fc420-159">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="fc420-160">執行 ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="fc420-160">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="fc420-161">使用 ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="fc420-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="fc420-162">實作 `ProblemDetailsFactory`</span><span class="sxs-lookup"><span data-stu-id="fc420-162">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="fc420-163">MVC 用 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> 來產生和的所有 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 實例 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 。</span><span class="sxs-lookup"><span data-stu-id="fc420-163">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="fc420-164">這包括用戶端錯誤回應、驗證失敗錯誤回應，以及 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> 和 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper 方法。</span><span class="sxs-lookup"><span data-stu-id="fc420-164">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="fc420-165">若要自訂問題詳細資料回應，請在中註冊的自訂執行 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="fc420-165">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fc420-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span><span class="sxs-lookup"><span data-stu-id="fc420-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="fc420-167">使用 ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="fc420-167">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="fc420-168">使用 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> 屬性可設定 `ProblemDetails` 回應的內容。</span><span class="sxs-lookup"><span data-stu-id="fc420-168">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="fc420-169">例如，中的下列程式碼會 `Startup.ConfigureServices` 更新 `type` 404 回應的屬性：</span><span class="sxs-lookup"><span data-stu-id="fc420-169">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a><span data-ttu-id="fc420-170">用來處理例外狀況的自訂中介軟體</span><span class="sxs-lookup"><span data-stu-id="fc420-170">Custom Middleware to handle exceptions</span></span>

<span data-ttu-id="fc420-171">例外狀況處理中介軟體中的預設值適用于大部分的應用程式。</span><span class="sxs-lookup"><span data-stu-id="fc420-171">The defaults in the exception handling middleware works well for most apps.</span></span> <span data-ttu-id="fc420-172">對於需要特製化例外狀況處理的應用程式，請考慮 [自訂例外狀況處理中介軟體](xref:fundamentals/error-handling#exception-handler-lambda)。</span><span class="sxs-lookup"><span data-stu-id="fc420-172">For apps that require specialized exception handling, consider [customizing the exception handling middleware](xref:fundamentals/error-handling#exception-handler-lambda).</span></span>
