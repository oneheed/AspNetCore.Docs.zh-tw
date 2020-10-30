---
title: 從 ASP.NET Web API 遷移至 ASP.NET Core
author: ardalis
description: 瞭解如何將 web API 的執行從 ASP.NET 4.x Web API 遷移至 ASP.NET Core MVC。
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/webapi
ms.openlocfilehash: 320805c0d40bf06cee384e6d98caea5c420d45bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061466"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="78212-103">從 ASP.NET Web API 遷移至 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78212-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="78212-104">[Scott Addie](https://twitter.com/scott_addie)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="78212-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="78212-105">ASP.NET 4.x Web API 是一種 HTTP 服務，可達到各式各樣的用戶端，包括瀏覽器和行動裝置。</span><span class="sxs-lookup"><span data-stu-id="78212-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="78212-106">ASP.NET Core 將 ASP.NET 4.x 的 MVC 和 Web API 應用程式模型合併為單一程式設計模型，稱為 ASP.NET Core MVC。</span><span class="sxs-lookup"><span data-stu-id="78212-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="78212-107">本文示範從 ASP.NET 4.x Web API 遷移至 ASP.NET Core MVC 所需的步驟。</span><span class="sxs-lookup"><span data-stu-id="78212-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="78212-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="78212-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="78212-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="78212-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="78212-110">複習 ASP.NET 4.x Web API 專案</span><span class="sxs-lookup"><span data-stu-id="78212-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="78212-111">本文使用消費者入門中建立的 *ProductsApp* 專案搭配 [ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="78212-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="78212-112">在該專案中，基本的 ASP.NET 4.x Web API 專案會依照下列方式設定。</span><span class="sxs-lookup"><span data-stu-id="78212-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="78212-113">在 *Global.asax.cs* 中，呼叫會進行 `WebApiConfig.Register` 下列動作：</span><span class="sxs-lookup"><span data-stu-id="78212-113">In *Global.asax.cs* , a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="78212-114">`WebApiConfig`類別可在 *App_Start* 資料夾中找到，並具有靜態 `Register` 方法：</span><span class="sxs-lookup"><span data-stu-id="78212-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="78212-115">上述類別：</span><span class="sxs-lookup"><span data-stu-id="78212-115">The preceding class:</span></span>

* <span data-ttu-id="78212-116">設定 [屬性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，但實際上不會使用它。</span><span class="sxs-lookup"><span data-stu-id="78212-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="78212-117">設定路由表。</span><span class="sxs-lookup"><span data-stu-id="78212-117">Configures the routing table.</span></span>
<span data-ttu-id="78212-118">範例程式碼會預期 Url 必須符合格式 `/api/{controller}/{id}` ，而且 `{id}` 是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="78212-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="78212-119">下列各節將示範如何將 Web API 專案遷移至 ASP.NET Core MVC。</span><span class="sxs-lookup"><span data-stu-id="78212-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="78212-120">建立目的地專案</span><span class="sxs-lookup"><span data-stu-id="78212-120">Create the destination project</span></span>

<span data-ttu-id="78212-121">在 Visual Studio 中建立新的空白解決方案，並新增 ASP.NET 4.x Web API 專案以進行遷移：</span><span class="sxs-lookup"><span data-stu-id="78212-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="78212-122">從 [ **檔案** ] 功能表選取 [ **新增** > **專案** ]。</span><span class="sxs-lookup"><span data-stu-id="78212-122">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="78212-123">選取 **空白的解決方案** 範本，然後選取 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="78212-123">Select the **Blank Solution** template and select **Next** .</span></span>
1. <span data-ttu-id="78212-124">將方案命名為 *WebAPIMigration* 。</span><span class="sxs-lookup"><span data-stu-id="78212-124">Name the solution *WebAPIMigration* .</span></span> <span data-ttu-id="78212-125">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="78212-125">Select **Create** .</span></span>
1. <span data-ttu-id="78212-126">將現有的 *ProductsApp* 專案加入至方案。</span><span class="sxs-lookup"><span data-stu-id="78212-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="78212-127">新增要遷移至的 API 專案：</span><span class="sxs-lookup"><span data-stu-id="78212-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="78212-128">將新的 **ASP.NET Core Web 應用程式** 專案加入至方案。</span><span class="sxs-lookup"><span data-stu-id="78212-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="78212-129">在 [ **設定您的新專案** ] 對話方塊中，將專案命名為 *ProductsCore* ，然後選取 [ **建立** ]。</span><span class="sxs-lookup"><span data-stu-id="78212-129">In the **Configure your new project** dialog, Name the project *ProductsCore* , and select **Create** .</span></span>
1. <span data-ttu-id="78212-130">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。</span><span class="sxs-lookup"><span data-stu-id="78212-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="78212-131">選取 [API]  專案範本，然後選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="78212-131">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="78212-132">從新的 *ProductsCore* 專案中移除 *WeatherForecast.cs* 和 *控制器/WeatherForecastController .cs* 範例檔案。</span><span class="sxs-lookup"><span data-stu-id="78212-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="78212-133">方案現在包含兩個專案。</span><span class="sxs-lookup"><span data-stu-id="78212-133">The solution now contains two projects.</span></span> <span data-ttu-id="78212-134">下列各節說明如何將 *ProductsApp* 專案的內容遷移至 *ProductsCore* 專案。</span><span class="sxs-lookup"><span data-stu-id="78212-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="78212-135">移轉組態</span><span class="sxs-lookup"><span data-stu-id="78212-135">Migrate configuration</span></span>

<span data-ttu-id="78212-136">ASP.NET Core 不會使用 *App_Start* 資料夾或 *global.asax* 檔案。</span><span class="sxs-lookup"><span data-stu-id="78212-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="78212-137">此外，也會在發行時新增 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="78212-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="78212-138">`Startup` 類別：</span><span class="sxs-lookup"><span data-stu-id="78212-138">The `Startup` class:</span></span>

* <span data-ttu-id="78212-139">取代 *global.asax* 。</span><span class="sxs-lookup"><span data-stu-id="78212-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="78212-140">處理所有應用程式啟動工作。</span><span class="sxs-lookup"><span data-stu-id="78212-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="78212-141">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="78212-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="78212-142">遷移模型和控制器</span><span class="sxs-lookup"><span data-stu-id="78212-142">Migrate models and controllers</span></span>

<span data-ttu-id="78212-143">下列程式碼顯示 `ProductsController` 要為 ASP.NET Core 更新的：</span><span class="sxs-lookup"><span data-stu-id="78212-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="78212-144">更新 `ProductsController` ASP.NET Core 的：</span><span class="sxs-lookup"><span data-stu-id="78212-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="78212-145">將 *控制器/productscontroller.cs* 和 *模型* 資料夾從原始專案複製到新專案。</span><span class="sxs-lookup"><span data-stu-id="78212-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="78212-146">將複製的檔案根命名空間變更為 `ProductsCore` 。</span><span class="sxs-lookup"><span data-stu-id="78212-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="78212-147">將 `using ProductsApp.Models;` 語句更新為 `using ProductsCore.Models;` 。</span><span class="sxs-lookup"><span data-stu-id="78212-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="78212-148">下列元件不存在於 ASP.NET Core 中：</span><span class="sxs-lookup"><span data-stu-id="78212-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="78212-149">`ApiController` 類別</span><span class="sxs-lookup"><span data-stu-id="78212-149">`ApiController` class</span></span>
* <span data-ttu-id="78212-150">`System.Web.Http` 命名空間</span><span class="sxs-lookup"><span data-stu-id="78212-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="78212-151">`IHttpActionResult` 介面</span><span class="sxs-lookup"><span data-stu-id="78212-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="78212-152">進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="78212-152">Make the following changes:</span></span>

1. <span data-ttu-id="78212-153">將 `ApiController` 變更為 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。</span><span class="sxs-lookup"><span data-stu-id="78212-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="78212-154">加入 `using Microsoft.AspNetCore.Mvc;` 以解析 `ControllerBase` 參考。</span><span class="sxs-lookup"><span data-stu-id="78212-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="78212-155">刪除 `using System.Web.Http;`。</span><span class="sxs-lookup"><span data-stu-id="78212-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="78212-156">將 `GetProduct` 動作的傳回類型從變更 `IHttpActionResult` 為 `ActionResult<Product>` 。</span><span class="sxs-lookup"><span data-stu-id="78212-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="78212-157">簡化 `GetProduct` 動作的 `return` 語句，如下所示：</span><span class="sxs-lookup"><span data-stu-id="78212-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="78212-158">設定路由</span><span class="sxs-lookup"><span data-stu-id="78212-158">Configure routing</span></span>

<span data-ttu-id="78212-159">ASP.NET Core *API* 專案範本在產生的程式碼中包含端點路由設定。</span><span class="sxs-lookup"><span data-stu-id="78212-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="78212-160">下列 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> 內容會 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 呼叫：</span><span class="sxs-lookup"><span data-stu-id="78212-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="78212-161">在 [中介軟體](xref:fundamentals/middleware/index) 管線中註冊路由對應和端點執行。</span><span class="sxs-lookup"><span data-stu-id="78212-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="78212-162">取代 *ProductsApp* 專案的 *App_Start/webapiconfig.cs* 檔。</span><span class="sxs-lookup"><span data-stu-id="78212-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="78212-163">設定路由，如下所示：</span><span class="sxs-lookup"><span data-stu-id="78212-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="78212-164">`ProductsController`以下列屬性標示類別：</span><span class="sxs-lookup"><span data-stu-id="78212-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="78212-165">上述屬性會設定 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 控制器的屬性路由模式。</span><span class="sxs-lookup"><span data-stu-id="78212-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="78212-166">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)屬性會讓屬性路由成為此控制器中所有動作的需求。</span><span class="sxs-lookup"><span data-stu-id="78212-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="78212-167">屬性路由支援權杖，例如 `[controller]` 和 `[action]` 。</span><span class="sxs-lookup"><span data-stu-id="78212-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="78212-168">在執行時間，每個權杖都會分別取代為已套用屬性的控制器或動作名稱。</span><span class="sxs-lookup"><span data-stu-id="78212-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="78212-169">標記：</span><span class="sxs-lookup"><span data-stu-id="78212-169">The tokens:</span></span>
    * <span data-ttu-id="78212-170">減少專案中魔術字串的數目。</span><span class="sxs-lookup"><span data-stu-id="78212-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="78212-171">套用自動重新命名重構時，請確定路由與對應的控制器和動作保持同步。</span><span class="sxs-lookup"><span data-stu-id="78212-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="78212-172">啟用動作的 HTTP Get 要求 `ProductController` ：</span><span class="sxs-lookup"><span data-stu-id="78212-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="78212-173">將 [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 屬性套用至 `GetAllProducts` 動作。</span><span class="sxs-lookup"><span data-stu-id="78212-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="78212-174">將 `[HttpGet("{id}")]` 屬性套用至 `GetProduct` 動作。</span><span class="sxs-lookup"><span data-stu-id="78212-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="78212-175">執行已遷移的專案，然後流覽至 `/api/products` 。</span><span class="sxs-lookup"><span data-stu-id="78212-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="78212-176">三個產品的完整清單隨即出現。</span><span class="sxs-lookup"><span data-stu-id="78212-176">A full list of three products appears.</span></span> <span data-ttu-id="78212-177">瀏覽至 `/api/products/1`。</span><span class="sxs-lookup"><span data-stu-id="78212-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="78212-178">第一個產品隨即出現。</span><span class="sxs-lookup"><span data-stu-id="78212-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78212-179">其他資源</span><span class="sxs-lookup"><span data-stu-id="78212-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="78212-180">必要條件</span><span class="sxs-lookup"><span data-stu-id="78212-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="78212-181">複習 ASP.NET 4.x Web API 專案</span><span class="sxs-lookup"><span data-stu-id="78212-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="78212-182">本文使用消費者入門中建立的 *ProductsApp* 專案搭配 [ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="78212-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="78212-183">在該專案中，基本的 ASP.NET 4.x Web API 專案會依照下列方式設定。</span><span class="sxs-lookup"><span data-stu-id="78212-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="78212-184">在 *Global.asax.cs* 中，呼叫會進行 `WebApiConfig.Register` 下列動作：</span><span class="sxs-lookup"><span data-stu-id="78212-184">In *Global.asax.cs* , a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="78212-185">`WebApiConfig`類別可在 *App_Start* 資料夾中找到，並具有靜態 `Register` 方法：</span><span class="sxs-lookup"><span data-stu-id="78212-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="78212-186">這個類別會設定 [屬性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，雖然它實際上不是在專案中使用。</span><span class="sxs-lookup"><span data-stu-id="78212-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="78212-187">它也會設定 ASP.NET Web API 所使用的路由表。</span><span class="sxs-lookup"><span data-stu-id="78212-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="78212-188">在此情況下，ASP.NET 4.x Web API 預期 Url 必須符合格式 `/api/{controller}/{id}` ，而且 `{id}` 是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="78212-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="78212-189">下列各節將示範如何將 Web API 專案遷移至 ASP.NET Core MVC。</span><span class="sxs-lookup"><span data-stu-id="78212-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="78212-190">建立目的地專案</span><span class="sxs-lookup"><span data-stu-id="78212-190">Create the destination project</span></span>

<span data-ttu-id="78212-191">在 Visual Studio 中完成下列步驟：</span><span class="sxs-lookup"><span data-stu-id="78212-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="78212-192">移至 **[**  >  **新增**  >  **專案** ]  >  **其他專案類型**  >  **Visual Studio 方案** 。</span><span class="sxs-lookup"><span data-stu-id="78212-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions** .</span></span> <span data-ttu-id="78212-193">選取 [ **空白方案** ]，並將方案命名為 *WebAPIMigration* 。</span><span class="sxs-lookup"><span data-stu-id="78212-193">Select **Blank Solution** , and name the solution *WebAPIMigration* .</span></span> <span data-ttu-id="78212-194">按一下 [確定] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="78212-194">Click the **OK** button.</span></span>
* <span data-ttu-id="78212-195">將現有的 *ProductsApp* 專案加入至方案。</span><span class="sxs-lookup"><span data-stu-id="78212-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="78212-196">將新的 **ASP.NET Core Web 應用程式** 專案加入至方案。</span><span class="sxs-lookup"><span data-stu-id="78212-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="78212-197">從下拉式清單中選取 [ **.Net Core** 目標 framework]，然後選取 [ **API** ] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="78212-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="78212-198">將專案命名為 *ProductsCore* ，然後按一下 [ **確定]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="78212-198">Name the project *ProductsCore* , and click the **OK** button.</span></span>

<span data-ttu-id="78212-199">方案現在包含兩個專案。</span><span class="sxs-lookup"><span data-stu-id="78212-199">The solution now contains two projects.</span></span> <span data-ttu-id="78212-200">下列各節說明如何將 *ProductsApp* 專案的內容遷移至 *ProductsCore* 專案。</span><span class="sxs-lookup"><span data-stu-id="78212-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="78212-201">移轉組態</span><span class="sxs-lookup"><span data-stu-id="78212-201">Migrate configuration</span></span>

<span data-ttu-id="78212-202">ASP.NET Core 不會使用：</span><span class="sxs-lookup"><span data-stu-id="78212-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="78212-203">*App_Start* 資料夾或 *global.asax* 檔案</span><span class="sxs-lookup"><span data-stu-id="78212-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="78212-204">*web.config* 檔案會在發行時新增。</span><span class="sxs-lookup"><span data-stu-id="78212-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="78212-205">`Startup` 類別：</span><span class="sxs-lookup"><span data-stu-id="78212-205">The `Startup` class:</span></span>

* <span data-ttu-id="78212-206">取代 *global.asax* 。</span><span class="sxs-lookup"><span data-stu-id="78212-206">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="78212-207">處理所有應用程式啟動工作。</span><span class="sxs-lookup"><span data-stu-id="78212-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="78212-208">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="78212-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="78212-209">在 ASP.NET Core MVC 中，當呼叫時，預設會包含屬性路由 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="78212-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="78212-210">下列 `UseMvc` 呼叫會取代 *ProductsApp* 專案的 *App_Start/webapiconfig.cs* 檔：</span><span class="sxs-lookup"><span data-stu-id="78212-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="78212-211">遷移模型和控制器</span><span class="sxs-lookup"><span data-stu-id="78212-211">Migrate models and controllers</span></span>

<span data-ttu-id="78212-212">下列程式碼顯示 `ProductsController` ASP.NET Core 的更新： [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="78212-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="78212-213">更新 `ProductsController` ASP.NET Core 的：</span><span class="sxs-lookup"><span data-stu-id="78212-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="78212-214">將 *控制器/productscontroller.cs* 從原始專案複製到新專案。</span><span class="sxs-lookup"><span data-stu-id="78212-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="78212-215">從原始專案將 [ *模型* ] 資料夾複製到新專案。</span><span class="sxs-lookup"><span data-stu-id="78212-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="78212-216">將複製的檔案根命名空間變更為 `ProductsCore` 。</span><span class="sxs-lookup"><span data-stu-id="78212-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="78212-217">將 `using ProductsApp.Models;` 語句更新為 `using ProductsCore.Models;` 。</span><span class="sxs-lookup"><span data-stu-id="78212-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="78212-218">下列元件不存在於 ASP.NET Core 中：</span><span class="sxs-lookup"><span data-stu-id="78212-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="78212-219">`ApiController` 類別</span><span class="sxs-lookup"><span data-stu-id="78212-219">`ApiController` class</span></span>
* <span data-ttu-id="78212-220">`System.Web.Http` 命名空間</span><span class="sxs-lookup"><span data-stu-id="78212-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="78212-221">`IHttpActionResult` 介面</span><span class="sxs-lookup"><span data-stu-id="78212-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="78212-222">進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="78212-222">Make the following changes:</span></span>

1. <span data-ttu-id="78212-223">將 `ApiController` 變更為 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。</span><span class="sxs-lookup"><span data-stu-id="78212-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="78212-224">加入 `using Microsoft.AspNetCore.Mvc;` 以解析 `ControllerBase` 參考。</span><span class="sxs-lookup"><span data-stu-id="78212-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="78212-225">刪除 `using System.Web.Http;`。</span><span class="sxs-lookup"><span data-stu-id="78212-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="78212-226">將 `GetProduct` 動作的傳回類型從變更 `IHttpActionResult` 為 `ActionResult<Product>` 。</span><span class="sxs-lookup"><span data-stu-id="78212-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="78212-227">簡化 `GetProduct` 動作的 `return` 語句，如下所示：</span><span class="sxs-lookup"><span data-stu-id="78212-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="78212-228">設定路由</span><span class="sxs-lookup"><span data-stu-id="78212-228">Configure routing</span></span>

<span data-ttu-id="78212-229">設定路由，如下所示：</span><span class="sxs-lookup"><span data-stu-id="78212-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="78212-230">`ProductsController`以下列屬性標示類別：</span><span class="sxs-lookup"><span data-stu-id="78212-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="78212-231">上述屬性會設定 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 控制器的屬性路由模式。</span><span class="sxs-lookup"><span data-stu-id="78212-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="78212-232">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)屬性會讓屬性路由成為此控制器中所有動作的需求。</span><span class="sxs-lookup"><span data-stu-id="78212-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="78212-233">屬性路由支援權杖，例如 `[controller]` 和 `[action]` 。</span><span class="sxs-lookup"><span data-stu-id="78212-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="78212-234">在執行時間，每個權杖都會分別取代為已套用屬性的控制器或動作名稱。</span><span class="sxs-lookup"><span data-stu-id="78212-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="78212-235">這些標記會減少專案中魔術字串的數目。</span><span class="sxs-lookup"><span data-stu-id="78212-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="78212-236">標記也可確保在套用自動重新命名重構時，路由會與對應的控制器和動作保持同步。</span><span class="sxs-lookup"><span data-stu-id="78212-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="78212-237">將專案的相容性模式設定為 ASP.NET Core 2.2：</span><span class="sxs-lookup"><span data-stu-id="78212-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="78212-238">上述變更：</span><span class="sxs-lookup"><span data-stu-id="78212-238">The preceding change:</span></span>

    * <span data-ttu-id="78212-239">必須使用 `[ApiController]` 控制器層級的屬性。</span><span class="sxs-lookup"><span data-stu-id="78212-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="78212-240">加入宣告 ASP.NET Core 2.2 中引進的潛在中斷行為。</span><span class="sxs-lookup"><span data-stu-id="78212-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="78212-241">啟用動作的 HTTP Get 要求 `ProductController` ：</span><span class="sxs-lookup"><span data-stu-id="78212-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="78212-242">將 [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 屬性套用至 `GetAllProducts` 動作。</span><span class="sxs-lookup"><span data-stu-id="78212-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="78212-243">將 `[HttpGet("{id}")]` 屬性套用至 `GetProduct` 動作。</span><span class="sxs-lookup"><span data-stu-id="78212-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="78212-244">執行已遷移的專案，然後流覽至 `/api/products` 。</span><span class="sxs-lookup"><span data-stu-id="78212-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="78212-245">三個產品的完整清單隨即出現。</span><span class="sxs-lookup"><span data-stu-id="78212-245">A full list of three products appears.</span></span> <span data-ttu-id="78212-246">瀏覽至 `/api/products/1`。</span><span class="sxs-lookup"><span data-stu-id="78212-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="78212-247">第一個產品隨即出現。</span><span class="sxs-lookup"><span data-stu-id="78212-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="78212-248">相容性填充碼</span><span class="sxs-lookup"><span data-stu-id="78212-248">Compatibility shim</span></span>

<span data-ttu-id="78212-249">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)程式庫提供相容性填充碼，可將 ASP.NET 4.X Web API 專案移至 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="78212-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="78212-250">相容性填充碼會擴充 ASP.NET Core，以支援 ASP.NET 4.x Web API 2 中的一些慣例。</span><span class="sxs-lookup"><span data-stu-id="78212-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="78212-251">本檔先前移植的範例已足夠基本，因此不需要相容性填充碼。</span><span class="sxs-lookup"><span data-stu-id="78212-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="78212-252">針對較大型的專案，使用相容性填充碼有助於暫時橋接 ASP.NET Core 與 ASP.NET 4.x Web API 2 之間的 API 間距。</span><span class="sxs-lookup"><span data-stu-id="78212-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="78212-253">Web API 相容性填充碼旨在作為暫時的量值，以支援將大型 ASP.NET 4.x Web API 專案遷移至 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="78212-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="78212-254">經過一段時間，專案應該更新為使用 ASP.NET Core 模式，而不是依賴相容性填充碼。</span><span class="sxs-lookup"><span data-stu-id="78212-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="78212-255">包含的相容性功能 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 包括：</span><span class="sxs-lookup"><span data-stu-id="78212-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="78212-256">加入 `ApiController` 類型，因此不需要更新控制器的基底類型。</span><span class="sxs-lookup"><span data-stu-id="78212-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="78212-257">啟用 Web API 樣式的模型系結。</span><span class="sxs-lookup"><span data-stu-id="78212-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="78212-258">根據預設，ASP.NET Core MVC 模型系結函式與 ASP.NET 4.x MVC 5 的功能相似。</span><span class="sxs-lookup"><span data-stu-id="78212-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="78212-259">相容性填充碼會將模型系結變更為更類似 ASP.NET 4.x Web API 2 模型系結慣例。</span><span class="sxs-lookup"><span data-stu-id="78212-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="78212-260">例如，複雜型別會自動從要求主體系結。</span><span class="sxs-lookup"><span data-stu-id="78212-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="78212-261">擴充模型系結，讓控制器動作可以採用型別的參數 `HttpRequestMessage` 。</span><span class="sxs-lookup"><span data-stu-id="78212-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="78212-262">加入訊息格式器，允許動作傳回型別的結果 `HttpResponseMessage` 。</span><span class="sxs-lookup"><span data-stu-id="78212-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="78212-263">新增 Web API 2 動作可能用來提供回應的額外回應方法：</span><span class="sxs-lookup"><span data-stu-id="78212-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="78212-264">`HttpResponseMessage` 發電機：</span><span class="sxs-lookup"><span data-stu-id="78212-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="78212-265">動作結果方法：</span><span class="sxs-lookup"><span data-stu-id="78212-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="78212-266">將的實例加入 `IContentNegotiator` 至應用程式的相依性插入 (DI) 容器，並從 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)提供與內容協商相關的類型。</span><span class="sxs-lookup"><span data-stu-id="78212-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="78212-267">這類類型的範例包括 `DefaultContentNegotiator` 和 `MediaTypeFormatter` 。</span><span class="sxs-lookup"><span data-stu-id="78212-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="78212-268">使用相容性填充碼：</span><span class="sxs-lookup"><span data-stu-id="78212-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="78212-269">安裝 [AspNetCore >webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="78212-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="78212-270">藉由呼叫，向應用程式的 DI 容器註冊相容性填充碼的服務 `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="78212-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="78212-271">使用 `MapWebApiRoute` `IRouteBuilder` 應用程式呼叫中的來定義 web API 特定路由 `IApplicationBuilder.UseMvc` 。</span><span class="sxs-lookup"><span data-stu-id="78212-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78212-272">其他資源</span><span class="sxs-lookup"><span data-stu-id="78212-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
