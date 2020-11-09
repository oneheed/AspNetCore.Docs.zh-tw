---
title: ASP.NET Core web API 中的控制器動作傳回類型
author: scottaddie
description: 瞭解如何在 ASP.NET Core web API 中使用各種控制器動作方法的傳回類型。
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: web-api/action-return-types
ms.openlocfilehash: a2866970a20785ae8fa306d484972697817b7f92
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058944"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="27e39-103">ASP.NET Core web API 中的控制器動作傳回類型</span><span class="sxs-lookup"><span data-stu-id="27e39-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="27e39-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="27e39-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="27e39-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="27e39-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="27e39-106">ASP.NET Core 針對 web API 控制器動作傳回類型提供下列選項：</span><span class="sxs-lookup"><span data-stu-id="27e39-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="27e39-107">特定類型</span><span class="sxs-lookup"><span data-stu-id="27e39-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="27e39-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="27e39-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="27e39-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="27e39-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="27e39-110">特定類型</span><span class="sxs-lookup"><span data-stu-id="27e39-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="27e39-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="27e39-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="27e39-112">本文件說明使用每個傳回型別的最適時機。</span><span class="sxs-lookup"><span data-stu-id="27e39-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="27e39-113">特定類型</span><span class="sxs-lookup"><span data-stu-id="27e39-113">Specific type</span></span>

<span data-ttu-id="27e39-114">最簡單的動作傳回基本或複雜的資料類型 (例如，`string` 或自訂物件類型)。</span><span class="sxs-lookup"><span data-stu-id="27e39-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="27e39-115">請考慮下列動作，它會傳回自訂 `Product` 物件的集合：</span><span class="sxs-lookup"><span data-stu-id="27e39-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="27e39-116">無已知條件，無法確保在動作執行期間能滿足傳回特定類型。</span><span class="sxs-lookup"><span data-stu-id="27e39-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="27e39-117">上述動作不接受任何參數，因此不需要驗證參數條件約束。</span><span class="sxs-lookup"><span data-stu-id="27e39-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="27e39-118">當有多個傳回型別時，通常會混合 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 具有基本或複雜傳回型別的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="27e39-118">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="27e39-119">需要[>iactionresult](#iactionresult-type)或[ActionResult \<T> ](#actionresultt-type)來容納這種類型的動作。</span><span class="sxs-lookup"><span data-stu-id="27e39-119">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="27e39-120">本檔提供數個傳回類型的範例。</span><span class="sxs-lookup"><span data-stu-id="27e39-120">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="27e39-121">傳回 IEnumerable \<T> 或 IAsyncEnumerable\<T></span><span class="sxs-lookup"><span data-stu-id="27e39-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="27e39-122">在 ASP.NET Core 2.2 及更早版本中， <xref:System.Collections.Generic.IEnumerable%601> 從動作傳回會導致序列化程式進行同步的集合反復專案。</span><span class="sxs-lookup"><span data-stu-id="27e39-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="27e39-123">結果是封鎖呼叫，以及執行緒集區耗盡的可能性。</span><span class="sxs-lookup"><span data-stu-id="27e39-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="27e39-124">為了說明這一點，假設 Entity Framework (EF) Core 用於 web API 的資料存取需求。</span><span class="sxs-lookup"><span data-stu-id="27e39-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="27e39-125">在序列化期間，會以同步方式列舉下列動作的傳回型別：</span><span class="sxs-lookup"><span data-stu-id="27e39-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="27e39-126">若要避免在 ASP.NET Core 2.2 及更早版本的資料庫上進行同步列舉和封鎖等候，請叫用 `ToListAsync` ：</span><span class="sxs-lookup"><span data-stu-id="27e39-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="27e39-127">在 ASP.NET Core 3.0 和更新版本中， `IAsyncEnumerable<T>` 從動作傳回：</span><span class="sxs-lookup"><span data-stu-id="27e39-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="27e39-128">不再產生同步反復專案。</span><span class="sxs-lookup"><span data-stu-id="27e39-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="27e39-129">成為傳回的效率 <xref:System.Collections.Generic.IEnumerable%601> 。</span><span class="sxs-lookup"><span data-stu-id="27e39-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="27e39-130">在提供序列化程式給序列化程式之前，ASP.NET Core 3.0 和更新版本會緩衝下列動作的結果：</span><span class="sxs-lookup"><span data-stu-id="27e39-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="27e39-131">請考慮將動作簽章的傳回類型宣告為 `IAsyncEnumerable<T>` ，以保證非同步反覆運算。</span><span class="sxs-lookup"><span data-stu-id="27e39-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="27e39-132">最後，反覆運算模式是以所傳回的基礎具象型別為基礎。</span><span class="sxs-lookup"><span data-stu-id="27e39-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="27e39-133">MVC 會自動緩衝任何實作為的實體型別 `IAsyncEnumerable<T>` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="27e39-134">請考慮下列動作，此動作會傳回銷售定價的產品記錄，如下所示 `IEnumerable<Product>` ：</span><span class="sxs-lookup"><span data-stu-id="27e39-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="27e39-135">`IAsyncEnumerable<Product>`上述動作的對等專案為：</span><span class="sxs-lookup"><span data-stu-id="27e39-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="27e39-136">從 ASP.NET Core 3.0 起，上述兩個動作都不會封鎖。</span><span class="sxs-lookup"><span data-stu-id="27e39-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="27e39-137">IActionResult 類型</span><span class="sxs-lookup"><span data-stu-id="27e39-137">IActionResult type</span></span>

<span data-ttu-id="27e39-138"><xref:Microsoft.AspNetCore.Mvc.IActionResult>當動作中可能有多個傳回型別時，傳回型別是適當的 `ActionResult` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="27e39-139">`ActionResult` 類型代表各種 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="27e39-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="27e39-140">任何衍生自的非抽象類別都 `ActionResult` 合格為有效的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="27e39-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="27e39-141">此類別中的某些常見傳回型別為 <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400) 、 <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) 和 <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200) 。</span><span class="sxs-lookup"><span data-stu-id="27e39-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="27e39-142">或者，您也可以使用類別中的便利性方法， <xref:Microsoft.AspNetCore.Mvc.ControllerBase> `ActionResult` 從動作傳回類型。</span><span class="sxs-lookup"><span data-stu-id="27e39-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="27e39-143">例如， `return BadRequest();` 是的縮寫形式 `return new BadRequestResult();` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="27e39-144">因為這種類型的動作中有多個傳回型別和路徑，所以需要使用屬性的自由 [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="27e39-144">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="27e39-145">這個屬性會針對 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)等工具產生的 web API 說明頁面產生更具描述性的回應詳細資料。</span><span class="sxs-lookup"><span data-stu-id="27e39-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="27e39-146">`[ProducesResponseType]` 表示已知類型和 HTTP 狀態碼要由動作傳回。</span><span class="sxs-lookup"><span data-stu-id="27e39-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="27e39-147">同步動作</span><span class="sxs-lookup"><span data-stu-id="27e39-147">Synchronous action</span></span>

<span data-ttu-id="27e39-148">請考慮下列有兩個可能傳回型別的同步動作：</span><span class="sxs-lookup"><span data-stu-id="27e39-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="27e39-149">在上述動作中：</span><span class="sxs-lookup"><span data-stu-id="27e39-149">In the preceding action:</span></span>

* <span data-ttu-id="27e39-150">當 `id` 基礎資料存放區中沒有代表的產品存在時，就會傳回404狀態碼。</span><span class="sxs-lookup"><span data-stu-id="27e39-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="27e39-151"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>方便的方法會被叫用為的速記 `return new NotFoundResult();` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="27e39-152">當產品存在時，會傳回200狀態碼與 `Product` 物件。</span><span class="sxs-lookup"><span data-stu-id="27e39-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="27e39-153"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>方便的方法會被叫用為的速記 `return new OkObjectResult(product);` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="27e39-154">非同步動作</span><span class="sxs-lookup"><span data-stu-id="27e39-154">Asynchronous action</span></span>

<span data-ttu-id="27e39-155">請考慮下列有兩個可能傳回型別的同步動作：</span><span class="sxs-lookup"><span data-stu-id="27e39-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="27e39-156">在上述動作中：</span><span class="sxs-lookup"><span data-stu-id="27e39-156">In the preceding action:</span></span>

* <span data-ttu-id="27e39-157">當產品描述包含 "XYZ Widget" 時，會傳回400狀態碼。</span><span class="sxs-lookup"><span data-stu-id="27e39-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="27e39-158"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>方便的方法會被叫用為的速記 `return new BadRequestResult();` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="27e39-159">建立產品時，便利性方法會產生201狀態碼 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 。</span><span class="sxs-lookup"><span data-stu-id="27e39-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="27e39-160">另一個呼叫的方法 `CreatedAtAction` 是 `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="27e39-161">在此程式碼路徑中， `Product` 會在回應主體中提供物件。</span><span class="sxs-lookup"><span data-stu-id="27e39-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="27e39-162">`Location`提供包含新建立產品 URL 的回應標頭。</span><span class="sxs-lookup"><span data-stu-id="27e39-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="27e39-163">例如，下列模型指出要求必須包含 `Name` 和 `Description` 屬性。</span><span class="sxs-lookup"><span data-stu-id="27e39-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="27e39-164">無法提供 `Name` 和 `Description` 要求會導致模型驗證失敗。</span><span class="sxs-lookup"><span data-stu-id="27e39-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="27e39-165">如果套用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) ASP.NET Core 2.1 或更新版本中的屬性，則模型驗證錯誤會導致400狀態碼。</span><span class="sxs-lookup"><span data-stu-id="27e39-165">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="27e39-166">如需詳細資訊，請參閱[自動 HTTP 400 回應](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="27e39-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="27e39-167">ActionResult \<T> 類型</span><span class="sxs-lookup"><span data-stu-id="27e39-167">ActionResult\<T> type</span></span>

<span data-ttu-id="27e39-168">ASP.NET Core 2.1 引進了 web API 控制器動作的[ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1)傳回型別。</span><span class="sxs-lookup"><span data-stu-id="27e39-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="27e39-169">它可讓您傳回衍生自的型別， <xref:Microsoft.AspNetCore.Mvc.ActionResult> 或傳回 [特定型](#specific-type)別。</span><span class="sxs-lookup"><span data-stu-id="27e39-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="27e39-170">`ActionResult<T>` 透過 [IActionResult 類型](#iactionresult-type)提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="27e39-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="27e39-171">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` 可以排除屬性的屬性。</span><span class="sxs-lookup"><span data-stu-id="27e39-171">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="27e39-172">例如，`[ProducesResponseType(200, Type = typeof(Product))]` 簡化為 `[ProducesResponseType(200)]`。</span><span class="sxs-lookup"><span data-stu-id="27e39-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="27e39-173">該動作的預期傳回型別會改為從 `ActionResult<T>` 中的 `T` 推斷。</span><span class="sxs-lookup"><span data-stu-id="27e39-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="27e39-174">[隱含轉型運算子](/dotnet/csharp/language-reference/keywords/implicit)支援 `T` 和 `ActionResult` 轉換成 `ActionResult<T>`。</span><span class="sxs-lookup"><span data-stu-id="27e39-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="27e39-175">`T` 轉換為 <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ，表示 `return new ObjectResult(T);` 簡化為 `return T;` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="27e39-176">C# 不支援介面上的隱含轉換運算子。</span><span class="sxs-lookup"><span data-stu-id="27e39-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="27e39-177">因此，必須將介面轉換為具象型別才能使用 `ActionResult<T>`。</span><span class="sxs-lookup"><span data-stu-id="27e39-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="27e39-178">例如，在下列範例中使用 `IEnumerable` 將無法運作：</span><span class="sxs-lookup"><span data-stu-id="27e39-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="27e39-179">修正上述程式碼的其中一個選項是傳回 `_repository.GetProducts().ToList();`。</span><span class="sxs-lookup"><span data-stu-id="27e39-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="27e39-180">大部分的動作都有特定的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="27e39-180">Most actions have a specific return type.</span></span> <span data-ttu-id="27e39-181">如果在動作執行期間發生非預期的狀況，就不會傳回特定的類型。</span><span class="sxs-lookup"><span data-stu-id="27e39-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="27e39-182">例如，動作的輸入參數可能無法驗證模型。</span><span class="sxs-lookup"><span data-stu-id="27e39-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="27e39-183">在此情況下，通常會傳回適當的 `ActionResult` 類型而不是特定的類型。</span><span class="sxs-lookup"><span data-stu-id="27e39-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="27e39-184">同步動作</span><span class="sxs-lookup"><span data-stu-id="27e39-184">Synchronous action</span></span>

<span data-ttu-id="27e39-185">請考慮有兩個可能傳回型別的同步動作：</span><span class="sxs-lookup"><span data-stu-id="27e39-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="27e39-186">在上述動作中：</span><span class="sxs-lookup"><span data-stu-id="27e39-186">In the preceding action:</span></span>

* <span data-ttu-id="27e39-187">當產品不存在於資料庫中時，會傳回404狀態碼。</span><span class="sxs-lookup"><span data-stu-id="27e39-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="27e39-188">當產品存在時，會傳回200狀態碼和對應的 `Product` 物件。</span><span class="sxs-lookup"><span data-stu-id="27e39-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="27e39-189">在 ASP.NET Core 2.1 之前， `return product;` 行必須是 `return Ok(product);` 。</span><span class="sxs-lookup"><span data-stu-id="27e39-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="27e39-190">非同步動作</span><span class="sxs-lookup"><span data-stu-id="27e39-190">Asynchronous action</span></span>

<span data-ttu-id="27e39-191">請考慮有兩個可能傳回型別的非同步動作：</span><span class="sxs-lookup"><span data-stu-id="27e39-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="27e39-192">在上述動作中：</span><span class="sxs-lookup"><span data-stu-id="27e39-192">In the preceding action:</span></span>

* <span data-ttu-id="27e39-193"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>當下列情況時，ASP.NET Core 執行時間會傳回400狀態碼 () ：</span><span class="sxs-lookup"><span data-stu-id="27e39-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="27e39-194">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)屬性已套用，而且模型驗證失敗。</span><span class="sxs-lookup"><span data-stu-id="27e39-194">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="27e39-195">產品描述包含 "XYZ Widget"。</span><span class="sxs-lookup"><span data-stu-id="27e39-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="27e39-196">建立產品時，方法會產生201狀態碼 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 。</span><span class="sxs-lookup"><span data-stu-id="27e39-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="27e39-197">在此程式碼路徑中， `Product` 會在回應主體中提供物件。</span><span class="sxs-lookup"><span data-stu-id="27e39-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="27e39-198">`Location`提供包含新建立產品 URL 的回應標頭。</span><span class="sxs-lookup"><span data-stu-id="27e39-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="27e39-199">其他資源</span><span class="sxs-lookup"><span data-stu-id="27e39-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
