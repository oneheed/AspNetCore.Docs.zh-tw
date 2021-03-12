---
title: ASP.NET Core 的 Factory 中介軟體啟用
author: rick-anderson
description: 了解如何在 ASP.NET Core 中搭配使用 Factory 啟用實作與強型別中介軟體。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: c4835d0d9b0cc20a1176bbb5b0aedf3c5b7d51af
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587185"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="f4e6b-103">ASP.NET Core 的 Factory 中介軟體啟用</span><span class="sxs-lookup"><span data-stu-id="f4e6b-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4e6b-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> 是[中介軟體](xref:fundamentals/middleware/index)啟用的擴充點。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="f4e6b-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 擴充方法會檢查中介軟體的已註冊類型是否實作 <xref:Microsoft.AspNetCore.Http.IMiddleware>。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="f4e6b-106">如果是，系統會使用容器中已註冊的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 執行個體來解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 實作，而不是使用以慣例為基礎的中介軟體啟用邏輯。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="f4e6b-107">中介軟體會註冊為應用程式服務容器中的[範圍服務或暫時性服務](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="f4e6b-108">優點：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-108">Benefits:</span></span>

* <span data-ttu-id="f4e6b-109">依據用戶端要求啟用 (插入範圍服務)</span><span class="sxs-lookup"><span data-stu-id="f4e6b-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="f4e6b-110">強型別的中介軟體</span><span class="sxs-lookup"><span data-stu-id="f4e6b-110">Strong typing of middleware</span></span>

<span data-ttu-id="f4e6b-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> 是依據用戶端要求 (連線) 來啟用，因此範圍服務可以插入中介軟體的建構函式中。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="f4e6b-112">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="f4e6b-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="f4e6b-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="f4e6b-113">IMiddleware</span></span>

<span data-ttu-id="f4e6b-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> 可定義應用程式要求管線的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="f4e6b-115">[InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) 方法會處理要求，並傳回 <xref:System.Threading.Tasks.Task> 以代表中介軟體的執行。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="f4e6b-116">依據慣例啟用的中介軟體：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="f4e6b-117">由 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> 啟用的中介軟體：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="f4e6b-118">您可為中介軟體建立延伸模組：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="f4e6b-119">您無法使用 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 將物件傳遞給由 Factory 啟用的中介軟體：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="f4e6b-120">由 Factory 所啟用中介軟體會新增至 `Startup.ConfigureServices` 中的內建容器：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f4e6b-121">這兩個中介軟體都會在要求處理管線的 `Startup.Configure` 中註冊：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="f4e6b-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="f4e6b-122">IMiddlewareFactory</span></span>

<span data-ttu-id="f4e6b-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供建立中介軟體的方法。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="f4e6b-124">中介軟體 Factory 實作會在容器中註冊為範圍服務。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="f4e6b-125">預設的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 實作是 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>，其位於 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 套件中。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f4e6b-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> 是[中介軟體](xref:fundamentals/middleware/index)啟用的擴充點。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="f4e6b-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 擴充方法會檢查中介軟體的已註冊類型是否實作 <xref:Microsoft.AspNetCore.Http.IMiddleware>。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="f4e6b-128">如果是，系統會使用容器中已註冊的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 執行個體來解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 實作，而不是使用以慣例為基礎的中介軟體啟用邏輯。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="f4e6b-129">中介軟體會註冊為應用程式服務容器中的[範圍服務或暫時性服務](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="f4e6b-130">優點：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-130">Benefits:</span></span>

* <span data-ttu-id="f4e6b-131">依據用戶端要求啟用 (插入範圍服務)</span><span class="sxs-lookup"><span data-stu-id="f4e6b-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="f4e6b-132">強型別的中介軟體</span><span class="sxs-lookup"><span data-stu-id="f4e6b-132">Strong typing of middleware</span></span>

<span data-ttu-id="f4e6b-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> 是依據用戶端要求 (連線) 來啟用，因此範圍服務可以插入中介軟體的建構函式中。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="f4e6b-134">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="f4e6b-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="f4e6b-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="f4e6b-135">IMiddleware</span></span>

<span data-ttu-id="f4e6b-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> 可定義應用程式要求管線的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="f4e6b-137">[InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) 方法會處理要求，並傳回 <xref:System.Threading.Tasks.Task> 以代表中介軟體的執行。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="f4e6b-138">依據慣例啟用的中介軟體：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="f4e6b-139">由 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> 啟用的中介軟體：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="f4e6b-140">您可為中介軟體建立延伸模組：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="f4e6b-141">您無法使用 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 將物件傳遞給由 Factory 啟用的中介軟體：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="f4e6b-142">由 Factory 所啟用中介軟體會新增至 `Startup.ConfigureServices` 中的內建容器：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f4e6b-143">這兩個中介軟體都會在要求處理管線的 `Startup.Configure` 中註冊：</span><span class="sxs-lookup"><span data-stu-id="f4e6b-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="f4e6b-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="f4e6b-144">IMiddlewareFactory</span></span>

<span data-ttu-id="f4e6b-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供建立中介軟體的方法。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="f4e6b-146">中介軟體 Factory 實作會在容器中註冊為範圍服務。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="f4e6b-147">預設的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 實作是 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>，其位於 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 套件中。</span><span class="sxs-lookup"><span data-stu-id="f4e6b-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f4e6b-148">其他資源</span><span class="sxs-lookup"><span data-stu-id="f4e6b-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
