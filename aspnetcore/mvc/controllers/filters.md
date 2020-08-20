---
title: ASP.NET Core 中的篩選條件
author: Rick-Anderson
description: 了解篩選條件的運作方式，以及如何在 ASP.NET Core 中使用它們。
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: 7134344abb5bc724aceb9a2adb117b3749435f55
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634848"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="98643-103">ASP.NET Core 中的篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="98643-104">作者：[Kirk Larkin](https://github.com/serpent5) \(英文\)、[Rick Anderson](https://twitter.com/RickAndMSFT) \(英文\)、[Tom Dykstra](https://github.com/tdykstra/) \(英文\) 及 [Steve Smith](https://ardalis.com/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="98643-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="98643-105">ASP.NET Core 中的「篩選條件」\*\* 可讓程式碼在要求處理管線中的特定階段之前或之後執行。</span><span class="sxs-lookup"><span data-stu-id="98643-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="98643-106">內建篩選條件會處理工作，例如：</span><span class="sxs-lookup"><span data-stu-id="98643-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="98643-107">授權 (避免存取使用者未獲授權的資源)。</span><span class="sxs-lookup"><span data-stu-id="98643-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="98643-108">回應快取 (縮短要求管線，傳回快取的回應)。</span><span class="sxs-lookup"><span data-stu-id="98643-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="98643-109">可以建立自訂篩選條件來處理跨領域關注。</span><span class="sxs-lookup"><span data-stu-id="98643-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="98643-110">跨領域關注的範例包括錯誤處理、快取、設定、授權及記錄。</span><span class="sxs-lookup"><span data-stu-id="98643-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="98643-111">篩選能避免重複的程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="98643-112">例如，錯誤處理例外狀況篩選條件中可以合併錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="98643-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="98643-113">本檔適用于 Razor 具有 views 的頁面、API 控制器和控制器。</span><span class="sxs-lookup"><span data-stu-id="98643-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="98643-114">篩選器無法直接使用[ Razor 元件](xref:blazor/components/index)。</span><span class="sxs-lookup"><span data-stu-id="98643-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="98643-115">篩選準則只會在下列情況時間接影響元件：</span><span class="sxs-lookup"><span data-stu-id="98643-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="98643-116">元件內嵌于頁面或視圖中。</span><span class="sxs-lookup"><span data-stu-id="98643-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="98643-117">頁面或控制器/視圖會使用篩選。</span><span class="sxs-lookup"><span data-stu-id="98643-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="98643-118">[檢視或下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) \(英文\) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98643-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="98643-119">篩選條件如何運作</span><span class="sxs-lookup"><span data-stu-id="98643-119">How filters work</span></span>

<span data-ttu-id="98643-120">篩選條件會在「ASP.NET Core 動作引動過程管線」\*\* 中執行，其有時也被稱為「篩選條件管線」\*\*。</span><span class="sxs-lookup"><span data-stu-id="98643-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="98643-121">在 ASP.NET Core 之後執行的篩選條件管線會選取要執行的動作。</span><span class="sxs-lookup"><span data-stu-id="98643-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求會透過其他中介軟體、路由中介軟體、動作選取和動作調用管線來處理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="98643-124">篩選條件類型</span><span class="sxs-lookup"><span data-stu-id="98643-124">Filter types</span></span>

<span data-ttu-id="98643-125">每個篩選類型會在篩選條件管線中的不同階段執行：</span><span class="sxs-lookup"><span data-stu-id="98643-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="98643-126">[授權篩選條件](#authorization-filters)會先執行，用來判斷使用者是否已針對要求取得授權。</span><span class="sxs-lookup"><span data-stu-id="98643-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="98643-127">如果要求未獲授權，授權篩選器會對管線進行較短的線路。</span><span class="sxs-lookup"><span data-stu-id="98643-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="98643-128">[資源篩選](#resource-filters)條件：</span><span class="sxs-lookup"><span data-stu-id="98643-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="98643-129">會在授權之後執行。</span><span class="sxs-lookup"><span data-stu-id="98643-129">Run after authorization.</span></span>  
  * <span data-ttu-id="98643-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 在篩選準則管線的其餘部分之前執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="98643-131">例如， `OnResourceExecuting` 在模型系結之前執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="98643-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 在管線的其餘部分完成之後執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="98643-133">[動作篩選](#action-filters)條件：</span><span class="sxs-lookup"><span data-stu-id="98643-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="98643-134">在呼叫動作方法之前和之後立即執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="98643-135">可以變更傳遞至動作的引數。</span><span class="sxs-lookup"><span data-stu-id="98643-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="98643-136">可以變更動作傳回的結果。</span><span class="sxs-lookup"><span data-stu-id="98643-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="98643-137">頁面中 **不** 支援 Razor 。</span><span class="sxs-lookup"><span data-stu-id="98643-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="98643-138">[例外狀況篩選準則](#exception-filters) 會將全域原則套用至回應主體寫入之前發生的未處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="98643-139">[結果篩選準則](#result-filters) 會在執行動作結果之前和之後立即執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="98643-140">它們只有在動作方法執行成功時才執行。</span><span class="sxs-lookup"><span data-stu-id="98643-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="98643-141">它們適用於必須包圍檢視或格式器執行的邏輯。</span><span class="sxs-lookup"><span data-stu-id="98643-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="98643-142">下圖顯示篩選條件類型如何在篩選條件管線中互動。</span><span class="sxs-lookup"><span data-stu-id="98643-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求處理會歷經授權篩選條件、資源篩選條件、模型繫結、動作篩選條件、動作執行和動作結果轉換、例外狀況篩選條件、結果篩選條件，以及結果執行。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="98643-145">實作</span><span class="sxs-lookup"><span data-stu-id="98643-145">Implementation</span></span>

<span data-ttu-id="98643-146">篩選條件同時支援透過不同介面定義的同步和非同步實作。</span><span class="sxs-lookup"><span data-stu-id="98643-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="98643-147">同步篩選準則會在其管線階段之前和之後執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="98643-148">例如，系統會在呼叫動作方法之前呼叫 <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>。</span><span class="sxs-lookup"><span data-stu-id="98643-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="98643-149">系統會在傳回動作方法之後呼叫 <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>。</span><span class="sxs-lookup"><span data-stu-id="98643-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="98643-150">在上述程式碼中， [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) 是 [範例下載](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs)中的公用程式函數。</span><span class="sxs-lookup"><span data-stu-id="98643-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="98643-151">非同步篩選會定義 `On-Stage-ExecutionAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="98643-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="98643-152">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>：</span><span class="sxs-lookup"><span data-stu-id="98643-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="98643-153">在上述程式碼中， `SampleAsyncActionFilter` 具有 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> `next` 執行動作方法的 () 。</span><span class="sxs-lookup"><span data-stu-id="98643-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="98643-154">多個篩選條件階段</span><span class="sxs-lookup"><span data-stu-id="98643-154">Multiple filter stages</span></span>

<span data-ttu-id="98643-155">可以在單一類別中實作多個篩選條件階段的介面。</span><span class="sxs-lookup"><span data-stu-id="98643-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="98643-156">例如，類別會實作為 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> ：</span><span class="sxs-lookup"><span data-stu-id="98643-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="98643-157">同步： <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 和  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="98643-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="98643-158">非同步： <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="98643-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="98643-159">請實作同步**或**非同步版本的篩選條件介面，而**不要**同時實作這兩者。</span><span class="sxs-lookup"><span data-stu-id="98643-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="98643-160">執行階段會先檢查以查看篩選條件是否會實作非同步介面，如果是，便會呼叫該介面。</span><span class="sxs-lookup"><span data-stu-id="98643-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="98643-161">如果沒有，它會呼叫同步介面的方法。</span><span class="sxs-lookup"><span data-stu-id="98643-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="98643-162">如果同時在單一類別中實作非同步和同步介面，系統只會呼叫非同步方法。</span><span class="sxs-lookup"><span data-stu-id="98643-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="98643-163">使用類似的抽象類別時 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> ，只會覆寫同步方法或每個篩選準則類型的非同步方法。</span><span class="sxs-lookup"><span data-stu-id="98643-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="98643-164">內建篩選條件屬性</span><span class="sxs-lookup"><span data-stu-id="98643-164">Built-in filter attributes</span></span>

<span data-ttu-id="98643-165">ASP.NET Core 包含內建的屬性型篩選條件，可對其進行子類別化和自訂。</span><span class="sxs-lookup"><span data-stu-id="98643-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="98643-166">例如，下列結果篩選條件會將標頭新增至回應：</span><span class="sxs-lookup"><span data-stu-id="98643-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="98643-167">屬性可讓篩選條件接受引數，如上述範例所示。</span><span class="sxs-lookup"><span data-stu-id="98643-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="98643-168">將 `AddHeaderAttribute` 新增至控制器或動作方法，並指定 HTTP 標頭的名稱和值：</span><span class="sxs-lookup"><span data-stu-id="98643-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="98643-169">使用 [瀏覽器開發人員工具](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) 之類的工具來檢查標頭。</span><span class="sxs-lookup"><span data-stu-id="98643-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="98643-170">在 [ **回應標頭**] 底下 `author: Rick Anderson` 顯示。</span><span class="sxs-lookup"><span data-stu-id="98643-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="98643-171">下列程式碼會實行 `ActionFilterAttribute` ：</span><span class="sxs-lookup"><span data-stu-id="98643-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="98643-172">從設定系統讀取標題和名稱。</span><span class="sxs-lookup"><span data-stu-id="98643-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="98643-173">不同于先前的範例，下列程式碼不需要將篩選參數新增至程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="98643-174">將標題和名稱新增至回應標頭。</span><span class="sxs-lookup"><span data-stu-id="98643-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="98643-175">設定選項是使用[選項模式](xref:fundamentals/configuration/options)從設定[系統](xref:fundamentals/configuration/index)提供。</span><span class="sxs-lookup"><span data-stu-id="98643-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="98643-176">例如，從檔案 *appsettings.js* ：</span><span class="sxs-lookup"><span data-stu-id="98643-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="98643-177">在 `StartUp.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="98643-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="98643-178">`PositionOptions`類別會新增至具有設定區域的服務容器 `"Position"` 。</span><span class="sxs-lookup"><span data-stu-id="98643-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="98643-179">`MyActionFilterAttribute`會加入至服務容器。</span><span class="sxs-lookup"><span data-stu-id="98643-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="98643-180">下列程式碼顯示 `PositionOptions` 類別：</span><span class="sxs-lookup"><span data-stu-id="98643-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="98643-181">下列程式碼會將套用 `MyActionFilterAttribute` 至 `Index2` 方法：</span><span class="sxs-lookup"><span data-stu-id="98643-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="98643-182">**Response Headers** `author: Rick Anderson` `Editor: Joe Smith` 當呼叫端點時，會顯示回應標頭、和 `Sample/Index2` 。</span><span class="sxs-lookup"><span data-stu-id="98643-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="98643-183">下列程式碼會將 `MyActionFilterAttribute` 和加入 `AddHeaderAttribute` 至 Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="98643-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="98643-184">篩選無法套用至 Razor 頁面處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="98643-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="98643-185">它們可以套用至 Razor 頁面模型或全域套用。</span><span class="sxs-lookup"><span data-stu-id="98643-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="98643-186">有幾個篩選條件介面有對應的屬性，可用來作為自訂實作的基底類別。</span><span class="sxs-lookup"><span data-stu-id="98643-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="98643-187">篩選條件屬性：</span><span class="sxs-lookup"><span data-stu-id="98643-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="98643-188">篩選條件範圍和執行的順序</span><span class="sxs-lookup"><span data-stu-id="98643-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="98643-189">篩選條件能以三個「範圍」\*\* 之一新增至管線：</span><span class="sxs-lookup"><span data-stu-id="98643-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="98643-190">在控制器動作上使用屬性。</span><span class="sxs-lookup"><span data-stu-id="98643-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="98643-191">篩選屬性無法套用至 Razor 頁面處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="98643-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="98643-192">使用控制器或頁面上的屬性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="98643-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="98643-193">全域適用于所有控制器、動作和 Razor 頁面，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="98643-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="98643-194">預設執行順序</span><span class="sxs-lookup"><span data-stu-id="98643-194">Default order of execution</span></span>

<span data-ttu-id="98643-195">當管線的特定階段有多個篩選條件時，範圍會決定篩選條件的預設執行順序。</span><span class="sxs-lookup"><span data-stu-id="98643-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="98643-196">全域篩選條件會圍繞類別篩選條件，後者又圍繞方法篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="98643-197">因為篩選條件巢狀結構的原因，篩選條件的「之後」\*\* 程式碼的執行順序會與「之前」\*\* 程式碼相反。</span><span class="sxs-lookup"><span data-stu-id="98643-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="98643-198">篩選條件序列：</span><span class="sxs-lookup"><span data-stu-id="98643-198">The filter sequence:</span></span>

* <span data-ttu-id="98643-199">全域篩選條件的「之前」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="98643-200">控制器和頁面篩選器的先前 *程式碼* Razor 。</span><span class="sxs-lookup"><span data-stu-id="98643-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="98643-201">動作方法篩選條件的「之前」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="98643-202">動作方法篩選條件的「之後」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="98643-203">控制器和頁面篩選器的 *之後* 程式碼 Razor 。</span><span class="sxs-lookup"><span data-stu-id="98643-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="98643-204">全域篩選條件的「之後」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="98643-205">下列範例說明針對同步動作篩選條件呼叫篩選條件方法的順序。</span><span class="sxs-lookup"><span data-stu-id="98643-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="98643-206">順序</span><span class="sxs-lookup"><span data-stu-id="98643-206">Sequence</span></span> | <span data-ttu-id="98643-207">篩選條件範圍</span><span class="sxs-lookup"><span data-stu-id="98643-207">Filter scope</span></span> | <span data-ttu-id="98643-208">篩選條件方法</span><span class="sxs-lookup"><span data-stu-id="98643-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="98643-209">1</span><span class="sxs-lookup"><span data-stu-id="98643-209">1</span></span> | <span data-ttu-id="98643-210">全球</span><span class="sxs-lookup"><span data-stu-id="98643-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98643-211">2</span><span class="sxs-lookup"><span data-stu-id="98643-211">2</span></span> | <span data-ttu-id="98643-212">控制器或 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="98643-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="98643-213">3</span><span class="sxs-lookup"><span data-stu-id="98643-213">3</span></span> | <span data-ttu-id="98643-214">方法</span><span class="sxs-lookup"><span data-stu-id="98643-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98643-215">4</span><span class="sxs-lookup"><span data-stu-id="98643-215">4</span></span> | <span data-ttu-id="98643-216">方法</span><span class="sxs-lookup"><span data-stu-id="98643-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="98643-217">5</span><span class="sxs-lookup"><span data-stu-id="98643-217">5</span></span> | <span data-ttu-id="98643-218">控制器或 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="98643-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="98643-219">6</span><span class="sxs-lookup"><span data-stu-id="98643-219">6</span></span> | <span data-ttu-id="98643-220">全球</span><span class="sxs-lookup"><span data-stu-id="98643-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="98643-221">控制器層級篩選</span><span class="sxs-lookup"><span data-stu-id="98643-221">Controller level filters</span></span>

<span data-ttu-id="98643-222">繼承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基底類別的每個控制器都會包含 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)，以及 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="98643-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="98643-223">這些方法會：</span><span class="sxs-lookup"><span data-stu-id="98643-223">These methods:</span></span>

* <span data-ttu-id="98643-224">包裝針對指定動作執行的篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="98643-225">系統會在呼叫任何動作的篩選條件之前呼叫 `OnActionExecuting`。</span><span class="sxs-lookup"><span data-stu-id="98643-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="98643-226">系統會在呼叫所有動作篩選條件之後呼叫 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="98643-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="98643-227">系統會在呼叫任何動作的篩選條件之前呼叫 `OnActionExecutionAsync`。</span><span class="sxs-lookup"><span data-stu-id="98643-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="98643-228">在動作方法之後執行 `next` 後，位於篩選條件中的程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="98643-229">例如，在下載範例中，`MySampleActionFilter` 會在啟動時全域套用。</span><span class="sxs-lookup"><span data-stu-id="98643-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="98643-230">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="98643-230">The `TestController`:</span></span>

* <span data-ttu-id="98643-231">將 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 套用至 `FilterTest2` 動作。</span><span class="sxs-lookup"><span data-stu-id="98643-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="98643-232">覆寫 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="98643-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="98643-233">瀏覽至 `https://localhost:5001/Test2/FilterTest2` 執行下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="98643-233">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="98643-234">控制器層級篩選器會將 [ [順序](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) ] 屬性設定為 `int.MinValue` 。</span><span class="sxs-lookup"><span data-stu-id="98643-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="98643-235">在套用至方法的篩選之後，控制器層級篩選 **無法** 設定為執行。</span><span class="sxs-lookup"><span data-stu-id="98643-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="98643-236">下一節將說明順序。</span><span class="sxs-lookup"><span data-stu-id="98643-236">Order is explained in the next section.</span></span>

<span data-ttu-id="98643-237">若為 Razor 頁面，請參閱藉 [由覆 Razor 寫篩選方法來執行頁面篩選](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="98643-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="98643-238">覆寫預設順序</span><span class="sxs-lookup"><span data-stu-id="98643-238">Overriding the default order</span></span>

<span data-ttu-id="98643-239">可以藉由實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 來覆寫預設執行序列。</span><span class="sxs-lookup"><span data-stu-id="98643-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="98643-240">`IOrderedFilter` 會公開 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 屬性，其優先順序會高於範圍以決定執行順序。</span><span class="sxs-lookup"><span data-stu-id="98643-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="98643-241">具有較低 `Order` 值的篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="98643-242">在具有較高 `Order` 值的篩選條件之前執行「之前」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="98643-243">在具有較高 `Order` 值的篩選條件之後執行「之後」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="98643-244">使用函式 `Order` 參數設定屬性：</span><span class="sxs-lookup"><span data-stu-id="98643-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="98643-245">請考慮下列控制器中的兩個動作篩選準則：</span><span class="sxs-lookup"><span data-stu-id="98643-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="98643-246">全域篩選準則會加入至 `StartUp.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="98643-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="98643-247">3個篩選準則會以下列循序執行：</span><span class="sxs-lookup"><span data-stu-id="98643-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="98643-248">`Order` 屬性在決定篩選條件執行的順序時會覆寫範圍。</span><span class="sxs-lookup"><span data-stu-id="98643-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="98643-249">篩選條件會先依照順序排序，然後使用範圍來打破僵局。</span><span class="sxs-lookup"><span data-stu-id="98643-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="98643-250">所有內建的篩選條件都會實作 `IOrderedFilter` 並將預設 `Order` 值設為 0。</span><span class="sxs-lookup"><span data-stu-id="98643-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="98643-251">如先前所述，如果設定為非零值，控制器層級篩選器會將 [ [順序](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) ] 屬性設定 `int.MinValue` 為 [內建篩選準則]，範圍會決定順序 `Order` 。</span><span class="sxs-lookup"><span data-stu-id="98643-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="98643-252">在上述程式碼中， `MySampleActionFilter` 有全域範圍，因此它會在 `MyAction2FilterAttribute` 具有控制器範圍的之前執行。</span><span class="sxs-lookup"><span data-stu-id="98643-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="98643-253">若要 `MyAction2FilterAttribute` 先執行，請將順序設定為 `int.MinValue` ：</span><span class="sxs-lookup"><span data-stu-id="98643-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="98643-254">若要 `MySampleActionFilter` 先執行全域篩選，請將設定 `Order` 為 `int.MinValue` ：</span><span class="sxs-lookup"><span data-stu-id="98643-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="98643-255">取消和縮短</span><span class="sxs-lookup"><span data-stu-id="98643-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="98643-256">可以設定提供給篩選條件方法之 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 參數上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 屬性，以縮短篩選條件管線。</span><span class="sxs-lookup"><span data-stu-id="98643-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="98643-257">比方說，下列資源篩選條件可防止管線的其餘部分執行：</span><span class="sxs-lookup"><span data-stu-id="98643-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="98643-258">在下列程式碼中，`ShortCircuitingResourceFilter` 和 `AddHeader` 篩選條件都以 `SomeResource` 動作方法為目標。</span><span class="sxs-lookup"><span data-stu-id="98643-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="98643-259">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="98643-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="98643-260">最先執行，因為它是資源篩選條件，且 `AddHeader` 是動作篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="98643-261">縮短管線的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="98643-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="98643-262">因此，`SomeResource` 動作的 `AddHeader` 篩選條件永遠不會執行。</span><span class="sxs-lookup"><span data-stu-id="98643-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="98643-263">如果這兩個篩選條件都套用在動作方法層級，此行為也會相同，假設 `ShortCircuitingResourceFilter` 先執行的話。</span><span class="sxs-lookup"><span data-stu-id="98643-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="98643-264">`ShortCircuitingResourceFilter` 會因為其篩選器類型而先執行，或藉由明確使用 `Order` 屬性而先執行。</span><span class="sxs-lookup"><span data-stu-id="98643-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="98643-265">相依性插入</span><span class="sxs-lookup"><span data-stu-id="98643-265">Dependency injection</span></span>

<span data-ttu-id="98643-266">可以依類型或執行個體新增篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="98643-267">如果新增執行個體，該執行個體將用於每個要求。</span><span class="sxs-lookup"><span data-stu-id="98643-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="98643-268">如果新增類型，其將會由類型啟動。</span><span class="sxs-lookup"><span data-stu-id="98643-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="98643-269">由類型啟動的篩選條件表示：</span><span class="sxs-lookup"><span data-stu-id="98643-269">A type-activated filter means:</span></span>

* <span data-ttu-id="98643-270">系統會針對每個要求建立執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-270">An instance is created for each request.</span></span>
* <span data-ttu-id="98643-271">任何建構函式相依性都會由[相依性插入](xref:fundamentals/dependency-injection) (DI) 所填入。</span><span class="sxs-lookup"><span data-stu-id="98643-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="98643-272">實作為屬性並直接新增至控制器類別或動作方法的篩選條件，不能由[相依性插入](xref:fundamentals/dependency-injection) (DI) 提供建構函式相依性。</span><span class="sxs-lookup"><span data-stu-id="98643-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="98643-273">DI 無法提供建構函式相依性，因為：</span><span class="sxs-lookup"><span data-stu-id="98643-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="98643-274">必須在提供屬性之建構函式參數的地方提供它們。</span><span class="sxs-lookup"><span data-stu-id="98643-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="98643-275">這是屬性運作方式的限制。</span><span class="sxs-lookup"><span data-stu-id="98643-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="98643-276">下列篩選條件支援由 DI 所提供的建構函式相依性：</span><span class="sxs-lookup"><span data-stu-id="98643-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="98643-277">在屬性上實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="98643-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="98643-278">上述篩選條件可以被套用至控制器或動作方法：</span><span class="sxs-lookup"><span data-stu-id="98643-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="98643-279">DI 會提供記錄器。</span><span class="sxs-lookup"><span data-stu-id="98643-279">Loggers are available from DI.</span></span> <span data-ttu-id="98643-280">不過，請避免僅針對記錄目的建立並使用篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="98643-281">[內建架構記錄](xref:fundamentals/logging/index)通常便可以提供記錄所需的項目。</span><span class="sxs-lookup"><span data-stu-id="98643-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="98643-282">新增至篩選條件的記錄：</span><span class="sxs-lookup"><span data-stu-id="98643-282">Logging added to filters:</span></span>

* <span data-ttu-id="98643-283">應該專注在篩選條件特定的商務領域考量或行為。</span><span class="sxs-lookup"><span data-stu-id="98643-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="98643-284">**不**應該記錄動作或其他架構事件。</span><span class="sxs-lookup"><span data-stu-id="98643-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="98643-285">內建的篩選準則會記錄動作和架構事件。</span><span class="sxs-lookup"><span data-stu-id="98643-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="98643-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="98643-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="98643-287">服務篩選條件實作類型會註冊在 `ConfigureServices` 中。</span><span class="sxs-lookup"><span data-stu-id="98643-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="98643-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 會從 DI 擷取篩選條件的執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="98643-289">下面程式碼會說明 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="98643-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="98643-290">在下列程式碼中，會將 `AddHeaderResultServiceFilter` 新增至 DI 容器：</span><span class="sxs-lookup"><span data-stu-id="98643-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="98643-291">在下列程式碼中，`ServiceFilter` 屬性會從 DI 擷取 `AddHeaderResultServiceFilter` 篩選條件的執行個體：</span><span class="sxs-lookup"><span data-stu-id="98643-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="98643-292">使用 `ServiceFilterAttribute` 時，設定 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)：</span><span class="sxs-lookup"><span data-stu-id="98643-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="98643-293">能提供篩選條件執行個體「可能」\*\* 可以在其建立的要求範圍之外重複使用的提示。</span><span class="sxs-lookup"><span data-stu-id="98643-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="98643-294">ASP.NET Core 執行階段並不保證：</span><span class="sxs-lookup"><span data-stu-id="98643-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="98643-295">將會建立篩選條件的單一執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="98643-296">將不會於稍後的時間從 DI 容器重新要求篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="98643-297">不應該搭配仰賴具有非單一資料庫存留期之服務的篩選條件使用。</span><span class="sxs-lookup"><span data-stu-id="98643-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="98643-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 會實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="98643-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="98643-299">`IFilterFactory` 會公開 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法來建立 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="98643-300">`CreateInstance` 會從 DI 載入指定的類型。</span><span class="sxs-lookup"><span data-stu-id="98643-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="98643-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="98643-301">TypeFilterAttribute</span></span>

<span data-ttu-id="98643-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 類似於 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>，但其類型不會直接從 DI 容器解析。</span><span class="sxs-lookup"><span data-stu-id="98643-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="98643-303">它會使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 來具現化類型。</span><span class="sxs-lookup"><span data-stu-id="98643-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="98643-304">由於 `TypeFilterAttribute` 類型不會直接從 DI 容器解析：</span><span class="sxs-lookup"><span data-stu-id="98643-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="98643-305">使用 `TypeFilterAttribute` 參考的類型不需要向 DI 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="98643-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="98643-306">不過它們的相依性會由 DI 容器滿足。</span><span class="sxs-lookup"><span data-stu-id="98643-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="98643-307">`TypeFilterAttribute` 可以選擇性地接受類型的建構函式引數。</span><span class="sxs-lookup"><span data-stu-id="98643-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="98643-308">使用 `TypeFilterAttribute` 時，設定 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)：</span><span class="sxs-lookup"><span data-stu-id="98643-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="98643-309">能提供篩選條件執行個體「可能」\*\* 可以在其建立的要求範圍之外重複使用的提示。</span><span class="sxs-lookup"><span data-stu-id="98643-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="98643-310">ASP.NET Core 執行階段不保證將建立篩選條件的單一執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="98643-311">不應該搭配仰賴具有非單一資料庫存留期之服務的篩選條件使用。</span><span class="sxs-lookup"><span data-stu-id="98643-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="98643-312">下列範例示範如何使用 `TypeFilterAttribute` 將引數傳遞至類型：</span><span class="sxs-lookup"><span data-stu-id="98643-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="98643-313">授權篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-313">Authorization filters</span></span>

<span data-ttu-id="98643-314">授權篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-314">Authorization filters:</span></span>

* <span data-ttu-id="98643-315">是篩選條件管線內最先執行的篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="98643-316">控制動作方法的存取。</span><span class="sxs-lookup"><span data-stu-id="98643-316">Control access to action methods.</span></span>
* <span data-ttu-id="98643-317">有之前的方法，但沒有之後的方法。</span><span class="sxs-lookup"><span data-stu-id="98643-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="98643-318">自訂授權篩選條件需要自訂的授權架構。</span><span class="sxs-lookup"><span data-stu-id="98643-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="98643-319">最好是設定授權原則或撰寫自訂授權原則，而不要撰寫自訂篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="98643-320">內建的授權篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="98643-321">呼叫授權系統。</span><span class="sxs-lookup"><span data-stu-id="98643-321">Calls the authorization system.</span></span>
* <span data-ttu-id="98643-322">不會授權要求。</span><span class="sxs-lookup"><span data-stu-id="98643-322">Does not authorize requests.</span></span>

<span data-ttu-id="98643-323">**不會**在授權篩選條件內擲回例外狀況：</span><span class="sxs-lookup"><span data-stu-id="98643-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="98643-324">該例外狀況將不會被處理。</span><span class="sxs-lookup"><span data-stu-id="98643-324">The exception will not be handled.</span></span>
* <span data-ttu-id="98643-325">例外狀況篩選條件將不會處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="98643-326">請考慮在例外狀況於授權篩選條件中發生時發出挑戰。</span><span class="sxs-lookup"><span data-stu-id="98643-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="98643-327">深入了解[授權](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="98643-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="98643-328">資源篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-328">Resource filters</span></span>

<span data-ttu-id="98643-329">資源篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-329">Resource filters:</span></span>

* <span data-ttu-id="98643-330">實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 介面。</span><span class="sxs-lookup"><span data-stu-id="98643-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="98643-331">執行會包裝大部分的篩選條件管線。</span><span class="sxs-lookup"><span data-stu-id="98643-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="98643-332">只有[授權篩選條件](#authorization-filters)會在資源篩選條件之前執行。</span><span class="sxs-lookup"><span data-stu-id="98643-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="98643-333">資源篩選條件很適合用來縮短大部分的管線。</span><span class="sxs-lookup"><span data-stu-id="98643-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="98643-334">比方說，快取篩選條件可以避免快取命中上的其餘管線。</span><span class="sxs-lookup"><span data-stu-id="98643-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="98643-335">資源篩選條件範例：</span><span class="sxs-lookup"><span data-stu-id="98643-335">Resource filter examples:</span></span>

* <span data-ttu-id="98643-336">先前所示的[縮短資源篩選條件](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="98643-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="98643-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) \(英文\)：</span><span class="sxs-lookup"><span data-stu-id="98643-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="98643-338">防止模型繫結存取表單資料。</span><span class="sxs-lookup"><span data-stu-id="98643-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="98643-339">用於大型檔案上傳，以避免將表單資料讀入記憶體。</span><span class="sxs-lookup"><span data-stu-id="98643-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="98643-340">動作篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-340">Action filters</span></span>

<span data-ttu-id="98643-341">動作 **篩選不適用於** Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="98643-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="98643-342">Razor 頁面支援 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 。</span><span class="sxs-lookup"><span data-stu-id="98643-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="98643-343">如需詳細資訊，請參閱 [ Razor 頁面的篩選方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="98643-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="98643-344">動作篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-344">Action filters:</span></span>

* <span data-ttu-id="98643-345">實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 介面。</span><span class="sxs-lookup"><span data-stu-id="98643-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="98643-346">它們執行會包圍動作方法的執行。</span><span class="sxs-lookup"><span data-stu-id="98643-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="98643-347">下列程式碼會顯示範例動作篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="98643-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供下列屬性：</span><span class="sxs-lookup"><span data-stu-id="98643-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="98643-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> -啟用讀取動作方法的輸入。</span><span class="sxs-lookup"><span data-stu-id="98643-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="98643-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - 提供對控制器執行個體的管理。</span><span class="sxs-lookup"><span data-stu-id="98643-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="98643-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 設定 `Result` 會縮短動作方法和後續動作篩選條件的執行。</span><span class="sxs-lookup"><span data-stu-id="98643-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="98643-352">在動作方法中擲回例外狀況：</span><span class="sxs-lookup"><span data-stu-id="98643-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="98643-353">防止執行後續篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="98643-354">和設定 `Result` 不同，會被視為失敗而非成功結果。</span><span class="sxs-lookup"><span data-stu-id="98643-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="98643-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 與 `Result`，加上下列屬性：</span><span class="sxs-lookup"><span data-stu-id="98643-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="98643-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果動作執行被另一個篩選條件縮短，則為 true。</span><span class="sxs-lookup"><span data-stu-id="98643-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="98643-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果動作或先前所執行的動作篩選條件擲回例外狀況，則為非 Null。</span><span class="sxs-lookup"><span data-stu-id="98643-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="98643-358">將此屬性設定為 Null：</span><span class="sxs-lookup"><span data-stu-id="98643-358">Setting this property to null:</span></span>

  * <span data-ttu-id="98643-359">實際上會「處理」例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="98643-360">會執行 `Result`，有如它是從動作方法傳回一般。</span><span class="sxs-lookup"><span data-stu-id="98643-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="98643-361">對於 `IAsyncActionFilter`，呼叫 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 會：</span><span class="sxs-lookup"><span data-stu-id="98643-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="98643-362">執行任何後續的動作篩選條件和動作方法。</span><span class="sxs-lookup"><span data-stu-id="98643-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="98643-363">傳回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="98643-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="98643-364">若要縮短，請指派 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 給某個結果執行個體，並且不要呼叫 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="98643-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="98643-365">架構提供抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>，其可被子類別化。</span><span class="sxs-lookup"><span data-stu-id="98643-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="98643-366">`OnActionExecuting` 動作篩選條件可以用來：</span><span class="sxs-lookup"><span data-stu-id="98643-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="98643-367">驗證模型狀態。</span><span class="sxs-lookup"><span data-stu-id="98643-367">Validate model state.</span></span>
* <span data-ttu-id="98643-368">如果狀態無效，則傳回錯誤。</span><span class="sxs-lookup"><span data-stu-id="98643-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="98643-369">以屬性標注的控制器 `[ApiController]` 會自動驗證模型狀態，並傳回400回應。</span><span class="sxs-lookup"><span data-stu-id="98643-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="98643-370">如需詳細資訊，請參閱[自動 HTTP 400 回應](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="98643-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="98643-371">`OnActionExecuted` 方法會在動作方法之後執行：</span><span class="sxs-lookup"><span data-stu-id="98643-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="98643-372">並可以透過 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 屬性查看及操作動作的結果。</span><span class="sxs-lookup"><span data-stu-id="98643-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="98643-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 會在動作執行被另一個篩選條件縮短時被設為 true。</span><span class="sxs-lookup"><span data-stu-id="98643-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="98643-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 會在動作或後續的動作篩選條件擲回例外狀況時被設為非 Null 值。</span><span class="sxs-lookup"><span data-stu-id="98643-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="98643-375">將 `Exception` 設定為 null：</span><span class="sxs-lookup"><span data-stu-id="98643-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="98643-376">實際上會「處理」例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="98643-377">執行 `ActionExecutedContext.Result`，彷彿它已正常地從動作方法傳回。</span><span class="sxs-lookup"><span data-stu-id="98643-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="98643-378">例外狀況篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-378">Exception filters</span></span>

<span data-ttu-id="98643-379">例外狀況篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-379">Exception filters:</span></span>

* <span data-ttu-id="98643-380">實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="98643-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="98643-381">可以用來實作常見的錯誤處理原則。</span><span class="sxs-lookup"><span data-stu-id="98643-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="98643-382">下列範例例外狀況篩選條件會使用自訂的錯誤檢視，以顯示在開發應用程式時發生的例外狀況詳細資料：</span><span class="sxs-lookup"><span data-stu-id="98643-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="98643-383">下列程式碼會測試例外狀況篩選準則：</span><span class="sxs-lookup"><span data-stu-id="98643-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="98643-384">例外狀況篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-384">Exception filters:</span></span>

* <span data-ttu-id="98643-385">沒有之前和之後的事件。</span><span class="sxs-lookup"><span data-stu-id="98643-385">Don't have before and after events.</span></span>
* <span data-ttu-id="98643-386">實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="98643-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="98643-387">處理 Razor 頁面或控制器建立、 [模型](xref:mvc/models/model-binding)系結、動作篩選準則或動作方法中發生的未處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="98643-388">**不會**攔截在資源篩選條件、結果篩選條件或 MVC 結果執行中發生的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="98643-389">若要處理例外狀況，請將 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 屬性設為 `true`，或撰寫回應。</span><span class="sxs-lookup"><span data-stu-id="98643-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="98643-390">這樣會阻止傳播例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-390">This stops propagation of the exception.</span></span> <span data-ttu-id="98643-391">例外狀況篩選條件無法將例外狀況變成「成功」。</span><span class="sxs-lookup"><span data-stu-id="98643-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="98643-392">只有動作篩選條件可以這麼做。</span><span class="sxs-lookup"><span data-stu-id="98643-392">Only an action filter can do that.</span></span>

<span data-ttu-id="98643-393">例外狀況篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-393">Exception filters:</span></span>

* <span data-ttu-id="98643-394">適合用於設陷在動作內發生的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="98643-395">不像錯誤處理中介軟體那麼有彈性。</span><span class="sxs-lookup"><span data-stu-id="98643-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="98643-396">偏好使用中介軟體進行例外狀況處理。</span><span class="sxs-lookup"><span data-stu-id="98643-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="98643-397">只有在錯誤處理會根據所呼叫的動作方法而「不同」\*\* 時才使用例外狀況篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="98643-398">比方說，應用程式可能會有適用於 API 端點及檢視/HTML 的動作方法。</span><span class="sxs-lookup"><span data-stu-id="98643-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="98643-399">API 端點可能將錯誤資訊傳回為 JSON，而以檢視為基礎的動作則可能將錯誤頁面傳回為 HTML。</span><span class="sxs-lookup"><span data-stu-id="98643-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="98643-400">結果篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-400">Result filters</span></span>

<span data-ttu-id="98643-401">結果篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-401">Result filters:</span></span>

* <span data-ttu-id="98643-402">實作介面：</span><span class="sxs-lookup"><span data-stu-id="98643-402">Implement an interface:</span></span>
  * <span data-ttu-id="98643-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="98643-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="98643-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="98643-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="98643-405">它們執行會包圍動作結果的執行。</span><span class="sxs-lookup"><span data-stu-id="98643-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="98643-406">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="98643-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="98643-407">以下程式碼會顯示能新增 HTTP 標頭的結果篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="98643-408">執行的結果類型會取決於動作。</span><span class="sxs-lookup"><span data-stu-id="98643-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="98643-409">傳回視圖的動作會在執行中包含所有的 razor 處理 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 。</span><span class="sxs-lookup"><span data-stu-id="98643-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="98643-410">API 方法可能在結果執行當中執行某種序列化。</span><span class="sxs-lookup"><span data-stu-id="98643-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="98643-411">深入瞭解 [動作結果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="98643-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="98643-412">結果篩選準則只會在動作或動作篩選產生動作結果時執行。</span><span class="sxs-lookup"><span data-stu-id="98643-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="98643-413">當下列情況時，不會執行結果篩選：</span><span class="sxs-lookup"><span data-stu-id="98643-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="98643-414">授權篩選準則或資源篩選器會對管線進行較短的線路。</span><span class="sxs-lookup"><span data-stu-id="98643-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="98643-415">例外狀況篩選條件會產生動作結果來處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="98643-416">藉由將 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 設為 `true`，<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以縮短動作結果和後續結果篩選條件的執行。</span><span class="sxs-lookup"><span data-stu-id="98643-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="98643-417">在縮短時寫入至回應物件，以避免產生空的回應。</span><span class="sxs-lookup"><span data-stu-id="98643-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="98643-418">擲回例外狀況 `IResultFilter.OnResultExecuting` ：</span><span class="sxs-lookup"><span data-stu-id="98643-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="98643-419">防止執行動作結果和後續的篩選準則。</span><span class="sxs-lookup"><span data-stu-id="98643-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="98643-420">會被視為失敗，而不是成功的結果。</span><span class="sxs-lookup"><span data-stu-id="98643-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="98643-421">當 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法執行時，回應可能已經傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="98643-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="98643-422">如果回應已傳送至用戶端，則無法變更。</span><span class="sxs-lookup"><span data-stu-id="98643-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="98643-423">如果動作結果執行被另一個篩選條件縮短，則 `ResultExecutedContext.Canceled` 會被設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="98643-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="98643-424">如果動作結果或後續的結果篩選條件擲回例外狀況，則 `ResultExecutedContext.Exception` 會被設為非 Null 值。</span><span class="sxs-lookup"><span data-stu-id="98643-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="98643-425">將設定 `Exception` 為 null 可有效地處理例外狀況，並防止稍後在管線中重複擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="98643-426">並沒有可以在處理結果篩選條件中的例外狀況時，將資料寫入回應的可靠方式。</span><span class="sxs-lookup"><span data-stu-id="98643-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="98643-427">當動作結果擲回例外狀況時，如果標頭已清除至用戶端，則沒有任何可靠的機制能傳送失敗碼。</span><span class="sxs-lookup"><span data-stu-id="98643-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="98643-428">針對 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> 而言，對 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 的呼叫會執行任何後續的結果篩選條件和動作結果。</span><span class="sxs-lookup"><span data-stu-id="98643-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="98643-429">若要縮短，請將 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 設定為 `true`，並且不要呼叫 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="98643-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="98643-430">架構提供抽象 `ResultFilterAttribute`，其可被子類別化。</span><span class="sxs-lookup"><span data-stu-id="98643-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="98643-431">先前所示的 [AddHeaderAttribute](#add-header-attribute) 類別是結果篩選條件屬性的範例。</span><span class="sxs-lookup"><span data-stu-id="98643-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="98643-432">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="98643-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="98643-433"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 介面會宣告針對所有動作結果執行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 實作。</span><span class="sxs-lookup"><span data-stu-id="98643-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="98643-434">這包括下列所產生的動作結果：</span><span class="sxs-lookup"><span data-stu-id="98643-434">This includes action results produced by:</span></span>

* <span data-ttu-id="98643-435">簡短電路的授權篩選和資源篩選。</span><span class="sxs-lookup"><span data-stu-id="98643-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="98643-436">例外狀況篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-436">Exception filters.</span></span>

<span data-ttu-id="98643-437">例如，下列篩選一律會執行，並會在內容交涉失敗時，為動作結果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) 設定「422 無法處理的實體」\*\* 狀態碼：</span><span class="sxs-lookup"><span data-stu-id="98643-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="98643-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="98643-438">IFilterFactory</span></span>

<span data-ttu-id="98643-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 會實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="98643-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="98643-440">因此，`IFilterFactory` 執行個體可用來在篩選條件管線中任何位置作為 `IFilterMetadata` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="98643-441">當執行階段準備要叫用篩選條件時，它會嘗試將它轉換成 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="98643-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="98643-442">如果該轉換成功，系統會呼叫 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法來建立被叫用的 `IFilterMetadata` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="98643-443">因為在應用程式啟動時不需要明確設定精確的篩選條件管線，所以這提供了具有彈性的設計。</span><span class="sxs-lookup"><span data-stu-id="98643-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="98643-444">可以使用自訂屬性實作作為另一種建立篩選條件的方法，來實作 `IFilterFactory`：</span><span class="sxs-lookup"><span data-stu-id="98643-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="98643-445">篩選準則會套用至下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="98643-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="98643-446">執行 [下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)來測試上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="98643-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="98643-447">叫用 F12 開發人員工具。</span><span class="sxs-lookup"><span data-stu-id="98643-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="98643-448">瀏覽至 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="98643-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="98643-449">F12 開發人員工具會顯示由範例程式碼所加入的下列回應標頭：</span><span class="sxs-lookup"><span data-stu-id="98643-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="98643-450">**作者：**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="98643-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="98643-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="98643-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="98643-452">**內部：**`My header`</span><span class="sxs-lookup"><span data-stu-id="98643-452">**internal:** `My header`</span></span>

<span data-ttu-id="98643-453">上述程式碼會建立 **internal:** `My header` 回應標頭。</span><span class="sxs-lookup"><span data-stu-id="98643-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="98643-454">在屬性上實作的 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="98643-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="98643-455">實作 `IFilterFactory` 的篩選條件很適合用於下列類型的篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="98643-456">不需要傳遞參數。</span><span class="sxs-lookup"><span data-stu-id="98643-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="98643-457">有需要由 DI 滿足的建構函式相依性。</span><span class="sxs-lookup"><span data-stu-id="98643-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="98643-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 會實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="98643-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="98643-459">`IFilterFactory` 會公開 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法來建立 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="98643-460">`CreateInstance` 會從服務容器 (DI) 載入指定的類型。</span><span class="sxs-lookup"><span data-stu-id="98643-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="98643-461">下列程式碼會顯示套用 `[SampleActionFilter]` 的三種方式：</span><span class="sxs-lookup"><span data-stu-id="98643-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="98643-462">在上述程式碼中，使用 `[SampleActionFilter]` 來裝飾方法是套用 `SampleActionFilter` 的建議方法。</span><span class="sxs-lookup"><span data-stu-id="98643-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="98643-463">在篩選條件管線中使用中介軟體</span><span class="sxs-lookup"><span data-stu-id="98643-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="98643-464">資源篩選條件的運作與[中介軟體](xref:fundamentals/middleware/index)相似之處在於，它們會圍繞管線中稍後的所有項目執行。</span><span class="sxs-lookup"><span data-stu-id="98643-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="98643-465">但是，篩選準則與中介軟體不同之處在于它們是執行時間的一部分，這表示它們可以存取內容和結構。</span><span class="sxs-lookup"><span data-stu-id="98643-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="98643-466">若要使用中介軟體作為篩選條件，請建立一個具有 `Configure` 方法的類型，其能指定要插入到篩選條件管線的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="98643-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="98643-467">以下範例會使用當地語系化中介軟體來建立某個要求目前的文化特性：</span><span class="sxs-lookup"><span data-stu-id="98643-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="98643-468">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 來執行中介軟體：</span><span class="sxs-lookup"><span data-stu-id="98643-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="98643-469">中介軟體篩選條件與資源篩選條件在相同的篩選條件管線階段執行：模型繫結之前和管線的其餘部分之後。</span><span class="sxs-lookup"><span data-stu-id="98643-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="98643-470">後續動作</span><span class="sxs-lookup"><span data-stu-id="98643-470">Next actions</span></span>

* <span data-ttu-id="98643-471">請參閱 [ Razor 頁面的篩選方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="98643-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="98643-472">若要嘗試使用篩選準則，請 [下載、測試及修改 GitHub 範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)。</span><span class="sxs-lookup"><span data-stu-id="98643-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="98643-473">作者：[Kirk Larkin](https://github.com/serpent5) \(英文\)、[Rick Anderson](https://twitter.com/RickAndMSFT) \(英文\)、[Tom Dykstra](https://github.com/tdykstra/) \(英文\) 及 [Steve Smith](https://ardalis.com/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="98643-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="98643-474">ASP.NET Core 中的「篩選條件」\*\* 可讓程式碼在要求處理管線中的特定階段之前或之後執行。</span><span class="sxs-lookup"><span data-stu-id="98643-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="98643-475">內建篩選條件會處理工作，例如：</span><span class="sxs-lookup"><span data-stu-id="98643-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="98643-476">授權 (避免存取使用者未獲授權的資源)。</span><span class="sxs-lookup"><span data-stu-id="98643-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="98643-477">回應快取 (縮短要求管線，傳回快取的回應)。</span><span class="sxs-lookup"><span data-stu-id="98643-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="98643-478">可以建立自訂篩選條件來處理跨領域關注。</span><span class="sxs-lookup"><span data-stu-id="98643-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="98643-479">跨領域關注的範例包括錯誤處理、快取、設定、授權及記錄。</span><span class="sxs-lookup"><span data-stu-id="98643-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="98643-480">篩選能避免重複的程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="98643-481">例如，錯誤處理例外狀況篩選條件中可以合併錯誤處理。</span><span class="sxs-lookup"><span data-stu-id="98643-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="98643-482">本檔適用于 Razor 具有 views 的頁面、API 控制器和控制器。</span><span class="sxs-lookup"><span data-stu-id="98643-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="98643-483">[檢視或下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) \(英文\) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98643-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="98643-484">篩選條件如何運作</span><span class="sxs-lookup"><span data-stu-id="98643-484">How filters work</span></span>

<span data-ttu-id="98643-485">篩選條件會在「ASP.NET Core 動作引動過程管線」\*\* 中執行，其有時也被稱為「篩選條件管線」\*\*。</span><span class="sxs-lookup"><span data-stu-id="98643-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="98643-486">在 ASP.NET Core 之後執行的篩選條件管線會選取要執行的動作。</span><span class="sxs-lookup"><span data-stu-id="98643-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求處理會歷經其他中介軟體、路由中介軟體、動作選取和 ASP.NET Core 動作引動過程管線。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="98643-489">篩選條件類型</span><span class="sxs-lookup"><span data-stu-id="98643-489">Filter types</span></span>

<span data-ttu-id="98643-490">每個篩選類型會在篩選條件管線中的不同階段執行：</span><span class="sxs-lookup"><span data-stu-id="98643-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="98643-491">[授權篩選條件](#authorization-filters)會先執行，用來判斷使用者是否已針對要求取得授權。</span><span class="sxs-lookup"><span data-stu-id="98643-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="98643-492">如果要求未經授權，授權篩選條件便會縮短管線。</span><span class="sxs-lookup"><span data-stu-id="98643-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="98643-493">[資源篩選](#resource-filters)條件：</span><span class="sxs-lookup"><span data-stu-id="98643-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="98643-494">會在授權之後執行。</span><span class="sxs-lookup"><span data-stu-id="98643-494">Run after authorization.</span></span>  
  * <span data-ttu-id="98643-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 可以在其餘的篩選條件管線之前執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="98643-496">例如，`OnResourceExecuting` 可以在模型繫結之前執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="98643-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 可以在其餘的管線皆已完成之後執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="98643-498">[動作篩選條件](#action-filters)可以緊接在呼叫個別動作方法之前和之後執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="98643-499">它們可以用來處理傳遞至動作的引數，和從動作傳回的結果。</span><span class="sxs-lookup"><span data-stu-id="98643-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="98643-500">頁面中 **不** 支援動作篩選準則 Razor 。</span><span class="sxs-lookup"><span data-stu-id="98643-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="98643-501">[例外狀況篩選條件](#exception-filters)用來將通用原則套用到在任何項目寫入回應主體之前，發生的未處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="98643-502">[結果篩選條件](#result-filters)可以緊接在執行個別動作結果之前和之後執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="98643-503">它們只有在動作方法執行成功時才執行。</span><span class="sxs-lookup"><span data-stu-id="98643-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="98643-504">它們適用於必須包圍檢視或格式器執行的邏輯。</span><span class="sxs-lookup"><span data-stu-id="98643-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="98643-505">下圖顯示篩選條件類型如何在篩選條件管線中互動。</span><span class="sxs-lookup"><span data-stu-id="98643-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求處理會歷經授權篩選條件、資源篩選條件、模型繫結、動作篩選條件、動作執行和動作結果轉換、例外狀況篩選條件、結果篩選條件，以及結果執行。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="98643-508">實作</span><span class="sxs-lookup"><span data-stu-id="98643-508">Implementation</span></span>

<span data-ttu-id="98643-509">篩選條件同時支援透過不同介面定義的同步和非同步實作。</span><span class="sxs-lookup"><span data-stu-id="98643-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="98643-510">同步篩選條件可以在其管線階段之前 (`On-Stage-Executing`) 和之後 (`On-Stage-Executed`) 執行程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="98643-511">例如，系統會在呼叫動作方法之前呼叫 `OnActionExecuting`。</span><span class="sxs-lookup"><span data-stu-id="98643-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="98643-512">系統會在傳回動作方法之後呼叫 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="98643-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="98643-513">非同步篩選條件會定義 `On-Stage-ExecutionAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="98643-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="98643-514">在上述程式碼中，`SampleAsyncActionFilter` 具有會執行動作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="98643-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="98643-515">每個 `On-Stage-ExecutionAsync` 都會接受能執行篩選條件之管線階段的 `FilterType-ExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="98643-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="98643-516">多個篩選條件階段</span><span class="sxs-lookup"><span data-stu-id="98643-516">Multiple filter stages</span></span>

<span data-ttu-id="98643-517">可以在單一類別中實作多個篩選條件階段的介面。</span><span class="sxs-lookup"><span data-stu-id="98643-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="98643-518">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 類別會實作 `IActionFilter`、`IResultFilter` 與其非同步對等項目。</span><span class="sxs-lookup"><span data-stu-id="98643-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="98643-519">請實作同步**或**非同步版本的篩選條件介面，而**不要**同時實作這兩者。</span><span class="sxs-lookup"><span data-stu-id="98643-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="98643-520">執行階段會先檢查以查看篩選條件是否會實作非同步介面，如果是，便會呼叫該介面。</span><span class="sxs-lookup"><span data-stu-id="98643-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="98643-521">如果沒有，它會呼叫同步介面的方法。</span><span class="sxs-lookup"><span data-stu-id="98643-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="98643-522">如果同時在單一類別中實作非同步和同步介面，系統只會呼叫非同步方法。</span><span class="sxs-lookup"><span data-stu-id="98643-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="98643-523">使用抽象類別 (例如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>) 時，請僅覆寫每個篩選條件類型的同步方法或非同步方法。</span><span class="sxs-lookup"><span data-stu-id="98643-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="98643-524">內建篩選條件屬性</span><span class="sxs-lookup"><span data-stu-id="98643-524">Built-in filter attributes</span></span>

<span data-ttu-id="98643-525">ASP.NET Core 包含內建的屬性型篩選條件，可對其進行子類別化和自訂。</span><span class="sxs-lookup"><span data-stu-id="98643-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="98643-526">例如，下列結果篩選條件會將標頭新增至回應：</span><span class="sxs-lookup"><span data-stu-id="98643-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="98643-527">屬性可讓篩選條件接受引數，如上述範例所示。</span><span class="sxs-lookup"><span data-stu-id="98643-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="98643-528">將 `AddHeaderAttribute` 新增至控制器或動作方法，並指定 HTTP 標頭的名稱和值：</span><span class="sxs-lookup"><span data-stu-id="98643-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="98643-529">有幾個篩選條件介面有對應的屬性，可用來作為自訂實作的基底類別。</span><span class="sxs-lookup"><span data-stu-id="98643-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="98643-530">篩選條件屬性：</span><span class="sxs-lookup"><span data-stu-id="98643-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="98643-531">篩選條件範圍和執行的順序</span><span class="sxs-lookup"><span data-stu-id="98643-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="98643-532">篩選條件能以三個「範圍」\*\* 之一新增至管線：</span><span class="sxs-lookup"><span data-stu-id="98643-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="98643-533">針對動作使用屬性。</span><span class="sxs-lookup"><span data-stu-id="98643-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="98643-534">針對控制器使用屬性。</span><span class="sxs-lookup"><span data-stu-id="98643-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="98643-535">全域地針對所有控制器和動作，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="98643-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="98643-536">上述程式碼會使用 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 集合來全域地新增三個篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="98643-537">預設執行順序</span><span class="sxs-lookup"><span data-stu-id="98643-537">Default order of execution</span></span>

<span data-ttu-id="98643-538">當有多個 *相同類型*的篩選準則時，範圍會決定篩選執行的預設順序。</span><span class="sxs-lookup"><span data-stu-id="98643-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="98643-539">全域篩選會圍繞類別篩選。</span><span class="sxs-lookup"><span data-stu-id="98643-539">Global filters surround class filters.</span></span> <span data-ttu-id="98643-540">類別篩選準則圍繞方法篩選。</span><span class="sxs-lookup"><span data-stu-id="98643-540">Class filters surround method filters.</span></span>

<span data-ttu-id="98643-541">因為篩選條件巢狀結構的原因，篩選條件的「之後」\*\* 程式碼的執行順序會與「之前」\*\* 程式碼相反。</span><span class="sxs-lookup"><span data-stu-id="98643-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="98643-542">篩選條件序列：</span><span class="sxs-lookup"><span data-stu-id="98643-542">The filter sequence:</span></span>

* <span data-ttu-id="98643-543">全域篩選條件的「之前」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="98643-544">控制器篩選條件的「之前」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="98643-545">動作方法篩選條件的「之前」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="98643-546">動作方法篩選條件的「之後」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="98643-547">控制器篩選條件的「之後」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="98643-548">全域篩選條件的「之後」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="98643-549">下列範例說明針對同步動作篩選條件呼叫篩選條件方法的順序。</span><span class="sxs-lookup"><span data-stu-id="98643-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="98643-550">順序</span><span class="sxs-lookup"><span data-stu-id="98643-550">Sequence</span></span> | <span data-ttu-id="98643-551">篩選條件範圍</span><span class="sxs-lookup"><span data-stu-id="98643-551">Filter scope</span></span> | <span data-ttu-id="98643-552">篩選條件方法</span><span class="sxs-lookup"><span data-stu-id="98643-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="98643-553">1</span><span class="sxs-lookup"><span data-stu-id="98643-553">1</span></span> | <span data-ttu-id="98643-554">全球</span><span class="sxs-lookup"><span data-stu-id="98643-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98643-555">2</span><span class="sxs-lookup"><span data-stu-id="98643-555">2</span></span> | <span data-ttu-id="98643-556">控制器</span><span class="sxs-lookup"><span data-stu-id="98643-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98643-557">3</span><span class="sxs-lookup"><span data-stu-id="98643-557">3</span></span> | <span data-ttu-id="98643-558">方法</span><span class="sxs-lookup"><span data-stu-id="98643-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98643-559">4</span><span class="sxs-lookup"><span data-stu-id="98643-559">4</span></span> | <span data-ttu-id="98643-560">方法</span><span class="sxs-lookup"><span data-stu-id="98643-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="98643-561">5</span><span class="sxs-lookup"><span data-stu-id="98643-561">5</span></span> | <span data-ttu-id="98643-562">控制器</span><span class="sxs-lookup"><span data-stu-id="98643-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="98643-563">6</span><span class="sxs-lookup"><span data-stu-id="98643-563">6</span></span> | <span data-ttu-id="98643-564">全球</span><span class="sxs-lookup"><span data-stu-id="98643-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="98643-565">此順序顯示：</span><span class="sxs-lookup"><span data-stu-id="98643-565">This sequence shows:</span></span>

* <span data-ttu-id="98643-566">方法篩選條件巢狀位於控制器篩選條件內。</span><span class="sxs-lookup"><span data-stu-id="98643-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="98643-567">控制器篩選條件巢狀位於全域篩選條件內。</span><span class="sxs-lookup"><span data-stu-id="98643-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="98643-568">控制器和 Razor 頁面層級篩選</span><span class="sxs-lookup"><span data-stu-id="98643-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="98643-569">繼承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基底類別的每個控制器都會包含 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)，以及 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="98643-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="98643-570">這些方法會：</span><span class="sxs-lookup"><span data-stu-id="98643-570">These methods:</span></span>

* <span data-ttu-id="98643-571">包裝針對指定動作執行的篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="98643-572">系統會在呼叫任何動作的篩選條件之前呼叫 `OnActionExecuting`。</span><span class="sxs-lookup"><span data-stu-id="98643-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="98643-573">系統會在呼叫所有動作篩選條件之後呼叫 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="98643-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="98643-574">系統會在呼叫任何動作的篩選條件之前呼叫 `OnActionExecutionAsync`。</span><span class="sxs-lookup"><span data-stu-id="98643-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="98643-575">在動作方法之後執行 `next` 後，位於篩選條件中的程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="98643-576">例如，在下載範例中，`MySampleActionFilter` 會在啟動時全域套用。</span><span class="sxs-lookup"><span data-stu-id="98643-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="98643-577">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="98643-577">The `TestController`:</span></span>

* <span data-ttu-id="98643-578">將 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 套用至 `FilterTest2` 動作。</span><span class="sxs-lookup"><span data-stu-id="98643-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="98643-579">覆寫 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="98643-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="98643-580">瀏覽至 `https://localhost:5001/Test/FilterTest2` 執行下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="98643-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="98643-581">若為 Razor 頁面，請參閱藉 [由覆 Razor 寫篩選方法來執行頁面篩選](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="98643-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="98643-582">覆寫預設順序</span><span class="sxs-lookup"><span data-stu-id="98643-582">Overriding the default order</span></span>

<span data-ttu-id="98643-583">可以藉由實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 來覆寫預設執行序列。</span><span class="sxs-lookup"><span data-stu-id="98643-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="98643-584">`IOrderedFilter` 會公開 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 屬性，其優先順序會高於範圍以決定執行順序。</span><span class="sxs-lookup"><span data-stu-id="98643-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="98643-585">具有較低 `Order` 值的篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="98643-586">在具有較高 `Order` 值的篩選條件之前執行「之前」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="98643-587">在具有較高 `Order` 值的篩選條件之後執行「之後」\*\* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="98643-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="98643-588">`Order` 屬性可以搭配建構函式參數設定：</span><span class="sxs-lookup"><span data-stu-id="98643-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="98643-589">請考慮先前範例所示的同樣 3 個動作篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="98643-590">如果控制器和全域篩選條件的 `Order` 屬性被個別設定為 1 和 2，執行順序將會反轉。</span><span class="sxs-lookup"><span data-stu-id="98643-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="98643-591">順序</span><span class="sxs-lookup"><span data-stu-id="98643-591">Sequence</span></span> | <span data-ttu-id="98643-592">篩選條件範圍</span><span class="sxs-lookup"><span data-stu-id="98643-592">Filter scope</span></span> | <span data-ttu-id="98643-593">`Order` 屬性</span><span class="sxs-lookup"><span data-stu-id="98643-593">`Order` property</span></span> | <span data-ttu-id="98643-594">篩選條件方法</span><span class="sxs-lookup"><span data-stu-id="98643-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="98643-595">1</span><span class="sxs-lookup"><span data-stu-id="98643-595">1</span></span> | <span data-ttu-id="98643-596">方法</span><span class="sxs-lookup"><span data-stu-id="98643-596">Method</span></span> | <span data-ttu-id="98643-597">0</span><span class="sxs-lookup"><span data-stu-id="98643-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98643-598">2</span><span class="sxs-lookup"><span data-stu-id="98643-598">2</span></span> | <span data-ttu-id="98643-599">控制器</span><span class="sxs-lookup"><span data-stu-id="98643-599">Controller</span></span> | <span data-ttu-id="98643-600">1</span><span class="sxs-lookup"><span data-stu-id="98643-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="98643-601">3</span><span class="sxs-lookup"><span data-stu-id="98643-601">3</span></span> | <span data-ttu-id="98643-602">全球</span><span class="sxs-lookup"><span data-stu-id="98643-602">Global</span></span> | <span data-ttu-id="98643-603">2</span><span class="sxs-lookup"><span data-stu-id="98643-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="98643-604">4</span><span class="sxs-lookup"><span data-stu-id="98643-604">4</span></span> | <span data-ttu-id="98643-605">全球</span><span class="sxs-lookup"><span data-stu-id="98643-605">Global</span></span> | <span data-ttu-id="98643-606">2</span><span class="sxs-lookup"><span data-stu-id="98643-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="98643-607">5</span><span class="sxs-lookup"><span data-stu-id="98643-607">5</span></span> | <span data-ttu-id="98643-608">控制器</span><span class="sxs-lookup"><span data-stu-id="98643-608">Controller</span></span> | <span data-ttu-id="98643-609">1</span><span class="sxs-lookup"><span data-stu-id="98643-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="98643-610">6</span><span class="sxs-lookup"><span data-stu-id="98643-610">6</span></span> | <span data-ttu-id="98643-611">方法</span><span class="sxs-lookup"><span data-stu-id="98643-611">Method</span></span> | <span data-ttu-id="98643-612">0</span><span class="sxs-lookup"><span data-stu-id="98643-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="98643-613">`Order` 屬性在決定篩選條件執行的順序時會覆寫範圍。</span><span class="sxs-lookup"><span data-stu-id="98643-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="98643-614">篩選條件會先依照順序排序，然後使用範圍來打破僵局。</span><span class="sxs-lookup"><span data-stu-id="98643-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="98643-615">所有內建的篩選條件都會實作 `IOrderedFilter` 並將預設 `Order` 值設為 0。</span><span class="sxs-lookup"><span data-stu-id="98643-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="98643-616">針對內建篩選條件，除非將 `Order` 設定為非零值，否則範圍會決定順序。</span><span class="sxs-lookup"><span data-stu-id="98643-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="98643-617">取消和縮短</span><span class="sxs-lookup"><span data-stu-id="98643-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="98643-618">可以設定提供給篩選條件方法之 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 參數上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 屬性，以縮短篩選條件管線。</span><span class="sxs-lookup"><span data-stu-id="98643-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="98643-619">比方說，下列資源篩選條件可防止管線的其餘部分執行：</span><span class="sxs-lookup"><span data-stu-id="98643-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="98643-620">在下列程式碼中，`ShortCircuitingResourceFilter` 和 `AddHeader` 篩選條件都以 `SomeResource` 動作方法為目標。</span><span class="sxs-lookup"><span data-stu-id="98643-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="98643-621">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="98643-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="98643-622">最先執行，因為它是資源篩選條件，且 `AddHeader` 是動作篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="98643-623">縮短管線的其餘部分。</span><span class="sxs-lookup"><span data-stu-id="98643-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="98643-624">因此，`SomeResource` 動作的 `AddHeader` 篩選條件永遠不會執行。</span><span class="sxs-lookup"><span data-stu-id="98643-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="98643-625">如果這兩個篩選條件都套用在動作方法層級，此行為也會相同，假設 `ShortCircuitingResourceFilter` 先執行的話。</span><span class="sxs-lookup"><span data-stu-id="98643-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="98643-626">`ShortCircuitingResourceFilter` 會因為其篩選器類型而先執行，或藉由明確使用 `Order` 屬性而先執行。</span><span class="sxs-lookup"><span data-stu-id="98643-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="98643-627">相依性插入</span><span class="sxs-lookup"><span data-stu-id="98643-627">Dependency injection</span></span>

<span data-ttu-id="98643-628">可以依類型或執行個體新增篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="98643-629">如果新增執行個體，該執行個體將用於每個要求。</span><span class="sxs-lookup"><span data-stu-id="98643-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="98643-630">如果新增類型，其將會由類型啟動。</span><span class="sxs-lookup"><span data-stu-id="98643-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="98643-631">由類型啟動的篩選條件表示：</span><span class="sxs-lookup"><span data-stu-id="98643-631">A type-activated filter means:</span></span>

* <span data-ttu-id="98643-632">系統會針對每個要求建立執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-632">An instance is created for each request.</span></span>
* <span data-ttu-id="98643-633">任何建構函式相依性都會由[相依性插入](xref:fundamentals/dependency-injection) (DI) 所填入。</span><span class="sxs-lookup"><span data-stu-id="98643-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="98643-634">實作為屬性並直接新增至控制器類別或動作方法的篩選條件，不能由[相依性插入](xref:fundamentals/dependency-injection) (DI) 提供建構函式相依性。</span><span class="sxs-lookup"><span data-stu-id="98643-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="98643-635">DI 無法提供建構函式相依性，因為：</span><span class="sxs-lookup"><span data-stu-id="98643-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="98643-636">必須在提供屬性之建構函式參數的地方提供它們。</span><span class="sxs-lookup"><span data-stu-id="98643-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="98643-637">這是屬性運作方式的限制。</span><span class="sxs-lookup"><span data-stu-id="98643-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="98643-638">下列篩選條件支援由 DI 所提供的建構函式相依性：</span><span class="sxs-lookup"><span data-stu-id="98643-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="98643-639">在屬性上實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="98643-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="98643-640">上述篩選條件可以被套用至控制器或動作方法：</span><span class="sxs-lookup"><span data-stu-id="98643-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="98643-641">DI 會提供記錄器。</span><span class="sxs-lookup"><span data-stu-id="98643-641">Loggers are available from DI.</span></span> <span data-ttu-id="98643-642">不過，請避免僅針對記錄目的建立並使用篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="98643-643">[內建架構記錄](xref:fundamentals/logging/index)通常便可以提供記錄所需的項目。</span><span class="sxs-lookup"><span data-stu-id="98643-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="98643-644">新增至篩選條件的記錄：</span><span class="sxs-lookup"><span data-stu-id="98643-644">Logging added to filters:</span></span>

* <span data-ttu-id="98643-645">應該專注在篩選條件特定的商務領域考量或行為。</span><span class="sxs-lookup"><span data-stu-id="98643-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="98643-646">**不**應該記錄動作或其他架構事件。</span><span class="sxs-lookup"><span data-stu-id="98643-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="98643-647">內建篩選條件能記錄動作和架構事件。</span><span class="sxs-lookup"><span data-stu-id="98643-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="98643-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="98643-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="98643-649">服務篩選條件實作類型會註冊在 `ConfigureServices` 中。</span><span class="sxs-lookup"><span data-stu-id="98643-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="98643-650"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 會從 DI 擷取篩選條件的執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="98643-651">下面程式碼會說明 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="98643-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="98643-652">在下列程式碼中，會將 `AddHeaderResultServiceFilter` 新增至 DI 容器：</span><span class="sxs-lookup"><span data-stu-id="98643-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="98643-653">在下列程式碼中，`ServiceFilter` 屬性會從 DI 擷取 `AddHeaderResultServiceFilter` 篩選條件的執行個體：</span><span class="sxs-lookup"><span data-stu-id="98643-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="98643-654">使用 `ServiceFilterAttribute` 時，設定 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)：</span><span class="sxs-lookup"><span data-stu-id="98643-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="98643-655">能提供篩選條件執行個體「可能」\*\* 可以在其建立的要求範圍之外重複使用的提示。</span><span class="sxs-lookup"><span data-stu-id="98643-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="98643-656">ASP.NET Core 執行階段並不保證：</span><span class="sxs-lookup"><span data-stu-id="98643-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="98643-657">將會建立篩選條件的單一執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="98643-658">將不會於稍後的時間從 DI 容器重新要求篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="98643-659">不應該搭配仰賴具有非單一資料庫存留期之服務的篩選條件使用。</span><span class="sxs-lookup"><span data-stu-id="98643-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="98643-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 會實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="98643-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="98643-661">`IFilterFactory` 會公開 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法來建立 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="98643-662">`CreateInstance` 會從 DI 載入指定的類型。</span><span class="sxs-lookup"><span data-stu-id="98643-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="98643-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="98643-663">TypeFilterAttribute</span></span>

<span data-ttu-id="98643-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 類似於 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>，但其類型不會直接從 DI 容器解析。</span><span class="sxs-lookup"><span data-stu-id="98643-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="98643-665">它會使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 來具現化類型。</span><span class="sxs-lookup"><span data-stu-id="98643-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="98643-666">由於 `TypeFilterAttribute` 類型不會直接從 DI 容器解析：</span><span class="sxs-lookup"><span data-stu-id="98643-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="98643-667">使用 `TypeFilterAttribute` 參考的類型不需要向 DI 容器註冊。</span><span class="sxs-lookup"><span data-stu-id="98643-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="98643-668">不過它們的相依性會由 DI 容器滿足。</span><span class="sxs-lookup"><span data-stu-id="98643-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="98643-669">`TypeFilterAttribute` 可以選擇性地接受類型的建構函式引數。</span><span class="sxs-lookup"><span data-stu-id="98643-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="98643-670">使用 `TypeFilterAttribute` 時，設定 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)：</span><span class="sxs-lookup"><span data-stu-id="98643-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="98643-671">能提供篩選條件執行個體「可能」\*\* 可以在其建立的要求範圍之外重複使用的提示。</span><span class="sxs-lookup"><span data-stu-id="98643-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="98643-672">ASP.NET Core 執行階段不保證將建立篩選條件的單一執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="98643-673">不應該搭配仰賴具有非單一資料庫存留期之服務的篩選條件使用。</span><span class="sxs-lookup"><span data-stu-id="98643-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="98643-674">下列範例示範如何使用 `TypeFilterAttribute` 將引數傳遞至類型：</span><span class="sxs-lookup"><span data-stu-id="98643-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="98643-675">授權篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-675">Authorization filters</span></span>

<span data-ttu-id="98643-676">授權篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-676">Authorization filters:</span></span>

* <span data-ttu-id="98643-677">是篩選條件管線內最先執行的篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="98643-678">控制動作方法的存取。</span><span class="sxs-lookup"><span data-stu-id="98643-678">Control access to action methods.</span></span>
* <span data-ttu-id="98643-679">有之前的方法，但沒有之後的方法。</span><span class="sxs-lookup"><span data-stu-id="98643-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="98643-680">自訂授權篩選條件需要自訂的授權架構。</span><span class="sxs-lookup"><span data-stu-id="98643-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="98643-681">最好是設定授權原則或撰寫自訂授權原則，而不要撰寫自訂篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="98643-682">內建的授權篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="98643-683">呼叫授權系統。</span><span class="sxs-lookup"><span data-stu-id="98643-683">Calls the authorization system.</span></span>
* <span data-ttu-id="98643-684">不會授權要求。</span><span class="sxs-lookup"><span data-stu-id="98643-684">Does not authorize requests.</span></span>

<span data-ttu-id="98643-685">**不會**在授權篩選條件內擲回例外狀況：</span><span class="sxs-lookup"><span data-stu-id="98643-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="98643-686">該例外狀況將不會被處理。</span><span class="sxs-lookup"><span data-stu-id="98643-686">The exception will not be handled.</span></span>
* <span data-ttu-id="98643-687">例外狀況篩選條件將不會處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="98643-688">請考慮在例外狀況於授權篩選條件中發生時發出挑戰。</span><span class="sxs-lookup"><span data-stu-id="98643-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="98643-689">深入了解[授權](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="98643-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="98643-690">資源篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-690">Resource filters</span></span>

<span data-ttu-id="98643-691">資源篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-691">Resource filters:</span></span>

* <span data-ttu-id="98643-692">實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 介面。</span><span class="sxs-lookup"><span data-stu-id="98643-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="98643-693">執行會包裝大部分的篩選條件管線。</span><span class="sxs-lookup"><span data-stu-id="98643-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="98643-694">只有[授權篩選條件](#authorization-filters)會在資源篩選條件之前執行。</span><span class="sxs-lookup"><span data-stu-id="98643-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="98643-695">資源篩選條件很適合用來縮短大部分的管線。</span><span class="sxs-lookup"><span data-stu-id="98643-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="98643-696">比方說，快取篩選條件可以避免快取命中上的其餘管線。</span><span class="sxs-lookup"><span data-stu-id="98643-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="98643-697">資源篩選條件範例：</span><span class="sxs-lookup"><span data-stu-id="98643-697">Resource filter examples:</span></span>

* <span data-ttu-id="98643-698">先前所示的[縮短資源篩選條件](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="98643-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="98643-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) \(英文\)：</span><span class="sxs-lookup"><span data-stu-id="98643-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="98643-700">防止模型繫結存取表單資料。</span><span class="sxs-lookup"><span data-stu-id="98643-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="98643-701">用於大型檔案上傳，以避免將表單資料讀入記憶體。</span><span class="sxs-lookup"><span data-stu-id="98643-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="98643-702">動作篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="98643-703">動作 **篩選不適用於** Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="98643-703">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="98643-704">Razor 頁面支援 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 。</span><span class="sxs-lookup"><span data-stu-id="98643-704">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="98643-705">如需詳細資訊，請參閱 [ Razor 頁面的篩選方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="98643-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="98643-706">動作篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-706">Action filters:</span></span>

* <span data-ttu-id="98643-707">實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 介面。</span><span class="sxs-lookup"><span data-stu-id="98643-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="98643-708">它們執行會包圍動作方法的執行。</span><span class="sxs-lookup"><span data-stu-id="98643-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="98643-709">下列程式碼會顯示範例動作篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="98643-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供下列屬性：</span><span class="sxs-lookup"><span data-stu-id="98643-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="98643-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 讓針對某個動作方法的輸入可以被讀取。</span><span class="sxs-lookup"><span data-stu-id="98643-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="98643-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - 提供對控制器執行個體的管理。</span><span class="sxs-lookup"><span data-stu-id="98643-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="98643-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 設定 `Result` 會縮短動作方法和後續動作篩選條件的執行。</span><span class="sxs-lookup"><span data-stu-id="98643-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="98643-714">在動作方法中擲回例外狀況：</span><span class="sxs-lookup"><span data-stu-id="98643-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="98643-715">防止執行後續篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="98643-716">和設定 `Result` 不同，會被視為失敗而非成功結果。</span><span class="sxs-lookup"><span data-stu-id="98643-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="98643-717"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 與 `Result`，加上下列屬性：</span><span class="sxs-lookup"><span data-stu-id="98643-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="98643-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果動作執行被另一個篩選條件縮短，則為 true。</span><span class="sxs-lookup"><span data-stu-id="98643-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="98643-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果動作或先前所執行的動作篩選條件擲回例外狀況，則為非 Null。</span><span class="sxs-lookup"><span data-stu-id="98643-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="98643-720">將此屬性設定為 Null：</span><span class="sxs-lookup"><span data-stu-id="98643-720">Setting this property to null:</span></span>

  * <span data-ttu-id="98643-721">實際上會「處理」例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="98643-722">會執行 `Result`，有如它是從動作方法傳回一般。</span><span class="sxs-lookup"><span data-stu-id="98643-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="98643-723">對於 `IAsyncActionFilter`，呼叫 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 會：</span><span class="sxs-lookup"><span data-stu-id="98643-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="98643-724">執行任何後續的動作篩選條件和動作方法。</span><span class="sxs-lookup"><span data-stu-id="98643-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="98643-725">傳回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="98643-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="98643-726">若要縮短，請指派 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 給某個結果執行個體，並且不要呼叫 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="98643-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="98643-727">架構提供抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>，其可被子類別化。</span><span class="sxs-lookup"><span data-stu-id="98643-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="98643-728">`OnActionExecuting` 動作篩選條件可以用來：</span><span class="sxs-lookup"><span data-stu-id="98643-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="98643-729">驗證模型狀態。</span><span class="sxs-lookup"><span data-stu-id="98643-729">Validate model state.</span></span>
* <span data-ttu-id="98643-730">如果狀態無效，則傳回錯誤。</span><span class="sxs-lookup"><span data-stu-id="98643-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="98643-731">`OnActionExecuted` 方法會在動作方法之後執行：</span><span class="sxs-lookup"><span data-stu-id="98643-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="98643-732">並可以透過 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 屬性查看及操作動作的結果。</span><span class="sxs-lookup"><span data-stu-id="98643-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="98643-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 會在動作執行被另一個篩選條件縮短時被設為 true。</span><span class="sxs-lookup"><span data-stu-id="98643-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="98643-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 會在動作或後續的動作篩選條件擲回例外狀況時被設為非 Null 值。</span><span class="sxs-lookup"><span data-stu-id="98643-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="98643-735">將 `Exception` 設定為 null：</span><span class="sxs-lookup"><span data-stu-id="98643-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="98643-736">實際上會「處理」例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="98643-737">執行 `ActionExecutedContext.Result`，彷彿它已正常地從動作方法傳回。</span><span class="sxs-lookup"><span data-stu-id="98643-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="98643-738">例外狀況篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-738">Exception filters</span></span>

<span data-ttu-id="98643-739">例外狀況篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-739">Exception filters:</span></span>

* <span data-ttu-id="98643-740">實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="98643-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="98643-741">可以用來實作常見的錯誤處理原則。</span><span class="sxs-lookup"><span data-stu-id="98643-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="98643-742">下列範例例外狀況篩選條件會使用自訂的錯誤檢視，以顯示在開發應用程式時發生的例外狀況詳細資料：</span><span class="sxs-lookup"><span data-stu-id="98643-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="98643-743">例外狀況篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-743">Exception filters:</span></span>

* <span data-ttu-id="98643-744">沒有之前和之後的事件。</span><span class="sxs-lookup"><span data-stu-id="98643-744">Don't have before and after events.</span></span>
* <span data-ttu-id="98643-745">實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="98643-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="98643-746">處理 Razor 頁面或控制器建立、 [模型](xref:mvc/models/model-binding)系結、動作篩選準則或動作方法中發生的未處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="98643-747">**不會**攔截在資源篩選條件、結果篩選條件或 MVC 結果執行中發生的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="98643-748">若要處理例外狀況，請將 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 屬性設為 `true`，或撰寫回應。</span><span class="sxs-lookup"><span data-stu-id="98643-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="98643-749">這樣會阻止傳播例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-749">This stops propagation of the exception.</span></span> <span data-ttu-id="98643-750">例外狀況篩選條件無法將例外狀況變成「成功」。</span><span class="sxs-lookup"><span data-stu-id="98643-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="98643-751">只有動作篩選條件可以這麼做。</span><span class="sxs-lookup"><span data-stu-id="98643-751">Only an action filter can do that.</span></span>

<span data-ttu-id="98643-752">例外狀況篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-752">Exception filters:</span></span>

* <span data-ttu-id="98643-753">適合用於設陷在動作內發生的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="98643-754">不像錯誤處理中介軟體那麼有彈性。</span><span class="sxs-lookup"><span data-stu-id="98643-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="98643-755">偏好使用中介軟體進行例外狀況處理。</span><span class="sxs-lookup"><span data-stu-id="98643-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="98643-756">只有在錯誤處理會根據所呼叫的動作方法而「不同」\*\* 時才使用例外狀況篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="98643-757">比方說，應用程式可能會有適用於 API 端點及檢視/HTML 的動作方法。</span><span class="sxs-lookup"><span data-stu-id="98643-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="98643-758">API 端點可能將錯誤資訊傳回為 JSON，而以檢視為基礎的動作則可能將錯誤頁面傳回為 HTML。</span><span class="sxs-lookup"><span data-stu-id="98643-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="98643-759">結果篩選條件</span><span class="sxs-lookup"><span data-stu-id="98643-759">Result filters</span></span>

<span data-ttu-id="98643-760">結果篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-760">Result filters:</span></span>

* <span data-ttu-id="98643-761">實作介面：</span><span class="sxs-lookup"><span data-stu-id="98643-761">Implement an interface:</span></span>
  * <span data-ttu-id="98643-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="98643-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="98643-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="98643-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="98643-764">它們執行會包圍動作結果的執行。</span><span class="sxs-lookup"><span data-stu-id="98643-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="98643-765">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="98643-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="98643-766">以下程式碼會顯示能新增 HTTP 標頭的結果篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="98643-767">執行的結果類型會取決於動作。</span><span class="sxs-lookup"><span data-stu-id="98643-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="98643-768">傳回檢視的動作會在執行中的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 裡包含處理中的所有 Razor。</span><span class="sxs-lookup"><span data-stu-id="98643-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="98643-769">API 方法可能在結果執行當中執行某種序列化。</span><span class="sxs-lookup"><span data-stu-id="98643-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="98643-770">深入瞭解 [動作結果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="98643-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="98643-771">結果篩選準則只會在動作或動作篩選產生動作結果時執行。</span><span class="sxs-lookup"><span data-stu-id="98643-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="98643-772">當下列情況時，不會執行結果篩選：</span><span class="sxs-lookup"><span data-stu-id="98643-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="98643-773">授權篩選準則或資源篩選器會對管線進行較短的線路。</span><span class="sxs-lookup"><span data-stu-id="98643-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="98643-774">例外狀況篩選條件會產生動作結果來處理例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="98643-775">藉由將 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 設為 `true`，<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以縮短動作結果和後續結果篩選條件的執行。</span><span class="sxs-lookup"><span data-stu-id="98643-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="98643-776">在縮短時寫入至回應物件，以避免產生空的回應。</span><span class="sxs-lookup"><span data-stu-id="98643-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="98643-777">在 `IResultFilter.OnResultExecuting` 中擲回例外狀況會：</span><span class="sxs-lookup"><span data-stu-id="98643-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="98643-778">導致無法執行動作結果和後續的篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="98643-779">視為失敗，而不是成功的結果。</span><span class="sxs-lookup"><span data-stu-id="98643-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="98643-780">當 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法執行時，可能已經將回應傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="98643-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="98643-781">如果回應已傳送至用戶端，則無法進一步變更。</span><span class="sxs-lookup"><span data-stu-id="98643-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="98643-782">如果動作結果執行被另一個篩選條件縮短，則 `ResultExecutedContext.Canceled` 會被設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="98643-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="98643-783">如果動作結果或後續的結果篩選條件擲回例外狀況，則 `ResultExecutedContext.Exception` 會被設為非 Null 值。</span><span class="sxs-lookup"><span data-stu-id="98643-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="98643-784">將 `Exception` 設為 Null 實際上會「處理」例外狀況，並且使 ASP.NET Core 稍後不會在管線中重新擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="98643-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="98643-785">並沒有可以在處理結果篩選條件中的例外狀況時，將資料寫入回應的可靠方式。</span><span class="sxs-lookup"><span data-stu-id="98643-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="98643-786">當動作結果擲回例外狀況時，如果標頭已清除至用戶端，則沒有任何可靠的機制能傳送失敗碼。</span><span class="sxs-lookup"><span data-stu-id="98643-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="98643-787">針對 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> 而言，對 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 的呼叫會執行任何後續的結果篩選條件和動作結果。</span><span class="sxs-lookup"><span data-stu-id="98643-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="98643-788">若要縮短，請將 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 設定為 `true`，並且不要呼叫 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="98643-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="98643-789">架構提供抽象 `ResultFilterAttribute`，其可被子類別化。</span><span class="sxs-lookup"><span data-stu-id="98643-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="98643-790">先前所示的 [AddHeaderAttribute](#add-header-attribute) 類別是結果篩選條件屬性的範例。</span><span class="sxs-lookup"><span data-stu-id="98643-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="98643-791">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="98643-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="98643-792"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 介面會宣告針對所有動作結果執行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 實作。</span><span class="sxs-lookup"><span data-stu-id="98643-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="98643-793">這包括下列所產生的動作結果：</span><span class="sxs-lookup"><span data-stu-id="98643-793">This includes action results produced by:</span></span>

* <span data-ttu-id="98643-794">簡短電路的授權篩選和資源篩選。</span><span class="sxs-lookup"><span data-stu-id="98643-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="98643-795">例外狀況篩選條件。</span><span class="sxs-lookup"><span data-stu-id="98643-795">Exception filters.</span></span>

<span data-ttu-id="98643-796">例如，下列篩選一律會執行，並會在內容交涉失敗時，為動作結果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) 設定「422 無法處理的實體」\*\* 狀態碼：</span><span class="sxs-lookup"><span data-stu-id="98643-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="98643-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="98643-797">IFilterFactory</span></span>

<span data-ttu-id="98643-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 會實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="98643-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="98643-799">因此，`IFilterFactory` 執行個體可用來在篩選條件管線中任何位置作為 `IFilterMetadata` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="98643-800">當執行階段準備要叫用篩選條件時，它會嘗試將它轉換成 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="98643-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="98643-801">如果該轉換成功，系統會呼叫 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法來建立被叫用的 `IFilterMetadata` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="98643-802">因為在應用程式啟動時不需要明確設定精確的篩選條件管線，所以這提供了具有彈性的設計。</span><span class="sxs-lookup"><span data-stu-id="98643-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="98643-803">可以使用自訂屬性實作作為另一種建立篩選條件的方法，來實作 `IFilterFactory`：</span><span class="sxs-lookup"><span data-stu-id="98643-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="98643-804">上述程式碼可以透過執行[下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) \(英文\) 來進行測試：</span><span class="sxs-lookup"><span data-stu-id="98643-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="98643-805">叫用 F12 開發人員工具。</span><span class="sxs-lookup"><span data-stu-id="98643-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="98643-806">瀏覽至 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="98643-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="98643-807">F12 開發人員工具會顯示由範例程式碼所加入的下列回應標頭：</span><span class="sxs-lookup"><span data-stu-id="98643-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="98643-808">**作者：**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="98643-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="98643-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="98643-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="98643-810">**內部：**`My header`</span><span class="sxs-lookup"><span data-stu-id="98643-810">**internal:** `My header`</span></span>

<span data-ttu-id="98643-811">上述程式碼會建立 **internal:** `My header` 回應標頭。</span><span class="sxs-lookup"><span data-stu-id="98643-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="98643-812">在屬性上實作的 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="98643-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="98643-813">實作 `IFilterFactory` 的篩選條件很適合用於下列類型的篩選條件：</span><span class="sxs-lookup"><span data-stu-id="98643-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="98643-814">不需要傳遞參數。</span><span class="sxs-lookup"><span data-stu-id="98643-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="98643-815">有需要由 DI 滿足的建構函式相依性。</span><span class="sxs-lookup"><span data-stu-id="98643-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="98643-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 會實作 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="98643-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="98643-817">`IFilterFactory` 會公開 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法來建立 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 執行個體。</span><span class="sxs-lookup"><span data-stu-id="98643-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="98643-818">`CreateInstance` 會從服務容器 (DI) 載入指定的類型。</span><span class="sxs-lookup"><span data-stu-id="98643-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="98643-819">下列程式碼會顯示套用 `[SampleActionFilter]` 的三種方式：</span><span class="sxs-lookup"><span data-stu-id="98643-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="98643-820">在上述程式碼中，使用 `[SampleActionFilter]` 來裝飾方法是套用 `SampleActionFilter` 的建議方法。</span><span class="sxs-lookup"><span data-stu-id="98643-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="98643-821">在篩選條件管線中使用中介軟體</span><span class="sxs-lookup"><span data-stu-id="98643-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="98643-822">資源篩選條件的運作與[中介軟體](xref:fundamentals/middleware/index)相似之處在於，它們會圍繞管線中稍後的所有項目執行。</span><span class="sxs-lookup"><span data-stu-id="98643-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="98643-823">但篩選條件與中介軟體不同之處在於它們是 ASP.NET Core 執行階段的一部分，這表示它們能存取 ASP.NET Core 內容和建構。</span><span class="sxs-lookup"><span data-stu-id="98643-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="98643-824">若要使用中介軟體作為篩選條件，請建立一個具有 `Configure` 方法的類型，其能指定要插入到篩選條件管線的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="98643-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="98643-825">以下範例會使用當地語系化中介軟體來建立某個要求目前的文化特性：</span><span class="sxs-lookup"><span data-stu-id="98643-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="98643-826">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 來執行中介軟體：</span><span class="sxs-lookup"><span data-stu-id="98643-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="98643-827">中介軟體篩選條件與資源篩選條件在相同的篩選條件管線階段執行：模型繫結之前和管線的其餘部分之後。</span><span class="sxs-lookup"><span data-stu-id="98643-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="98643-828">後續動作</span><span class="sxs-lookup"><span data-stu-id="98643-828">Next actions</span></span>

* <span data-ttu-id="98643-829">請參閱 [ Razor 頁面的篩選方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="98643-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="98643-830">若要嘗試使用篩選準則，請 [下載、測試及修改 GitHub 範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="98643-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
