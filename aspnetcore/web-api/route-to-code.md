---
title: ASP.NET Core 中的基本 JSON Api Route-to-code
author: jamesnk
description: 瞭解如何使用 Route-to-code 和 JSON 擴充方法來建立輕量的 json Web api。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 49eaa3ceb47c41226b7a50782436ec270e6e1b7b
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96335606"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="73b30-103">ASP.NET Core 中的基本 JSON Api Route-to-code</span><span class="sxs-lookup"><span data-stu-id="73b30-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="73b30-104">依 [James 牛頓](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="73b30-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="73b30-105">ASP.NET Core 支援多種方法來建立 JSON web Api：</span><span class="sxs-lookup"><span data-stu-id="73b30-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="73b30-106">[ASP.NET Core WEB API](xref:web-api/index) 提供完整的架構來建立 api。</span><span class="sxs-lookup"><span data-stu-id="73b30-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="73b30-107">藉由繼承來建立服務 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。</span><span class="sxs-lookup"><span data-stu-id="73b30-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="73b30-108">架構所提供的部分功能包括模型系結、驗證、內容協商、輸入和輸出格式，以及 OpenAPI。</span><span class="sxs-lookup"><span data-stu-id="73b30-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="73b30-109">Route-to-code 是 ASP.NET Core web API 的非架構替代方案。</span><span class="sxs-lookup"><span data-stu-id="73b30-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="73b30-110">Route-to-code 將 [ASP.NET Core 路由](xref:fundamentals/routing) 直接連接到您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="73b30-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="73b30-111">您的程式碼會從要求中讀取並寫入回應。</span><span class="sxs-lookup"><span data-stu-id="73b30-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="73b30-112">Route-to-code 沒有 web API 的 advanced 功能，但您也不需要進行任何設定，就能使用它。</span><span class="sxs-lookup"><span data-stu-id="73b30-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="73b30-113">Route-to-code 在建立小型和基本的 JSON web Api 時，是不錯的方法。</span><span class="sxs-lookup"><span data-stu-id="73b30-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="73b30-114">建立 JSON web Api</span><span class="sxs-lookup"><span data-stu-id="73b30-114">Create JSON web APIs</span></span>

<span data-ttu-id="73b30-115">ASP.NET Core 提供協助程式方法，可輕鬆建立 JSON web Api：</span><span class="sxs-lookup"><span data-stu-id="73b30-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="73b30-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> 檢查 `Content-Type` JSON 內容類型的標頭。</span><span class="sxs-lookup"><span data-stu-id="73b30-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="73b30-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 從要求讀取 JSON，並將它還原序列化為指定的類型。</span><span class="sxs-lookup"><span data-stu-id="73b30-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="73b30-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 將指定的值以 JSON 寫入至回應主體，並將回應內容類型設定為 `application/json` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="73b30-119">以路由為基礎的輕量 JSON Api 是在 *Startup.cs* 中指定。</span><span class="sxs-lookup"><span data-stu-id="73b30-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="73b30-120">路由和 API 邏輯會在中設定 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 為應用程式要求管線的一部分。</span><span class="sxs-lookup"><span data-stu-id="73b30-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="73b30-121">寫入 JSON 回應</span><span class="sxs-lookup"><span data-stu-id="73b30-121">Write JSON response</span></span>

<span data-ttu-id="73b30-122">請考慮下列程式碼，以設定應用程式的 JSON API：</span><span class="sxs-lookup"><span data-stu-id="73b30-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="73b30-123">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="73b30-123">The preceding code:</span></span>

* <span data-ttu-id="73b30-124">以路由範本的形式新增 HTTP GET API 端點 `/hello/{name:alpha}` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="73b30-125">當路由相符時，API 會讀取要求的 `name` 路由值。</span><span class="sxs-lookup"><span data-stu-id="73b30-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="73b30-126">以 JSON 回應的形式寫入匿名型別 `WriteAsJsonAsync` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="73b30-127">讀取 JSON 要求</span><span class="sxs-lookup"><span data-stu-id="73b30-127">Read JSON request</span></span>

<span data-ttu-id="73b30-128">`HasJsonContentType` 和 `ReadFromJsonAsync` 可以用來還原序列化以路由為基礎的 JSON API 中的 json 回應：</span><span class="sxs-lookup"><span data-stu-id="73b30-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="73b30-129">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="73b30-129">The preceding code:</span></span>

* <span data-ttu-id="73b30-130">以路由範本的形式新增 HTTP POST API 端點 `/weather` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="73b30-131">當路由相符時，會 `HasJsonContentType` 驗證要求內容類型。</span><span class="sxs-lookup"><span data-stu-id="73b30-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="73b30-132">非 JSON 內容類型會傳回415狀態碼。</span><span class="sxs-lookup"><span data-stu-id="73b30-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="73b30-133">如果內容類型為 JSON，則會將要求內容還原序列化 `ReadFromJsonAsync` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="73b30-134">設定 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="73b30-134">Configure JSON serialization</span></span>

<span data-ttu-id="73b30-135">有兩種方式可以自訂 JSON 序列化：</span><span class="sxs-lookup"><span data-stu-id="73b30-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="73b30-136">您可以在方法中使用來設定預設序列化選項 `JsonOptions` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-136">Default serialization options can be configured with `JsonOptions` in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="73b30-137">`WriteAsJsonAsync` 和 `ReadFromJsonAsync` 具有接受物件的多載 `JsonSerializerOptions` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a `JsonSerializerOptions` object.</span></span> <span data-ttu-id="73b30-138">這個 `JsonSerializerOptions` 物件會覆寫預設選項。</span><span class="sxs-lookup"><span data-stu-id="73b30-138">This `JsonSerializerOptions` object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="73b30-139">驗證與授權</span><span class="sxs-lookup"><span data-stu-id="73b30-139">Authentication and authorization</span></span>

<span data-ttu-id="73b30-140">Route-to-code 支援驗證和授權。</span><span class="sxs-lookup"><span data-stu-id="73b30-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="73b30-141">屬性（例如 `[Authorize]` 和 `[AllowAnonymous]` ）無法放在對應至要求委派的端點上。</span><span class="sxs-lookup"><span data-stu-id="73b30-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="73b30-142">相反地，會使用 `RequireAuthorization` 和擴充方法來新增授權中繼資料 `AllowAnonymous` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-142">Instead, authorization metadata is added using the `RequireAuthorization` and `AllowAnonymous` extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="73b30-143">相依性插入</span><span class="sxs-lookup"><span data-stu-id="73b30-143">Dependency injection</span></span>

<span data-ttu-id="73b30-144">使用函式的相依性[插入 (DI) ](xref:fundamentals/dependency-injection)無法搭配使用 Route-to-code 。</span><span class="sxs-lookup"><span data-stu-id="73b30-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="73b30-145">Web API 會使用插入至函式的服務，為您建立一個控制器。</span><span class="sxs-lookup"><span data-stu-id="73b30-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="73b30-146">執行端點時不會建立類型，因此必須手動解決服務。</span><span class="sxs-lookup"><span data-stu-id="73b30-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="73b30-147">以路由為基礎的 Api 可以用 <xref:System.IServiceProvider> 來解析服務：</span><span class="sxs-lookup"><span data-stu-id="73b30-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="73b30-148">暫時性和範圍的存留期服務（例如 `DbContext` ）必須從端點的要求委派內的 [HttpCoNtext RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 解析。</span><span class="sxs-lookup"><span data-stu-id="73b30-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="73b30-149">您 `ILogger` 可以從 [IEndpointRouteBuilder ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)中解析單一存留期服務（例如）。</span><span class="sxs-lookup"><span data-stu-id="73b30-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="73b30-150">服務可以在要求委派之外解析，並且在端點之間共用。</span><span class="sxs-lookup"><span data-stu-id="73b30-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="73b30-151">廣泛使用 DI 的 Api 應該考慮使用支援 DI 的 ASP.NET Core 應用程式類型。</span><span class="sxs-lookup"><span data-stu-id="73b30-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="73b30-152">例如，ASP.NET Core web API。</span><span class="sxs-lookup"><span data-stu-id="73b30-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="73b30-153">使用控制器的函式插入服務，比手動解析服務更容易。</span><span class="sxs-lookup"><span data-stu-id="73b30-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="73b30-154">API 專案結構</span><span class="sxs-lookup"><span data-stu-id="73b30-154">API project structure</span></span>

<span data-ttu-id="73b30-155">以路由為基礎的 Api 不一定要位於 *Startup.cs* 中。</span><span class="sxs-lookup"><span data-stu-id="73b30-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="73b30-156">Api 可以放在其他檔案中，並在啟動時對應 `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="73b30-157">這種方法可減少啟動設定檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="73b30-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="73b30-158">請考慮下列 `UserApi` 定義方法的靜態類別 `Map` 。</span><span class="sxs-lookup"><span data-stu-id="73b30-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="73b30-159">方法會對應以路由為基礎的 Api。</span><span class="sxs-lookup"><span data-stu-id="73b30-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="73b30-160">在 `Startup.Configure` 方法中， `Map` 會在中呼叫方法和其他類別的靜態方法 `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="73b30-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="73b30-161">其他資源</span><span class="sxs-lookup"><span data-stu-id="73b30-161">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
