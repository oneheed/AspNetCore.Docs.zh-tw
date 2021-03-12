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
# <a name="handle-errors-in-aspnet-core-web-apis"></a>處理 ASP.NET Core web Api 中的錯誤

本文說明如何使用 ASP.NET Core web Api 處理和自訂錯誤處理。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/handle-errors/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="developer-exception-page"></a>開發人員例外狀況頁面

[開發人員例外狀況頁面](xref:fundamentals/error-handling)是一個實用的工具，可取得伺服器錯誤的詳細堆疊追蹤。 它會使用 <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> 從 HTTP 管線捕獲同步和非同步例外狀況，並產生錯誤回應。 為了說明，請考慮下列控制器動作：

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

執行下列 `curl` 命令來測試上述動作：

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET Core 3.0 和更新版本中，如果用戶端不要求 HTML 格式的輸出，開發人員例外狀況頁面會顯示純文字回應。 下列輸出會出現：

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

若要改為顯示 HTML 格式的回應，請將 `Accept` HTTP 要求標頭設定為 `text/html` 媒體類型。 例如：

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

請考慮下列來自 HTTP 回應的摘錄：

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

在 ASP.NET Core 2.2 及更早版本中，開發人員例外狀況頁面會顯示 HTML 格式的回應。 例如，請考慮下列來自 HTTP 回應的摘錄：

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

HTML 格式的回應會在透過 Postman 之類的工具進行測試時變得很有用。 下列螢幕擷取畫面顯示 Postman 中的純文字和 HTML 格式的回應：

![Postman 中的開發人員例外狀況頁面測試](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> **只有當應用程式在開發環境中執行時，才** 啟用開發人員例外狀況頁面。 當應用程式在生產環境中執行時，請勿公開詳細的例外狀況資訊。 如需設定環境的詳細資訊，請參閱<xref:fundamentals/environments>。
>
> 請勿將錯誤處理常式動作方法標記為 HTTP 方法屬性，例如 `HttpGet` 。 明確動詞命令會防止某些要求到達動作方法。 如果未經驗證的使用者應該會看到錯誤，則允許匿名存取方法。

## <a name="exception-handler"></a>例外處理常式

在非開發環境中，您可以使用 [例外狀況處理中介軟體](xref:fundamentals/error-handling) 來產生錯誤承載：

1. 在中 `Startup.Configure` ，叫 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 用以使用中介軟體：

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. 設定控制器動作以回應 `/error` 路由：

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

上述 `Error` 動作會將符合 [RFC 7807](https://tools.ietf.org/html/rfc7807)規範的承載傳送給用戶端。

例外狀況處理中介軟體也可以在本機開發環境中提供更詳細的內容協商輸出。 使用下列步驟，在開發和生產環境中產生一致的裝載格式：

1. 在中 `Startup.Configure` ，註冊環境特定的例外狀況處理中介軟體實例：

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

    在上述程式碼中，會使用下列程式碼來註冊中介軟體：

    * `/error-local-development`開發環境中的路由。
    * `/error`在非開發環境中的路由。
    
1. 將屬性路由套用至控制器動作：

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    上述程式碼會呼叫[ControllerBase](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A)來建立回應。 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>

## <a name="use-exceptions-to-modify-the-response"></a>使用例外狀況來修改回應

您可以從控制器外部修改回應內容。 在 ASP.NET 4.x Web API 中，有一種方法可以使用 <xref:System.Web.Http.HttpResponseException> 類型。 ASP.NET Core 不包含對等類型。 您 `HttpResponseException` 可以使用下列步驟來新增的支援：

1. 建立名為的已知例外狀況類型 `HttpResponseException` ：

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. 建立名為的動作篩選準則 `HttpResponseExceptionFilter` ：

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    上述篩選準則會指定 `Order` 最大整數值減10的。 這可讓其他篩選準則在管線結束時執行。

1. 在中 `Startup.ConfigureServices` ，將動作篩選準則新增至篩選集合：

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>驗證失敗錯誤回應

針對 web API 控制器， <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 當模型驗證失敗時，MVC 會以回應類型回應。 MVC 使用的結果 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> 來建立驗證失敗的錯誤回應。 下列範例會使用 factory 將預設回應類型變更為 <xref:Microsoft.AspNetCore.Mvc.SerializableError> `Startup.ConfigureServices` ：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>用戶端錯誤回應

*錯誤結果* 定義為 HTTP 狀態碼為400或更高的結果。 針對 web API 控制器，MVC 會將錯誤結果轉換成結果 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 。

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2.1 會產生幾乎符合 RFC 7807 規範的問題詳細資料回應。 如果100% 合規性很重要，請將專案升級至 ASP.NET Core 2.2 或更新版本。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

您可以透過下列其中一種方式來設定錯誤回應：

1. [執行 ProblemDetailsFactory](#implement-problemdetailsfactory)
1. [使用 ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>實作 `ProblemDetailsFactory`

MVC 用 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> 來產生和的所有 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 實例 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 。 這包括用戶端錯誤回應、驗證失敗錯誤回應，以及 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> 和 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper 方法。

若要自訂問題詳細資料回應，請在中註冊的自訂執行 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> `Startup.ConfigureServices` ：

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>使用 ApiBehaviorOptions. ClientErrorMapping

使用 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> 屬性可設定 `ProblemDetails` 回應的內容。 例如，中的下列程式碼會 `Startup.ConfigureServices` 更新 `type` 404 回應的屬性：

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a>用來處理例外狀況的自訂中介軟體

例外狀況處理中介軟體中的預設值適用于大部分的應用程式。 對於需要特製化例外狀況處理的應用程式，請考慮 [自訂例外狀況處理中介軟體](xref:fundamentals/error-handling#exception-handler-lambda)。
