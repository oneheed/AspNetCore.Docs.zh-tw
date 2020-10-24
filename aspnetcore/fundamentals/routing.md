---
title: ASP.NET Core 中的路由
author: rick-anderson
description: 探索 ASP.NET Core 路由如何負責比對 HTTP 要求和分派至可執行檔端點。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
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
uid: fundamentals/routing
ms.openlocfilehash: e3dd7168e6974f63fa963d3732bc5df41814c70e
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491612"
---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="9b58b-103">ASP.NET Core 中的路由</span><span class="sxs-lookup"><span data-stu-id="9b58b-103">Routing in ASP.NET Core</span></span>

<span data-ttu-id="9b58b-104">[Ryan Nowak](https://github.com/rynowak)、 [Kirk Larkin](https://twitter.com/serpent5)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9b58b-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b58b-105">路由會負責比對傳入的 HTTP 要求，並將這些要求分派至應用程式的可執行端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-105">Routing is responsible for matching incoming HTTP requests and dispatching those requests to the app's executable endpoints.</span></span> <span data-ttu-id="9b58b-106">[端點](#endpoint) 是應用程式可執行檔要求處理常式代碼的單位。</span><span class="sxs-lookup"><span data-stu-id="9b58b-106">[Endpoints](#endpoint) are the app's units of executable request-handling code.</span></span> <span data-ttu-id="9b58b-107">端點會定義在應用程式中，並在應用程式啟動時設定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-107">Endpoints are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="9b58b-108">端點比對程式可以從要求的 URL 中解壓縮值，並為要求處理提供這些值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-108">The endpoint matching process can extract values from the request's URL and provide those values for request processing.</span></span> <span data-ttu-id="9b58b-109">使用應用程式中的端點資訊，路由也能夠產生對應至端點的 Url。</span><span class="sxs-lookup"><span data-stu-id="9b58b-109">Using endpoint information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="9b58b-110">應用程式可以使用下列內容來設定路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-110">Apps can configure routing using:</span></span>

- <span data-ttu-id="9b58b-111">控制器</span><span class="sxs-lookup"><span data-stu-id="9b58b-111">Controllers</span></span>
- <span data-ttu-id="9b58b-112">Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9b58b-112">Razor Pages</span></span>
- SignalR
- <span data-ttu-id="9b58b-113">gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="9b58b-113">gRPC Services</span></span>
- <span data-ttu-id="9b58b-114">啟用端點的 [中介軟體](xref:fundamentals/middleware/index) ，例如 [健康情況檢查](xref:host-and-deploy/health-checks)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-114">Endpoint-enabled [middleware](xref:fundamentals/middleware/index) such as [Health Checks](xref:host-and-deploy/health-checks).</span></span>
- <span data-ttu-id="9b58b-115">使用路由註冊的委派和 lambda。</span><span class="sxs-lookup"><span data-stu-id="9b58b-115">Delegates and lambdas registered with routing.</span></span>

<span data-ttu-id="9b58b-116">本檔涵蓋 ASP.NET Core 路由的低層級詳細資料。</span><span class="sxs-lookup"><span data-stu-id="9b58b-116">This document covers low-level details of ASP.NET Core routing.</span></span> <span data-ttu-id="9b58b-117">如需設定路由的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="9b58b-117">For information on configuring routing:</span></span>

* <span data-ttu-id="9b58b-118">若為控制器，請參閱 <xref:mvc/controllers/routing> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-118">For controllers, see <xref:mvc/controllers/routing>.</span></span>
* <span data-ttu-id="9b58b-119">如需 Razor 頁面慣例，請參閱 <xref:razor-pages/razor-pages-conventions> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-119">For Razor Pages conventions, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="9b58b-120">本檔中所述的端點路由系統適用于 ASP.NET Core 3.0 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="9b58b-120">The endpoint routing system described in this document applies to ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="9b58b-121">如需以上一個路由系統為基礎的相關資訊 <xref:Microsoft.AspNetCore.Routing.IRouter> ，請使用下列其中一種方法來選取 ASP.NET Core 2.1 版本：</span><span class="sxs-lookup"><span data-stu-id="9b58b-121">For information on the previous routing system based on <xref:Microsoft.AspNetCore.Routing.IRouter>, select the ASP.NET Core 2.1 version using one of the following approaches:</span></span>

* <span data-ttu-id="9b58b-122">先前版本的版本選取器。</span><span class="sxs-lookup"><span data-stu-id="9b58b-122">The version selector for a previous version.</span></span>
* <span data-ttu-id="9b58b-123">選取 [ [ASP.NET Core 2.1 路由](?view=aspnetcore-2.1)]。</span><span class="sxs-lookup"><span data-stu-id="9b58b-123">Select [ASP.NET Core 2.1 routing](?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="9b58b-124">[查看或下載範例程式碼](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9b58b-124">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9b58b-125">這份檔的下載範例是由特定類別啟用 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-125">The download samples for this document are enabled by a specific `Startup` class.</span></span> <span data-ttu-id="9b58b-126">若要執行特定範例，請修改 *Program.cs* 以呼叫所需的 `Startup` 類別。</span><span class="sxs-lookup"><span data-stu-id="9b58b-126">To run a specific sample, modify *Program.cs* to call the desired `Startup` class.</span></span>

## <a name="routing-basics"></a><span data-ttu-id="9b58b-127">路由的基本概念</span><span class="sxs-lookup"><span data-stu-id="9b58b-127">Routing basics</span></span>

<span data-ttu-id="9b58b-128">所有 ASP.NET Core 範本都包含產生的程式碼中的路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-128">All ASP.NET Core templates include routing in the generated code.</span></span> <span data-ttu-id="9b58b-129">路由是在的 [中介軟體](xref:fundamentals/middleware/index) 管線中註冊 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-129">Routing is registered in the [middleware](xref:fundamentals/middleware/index) pipeline in `Startup.Configure`.</span></span>

<span data-ttu-id="9b58b-130">下列程式碼顯示路由的基本範例：</span><span class="sxs-lookup"><span data-stu-id="9b58b-130">The following code shows a basic example of routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

<span data-ttu-id="9b58b-131">路由會使用一對由和註冊的中介軟體 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-131">Routing uses a pair of middleware, registered by <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>:</span></span>

* <span data-ttu-id="9b58b-132">`UseRouting` 將路由對應新增至中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-132">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="9b58b-133">此中介軟體會查看應用程式中定義的端點集合，並根據要求選取 [最符合](#urlm) 的條件。</span><span class="sxs-lookup"><span data-stu-id="9b58b-133">This middleware looks at the set of endpoints defined in the app, and selects the [best match](#urlm) based on the request.</span></span>
* <span data-ttu-id="9b58b-134">`UseEndpoints` 將端點執行新增至中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-134">`UseEndpoints` adds endpoint execution to the middleware pipeline.</span></span> <span data-ttu-id="9b58b-135">它會執行與所選端點相關聯的委派。</span><span class="sxs-lookup"><span data-stu-id="9b58b-135">It runs the delegate associated with the selected endpoint.</span></span>

<span data-ttu-id="9b58b-136">上述範例包含使用[MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*)方法 *，將單一路由傳送至程式碼*端點：</span><span class="sxs-lookup"><span data-stu-id="9b58b-136">The preceding example includes a single *route to code* endpoint using the [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) method:</span></span>

* <span data-ttu-id="9b58b-137">當 HTTP `GET` 要求傳送至根 URL 時 `/` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-137">When an HTTP `GET` request is sent to the root URL `/`:</span></span>
  * <span data-ttu-id="9b58b-138">顯示的要求委派會執行。</span><span class="sxs-lookup"><span data-stu-id="9b58b-138">The request delegate shown executes.</span></span>
  * <span data-ttu-id="9b58b-139">`Hello World!` 會寫入 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="9b58b-139">`Hello World!` is written to the HTTP response.</span></span> <span data-ttu-id="9b58b-140">根據預設，根 URL `/` 為 `https://localhost:5001/` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-140">By default, the root URL `/` is `https://localhost:5001/`.</span></span>
* <span data-ttu-id="9b58b-141">如果要求方法不是 `GET` 或根 URL 不是，則 `/` 不符合任何路由，而且會傳回 HTTP 404。</span><span class="sxs-lookup"><span data-stu-id="9b58b-141">If the request method is not `GET` or the root URL is not `/`, no route matches and an HTTP 404 is returned.</span></span>

### <a name="endpoint"></a><span data-ttu-id="9b58b-142">端點</span><span class="sxs-lookup"><span data-stu-id="9b58b-142">Endpoint</span></span>

<a name="endpoint"></a>

<span data-ttu-id="9b58b-143">`MapGet`方法是用來定義**端點**。</span><span class="sxs-lookup"><span data-stu-id="9b58b-143">The `MapGet` method is used to define an **endpoint**.</span></span> <span data-ttu-id="9b58b-144">端點可以是：</span><span class="sxs-lookup"><span data-stu-id="9b58b-144">An endpoint is something that can be:</span></span>

* <span data-ttu-id="9b58b-145">選取此選項，藉由比對 URL 和 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-145">Selected, by matching the URL and HTTP method.</span></span>
* <span data-ttu-id="9b58b-146">執行委派。</span><span class="sxs-lookup"><span data-stu-id="9b58b-146">Executed, by running the delegate.</span></span>

<span data-ttu-id="9b58b-147">應用程式可比對和執行的端點會在中設定 `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-147">Endpoints that can be matched and executed by the app are configured in `UseEndpoints`.</span></span> <span data-ttu-id="9b58b-148">例如，、 <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> 和類似的 [方法](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) 會將要求委派連接至路由系統。</span><span class="sxs-lookup"><span data-stu-id="9b58b-148">For example, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, and [similar methods](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) connect request delegates to the routing system.</span></span>
<span data-ttu-id="9b58b-149">您可以使用其他方法，將 ASP.NET Core framework 功能連接到路由系統：</span><span class="sxs-lookup"><span data-stu-id="9b58b-149">Additional methods can be used to connect ASP.NET Core framework features to the routing system:</span></span>
- <span data-ttu-id="9b58b-150">[對應 Razor 頁面的頁面 Razor](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span><span class="sxs-lookup"><span data-stu-id="9b58b-150">[MapRazorPages for Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span></span>
- [<span data-ttu-id="9b58b-151">控制器的 MapControllers</span><span class="sxs-lookup"><span data-stu-id="9b58b-151">MapControllers for controllers</span></span>](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- <span data-ttu-id="9b58b-152">[MapHub \<THub>SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span><span class="sxs-lookup"><span data-stu-id="9b58b-152">[MapHub\<THub> for SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span></span> 
- [<span data-ttu-id="9b58b-153">\<TService>適用于 gRPC 的 MapGrpcService</span><span class="sxs-lookup"><span data-stu-id="9b58b-153">MapGrpcService\<TService> for gRPC</span></span>](xref:grpc/aspnetcore)

<span data-ttu-id="9b58b-154">下列範例顯示使用更精密的路由範本路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-154">The following example shows routing with a more sophisticated route template:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

<span data-ttu-id="9b58b-155">字串 `/hello/{name:alpha}` 是 **路由範本**。</span><span class="sxs-lookup"><span data-stu-id="9b58b-155">The string `/hello/{name:alpha}` is a **route template**.</span></span> <span data-ttu-id="9b58b-156">它是用來設定端點的相符方式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-156">It is used to configure how the endpoint is matched.</span></span> <span data-ttu-id="9b58b-157">在此情況下，範本會符合：</span><span class="sxs-lookup"><span data-stu-id="9b58b-157">In this case, the template matches:</span></span>

* <span data-ttu-id="9b58b-158">如下的 URL： `/hello/Ryan`</span><span class="sxs-lookup"><span data-stu-id="9b58b-158">A URL like `/hello/Ryan`</span></span>
* <span data-ttu-id="9b58b-159">開頭為字母字元序列的任何 URL 路徑 `/hello/` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-159">Any URL path that begins with `/hello/` followed by a sequence of alphabetic characters.</span></span>  <span data-ttu-id="9b58b-160">`:alpha` 套用僅符合字母字元的路由條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-160">`:alpha` applies a route constraint that matches only alphabetic characters.</span></span> <span data-ttu-id="9b58b-161">本檔稍後會說明[路由條件約束](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-161">[Route constraints](#route-constraint-reference) are explained later in this document.</span></span>

<span data-ttu-id="9b58b-162">URL 路徑的第二個區段 `{name:alpha}` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-162">The second segment of the URL path, `{name:alpha}`:</span></span>

* <span data-ttu-id="9b58b-163">系結至 `name` 參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-163">Is bound to the `name` parameter.</span></span>
* <span data-ttu-id="9b58b-164">會被捕獲並儲存在 [HttpRequest. RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*)中。</span><span class="sxs-lookup"><span data-stu-id="9b58b-164">Is captured and stored in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).</span></span>

<span data-ttu-id="9b58b-165">本檔中所述的端點路由系統是 ASP.NET Core 3.0 的新功能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-165">The endpoint routing system described in this document is new as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="9b58b-166">不過，所有版本的 ASP.NET Core 都支援相同的一組路由範本功能和路由條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-166">However, all versions of ASP.NET Core support the same set of route template features and route constraints.</span></span>

<span data-ttu-id="9b58b-167">下列範例顯示使用健康情況 [檢查](xref:host-and-deploy/health-checks) 和授權的路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-167">The following example shows routing with [health checks](xref:host-and-deploy/health-checks) and authorization:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="9b58b-168">上述範例示範如何：</span><span class="sxs-lookup"><span data-stu-id="9b58b-168">The preceding example demonstrates how:</span></span>

* <span data-ttu-id="9b58b-169">授權中介軟體可以搭配路由使用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-169">The authorization middleware can be used with routing.</span></span>
* <span data-ttu-id="9b58b-170">端點可以用來設定授權行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-170">Endpoints can be used to configure authorization behavior.</span></span>

<span data-ttu-id="9b58b-171"><xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*>呼叫會新增健康情況檢查端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-171">The <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> call adds a health check endpoint.</span></span> <span data-ttu-id="9b58b-172">連結 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> 至此呼叫會將授權原則附加至端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-172">Chaining <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> on to this call attaches an authorization policy to the endpoint.</span></span>

<span data-ttu-id="9b58b-173">呼叫 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 並 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> 新增驗證和授權中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-173">Calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> adds the authentication and authorization middleware.</span></span> <span data-ttu-id="9b58b-174">這些中介軟體會在和之間放置， <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> `UseEndpoints` 如此它們才能：</span><span class="sxs-lookup"><span data-stu-id="9b58b-174">These middleware are placed between <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and `UseEndpoints` so that they can:</span></span>

* <span data-ttu-id="9b58b-175">查看所選取的端點 `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-175">See which endpoint was selected by `UseRouting`.</span></span>
* <span data-ttu-id="9b58b-176">先套用授權原則，再將其 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 分派至端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-176">Apply an authorization policy before <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> dispatches to the endpoint.</span></span>

<a name="metadata"></a>

### <a name="endpoint-metadata"></a><span data-ttu-id="9b58b-177">端點中繼資料</span><span class="sxs-lookup"><span data-stu-id="9b58b-177">Endpoint metadata</span></span>

<span data-ttu-id="9b58b-178">在上述範例中，有兩個端點，但只有健康情況檢查端點已附加授權原則。</span><span class="sxs-lookup"><span data-stu-id="9b58b-178">In the preceding example, there are two endpoints, but only the health check endpoint has an authorization policy attached.</span></span> <span data-ttu-id="9b58b-179">如果要求符合健康狀態檢查端點， `/healthz` 則會執行授權檢查。</span><span class="sxs-lookup"><span data-stu-id="9b58b-179">If the request matches the health check endpoint, `/healthz`, an authorization check is performed.</span></span> <span data-ttu-id="9b58b-180">這會示範端點可以附加額外的資料。</span><span class="sxs-lookup"><span data-stu-id="9b58b-180">This demonstrates that endpoints can have extra data attached to them.</span></span> <span data-ttu-id="9b58b-181">這項額外的資料稱為端點 **中繼資料**：</span><span class="sxs-lookup"><span data-stu-id="9b58b-181">This extra data is called endpoint **metadata**:</span></span>

* <span data-ttu-id="9b58b-182">中繼資料可以由路由感知中介軟體處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-182">The metadata can be processed by routing-aware middleware.</span></span>
* <span data-ttu-id="9b58b-183">中繼資料可以是任何 .NET 型別。</span><span class="sxs-lookup"><span data-stu-id="9b58b-183">The metadata can be of any .NET type.</span></span>

## <a name="routing-concepts"></a><span data-ttu-id="9b58b-184">路由概念</span><span class="sxs-lookup"><span data-stu-id="9b58b-184">Routing concepts</span></span>

<span data-ttu-id="9b58b-185">路由系統會藉由新增強大的 **端點** 概念，在中介軟體管線之上建立。</span><span class="sxs-lookup"><span data-stu-id="9b58b-185">The routing system builds on top of the middleware pipeline by adding the powerful **endpoint** concept.</span></span> <span data-ttu-id="9b58b-186">端點代表應用程式功能的單位，與路由、授權和任意數量的 ASP.NET Core 系統之間不同。</span><span class="sxs-lookup"><span data-stu-id="9b58b-186">Endpoints represent units of the app's functionality that are distinct from each other in terms of routing, authorization, and any number of ASP.NET Core's systems.</span></span>

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a><span data-ttu-id="9b58b-187">ASP.NET Core 端點定義</span><span class="sxs-lookup"><span data-stu-id="9b58b-187">ASP.NET Core endpoint definition</span></span>

<span data-ttu-id="9b58b-188">ASP.NET Core 端點為：</span><span class="sxs-lookup"><span data-stu-id="9b58b-188">An ASP.NET Core endpoint is:</span></span>

* <span data-ttu-id="9b58b-189">可執行檔：有 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-189">Executable: Has a <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.</span></span>
* <span data-ttu-id="9b58b-190">可擴充：具有 [中繼資料](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) 集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-190">Extensible: Has a [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) collection.</span></span>
* <span data-ttu-id="9b58b-191">可選取：有 [路由資訊](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-191">Selectable: Optionally, has [routing information](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).</span></span>
* <span data-ttu-id="9b58b-192">可列舉：透過從 DI 抓取來列出端點的集合 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> 。 [DI](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="9b58b-192">Enumerable: The collection of endpoints can be listed by retrieving the <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> from [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="9b58b-193">下列程式碼示範如何取出和檢查符合目前要求的端點：</span><span class="sxs-lookup"><span data-stu-id="9b58b-193">The following code shows how to retrieve and inspect the endpoint matching the current request:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

<span data-ttu-id="9b58b-194">您可以從取出端點（如果已選取） `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-194">The endpoint, if selected, can be retrieved from the `HttpContext`.</span></span> <span data-ttu-id="9b58b-195">可以檢查它的屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-195">Its properties can be inspected.</span></span> <span data-ttu-id="9b58b-196">端點物件是不可變的，無法在建立之後修改。</span><span class="sxs-lookup"><span data-stu-id="9b58b-196">Endpoint objects are immutable and cannot be modified after creation.</span></span> <span data-ttu-id="9b58b-197">最常見的端點類型是 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-197">The most common type of endpoint is a <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>.</span></span> <span data-ttu-id="9b58b-198">`RouteEndpoint` 包含可讓路由系統選取的資訊。</span><span class="sxs-lookup"><span data-stu-id="9b58b-198">`RouteEndpoint` includes information that allows it to be to selected by the routing system.</span></span>

<span data-ttu-id="9b58b-199">在上述程式碼中， [應用程式。使用](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) 會設定內嵌 [中介軟體](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-199">In the preceding code, [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configures an in-line [middleware](xref:fundamentals/middleware/index).</span></span>

<a name="mt"></a>

<span data-ttu-id="9b58b-200">下列程式碼顯示，視管線中呼叫的位置而定 `app.Use` ，可能不會有端點：</span><span class="sxs-lookup"><span data-stu-id="9b58b-200">The following code shows that, depending on where `app.Use` is called in the pipeline, there may not be an endpoint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

<span data-ttu-id="9b58b-201">上述範例會新增 `Console.WriteLine` 語句，以顯示是否已選取端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-201">This preceding sample adds `Console.WriteLine` statements that display whether or not an endpoint has been selected.</span></span> <span data-ttu-id="9b58b-202">為了清楚起見，此範例會將顯示名稱指派給所提供的 `/` 端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-202">For clarity, the sample assigns a display name to the provided `/` endpoint.</span></span>

<span data-ttu-id="9b58b-203">使用顯示的 URL 來執行此程式碼 `/` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-203">Running this code with a URL of `/` displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

<span data-ttu-id="9b58b-204">使用任何其他 URL 來執行此程式碼會顯示：</span><span class="sxs-lookup"><span data-stu-id="9b58b-204">Running this code with any other URL displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

<span data-ttu-id="9b58b-205">此輸出示範：</span><span class="sxs-lookup"><span data-stu-id="9b58b-205">This output demonstrates that:</span></span>

* <span data-ttu-id="9b58b-206">在呼叫之前，端點一律為 null `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-206">The endpoint is always null before `UseRouting` is called.</span></span>
* <span data-ttu-id="9b58b-207">如果找到相符的，則在和之間的端點為非 `UseRouting` null <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-207">If a match is found, the endpoint is non-null between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="9b58b-208">`UseEndpoints`當找到相符的情況時，中介軟體會是**終端**機。</span><span class="sxs-lookup"><span data-stu-id="9b58b-208">The `UseEndpoints` middleware is **terminal** when a match is found.</span></span> <span data-ttu-id="9b58b-209">本檔稍後會定義[終端中介軟體](#tm)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-209">[Terminal middleware](#tm) is defined later in this document.</span></span>
* <span data-ttu-id="9b58b-210">`UseEndpoints`只有在找不到相符的情況時，才會執行中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-210">The middleware after `UseEndpoints` execute only when no match is found.</span></span>

<span data-ttu-id="9b58b-211">`UseRouting`中介軟體會使用[task.setendpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*)方法，將端點附加至目前的內容。</span><span class="sxs-lookup"><span data-stu-id="9b58b-211">The `UseRouting` middleware uses the [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) method to attach the endpoint to the current context.</span></span> <span data-ttu-id="9b58b-212">您可以 `UseRouting` 使用自訂邏輯來取代中介軟體，但仍能獲得使用端點的優點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-212">It's possible to replace the `UseRouting` middleware with custom logic and still get the benefits of using endpoints.</span></span> <span data-ttu-id="9b58b-213">端點是低層級的基本類型，像是中介軟體，並不會與路由執行結合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-213">Endpoints are a low-level primitive like middleware, and aren't coupled to the routing implementation.</span></span> <span data-ttu-id="9b58b-214">大部分的應用程式都不需要以 `UseRouting` 自訂邏輯取代。</span><span class="sxs-lookup"><span data-stu-id="9b58b-214">Most apps don't need to replace `UseRouting` with custom logic.</span></span>

<span data-ttu-id="9b58b-215">`UseEndpoints`中介軟體是設計來與 `UseRouting` 中介軟體一起使用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-215">The `UseEndpoints` middleware is designed to be used in tandem with the `UseRouting` middleware.</span></span> <span data-ttu-id="9b58b-216">執行端點的核心邏輯並不複雜。</span><span class="sxs-lookup"><span data-stu-id="9b58b-216">The core logic to execute an endpoint isn't complicated.</span></span> <span data-ttu-id="9b58b-217">使用 <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> 取出端點，然後叫用其 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> 屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-217">Use <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> to retrieve the endpoint, and then invoke its <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> property.</span></span>

<span data-ttu-id="9b58b-218">下列程式碼會示範中介軟體如何影響或回應路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-218">The following code demonstrates how middleware can influence or react to routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="9b58b-219">上述範例會示範兩個重要概念：</span><span class="sxs-lookup"><span data-stu-id="9b58b-219">The preceding example demonstrates two important concepts:</span></span>

* <span data-ttu-id="9b58b-220">中介軟體可以先執行，才能 `UseRouting` 修改路由運作所依據的資料。</span><span class="sxs-lookup"><span data-stu-id="9b58b-220">Middleware can run before `UseRouting` to modify the data that routing operates upon.</span></span>
    * <span data-ttu-id="9b58b-221">通常在路由之前出現的中介軟體會修改要求的某些屬性，例如 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 、 <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> 或 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-221">Usually middleware that appears before routing modifies some property of the request, such as <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>, or <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.</span></span>
* <span data-ttu-id="9b58b-222">中介軟體可以在和之間執行， `UseRouting` <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 以便在端點執行之前處理路由的結果。</span><span class="sxs-lookup"><span data-stu-id="9b58b-222">Middleware can run between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> to process the results of routing before the endpoint is executed.</span></span>
    * <span data-ttu-id="9b58b-223">在和之間執行的中介軟體 `UseRouting` `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-223">Middleware that runs between `UseRouting` and `UseEndpoints`:</span></span>
      * <span data-ttu-id="9b58b-224">通常會檢查中繼資料來瞭解端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-224">Usually inspects metadata to understand the endpoints.</span></span>
      * <span data-ttu-id="9b58b-225">通常會依照和來進行安全性決策 `UseAuthorization` `UseCors` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-225">Often makes security decisions, as done by `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="9b58b-226">中介軟體和中繼資料的組合可讓您設定每個端點的原則。</span><span class="sxs-lookup"><span data-stu-id="9b58b-226">The combination of middleware and metadata allows configuring policies per-endpoint.</span></span>

<span data-ttu-id="9b58b-227">上述程式碼顯示支援每個端點原則的自訂中介軟體範例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-227">The preceding code shows an example of a custom middleware that supports per-endpoint policies.</span></span> <span data-ttu-id="9b58b-228">中介軟體會將敏感性資料的存取權 *審核記錄* 寫入主控台。</span><span class="sxs-lookup"><span data-stu-id="9b58b-228">The middleware writes an *audit log* of access to sensitive data to the console.</span></span> <span data-ttu-id="9b58b-229">中介軟體可以設定為使用中繼資料來 *審核* 端點 `AuditPolicyAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-229">The middleware can be configured to *audit* an endpoint with the `AuditPolicyAttribute` metadata.</span></span> <span data-ttu-id="9b58b-230">這個範例會示範 *選擇性* 模式，其中只會審核標示為機密的端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-230">This sample demonstrates an *opt-in* pattern where only endpoints that are marked as sensitive are audited.</span></span> <span data-ttu-id="9b58b-231">例如，您可以反向定義此邏輯，並針對未標示為安全的所有專案進行審核。</span><span class="sxs-lookup"><span data-stu-id="9b58b-231">It's possible to define this logic in reverse, auditing everything that isn't marked as safe, for example.</span></span> <span data-ttu-id="9b58b-232">端點中繼資料系統很有彈性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-232">The endpoint metadata system is flexible.</span></span> <span data-ttu-id="9b58b-233">您可以使用任何符合使用案例的方式來設計這個邏輯。</span><span class="sxs-lookup"><span data-stu-id="9b58b-233">This logic could be designed in whatever way suits the use case.</span></span>

<span data-ttu-id="9b58b-234">上述範例程式碼旨在示範端點的基本概念。</span><span class="sxs-lookup"><span data-stu-id="9b58b-234">The preceding sample code is intended to demonstrate the basic concepts of endpoints.</span></span> <span data-ttu-id="9b58b-235">**此範例不適合用于生產用途**。</span><span class="sxs-lookup"><span data-stu-id="9b58b-235">**The sample is not intended for production use**.</span></span> <span data-ttu-id="9b58b-236">更完整的 *audit 記錄* 中介軟體版本將會：</span><span class="sxs-lookup"><span data-stu-id="9b58b-236">A more complete version of an *audit log* middleware would:</span></span>

* <span data-ttu-id="9b58b-237">記錄到檔案或資料庫。</span><span class="sxs-lookup"><span data-stu-id="9b58b-237">Log to a file or database.</span></span>
* <span data-ttu-id="9b58b-238">包含詳細資料，例如使用者、IP 位址、機密端點的名稱等。</span><span class="sxs-lookup"><span data-stu-id="9b58b-238">Include details such as the user, IP address, name of the sensitive endpoint, and more.</span></span>

<span data-ttu-id="9b58b-239">稽核原則中繼資料 `AuditPolicyAttribute` 定義為，可 `Attribute` 讓您更輕鬆地使用以類別為基礎的架構，例如控制器和 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-239">The audit policy metadata `AuditPolicyAttribute` is defined as an `Attribute` for easier use with class-based frameworks such as controllers and SignalR.</span></span> <span data-ttu-id="9b58b-240">使用程式 *代碼的路由*時：</span><span class="sxs-lookup"><span data-stu-id="9b58b-240">When using *route to code*:</span></span>

* <span data-ttu-id="9b58b-241">中繼資料會與建立器 API 連接。</span><span class="sxs-lookup"><span data-stu-id="9b58b-241">Metadata is attached with a builder API.</span></span>
* <span data-ttu-id="9b58b-242">以類別為基礎的架構，會在建立端點時，在對應的方法和類別上包含所有屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-242">Class-based frameworks include all attributes on the corresponding method and class when creating endpoints.</span></span>

<span data-ttu-id="9b58b-243">元資料類型的最佳作法是將它們定義為介面或屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-243">The best practices for metadata types are to define them either as interfaces or attributes.</span></span> <span data-ttu-id="9b58b-244">介面和屬性可讓您重複使用程式碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-244">Interfaces and attributes allow code reuse.</span></span> <span data-ttu-id="9b58b-245">中繼資料系統具有彈性，且不會強加任何限制。</span><span class="sxs-lookup"><span data-stu-id="9b58b-245">The metadata system is flexible and doesn't impose any limitations.</span></span>

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a><span data-ttu-id="9b58b-246">比較終端中介軟體和路由</span><span class="sxs-lookup"><span data-stu-id="9b58b-246">Comparing a terminal middleware and routing</span></span>

<span data-ttu-id="9b58b-247">下列程式碼範例會對照使用中介軟體與使用路由的方式進行對比：</span><span class="sxs-lookup"><span data-stu-id="9b58b-247">The following code sample contrasts using middleware with using routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="9b58b-248">顯示的中介軟體樣式 `Approach 1:` 為 **終端中介軟體**。</span><span class="sxs-lookup"><span data-stu-id="9b58b-248">The style of middleware shown with `Approach 1:` is **terminal middleware**.</span></span> <span data-ttu-id="9b58b-249">它稱為「終端中介軟體」，因為它會執行相符的作業：</span><span class="sxs-lookup"><span data-stu-id="9b58b-249">It's called terminal middleware because it does a matching operation:</span></span>

* <span data-ttu-id="9b58b-250">上述範例中的比對作業 `Path == "/"` 適用于中介軟體和 `Path == "/Movie"` 路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-250">The matching operation in the preceding sample is `Path == "/"` for the middleware and `Path == "/Movie"` for routing.</span></span>
* <span data-ttu-id="9b58b-251">當相符成功時，它會執行一些功能並傳回，而不是叫用 `next` 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-251">When a match is successful, it executes some functionality and returns, rather than invoking the `next` middleware.</span></span>

<span data-ttu-id="9b58b-252">它稱為「終端中介軟體」，因為它會終止搜尋、執行一些功能，然後傳回。</span><span class="sxs-lookup"><span data-stu-id="9b58b-252">It's called terminal middleware because it terminates the search, executes some functionality, and then returns.</span></span>

<span data-ttu-id="9b58b-253">比較終端中介軟體和路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-253">Comparing a terminal middleware and routing:</span></span>
* <span data-ttu-id="9b58b-254">這兩種方法都允許終止處理管線：</span><span class="sxs-lookup"><span data-stu-id="9b58b-254">Both approaches allow terminating the processing pipeline:</span></span>
    * <span data-ttu-id="9b58b-255">中介軟體會藉由傳回而非叫用來終止管線 `next` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-255">Middleware terminates the pipeline by returning rather than invoking `next`.</span></span>
    * <span data-ttu-id="9b58b-256">端點一律為終端機。</span><span class="sxs-lookup"><span data-stu-id="9b58b-256">Endpoints are always terminal.</span></span>
* <span data-ttu-id="9b58b-257">終端中介軟體可將中介軟體放置於管線中的任意位置：</span><span class="sxs-lookup"><span data-stu-id="9b58b-257">Terminal middleware allows positioning the middleware at an arbitrary place in the pipeline:</span></span>
    * <span data-ttu-id="9b58b-258">端點會在的位置執行 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-258">Endpoints execute at the position of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="9b58b-259">終端中介軟體可讓任意程式碼判斷中介軟體何時符合：</span><span class="sxs-lookup"><span data-stu-id="9b58b-259">Terminal middleware allows arbitrary code to determine when the middleware matches:</span></span>
    * <span data-ttu-id="9b58b-260">自訂路由比對程式碼可能很冗長，而且難以正確寫入。</span><span class="sxs-lookup"><span data-stu-id="9b58b-260">Custom route matching code can be verbose and difficult to write correctly.</span></span>
    * <span data-ttu-id="9b58b-261">路由為一般應用程式提供簡單的解決方案。</span><span class="sxs-lookup"><span data-stu-id="9b58b-261">Routing provides straightforward solutions for typical apps.</span></span> <span data-ttu-id="9b58b-262">大部分的應用程式都不需要自訂路由對應程式碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-262">Most apps don't require custom route matching code.</span></span>
* <span data-ttu-id="9b58b-263">具有中介軟體的端點介面 `UseAuthorization` ，例如和 `UseCors` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-263">Endpoints interface with middleware such as `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="9b58b-264">使用終端中介軟體搭配 `UseAuthorization` 或 `UseCors` 需要手動與授權系統互動。</span><span class="sxs-lookup"><span data-stu-id="9b58b-264">Using a terminal middleware with `UseAuthorization` or `UseCors` requires manual interfacing with the authorization system.</span></span>

<span data-ttu-id="9b58b-265">[端點](#endpoint)會定義兩者：</span><span class="sxs-lookup"><span data-stu-id="9b58b-265">An [endpoint](#endpoint) defines both:</span></span>

* <span data-ttu-id="9b58b-266">用來處理要求的委派。</span><span class="sxs-lookup"><span data-stu-id="9b58b-266">A delegate to process requests.</span></span>
* <span data-ttu-id="9b58b-267">任意中繼資料的集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-267">A collection of arbitrary metadata.</span></span> <span data-ttu-id="9b58b-268">中繼資料是用來根據附加至每個端點的原則和設定，來實行跨領域考慮。</span><span class="sxs-lookup"><span data-stu-id="9b58b-268">The metadata is used to implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="9b58b-269">終端中介軟體可以是有效的工具，但可能需要：</span><span class="sxs-lookup"><span data-stu-id="9b58b-269">Terminal middleware can be an effective tool, but can require:</span></span>

* <span data-ttu-id="9b58b-270">大量的編碼和測試。</span><span class="sxs-lookup"><span data-stu-id="9b58b-270">A significant amount of coding and testing.</span></span>
* <span data-ttu-id="9b58b-271">手動與其他系統整合，以達成所需的彈性層級。</span><span class="sxs-lookup"><span data-stu-id="9b58b-271">Manual integration with other systems to achieve the desired level of flexibility.</span></span>

<span data-ttu-id="9b58b-272">在撰寫終端中介軟體之前，請考慮與路由整合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-272">Consider integrating with routing before writing a terminal middleware.</span></span>

<span data-ttu-id="9b58b-273">現有的終端中介軟體與 [地圖](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) 整合，或 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 通常可以轉換成路由感知端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-273">Existing terminal middleware that integrates with [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) or <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> can usually be turned into a routing aware endpoint.</span></span> <span data-ttu-id="9b58b-274">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) 示範路由器的模式：</span><span class="sxs-lookup"><span data-stu-id="9b58b-274">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) demonstrates the pattern for router-ware:</span></span>
* <span data-ttu-id="9b58b-275">在上撰寫擴充方法 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-275">Write an extension method on <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span>
* <span data-ttu-id="9b58b-276">使用建立內嵌中介軟體管線 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-276">Create a nested middleware pipeline using <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.</span></span>
* <span data-ttu-id="9b58b-277">將中介軟體附加至新的管線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-277">Attach the middleware to the new pipeline.</span></span> <span data-ttu-id="9b58b-278">在此案例中為 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-278">In this case, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.</span></span>
* <span data-ttu-id="9b58b-279"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> 的中介軟體管線 <xref:Microsoft.AspNetCore.Http.RequestDelegate> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-279"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> the middleware pipeline into a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="9b58b-280">呼叫 `Map` 並提供新的中介軟體管線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-280">Call `Map` and provide the new middleware pipeline.</span></span>
* <span data-ttu-id="9b58b-281">從擴充方法傳回所提供的產生器物件 `Map` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-281">Return the builder object provided by `Map` from the extension method.</span></span>

<span data-ttu-id="9b58b-282">下列程式碼示範如何使用 [MapHealthChecks](xref:host-and-deploy/health-checks)：</span><span class="sxs-lookup"><span data-stu-id="9b58b-282">The following code shows use of [MapHealthChecks](xref:host-and-deploy/health-checks):</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

<span data-ttu-id="9b58b-283">上述範例示範如何傳回產生器物件很重要。</span><span class="sxs-lookup"><span data-stu-id="9b58b-283">The preceding sample shows why returning the builder object is important.</span></span> <span data-ttu-id="9b58b-284">傳回 builder 物件可讓應用程式開發人員設定原則，例如端點的授權。</span><span class="sxs-lookup"><span data-stu-id="9b58b-284">Returning the builder object allows the app developer to configure policies such as authorization for the endpoint.</span></span> <span data-ttu-id="9b58b-285">在此範例中，健康情況檢查中介軟體沒有與授權系統的直接整合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-285">In this example, the health checks middleware has no direct integration with the authorization system.</span></span>

<span data-ttu-id="9b58b-286">中繼資料系統是為了回應使用終端中介軟體的擴充性作者所遇到的問題而建立的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-286">The metadata system was created in response to the problems encountered by extensibility authors using terminal middleware.</span></span> <span data-ttu-id="9b58b-287">每個中介軟體都會有問題，使其本身與授權系統整合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-287">It's problematic for each middleware to implement its own integration with the authorization system.</span></span>

<a name="urlm"></a>

### <a name="url-matching"></a><span data-ttu-id="9b58b-288">URL 比對</span><span class="sxs-lookup"><span data-stu-id="9b58b-288">URL matching</span></span>

* <span data-ttu-id="9b58b-289">這是路由將傳入要求對應至 [端點](#endpoint)的進程。</span><span class="sxs-lookup"><span data-stu-id="9b58b-289">Is the process by which routing matches an incoming request to an [endpoint](#endpoint).</span></span>
* <span data-ttu-id="9b58b-290">是以 URL 路徑和標頭中的資料為基礎。</span><span class="sxs-lookup"><span data-stu-id="9b58b-290">Is based on data in the URL path and headers.</span></span>
* <span data-ttu-id="9b58b-291">可以擴充以考慮要求中的任何資料。</span><span class="sxs-lookup"><span data-stu-id="9b58b-291">Can be extended to consider any data in the request.</span></span>

<span data-ttu-id="9b58b-292">當路由中介軟體執行時，它會 `Endpoint` 從目前的要求將和路由值設定為的 [要求功能](xref:fundamentals/request-features) <xref:Microsoft.AspNetCore.Http.HttpContext> ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-292">When a routing middleware executes, it sets an `Endpoint` and route values to a [request feature](xref:fundamentals/request-features) on the <xref:Microsoft.AspNetCore.Http.HttpContext> from the current request:</span></span>

* <span data-ttu-id="9b58b-293">呼叫 [HttpCoNtext GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) 會取得端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-293">Calling [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) gets the endpoint.</span></span>
* <span data-ttu-id="9b58b-294">`HttpRequest.RouteValues` 會取得路由值的集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-294">`HttpRequest.RouteValues` gets the collection of route values.</span></span>

<span data-ttu-id="9b58b-295">在路由中介軟體之後執行的[中介軟體](xref:fundamentals/middleware/index)可以檢查端點並採取動作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-295">[Middleware](xref:fundamentals/middleware/index) running after the routing middleware can inspect the endpoint and take action.</span></span> <span data-ttu-id="9b58b-296">例如，授權中介軟體可以詢問端點的元資料集合，以取得授權原則。</span><span class="sxs-lookup"><span data-stu-id="9b58b-296">For example, an authorization middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span> <span data-ttu-id="9b58b-297">執行要求處理管線中的所有中介軟體之後，會叫用所選端點的委派。</span><span class="sxs-lookup"><span data-stu-id="9b58b-297">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>

<span data-ttu-id="9b58b-298">端點路由中的路由系統負責制定所有分派決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-298">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="9b58b-299">因為中介軟體會根據選取的端點套用原則，所以請務必：</span><span class="sxs-lookup"><span data-stu-id="9b58b-299">Because the middleware applies policies based on the selected endpoint, it's important that:</span></span>

* <span data-ttu-id="9b58b-300">任何可能會影響分派的決策或安全性原則的應用程式，都是在路由系統內進行。</span><span class="sxs-lookup"><span data-stu-id="9b58b-300">Any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

> [!WARNING]
> <span data-ttu-id="9b58b-301">為了回溯相容性，當執行控制器或 Razor Pages 端點委派時， [>routecoNtext.routedata](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的屬性會根據到目前為止執行的要求處理，設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-301">For backwards-compatibility, when a Controller or Razor Pages endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>
>
> <span data-ttu-id="9b58b-302">`RouteContext`未來的版本將會將類型標示為過時：</span><span class="sxs-lookup"><span data-stu-id="9b58b-302">The `RouteContext` type will be marked obsolete in a future release:</span></span>
>
> * <span data-ttu-id="9b58b-303">遷移 `RouteData.Values` 至 `HttpRequest.RouteValues` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-303">Migrate `RouteData.Values` to `HttpRequest.RouteValues`.</span></span>
> * <span data-ttu-id="9b58b-304">遷移 `RouteData.DataTokens` 以從端點中繼資料取出 [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-304">Migrate `RouteData.DataTokens` to retrieve [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) from the endpoint metadata.</span></span>

<span data-ttu-id="9b58b-305">URL 比對會在一組可設定的階段中運作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-305">URL matching operates in a configurable set of phases.</span></span> <span data-ttu-id="9b58b-306">在每個階段中，輸出是一組相符專案。</span><span class="sxs-lookup"><span data-stu-id="9b58b-306">In each phase, the output is a set of matches.</span></span> <span data-ttu-id="9b58b-307">下一個階段可以進一步縮小相符專案集。</span><span class="sxs-lookup"><span data-stu-id="9b58b-307">The set of matches can be narrowed down further by the next phase.</span></span> <span data-ttu-id="9b58b-308">路由執行不保證符合端點的處理順序。</span><span class="sxs-lookup"><span data-stu-id="9b58b-308">The routing implementation does not guarantee a processing order for matching endpoints.</span></span> <span data-ttu-id="9b58b-309">系統會一次處理**所有**可能的相符專案。</span><span class="sxs-lookup"><span data-stu-id="9b58b-309">**All** possible matches are processed at once.</span></span> <span data-ttu-id="9b58b-310">URL 比對階段會依下列順序發生。</span><span class="sxs-lookup"><span data-stu-id="9b58b-310">The URL matching phases occur in the following order.</span></span> <span data-ttu-id="9b58b-311">ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="9b58b-311">ASP.NET Core:</span></span>

1. <span data-ttu-id="9b58b-312">針對一組端點和其路由範本處理 URL 路徑，以收集 **所有** 相符專案。</span><span class="sxs-lookup"><span data-stu-id="9b58b-312">Processes the URL path against the set of endpoints and their route templates, collecting **all** of the matches.</span></span>
1. <span data-ttu-id="9b58b-313">採用上述清單，並移除套用路由條件約束失敗的相符專案。</span><span class="sxs-lookup"><span data-stu-id="9b58b-313">Takes the preceding list and removes matches that fail with route constraints applied.</span></span>
1. <span data-ttu-id="9b58b-314">採用上述清單，並移除 [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) 實例失敗的相符專案。</span><span class="sxs-lookup"><span data-stu-id="9b58b-314">Takes the preceding list and removes matches that fail the set of [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) instances.</span></span>
1. <span data-ttu-id="9b58b-315">使用 [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) 從上述清單進行最後的決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-315">Uses the [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) to make a final decision from the preceding list.</span></span>

<span data-ttu-id="9b58b-316">端點清單會根據下列各項設定優先順序：</span><span class="sxs-lookup"><span data-stu-id="9b58b-316">The list of endpoints is prioritized according to:</span></span>

* <span data-ttu-id="9b58b-317">[RouteEndpoint 順序](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span><span class="sxs-lookup"><span data-stu-id="9b58b-317">The [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span></span>
* <span data-ttu-id="9b58b-318">[路由範本優先順序](#rtp)</span><span class="sxs-lookup"><span data-stu-id="9b58b-318">The [route template precedence](#rtp)</span></span>

<span data-ttu-id="9b58b-319">在到達之前，所有相符的端點都會在每個階段中處理 <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-319">All matching endpoints are processed in each phase until the <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> is reached.</span></span> <span data-ttu-id="9b58b-320">`EndpointSelector`是最後一個階段。</span><span class="sxs-lookup"><span data-stu-id="9b58b-320">The `EndpointSelector` is the final phase.</span></span> <span data-ttu-id="9b58b-321">它會從相符專案中選擇最高優先順序的端點，使其成為最符合專案。</span><span class="sxs-lookup"><span data-stu-id="9b58b-321">It chooses the highest priority endpoint from the matches as the best match.</span></span> <span data-ttu-id="9b58b-322">如果有其他相符專案具有與最相符的相同優先權，則會擲回不明確的 match 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9b58b-322">If there are other matches with the same priority as the best match, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="9b58b-323">路由優先順序是根據 **更明確** 的路由範本，以較高的優先順序來計算。</span><span class="sxs-lookup"><span data-stu-id="9b58b-323">The route precedence is computed based on a **more specific** route template being given a higher priority.</span></span> <span data-ttu-id="9b58b-324">例如，請考慮範本 `/hello` 和 `/{message}` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-324">For example, consider the templates `/hello` and `/{message}`:</span></span>

* <span data-ttu-id="9b58b-325">兩者都符合 URL 路徑 `/hello` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-325">Both match the URL path `/hello`.</span></span>
* <span data-ttu-id="9b58b-326">`/hello`  更明確，因此優先順序較高。</span><span class="sxs-lookup"><span data-stu-id="9b58b-326">`/hello`  is more specific and therefore higher priority.</span></span>

<span data-ttu-id="9b58b-327">一般而言，路由優先順序是針對實務中所使用的 URL 配置種類選擇最符合性的絕佳工作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-327">In general, route precedence does a good job of choosing the best match for the kinds of URL schemes used in practice.</span></span> <span data-ttu-id="9b58b-328"><xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order>只有在必要時才使用，以避免混淆。</span><span class="sxs-lookup"><span data-stu-id="9b58b-328">Use <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> only when necessary to avoid an ambiguity.</span></span>

<span data-ttu-id="9b58b-329">由於路由提供的擴充性種類，路由系統不可能事先計算不明確的路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-329">Due to the kinds of extensibility provided by routing, it isn't possible for the routing system to compute ahead of time the ambiguous routes.</span></span> <span data-ttu-id="9b58b-330">請考慮範例，例如路由範本 `/{message:alpha}` 和 `/{message:int}` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-330">Consider an example such as the route templates `/{message:alpha}` and `/{message:int}`:</span></span>

* <span data-ttu-id="9b58b-331">`alpha`條件約束只符合字母字元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-331">The `alpha` constraint matches only alphabetic characters.</span></span>
* <span data-ttu-id="9b58b-332">`int`條件約束只符合數位。</span><span class="sxs-lookup"><span data-stu-id="9b58b-332">The `int` constraint matches only numbers.</span></span>
* <span data-ttu-id="9b58b-333">這些範本具有相同的路由優先順序，但是沒有相符的單一 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-333">These templates have the same route precedence, but there's no single URL they both match.</span></span>
* <span data-ttu-id="9b58b-334">如果路由系統在啟動時回報了不明確的錯誤，則會封鎖此有效的使用案例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-334">If the routing system reported an ambiguity error at startup, it would block this valid use case.</span></span>

> [!WARNING]
>
> <span data-ttu-id="9b58b-335">內的作業順序 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 不會影響路由的行為，但有一個例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9b58b-335">The order of operations inside <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> doesn't influence the behavior of routing, with one exception.</span></span> <span data-ttu-id="9b58b-336"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 並根據叫用順序 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 值的順序，自動指派訂單值給其端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-336"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="9b58b-337">這會模擬控制器的長時間行為，而不會有路由系統提供與舊版路由執行相同的保證。</span><span class="sxs-lookup"><span data-stu-id="9b58b-337">This simulates long-time behavior of controllers without the routing system providing the same guarantees as older routing implementations.</span></span>
>
> <span data-ttu-id="9b58b-338">在舊版的路由執行中，您可以執行與路由處理順序相依性的路由擴充性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-338">In the legacy implementation of routing, it's possible to implement routing extensibility that has a dependency on the order in which routes are processed.</span></span> <span data-ttu-id="9b58b-339">ASP.NET Core 3.0 和更新版本中的端點路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-339">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>
> 
> * <span data-ttu-id="9b58b-340">沒有路由的概念。</span><span class="sxs-lookup"><span data-stu-id="9b58b-340">Doesn't have a concept of routes.</span></span>
> * <span data-ttu-id="9b58b-341">不提供排序保證。</span><span class="sxs-lookup"><span data-stu-id="9b58b-341">Doesn't provide ordering guarantees.</span></span> <span data-ttu-id="9b58b-342">所有端點會一次處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-342">All endpoints are processed at once.</span></span>

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a><span data-ttu-id="9b58b-343">路由範本優先順序和端點選取順序</span><span class="sxs-lookup"><span data-stu-id="9b58b-343">Route template precedence and endpoint selection order</span></span>

<span data-ttu-id="9b58b-344">[路由範本優先順序](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) 是一種系統，會根據特定的方式將值指派給每個路由範本。</span><span class="sxs-lookup"><span data-stu-id="9b58b-344">[Route template precedence](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) is a system that assigns each route template a value based on how specific it is.</span></span> <span data-ttu-id="9b58b-345">路由範本優先順序：</span><span class="sxs-lookup"><span data-stu-id="9b58b-345">Route template precedence:</span></span>

* <span data-ttu-id="9b58b-346">避免在常見案例中調整端點順序的需求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-346">Avoids the need to adjust the order of endpoints in common cases.</span></span>
* <span data-ttu-id="9b58b-347">嘗試符合一般意義的路由行為預期。</span><span class="sxs-lookup"><span data-stu-id="9b58b-347">Attempts to match the common-sense expectations of routing behavior.</span></span>

<span data-ttu-id="9b58b-348">例如，請考慮範本 `/Products/List` 和 `/Products/{id}` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-348">For example, consider templates `/Products/List` and `/Products/{id}`.</span></span> <span data-ttu-id="9b58b-349">假設 `/Products/List` 是比 URL 路徑更相符的結果，是合理的做法 `/Products/{id}` `/Products/List` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-349">It would be reasonable to assume that `/Products/List` is a better match than `/Products/{id}` for the URL path `/Products/List`.</span></span> <span data-ttu-id="9b58b-350">的運作方式是因為常 `/List` 值區段被視為具有比參數區段更好的優先順序 `/{id}` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-350">The works because the literal segment `/List` is considered to have better precedence than the parameter segment `/{id}`.</span></span>

<span data-ttu-id="9b58b-351">優先順序運作方式的詳細資料，與路由範本的定義方式有關：</span><span class="sxs-lookup"><span data-stu-id="9b58b-351">The details of how precedence works are coupled to how route templates are defined:</span></span>

* <span data-ttu-id="9b58b-352">具有更多區段的範本會被視為更具體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-352">Templates with more segments are considered more specific.</span></span>
* <span data-ttu-id="9b58b-353">具有常值文字的區段會被視為比參數區段更明確的部分。</span><span class="sxs-lookup"><span data-stu-id="9b58b-353">A segment with literal text is considered more specific than a parameter segment.</span></span>
* <span data-ttu-id="9b58b-354">具有條件約束的參數區段會被視為更明確的參數區段，而不會有。</span><span class="sxs-lookup"><span data-stu-id="9b58b-354">A parameter segment with a constraint is considered more specific than one without.</span></span>
* <span data-ttu-id="9b58b-355">複雜區段會被視為具有條件約束的參數區段。</span><span class="sxs-lookup"><span data-stu-id="9b58b-355">A complex segment is considered as specific as a parameter segment with a constraint.</span></span>
* <span data-ttu-id="9b58b-356">Catch-all 參數是最不明確的參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-356">Catch-all parameters are the least specific.</span></span> <span data-ttu-id="9b58b-357">請參閱[路由範本參考](#rtr)中的**全部攔截**，以取得有關 catch 所有路由的重要資訊。</span><span class="sxs-lookup"><span data-stu-id="9b58b-357">See **catch-all** in the [Route template reference](#rtr) for important information on catch-all routes.</span></span>

<span data-ttu-id="9b58b-358">如需確切值的參考，請參閱 [GitHub 上的原始程式碼](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-358">See the [source code on GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) for a reference of exact values.</span></span>

<a name="lg"></a>

### <a name="url-generation-concepts"></a><span data-ttu-id="9b58b-359">URL 產生概念</span><span class="sxs-lookup"><span data-stu-id="9b58b-359">URL generation concepts</span></span>

<span data-ttu-id="9b58b-360">URL 產生：</span><span class="sxs-lookup"><span data-stu-id="9b58b-360">URL generation:</span></span>

* <span data-ttu-id="9b58b-361">這是路由可以根據一組路由值來建立 URL 路徑的進程。</span><span class="sxs-lookup"><span data-stu-id="9b58b-361">Is the process by which routing can create a URL path based on a set of route values.</span></span>
* <span data-ttu-id="9b58b-362">允許端點和存取它們的 Url 之間的邏輯分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-362">Allows for a logical separation between endpoints and the URLs that access them.</span></span>

<span data-ttu-id="9b58b-363">端點路由包含 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API。</span><span class="sxs-lookup"><span data-stu-id="9b58b-363">Endpoint routing includes the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API.</span></span> <span data-ttu-id="9b58b-364">`LinkGenerator` 是可從 [DI](xref:fundamentals/dependency-injection)取得的單一服務。</span><span class="sxs-lookup"><span data-stu-id="9b58b-364">`LinkGenerator` is a singleton service available from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9b58b-365">`LinkGenerator`API 可以在執行要求的內容之外使用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-365">The `LinkGenerator` API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="9b58b-366">[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper)和依賴的案例（例如標籤協助程式 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 、HTML 協助程式和[動作結果](xref:mvc/controllers/actions)）會在[Tag Helpers](xref:mvc/views/tag-helpers/intro)內部使用 `LinkGenerator` API 來提供連結產生功能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-366">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the `LinkGenerator` API internally to provide link generating capabilities.</span></span>

<span data-ttu-id="9b58b-367">連結產生器背後支援的概念為「位址」\*\*\*\* 和「位址配置」\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-367">The link generator is backed by the concept of an **address** and **address schemes**.</span></span> <span data-ttu-id="9b58b-368">位址配置可讓您判斷應考慮用於連結產生的端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-368">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="9b58b-369">例如，許多使用者都熟悉的路由名稱和路由值案例，會將控制器和 Razor 頁面實作為位址配置。</span><span class="sxs-lookup"><span data-stu-id="9b58b-369">For example, the route name and route values scenarios many users are familiar with from controllers and Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="9b58b-370">連結產生器可以透過下列擴充方法連結至控制器和 Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="9b58b-370">The link generator can link to controllers and Razor Pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="9b58b-371">這些方法的多載接受包含的引數 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-371">Overloads of these methods accept arguments that include the `HttpContext`.</span></span> <span data-ttu-id="9b58b-372">這些方法在功能上相當於 [Url、Action](xref:System.Web.Mvc.UrlHelper.Action*) 和 [url。頁面](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)，但提供額外的彈性和選項。</span><span class="sxs-lookup"><span data-stu-id="9b58b-372">These methods are functionally equivalent to [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) and [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), but offer additional flexibility and options.</span></span>

<span data-ttu-id="9b58b-373">這些 `GetPath*` 方法最類似于 `Url.Action` 和 `Url.Page` ，因為它們會產生包含絕對路徑的 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-373">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page`, in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="9b58b-374">`GetUri*` 方法一律會產生包含配置和主機的絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-374">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="9b58b-375">接受 `HttpContext` 的方法會在執行要求的內容中產生 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-375">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="9b58b-376">除非覆寫，否則會使用來自執行要求的 [環境](#ambient) 路由值、URL 基底路徑、配置和主機。</span><span class="sxs-lookup"><span data-stu-id="9b58b-376">The [ambient](#ambient) route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="9b58b-377">呼叫 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 並指定一個位址。</span><span class="sxs-lookup"><span data-stu-id="9b58b-377"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="9b58b-378">執行下列兩個步驟來產生 URI：</span><span class="sxs-lookup"><span data-stu-id="9b58b-378">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="9b58b-379">將位址繫結至符合該位址的端點清單。</span><span class="sxs-lookup"><span data-stu-id="9b58b-379">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="9b58b-380">評估每個端點的 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern>，直到找到符合所提供值的路由模式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-380">Each endpoint's <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="9b58b-381">產生的輸出會與提供給連結產生器的其他 URI 組件合併並傳回。</span><span class="sxs-lookup"><span data-stu-id="9b58b-381">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="9b58b-382"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法支援適用於任何位址類型的標準連結產生功能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-382">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="9b58b-383">使用連結產生器最方便的方式，是透過執行特定網址類別型作業的擴充方法：</span><span class="sxs-lookup"><span data-stu-id="9b58b-383">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type:</span></span>

| <span data-ttu-id="9b58b-384">擴充方法</span><span class="sxs-lookup"><span data-stu-id="9b58b-384">Extension Method</span></span> | <span data-ttu-id="9b58b-385">說明</span><span class="sxs-lookup"><span data-stu-id="9b58b-385">Description</span></span> |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="9b58b-386">根據提供的值產生具有絕對路徑的 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-386">Generates a URI with an absolute path based on the provided values.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="9b58b-387">根據提供的值產生絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-387">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="9b58b-388">注意呼叫 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列影響：</span><span class="sxs-lookup"><span data-stu-id="9b58b-388">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="9b58b-389">使用 `GetUri*` 擴充方法，並注意應用程式組態不會驗證傳入要求的 `Host` 標頭。</span><span class="sxs-lookup"><span data-stu-id="9b58b-389">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="9b58b-390">如果連 `Host` 入要求的標頭未經過驗證，則可以將不受信任的要求輸入傳送回視圖或頁面中 uri 的用戶端。</span><span class="sxs-lookup"><span data-stu-id="9b58b-390">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view or page.</span></span> <span data-ttu-id="9b58b-391">建議所有生產應用程式將其伺服器設定為驗證 `Host` 標頭是否為已知有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-391">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="9b58b-392">使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>，並注意與 `Map` 或 `MapWhen` 搭配使用的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-392">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="9b58b-393">`Map*` 會變更執行要求的基底路徑，這會影響連結產生的輸出。</span><span class="sxs-lookup"><span data-stu-id="9b58b-393">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="9b58b-394">所有的 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允許指定基底路徑。</span><span class="sxs-lookup"><span data-stu-id="9b58b-394">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="9b58b-395">指定空的基底路徑，以復原對連結產生的 `Map*` 影響。</span><span class="sxs-lookup"><span data-stu-id="9b58b-395">Specify an empty base path to undo the `Map*` affect on link generation.</span></span>

### <a name="middleware-example"></a><span data-ttu-id="9b58b-396">中介軟體範例</span><span class="sxs-lookup"><span data-stu-id="9b58b-396">Middleware example</span></span>

<span data-ttu-id="9b58b-397">在下列範例中，中介軟體會使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 來建立動作方法的連結，以列出商店產品。</span><span class="sxs-lookup"><span data-stu-id="9b58b-397">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create a link to an action method that lists store products.</span></span> <span data-ttu-id="9b58b-398">使用連結產生器，方法是將它插入類別，然後呼叫 `GenerateLink` 可供應用程式中的任何類別使用：</span><span class="sxs-lookup"><span data-stu-id="9b58b-398">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a><span data-ttu-id="9b58b-399">路由範本參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-399">Route template reference</span></span>

<span data-ttu-id="9b58b-400">內 `{}` 的權杖會定義路由參數，這些參數會在路由相符時系結。</span><span class="sxs-lookup"><span data-stu-id="9b58b-400">Tokens within `{}` define route parameters that are bound if the route is matched.</span></span> <span data-ttu-id="9b58b-401">路由區段中可以定義一個以上的路由參數，但路由參數必須以常值分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-401">More than one route parameter can be defined in a route segment, but route parameters  must be separated by a literal value.</span></span> <span data-ttu-id="9b58b-402">例如，`{controller=Home}{action=Index}` 不是有效的路由，因為 `{controller}` 與 `{action}` 之間沒有任何常值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-402">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span>  <span data-ttu-id="9b58b-403">路由參數必須有名稱，而且可能會指定其他屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-403">Route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="9b58b-404">路由參數之外的常值文字 (例如，`{id}`) 和路徑分隔符號 `/` 必須符合 URL 中的文字。</span><span class="sxs-lookup"><span data-stu-id="9b58b-404">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="9b58b-405">文字比對不區分大小寫，而且會根據 URL 路徑的已解碼標記法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-405">Text matching is case-insensitive and based on the decoded representation of the URL's path.</span></span> <span data-ttu-id="9b58b-406">若要比對常值路由參數分隔符號 `{` 或 `}` ，請重複字元來將分隔符號取消。</span><span class="sxs-lookup"><span data-stu-id="9b58b-406">To match a literal route parameter delimiter `{` or `}`, escape the delimiter by repeating the character.</span></span> <span data-ttu-id="9b58b-407">例如， `{{` 或 `}}` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-407">For example `{{` or `}}`.</span></span>

<span data-ttu-id="9b58b-408">星號 `*` 或雙星號 `**` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-408">Asterisk `*` or double asterisk `**`:</span></span>

* <span data-ttu-id="9b58b-409">可以用來作為路由參數的前置詞，以系結至 URI 的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="9b58b-409">Can be used as a prefix to a route parameter to bind to the rest of the URI.</span></span>
* <span data-ttu-id="9b58b-410">稱為 **全部攔截** 參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-410">Are called a **catch-all** parameters.</span></span> <span data-ttu-id="9b58b-411">例如，`blog/{**slug}`：</span><span class="sxs-lookup"><span data-stu-id="9b58b-411">For example, `blog/{**slug}`:</span></span>
  * <span data-ttu-id="9b58b-412">比對以開頭的任何 URI， `/blog` 並在後面加上任何值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-412">Matches any URI that starts with `/blog` and has any value following it.</span></span>
  * <span data-ttu-id="9b58b-413">下列值 `/blog` 會指派給 [資訊](https://developer.mozilla.org/docs/Glossary/Slug) 區路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-413">The value following `/blog` is assigned to the [slug](https://developer.mozilla.org/docs/Glossary/Slug) route value.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<span data-ttu-id="9b58b-414">全部擷取參數也可以符合空字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-414">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="9b58b-415">當使用路由來產生 URL （包括路徑分隔符號）時，全部攔截參數會將適當的字元進行轉義 `/` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-415">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator `/` characters.</span></span> <span data-ttu-id="9b58b-416">例如，路由值為 `{ path = "my/path" }` 的路由 `foo/{*path}` 會產生 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-416">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="9b58b-417">請注意逸出的斜線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-417">Note the escaped forward slash.</span></span> <span data-ttu-id="9b58b-418">若要反覆存取路徑分隔符號字元，請使用 `**` 路由參數前置詞。</span><span class="sxs-lookup"><span data-stu-id="9b58b-418">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="9b58b-419">具有 `{ path = "my/path" }` 的路由 `foo/{**path}` 會產生 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-419">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="9b58b-420">URL 模式嘗試擷取具有選擇性副檔名的檔案名稱時，具有其他考量。</span><span class="sxs-lookup"><span data-stu-id="9b58b-420">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="9b58b-421">以範本 `files/{filename}.{ext?}` 為例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-421">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="9b58b-422">當 `filename` 和 `ext` 都存在值時，就會填入這兩個值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-422">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="9b58b-423">如果 `filename` URL 中只有存在的值，則路由會相符，因為尾端 `.` 是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-423">If only a value for `filename` exists in the URL, the route matches because the trailing `.` is  optional.</span></span> <span data-ttu-id="9b58b-424">下列 URL 符合此路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-424">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="9b58b-425">路由參數可能有「預設值」\*\*\*\*，指定方法是在參數名稱之後指定預設值，並以等號 (`=`) 分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-425">Route parameters may have **default values** designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="9b58b-426">例如，`{controller=Home}` 定義 `Home` 作為 `controller` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-426">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="9b58b-427">如果 URL 中沒有用於參數的任何值，則會使用預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-427">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="9b58b-428">路由參數是選擇性的，方法是將問號 (`?`) 附加到參數名稱的結尾。</span><span class="sxs-lookup"><span data-stu-id="9b58b-428">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name.</span></span> <span data-ttu-id="9b58b-429">例如，`id?`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-429">For example, `id?`.</span></span> <span data-ttu-id="9b58b-430">選擇性值與預設路由參數之間的差異如下：</span><span class="sxs-lookup"><span data-stu-id="9b58b-430">The difference between optional values and default route parameters is:</span></span>

* <span data-ttu-id="9b58b-431">具有預設值的路由參數一定會產生值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-431">A route parameter with a default value always produces a value.</span></span>
* <span data-ttu-id="9b58b-432">選擇性參數只有在要求 URL 提供值時才會有值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-432">An optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="9b58b-433">路由參數可能具有條件約束，這些條件約束必須符合與 URL 繫結的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-433">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="9b58b-434">在 `:` 路由參數名稱之後加入和條件約束名稱，會在路由參數上指定內嵌條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-434">Adding `:` and constraint name after the route parameter name specifies an inline constraint on a route parameter.</span></span> <span data-ttu-id="9b58b-435">如果條件約束需要引數，這些引數會在條件約束名稱後面以括弧 `(...)` 括住。</span><span class="sxs-lookup"><span data-stu-id="9b58b-435">If the constraint requires arguments, they're enclosed in parentheses `(...)` after the constraint name.</span></span> <span data-ttu-id="9b58b-436">您可以附加另一個和條件約束名稱，以指定多個 *內嵌條件約束* `:` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-436">Multiple *inline constraints* can be specified by appending another `:` and constraint name.</span></span>

<span data-ttu-id="9b58b-437">條件約束名稱和引述會傳遞至 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服務來建立 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的執行個體，以用於 URL 處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-437">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="9b58b-438">例如，路由範本 `blog/{article:minlength(10)}` 指定具有引數 `10` 的 `minlength` 條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-438">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="9b58b-439">如需路由條件約束詳細資訊和架構所提供的條件約束清單，請參閱[路由條件約束參考](#route-constraint-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-439">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="9b58b-440">路由參數也可以具有參數轉換器。</span><span class="sxs-lookup"><span data-stu-id="9b58b-440">Route parameters may also have parameter transformers.</span></span> <span data-ttu-id="9b58b-441">參數轉換程式會在產生連結並比對動作和頁面至 Url 時，轉換參數的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-441">Parameter transformers transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="9b58b-442">如同條件約束，您可以在 `:` 路由參數名稱之後加入和轉換程式名稱，以內嵌方式將參數轉換器新增至路由參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-442">Like constraints, parameter transformers can be added inline to a route parameter by adding a `:` and transformer name after the route parameter name.</span></span> <span data-ttu-id="9b58b-443">例如，路由範本 `blog/{article:slugify}` 會指定 `slugify` 轉換器。</span><span class="sxs-lookup"><span data-stu-id="9b58b-443">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="9b58b-444">如需參數轉換器的詳細資訊，請參閱[參數轉器參考](#parameter-transformer-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-444">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="9b58b-445">下表示范範例路由範本及其行為：</span><span class="sxs-lookup"><span data-stu-id="9b58b-445">The following table demonstrates example route templates and their behavior:</span></span>

| <span data-ttu-id="9b58b-446">路由範本</span><span class="sxs-lookup"><span data-stu-id="9b58b-446">Route Template</span></span>                           | <span data-ttu-id="9b58b-447">範例比對 URI</span><span class="sxs-lookup"><span data-stu-id="9b58b-447">Example Matching URI</span></span>    | <span data-ttu-id="9b58b-448">要求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="9b58b-448">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="9b58b-449">只比對單一路徑 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-449">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="9b58b-450">比對並將 `Page` 設定為 `Home`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-450">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="9b58b-451">比對並將 `Page` 設定為 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-451">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="9b58b-452">對應至 `Products` 控制器和 `List` 動作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-452">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="9b58b-453">會對應至 `Products`  `Details` 設定為123的控制器和動作 `id` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-453">Maps to the `Products` controller and  `Details` action with`id` set to 123.</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="9b58b-454">對應至 `Home` 控制器和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-454">Maps to the `Home` controller and `Index` method.</span></span> <span data-ttu-id="9b58b-455">已忽略 `id`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-455">`id` is ignored.</span></span>        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | <span data-ttu-id="9b58b-456">對應至 `Products` 控制器和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-456">Maps to the `Products` controller and `Index` method.</span></span> <span data-ttu-id="9b58b-457">已忽略 `id`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-457">`id` is ignored.</span></span>        |

<span data-ttu-id="9b58b-458">使用範本通常是最簡單的路由方式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-458">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="9b58b-459">條件約束和預設值也可以在路由範本外部指定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-459">Constraints and defaults can also be specified outside the route template.</span></span>

### <a name="complex-segments"></a><span data-ttu-id="9b58b-460">複雜區段</span><span class="sxs-lookup"><span data-stu-id="9b58b-460">Complex segments</span></span>

<span data-ttu-id="9b58b-461">複雜區段的處理方式是在 [非貪婪](#greedy) 的情況下，由右至左比對常值分隔符號。</span><span class="sxs-lookup"><span data-stu-id="9b58b-461">Complex segments are processed by matching up literal delimiters from right to left in a [non-greedy](#greedy) way.</span></span> <span data-ttu-id="9b58b-462">例如， `[Route("/a{b}c{d}")]` 是複雜區段。</span><span class="sxs-lookup"><span data-stu-id="9b58b-462">For example, `[Route("/a{b}c{d}")]` is a complex segment.</span></span>
<span data-ttu-id="9b58b-463">複雜的區段會以特定的方式運作，必須瞭解才能成功使用它們。</span><span class="sxs-lookup"><span data-stu-id="9b58b-463">Complex segments work in a particular way that must be understood to use them successfully.</span></span> <span data-ttu-id="9b58b-464">本章節中的範例會示範當分隔符號文字未出現在參數值內部時，複雜區段只會很適合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-464">The example in this section demonstrates why complex segments only really work well when the delimiter text doesn't appear inside the parameter values.</span></span> <span data-ttu-id="9b58b-465">使用 [RegEx](/dotnet/standard/base-types/regular-expressions) ，然後以手動方式將值解壓縮，對較複雜的案例而言是必要的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-465">Using a [regex](/dotnet/standard/base-types/regular-expressions) and then manually extracting the values is needed for more complex cases.</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="9b58b-466">這是路由使用範本和 URL 路徑執行的步驟摘要 `/a{b}c{d}` `/abcd` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-466">This is a summary of the steps that routing performs with the template `/a{b}c{d}` and the URL path `/abcd`.</span></span> <span data-ttu-id="9b58b-467">`|`用來協助視覺化演算法的運作方式：</span><span class="sxs-lookup"><span data-stu-id="9b58b-467">The `|` is used to help visualize how the algorithm works:</span></span>

* <span data-ttu-id="9b58b-468">第一個常值（由右至左）為 `c` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-468">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="9b58b-469">這 `/abcd` 會從右側搜尋並尋找 `/ab|c|d` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-469">So `/abcd` is searched from right and finds `/ab|c|d`.</span></span>
* <span data-ttu-id="9b58b-470">右邊 () 的所有內容 `d` 現在都會與 route 參數相符 `{d}` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-470">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="9b58b-471">下一個常值（由右至左）為 `a` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-471">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="9b58b-472">`/ab|c|d`搜尋從我們離職開始，然後 `a` 找到 `/|a|b|c|d` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-472">So `/ab|c|d` is searched starting where we left off, then `a` is found `/|a|b|c|d`.</span></span>
* <span data-ttu-id="9b58b-473">右邊 () 的值 `b` 現在會符合 route 參數 `{b}` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-473">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="9b58b-474">沒有剩餘的文字和剩餘的路由範本，因此這是相符的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-474">There is no remaining text and no remaining route template, so this is a match.</span></span>

<span data-ttu-id="9b58b-475">以下是使用相同範本和 URL 路徑的負面案例範例 `/a{b}c{d}` `/aabcd` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-475">Here's an example of a negative case using the same template `/a{b}c{d}` and the URL path `/aabcd`.</span></span> <span data-ttu-id="9b58b-476">`|`用來協助視覺化演算法的運作方式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-476">The `|` is used to help visualize how the algorithm works.</span></span> <span data-ttu-id="9b58b-477">此案例不是相符的，這會透過相同的演算法來說明：</span><span class="sxs-lookup"><span data-stu-id="9b58b-477">This case isn't a match, which is explained by the same algorithm:</span></span>
* <span data-ttu-id="9b58b-478">第一個常值（由右至左）為 `c` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-478">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="9b58b-479">這 `/aabcd` 會從右側搜尋並尋找 `/aab|c|d` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-479">So `/aabcd` is searched from right and finds `/aab|c|d`.</span></span>
* <span data-ttu-id="9b58b-480">右邊 () 的所有內容 `d` 現在都會與 route 參數相符 `{d}` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-480">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="9b58b-481">下一個常值（由右至左）為 `a` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-481">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="9b58b-482">`/aab|c|d`搜尋從我們離職開始，然後 `a` 找到 `/a|a|b|c|d` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-482">So `/aab|c|d` is searched starting where we left off, then `a` is found `/a|a|b|c|d`.</span></span>
* <span data-ttu-id="9b58b-483">右邊 () 的值 `b` 現在會符合 route 參數 `{b}` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-483">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="9b58b-484">此時有剩餘 `a` 的文字，但演算法已用完路由範本來剖析，因此這不相符。</span><span class="sxs-lookup"><span data-stu-id="9b58b-484">At this point there is remaining text `a`, but the algorithm has run out of route template to parse, so this is not a match.</span></span>

<span data-ttu-id="9b58b-485">因為比對演算法 [是非貪婪](#greedy)的：</span><span class="sxs-lookup"><span data-stu-id="9b58b-485">Since the matching algorithm is [non-greedy](#greedy):</span></span>

* <span data-ttu-id="9b58b-486">它會比對每個步驟中可能的最小文字數量。</span><span class="sxs-lookup"><span data-stu-id="9b58b-486">It matches the smallest amount of text possible in each step.</span></span>
* <span data-ttu-id="9b58b-487">在參數值內出現分隔符號值的任何情況下，都會導致不相符。</span><span class="sxs-lookup"><span data-stu-id="9b58b-487">Any case where the delimiter value appears inside the parameter values results in not matching.</span></span>

<span data-ttu-id="9b58b-488">正則運算式對比對行為提供更多的控制權。</span><span class="sxs-lookup"><span data-stu-id="9b58b-488">Regular expressions provide much more control over their matching behavior.</span></span>

<a name="greedy"></a>

<span data-ttu-id="9b58b-489">貪婪比對（也知道 [延遲](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)比對）符合最大的可能字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-489">Greedy matching, also know as [lazy matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), matches the largest possible string.</span></span> <span data-ttu-id="9b58b-490">非貪婪符合最小的可能字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-490">Non-greedy matches the smallest possible string.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="9b58b-491">路由條件約束參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-491">Route constraint reference</span></span>

<span data-ttu-id="9b58b-492">路由條件約束執行時機是出現符合傳入 URL 的項目，並將 URL 路徑語彙基元化成路由值時。</span><span class="sxs-lookup"><span data-stu-id="9b58b-492">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="9b58b-493">路由條件約束通常會檢查透過路由範本相關聯的路由值，並針對該值是否可接受，做出 true 或 false 的決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-493">Route constraints generally inspect the route value associated via the route template and make a true or false decision about whether the value is acceptable.</span></span> <span data-ttu-id="9b58b-494">某些路由條件約束會使用路由值以外的資料，以考慮是否可以路由要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-494">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="9b58b-495">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以依據其 HTTP 指令動詞接受或拒絕要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-495">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="9b58b-496">條件約束可用於路由要求和連結產生。</span><span class="sxs-lookup"><span data-stu-id="9b58b-496">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="9b58b-497">請勿針對輸入驗證使用條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-497">Don't use constraints for input validation.</span></span> <span data-ttu-id="9b58b-498">如果條件約束用於輸入驗證，則不正確輸入會導致找不到的 `404` 回應。</span><span class="sxs-lookup"><span data-stu-id="9b58b-498">If constraints are used for input validation, invalid input results in a `404` Not Found response.</span></span> <span data-ttu-id="9b58b-499">不正確輸入應產生 `400` 錯誤的要求，並顯示適當的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9b58b-499">Invalid input should produce a `400` Bad Request with an appropriate error message.</span></span> <span data-ttu-id="9b58b-500">路由條件約束會用來釐清類似的路由，而不是用來驗證特定路由的輸入。</span><span class="sxs-lookup"><span data-stu-id="9b58b-500">Route constraints are used to disambiguate similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="9b58b-501">下表示范範例路由條件約束及其預期行為：</span><span class="sxs-lookup"><span data-stu-id="9b58b-501">The following table demonstrates example route constraints and their expected behavior:</span></span>

| <span data-ttu-id="9b58b-502">constraint (條件約束)</span><span class="sxs-lookup"><span data-stu-id="9b58b-502">constraint</span></span> | <span data-ttu-id="9b58b-503">範例</span><span class="sxs-lookup"><span data-stu-id="9b58b-503">Example</span></span> | <span data-ttu-id="9b58b-504">範例相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-504">Example Matches</span></span> | <span data-ttu-id="9b58b-505">附註</span><span class="sxs-lookup"><span data-stu-id="9b58b-505">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="9b58b-506">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9b58b-506">`123456789`, `-123456789`</span></span> | <span data-ttu-id="9b58b-507">符合任何整數</span><span class="sxs-lookup"><span data-stu-id="9b58b-507">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="9b58b-508">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="9b58b-508">`true`, `FALSE`</span></span> | <span data-ttu-id="9b58b-509">符合 `true` 或 `false` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-509">Matches `true` or `false`.</span></span> <span data-ttu-id="9b58b-510">不區分大小寫</span><span class="sxs-lookup"><span data-stu-id="9b58b-510">Case-insensitive</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="9b58b-511">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="9b58b-511">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="9b58b-512">符合非變異 `DateTime` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-512">Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-513">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-513">See preceding warning.</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="9b58b-514">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="9b58b-514">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="9b58b-515">符合非變異 `decimal` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-515">Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-516">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-516">See preceding warning.</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="9b58b-517">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9b58b-517">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="9b58b-518">符合非變異 `double` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-518">Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-519">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-519">See preceding warning.</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="9b58b-520">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9b58b-520">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="9b58b-521">符合非變異 `float` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-521">Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-522">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-522">See preceding warning.</span></span>|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | <span data-ttu-id="9b58b-523">符合有效的 `Guid` 值</span><span class="sxs-lookup"><span data-stu-id="9b58b-523">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="9b58b-524">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9b58b-524">`123456789`, `-123456789`</span></span> | <span data-ttu-id="9b58b-525">符合有效的 `long` 值</span><span class="sxs-lookup"><span data-stu-id="9b58b-525">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="9b58b-526">字串必須至少有 4 個字元</span><span class="sxs-lookup"><span data-stu-id="9b58b-526">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | <span data-ttu-id="9b58b-527">字串不能超過 8 個字元</span><span class="sxs-lookup"><span data-stu-id="9b58b-527">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="9b58b-528">字串長度必須剛好是 12 個字元</span><span class="sxs-lookup"><span data-stu-id="9b58b-528">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="9b58b-529">字串長度必須至少有 8 個字元，但不能超過 16 個字元</span><span class="sxs-lookup"><span data-stu-id="9b58b-529">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="9b58b-530">整數值必須至少為 18</span><span class="sxs-lookup"><span data-stu-id="9b58b-530">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="9b58b-531">整數值不能超過 120</span><span class="sxs-lookup"><span data-stu-id="9b58b-531">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="9b58b-532">整數值必須至少為 18，但不能超過 120</span><span class="sxs-lookup"><span data-stu-id="9b58b-532">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="9b58b-533">字串必須包含一或多個字母字元，且不區分 `a` - `z` 大小寫。</span><span class="sxs-lookup"><span data-stu-id="9b58b-533">String must consist of one or more alphabetical characters, `a`-`z` and case-insensitive.</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="9b58b-534">字串必須符合正則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-534">String must match the regular expression.</span></span> <span data-ttu-id="9b58b-535">請參閱定義正則運算式的秘訣。</span><span class="sxs-lookup"><span data-stu-id="9b58b-535">See tips about defining a regular expression.</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="9b58b-536">用來強制執行在 URL 產生期間呈現非參數值</span><span class="sxs-lookup"><span data-stu-id="9b58b-536">Used to enforce that a non-parameter value is present during URL generation</span></span> |

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="9b58b-537">可以將多個冒號分隔的條件約束套用至單一參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-537">Multiple, colon delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="9b58b-538">例如，下列條件約束會將參數限制在 1 或更大的整數值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-538">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="9b58b-539">驗證 URL 和轉換成 CLR 類型的路由條件約束一律會使用不變的文化特性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-539">Route constraints that verify the URL and are converted to a CLR type always use the invariant culture.</span></span> <span data-ttu-id="9b58b-540">例如，轉換成 CLR 類型 `int` 或 `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-540">For example, conversion to the CLR type `int` or `DateTime`.</span></span> <span data-ttu-id="9b58b-541">這些條件約束假設 URL 不可當地語系化。</span><span class="sxs-lookup"><span data-stu-id="9b58b-541">These constraints assume that the URL is not localizable.</span></span> <span data-ttu-id="9b58b-542">架構提供的路由條件約束不會修改路由值中儲存的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-542">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="9b58b-543">所有從 URL 剖析而來的路由值會儲存為字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-543">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="9b58b-544">例如，`float` 條件約束會嘗試將路由值轉換成浮點數，但轉換的值只能用來確認它可以轉換成浮點數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-544">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

### <a name="regular-expressions-in-constraints"></a><span data-ttu-id="9b58b-545">條件約束中的正則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-545">Regular expressions in constraints</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="9b58b-546">您可以使用路由條件約束，將正則運算式指定為內嵌條件約束 `regex(...)` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-546">Regular expressions can be specified as inline constraints using the `regex(...)` route constraint.</span></span> <span data-ttu-id="9b58b-547">系列中的方法 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 也接受條件約束的物件常值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-547">Methods in the <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> family also accept an object literal of constraints.</span></span> <span data-ttu-id="9b58b-548">如果使用該表單，則會將字串值視為正則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-548">If that form is used, string values are interpreted as regular expressions.</span></span>

<span data-ttu-id="9b58b-549">下列程式碼會使用內嵌的 RegEx 條件約束：</span><span class="sxs-lookup"><span data-stu-id="9b58b-549">The following code uses an inline regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

<span data-ttu-id="9b58b-550">下列程式碼會使用物件常值來指定 RegEx 條件約束：</span><span class="sxs-lookup"><span data-stu-id="9b58b-550">The following code uses an object literal to specify a regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

<span data-ttu-id="9b58b-551">ASP.NET Core 架構將 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` 新增至規則運算式建構函式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-551">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="9b58b-552">如需這些成員的說明，請參閱 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-552">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="9b58b-553">正則運算式使用的分隔符號和標記類似于路由和 c # 語言所使用的標記。</span><span class="sxs-lookup"><span data-stu-id="9b58b-553">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="9b58b-554">規則運算式的語彙基元必須逸出。</span><span class="sxs-lookup"><span data-stu-id="9b58b-554">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="9b58b-555">若要 `^\d{3}-\d{2}-\d{4}$` 在內嵌條件約束中使用正則運算式，請使用下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="9b58b-555">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in an inline constraint, use one of the following:</span></span>

* <span data-ttu-id="9b58b-556">將 `\` 字串中提供的字元取代為 c # 原始檔中的字元，以將 `\\` `\` 字串 escape 字元換用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-556">Replace `\` characters provided in the string as `\\` characters in the C# source file in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="9b58b-557">[逐字字串常](/dotnet/csharp/language-reference/keywords/string)值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-557">[Verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="9b58b-558">若要將路由參數分隔符號 `{` 、 `}` 、 `[` 、 `]` ，請將運算式中的字元加倍，例如，、、 `{{` `}}` `[[` 、 `]]` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-558">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression, for example, `{{`, `}}`, `[[`, `]]`.</span></span> <span data-ttu-id="9b58b-559">下表顯示正則運算式及其轉義版本：</span><span class="sxs-lookup"><span data-stu-id="9b58b-559">The following table shows a regular expression and its escaped version:</span></span>

| <span data-ttu-id="9b58b-560">規則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-560">Regular expression</span></span>    | <span data-ttu-id="9b58b-561">已轉義的正則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-561">Escaped regular expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="9b58b-562">路由中使用的正則運算式通常以 `^` 字元開頭，並符合字串的開始位置。</span><span class="sxs-lookup"><span data-stu-id="9b58b-562">Regular expressions used in routing often start with the `^` character and match the starting position of the string.</span></span> <span data-ttu-id="9b58b-563">運算式的結尾通常是 `$` 字元，並符合字串的結尾。</span><span class="sxs-lookup"><span data-stu-id="9b58b-563">The expressions often end with the `$` character and match the end of the string.</span></span> <span data-ttu-id="9b58b-564">`^`和 `$` 字元可確保正則運算式符合整個路由參數值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-564">The `^` and `$` characters ensure that the regular expression matches the entire route parameter value.</span></span> <span data-ttu-id="9b58b-565">如果沒有 `^` 和 `$` 字元，正則運算式會比對字串內的任何子字串，這通常是不必要的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-565">Without the `^` and `$` characters, the regular expression matches any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="9b58b-566">下表提供範例，並說明它們符合或無法符合的原因：</span><span class="sxs-lookup"><span data-stu-id="9b58b-566">The following table provides examples and explains why they match or fail to match:</span></span>

| <span data-ttu-id="9b58b-567">運算是</span><span class="sxs-lookup"><span data-stu-id="9b58b-567">Expression</span></span>   | <span data-ttu-id="9b58b-568">String</span><span class="sxs-lookup"><span data-stu-id="9b58b-568">String</span></span>    | <span data-ttu-id="9b58b-569">相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-569">Match</span></span> | <span data-ttu-id="9b58b-570">註解</span><span class="sxs-lookup"><span data-stu-id="9b58b-570">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-571">hello</span><span class="sxs-lookup"><span data-stu-id="9b58b-571">hello</span></span>     | <span data-ttu-id="9b58b-572">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-572">Yes</span></span>   | <span data-ttu-id="9b58b-573">子字串相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-573">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-574">123abc456</span><span class="sxs-lookup"><span data-stu-id="9b58b-574">123abc456</span></span> | <span data-ttu-id="9b58b-575">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-575">Yes</span></span>   | <span data-ttu-id="9b58b-576">子字串相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-576">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-577">mz</span><span class="sxs-lookup"><span data-stu-id="9b58b-577">mz</span></span>        | <span data-ttu-id="9b58b-578">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-578">Yes</span></span>   | <span data-ttu-id="9b58b-579">符合運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-579">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-580">MZ</span><span class="sxs-lookup"><span data-stu-id="9b58b-580">MZ</span></span>        | <span data-ttu-id="9b58b-581">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-581">Yes</span></span>   | <span data-ttu-id="9b58b-582">不區分大小寫</span><span class="sxs-lookup"><span data-stu-id="9b58b-582">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="9b58b-583">hello</span><span class="sxs-lookup"><span data-stu-id="9b58b-583">hello</span></span>     | <span data-ttu-id="9b58b-584">否</span><span class="sxs-lookup"><span data-stu-id="9b58b-584">No</span></span>    | <span data-ttu-id="9b58b-585">請參閱上述的 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="9b58b-585">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="9b58b-586">123abc456</span><span class="sxs-lookup"><span data-stu-id="9b58b-586">123abc456</span></span> | <span data-ttu-id="9b58b-587">否</span><span class="sxs-lookup"><span data-stu-id="9b58b-587">No</span></span>    | <span data-ttu-id="9b58b-588">請參閱上述的 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="9b58b-588">See `^` and `$` above</span></span> |

<span data-ttu-id="9b58b-589">如需規則運算式語法的詳細資訊，請參閱 [.NET Framework 規則運算式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-589">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="9b58b-590">若要將參數限制為一組已知的可能值，請使用規則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-590">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="9b58b-591">例如，`{action:regex(^(list|get|create)$)}` 只會將 `action` 路由值與 `list`、`get` 或 `create` 相符。</span><span class="sxs-lookup"><span data-stu-id="9b58b-591">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="9b58b-592">如果已傳入條件約束字典，字串 `^(list|get|create)$` 則是對等項目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-592">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="9b58b-593">在條件約束字典中傳遞不符合其中一個已知條件約束的條件約束，也會被視為正則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-593">Constraints that are passed in the constraints dictionary that don't match one of the known constraints are also treated as regular expressions.</span></span> <span data-ttu-id="9b58b-594">在不符合其中一個已知條件約束的範本中傳遞的條件約束，不會被視為正則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-594">Constraints that are passed  within a template that don't match one of the known constraints are not treated as regular expressions.</span></span>

### <a name="custom-route-constraints"></a><span data-ttu-id="9b58b-595">自訂路由條件約束</span><span class="sxs-lookup"><span data-stu-id="9b58b-595">Custom route constraints</span></span>

<span data-ttu-id="9b58b-596">您可以藉由實作為介面來建立自訂路由條件約束 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-596">Custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="9b58b-597">`IRouteConstraint`介面包含 <xref:System.Web.Routing.IRouteConstraint.Match*> ， `true` 如果滿足條件約束，則會傳回， `false` 否則會傳回。</span><span class="sxs-lookup"><span data-stu-id="9b58b-597">The `IRouteConstraint` interface contains <xref:System.Web.Routing.IRouteConstraint.Match*>, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="9b58b-598">自訂路由條件約束很少需要。</span><span class="sxs-lookup"><span data-stu-id="9b58b-598">Custom route constraints are rarely needed.</span></span> <span data-ttu-id="9b58b-599">在執行自訂路由條件約束之前，請考慮使用替代方法，例如模型系結。</span><span class="sxs-lookup"><span data-stu-id="9b58b-599">Before implementing a custom route constraint, consider alternatives, such as model binding.</span></span>

<span data-ttu-id="9b58b-600">ASP.NET Core [條件約束](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) 資料夾提供建立條件約束的良好範例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-600">The ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) folder provides good examples of creating a constraints.</span></span> <span data-ttu-id="9b58b-601">例如， [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-601">For example, [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).</span></span>

<span data-ttu-id="9b58b-602">若要使用自訂 `IRouteConstraint` ，必須向 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 服務容器中的應用程式註冊路由條件約束類型。</span><span class="sxs-lookup"><span data-stu-id="9b58b-602">To use a custom `IRouteConstraint`, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the service container.</span></span> <span data-ttu-id="9b58b-603">`ConstraintMap` 是一個目錄，它將路由限制式機碼對應到可驗證那些限制式的 `IRouteConstraint` 實作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-603">A `ConstraintMap` is a dictionary that maps route constraint keys to `IRouteConstraint` implementations that validate those constraints.</span></span> <span data-ttu-id="9b58b-604">更新應用程式的 `ConstraintMap` 時，可在 `Startup.ConfigureServices` 中於進行 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼叫時更新，或透過使用 `services.Configure<RouteOptions>` 直接設定 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 來更新。</span><span class="sxs-lookup"><span data-stu-id="9b58b-604">An app's `ConstraintMap` can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="9b58b-605">例如：</span><span class="sxs-lookup"><span data-stu-id="9b58b-605">For example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

<span data-ttu-id="9b58b-606">上述條件約束適用于下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="9b58b-606">The preceding constraint is applied in the following code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<span data-ttu-id="9b58b-607">的執行 `MyCustomConstraint` 會防止套用 `0` 至路由參數：</span><span class="sxs-lookup"><span data-stu-id="9b58b-607">The implementation of `MyCustomConstraint` prevents `0` being applied to a route parameter:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="9b58b-608">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="9b58b-608">The preceding code:</span></span>

* <span data-ttu-id="9b58b-609">防止 `0` 在 `{id}` 路由區段中。</span><span class="sxs-lookup"><span data-stu-id="9b58b-609">Prevents `0` in the `{id}` segment of the route.</span></span>
* <span data-ttu-id="9b58b-610">會顯示，以提供執行自訂條件約束的基本範例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-610">Is shown to provide a basic example of implementing a custom constraint.</span></span> <span data-ttu-id="9b58b-611">它不應該用於生產應用程式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-611">It should not be used in a production app.</span></span>

<span data-ttu-id="9b58b-612">下列程式碼是一個更好的方法，可防止 `id` 包含的進行 `0` 處理：</span><span class="sxs-lookup"><span data-stu-id="9b58b-612">The following code is a better approach to preventing an `id` containing a `0` from being processed:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="9b58b-613">上述程式碼在此方法中具有下列優點 `MyCustomConstraint` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-613">The preceding code has the following advantages over the `MyCustomConstraint` approach:</span></span>

* <span data-ttu-id="9b58b-614">它不需要自訂條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-614">It doesn't require a custom constraint.</span></span>
* <span data-ttu-id="9b58b-615">當 route 參數包含時，它會傳回更具描述性的錯誤 `0` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-615">It returns a more descriptive error when the route parameter includes `0`.</span></span>

## <a name="parameter-transformer-reference"></a><span data-ttu-id="9b58b-616">參數轉換器參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-616">Parameter transformer reference</span></span>

<span data-ttu-id="9b58b-617">參數轉換程式：</span><span class="sxs-lookup"><span data-stu-id="9b58b-617">Parameter transformers:</span></span>

* <span data-ttu-id="9b58b-618">使用產生連結時執行 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-618">Execute when generating a link using <xref:Microsoft.AspNetCore.Routing.LinkGenerator>.</span></span>
* <span data-ttu-id="9b58b-619">實作 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-619">Implement <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.</span></span>
* <span data-ttu-id="9b58b-620">是使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 進行設定的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-620">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="9b58b-621">採用參數的路由值，並將它轉換為新的字串值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-621">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="9b58b-622">導致在所產生連結中使用已轉換的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-622">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="9b58b-623">例如，具有 `Url.Action(new { article = "MyTestArticle" })` 之路由模式 `blog\{article:slugify}` 中的自訂 `slugify` 參數轉換器，會產生 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-623">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="9b58b-624">請考慮下列 `IOutboundParameterTransformer` 實行：</span><span class="sxs-lookup"><span data-stu-id="9b58b-624">Consider the following `IOutboundParameterTransformer` implementation:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

<span data-ttu-id="9b58b-625">若要在路由模式中使用參數轉換，請使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 在中進行設定 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-625">To use a parameter transformer in a route pattern, configure it using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

<span data-ttu-id="9b58b-626">ASP.NET Core framework 會使用參數轉換器來轉換端點解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-626">The ASP.NET Core framework uses parameter transformers to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="9b58b-627">例如，參數轉換器會轉換用來符合 `area` 、、和的路由 `controller` 值 `action` `page` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-627">For example, parameter transformers transform the route values used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="9b58b-628">使用上述的路由範本，此動作 `SubscriptionManagementController.GetAll` 會與 URI 相符 `/subscription-management/get-all` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-628">With the preceding route template, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="9b58b-629">參數轉換器不會變更用來產生連結的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-629">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="9b58b-630">例如，`Url.Action("GetAll", "SubscriptionManagement")` 會輸出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-630">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="9b58b-631">ASP.NET Core 提供使用參數轉換器搭配產生之路由的 API 慣例：</span><span class="sxs-lookup"><span data-stu-id="9b58b-631">ASP.NET Core provides API conventions for using parameter transformers with generated routes:</span></span>

* <span data-ttu-id="9b58b-632"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName>MVC 慣例會將指定的參數轉換程式套用至應用程式中的所有屬性路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-632">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="9b58b-633">參數轉換程式會在被取代時轉換屬性路由語彙基元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-633">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="9b58b-634">如需詳細資訊，請參閱[使用參數轉換程式自訂語彙基元取代](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-634">For more information, see [Use a parameter transformer to customize token replacement](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="9b58b-635">Razor 頁面會使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API 慣例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-635">Razor Pages uses the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API convention.</span></span> <span data-ttu-id="9b58b-636">此慣例會將指定的參數轉換器套用至所有自動探索到的 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="9b58b-636">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="9b58b-637">參數轉換程式會轉換頁面路由的資料夾和檔案名區段 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-637">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="9b58b-638">如需詳細資訊，請參閱[使用參數轉換程式自訂頁面路由](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-638">For more information, see [Use a parameter transformer to customize page routes](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

<a name="ugr"></a>

## <a name="url-generation-reference"></a><span data-ttu-id="9b58b-639">URL 產生參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-639">URL generation reference</span></span>

<span data-ttu-id="9b58b-640">本章節包含 URL 產生所執行之演算法的參考。</span><span class="sxs-lookup"><span data-stu-id="9b58b-640">This section contains a reference for the algorithm implemented by URL generation.</span></span> <span data-ttu-id="9b58b-641">在實務上，最複雜的 URL 產生範例會使用控制器或 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="9b58b-641">In practice, most complex examples of URL generation use controllers or Razor Pages.</span></span> <span data-ttu-id="9b58b-642">如需其他資訊，請參閱  [控制器中的路由](xref:mvc/controllers/routing) 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-642">See  [routing in controllers](xref:mvc/controllers/routing) for additional information.</span></span>

<span data-ttu-id="9b58b-643">URL 產生進程的開頭是呼叫 [LinkGenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) 或類似的方法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-643">The URL generation process begins with a call to [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) or a similar method.</span></span> <span data-ttu-id="9b58b-644">提供的方法有位址、一組路由值，以及有關目前要求的選擇性資訊 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-644">The method is provided with an address, a set of route values, and optionally information about the current request from `HttpContext`.</span></span>

<span data-ttu-id="9b58b-645">第一個步驟是使用位址，以符合網址類別型的來解析一組候選端點 [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-645">The first step is to use the address to resolve a set of candidate endpoints using an [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) that matches the address's type.</span></span>

<span data-ttu-id="9b58b-646">一旦位址配置找到一組候選項目之後，就會反復排序和處理端點，直到 URL 產生作業成功為止。</span><span class="sxs-lookup"><span data-stu-id="9b58b-646">Once of set of candidates is found by the address scheme, the endpoints are ordered and processed iteratively until a URL generation operation succeeds.</span></span> <span data-ttu-id="9b58b-647">URL **產生不會檢查是否** 有多義性，傳回的第一個結果是最後的結果。</span><span class="sxs-lookup"><span data-stu-id="9b58b-647">URL generation does **not** check for ambiguities, the first result returned is the final result.</span></span>

### <a name="troubleshooting-url-generation-with-logging"></a><span data-ttu-id="9b58b-648">使用記錄針對 URL 產生進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="9b58b-648">Troubleshooting URL generation with logging</span></span>

<span data-ttu-id="9b58b-649">針對 URL 產生進行疑難排解的第一個步驟，是將的記錄層級設定 `Microsoft.AspNetCore.Routing` 為 `TRACE` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-649">The first step in troubleshooting URL generation is setting the logging level of `Microsoft.AspNetCore.Routing` to `TRACE`.</span></span> <span data-ttu-id="9b58b-650">`LinkGenerator` 記錄有關其處理的許多詳細資料，對於疑難排解問題可能很有用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-650">`LinkGenerator` logs many details about its processing which can be useful to troubleshoot problems.</span></span>

<span data-ttu-id="9b58b-651">如需 URL 產生的詳細資訊，請參閱 [url 產生參考](#ugr) 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-651">See [URL generation reference](#ugr) for details on URL generation.</span></span>

### <a name="addresses"></a><span data-ttu-id="9b58b-652">位址</span><span class="sxs-lookup"><span data-stu-id="9b58b-652">Addresses</span></span>

<span data-ttu-id="9b58b-653">位址是 URL 產生中用來將連結產生器的呼叫系結至一組候選端點的概念。</span><span class="sxs-lookup"><span data-stu-id="9b58b-653">Addresses are the concept in URL generation used to bind a call into the link generator to a set of candidate endpoints.</span></span>

<span data-ttu-id="9b58b-654">位址是一種可延伸的概念，預設會隨附兩個執行：</span><span class="sxs-lookup"><span data-stu-id="9b58b-654">Addresses are an extensible concept that come with two implementations by default:</span></span>

* <span data-ttu-id="9b58b-655">使用 *端點名稱* (`string`) 作為位址：</span><span class="sxs-lookup"><span data-stu-id="9b58b-655">Using *endpoint name* (`string`) as the address:</span></span>
    * <span data-ttu-id="9b58b-656">提供類似于 MVC 之路由名稱的功能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-656">Provides similar functionality to MVC's route name.</span></span>
    * <span data-ttu-id="9b58b-657">使用 <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> 元資料類型。</span><span class="sxs-lookup"><span data-stu-id="9b58b-657">Uses the <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> metadata type.</span></span>
    * <span data-ttu-id="9b58b-658">針對所有已註冊端點的中繼資料解析提供的字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-658">Resolves the provided string against the metadata of all registered endpoints.</span></span>
    * <span data-ttu-id="9b58b-659">如果多個端點使用相同的名稱，則會在啟動時擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9b58b-659">Throws an exception on startup if multiple endpoints use the same name.</span></span>
    * <span data-ttu-id="9b58b-660">建議用於在控制器和頁面之外使用一般用途 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-660">Recommended for general-purpose use outside of controllers and Razor Pages.</span></span>
* <span data-ttu-id="9b58b-661">使用 *路由值* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) 作為位址：</span><span class="sxs-lookup"><span data-stu-id="9b58b-661">Using *route values* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) as the address:</span></span>
    * <span data-ttu-id="9b58b-662">提供類似于控制器和 Razor 頁面舊版 URL 產生的功能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-662">Provides similar functionality to controllers and Razor Pages legacy URL generation.</span></span>
    * <span data-ttu-id="9b58b-663">非常複雜，可進行擴充和調試。</span><span class="sxs-lookup"><span data-stu-id="9b58b-663">Very complex to extend and debug.</span></span>
    * <span data-ttu-id="9b58b-664">提供所使用的實作為、標籤協助程式 `IUrlHelper` 、HTML 協助程式、動作結果等。</span><span class="sxs-lookup"><span data-stu-id="9b58b-664">Provides the implementation used by `IUrlHelper`, Tag Helpers, HTML Helpers, Action Results, etc.</span></span>

<span data-ttu-id="9b58b-665">位址配置的角色是將位址和相符端點之間的關聯設為任意準則：</span><span class="sxs-lookup"><span data-stu-id="9b58b-665">The role of the address scheme is to make the association between the address and matching endpoints by arbitrary criteria:</span></span>

* <span data-ttu-id="9b58b-666">端點名稱配置會執行基本的字典查閱。</span><span class="sxs-lookup"><span data-stu-id="9b58b-666">The endpoint name scheme performs a basic dictionary lookup.</span></span>
* <span data-ttu-id="9b58b-667">路由值配置具有複雜的集合演算法的最佳子集。</span><span class="sxs-lookup"><span data-stu-id="9b58b-667">The route values scheme has a complex best subset of set algorithm.</span></span>

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a><span data-ttu-id="9b58b-668">環境值和明確值</span><span class="sxs-lookup"><span data-stu-id="9b58b-668">Ambient values and explicit values</span></span>

<span data-ttu-id="9b58b-669">從目前的要求中，路由會存取目前要求的路由值 `HttpContext.Request.RouteValues` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-669">From the current request, routing accesses the route values of the current request `HttpContext.Request.RouteValues`.</span></span> <span data-ttu-id="9b58b-670">與目前要求相關聯的值稱為 **環境值**。</span><span class="sxs-lookup"><span data-stu-id="9b58b-670">The values associated with the current request are referred to as the **ambient values**.</span></span> <span data-ttu-id="9b58b-671">為了清楚起見，檔是指將傳入方法的路由值視為 **明確值**。</span><span class="sxs-lookup"><span data-stu-id="9b58b-671">For the purpose of clarity, the documentation refers to the route values passed in to methods as **explicit values**.</span></span>

<span data-ttu-id="9b58b-672">下列範例會顯示環境值和明確值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-672">The following example shows ambient values and explicit values.</span></span> <span data-ttu-id="9b58b-673">它會從目前的要求和明確的值提供環境 `{ id = 17, }` 值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-673">It provides ambient values from the current request and explicit values: `{ id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

<span data-ttu-id="9b58b-674">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="9b58b-674">The preceding code:</span></span>

* <span data-ttu-id="9b58b-675">傳回 `/Widget/Index/17`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-675">Returns `/Widget/Index/17`</span></span>
* <span data-ttu-id="9b58b-676">取得 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> Via [DI](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-676">Gets <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="9b58b-677">下列程式碼不提供任何環境值和明確的 `{ controller = "Home", action = "Subscribe", id = 17, }` 值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-677">The following code provides no ambient values and explicit values: `{ controller = "Home", action = "Subscribe", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

<span data-ttu-id="9b58b-678">上述方法會傳回 `/Home/Subscribe/17`</span><span class="sxs-lookup"><span data-stu-id="9b58b-678">The preceding  method returns `/Home/Subscribe/17`</span></span>

<span data-ttu-id="9b58b-679">中的下列程式碼會傳回 `WidgetController` `/Widget/Subscribe/17` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-679">The following code in the `WidgetController` returns `/Widget/Subscribe/17`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

<span data-ttu-id="9b58b-680">下列程式碼會從目前要求中的環境值和明確的值提供控制器 `{ action = "Edit", id = 17, }` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-680">The following code provides the controller from ambient values in the current request and explicit values: `{ action = "Edit", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

<span data-ttu-id="9b58b-681">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="9b58b-681">In the preceding code:</span></span>

* <span data-ttu-id="9b58b-682">`/Gadget/Edit/17` 傳回。</span><span class="sxs-lookup"><span data-stu-id="9b58b-682">`/Gadget/Edit/17` is returned.</span></span>
* <span data-ttu-id="9b58b-683"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> 取得 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-683"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> gets the <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.</span></span>
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
<span data-ttu-id="9b58b-684">使用動作方法的絕對路徑產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-684">generates a URL with an absolute path for an action method.</span></span> <span data-ttu-id="9b58b-685">URL 包含指定的 `action` 名稱和 `route` 值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-685">The URL contains the specified `action` name and `route` values.</span></span>

<span data-ttu-id="9b58b-686">下列程式碼會從目前的要求和明確的值提供環境 `{ page = "./Edit, id = 17, }` 值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-686">The following code provides ambient values from the current request and explicit values: `{ page = "./Edit, id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="9b58b-687">`url` `/Edit/17` 當 [編輯] Razor 頁面包含下列頁面指示詞時，上述程式碼會設定為：</span><span class="sxs-lookup"><span data-stu-id="9b58b-687">The preceding code sets `url` to  `/Edit/17` when the Edit Razor Page contains the following page directive:</span></span>

 `@page "{id:int}"`

<span data-ttu-id="9b58b-688">如果編輯頁面未包含 `"{id:int}"` 路由範本， `url` 則為 `/Edit?id=17` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-688">If the Edit page doesn't contain the `"{id:int}"` route template, `url` is `/Edit?id=17`.</span></span>

<span data-ttu-id="9b58b-689"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>除了此處所述的規則之外，MVC 的行為還會增加一層複雜性：</span><span class="sxs-lookup"><span data-stu-id="9b58b-689">The behavior of MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> adds a layer of complexity in addition to the rules described here:</span></span>

* <span data-ttu-id="9b58b-690">`IUrlHelper` 一律提供來自目前要求的路由值做為環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-690">`IUrlHelper` always provides the route values from the current request as ambient values.</span></span>
* <span data-ttu-id="9b58b-691">除非由開發人員覆寫，否則[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*)一律會將目前的 `action` 和 `controller` 路由值複製為明確值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-691">[IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) always copies the current `action` and `controller` route values as explicit values unless overridden by the developer.</span></span>
* <span data-ttu-id="9b58b-692">除非覆寫，否則[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)一律會將目前的 `page` 路由值複製為明確值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-692">[IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) always copies the current `page` route value as an explicit value unless overridden.</span></span> <!--by the user-->
* <span data-ttu-id="9b58b-693">`IUrlHelper.Page` 除非覆寫，否則一律以明確值覆寫目前的 `handler` 路由值 `null` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-693">`IUrlHelper.Page` always overrides the current `handler` route value with `null` as an explicit values unless overridden.</span></span>

<span data-ttu-id="9b58b-694">使用者通常會因為環境值的行為詳細資料而感到驚訝，因為 MVC 似乎不會遵循自己的規則。</span><span class="sxs-lookup"><span data-stu-id="9b58b-694">Users are often surprised by the behavioral details of ambient values, because MVC doesn't seem to follow its own rules.</span></span> <span data-ttu-id="9b58b-695">基於歷程記錄和相容性的理由，某些路由值（例如 `action` 、 `controller` 、 `page` 和） `handler` 有自己的特殊案例行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-695">For historical and compatibility reasons, certain route values such as `action`, `controller`, `page`, and `handler` have their own special-case behavior.</span></span>

<span data-ttu-id="9b58b-696">提供的對等功能 `LinkGenerator.GetPathByAction` 會 `LinkGenerator.GetPathByPage` `IUrlHelper` 針對相容性複製這些異常。</span><span class="sxs-lookup"><span data-stu-id="9b58b-696">The equivalent functionality provided by `LinkGenerator.GetPathByAction` and `LinkGenerator.GetPathByPage` duplicates these anomalies of `IUrlHelper` for compatibility.</span></span>

### <a name="url-generation-process"></a><span data-ttu-id="9b58b-697">URL 產生進程</span><span class="sxs-lookup"><span data-stu-id="9b58b-697">URL generation process</span></span>

<span data-ttu-id="9b58b-698">一旦找到候選端點集合之後，URL 產生演算法：</span><span class="sxs-lookup"><span data-stu-id="9b58b-698">Once the set of candidate endpoints are found, the URL generation algorithm:</span></span>

* <span data-ttu-id="9b58b-699">反復處理端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-699">Processes the endpoints iteratively.</span></span>
* <span data-ttu-id="9b58b-700">傳回第一個成功的結果。</span><span class="sxs-lookup"><span data-stu-id="9b58b-700">Returns the first successful result.</span></span>

<span data-ttu-id="9b58b-701">此程式中的第一個步驟是呼叫 **路由值失效**。</span><span class="sxs-lookup"><span data-stu-id="9b58b-701">The first step in this process is called **route value invalidation**.</span></span>  <span data-ttu-id="9b58b-702">路由值失效是路由用來決定應該使用環境值的路由值，以及應忽略哪些路由值的處理常式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-702">Route value invalidation is the process by which routing decides which route values from the ambient values should be used and which should be ignored.</span></span> <span data-ttu-id="9b58b-703">會考慮每個環境的值，並結合明確的值，或忽略此值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-703">Each ambient value is considered and either combined with the explicit values, or ignored.</span></span>

<span data-ttu-id="9b58b-704">考慮環境值角色的最佳方式，就是在某些常見的情況下，他們會嘗試儲存應用程式開發人員的輸入。</span><span class="sxs-lookup"><span data-stu-id="9b58b-704">The best way to think about the role of ambient values is that they attempt to save application developers typing, in some common cases.</span></span> <span data-ttu-id="9b58b-705">傳統上，環境值很有説明的情況與 MVC 相關：</span><span class="sxs-lookup"><span data-stu-id="9b58b-705">Traditionally, the scenarios where ambient values are helpful are related to MVC:</span></span>

* <span data-ttu-id="9b58b-706">連結至相同控制器中的另一個動作時，不需要指定控制器名稱。</span><span class="sxs-lookup"><span data-stu-id="9b58b-706">When linking to another action in the same controller, the controller name doesn't need to be specified.</span></span>
* <span data-ttu-id="9b58b-707">連結到相同區域中的另一個控制器時，不需要指定區功能變數名稱稱。</span><span class="sxs-lookup"><span data-stu-id="9b58b-707">When linking to another controller in the same area, the area name doesn't need to be specified.</span></span>
* <span data-ttu-id="9b58b-708">連結至相同的動作方法時，不需要指定路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-708">When linking to the same action method, route values don't need to be specified.</span></span>
* <span data-ttu-id="9b58b-709">連結到應用程式的另一個部分時，您不會想要在應用程式的該部分中執行沒有意義的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-709">When linking to another part of the app, you don't want to carry over route values that have no meaning in that part of the app.</span></span>

<span data-ttu-id="9b58b-710">呼叫或傳回傳回 `LinkGenerator` `IUrlHelper` 的 `null` 原因通常是因為不了解路由值失效。</span><span class="sxs-lookup"><span data-stu-id="9b58b-710">Calls to `LinkGenerator` or `IUrlHelper` that return `null` are usually caused by not understanding route value invalidation.</span></span> <span data-ttu-id="9b58b-711">明確指定更多路由值以查看是否能解決問題，以針對路由值失效進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="9b58b-711">Troubleshoot route value invalidation by explicitly specifying more of the route values to see if that solves the problem.</span></span>

<span data-ttu-id="9b58b-712">路由值失效的運作方式是假設應用程式的 URL 配置為階層式，且階層是由左至右形成的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-712">Route value invalidation works on the assumption that the app's URL scheme is hierarchical, with a hierarchy formed from left-to-right.</span></span> <span data-ttu-id="9b58b-713">請考慮使用基本控制器路由範本 `{controller}/{action}/{id?}` ，以直覺瞭解這在實務上的運作方式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-713">Consider the basic controller route template `{controller}/{action}/{id?}` to get an intuitive sense of how this works in practice.</span></span> <span data-ttu-id="9b58b-714">值的 **變更** 會使出現在右邊的所有路由值 **失效** 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-714">A **change** to a value **invalidates** all of the route values that appear to the right.</span></span> <span data-ttu-id="9b58b-715">這會反映階層的假設。</span><span class="sxs-lookup"><span data-stu-id="9b58b-715">This reflects the assumption about hierarchy.</span></span> <span data-ttu-id="9b58b-716">如果應用程式具有的環境值 `id` ，且此作業為指定了不同的值 `controller` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-716">If the app has an ambient value for `id`, and the operation specifies a different value for the `controller`:</span></span>

* <span data-ttu-id="9b58b-717">`id` 因為是左邊的，所以不會重複使用 `{controller}` `{id?}` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-717">`id` won't be reused because `{controller}` is to the left of `{id?}`.</span></span>

<span data-ttu-id="9b58b-718">示範此原則的一些範例：</span><span class="sxs-lookup"><span data-stu-id="9b58b-718">Some examples demonstrating this principle:</span></span>

* <span data-ttu-id="9b58b-719">如果明確的值包含的值 `id` ，的環境值會被 `id` 忽略。</span><span class="sxs-lookup"><span data-stu-id="9b58b-719">If the explicit values contain a value for `id`, the ambient value for `id` is ignored.</span></span> <span data-ttu-id="9b58b-720">您 `controller` 可以使用和的環境值 `action` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-720">The ambient values for `controller` and `action` can be used.</span></span>
* <span data-ttu-id="9b58b-721">如果明確的值包含的值 `action` ，的任何環境值 `action` 都會被忽略。</span><span class="sxs-lookup"><span data-stu-id="9b58b-721">If the explicit values contain a value for `action`, any ambient value for `action` is ignored.</span></span> <span data-ttu-id="9b58b-722">您可以使用的環境值 `controller` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-722">The ambient values for `controller` can be used.</span></span> <span data-ttu-id="9b58b-723">如果的明確值與 `action` 的環境值不同 `action` ，將 `id` 不會使用此值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-723">If the explicit value for `action` is different from the ambient value for `action`, the `id` value won't be used.</span></span>  <span data-ttu-id="9b58b-724">如果的明確值與 `action` 的環境值相同 `action` ，則 `id` 可以使用此值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-724">If the explicit value for `action` is the same as the ambient value for `action`, the `id` value can be used.</span></span>
* <span data-ttu-id="9b58b-725">如果明確的值包含的值 `controller` ，的任何環境值 `controller` 都會被忽略。</span><span class="sxs-lookup"><span data-stu-id="9b58b-725">If the explicit values contain a value for `controller`, any ambient value for `controller` is ignored.</span></span> <span data-ttu-id="9b58b-726">如果的明確值與 `controller` 的環境值不同 `controller` ，將 `action` 不會使用和 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-726">If the explicit value for `controller` is different from the ambient value for `controller`, the `action` and `id` values won't be used.</span></span> <span data-ttu-id="9b58b-727">如果的明確值與 `controller` 的環境值相同 `controller` ，則 `action` `id` 可以使用和值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-727">If the explicit value for `controller` is the same as the ambient value for `controller`, the `action` and `id` values can be used.</span></span>

<span data-ttu-id="9b58b-728">此程式會因為屬性路由和專用慣例路由的存在而變得更複雜。</span><span class="sxs-lookup"><span data-stu-id="9b58b-728">This process is further complicated by the existence of attribute routes and dedicated conventional routes.</span></span> <span data-ttu-id="9b58b-729">控制器慣例路由，例如 `{controller}/{action}/{id?}` 使用路由參數指定階層。</span><span class="sxs-lookup"><span data-stu-id="9b58b-729">Controller conventional routes such as `{controller}/{action}/{id?}` specify a hierarchy using route parameters.</span></span> <span data-ttu-id="9b58b-730">適用于控制器和頁面的 [專用傳統路由](xref:mvc/controllers/routing#dcr) 和 [屬性路由](xref:mvc/controllers/routing#ar) Razor ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-730">For [dedicated conventional routes](xref:mvc/controllers/routing#dcr) and [attribute routes](xref:mvc/controllers/routing#ar) to controllers and Razor Pages:</span></span>

* <span data-ttu-id="9b58b-731">有路由值的階層。</span><span class="sxs-lookup"><span data-stu-id="9b58b-731">There is a hierarchy of route values.</span></span>
* <span data-ttu-id="9b58b-732">它們不會出現在範本中。</span><span class="sxs-lookup"><span data-stu-id="9b58b-732">They don't appear in the template.</span></span>

<span data-ttu-id="9b58b-733">在這些情況下，URL 產生會定義 **必要的值** 概念。</span><span class="sxs-lookup"><span data-stu-id="9b58b-733">For these cases, URL generation defines the **required values** concept.</span></span> <span data-ttu-id="9b58b-734">控制器和頁面所建立的端點 Razor 都具有指定的值，可讓路由值失效運作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-734">Endpoints created by controllers and Razor Pages have required values specified that allow route value invalidation to work.</span></span>

<span data-ttu-id="9b58b-735">路由值失效演算法的詳細資料：</span><span class="sxs-lookup"><span data-stu-id="9b58b-735">The route value invalidation algorithm in detail:</span></span>

* <span data-ttu-id="9b58b-736">必要的值名稱會與路由參數合併，然後由左至右處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-736">The required value names are combined with the route parameters, then processed from left-to-right.</span></span>
* <span data-ttu-id="9b58b-737">針對每個參數，會比較環境值和明確值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-737">For each parameter, the ambient value and explicit value are compared:</span></span>
    * <span data-ttu-id="9b58b-738">如果環境值和明確值相同，進程會繼續執行。</span><span class="sxs-lookup"><span data-stu-id="9b58b-738">If the ambient value and explicit value are the same, the process continues.</span></span>
    * <span data-ttu-id="9b58b-739">如果環境值存在且明確值不是，則會在產生 URL 時使用環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-739">If the ambient value is present and the explicit value isn't, the ambient value is used when generating the URL.</span></span>
    * <span data-ttu-id="9b58b-740">如果環境值不存在且明確的值為，則會拒絕環境值和所有後續的環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-740">If the ambient value isn't present and the explicit value is, reject the ambient value and all subsequent ambient values.</span></span>
    * <span data-ttu-id="9b58b-741">如果有環境值和明確的值，而且這兩個值不同，則會拒絕環境值和所有後續的環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-741">If the ambient value and the explicit value are present, and the two values are different, reject the ambient value and all subsequent ambient values.</span></span>

<span data-ttu-id="9b58b-742">此時，URL 產生作業已準備好評估路由條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-742">At this point, the URL generation operation is ready to evaluate route constraints.</span></span> <span data-ttu-id="9b58b-743">接受的值集合會與提供給條件約束的參數預設值結合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-743">The set of accepted values is combined with the parameter default values, which is provided to constraints.</span></span> <span data-ttu-id="9b58b-744">如果所有條件約束都通過，則作業會繼續進行。</span><span class="sxs-lookup"><span data-stu-id="9b58b-744">If the constraints all pass, the operation continues.</span></span>

<span data-ttu-id="9b58b-745">接下來，可以使用 **接受的值** 來展開路由範本。</span><span class="sxs-lookup"><span data-stu-id="9b58b-745">Next, the **accepted values** can be used to expand the route template.</span></span> <span data-ttu-id="9b58b-746">路由範本的處理：</span><span class="sxs-lookup"><span data-stu-id="9b58b-746">The route template is processed:</span></span>

* <span data-ttu-id="9b58b-747">從左至右。</span><span class="sxs-lookup"><span data-stu-id="9b58b-747">From left-to-right.</span></span>
* <span data-ttu-id="9b58b-748">每個參數都有接受的值取代。</span><span class="sxs-lookup"><span data-stu-id="9b58b-748">Each parameter has its accepted value substituted.</span></span>
* <span data-ttu-id="9b58b-749">使用下列特殊案例：</span><span class="sxs-lookup"><span data-stu-id="9b58b-749">With the following special cases:</span></span>
  * <span data-ttu-id="9b58b-750">如果接受的值遺漏值，而且參數有預設值，則會使用預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-750">If the accepted values is missing a value and the parameter has a default value, the default value is used.</span></span>
  * <span data-ttu-id="9b58b-751">如果接受的值遺漏值，而且參數是選擇性的，則會繼續處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-751">If the accepted values is missing a value and the parameter is optional, processing continues.</span></span>
  * <span data-ttu-id="9b58b-752">如果遺漏選擇性參數右邊的任何路由參數都有值，則作業會失敗。</span><span class="sxs-lookup"><span data-stu-id="9b58b-752">If any route parameter to the right of a missing optional parameter has a value, the operation fails.</span></span>
  * <!-- review default-valued parameters optional parameters --> <span data-ttu-id="9b58b-753">可能的話，會折迭連續的預設值參數和選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-753">Contiguous default-valued parameters and optional parameters are collapsed where possible.</span></span>

<span data-ttu-id="9b58b-754">明確提供但不符合路由區段的值會新增至查詢字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-754">Values explicitly provided that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="9b58b-755">下表顯示使用路由範本 `{controller}/{action}/{id?}` 時的結果。</span><span class="sxs-lookup"><span data-stu-id="9b58b-755">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="9b58b-756">環境值</span><span class="sxs-lookup"><span data-stu-id="9b58b-756">Ambient Values</span></span>                     | <span data-ttu-id="9b58b-757">明確值</span><span class="sxs-lookup"><span data-stu-id="9b58b-757">Explicit Values</span></span>                        | <span data-ttu-id="9b58b-758">結果</span><span class="sxs-lookup"><span data-stu-id="9b58b-758">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="9b58b-759">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-759">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-760">action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-760">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="9b58b-761">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-761">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-762">controller = "Order", action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-762">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="9b58b-763">controller = "Home", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="9b58b-763">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="9b58b-764">action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-764">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="9b58b-765">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-765">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-766">action = "About", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="9b58b-766">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a><span data-ttu-id="9b58b-767">路由值失效的問題</span><span class="sxs-lookup"><span data-stu-id="9b58b-767">Problems with route value invalidation</span></span>

<span data-ttu-id="9b58b-768">從 ASP.NET Core 3.0 開始，舊版 ASP.NET Core 中使用的某些 URL 產生配置，不適用於 URL 產生。</span><span class="sxs-lookup"><span data-stu-id="9b58b-768">As of ASP.NET Core 3.0, some URL generation schemes used in earlier ASP.NET Core versions don't work well with URL generation.</span></span> <span data-ttu-id="9b58b-769">ASP.NET Core 小組計畫在未來的版本中新增功能來解決這些需求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-769">The ASP.NET Core team plans to add features to address these needs in a future release.</span></span> <span data-ttu-id="9b58b-770">現在，最好的解決方法是使用舊版路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-770">For now the best solution is to use legacy routing.</span></span>

<span data-ttu-id="9b58b-771">下列程式碼顯示路由不支援的 URL 產生配置範例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-771">The following code shows an example of a URL generation scheme that's not supported by routing.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

<span data-ttu-id="9b58b-772">在上述程式碼中， `culture` 會使用 route 參數進行當地語系化。</span><span class="sxs-lookup"><span data-stu-id="9b58b-772">In the preceding code, the `culture` route parameter is used for localization.</span></span> <span data-ttu-id="9b58b-773">希望將 `culture` 參數一律接受為環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-773">The desire is to have the `culture` parameter always accepted as an ambient value.</span></span> <span data-ttu-id="9b58b-774">不過， `culture` 因為所需的值運作方式，所以不接受參數作為環境值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-774">However, the `culture` parameter is not accepted as an ambient value because of the way required values work:</span></span>

* <span data-ttu-id="9b58b-775">在 `"default"` 路由範本中， `culture` 路由參數是左邊的 `controller` ，因此的變更將 `controller` 不會失效 `culture` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-775">In the `"default"` route template, the `culture` route parameter is to the left of `controller`, so changes to `controller` won't invalidate `culture`.</span></span>
* <span data-ttu-id="9b58b-776">在 `"blog"` 路由範本中， `culture` 路由參數會被視為 `controller` 出現在必要值中的右邊。</span><span class="sxs-lookup"><span data-stu-id="9b58b-776">In the `"blog"` route template, the `culture` route parameter is considered to be to the right of `controller`, which appears in the required values.</span></span>

## <a name="configuring-endpoint-metadata"></a><span data-ttu-id="9b58b-777">設定端點中繼資料</span><span class="sxs-lookup"><span data-stu-id="9b58b-777">Configuring endpoint metadata</span></span>

<span data-ttu-id="9b58b-778">下列連結提供設定端點中繼資料的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="9b58b-778">The following links provide information on configuring endpoint metadata:</span></span>

* [<span data-ttu-id="9b58b-779">使用端點路由來啟用 Cors</span><span class="sxs-lookup"><span data-stu-id="9b58b-779">Enable Cors with endpoint routing</span></span>](xref:security/cors#enable-cors-with-endpoint-routing)
* <span data-ttu-id="9b58b-780">使用自訂屬性的[IAuthorizationPolicyProvider 範例](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider) `[MinimumAgeAuthorize]`</span><span class="sxs-lookup"><span data-stu-id="9b58b-780">[IAuthorizationPolicyProvider sample](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider) using a custom `[MinimumAgeAuthorize]` attribute</span></span>
* <span data-ttu-id="9b58b-781">[使用 [授權] 屬性測試驗證](xref:security/authentication/identity#test-identity)</span><span class="sxs-lookup"><span data-stu-id="9b58b-781">[Test authentication with the [Authorize] attribute](xref:security/authentication/identity#test-identity)</span></span>
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* <span data-ttu-id="9b58b-782">[選取具有 [授權] 屬性的配置](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="9b58b-782">[Selecting the scheme with the [Authorize] attribute](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span></span>
* <span data-ttu-id="9b58b-783">[使用 [授權] 屬性套用原則](xref:security/authorization/policies#apply-policies-to-mvc-controllers)</span><span class="sxs-lookup"><span data-stu-id="9b58b-783">[Apply policies using the [Authorize] attribute](xref:security/authorization/policies#apply-policies-to-mvc-controllers)</span></span>
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a><span data-ttu-id="9b58b-784">使用 RequireHost 的路由中的主機相符</span><span class="sxs-lookup"><span data-stu-id="9b58b-784">Host matching in routes with RequireHost</span></span>

<span data-ttu-id="9b58b-785"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> 將條件約束套用至需要指定主機的路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-785"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> applies a constraint to the route which requires the specified host.</span></span> <span data-ttu-id="9b58b-786">`RequireHost`或[[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute)參數可以是：</span><span class="sxs-lookup"><span data-stu-id="9b58b-786">The `RequireHost` or [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) parameter can be:</span></span>

* <span data-ttu-id="9b58b-787">Host： `www.domain.com` ，符合 `www.domain.com` 任何埠。</span><span class="sxs-lookup"><span data-stu-id="9b58b-787">Host: `www.domain.com`, matches `www.domain.com` with any port.</span></span>
* <span data-ttu-id="9b58b-788">具有萬用字元的主機： `*.domain.com` 、 `www.domain.com` 比 `subdomain.domain.com` 對、或 `www.subdomain.domain.com` 在任何埠上。</span><span class="sxs-lookup"><span data-stu-id="9b58b-788">Host with wildcard: `*.domain.com`, matches `www.domain.com`, `subdomain.domain.com`, or `www.subdomain.domain.com` on any port.</span></span>
* <span data-ttu-id="9b58b-789">埠：與 `*:5000` 任何主機相符的埠5000。</span><span class="sxs-lookup"><span data-stu-id="9b58b-789">Port: `*:5000`, matches port 5000 with any host.</span></span>
* <span data-ttu-id="9b58b-790">主機和埠： `www.domain.com:5000` 或 `*.domain.com:5000` 符合主機和埠。</span><span class="sxs-lookup"><span data-stu-id="9b58b-790">Host and port: `www.domain.com:5000` or `*.domain.com:5000`, matches host and port.</span></span>

<span data-ttu-id="9b58b-791">您可以使用或來指定多個參數 `RequireHost` `[Host]` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-791">Multiple parameters can be specified using `RequireHost` or `[Host]`.</span></span> <span data-ttu-id="9b58b-792">條件約束符合任何參數的有效主機。</span><span class="sxs-lookup"><span data-stu-id="9b58b-792">The constraint  matches hosts valid for any of the parameters.</span></span> <span data-ttu-id="9b58b-793">例如， `[Host("domain.com", "*.domain.com")]` 符合 `domain.com` 、 `www.domain.com` 和 `subdomain.domain.com` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-793">For example, `[Host("domain.com", "*.domain.com")]` matches `domain.com`, `www.domain.com`, and `subdomain.domain.com`.</span></span>

<span data-ttu-id="9b58b-794">下列程式碼會使用 `RequireHost` 來要求路由上指定的主機：</span><span class="sxs-lookup"><span data-stu-id="9b58b-794">The following code uses `RequireHost` to require the specified host on the route:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

<span data-ttu-id="9b58b-795">下列程式碼會使用 `[Host]` 控制器上的屬性來要求任何指定的主機：</span><span class="sxs-lookup"><span data-stu-id="9b58b-795">The following code uses the `[Host]` attribute on the controller to require any of the specified hosts:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

<span data-ttu-id="9b58b-796">當 `[Host]` 屬性同時套用至控制器和動作方法時：</span><span class="sxs-lookup"><span data-stu-id="9b58b-796">When the `[Host]` attribute is applied to both the controller and action method:</span></span>

* <span data-ttu-id="9b58b-797">使用動作上的屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-797">The attribute on the action is used.</span></span>
* <span data-ttu-id="9b58b-798">控制器屬性會被忽略。</span><span class="sxs-lookup"><span data-stu-id="9b58b-798">The controller attribute is ignored.</span></span>

## <a name="performance-guidance-for-routing"></a><span data-ttu-id="9b58b-799">路由的效能指引</span><span class="sxs-lookup"><span data-stu-id="9b58b-799">Performance guidance for routing</span></span>

<span data-ttu-id="9b58b-800">大部分的路由會在 ASP.NET Core 3.0 中更新，以提升效能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-800">Most of routing was updated in ASP.NET Core 3.0 to increase performance.</span></span>

<span data-ttu-id="9b58b-801">當應用程式發生效能問題時，通常會將路由視為問題。</span><span class="sxs-lookup"><span data-stu-id="9b58b-801">When an app has performance problems, routing is often suspected as the problem.</span></span> <span data-ttu-id="9b58b-802">考慮路由的原因是，控制器和 Razor 頁面等架構會報告在架構中的記錄訊息內花費的時間量。</span><span class="sxs-lookup"><span data-stu-id="9b58b-802">The reason routing is suspected is that frameworks like controllers and Razor Pages report the amount of time spent inside the framework in their logging messages.</span></span> <span data-ttu-id="9b58b-803">當控制器所報告的時間與要求的總時間之間有顯著的差異時：</span><span class="sxs-lookup"><span data-stu-id="9b58b-803">When there's a significant difference between the time reported by controllers and the total time of the request:</span></span>

* <span data-ttu-id="9b58b-804">開發人員會將其應用程式程式碼消除為問題的根源。</span><span class="sxs-lookup"><span data-stu-id="9b58b-804">Developers eliminate their app code as the source of the problem.</span></span>
* <span data-ttu-id="9b58b-805">通常會假設路由是原因。</span><span class="sxs-lookup"><span data-stu-id="9b58b-805">It's common to assume routing is the cause.</span></span>

<span data-ttu-id="9b58b-806">路由是使用上千個端點測試的效能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-806">Routing is performance tested using thousands of endpoints.</span></span> <span data-ttu-id="9b58b-807">一般的應用程式不太可能只會因為太大而遇到效能問題。</span><span class="sxs-lookup"><span data-stu-id="9b58b-807">It's unlikely that a typical app will encounter a performance problem just by being too large.</span></span> <span data-ttu-id="9b58b-808">緩慢路由效能最常見的根本原因，通常是行為錯誤的自訂中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-808">The most common root cause of slow routing performance is usually a badly-behaving custom middleware.</span></span>

<span data-ttu-id="9b58b-809">下列程式碼範例示範縮小延遲來源的基本技術：</span><span class="sxs-lookup"><span data-stu-id="9b58b-809">This following code sample demonstrates a basic technique for narrowing down the source of delay:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

<span data-ttu-id="9b58b-810">到時間路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-810">To time routing:</span></span>

* <span data-ttu-id="9b58b-811">將每個中介軟體與先前程式碼中顯示的計時中介軟體複本交錯。</span><span class="sxs-lookup"><span data-stu-id="9b58b-811">Interleave each middleware with a copy of the timing middleware shown in the preceding code.</span></span>
* <span data-ttu-id="9b58b-812">加入唯一識別碼，將計時資料與程式碼產生關聯。</span><span class="sxs-lookup"><span data-stu-id="9b58b-812">Add a unique identifier to correlate the timing data with the code.</span></span>

<span data-ttu-id="9b58b-813">這是在很重要的情況下縮小延遲的基本方法，例如，超過 `10ms` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-813">This is a basic way to narrow down the delay when it's significant, for example, more than `10ms`.</span></span>  <span data-ttu-id="9b58b-814">減去 `Time 2` `Time 1` 中介軟體內花費的時間 `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-814">Subtracting `Time 2` from `Time 1` reports the time spent inside the `UseRouting` middleware.</span></span>

<span data-ttu-id="9b58b-815">下列程式碼會對先前的計時程式碼使用更精簡的方法：</span><span class="sxs-lookup"><span data-stu-id="9b58b-815">The following code uses a more compact approach to the preceding timing code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a><span data-ttu-id="9b58b-816">可能昂貴的路由功能</span><span class="sxs-lookup"><span data-stu-id="9b58b-816">Potentially expensive routing features</span></span>

<span data-ttu-id="9b58b-817">下列清單提供對路由功能的一些深入解析，相較于基本路由範本，這些功能相當昂貴：</span><span class="sxs-lookup"><span data-stu-id="9b58b-817">The following list provides some insight into routing features that are relatively expensive compared with basic route templates:</span></span>

* <span data-ttu-id="9b58b-818">正則運算式：可以撰寫很複雜的正則運算式，或具有少量輸入的長時間執行時間。</span><span class="sxs-lookup"><span data-stu-id="9b58b-818">Regular expressions: It's possible to write regular expressions that are complex, or have long running time with a small amount of input.</span></span>

* <span data-ttu-id="9b58b-819"> () 的複雜區段 `{x}-{y}-{z}` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-819">Complex segments (`{x}-{y}-{z}`):</span></span> 
  * <span data-ttu-id="9b58b-820">比剖析一般 URL 路徑區段更昂貴。</span><span class="sxs-lookup"><span data-stu-id="9b58b-820">Are significantly more expensive than parsing a regular URL path segment.</span></span>
  * <span data-ttu-id="9b58b-821">會產生更多的子字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-821">Result in many more substrings being allocated.</span></span>
  * <span data-ttu-id="9b58b-822">ASP.NET Core 3.0 路由效能更新中未更新複雜區段邏輯。</span><span class="sxs-lookup"><span data-stu-id="9b58b-822">The complex segment logic was not updated in ASP.NET Core 3.0 routing performance update.</span></span>

* <span data-ttu-id="9b58b-823">同步資料存取：許多複雜的應用程式在其路由中都有資料庫存取權。</span><span class="sxs-lookup"><span data-stu-id="9b58b-823">Synchronous data access: Many complex apps have database access as part of their routing.</span></span> <span data-ttu-id="9b58b-824">ASP.NET Core 2.2 和較早的路由可能無法提供支援資料庫存取路由的適當擴充性點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-824">ASP.NET Core 2.2 and earlier routing might not provide the right extensibility points to support database access routing.</span></span> <span data-ttu-id="9b58b-825">例如， <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 和 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 是同步的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-825">For example, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, and <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are synchronous.</span></span> <span data-ttu-id="9b58b-826">和等擴充點 <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> 都是非同步。</span><span class="sxs-lookup"><span data-stu-id="9b58b-826">Extensibility points such as <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> and <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> are asynchronous.</span></span>

## <a name="guidance-for-library-authors"></a><span data-ttu-id="9b58b-827">程式庫作者指南</span><span class="sxs-lookup"><span data-stu-id="9b58b-827">Guidance for library authors</span></span>

<span data-ttu-id="9b58b-828">本節包含程式庫作者在路由上建立的指引。</span><span class="sxs-lookup"><span data-stu-id="9b58b-828">This section contains guidance for library authors building on top of routing.</span></span> <span data-ttu-id="9b58b-829">這些詳細資料的目的是要確保應用程式開發人員使用延伸路由的程式庫和架構有良好的體驗。</span><span class="sxs-lookup"><span data-stu-id="9b58b-829">These details are intended to ensure that app developers have a good experience using libraries and frameworks that extend routing.</span></span>

### <a name="define-endpoints"></a><span data-ttu-id="9b58b-830">定義端點</span><span class="sxs-lookup"><span data-stu-id="9b58b-830">Define endpoints</span></span>

<span data-ttu-id="9b58b-831">若要建立使用路由進行 URL 比對的架構，請先定義以之上建立的使用者體驗 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-831">To create a framework that uses routing for URL matching, start by defining a user experience that builds on top of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>

<span data-ttu-id="9b58b-832">在之上**進行**build <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-832">**DO** build on top of <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span> <span data-ttu-id="9b58b-833">這可讓使用者使用其他 ASP.NET Core 功能來撰寫您的架構，而不會造成混淆。</span><span class="sxs-lookup"><span data-stu-id="9b58b-833">This allows users to compose your framework with other ASP.NET Core features without confusion.</span></span> <span data-ttu-id="9b58b-834">每個 ASP.NET Core 範本都包含路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-834">Every ASP.NET Core template includes routing.</span></span> <span data-ttu-id="9b58b-835">假設路由存在且熟悉使用者。</span><span class="sxs-lookup"><span data-stu-id="9b58b-835">Assume routing is present and familiar for users.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="9b58b-836">**確實** 從呼叫傳回密封的具象型別 `MapMyFramework(...)` <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-836">**DO** return a sealed concrete type from a call to `MapMyFramework(...)` that implements <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>.</span></span> <span data-ttu-id="9b58b-837">大部分 `Map...` 的 framework 方法都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-837">Most framework `Map...` methods follow this pattern.</span></span> <span data-ttu-id="9b58b-838">`IEndpointConventionBuilder`介面：</span><span class="sxs-lookup"><span data-stu-id="9b58b-838">The `IEndpointConventionBuilder` interface:</span></span>

* <span data-ttu-id="9b58b-839">允許中繼資料的可組合性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-839">Allows composability of metadata.</span></span>
* <span data-ttu-id="9b58b-840">的目標是各種擴充方法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-840">Is targeted by a variety of extension methods.</span></span>

<span data-ttu-id="9b58b-841">宣告您自己的型別可讓您將自己的架構專屬功能新增至建立器。</span><span class="sxs-lookup"><span data-stu-id="9b58b-841">Declaring your own type allows you to add your own framework-specific functionality to the builder.</span></span> <span data-ttu-id="9b58b-842">您可以包裝架構宣告的產生器，並將呼叫轉寄給它。</span><span class="sxs-lookup"><span data-stu-id="9b58b-842">It's ok to wrap a framework-declared builder and forward calls to it.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="9b58b-843">**請考慮** 撰寫您自己 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> 的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-843">**CONSIDER** writing your own <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>.</span></span> <span data-ttu-id="9b58b-844">`EndpointDataSource` 這是用來宣告和更新端點集合的低層級基本類型。</span><span class="sxs-lookup"><span data-stu-id="9b58b-844">`EndpointDataSource` is the low-level primitive for declaring and updating a collection of endpoints.</span></span> <span data-ttu-id="9b58b-845">`EndpointDataSource` 是控制器和頁面所使用的強大 API Razor 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-845">`EndpointDataSource` is a powerful API used by controllers and Razor Pages.</span></span>

<span data-ttu-id="9b58b-846">路由測試有一個基本的不可更新資料來源 [範例](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-846">The routing tests have a [basic example](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) of a non-updating data source.</span></span>

<span data-ttu-id="9b58b-847">預設**不**嘗試註冊 `EndpointDataSource` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-847">**DO NOT** attempt to register an `EndpointDataSource` by default.</span></span> <span data-ttu-id="9b58b-848">要求使用者在中註冊您的架構 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-848">Require users to register your framework in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span> <span data-ttu-id="9b58b-849">路由的原理是，預設不會包含任何專案，而且這 `UseEndpoints` 是註冊端點的位置。</span><span class="sxs-lookup"><span data-stu-id="9b58b-849">The philosophy of routing is that nothing is included by default, and that `UseEndpoints` is the place to register endpoints.</span></span>

### <a name="creating-routing-integrated-middleware"></a><span data-ttu-id="9b58b-850">建立路由整合中介軟體</span><span class="sxs-lookup"><span data-stu-id="9b58b-850">Creating routing-integrated middleware</span></span>

<span data-ttu-id="9b58b-851">**請考慮** 將元資料類型定義為介面。</span><span class="sxs-lookup"><span data-stu-id="9b58b-851">**CONSIDER** defining metadata types as an interface.</span></span>

<span data-ttu-id="9b58b-852">**請盡可能** 使用元資料類型做為類別和方法上的屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-852">**DO** make it possible to use metadata types as an attribute on classes and methods.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

<span data-ttu-id="9b58b-853">控制器和頁面等架構 Razor 支援將中繼資料屬性套用至類型和方法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-853">Frameworks like controllers and Razor Pages support applying metadata attributes to types and methods.</span></span> <span data-ttu-id="9b58b-854">如果您宣告元資料類型：</span><span class="sxs-lookup"><span data-stu-id="9b58b-854">If you declare metadata types:</span></span>

* <span data-ttu-id="9b58b-855">讓它們可作為 [屬性](/dotnet/csharp/programming-guide/concepts/attributes/)存取。</span><span class="sxs-lookup"><span data-stu-id="9b58b-855">Make them accessible as [attributes](/dotnet/csharp/programming-guide/concepts/attributes/).</span></span>
* <span data-ttu-id="9b58b-856">大部分的使用者都很熟悉套用屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-856">Most users are familiar with applying attributes.</span></span>

<span data-ttu-id="9b58b-857">將元資料類型宣告為介面，可增加另一層彈性：</span><span class="sxs-lookup"><span data-stu-id="9b58b-857">Declaring a metadata type as an interface adds another layer of flexibility:</span></span>

* <span data-ttu-id="9b58b-858">介面是可組合的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-858">Interfaces are composable.</span></span>
* <span data-ttu-id="9b58b-859">開發人員可以宣告自己的類型，以結合多個原則。</span><span class="sxs-lookup"><span data-stu-id="9b58b-859">Developers can declare their own types that combine multiple policies.</span></span>

<span data-ttu-id="9b58b-860">**請確定可以** 覆寫中繼資料，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="9b58b-860">**DO** make it possible to override metadata, as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

<span data-ttu-id="9b58b-861">遵循這些指導方針的最佳方式是避免定義 **標記中繼資料**：</span><span class="sxs-lookup"><span data-stu-id="9b58b-861">The best way to follow these guidelines is to avoid defining **marker metadata**:</span></span>

* <span data-ttu-id="9b58b-862">請不要只尋找元資料類型是否存在。</span><span class="sxs-lookup"><span data-stu-id="9b58b-862">Don't just look for the presence of a metadata type.</span></span>
* <span data-ttu-id="9b58b-863">在中繼資料上定義屬性，並檢查屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-863">Define a property on the metadata and check the property.</span></span>

<span data-ttu-id="9b58b-864">元資料集合已排序，並支援依優先順序覆寫。</span><span class="sxs-lookup"><span data-stu-id="9b58b-864">The metadata collection is ordered and supports overriding by priority.</span></span> <span data-ttu-id="9b58b-865">在控制器的案例中，動作方法上的中繼資料最特定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-865">In the case of controllers, metadata on the action method is most specific.</span></span>

<span data-ttu-id="9b58b-866">**讓中間** 件在使用和沒有路由的情況下很有用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-866">**DO** make middleware useful with and without routing.</span></span>

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization();
});
```

<span data-ttu-id="9b58b-867">作為此指導方針的範例，請考慮使用 `UseAuthorization` 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-867">As an example of this guideline, consider the `UseAuthorization` middleware.</span></span> <span data-ttu-id="9b58b-868">授權中介軟體可讓您傳入回原則。</span><span class="sxs-lookup"><span data-stu-id="9b58b-868">The authorization middleware allows you to pass in a fallback policy.</span></span> <!-- shown where?  (shown here) --> <span data-ttu-id="9b58b-869">若有指定，則會同時套用至這兩個原則：</span><span class="sxs-lookup"><span data-stu-id="9b58b-869">The fallback policy, if specified, applies to both:</span></span>

* <span data-ttu-id="9b58b-870">沒有指定原則的端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-870">Endpoints without a specified policy.</span></span>
* <span data-ttu-id="9b58b-871">不符合端點的要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-871">Requests that don't match an endpoint.</span></span>

<span data-ttu-id="9b58b-872">這使得授權中介軟體在路由的內容之外很有用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-872">This makes the authorization middleware useful outside of the context of routing.</span></span> <span data-ttu-id="9b58b-873">授權中介軟體可用於傳統中介軟體程式設計。</span><span class="sxs-lookup"><span data-stu-id="9b58b-873">The authorization middleware can be used for traditional middleware programming.</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9b58b-874">路由負責將要求 Uri 對應至端點，並將傳入要求分派至這些端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-874">Routing is responsible for mapping request URIs to endpoints and dispatching incoming requests to those endpoints.</span></span> <span data-ttu-id="9b58b-875">路由定義於應用程式，並在該應用程式啟動時進行設定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-875">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="9b58b-876">路由可以選擇性地從要求中所包含的 URL 擷取值，然後這些值就可用於處理要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-876">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="9b58b-877">使用應用程式中的路由資訊，路由也能夠產生對應至端點的 Url。</span><span class="sxs-lookup"><span data-stu-id="9b58b-877">Using route information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="9b58b-878">若要使用 ASP.NET Core 2.2 中的最新路由情節，請將[相容性版本](xref:mvc/compatibility-version)指定為 `Startup.ConfigureServices` 中的 MVC 服務註冊：</span><span class="sxs-lookup"><span data-stu-id="9b58b-878">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="9b58b-879"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 選項會決定路由功能應該在內部使用以端點為基礎的邏輯，或以 <xref:Microsoft.AspNetCore.Routing.IRouter> 為基礎的邏輯 (適用於 ASP.NET Core 2.1 或更舊版本)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-879">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="9b58b-880">當相容性版本設定為 2.2 或更新版本時，預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-880">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="9b58b-881">將值設定為 `false` 可使用舊版路由邏輯：</span><span class="sxs-lookup"><span data-stu-id="9b58b-881">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="9b58b-882">如需以 <xref:Microsoft.AspNetCore.Routing.IRouter> 為基礎之路由的詳細資訊，請參閱[本主題的 ASP.NET Core 2.1 版本](?view=aspnetcore-2.1)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-882">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](?view=aspnetcore-2.1).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9b58b-883">本文件涵蓋低階的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-883">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="9b58b-884">如需 ASP.NET Core MVC 路由的資訊，請參閱 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-884">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="9b58b-885">如需頁面中路由慣例的詳細資訊 Razor ，請參閱 <xref:razor-pages/razor-pages-conventions> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-885">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="9b58b-886">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9b58b-886">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="9b58b-887">路由的基本概念</span><span class="sxs-lookup"><span data-stu-id="9b58b-887">Routing basics</span></span>

<span data-ttu-id="9b58b-888">大部分應用程式都應該選擇基本的描述性路由傳送配置，讓 URL 可讀且有意義。</span><span class="sxs-lookup"><span data-stu-id="9b58b-888">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="9b58b-889">預設慣例路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="9b58b-889">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="9b58b-890">支援基本的描述性路由配置。</span><span class="sxs-lookup"><span data-stu-id="9b58b-890">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="9b58b-891">適合作為 UI 型應用程式的起點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-891">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="9b58b-892">開發人員通常會在使用 [屬性路由](xref:mvc/controllers/routing#attribute-routing) 或專用慣例路由的特殊情況下，將額外的簡易路由新增到應用程式的高流量區域中。</span><span class="sxs-lookup"><span data-stu-id="9b58b-892">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span> <span data-ttu-id="9b58b-893">特殊化的情況範例包括、blog 和電子商務端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-893">Specialized situations examples include, blog and ecommerce endpoints.</span></span>

<span data-ttu-id="9b58b-894">Web API 應該使用屬性路由傳送來將應用程式功能模型建構為作業由 HTTP 指令動詞代表的資源集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-894">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="9b58b-895">這表示相同邏輯資源上的許多作業（例如 GET 和 POST）都會使用相同的 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-895">This means that many operations, for example, GET, and POST, on the same logical resource use the same URL.</span></span> <span data-ttu-id="9b58b-896">屬性路由提供仔細設計 API 公用端點配置所需的控制層級。</span><span class="sxs-lookup"><span data-stu-id="9b58b-896">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="9b58b-897">Razor 頁面應用程式會使用預設的傳統路由，在應用程式的 *Pages* 資料夾中提供命名資源。</span><span class="sxs-lookup"><span data-stu-id="9b58b-897">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="9b58b-898">有其他慣例可讓您自訂 Razor 頁面路由行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-898">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="9b58b-899">如需詳細資訊，請參閱 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-899">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="9b58b-900">URL 產生支援允許在不需要硬式編碼的 URL 來連結應用程式的情況下開發應用程式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-900">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="9b58b-901">這項支援可讓您從基本路由設定開始，並在決定應用程式資源配置之後修改路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-901">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="9b58b-902">路由會使用 *端點* (`Endpoint`) 來代表應用程式中的邏輯端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-902">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="9b58b-903">端點會定義用來處理要求的一項委派及一個任意中繼資料集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-903">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="9b58b-904">中繼資料可根據附加至每個端點的原則和組態，來實作跨領域關注。</span><span class="sxs-lookup"><span data-stu-id="9b58b-904">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="9b58b-905">路由系統具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="9b58b-905">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="9b58b-906">使用路由範本語法以 Token 化路由參數來定義路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-906">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="9b58b-907">允許傳統式和屬性式端點組態。</span><span class="sxs-lookup"><span data-stu-id="9b58b-907">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="9b58b-908"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 可用來判斷 URL 參數是否包含對指定端點條件約束有效的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-908"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="9b58b-909">應用程式模型（例如 MVC/ Razor Pages）會註冊其所有端點，這些端點具有可預測的路由案例執行。</span><span class="sxs-lookup"><span data-stu-id="9b58b-909">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="9b58b-910">路由實作會在中介軟體管線需要時制定路由決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-910">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="9b58b-911">在路由中介軟體之後出現的中介軟體可以檢查指定要求 URI 的路由中介軟體端點決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-911">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="9b58b-912">您可以針對中介軟體管線中任何位置的應用程式，列舉其中的所有端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-912">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="9b58b-913">應用程式可以根據端點資訊使用路由來產生 URL (例如，針對重新導向或連結)，因此避免硬式編碼的 URL，這有助於可維護性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-913">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="9b58b-914">URL 是根據支援任意擴充性的位址所產生：</span><span class="sxs-lookup"><span data-stu-id="9b58b-914">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="9b58b-915">您可以在任何位置使用[相依性插入 (DI)](xref:fundamentals/dependency-injection) 來解析連結產生器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)，以產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-915">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="9b58b-916">如果無法透過 DI 使用連結產生器 API，<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 會提供方法來建立 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-916">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="9b58b-917">當 ASP.NET Core 2.2 中的端點路由發行時，端點連結僅限於 MVC/ Razor 頁面動作和頁面。</span><span class="sxs-lookup"><span data-stu-id="9b58b-917">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="9b58b-918">未來版本將規劃擴充端點連結功能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-918">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

<span data-ttu-id="9b58b-919">路由會透過 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 類別連線到[中介軟體](xref:fundamentals/middleware/index)管線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-919">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="9b58b-920">[ASP.NET CORE mvc](xref:mvc/overview) 會將路由新增至中介軟體管線，作為其設定的一部分，並處理 MVC 和 Razor 頁面應用程式中的路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-920">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="9b58b-921">若要了解如何使用路由作為獨立元件，請參閱[使用路由中介軟體](#use-routing-middleware)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-921">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="9b58b-922">URL 比對</span><span class="sxs-lookup"><span data-stu-id="9b58b-922">URL matching</span></span>

<span data-ttu-id="9b58b-923">URL 比對是路由用來將傳入要求分派給「端點」\*\* 的處理序。</span><span class="sxs-lookup"><span data-stu-id="9b58b-923">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="9b58b-924">這個處理序是基於 URL 路徑中的資料，但是可以擴展為考慮要求中的任何資料。</span><span class="sxs-lookup"><span data-stu-id="9b58b-924">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="9b58b-925">分派要求給不同處理常式的能力，是調整應用程式大小和複雜度的關鍵。</span><span class="sxs-lookup"><span data-stu-id="9b58b-925">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="9b58b-926">端點路由中的路由系統負責制定所有分派決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-926">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="9b58b-927">由於中介軟體會根據所選取的端點來套用原則，因此請務必在路由系統內制定可能影響分派或應用安全性原則的任何決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-927">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="9b58b-928">執行端點委派時，會根據到目前為止所執行的要求處理，將 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的屬性設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-928">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="9b58b-929">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是「路由值」\*\* 的字典，而路由值產生自路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-929">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="9b58b-930">這些值通常是透過將 URL 語彙基元化來決定，可以用來接受使用者輸入，或在應用程式內做出進一步的分派決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-930">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="9b58b-931">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是其他資料的屬性包，而這些資料與相符路由相關。</span><span class="sxs-lookup"><span data-stu-id="9b58b-931">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="9b58b-932">提供了 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 來支援與每個路由建立關聯的狀態資料，因此應用程式可以依據符合哪一個路由來制定決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-932"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="9b58b-933">這些是開發人員定義的值，**不會**以任何方式影響路由的行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-933">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="9b58b-934">此外，儲藏在 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以是任何類型，對比之下，[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 則必須可轉換成字串或可從字串轉換。</span><span class="sxs-lookup"><span data-stu-id="9b58b-934">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="9b58b-935">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是成功符合要求的參與路由清單。</span><span class="sxs-lookup"><span data-stu-id="9b58b-935">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="9b58b-936">路由可以用巢狀方式置於彼此內部。</span><span class="sxs-lookup"><span data-stu-id="9b58b-936">Routes can be nested inside of one another.</span></span> <span data-ttu-id="9b58b-937"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 屬性會透過導致產生相符項目的路由邏輯樹狀結構反映路徑。</span><span class="sxs-lookup"><span data-stu-id="9b58b-937">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="9b58b-938">一般而言，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一個項目是路由集合，應該用於產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-938">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="9b58b-939"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最後一個項目是相符的路由處理常式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-939">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="9b58b-940">使用 LinkGenerator 產生 URL</span><span class="sxs-lookup"><span data-stu-id="9b58b-940">URL generation with LinkGenerator</span></span>

<span data-ttu-id="9b58b-941">URL 產生是路由可用來依據一組路由值建立 URL 路徑的處理序。</span><span class="sxs-lookup"><span data-stu-id="9b58b-941">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="9b58b-942">這可讓您在端點和存取它們的 URL 之間建立邏輯分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-942">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="9b58b-943">端點路由包含連結產生器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-943">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="9b58b-944"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 是可從 [DI](xref:fundamentals/dependency-injection)取出的單一服務。</span><span class="sxs-lookup"><span data-stu-id="9b58b-944"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9b58b-945">您可以在執行要求內容外部使用此 API。</span><span class="sxs-lookup"><span data-stu-id="9b58b-945">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="9b58b-946">MVC 的 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 及依賴 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的情節 (例如[標籤協助程式](xref:mvc/views/tag-helpers/intro)、HTML 協助程式和[動作結果](xref:mvc/controllers/actions)) 均使用連結產生器來提供連結產生功能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-946">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="9b58b-947">連結產生器背後支援的概念為「位址」\*\* 和「位址配置」\*\*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-947">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="9b58b-948">位址配置可讓您判斷應考慮用於連結產生的端點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-948">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="9b58b-949">例如，許多使用者熟悉的路由名稱和路由值案例 Razor 會實作為位址配置。</span><span class="sxs-lookup"><span data-stu-id="9b58b-949">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="9b58b-950">連結產生器可以透過下列擴充方法，連結至 MVC/ Razor 頁面動作和頁面：</span><span class="sxs-lookup"><span data-stu-id="9b58b-950">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="9b58b-951">這些方法的多載接受包含 `HttpContext` 的引數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-951">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="9b58b-952">這些方法的功能等同於 `Url.Action` 和 `Url.Page`，但提供更多彈性和選項。</span><span class="sxs-lookup"><span data-stu-id="9b58b-952">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="9b58b-953">`GetPath*` 方法與 `Url.Action` 和 `Url.Page` 最類似，因為它們會產生包含絕對路徑的 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-953">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="9b58b-954">`GetUri*` 方法一律會產生包含配置和主機的絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-954">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="9b58b-955">接受 `HttpContext` 的方法會在執行要求的內容中產生 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-955">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="9b58b-956">除非遭到覆寫，否則會使用來自執行要求的環境路由值、URL 基底路徑、配置和主機。</span><span class="sxs-lookup"><span data-stu-id="9b58b-956">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="9b58b-957">呼叫 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 並指定一個位址。</span><span class="sxs-lookup"><span data-stu-id="9b58b-957"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="9b58b-958">執行下列兩個步驟來產生 URI：</span><span class="sxs-lookup"><span data-stu-id="9b58b-958">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="9b58b-959">將位址繫結至符合該位址的端點清單。</span><span class="sxs-lookup"><span data-stu-id="9b58b-959">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="9b58b-960">評估每個端點的 `RoutePattern`，直到找到符合所提供值的路由模式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-960">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="9b58b-961">產生的輸出會與提供給連結產生器的其他 URI 組件合併並傳回。</span><span class="sxs-lookup"><span data-stu-id="9b58b-961">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="9b58b-962"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法支援適用於任何位址類型的標準連結產生功能。</span><span class="sxs-lookup"><span data-stu-id="9b58b-962">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="9b58b-963">使用連結產生器的最便利方式是透過執行特定位址類型作業的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-963">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="9b58b-964">擴充方法</span><span class="sxs-lookup"><span data-stu-id="9b58b-964">Extension Method</span></span>   | <span data-ttu-id="9b58b-965">說明</span><span class="sxs-lookup"><span data-stu-id="9b58b-965">Description</span></span>                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="9b58b-966">根據提供的值產生具有絕對路徑的 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-966">Generates a URI with an absolute path based on the provided values.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="9b58b-967">根據提供的值產生絕對 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-967">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="9b58b-968">注意呼叫 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列影響：</span><span class="sxs-lookup"><span data-stu-id="9b58b-968">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="9b58b-969">使用 `GetUri*` 擴充方法，並注意應用程式組態不會驗證傳入要求的 `Host` 標頭。</span><span class="sxs-lookup"><span data-stu-id="9b58b-969">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="9b58b-970">如果傳入要求的 `Host` 標頭未經驗證，則可能將未受信任的要求輸入傳回檢視/頁面 URI 中的用戶端。</span><span class="sxs-lookup"><span data-stu-id="9b58b-970">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="9b58b-971">建議所有生產應用程式將其伺服器設定為驗證 `Host` 標頭是否為已知有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-971">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="9b58b-972">使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>，並注意與 `Map` 或 `MapWhen` 搭配使用的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-972">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="9b58b-973">`Map*` 會變更執行要求的基底路徑，這會影響連結產生的輸出。</span><span class="sxs-lookup"><span data-stu-id="9b58b-973">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="9b58b-974">所有的 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允許指定基底路徑。</span><span class="sxs-lookup"><span data-stu-id="9b58b-974">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="9b58b-975">請一律指定空白基底路徑來恢復 `Map*` 對連結產生的影響。</span><span class="sxs-lookup"><span data-stu-id="9b58b-975">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="9b58b-976">與舊版路由的差異</span><span class="sxs-lookup"><span data-stu-id="9b58b-976">Differences from earlier versions of routing</span></span>

<span data-ttu-id="9b58b-977">ASP.NET Core 2.2 或更新版本中的端點路由與 ASP.NET Core 中的舊版路由之間有一些差異：</span><span class="sxs-lookup"><span data-stu-id="9b58b-977">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="9b58b-978">端點路由系統不支援以 <xref:Microsoft.AspNetCore.Routing.IRouter> 為基礎的擴充性，包括從 <xref:Microsoft.AspNetCore.Routing.Route> 繼承。</span><span class="sxs-lookup"><span data-stu-id="9b58b-978">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="9b58b-979">端點路由不支援 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-979">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="9b58b-980">使用 2.1 [相容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 繼續使用相容性填充碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-980">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="9b58b-981">端點路由與使用傳統路由時所產生 URI 大小寫的行為不同。</span><span class="sxs-lookup"><span data-stu-id="9b58b-981">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="9b58b-982">請考慮下列預設路由範本：</span><span class="sxs-lookup"><span data-stu-id="9b58b-982">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="9b58b-983">假設您使用下列路由來產生動作連結：</span><span class="sxs-lookup"><span data-stu-id="9b58b-983">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="9b58b-984">使用以 <xref:Microsoft.AspNetCore.Routing.IRouter> 為基礎的路由，此程式碼會產生 `/blog/ReadPost/17` 的 URI，其遵守所提供路由值的大小寫。</span><span class="sxs-lookup"><span data-stu-id="9b58b-984">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="9b58b-985">ASP.NET Core 2.2 或更新版本中的端點路由會產生 `/Blog/ReadPost/17` ("Blog" 為大寫)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-985">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="9b58b-986">端點路由提供 `IOutboundParameterTransformer` 介面，可用來全域自訂此行為，或為對應 URL 套用不同的慣例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-986">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="9b58b-987">如需詳細資訊，請參閱[參數轉換器參考](#parameter-transformer-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-987">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="9b58b-988">Razor當您嘗試連結至不存在的控制器/動作或頁面時，MVC/頁面與傳統路由所使用的連結產生會有不同的行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-988">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="9b58b-989">請考慮下列預設路由範本：</span><span class="sxs-lookup"><span data-stu-id="9b58b-989">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="9b58b-990">假設您使用預設範本和下列程式碼來產生動作連結：</span><span class="sxs-lookup"><span data-stu-id="9b58b-990">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="9b58b-991">使用以 `IRouter` 為基礎的路由，結果一律為 `/Blog/ReadPost/17`，即使 `BlogController` 不存在或沒有 `ReadPost` 動作方法也一樣。</span><span class="sxs-lookup"><span data-stu-id="9b58b-991">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="9b58b-992">如預期，如果動作方法存在，則 ASP.NET Core 2.2 或更新版本中的端點路由會產生 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-992">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="9b58b-993">不過，如果動作不存在，則端點路由會產生空字串。\*\*</span><span class="sxs-lookup"><span data-stu-id="9b58b-993">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="9b58b-994">就概念而言，如果動作不存在，則端點路由不會假設端點存在。</span><span class="sxs-lookup"><span data-stu-id="9b58b-994">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="9b58b-995">連結產生「環境值失效演算法」\*\* 在搭配端點路由使用時會有不同的行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-995">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="9b58b-996">「環境值失效」\*\* 是一種演算法，會從目前執行的要求 (環境值) 決定可用於連結產生作業的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-996">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="9b58b-997">傳統路由一律會在連結至其他動作時，使額外的路由值失效。</span><span class="sxs-lookup"><span data-stu-id="9b58b-997">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="9b58b-998">在 ASP.NET Core 2.2 版以前，屬性路由沒有此行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-998">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="9b58b-999">在舊版的 ASP.NET Core 中，連結至使用相同路由參數名稱的其他動作會導致連結產生錯誤。</span><span class="sxs-lookup"><span data-stu-id="9b58b-999">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="9b58b-1000">在 ASP.NET Core 2.2 或更新版本中，這兩種路由形式都會在連結至其他動作時使值失效。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1000">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="9b58b-1001">請考慮 ASP.NET Core 2.1 或更舊版本中的下列範例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1001">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="9b58b-1002">連結至其他動作 (或其他頁面) 時，路由值可能會不適當地重複使用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1002">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="9b58b-1003">在 */Pages/Store/Product.cshtml* 中：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1003">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="9b58b-1004">在 */Pages/Login.cshtml* 中：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1004">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="9b58b-1005">如果 URI 在 ASP.NET Core 2.1 或更舊版本中為 `/Store/Product/18`，則 `@Url.Page("/Login")` 在 Store/Info 頁面中產生的連結為 `/Login/18`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1005">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="9b58b-1006">這會重複使用 `id` 值 18，即使連結目的地是完全不同的應用程式組件也一樣。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1006">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="9b58b-1007">`/Login` 頁面內容中的 `id` 路由值可能是使用者識別碼值，而不是市集產品識別碼值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1007">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="9b58b-1008">在 ASP.NET Core 2.2 或更新版本中的端點路由中，結果為 `/Login`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1008">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="9b58b-1009">當連結的目的地是不同的動作或頁面時，不會重複使用環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1009">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="9b58b-1010">往返路由參數語法：使用雙星號 (`**`) catch-all 參數語法時，斜線不會經過編碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1010">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="9b58b-1011">在連結產生期間，除了斜線，路由系統還會對雙星號 (`**`) catch-all 參數 (例如 `{**myparametername}`) 中擷取的值進行編碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1011">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="9b58b-1012">ASP.NET Core 2.2 或更新版本中以 `IRouter` 為基礎的路由支援雙星號 catch-all。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1012">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="9b58b-1013">舊版 ASP.NET Core 中的單一星號 catch-all (`{*myparametername}`) 參數語法仍會受到支援，而且斜線會經過編碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1013">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="9b58b-1014">路由</span><span class="sxs-lookup"><span data-stu-id="9b58b-1014">Route</span></span>              | <span data-ttu-id="9b58b-1015">以下列項目產生的連結：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1015">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | <span data-ttu-id="9b58b-1016">`/search/admin%2Fproducts` (斜線會經過編碼)</span><span class="sxs-lookup"><span data-stu-id="9b58b-1016">`/search/admin%2Fproducts` (the forward slash is encoded)</span></span>             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a><span data-ttu-id="9b58b-1017">中介軟體範例</span><span class="sxs-lookup"><span data-stu-id="9b58b-1017">Middleware example</span></span>

<span data-ttu-id="9b58b-1018">在下列範例中，中介軟體使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 建立列出市集產品的動作方法連結。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1018">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="9b58b-1019">應用程式中的任何類別都可以使用連結產生器，方法是將它插入類別並呼叫 `GenerateLink`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1019">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a><span data-ttu-id="9b58b-1020">建立路由</span><span class="sxs-lookup"><span data-stu-id="9b58b-1020">Create routes</span></span>

<span data-ttu-id="9b58b-1021">大部分的應用程式會藉由呼叫 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或其中一個 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定義的類似擴充方法來定建立路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1021">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="9b58b-1022">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 擴充方法都會建立 <xref:Microsoft.AspNetCore.Routing.Route> 的執行個體，並將它新增至路由集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1022">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="9b58b-1023"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由處理常式參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1023"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="9b58b-1024"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 只會新增 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 所處理的路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1024"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="9b58b-1025">若要深入了解 MVC 中的路由功能，請參閱 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1025">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="9b58b-1026">下列程式碼範例是典型 ASP.NET Core MVC 路由定義所使用的 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 呼叫範例：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1026">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9b58b-1027">此範本會比對 URL 路徑，並擷取路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1027">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="9b58b-1028">例如，路徑 `/Products/Details/17` 會產生下列路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1028">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="9b58b-1029">路由值是透過將 URL 路徑分割成區段，並比對每個區段與路由範本中的「路由參數」\*\* 名稱來判定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1029">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="9b58b-1030">路由參數為具名。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1030">Route parameters are named.</span></span> <span data-ttu-id="9b58b-1031">參數是透過以括弧 `{ ... }` 括住參數名稱來定義。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1031">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="9b58b-1032">上述範本也可以比對 URL 路徑 `/` 並產生值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1032">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="9b58b-1033">發生這種情況是因為 `{controller}` 和 `{action}` 路由參數有預設值，而 `id` 路由參數為選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1033">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="9b58b-1034">路由參數名稱之後緊接著值的等號 (`=`) 會定義參數預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1034">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="9b58b-1035">路由參數名稱之後的問號 (`?`) 會定義選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1035">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="9b58b-1036">在路由相符時，具有預設值的路由參數一定\*\* 會產生路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1036">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="9b58b-1037">如果沒有對應的 URL 路徑區段，選擇性參數不會產生路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1037">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="9b58b-1038">如需路由範本情節和語法的詳細描述，請參閱[路由範本參考](#route-template-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1038">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="9b58b-1039">在下列範例中，路由參數定義 `{id:int}` 會定義 `id` 路由參數的[路由條件約束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1039">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="9b58b-1040">此範本會符合 `/Products/Details/17` 等 URL 路徑，但不符合 `/Products/Details/Apples`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1040">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="9b58b-1041">路由條件約束會實作 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，並檢查路由值以進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1041">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="9b58b-1042">在此範例中，路由值 `id` 必須可以轉換為整數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1042">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="9b58b-1043">如需架構所提供之路由條件約束的說明，請參閱[路由條件約束參考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1043">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="9b58b-1044"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 的其他多載接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1044">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="9b58b-1045">這些參數通常是用來傳遞匿名類型的物件，其中匿名類型的屬性名稱符合路由參數名稱。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1045">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="9b58b-1046">下列 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 範例會建立對等的路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1046">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="9b58b-1047">對於簡單的路由而言，定義條件約束和預設的內嵌語法可能很方便。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1047">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="9b58b-1048">不過，內嵌語法不支援某些情節 (例如資料語彙基元)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1048">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="9b58b-1049">下列範例將示範一些其他情節：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1049">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="9b58b-1050">上述範本會比對 `/Blog/All-About-Routing/Introduction` 等 URL 路徑，並擷取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1050">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="9b58b-1051">即使範本中沒有任何對應的路由參數，路由也會產生 `controller` 和 `action` 的預設路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1051">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="9b58b-1052">預設值可以在路由範本中指定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1052">Default values can be specified in the route template.</span></span> <span data-ttu-id="9b58b-1053">`article` 路由參數透過在路由參數名稱之前加上雙星號 (`**`) 來定義為 *catch-all*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1053">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="9b58b-1054">全部擷取路由參數會擷取 URL 路徑的其餘部分，而且也可以符合空字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1054">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="9b58b-1055">下列範例會新增路由條件約束和資料語彙基元：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1055">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="9b58b-1056">上述範本會比對 `/en-US/Products/5` 等 URL 路徑，並擷取值 `{ controller = Products, action = Details, id = 5 }` 和資料語彙基元 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1056">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![[區域變數] 視窗語彙基元](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="9b58b-1058">路由類別 URL 產生</span><span class="sxs-lookup"><span data-stu-id="9b58b-1058">Route class URL generation</span></span>

<span data-ttu-id="9b58b-1059"><xref:Microsoft.AspNetCore.Routing.Route> 類別也可以結合一組路由值與其路由範本來產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1059">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="9b58b-1060">這在邏輯上是比對 URL 路徑的反向處理序。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1060">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="9b58b-1061">若要進一步了解 URL 產生，請假設您想要產生的 URL，並考慮路由範本將會如何比對該 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1061">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="9b58b-1062">產生的值為何？</span><span class="sxs-lookup"><span data-stu-id="9b58b-1062">What values would be produced?</span></span> <span data-ttu-id="9b58b-1063">這與 URL 產生在 <xref:Microsoft.AspNetCore.Routing.Route> 類別中的運作方式大致相同。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1063">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="9b58b-1064">下列範例使用一般 ASP.NET Core MVC 預設路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1064">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9b58b-1065">路由值為 `{ controller = Products, action = List }` 時，會產生 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1065">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="9b58b-1066">路由值會取代對應的路由參數，以形成 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1066">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="9b58b-1067">由於 `id` 是選擇性路由參數，因此沒有 `id` 值也可以成功產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1067">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="9b58b-1068">路由值為 `{ controller = Home, action = Index }` 時，會產生 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1068">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="9b58b-1069">所提供的路由值符合預設值，因此可以放心地省略這些預設值對應的區段。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1069">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="9b58b-1070">這兩個產生的 URL 會使用下列路由定義 (`/Home/Index` 和 `/`) 反覆存取，並產生用來產生 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1070">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="9b58b-1071">使用 ASP.NET Core MVC 的應用程式應該使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 來產生 URL，而不是直接呼叫路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1071">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="9b58b-1072">如需 URL 產生的詳細資訊，請參閱 [URI 產生參考](#url-generation-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1072">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="9b58b-1073">使用路由中介軟體</span><span class="sxs-lookup"><span data-stu-id="9b58b-1073">Use Routing Middleware</span></span>

<span data-ttu-id="9b58b-1074">參考應用程式專案檔中的 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1074">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="9b58b-1075">在 `Startup.ConfigureServices` 中，將路由新增至服務容器：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1075">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="9b58b-1076">路由必須設定在 `Startup.Configure` 方法中。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1076">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="9b58b-1077">範例應用程式使用下列 API：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1077">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="9b58b-1078"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>：僅符合 HTTP GET 要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1078"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="9b58b-1079">下表顯示使用指定 URL 的回應。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1079">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="9b58b-1080">URI</span><span class="sxs-lookup"><span data-stu-id="9b58b-1080">URI</span></span>                    | <span data-ttu-id="9b58b-1081">回應</span><span class="sxs-lookup"><span data-stu-id="9b58b-1081">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="9b58b-1082">Hello!</span><span class="sxs-lookup"><span data-stu-id="9b58b-1082">Hello!</span></span> <span data-ttu-id="9b58b-1083">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="9b58b-1083">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="9b58b-1084">Hello!</span><span class="sxs-lookup"><span data-stu-id="9b58b-1084">Hello!</span></span> <span data-ttu-id="9b58b-1085">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="9b58b-1085">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="9b58b-1086">Hello!</span><span class="sxs-lookup"><span data-stu-id="9b58b-1086">Hello!</span></span> <span data-ttu-id="9b58b-1087">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="9b58b-1087">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="9b58b-1088">要求失敗，沒有相符項目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1088">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="9b58b-1089">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="9b58b-1089">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="9b58b-1090">要求失敗，僅符合 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1090">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="9b58b-1091">要求失敗，沒有相符項目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1091">The request falls through, no match.</span></span>              |

<span data-ttu-id="9b58b-1092">架構會提供一組擴充方法來建立路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1092">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="9b58b-1093">`Map[Verb]` 方法會使用條件約束，將路由限制為方法名稱中的 HTTP 指令動詞。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1093">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="9b58b-1094">如需範例，請參閱 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 與 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1094">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="9b58b-1095">路由範本參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-1095">Route template reference</span></span>

<span data-ttu-id="9b58b-1096">大括弧 (`{ ... }`) 內的語彙基元定義路由相符時會繫結的「路由參數」\*\*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1096">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="9b58b-1097">您可以在路由區段中定義多個路由參數，但其必須以常值分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1097">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="9b58b-1098">例如，`{controller=Home}{action=Index}` 不是有效的路由，因為 `{controller}` 與 `{action}` 之間沒有任何常值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1098">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="9b58b-1099">這些路由參數必須有一個名稱，並且可以指定其他屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1099">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="9b58b-1100">路由參數之外的常值文字 (例如，`{id}`) 和路徑分隔符號 `/` 必須符合 URL 中的文字。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1100">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="9b58b-1101">文字比對會區分大小寫，並以 URL 路徑的已解碼表示法為基礎。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1101">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="9b58b-1102">若要比對常值路由參數分隔符號 (`{` 或 `}`)，請重複字元 (`{{` 或 `}}`) 來將分隔符號逸出。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1102">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="9b58b-1103">URL 模式嘗試擷取具有選擇性副檔名的檔案名稱時，具有其他考量。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1103">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="9b58b-1104">以範本 `files/{filename}.{ext?}` 為例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1104">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="9b58b-1105">當 `filename` 和 `ext` 都存在值時，就會填入這兩個值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1105">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="9b58b-1106">如果 URL 中只有 `filename` 存在值，由於結尾的句點 (`.`) 是選擇性項目，因此路由相符。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1106">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="9b58b-1107">下列 URL 符合此路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1107">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="9b58b-1108">您可以使用一個星號 (`*`) 或雙星號 (`**`) 作為路由參數的前置詞，以繫結至 URI 的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1108">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="9b58b-1109">這稱為 *catch-all* 參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1109">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="9b58b-1110">例如，`blog/{**slug}` 符合以 `/blog` 開頭且其後有任何值 (這會指派給 `slug` 路由值) 的所有 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1110">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="9b58b-1111">全部擷取參數也可以符合空字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1111">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="9b58b-1112">當使用路由產生 URL (包括路徑分隔符號 (`/`) 字元) 時，catch-all 參數會逸出適當的字元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1112">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="9b58b-1113">例如，路由值為 `{ path = "my/path" }` 的路由 `foo/{*path}` 會產生 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1113">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="9b58b-1114">請注意逸出的斜線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1114">Note the escaped forward slash.</span></span> <span data-ttu-id="9b58b-1115">若要反覆存取路徑分隔符號字元，請使用 `**` 路由參數前置詞。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1115">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="9b58b-1116">具有 `{ path = "my/path" }` 的路由 `foo/{**path}` 會產生 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1116">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="9b58b-1117">路由參數可能有「預設值」\*\*，指定方法是在參數名稱之後指定預設值，並以等號 (`=`) 分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1117">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="9b58b-1118">例如，`{controller=Home}` 定義 `Home` 作為 `controller` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1118">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="9b58b-1119">如果 URL 中沒有用於參數的任何值，則會使用預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1119">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="9b58b-1120">路由參數也可以設為選擇性，方法是在參數名稱結尾附加問號 (`?`)，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1120">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="9b58b-1121">選擇性值與預設路由參數之間的差異在於，具有預設值的路由參數一定會產生值&mdash;選擇性參數只有在要求 URL 提供值時才會有值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1121">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="9b58b-1122">路由參數可能具有條件約束，這些條件約束必須符合與 URL 繫結的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1122">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="9b58b-1123">在路由參數名稱之後新增分號 (`:`) 和條件約束名稱，即可指定路由參數的「內嵌條件約束」\*\*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1123">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="9b58b-1124">如果條件約束需要引數，這些引數會在條件約束名稱後面以括弧 (`(...)`) 括住。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1124">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="9b58b-1125">指定多個內嵌條件約束的方法是附加另一個冒號 (`:`) 和條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1125">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="9b58b-1126">條件約束名稱和引述會傳遞至 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服務來建立 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的執行個體，以用於 URL 處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1126">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="9b58b-1127">例如，路由範本 `blog/{article:minlength(10)}` 指定具有引數 `10` 的 `minlength` 條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1127">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="9b58b-1128">如需路由條件約束詳細資訊和架構所提供的條件約束清單，請參閱[路由條件約束參考](#route-constraint-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1128">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="9b58b-1129">路由參數也可以具有參數轉換器，以在產生連結及根據 URL 比對動作和頁面時，轉換參數的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1129">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="9b58b-1130">與條件約束類似，可以透過在路徑參數名稱後面新增冒號 (`:`) 和轉換器名稱，將參數轉換器內嵌新增至路徑參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1130">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="9b58b-1131">例如，路由範本 `blog/{article:slugify}` 會指定 `slugify` 轉換器。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1131">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="9b58b-1132">如需參數轉換器的詳細資訊，請參閱[參數轉器參考](#parameter-transformer-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1132">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="9b58b-1133">下表示範範例路由範本及其行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1133">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="9b58b-1134">路由範本</span><span class="sxs-lookup"><span data-stu-id="9b58b-1134">Route Template</span></span>                           | <span data-ttu-id="9b58b-1135">範例比對 URI</span><span class="sxs-lookup"><span data-stu-id="9b58b-1135">Example Matching URI</span></span>    | <span data-ttu-id="9b58b-1136">要求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="9b58b-1136">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="9b58b-1137">只比對單一路徑 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1137">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="9b58b-1138">比對並將 `Page` 設定為 `Home`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1138">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="9b58b-1139">比對並將 `Page` 設定為 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1139">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="9b58b-1140">對應至 `Products` 控制器和 `List` 動作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1140">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="9b58b-1141">對應至 `Products` 控制器和 `Details` 動作 (`id` 設定為 123)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1141">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="9b58b-1142">對應至 `Home` 控制器和 `Index` 方法 (會忽略 `id`)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1142">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="9b58b-1143">使用範本通常是最簡單的路由方式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1143">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="9b58b-1144">條件約束和預設值也可以在路由範本外部指定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1144">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="9b58b-1145">啟用[記錄](xref:fundamentals/logging/index)以查看內建路由實作 (例如 <xref:Microsoft.AspNetCore.Routing.Route>) 如何符合要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1145">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="9b58b-1146">保留的路由名稱</span><span class="sxs-lookup"><span data-stu-id="9b58b-1146">Reserved routing names</span></span>

<span data-ttu-id="9b58b-1147">下列關鍵字是保留的名稱，不能用作路由名稱或參數：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1147">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="9b58b-1148">路由條件約束參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-1148">Route constraint reference</span></span>

<span data-ttu-id="9b58b-1149">路由條件約束執行時機是出現符合傳入 URL 的項目，並將 URL 路徑語彙基元化成路由值時。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1149">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="9b58b-1150">路由條件約束通常會透過路由範本檢查相關聯的路由值，並對是否可接受值做出是/否決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1150">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="9b58b-1151">某些路由條件約束會使用路由值以外的資料，以考慮是否可以路由要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1151">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="9b58b-1152">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以依據其 HTTP 指令動詞接受或拒絕要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1152">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="9b58b-1153">條件約束可用於路由要求和連結產生。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1153">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="9b58b-1154">請勿針對**輸入驗證**使用條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1154">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="9b58b-1155">如果針對**輸入驗證**使用條件約束，則無效的輸入會導致產生「404 - 找不到」\*\* 回應，而不是「400 - 錯誤要求」\*\* 與適當的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1155">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="9b58b-1156">路由條件約束會用來**釐清**類似的路由，而不是用來驗證特定路由的輸入。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1156">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="9b58b-1157">下表示範範例路由條件約束及其預期行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1157">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="9b58b-1158">條件約束</span><span class="sxs-lookup"><span data-stu-id="9b58b-1158">Constraint</span></span> | <span data-ttu-id="9b58b-1159">範例</span><span class="sxs-lookup"><span data-stu-id="9b58b-1159">Example</span></span> | <span data-ttu-id="9b58b-1160">範例相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-1160">Example matches</span></span> | <span data-ttu-id="9b58b-1161">附註</span><span class="sxs-lookup"><span data-stu-id="9b58b-1161">Notes</span></span> |
|------------|---------|-----------------|-------|
| `int` | `{id:int}` | <span data-ttu-id="9b58b-1162">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1162">`123456789`, `-123456789`</span></span> | <span data-ttu-id="9b58b-1163">符合任何整數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1163">Matches any integer.</span></span>|
| `bool` | `{active:bool}` | <span data-ttu-id="9b58b-1164">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1164">`true`, `FALSE`</span></span> | <span data-ttu-id="9b58b-1165">符合 `true` 或 `false` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1165">Matches `true` or `false`.</span></span> <span data-ttu-id="9b58b-1166">不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1166">Case-insensitive.</span></span>|
| `datetime` | `{dob:datetime}` | <span data-ttu-id="9b58b-1167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1167">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="9b58b-1168">符合非變異 `DateTime` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1168">Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-1169">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1169">See preceding warning.</span></span>|
| `decimal` | `{price:decimal}` | <span data-ttu-id="9b58b-1170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1170">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="9b58b-1171">符合非變異 `decimal` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1171">Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-1172">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1172">See  preceding warning.</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="9b58b-1173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1173">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="9b58b-1174">符合非變異 `double` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1174">Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-1175">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1175">See  preceding warning.</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="9b58b-1176">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1176">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="9b58b-1177">符合非變異 `float` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1177">Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-1178">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1178">See  preceding warning.</span></span>|
| `guid` | `{id:guid}` | <span data-ttu-id="9b58b-1179">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1179">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="9b58b-1180">符合有效的 `Guid` 值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1180">Matches a valid `Guid` value.</span></span>|
| `long` | `{ticks:long}` | <span data-ttu-id="9b58b-1181">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1181">`123456789`, `-123456789`</span></span> | <span data-ttu-id="9b58b-1182">符合有效的 `long` 值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1182">Matches a valid `long` value.</span></span>|
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="9b58b-1183">字串必須至少有4個字元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1183">String must be at least 4 characters.</span></span>|
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | <span data-ttu-id="9b58b-1184">字串最多8個字元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1184">String has maximum of 8 characters.</span></span>|
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="9b58b-1185">字串的長度必須剛好為12個字元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1185">String must be exactly 12 characters long.</span></span>|
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="9b58b-1186">字串必須至少為8個字元，最多16個字元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1186">String must be at least 8 and has maximum of 16 characters.</span></span>|
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="9b58b-1187">整數值必須至少為18。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1187">Integer value must be at least 18.</span></span>|
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="9b58b-1188">整數值最大值120。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1188">Integer value maximum of 120.</span></span>|
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="9b58b-1189">整數值必須至少為18，最大值為120。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1189">Integer value must be at least 18 and maximum of 120.</span></span>|
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="9b58b-1190">字串必須由一或多個字母字元組成 `a` - `z` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1190">String must consist of one or more alphabetical characters `a`-`z`.</span></span> <span data-ttu-id="9b58b-1191">不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1191">Case-insensitive.</span></span>|
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="9b58b-1192">字串必須符合正則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1192">String must match the regular expression.</span></span> <span data-ttu-id="9b58b-1193">請參閱定義正則運算式的秘訣。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1193">See tips about defining a regular expression.</span></span>|
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="9b58b-1194">用來強制在 URL 產生期間存在非參數值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1194">Used to enforce that a non-parameter value is present during URL generation.</span></span>|

<span data-ttu-id="9b58b-1195">以冒號分隔的多個條件約束，可以套用至單一參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1195">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="9b58b-1196">例如，下列條件約束會將參數限制在 1 或更大的整數值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1196">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="9b58b-1197">確認 URL 可以轉換成 CLR 類型的路由條件約束 (例如 `int` 或 `DateTime`) 一律使用不因國別而異的文化特性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1197">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="9b58b-1198">這些條件約束假設 URL 不可當地語系化。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1198">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="9b58b-1199">架構提供的路由條件約束不會修改路由值中儲存的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1199">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="9b58b-1200">所有從 URL 剖析而來的路由值會儲存為字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1200">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="9b58b-1201">例如，`float` 條件約束會嘗試將路由值轉換成浮點數，但轉換的值只能用來確認它可以轉換成浮點數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1201">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="9b58b-1202">規則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1202">Regular expressions</span></span>

<span data-ttu-id="9b58b-1203">ASP.NET Core 架構將 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` 新增至規則運算式建構函式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1203">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="9b58b-1204">如需這些成員的說明，請參閱 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1204">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="9b58b-1205">正則運算式使用的分隔符號和標記類似于路由和 c # 語言所使用的標記。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1205">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="9b58b-1206">規則運算式的語彙基元必須逸出。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1206">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="9b58b-1207">若要在路由中使用正則運算式 `^\d{3}-\d{2}-\d{4}$` ：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1207">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing:</span></span>

* <span data-ttu-id="9b58b-1208">運算式必須 `\` 在字串中提供以雙反斜線字元形式在原始程式碼中提供的單一反斜線字元 `\\` 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1208">The expression must have the single backslash `\` characters provided in the string as double backslash `\\` characters in the source code.</span></span>
* <span data-ttu-id="9b58b-1209">正則運算式必須是我們才能將 `\\` `\` 字串 escape 字元換用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1209">The regular expression must us `\\` in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="9b58b-1210">`\\`使用[逐字字串常](/dotnet/csharp/language-reference/keywords/string)值時，不需要正則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1210">The regular expression doesn't require `\\` when using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="9b58b-1211">若要將路由參數分隔符號 `{` 、 `}` 、 `[` 、 `]` ，請將運算式中的字元 `{{` `}` `[[` `]]` （，）加倍。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1211">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression `{{`, `}`, `[[`, `]]`.</span></span> <span data-ttu-id="9b58b-1212">下表顯示正則運算式和已轉義的版本：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1212">The following table shows a regular expression and the escaped version:</span></span>

| <span data-ttu-id="9b58b-1213">規則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1213">Regular Expression</span></span>    | <span data-ttu-id="9b58b-1214">逸出的規則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1214">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="9b58b-1215">路由中使用的正則運算式通常會以插入 `^` 號字元開頭，並符合字串的開始位置。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1215">Regular expressions used in routing often start with the caret `^` character and match starting position of the string.</span></span> <span data-ttu-id="9b58b-1216">運算式通常以貨幣符號 `$` 字元結尾，且字串的結尾相符。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1216">The expressions often end with the dollar sign `$` character and match end of the string.</span></span> <span data-ttu-id="9b58b-1217">`^` 和 `$` 字元可確保規則運算式符合整個路由參數值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1217">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="9b58b-1218">若不使用 `^` 與 `$` 字元，規則運算式會比對字串內的所有部分字串，這通常不是您想要的結果。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1218">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="9b58b-1219">下表提供範例，並說明它們符合或無法符合的原因。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1219">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="9b58b-1220">運算是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1220">Expression</span></span>   | <span data-ttu-id="9b58b-1221">String</span><span class="sxs-lookup"><span data-stu-id="9b58b-1221">String</span></span>    | <span data-ttu-id="9b58b-1222">相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-1222">Match</span></span> | <span data-ttu-id="9b58b-1223">註解</span><span class="sxs-lookup"><span data-stu-id="9b58b-1223">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-1224">hello</span><span class="sxs-lookup"><span data-stu-id="9b58b-1224">hello</span></span>     | <span data-ttu-id="9b58b-1225">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1225">Yes</span></span>   | <span data-ttu-id="9b58b-1226">子字串相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-1226">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-1227">123abc456</span><span class="sxs-lookup"><span data-stu-id="9b58b-1227">123abc456</span></span> | <span data-ttu-id="9b58b-1228">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1228">Yes</span></span>   | <span data-ttu-id="9b58b-1229">子字串相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-1229">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-1230">mz</span><span class="sxs-lookup"><span data-stu-id="9b58b-1230">mz</span></span>        | <span data-ttu-id="9b58b-1231">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1231">Yes</span></span>   | <span data-ttu-id="9b58b-1232">符合運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1232">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-1233">MZ</span><span class="sxs-lookup"><span data-stu-id="9b58b-1233">MZ</span></span>        | <span data-ttu-id="9b58b-1234">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1234">Yes</span></span>   | <span data-ttu-id="9b58b-1235">不區分大小寫</span><span class="sxs-lookup"><span data-stu-id="9b58b-1235">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="9b58b-1236">hello</span><span class="sxs-lookup"><span data-stu-id="9b58b-1236">hello</span></span>     | <span data-ttu-id="9b58b-1237">否</span><span class="sxs-lookup"><span data-stu-id="9b58b-1237">No</span></span>    | <span data-ttu-id="9b58b-1238">請參閱上述的 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1238">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="9b58b-1239">123abc456</span><span class="sxs-lookup"><span data-stu-id="9b58b-1239">123abc456</span></span> | <span data-ttu-id="9b58b-1240">否</span><span class="sxs-lookup"><span data-stu-id="9b58b-1240">No</span></span>    | <span data-ttu-id="9b58b-1241">請參閱上述的 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1241">See `^` and `$` above</span></span> |

<span data-ttu-id="9b58b-1242">如需規則運算式語法的詳細資訊，請參閱 [.NET Framework 規則運算式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1242">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="9b58b-1243">若要將參數限制為一組已知的可能值，請使用規則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1243">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="9b58b-1244">例如，`{action:regex(^(list|get|create)$)}` 只會將 `action` 路由值與 `list`、`get` 或 `create` 相符。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1244">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="9b58b-1245">如果已傳入條件約束字典，字串 `^(list|get|create)$` 則是對等項目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1245">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="9b58b-1246">已傳入條件約束字典 (未內嵌在範本內) 的條件約束，即使不符合其中一個已知的條件約束，也會被視為規則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1246">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="9b58b-1247">自訂路由條件約束</span><span class="sxs-lookup"><span data-stu-id="9b58b-1247">Custom route constraints</span></span>

<span data-ttu-id="9b58b-1248">除了內建的路由限制式之外，自訂路由限制式也可以透過實作 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 介面來建立。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1248">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="9b58b-1249"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 介面包含單一方法 `Match`，此方法會在滿足限制式時傳回 `true`，否則會傳回 `false`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1249">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="9b58b-1250">若要使用自訂 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，路由限制式型別必須必須向應用程式的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> (在應用程式的服務容器中) 註冊。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1250">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="9b58b-1251"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是一個目錄，它將路由限制式機碼對應到可驗證那些限制式的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 實作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1251">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="9b58b-1252">更新應用程式的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 時，可在 `Startup.ConfigureServices` 中於進行 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼叫時更新，或透過使用 `services.Configure<RouteOptions>` 直接設定 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 來更新。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1252">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="9b58b-1253">例如：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1253">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="9b58b-1254">限制式接著能以一般方式套用到路由 (使用註冊限制式型別時使用名稱)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1254">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="9b58b-1255">例如：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1255">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a><span data-ttu-id="9b58b-1256">參數轉換器參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-1256">Parameter transformer reference</span></span>

<span data-ttu-id="9b58b-1257">參數轉換程式：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1257">Parameter transformers:</span></span>

* <span data-ttu-id="9b58b-1258">會在為 <xref:Microsoft.AspNetCore.Routing.Route> 產生連結時執行。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1258">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="9b58b-1259">實作 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1259">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="9b58b-1260">是使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 進行設定的。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1260">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="9b58b-1261">採用參數的路由值，並將它轉換為新的字串值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1261">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="9b58b-1262">導致在所產生連結中使用已轉換的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1262">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="9b58b-1263">例如，具有 `Url.Action(new { article = "MyTestArticle" })` 之路由模式 `blog\{article:slugify}` 中的自訂 `slugify` 參數轉換器，會產生 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1263">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="9b58b-1264">若要在路由模式中使用參數轉換器，請先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 進行設定：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1264">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="9b58b-1265">架構會使用參數轉換器來轉換端點解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1265">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="9b58b-1266">例如，ASP.NET Core MVC 會使用參數轉換器，轉換用於比對 `area`、`controller`、`action` 和 `page` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1266">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="9b58b-1267">使用上述的路由，動作 `SubscriptionManagementController.GetAll` 會與URI `/subscription-management/get-all` 比對。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1267">With the preceding route, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="9b58b-1268">參數轉換器不會變更用來產生連結的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1268">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="9b58b-1269">例如，`Url.Action("GetAll", "SubscriptionManagement")` 會輸出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1269">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="9b58b-1270">ASP.NET Core 針對搭配產生的路由使用參數轉換程式提供了 API 慣例：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1270">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="9b58b-1271">ASP.NET Core MVC 有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 慣例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1271">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="9b58b-1272">此慣例會將所指定參數轉換程式套用到應用程式中的所有屬性路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1272">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="9b58b-1273">參數轉換程式會在被取代時轉換屬性路由語彙基元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1273">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="9b58b-1274">如需詳細資訊，請參閱[使用參數轉換程式自訂語彙基元取代](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1274">For more information, see [Use a parameter transformer to customize token replacement](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="9b58b-1275">Razor 頁面具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 慣例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1275">Razor Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="9b58b-1276">此慣例會將指定的參數轉換器套用至所有自動探索到的 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1276">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="9b58b-1277">參數轉換程式會轉換頁面路由的資料夾和檔案名區段 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1277">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="9b58b-1278">如需詳細資訊，請參閱[使用參數轉換程式自訂頁面路由](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1278">For more information, see [Use a parameter transformer to customize page routes](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

## <a name="url-generation-reference"></a><span data-ttu-id="9b58b-1279">URL 產生參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-1279">URL generation reference</span></span>

<span data-ttu-id="9b58b-1280">下列範例示範如何在指定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情況下產生路由的連結。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1280">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="9b58b-1281">在上述範例的結尾產生的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 是 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1281">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="9b58b-1282">字典提供「追蹤套件路由」範本 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1282">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="9b58b-1283">如需詳細資訊，請參閱[使用路由中介軟體](#use-routing-middleware)一節或[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1283">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="9b58b-1284"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 建構函式的第二個參數是「環境值」\*\* 的集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1284">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="9b58b-1285">環境值便於使用，因為它們會限制開發人員必須在要求內容中指定的值數目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1285">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="9b58b-1286">目前要求的目前路由值被視為用於連結產生的環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1286">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="9b58b-1287">在 ASP.NET Core MVC 應用程式 `HomeController` 的 `About` 動作中，您不需要指定控制器路由值以連結到 `Index` 動作&mdash;會使用 `Home` 的環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1287">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="9b58b-1288">不符合參數的環境值會予以忽略。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1288">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="9b58b-1289">當明確提供的值覆寫環境值時，也會忽略環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1289">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="9b58b-1290">URL 中的比對是從左到右。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1290">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="9b58b-1291">明確提供但不符合路由區段的值會新增至查詢字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1291">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="9b58b-1292">下表顯示使用路由範本 `{controller}/{action}/{id?}` 時的結果。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1292">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="9b58b-1293">環境值</span><span class="sxs-lookup"><span data-stu-id="9b58b-1293">Ambient Values</span></span>                     | <span data-ttu-id="9b58b-1294">明確值</span><span class="sxs-lookup"><span data-stu-id="9b58b-1294">Explicit Values</span></span>                        | <span data-ttu-id="9b58b-1295">結果</span><span class="sxs-lookup"><span data-stu-id="9b58b-1295">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="9b58b-1296">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1296">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-1297">action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1297">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="9b58b-1298">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1298">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-1299">controller = "Order", action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1299">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="9b58b-1300">controller = "Home", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1300">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="9b58b-1301">action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1301">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="9b58b-1302">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1302">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-1303">action = "About", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1303">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="9b58b-1304">如果路由具有未對應至參數的預設值，且該值會明確提供，它必須符合預設值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1304">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="9b58b-1305">連結產生只有在提供了 `controller` 與 `action` 的相符值時，才會產生此路由的連結。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1305">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="9b58b-1306">複雜區段</span><span class="sxs-lookup"><span data-stu-id="9b58b-1306">Complex segments</span></span>

<span data-ttu-id="9b58b-1307">複雜區段 (例如，`[Route("/x{token}y")]`) 會透過以非窮盡的方式，由右至左比對常值來處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1307">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="9b58b-1308">請參閱[此程式碼](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解如何比對複雜區段的詳細解釋。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1308">See [this code](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="9b58b-1309">[程式法範例](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)不是由 ASP.NET Core 使用，但它提供一個好的複雜區段解釋。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1309">The [code sample](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9b58b-1310">路由功能負責將要求 URI 對應至路由處理常式，並分派傳入要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1310">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="9b58b-1311">路由定義於應用程式，並在該應用程式啟動時進行設定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1311">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="9b58b-1312">路由可以選擇性地從要求中所包含的 URL 擷取值，然後這些值就可用於處理要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1312">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="9b58b-1313">使用應用程式中已設定的路由，路由功能將能夠產生對應至路由處理常式的 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1313">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

<span data-ttu-id="9b58b-1314">若要使用 ASP.NET Core 2.1 中的最新路由情節，請將[相容性版本](xref:mvc/compatibility-version)指定為 `Startup.ConfigureServices` 中的 MVC 服務註冊：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1314">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> <span data-ttu-id="9b58b-1315">本文件涵蓋低階的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1315">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="9b58b-1316">如需 ASP.NET Core MVC 路由的資訊，請參閱 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1316">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="9b58b-1317">如需頁面中路由慣例的詳細資訊 Razor ，請參閱 <xref:razor-pages/razor-pages-conventions> 。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1317">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="9b58b-1318">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9b58b-1318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="9b58b-1319">路由的基本概念</span><span class="sxs-lookup"><span data-stu-id="9b58b-1319">Routing basics</span></span>

<span data-ttu-id="9b58b-1320">大部分應用程式都應該選擇基本的描述性路由傳送配置，讓 URL 可讀且有意義。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1320">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="9b58b-1321">預設慣例路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1321">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="9b58b-1322">支援基本的描述性路由配置。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1322">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="9b58b-1323">適合作為 UI 型應用程式的起點。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1323">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="9b58b-1324">在特殊情況下，開發人員通常會使用[屬性路由](xref:mvc/controllers/routing#attribute-routing)或專屬的慣例路由，新增額外的簡潔路由到應用程式的高流量區域 (例如，部落格和電子商務端點)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1324">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="9b58b-1325">Web API 應該使用屬性路由傳送來將應用程式功能模型建構為作業由 HTTP 指令動詞代表的資源集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1325">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="9b58b-1326">這表示相同邏輯資源上的許多作業 (例如，GET、POST) 都會使用相同的 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1326">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="9b58b-1327">屬性路由提供仔細設計 API 公用端點配置所需的控制層級。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="9b58b-1328">Razor 頁面應用程式會使用預設的傳統路由，在應用程式的 *Pages* 資料夾中提供命名資源。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1328">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="9b58b-1329">有其他慣例可讓您自訂 Razor 頁面路由行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1329">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="9b58b-1330">如需詳細資訊，請參閱 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1330">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="9b58b-1331">URL 產生支援允許在不需要硬式編碼的 URL 來連結應用程式的情況下開發應用程式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1331">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="9b58b-1332">這項支援可讓您從基本路由設定開始，並在決定應用程式資源配置之後修改路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1332">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="9b58b-1333">路由會使用的路由 <xref:Microsoft.AspNetCore.Routing.IRouter> 執行：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1333">Routing uses routes implementations of <xref:Microsoft.AspNetCore.Routing.IRouter> to:</span></span>

* <span data-ttu-id="9b58b-1334">將傳入要求對應至「路由處理常式」\*\*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1334">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="9b58b-1335">產生用於回應的 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1335">Generate the URLs used in responses.</span></span>

<span data-ttu-id="9b58b-1336">根據預設，應用程式有一個路由集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1336">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="9b58b-1337">當要求抵達時，集合中的路由會依其存在於集合的順序進行處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1337">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="9b58b-1338">此架構會嘗試對集合中的每個路由呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法，藉以比對傳入要求 URL 與集合中的路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1338">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="9b58b-1339">回應可以根據路由資訊使用路由來產生 URL (例如，針對重新導向或連結)，因此避免硬式編碼的 URL，這有助於可維護性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1339">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="9b58b-1340">路由系統具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1340">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="9b58b-1341">使用路由範本語法以 Token 化路由參數來定義路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1341">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="9b58b-1342">允許傳統式和屬性式端點組態。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1342">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="9b58b-1343"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 可用來判斷 URL 參數是否包含對指定端點條件約束有效的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1343"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="9b58b-1344">應用程式模型（例如 MVC/ Razor Pages）會註冊其所有路由，其具有可預測的路由案例執行。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1344">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="9b58b-1345">回應可以根據路由資訊使用路由來產生 URL (例如，針對重新導向或連結)，因此避免硬式編碼的 URL，這有助於可維護性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1345">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="9b58b-1346">URL 是根據支援任意擴充性的路由所產生。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1346">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="9b58b-1347"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 提供方法來建立 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1347"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
<span data-ttu-id="9b58b-1348">路由會透過 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 類別連線到[中介軟體](xref:fundamentals/middleware/index)管線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1348">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="9b58b-1349">[ASP.NET CORE mvc](xref:mvc/overview) 會將路由新增至中介軟體管線，作為其設定的一部分，並處理 MVC 和 Razor 頁面應用程式中的路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1349">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="9b58b-1350">若要了解如何使用路由作為獨立元件，請參閱[使用路由中介軟體](#use-routing-middleware)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1350">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="9b58b-1351">URL 比對</span><span class="sxs-lookup"><span data-stu-id="9b58b-1351">URL matching</span></span>

<span data-ttu-id="9b58b-1352">URL 比對是路由用來將傳入要求分派給「處理常式」\*\* 的處理序。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1352">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="9b58b-1353">這個處理序是基於 URL 路徑中的資料，但是可以擴展為考慮要求中的任何資料。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1353">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="9b58b-1354">分派要求給不同處理常式的能力，是調整應用程式大小和複雜度的關鍵。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1354">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="9b58b-1355">傳入要求將進入 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>，而後者會依序在每個路由上呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1355">Incoming requests enter the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="9b58b-1356"><xref:Microsoft.AspNetCore.Routing.IRouter> 執行個體可將 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) 設定為非 Null 的 <xref:Microsoft.AspNetCore.Http.RequestDelegate>，來選擇是否要「處理」\*\* 要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1356">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="9b58b-1357">如果路由為要求設定了處理常式，則路由處理會停止，且會叫用該處理常式來處理要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1357">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="9b58b-1358">如果找不到處理要求的路由處理常式，中介軟體會將要求傳遞給要求管線中的下一個中介軟體。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1358">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="9b58b-1359"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的主要輸入是與目前要求建立關聯的 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1359">The primary input to <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="9b58b-1360">[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 和 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) 是在比對路由之後設定的輸出。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1360">The [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="9b58b-1361">呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的比對也會根據到目前為止所執行的要求處理，將 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的屬性設定為適當的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1361">A match that calls <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> also sets the properties of the [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="9b58b-1362">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是「路由值」\*\* 的字典，而路由值產生自路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1362">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="9b58b-1363">這些值通常是透過將 URL 語彙基元化來決定，可以用來接受使用者輸入，或在應用程式內做出進一步的分派決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1363">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="9b58b-1364">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是其他資料的屬性包，而這些資料與相符路由相關。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1364">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="9b58b-1365">提供了 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 來支援與每個路由建立關聯的狀態資料，因此應用程式可以依據符合哪一個路由來制定決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1365"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="9b58b-1366">這些是開發人員定義的值，**不會**以任何方式影響路由的行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1366">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="9b58b-1367">此外，儲藏在 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以是任何類型，對比之下，[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 則必須可轉換成字串或可從字串轉換。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1367">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="9b58b-1368">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是成功符合要求的參與路由清單。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1368">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="9b58b-1369">路由可以用巢狀方式置於彼此內部。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1369">Routes can be nested inside of one another.</span></span> <span data-ttu-id="9b58b-1370"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 屬性會透過導致產生相符項目的路由邏輯樹狀結構反映路徑。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1370">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="9b58b-1371">一般而言，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一個項目是路由集合，應該用於產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1371">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="9b58b-1372"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最後一個項目是相符的路由處理常式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1372">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation"></a><span data-ttu-id="9b58b-1373">URL 產生</span><span class="sxs-lookup"><span data-stu-id="9b58b-1373">URL generation</span></span>

<span data-ttu-id="9b58b-1374">URL 產生是路由可用來依據一組路由值建立 URL 路徑的處理序。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1374">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="9b58b-1375">這可讓您在路由處理常式和存取它們的 URL 之間建立邏輯分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1375">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="9b58b-1376">URL 產生遵循類似的反覆執行處理序，但開頭是呼叫路由集合 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法的使用者或架構程式碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1376">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="9b58b-1377">每個「路由」\*\* 會依序呼叫其 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法，直到傳回非 Null 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData> 為止。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1377">Each *route* has its <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="9b58b-1378"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的主要輸入是：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1378">The primary inputs to <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> are:</span></span>

* [<span data-ttu-id="9b58b-1379">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="9b58b-1379">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [<span data-ttu-id="9b58b-1380">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="9b58b-1380">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [<span data-ttu-id="9b58b-1381">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="9b58b-1381">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

<span data-ttu-id="9b58b-1382">路由主要使用 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 和 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 所提供的路由值，以決定是否可能產生 URL，以及要包含哪些值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1382">Routes primarily use the route values provided by <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> and <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="9b58b-1383"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 是比對目前要求所產生的路由值集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1383">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="9b58b-1384">相反地，<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 是指定如何產生目前作業所需之 URL 的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1384">In contrast, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="9b58b-1385">提供了 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>，以防路由應該取得服務或與目前內容建立關聯的其他資料。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1385">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="9b58b-1386">將 [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) 視為 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) 的覆寫項目集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1386">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="9b58b-1387">URL 產生會嘗試重複使用來自目前要求的路由值，讓您使用相同路由或路由值產生連結的 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1387">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="9b58b-1388"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的輸出是 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1388">The output of <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is a <xref:Microsoft.AspNetCore.Routing.VirtualPathData>.</span></span> <span data-ttu-id="9b58b-1389"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> 是 <xref:Microsoft.AspNetCore.Routing.RouteData> 的平行處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1389"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> is a parallel of <xref:Microsoft.AspNetCore.Routing.RouteData>.</span></span> <span data-ttu-id="9b58b-1390"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> 包含用於輸出 URL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>，以及一些路由應該設定的其他屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1390"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> contains the <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="9b58b-1391">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 屬性包含路由所產生的「虛擬路徑」\*\*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1391">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="9b58b-1392">視您需求的不同，可能需要進一步處理路徑。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1392">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="9b58b-1393">如果您想要以 HTML 呈現產生的 URL，請在前面加上應用程式的基底路徑。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1393">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="9b58b-1394">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) 是成功產生 URL 的路由參考。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1394">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="9b58b-1395">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 屬性是其他資料的字典，而這些資料與產生 URL 的路由相關。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1395">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="9b58b-1396">這是 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 的平行處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1396">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

### <a name="create-routes"></a><span data-ttu-id="9b58b-1397">建立路由</span><span class="sxs-lookup"><span data-stu-id="9b58b-1397">Create routes</span></span>

<span data-ttu-id="9b58b-1398">路由提供 <xref:Microsoft.AspNetCore.Routing.Route> 類別作為 <xref:Microsoft.AspNetCore.Routing.IRouter> 的標準實作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1398">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="9b58b-1399">在呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 時，<xref:Microsoft.AspNetCore.Routing.Route> 會使用「路由範本」\*\* 語法來定義將比對 URL 路徑的模式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1399"><xref:Microsoft.AspNetCore.Routing.Route> uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="9b58b-1400">在呼叫 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 時，<xref:Microsoft.AspNetCore.Routing.Route> 會使用相同的路由範本來產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1400"><xref:Microsoft.AspNetCore.Routing.Route> uses the same route template to generate a URL when <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is called.</span></span>

<span data-ttu-id="9b58b-1401">大部分的應用程式會藉由呼叫 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或其中一個 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定義的類似擴充方法來定建立路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1401">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="9b58b-1402">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 擴充方法都會建立 <xref:Microsoft.AspNetCore.Routing.Route> 的執行個體，並將它新增至路由集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1402">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="9b58b-1403"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由處理常式參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1403"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="9b58b-1404"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 只會新增 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 所處理的路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1404"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="9b58b-1405">預設處理常式為 `IRouter`，該處理常式可能無法處理要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1405">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="9b58b-1406">例如，ASP.NET Core MVC 通常會設定為預設處理常式，只處理符合可用控制器和動作的要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1406">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="9b58b-1407">若要深入了解 MVC 中的路由功能，請參閱 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1407">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="9b58b-1408">下列程式碼範例是典型 ASP.NET Core MVC 路由定義所使用的 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 呼叫範例：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1408">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9b58b-1409">此範本會比對 URL 路徑，並擷取路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1409">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="9b58b-1410">例如，路徑 `/Products/Details/17` 會產生下列路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1410">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="9b58b-1411">路由值是透過將 URL 路徑分割成區段，並比對每個區段與路由範本中的「路由參數」\*\* 名稱來判定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1411">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="9b58b-1412">路由參數為具名。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1412">Route parameters are named.</span></span> <span data-ttu-id="9b58b-1413">參數是透過以括弧 `{ ... }` 括住參數名稱來定義。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1413">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="9b58b-1414">上述範本也可以比對 URL 路徑 `/` 並產生值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1414">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="9b58b-1415">發生這種情況是因為 `{controller}` 和 `{action}` 路由參數有預設值，而 `id` 路由參數為選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1415">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="9b58b-1416">路由參數名稱之後緊接著值的等號 (`=`) 會定義參數預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1416">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="9b58b-1417">路由參數名稱之後的問號 (`?`) 會定義選擇性參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1417">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="9b58b-1418">在路由相符時，具有預設值的路由參數一定\*\* 會產生路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1418">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="9b58b-1419">如果沒有對應的 URL 路徑區段，選擇性參數不會產生路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1419">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="9b58b-1420">如需路由範本情節和語法的詳細描述，請參閱[路由範本參考](#route-template-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1420">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="9b58b-1421">在下列範例中，路由參數定義 `{id:int}` 會定義 `id` 路由參數的[路由條件約束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1421">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="9b58b-1422">此範本會符合 `/Products/Details/17` 等 URL 路徑，但不符合 `/Products/Details/Apples`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1422">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="9b58b-1423">路由條件約束會實作 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，並檢查路由值以進行驗證。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1423">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="9b58b-1424">在此範例中，路由值 `id` 必須可以轉換為整數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1424">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="9b58b-1425">如需架構所提供之路由條件約束的說明，請參閱[路由條件約束參考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1425">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="9b58b-1426"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 的其他多載接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1426">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="9b58b-1427">這些參數通常是用來傳遞匿名類型的物件，其中匿名類型的屬性名稱符合路由參數名稱。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1427">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="9b58b-1428">下列 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 範例會建立對等的路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1428">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="9b58b-1429">對於簡單的路由而言，定義條件約束和預設的內嵌語法可能很方便。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1429">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="9b58b-1430">不過，內嵌語法不支援某些情節 (例如資料語彙基元)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1430">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="9b58b-1431">下列範例將示範一些其他情節：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1431">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="9b58b-1432">上述範本會比對 `/Blog/All-About-Routing/Introduction` 等 URL 路徑，並擷取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1432">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="9b58b-1433">即使範本中沒有任何對應的路由參數，路由也會產生 `controller` 和 `action` 的預設路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1433">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="9b58b-1434">預設值可以在路由範本中指定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1434">Default values can be specified in the route template.</span></span> <span data-ttu-id="9b58b-1435">`article` 路由參數透過在路由參數名稱之前加上一個星號 (`*`) 來定義為 *catch-all*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1435">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="9b58b-1436">全部擷取路由參數會擷取 URL 路徑的其餘部分，而且也可以符合空字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1436">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="9b58b-1437">下列範例會新增路由條件約束和資料語彙基元：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1437">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="9b58b-1438">上述範本會比對 `/en-US/Products/5` 等 URL 路徑，並擷取值 `{ controller = Products, action = Details, id = 5 }` 和資料語彙基元 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1438">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![[區域變數] 視窗語彙基元](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="9b58b-1440">路由類別 URL 產生</span><span class="sxs-lookup"><span data-stu-id="9b58b-1440">Route class URL generation</span></span>

<span data-ttu-id="9b58b-1441"><xref:Microsoft.AspNetCore.Routing.Route> 類別也可以結合一組路由值與其路由範本來產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1441">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="9b58b-1442">這在邏輯上是比對 URL 路徑的反向處理序。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1442">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="9b58b-1443">若要進一步了解 URL 產生，請假設您想要產生的 URL，並考慮路由範本將會如何比對該 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1443">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="9b58b-1444">產生的值為何？</span><span class="sxs-lookup"><span data-stu-id="9b58b-1444">What values would be produced?</span></span> <span data-ttu-id="9b58b-1445">這與 URL 產生在 <xref:Microsoft.AspNetCore.Routing.Route> 類別中的運作方式大致相同。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1445">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="9b58b-1446">下列範例使用一般 ASP.NET Core MVC 預設路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1446">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9b58b-1447">路由值為 `{ controller = Products, action = List }` 時，會產生 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1447">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="9b58b-1448">路由值會取代對應的路由參數，以形成 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1448">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="9b58b-1449">由於 `id` 是選擇性路由參數，因此沒有 `id` 值也可以成功產生 URL。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1449">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="9b58b-1450">路由值為 `{ controller = Home, action = Index }` 時，會產生 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1450">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="9b58b-1451">所提供的路由值符合預設值，因此可以放心地省略這些預設值對應的區段。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1451">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="9b58b-1452">這兩個產生的 URL 會使用下列路由定義 (`/Home/Index` 和 `/`) 反覆存取，並產生用來產生 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1452">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="9b58b-1453">使用 ASP.NET Core MVC 的應用程式應該使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 來產生 URL，而不是直接呼叫路由。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1453">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="9b58b-1454">如需 URL 產生的詳細資訊，請參閱 [URI 產生參考](#url-generation-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1454">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="9b58b-1455">使用路由中介軟體</span><span class="sxs-lookup"><span data-stu-id="9b58b-1455">Use routing middleware</span></span>

<span data-ttu-id="9b58b-1456">參考應用程式專案檔中的 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1456">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="9b58b-1457">在 `Startup.ConfigureServices` 中，將路由新增至服務容器：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1457">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="9b58b-1458">路由必須設定在 `Startup.Configure` 方法中。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1458">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="9b58b-1459">範例應用程式使用下列 API：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1459">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="9b58b-1460"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>：僅符合 HTTP GET 要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1460"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="9b58b-1461">下表顯示使用指定 URL 的回應。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1461">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="9b58b-1462">URI</span><span class="sxs-lookup"><span data-stu-id="9b58b-1462">URI</span></span>                    | <span data-ttu-id="9b58b-1463">回應</span><span class="sxs-lookup"><span data-stu-id="9b58b-1463">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="9b58b-1464">Hello!</span><span class="sxs-lookup"><span data-stu-id="9b58b-1464">Hello!</span></span> <span data-ttu-id="9b58b-1465">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="9b58b-1465">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="9b58b-1466">Hello!</span><span class="sxs-lookup"><span data-stu-id="9b58b-1466">Hello!</span></span> <span data-ttu-id="9b58b-1467">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="9b58b-1467">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="9b58b-1468">Hello!</span><span class="sxs-lookup"><span data-stu-id="9b58b-1468">Hello!</span></span> <span data-ttu-id="9b58b-1469">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="9b58b-1469">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="9b58b-1470">要求失敗，沒有相符項目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1470">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="9b58b-1471">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="9b58b-1471">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="9b58b-1472">要求失敗，僅符合 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1472">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="9b58b-1473">要求失敗，沒有相符項目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1473">The request falls through, no match.</span></span>              |

<span data-ttu-id="9b58b-1474">如果您要設定單一路由，請呼叫傳入 `IRouter` 執行個體的 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1474">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="9b58b-1475">您不需要使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1475">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

<span data-ttu-id="9b58b-1476">架構會提供一組擴充方法來建立路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1476">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="9b58b-1477">其中一些列出的方法 (例如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>) 需要 <xref:Microsoft.AspNetCore.Http.RequestDelegate>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1477">Some of listed methods, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, require a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="9b58b-1478">路由相符時，<xref:Microsoft.AspNetCore.Http.RequestDelegate> 會作為「路由處理常式」\*\* 使用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1478">The <xref:Microsoft.AspNetCore.Http.RequestDelegate> is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="9b58b-1479">此系列中的其他方法允許設定中介軟體管線，以作為路由處理常式使用。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1479">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="9b58b-1480">如果 `Map*` 方法不接受處理常式 (例如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>)，則會使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1480">If the `Map*` method doesn't accept a handler, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

<span data-ttu-id="9b58b-1481">`Map[Verb]` 方法會使用條件約束，將路由限制為方法名稱中的 HTTP 指令動詞。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1481">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="9b58b-1482">如需範例，請參閱 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 與 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1482">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="9b58b-1483">路由範本參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-1483">Route template reference</span></span>

<span data-ttu-id="9b58b-1484">大括弧 (`{ ... }`) 內的語彙基元定義路由相符時會繫結的「路由參數」\*\*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1484">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="9b58b-1485">您可以在路由區段中定義多個路由參數，但其必須以常值分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1485">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="9b58b-1486">例如，`{controller=Home}{action=Index}` 不是有效的路由，因為 `{controller}` 與 `{action}` 之間沒有任何常值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1486">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="9b58b-1487">這些路由參數必須有一個名稱，並且可以指定其他屬性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1487">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="9b58b-1488">路由參數之外的常值文字 (例如，`{id}`) 和路徑分隔符號 `/` 必須符合 URL 中的文字。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1488">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="9b58b-1489">文字比對會區分大小寫，並以 URL 路徑的已解碼表示法為基礎。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1489">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="9b58b-1490">若要比對常值路由參數分隔符號 (`{` 或 `}`)，請重複字元 (`{{` 或 `}}`) 來將分隔符號逸出。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1490">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="9b58b-1491">URL 模式嘗試擷取具有選擇性副檔名的檔案名稱時，具有其他考量。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1491">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="9b58b-1492">以範本 `files/{filename}.{ext?}` 為例。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1492">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="9b58b-1493">當 `filename` 和 `ext` 都存在值時，就會填入這兩個值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1493">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="9b58b-1494">如果 URL 中只有 `filename` 存在值，由於結尾的句點 (`.`) 是選擇性項目，因此路由相符。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1494">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="9b58b-1495">下列 URL 符合此路由：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1495">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="9b58b-1496">您可以使用星號 (`*`) 作為路由參數的前置詞，以繫結至 URI 的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1496">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="9b58b-1497">這稱為「全部擷取」\*\* 參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1497">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="9b58b-1498">例如，`blog/{*slug}` 符合以 `/blog` 開頭且其後有任何值 (這會指派給 `slug` 路由值) 的所有 URI。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1498">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="9b58b-1499">全部擷取參數也可以符合空字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1499">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="9b58b-1500">當使用路由產生 URL (包括路徑分隔符號 (`/`) 字元) 時，catch-all 參數會逸出適當的字元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1500">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="9b58b-1501">例如，路由值為 `{ path = "my/path" }` 的路由 `foo/{*path}` 會產生 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1501">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="9b58b-1502">請注意逸出的斜線。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1502">Note the escaped forward slash.</span></span>

<span data-ttu-id="9b58b-1503">路由參數可能有「預設值」\*\*，指定方法是在參數名稱之後指定預設值，並以等號 (`=`) 分隔。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1503">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="9b58b-1504">例如，`{controller=Home}` 定義 `Home` 作為 `controller` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1504">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="9b58b-1505">如果 URL 中沒有用於參數的任何值，則會使用預設值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1505">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="9b58b-1506">路由參數也可以設為選擇性，方法是在參數名稱結尾附加問號 (`?`)，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1506">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="9b58b-1507">選擇性值與預設路由參數之間的差異在於，具有預設值的路由參數一定會產生值&mdash;選擇性參數只有在要求 URL 提供值時才會有值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1507">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="9b58b-1508">路由參數可能具有條件約束，這些條件約束必須符合與 URL 繫結的路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1508">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="9b58b-1509">在路由參數名稱之後新增分號 (`:`) 和條件約束名稱，即可指定路由參數的「內嵌條件約束」\*\*。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1509">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="9b58b-1510">如果條件約束需要引數，這些引數會在條件約束名稱後面以括弧 (`(...)`) 括住。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1510">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="9b58b-1511">指定多個內嵌條件約束的方法是附加另一個冒號 (`:`) 和條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1511">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="9b58b-1512">條件約束名稱和引述會傳遞至 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服務來建立 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的執行個體，以用於 URL 處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1512">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="9b58b-1513">例如，路由範本 `blog/{article:minlength(10)}` 指定具有引數 `10` 的 `minlength` 條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1513">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="9b58b-1514">如需路由條件約束詳細資訊和架構所提供的條件約束清單，請參閱[路由條件約束參考](#route-constraint-reference)一節。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1514">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="9b58b-1515">下表示範範例路由範本及其行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1515">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="9b58b-1516">路由範本</span><span class="sxs-lookup"><span data-stu-id="9b58b-1516">Route Template</span></span>                           | <span data-ttu-id="9b58b-1517">範例比對 URI</span><span class="sxs-lookup"><span data-stu-id="9b58b-1517">Example Matching URI</span></span>    | <span data-ttu-id="9b58b-1518">要求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="9b58b-1518">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="9b58b-1519">只比對單一路徑 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1519">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="9b58b-1520">比對並將 `Page` 設定為 `Home`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1520">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="9b58b-1521">比對並將 `Page` 設定為 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1521">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="9b58b-1522">對應至 `Products` 控制器和 `List` 動作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1522">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="9b58b-1523">對應至 `Products` 控制器和 `Details` 動作 (`id` 設定為 123)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1523">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="9b58b-1524">對應至 `Home` 控制器和 `Index` 方法 (會忽略 `id`)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1524">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="9b58b-1525">使用範本通常是最簡單的路由方式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1525">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="9b58b-1526">條件約束和預設值也可以在路由範本外部指定。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1526">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="9b58b-1527">啟用[記錄](xref:fundamentals/logging/index)以查看內建路由實作 (例如 <xref:Microsoft.AspNetCore.Routing.Route>) 如何符合要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1527">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="9b58b-1528">路由條件約束參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-1528">Route constraint reference</span></span>

<span data-ttu-id="9b58b-1529">路由條件約束執行時機是出現符合傳入 URL 的項目，並將 URL 路徑語彙基元化成路由值時。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1529">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="9b58b-1530">路由條件約束通常會透過路由範本檢查相關聯的路由值，並對是否可接受值做出是/否決策。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1530">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="9b58b-1531">某些路由條件約束會使用路由值以外的資料，以考慮是否可以路由要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1531">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="9b58b-1532">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以依據其 HTTP 指令動詞接受或拒絕要求。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1532">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="9b58b-1533">條件約束可用於路由要求和連結產生。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1533">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="9b58b-1534">請勿針對**輸入驗證**使用條件約束。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1534">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="9b58b-1535">如果針對**輸入驗證**使用條件約束，則無效的輸入會導致產生「404 - 找不到」\*\* 回應，而不是「400 - 錯誤要求」\*\* 與適當的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1535">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="9b58b-1536">路由條件約束會用來**釐清**類似的路由，而不是用來驗證特定路由的輸入。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1536">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="9b58b-1537">下表示範範例路由條件約束及其預期行為。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1537">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="9b58b-1538">constraint (條件約束)</span><span class="sxs-lookup"><span data-stu-id="9b58b-1538">constraint</span></span> | <span data-ttu-id="9b58b-1539">範例</span><span class="sxs-lookup"><span data-stu-id="9b58b-1539">Example</span></span> | <span data-ttu-id="9b58b-1540">範例相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-1540">Example Matches</span></span> | <span data-ttu-id="9b58b-1541">附註</span><span class="sxs-lookup"><span data-stu-id="9b58b-1541">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="9b58b-1542">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1542">`123456789`, `-123456789`</span></span> | <span data-ttu-id="9b58b-1543">符合任何整數</span><span class="sxs-lookup"><span data-stu-id="9b58b-1543">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="9b58b-1544">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1544">`true`, `FALSE`</span></span> | <span data-ttu-id="9b58b-1545">符合 `true` 或 `false` (不區分大小寫)</span><span class="sxs-lookup"><span data-stu-id="9b58b-1545">Matches `true` or `false` (case-insensitive)</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="9b58b-1546">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1546">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="9b58b-1547">符合非變異 `DateTime` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1547">Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-1548">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1548">See  preceding warning.</span></span>|
| `decimal` | `{price:decimal}` | <span data-ttu-id="9b58b-1549">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1549">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="9b58b-1550">符合非變異 `decimal` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1550">Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-1551">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1551">See  preceding warning.</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="9b58b-1552">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1552">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="9b58b-1553">符合非變異 `double` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1553">Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-1554">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1554">See  preceding warning.</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="9b58b-1555">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1555">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="9b58b-1556">符合非變異 `float` 文化特性中的有效值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1556">Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="9b58b-1557">請參閱前面的警告。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1557">See  preceding warning.</span></span>|
| `guid` | `{id:guid}` | <span data-ttu-id="9b58b-1558">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1558">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="9b58b-1559">符合有效的 `Guid` 值</span><span class="sxs-lookup"><span data-stu-id="9b58b-1559">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="9b58b-1560">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1560">`123456789`, `-123456789`</span></span> | <span data-ttu-id="9b58b-1561">符合有效的 `long` 值</span><span class="sxs-lookup"><span data-stu-id="9b58b-1561">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="9b58b-1562">字串必須至少有 4 個字元</span><span class="sxs-lookup"><span data-stu-id="9b58b-1562">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="9b58b-1563">字串不能超過 8 個字元</span><span class="sxs-lookup"><span data-stu-id="9b58b-1563">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="9b58b-1564">字串長度必須剛好是 12 個字元</span><span class="sxs-lookup"><span data-stu-id="9b58b-1564">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="9b58b-1565">字串長度必須至少有 8 個字元，但不能超過 16 個字元</span><span class="sxs-lookup"><span data-stu-id="9b58b-1565">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="9b58b-1566">整數值必須至少為 18</span><span class="sxs-lookup"><span data-stu-id="9b58b-1566">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="9b58b-1567">整數值不能超過 120</span><span class="sxs-lookup"><span data-stu-id="9b58b-1567">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="9b58b-1568">整數值必須至少為 18，但不能超過 120</span><span class="sxs-lookup"><span data-stu-id="9b58b-1568">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="9b58b-1569">字串必須包含一或多個字母字元 (`a`-`z`，不區分大小寫)</span><span class="sxs-lookup"><span data-stu-id="9b58b-1569">String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive)</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="9b58b-1570">字串必須符合規則運算式 (請參閱有關定義規則運算式的提示)</span><span class="sxs-lookup"><span data-stu-id="9b58b-1570">String must match the regular expression (see tips about defining a regular expression)</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="9b58b-1571">用來強制執行在 URL 產生期間呈現非參數值</span><span class="sxs-lookup"><span data-stu-id="9b58b-1571">Used to enforce that a non-parameter value is present during URL generation</span></span> |

<span data-ttu-id="9b58b-1572">以冒號分隔的多個條件約束，可以套用至單一參數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1572">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="9b58b-1573">例如，下列條件約束會將參數限制在 1 或更大的整數值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1573">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="9b58b-1574">確認 URL 可以轉換成 CLR 類型的路由條件約束 (例如 `int` 或 `DateTime`) 一律使用不因國別而異的文化特性。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1574">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="9b58b-1575">這些條件約束假設 URL 不可當地語系化。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1575">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="9b58b-1576">架構提供的路由條件約束不會修改路由值中儲存的值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1576">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="9b58b-1577">所有從 URL 剖析而來的路由值會儲存為字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1577">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="9b58b-1578">例如，`float` 條件約束會嘗試將路由值轉換成浮點數，但轉換的值只能用來確認它可以轉換成浮點數。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1578">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="9b58b-1579">規則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1579">Regular expressions</span></span>

<span data-ttu-id="9b58b-1580">ASP.NET Core 架構將 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` 新增至規則運算式建構函式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1580">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="9b58b-1581">如需這些成員的說明，請參閱 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1581">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="9b58b-1582">規則運算式使用的分隔符號和語彙基元，類似於路由和 C# 語言所使用的分隔符號和語彙基元。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1582">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="9b58b-1583">規則運算式的語彙基元必須逸出。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1583">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="9b58b-1584">若要在路由中使用規則運算式 `^\d{3}-\d{2}-\d{4}$`，運算式必須以字串中所提供的 `\` (單一反斜線) 字元作為 C# 原始程式檔中的 `\\` (雙反斜線) 字元，才能逸出 `\` 字串逸出字元 (除非使用[逐字字串常值](/dotnet/csharp/language-reference/keywords/string))。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1584">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="9b58b-1585">若要逸出路由參數分隔符號字元 (`{`、`}`、`[`、`]`)，請在運算式中使用雙字元 (`{{`、`}`、`[[`、`]]`).</span><span class="sxs-lookup"><span data-stu-id="9b58b-1585">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="9b58b-1586">下表顯示規則運算式和逸出的版本。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1586">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="9b58b-1587">規則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1587">Regular Expression</span></span>    | <span data-ttu-id="9b58b-1588">逸出的規則運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1588">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="9b58b-1589">路由中所使用的規則運算式通常以插入號 (`^`) 字元開頭，並符合字串的開始位置。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1589">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="9b58b-1590">運算式通常以貨幣符號 (`$`) 字元結尾，並符合字串的結尾。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1590">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="9b58b-1591">`^` 和 `$` 字元可確保規則運算式符合整個路由參數值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1591">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="9b58b-1592">若不使用 `^` 與 `$` 字元，規則運算式會比對字串內的所有部分字串，這通常不是您想要的結果。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1592">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="9b58b-1593">下表提供範例，並說明它們符合或無法符合的原因。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1593">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="9b58b-1594">運算是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1594">Expression</span></span>   | <span data-ttu-id="9b58b-1595">String</span><span class="sxs-lookup"><span data-stu-id="9b58b-1595">String</span></span>    | <span data-ttu-id="9b58b-1596">相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-1596">Match</span></span> | <span data-ttu-id="9b58b-1597">註解</span><span class="sxs-lookup"><span data-stu-id="9b58b-1597">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-1598">hello</span><span class="sxs-lookup"><span data-stu-id="9b58b-1598">hello</span></span>     | <span data-ttu-id="9b58b-1599">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1599">Yes</span></span>   | <span data-ttu-id="9b58b-1600">子字串相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-1600">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-1601">123abc456</span><span class="sxs-lookup"><span data-stu-id="9b58b-1601">123abc456</span></span> | <span data-ttu-id="9b58b-1602">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1602">Yes</span></span>   | <span data-ttu-id="9b58b-1603">子字串相符項目</span><span class="sxs-lookup"><span data-stu-id="9b58b-1603">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-1604">mz</span><span class="sxs-lookup"><span data-stu-id="9b58b-1604">mz</span></span>        | <span data-ttu-id="9b58b-1605">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1605">Yes</span></span>   | <span data-ttu-id="9b58b-1606">符合運算式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1606">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="9b58b-1607">MZ</span><span class="sxs-lookup"><span data-stu-id="9b58b-1607">MZ</span></span>        | <span data-ttu-id="9b58b-1608">是</span><span class="sxs-lookup"><span data-stu-id="9b58b-1608">Yes</span></span>   | <span data-ttu-id="9b58b-1609">不區分大小寫</span><span class="sxs-lookup"><span data-stu-id="9b58b-1609">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="9b58b-1610">hello</span><span class="sxs-lookup"><span data-stu-id="9b58b-1610">hello</span></span>     | <span data-ttu-id="9b58b-1611">否</span><span class="sxs-lookup"><span data-stu-id="9b58b-1611">No</span></span>    | <span data-ttu-id="9b58b-1612">請參閱上述的 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1612">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="9b58b-1613">123abc456</span><span class="sxs-lookup"><span data-stu-id="9b58b-1613">123abc456</span></span> | <span data-ttu-id="9b58b-1614">否</span><span class="sxs-lookup"><span data-stu-id="9b58b-1614">No</span></span>    | <span data-ttu-id="9b58b-1615">請參閱上述的 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="9b58b-1615">See `^` and `$` above</span></span> |

<span data-ttu-id="9b58b-1616">如需規則運算式語法的詳細資訊，請參閱 [.NET Framework 規則運算式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1616">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="9b58b-1617">若要將參數限制為一組已知的可能值，請使用規則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1617">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="9b58b-1618">例如，`{action:regex(^(list|get|create)$)}` 只會將 `action` 路由值與 `list`、`get` 或 `create` 相符。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1618">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="9b58b-1619">如果已傳入條件約束字典，字串 `^(list|get|create)$` 則是對等項目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1619">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="9b58b-1620">已傳入條件約束字典 (未內嵌在範本內) 的條件約束，即使不符合其中一個已知的條件約束，也會被視為規則運算式。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1620">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="9b58b-1621">自訂路由限制式</span><span class="sxs-lookup"><span data-stu-id="9b58b-1621">Custom Route Constraints</span></span>

<span data-ttu-id="9b58b-1622">除了內建的路由限制式之外，自訂路由限制式也可以透過實作 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 介面來建立。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1622">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="9b58b-1623"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 介面包含單一方法 `Match`，此方法會在滿足限制式時傳回 `true`，否則會傳回 `false`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1623">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="9b58b-1624">若要使用自訂 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，路由限制式型別必須必須向應用程式的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> (在應用程式的服務容器中) 註冊。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1624">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="9b58b-1625"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是一個目錄，它將路由限制式機碼對應到可驗證那些限制式的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 實作。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1625">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="9b58b-1626">更新應用程式的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 時，可在 `Startup.ConfigureServices` 中於進行 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼叫時更新，或透過使用 `services.Configure<RouteOptions>` 直接設定 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 來更新。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1626">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="9b58b-1627">例如：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1627">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="9b58b-1628">限制式接著能以一般方式套用到路由 (使用註冊限制式型別時使用名稱)。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1628">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="9b58b-1629">例如：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1629">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a><span data-ttu-id="9b58b-1630">URL 產生參考</span><span class="sxs-lookup"><span data-stu-id="9b58b-1630">URL generation reference</span></span>

<span data-ttu-id="9b58b-1631">下列範例示範如何在指定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情況下產生路由的連結。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1631">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="9b58b-1632">在上述範例的結尾產生的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 是 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1632">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="9b58b-1633">字典提供「追蹤套件路由」範本 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1633">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="9b58b-1634">如需詳細資訊，請參閱[使用路由中介軟體](#use-routing-middleware)一節或[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1634">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="9b58b-1635"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 建構函式的第二個參數是「環境值」\*\* 的集合。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1635">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="9b58b-1636">環境值便於使用，因為它們會限制開發人員必須在要求內容中指定的值數目。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1636">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="9b58b-1637">目前要求的目前路由值被視為用於連結產生的環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1637">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="9b58b-1638">在 ASP.NET Core MVC 應用程式 `HomeController` 的 `About` 動作中，您不需要指定控制器路由值以連結到 `Index` 動作&mdash;會使用 `Home` 的環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1638">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="9b58b-1639">不符合參數的環境值會予以忽略。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1639">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="9b58b-1640">當明確提供的值覆寫環境值時，也會忽略環境值。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1640">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="9b58b-1641">URL 中的比對是從左到右。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1641">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="9b58b-1642">明確提供但不符合路由區段的值會新增至查詢字串。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1642">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="9b58b-1643">下表顯示使用路由範本 `{controller}/{action}/{id?}` 時的結果。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1643">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="9b58b-1644">環境值</span><span class="sxs-lookup"><span data-stu-id="9b58b-1644">Ambient Values</span></span>                     | <span data-ttu-id="9b58b-1645">明確值</span><span class="sxs-lookup"><span data-stu-id="9b58b-1645">Explicit Values</span></span>                        | <span data-ttu-id="9b58b-1646">結果</span><span class="sxs-lookup"><span data-stu-id="9b58b-1646">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="9b58b-1647">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1647">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-1648">action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1648">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="9b58b-1649">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1649">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-1650">controller = "Order", action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1650">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="9b58b-1651">controller = "Home", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1651">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="9b58b-1652">action = "About"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1652">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="9b58b-1653">controller = "Home"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1653">controller = "Home"</span></span>                | <span data-ttu-id="9b58b-1654">action = "About", color = "Red"</span><span class="sxs-lookup"><span data-stu-id="9b58b-1654">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="9b58b-1655">如果路由具有未對應至參數的預設值，且該值會明確提供，它必須符合預設值：</span><span class="sxs-lookup"><span data-stu-id="9b58b-1655">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="9b58b-1656">連結產生只有在提供了 `controller` 與 `action` 的相符值時，才會產生此路由的連結。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1656">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="9b58b-1657">複雜區段</span><span class="sxs-lookup"><span data-stu-id="9b58b-1657">Complex segments</span></span>

<span data-ttu-id="9b58b-1658">複雜區段 (例如，`[Route("/x{token}y")]`) 會透過以非窮盡的方式，由右至左比對常值來處理。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1658">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="9b58b-1659">請參閱[此程式碼](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解如何比對複雜區段的詳細解釋。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1659">See [this code](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="9b58b-1660">[程式法範例](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)不是由 ASP.NET Core 使用，但它提供一個好的複雜區段解釋。</span><span class="sxs-lookup"><span data-stu-id="9b58b-1660">The [code sample](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>

::: moniker-end
