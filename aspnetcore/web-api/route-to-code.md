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
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556602"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="6df8b-103">ASP.NET Core 中的基本 JSON Api Route-to-code</span><span class="sxs-lookup"><span data-stu-id="6df8b-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="6df8b-104">依 [James 牛頓](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6df8b-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="6df8b-105">ASP.NET Core 支援多種方法來建立 JSON web Api：</span><span class="sxs-lookup"><span data-stu-id="6df8b-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="6df8b-106">[ASP.NET Core WEB API](xref:web-api/index) 提供完整的架構來建立 api。</span><span class="sxs-lookup"><span data-stu-id="6df8b-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="6df8b-107">藉由繼承來建立服務 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="6df8b-108">架構所提供的部分功能包括模型系結、驗證、內容協商、輸入和輸出格式，以及 OpenAPI。</span><span class="sxs-lookup"><span data-stu-id="6df8b-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="6df8b-109">Route-to-code 是 ASP.NET Core web API 的非架構替代方案。</span><span class="sxs-lookup"><span data-stu-id="6df8b-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="6df8b-110">Route-to-code 將 [ASP.NET Core 路由](xref:fundamentals/routing) 直接連接到您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="6df8b-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="6df8b-111">您的程式碼會從要求中讀取並寫入回應。</span><span class="sxs-lookup"><span data-stu-id="6df8b-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="6df8b-112">Route-to-code 沒有 web API 的 advanced 功能，但您也不需要進行任何設定，就能使用它。</span><span class="sxs-lookup"><span data-stu-id="6df8b-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="6df8b-113">Route-to-code 在建立小型和基本的 JSON web Api 時，是不錯的方法。</span><span class="sxs-lookup"><span data-stu-id="6df8b-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="6df8b-114">建立 JSON web Api</span><span class="sxs-lookup"><span data-stu-id="6df8b-114">Create JSON web APIs</span></span>

<span data-ttu-id="6df8b-115">ASP.NET Core 提供協助程式方法，可輕鬆建立 JSON web Api：</span><span class="sxs-lookup"><span data-stu-id="6df8b-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="6df8b-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> 檢查 `Content-Type` JSON 內容類型的標頭。</span><span class="sxs-lookup"><span data-stu-id="6df8b-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="6df8b-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 從要求讀取 JSON，並將它還原序列化為指定的類型。</span><span class="sxs-lookup"><span data-stu-id="6df8b-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="6df8b-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 將指定的值以 JSON 寫入至回應主體，並將回應內容類型設定為 `application/json` 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="6df8b-119">以路由為基礎的輕量 JSON Api 是在 *Startup.cs* 中指定。</span><span class="sxs-lookup"><span data-stu-id="6df8b-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="6df8b-120">路由和 API 邏輯會在中設定 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 為應用程式要求管線的一部分。</span><span class="sxs-lookup"><span data-stu-id="6df8b-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="6df8b-121">寫入 JSON 回應</span><span class="sxs-lookup"><span data-stu-id="6df8b-121">Write JSON response</span></span>

<span data-ttu-id="6df8b-122">請考慮下列程式碼，以設定應用程式的 JSON API：</span><span class="sxs-lookup"><span data-stu-id="6df8b-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="6df8b-123">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="6df8b-123">The preceding code:</span></span>

* <span data-ttu-id="6df8b-124">以路由範本的形式新增 HTTP GET API 端點 `/hello/{name:alpha}` 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="6df8b-125">當路由相符時，API 會讀取要求的 `name` 路由值。</span><span class="sxs-lookup"><span data-stu-id="6df8b-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="6df8b-126">以 JSON 回應的形式寫入匿名型別 `WriteAsJsonAsync` 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="6df8b-127">讀取 JSON 要求</span><span class="sxs-lookup"><span data-stu-id="6df8b-127">Read JSON request</span></span>

<span data-ttu-id="6df8b-128">`HasJsonContentType` 和 `ReadFromJsonAsync` 可以用來還原序列化以路由為基礎的 JSON API 中的 json 回應：</span><span class="sxs-lookup"><span data-stu-id="6df8b-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="6df8b-129">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="6df8b-129">The preceding code:</span></span>

* <span data-ttu-id="6df8b-130">以路由範本的形式新增 HTTP POST API 端點 `/weather` 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="6df8b-131">當路由相符時，會 `HasJsonContentType` 驗證要求內容類型。</span><span class="sxs-lookup"><span data-stu-id="6df8b-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="6df8b-132">非 JSON 內容類型會傳回415狀態碼。</span><span class="sxs-lookup"><span data-stu-id="6df8b-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="6df8b-133">如果內容類型為 JSON，則會將要求內容還原序列化 `ReadFromJsonAsync` 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="6df8b-134">設定 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="6df8b-134">Configure JSON serialization</span></span>

<span data-ttu-id="6df8b-135">有兩種方式可以自訂 JSON 序列化：</span><span class="sxs-lookup"><span data-stu-id="6df8b-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="6df8b-136">您可以在方法中使用來設定預設序列化選項 <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-136">Default serialization options can be configured with <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="6df8b-137">`WriteAsJsonAsync` 和 `ReadFromJsonAsync` 具有接受物件的多載 <xref:System.Text.Json.JsonSerializerOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a <xref:System.Text.Json.JsonSerializerOptions> object.</span></span> <span data-ttu-id="6df8b-138">此選項物件會覆寫預設選項。</span><span class="sxs-lookup"><span data-stu-id="6df8b-138">This options object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="6df8b-139">驗證和授權</span><span class="sxs-lookup"><span data-stu-id="6df8b-139">Authentication and authorization</span></span>

<span data-ttu-id="6df8b-140">Route-to-code 支援驗證和授權。</span><span class="sxs-lookup"><span data-stu-id="6df8b-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="6df8b-141">屬性（例如 `[Authorize]` 和 `[AllowAnonymous]` ）無法放在對應至要求委派的端點上。</span><span class="sxs-lookup"><span data-stu-id="6df8b-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="6df8b-142">相反地，會使用 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> 和擴充方法來新增授權中繼資料 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-142">Instead, authorization metadata is added using the <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="6df8b-143">相依性插入</span><span class="sxs-lookup"><span data-stu-id="6df8b-143">Dependency injection</span></span>

<span data-ttu-id="6df8b-144">使用函式的相依性[插入 (DI) ](xref:fundamentals/dependency-injection)無法搭配使用 Route-to-code 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="6df8b-145">Web API 會使用插入至函式的服務，為您建立一個控制器。</span><span class="sxs-lookup"><span data-stu-id="6df8b-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="6df8b-146">執行端點時不會建立類型，因此必須手動解決服務。</span><span class="sxs-lookup"><span data-stu-id="6df8b-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="6df8b-147">以路由為基礎的 Api 可以用 <xref:System.IServiceProvider> 來解析服務：</span><span class="sxs-lookup"><span data-stu-id="6df8b-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="6df8b-148">暫時性和範圍的存留期服務（例如 `DbContext` ）必須從端點的要求委派內的 [HttpCoNtext RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 解析。</span><span class="sxs-lookup"><span data-stu-id="6df8b-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="6df8b-149">您 `ILogger` 可以從 [IEndpointRouteBuilder ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)中解析單一存留期服務（例如）。</span><span class="sxs-lookup"><span data-stu-id="6df8b-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="6df8b-150">服務可以在要求委派之外解析，並且在端點之間共用。</span><span class="sxs-lookup"><span data-stu-id="6df8b-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="6df8b-151">廣泛使用 DI 的 Api 應該考慮使用支援 DI 的 ASP.NET Core 應用程式類型。</span><span class="sxs-lookup"><span data-stu-id="6df8b-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="6df8b-152">例如，ASP.NET Core web API。</span><span class="sxs-lookup"><span data-stu-id="6df8b-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="6df8b-153">使用控制器的函式插入服務，比手動解析服務更容易。</span><span class="sxs-lookup"><span data-stu-id="6df8b-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="6df8b-154">API 專案結構</span><span class="sxs-lookup"><span data-stu-id="6df8b-154">API project structure</span></span>

<span data-ttu-id="6df8b-155">以路由為基礎的 Api 不一定要位於 *Startup.cs* 中。</span><span class="sxs-lookup"><span data-stu-id="6df8b-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="6df8b-156">Api 可以放在其他檔案中，並在啟動時對應 `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="6df8b-157">這種方法可減少啟動設定檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="6df8b-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="6df8b-158">請考慮下列 `UserApi` 定義方法的靜態類別 `Map` 。</span><span class="sxs-lookup"><span data-stu-id="6df8b-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="6df8b-159">方法會對應以路由為基礎的 Api。</span><span class="sxs-lookup"><span data-stu-id="6df8b-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="6df8b-160">在 `Startup.Configure` 方法中， `Map` 會在中呼叫方法和其他類別的靜態方法 `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="6df8b-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a><span data-ttu-id="6df8b-161">相較于 Web API，值得注意的遺漏功能</span><span class="sxs-lookup"><span data-stu-id="6df8b-161">Notable missing features compared to Web API</span></span>

<span data-ttu-id="6df8b-162">Route-to-code 是針對基本 JSON Api 所設計。</span><span class="sxs-lookup"><span data-stu-id="6df8b-162">Route-to-code is designed for basic JSON APIs.</span></span> <span data-ttu-id="6df8b-163">它並不支援 ASP.NET Core Web API 提供的許多先進功能。</span><span class="sxs-lookup"><span data-stu-id="6df8b-163">It doesn't have support for many of the advanced features provided by ASP.NET Core Web API.</span></span>

<span data-ttu-id="6df8b-164">未提供的功能 Route-to-code 包括：</span><span class="sxs-lookup"><span data-stu-id="6df8b-164">Features not provided by Route-to-code include:</span></span>

* <span data-ttu-id="6df8b-165">模型繫結</span><span class="sxs-lookup"><span data-stu-id="6df8b-165">Model binding</span></span>
* <span data-ttu-id="6df8b-166">模型驗證</span><span class="sxs-lookup"><span data-stu-id="6df8b-166">Model validation</span></span>
* <span data-ttu-id="6df8b-167">OpenAPI/Swagger</span><span class="sxs-lookup"><span data-stu-id="6df8b-167">OpenAPI/Swagger</span></span>
* <span data-ttu-id="6df8b-168">內容協商</span><span class="sxs-lookup"><span data-stu-id="6df8b-168">Content negotiation</span></span>
* <span data-ttu-id="6df8b-169">函數相依性插入</span><span class="sxs-lookup"><span data-stu-id="6df8b-169">Constructor dependency injection</span></span>
* <span data-ttu-id="6df8b-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807)) </span><span class="sxs-lookup"><span data-stu-id="6df8b-170">`ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))</span></span>

<span data-ttu-id="6df8b-171">如果需要上述清單中的某些功能，請考慮使用 [ASP.NET Core WEB API](xref:web-api/index) 來建立 api。</span><span class="sxs-lookup"><span data-stu-id="6df8b-171">Consider using [ASP.NET Core web API](xref:web-api/index) to create an API if it requires some of the features in the preceding list.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6df8b-172">其他資源</span><span class="sxs-lookup"><span data-stu-id="6df8b-172">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
