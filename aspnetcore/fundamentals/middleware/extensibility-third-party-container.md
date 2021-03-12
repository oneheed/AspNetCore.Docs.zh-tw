---
title: 在 ASP.NET Core 中以協力廠商容器啟用中介軟體
author: rick-anderson
description: 了解如何在 ASP.NET Core 中搭配 Factory 啟用和協力廠商容器來使用強型別中介軟體。
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: bbbfe0e9fba33eba9d69101e5615e931ea917bc1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587368"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="bce7a-103">在 ASP.NET Core 中以協力廠商容器啟用中介軟體</span><span class="sxs-lookup"><span data-stu-id="bce7a-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bce7a-104">此文章示範如何使用 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 與 <xref:Microsoft.AspNetCore.Http.IMiddleware> 作為以協力廠商容器啟用[中介軟體](xref:fundamentals/middleware/index)的擴充點。</span><span class="sxs-lookup"><span data-stu-id="bce7a-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="bce7a-105">如需有關 `IMiddlewareFactory` 與 `IMiddleware` 的詳細資訊，請參閱 <xref:fundamentals/middleware/extensibility>。</span><span class="sxs-lookup"><span data-stu-id="bce7a-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="bce7a-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="bce7a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bce7a-107">範例應用程式會示範如何透過 `IMiddlewareFactory` 的實作 `SimpleInjectorMiddlewareFactory` 來啟用中介軟體。</span><span class="sxs-lookup"><span data-stu-id="bce7a-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="bce7a-108">此範例會使用 [Simple Injector](https://simpleinjector.org) 相依性插入 (DI) 容器。</span><span class="sxs-lookup"><span data-stu-id="bce7a-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="bce7a-109">範例的中介軟體實作會記錄查詢字串參數 (`key`) 所提供的值。</span><span class="sxs-lookup"><span data-stu-id="bce7a-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="bce7a-110">中介軟體會使用插入的資料庫內容 (範圍服務)，以記錄記憶體內部資料庫的查詢字串值。</span><span class="sxs-lookup"><span data-stu-id="bce7a-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="bce7a-111">範例應用程式純粹基於示範目的使用 [Simple Injector](https://github.com/simpleinjector/SimpleInjector)。</span><span class="sxs-lookup"><span data-stu-id="bce7a-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="bce7a-112">使用 Simple Injector 並不表示為其背書。</span><span class="sxs-lookup"><span data-stu-id="bce7a-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="bce7a-113">Simple Injector 文件和 GitHub 問題中所述的中介軟體啟用方法是 Simple Injector 維護人員建議使用的方法。</span><span class="sxs-lookup"><span data-stu-id="bce7a-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="bce7a-114">如需詳細資訊，請參閱 [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) (Simple Injector 文件) 和 [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector) (Simple Injector GitHub 存放庫)。</span><span class="sxs-lookup"><span data-stu-id="bce7a-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="bce7a-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="bce7a-115">IMiddlewareFactory</span></span>

<span data-ttu-id="bce7a-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供建立中介軟體的方法。</span><span class="sxs-lookup"><span data-stu-id="bce7a-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="bce7a-117">在範例應用程式中，將實作中介軟體 Factory 來建立 `SimpleInjectorActivatedMiddleware` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="bce7a-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="bce7a-118">中介軟體 Factory 會使用 Simple Injector 容器來解析中介軟體：</span><span class="sxs-lookup"><span data-stu-id="bce7a-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="bce7a-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="bce7a-119">IMiddleware</span></span>

<span data-ttu-id="bce7a-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> 可定義應用程式要求管線的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="bce7a-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="bce7a-121">由 `IMiddlewareFactory` 實作啟動的中介軟體 (*Middleware/SimpleInjectorActivatedMiddleware.cs*)：</span><span class="sxs-lookup"><span data-stu-id="bce7a-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="bce7a-122">將會針對此中介軟體建立延伸模組 (*Middleware/MiddlewareExtensions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="bce7a-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="bce7a-123">`Startup.ConfigureServices` 必須執行幾項工作：</span><span class="sxs-lookup"><span data-stu-id="bce7a-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="bce7a-124">設定 Simple Injector 容器。</span><span class="sxs-lookup"><span data-stu-id="bce7a-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="bce7a-125">註冊 Factory 和中介軟體。</span><span class="sxs-lookup"><span data-stu-id="bce7a-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="bce7a-126">讓應用程式的資料庫內容可從 Simple Injector 容器使用。</span><span class="sxs-lookup"><span data-stu-id="bce7a-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="bce7a-127">此中介軟體會在要求處理管線的 `Startup.Configure` 中註冊：</span><span class="sxs-lookup"><span data-stu-id="bce7a-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bce7a-128">此文章示範如何使用 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 與 <xref:Microsoft.AspNetCore.Http.IMiddleware> 作為以協力廠商容器啟用[中介軟體](xref:fundamentals/middleware/index)的擴充點。</span><span class="sxs-lookup"><span data-stu-id="bce7a-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="bce7a-129">如需有關 `IMiddlewareFactory` 與 `IMiddleware` 的詳細資訊，請參閱 <xref:fundamentals/middleware/extensibility>。</span><span class="sxs-lookup"><span data-stu-id="bce7a-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="bce7a-130">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="bce7a-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bce7a-131">範例應用程式會示範如何透過 `IMiddlewareFactory` 的實作 `SimpleInjectorMiddlewareFactory` 來啟用中介軟體。</span><span class="sxs-lookup"><span data-stu-id="bce7a-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="bce7a-132">此範例會使用 [Simple Injector](https://simpleinjector.org) 相依性插入 (DI) 容器。</span><span class="sxs-lookup"><span data-stu-id="bce7a-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="bce7a-133">範例的中介軟體實作會記錄查詢字串參數 (`key`) 所提供的值。</span><span class="sxs-lookup"><span data-stu-id="bce7a-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="bce7a-134">中介軟體會使用插入的資料庫內容 (範圍服務)，以記錄記憶體內部資料庫的查詢字串值。</span><span class="sxs-lookup"><span data-stu-id="bce7a-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="bce7a-135">範例應用程式純粹基於示範目的使用 [Simple Injector](https://github.com/simpleinjector/SimpleInjector)。</span><span class="sxs-lookup"><span data-stu-id="bce7a-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="bce7a-136">使用 Simple Injector 並不表示為其背書。</span><span class="sxs-lookup"><span data-stu-id="bce7a-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="bce7a-137">Simple Injector 文件和 GitHub 問題中所述的中介軟體啟用方法是 Simple Injector 維護人員建議使用的方法。</span><span class="sxs-lookup"><span data-stu-id="bce7a-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="bce7a-138">如需詳細資訊，請參閱 [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) (Simple Injector 文件) 和 [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector) (Simple Injector GitHub 存放庫)。</span><span class="sxs-lookup"><span data-stu-id="bce7a-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="bce7a-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="bce7a-139">IMiddlewareFactory</span></span>

<span data-ttu-id="bce7a-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供建立中介軟體的方法。</span><span class="sxs-lookup"><span data-stu-id="bce7a-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="bce7a-141">在範例應用程式中，將實作中介軟體 Factory 來建立 `SimpleInjectorActivatedMiddleware` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="bce7a-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="bce7a-142">中介軟體 Factory 會使用 Simple Injector 容器來解析中介軟體：</span><span class="sxs-lookup"><span data-stu-id="bce7a-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="bce7a-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="bce7a-143">IMiddleware</span></span>

<span data-ttu-id="bce7a-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> 可定義應用程式要求管線的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="bce7a-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="bce7a-145">由 `IMiddlewareFactory` 實作啟動的中介軟體 (*Middleware/SimpleInjectorActivatedMiddleware.cs*)：</span><span class="sxs-lookup"><span data-stu-id="bce7a-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="bce7a-146">將會針對此中介軟體建立延伸模組 (*Middleware/MiddlewareExtensions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="bce7a-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="bce7a-147">`Startup.ConfigureServices` 必須執行幾項工作：</span><span class="sxs-lookup"><span data-stu-id="bce7a-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="bce7a-148">設定 Simple Injector 容器。</span><span class="sxs-lookup"><span data-stu-id="bce7a-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="bce7a-149">註冊 Factory 和中介軟體。</span><span class="sxs-lookup"><span data-stu-id="bce7a-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="bce7a-150">讓應用程式的資料庫內容可從 Simple Injector 容器使用。</span><span class="sxs-lookup"><span data-stu-id="bce7a-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="bce7a-151">此中介軟體會在要求處理管線的 `Startup.Configure` 中註冊：</span><span class="sxs-lookup"><span data-stu-id="bce7a-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bce7a-152">其他資源</span><span class="sxs-lookup"><span data-stu-id="bce7a-152">Additional resources</span></span>

* [<span data-ttu-id="bce7a-153">中介軟體</span><span class="sxs-lookup"><span data-stu-id="bce7a-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="bce7a-154">Factory 式中介軟體啟用</span><span class="sxs-lookup"><span data-stu-id="bce7a-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* <span data-ttu-id="bce7a-155">[Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector) (Simple Injector GitHub 存放庫)</span><span class="sxs-lookup"><span data-stu-id="bce7a-155">[Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector)</span></span>
* <span data-ttu-id="bce7a-156">[Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) (Simple Injector 文件)</span><span class="sxs-lookup"><span data-stu-id="bce7a-156">[Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html)</span></span>
