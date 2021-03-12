---
title: ASP.NET Core Web API 中的 JsonPatch
author: rick-anderson
description: 了解如何處理 ASP.NET Core Web API 中的 JSON Patch 要求。
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: 4ed44a0fca9e0834a78e433cdd48cbd153c58666
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587849"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="863e0-103">ASP.NET Core Web API 中的 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="863e0-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="863e0-104">由 [Tom Dykstra](https://github.com/tdykstra) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="863e0-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="863e0-105">本文說明如何處理 ASP.NET Core Web API 中的 JSON Patch 要求。</span><span class="sxs-lookup"><span data-stu-id="863e0-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="863e0-106">套件安裝</span><span class="sxs-lookup"><span data-stu-id="863e0-106">Package installation</span></span>

<span data-ttu-id="863e0-107">若要在您的應用程式中啟用 JSON 修補程式支援，請完成下列步驟：</span><span class="sxs-lookup"><span data-stu-id="863e0-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="863e0-108">安裝 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="863e0-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="863e0-109">更新專案 `Startup.ConfigureServices` 要呼叫的方法 <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> 。</span><span class="sxs-lookup"><span data-stu-id="863e0-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="863e0-110">例如：</span><span class="sxs-lookup"><span data-stu-id="863e0-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="863e0-111">`AddNewtonsoftJson` 相容于 MVC 服務註冊方法：</span><span class="sxs-lookup"><span data-stu-id="863e0-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="863e0-112">JSON Patch、AddNewtonsoftJson 和 System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="863e0-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="863e0-113">`AddNewtonsoftJson` 取代 `System.Text.Json` 用來格式化 **所有** JSON 內容的型輸入和輸出格式器。</span><span class="sxs-lookup"><span data-stu-id="863e0-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="863e0-114">若要使用來新增 JSON 修補程式的支援 `Newtonsoft.Json` ，同時讓其他格式器保持不變，請更新專案的方法，如下所示 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="863e0-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="863e0-115">上述程式碼需要 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 封裝和下列 `using` 語句：</span><span class="sxs-lookup"><span data-stu-id="863e0-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="863e0-116">PATCH HTTP 要求方法</span><span class="sxs-lookup"><span data-stu-id="863e0-116">PATCH HTTP request method</span></span>

<span data-ttu-id="863e0-117">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) \(英文\) 方法均用來更新現有的資源。</span><span class="sxs-lookup"><span data-stu-id="863e0-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="863e0-118">它們之間的差異是 PUT 會取代整個資源，而 PATCH 只會指定變更。</span><span class="sxs-lookup"><span data-stu-id="863e0-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="863e0-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="863e0-119">JSON Patch</span></span>

<span data-ttu-id="863e0-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) \(英文\) 是一種格式，可用來指定要套用至資源的更新。</span><span class="sxs-lookup"><span data-stu-id="863e0-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="863e0-121">JSON Patch 文件具有一個「作業」陣列。</span><span class="sxs-lookup"><span data-stu-id="863e0-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="863e0-122">每個作業都會識別特定的變更類型。</span><span class="sxs-lookup"><span data-stu-id="863e0-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="863e0-123">這類變更的範例包括新增陣列元素或取代屬性值。</span><span class="sxs-lookup"><span data-stu-id="863e0-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="863e0-124">例如，下列 JSON 檔代表資源、資源的 JSON 修補檔，以及套用修補作業的結果。</span><span class="sxs-lookup"><span data-stu-id="863e0-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="863e0-125">資源範例</span><span class="sxs-lookup"><span data-stu-id="863e0-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="863e0-126">JSON 修補範例</span><span class="sxs-lookup"><span data-stu-id="863e0-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="863e0-127">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="863e0-127">In the preceding JSON:</span></span>

* <span data-ttu-id="863e0-128">`op` 屬性會指出作業的類型。</span><span class="sxs-lookup"><span data-stu-id="863e0-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="863e0-129">`path` 屬性會指出要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="863e0-130">`value` 屬性會提供新值。</span><span class="sxs-lookup"><span data-stu-id="863e0-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="863e0-131">修補之後的資源</span><span class="sxs-lookup"><span data-stu-id="863e0-131">Resource after patch</span></span>

<span data-ttu-id="863e0-132">以下是套用上述 JSON Patch 文件之後的資源：</span><span class="sxs-lookup"><span data-stu-id="863e0-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="863e0-133">將 JSON 修補檔套用至資源所做的變更是不可部分完成的。</span><span class="sxs-lookup"><span data-stu-id="863e0-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="863e0-134">如果清單中的任何作業失敗，則不會套用清單中的任何作業。</span><span class="sxs-lookup"><span data-stu-id="863e0-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="863e0-135">路徑語法</span><span class="sxs-lookup"><span data-stu-id="863e0-135">Path syntax</span></span>

<span data-ttu-id="863e0-136">作業物件的 [path](https://tools.ietf.org/html/rfc6901) \(英文\) 屬性在層級之間有斜線。</span><span class="sxs-lookup"><span data-stu-id="863e0-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="863e0-137">例如： `"/address/zipCode"` 。</span><span class="sxs-lookup"><span data-stu-id="863e0-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="863e0-138">以零為起始的索引可用來指定陣列元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="863e0-139">`addresses` 陣列的第一個元素會在 `/addresses/0` 上。</span><span class="sxs-lookup"><span data-stu-id="863e0-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="863e0-140">到 `add` 陣列的結尾，使用連字號 (`-`) ，而不是索引編號： `/addresses/-` 。</span><span class="sxs-lookup"><span data-stu-id="863e0-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="863e0-141">Operations</span><span class="sxs-lookup"><span data-stu-id="863e0-141">Operations</span></span>

<span data-ttu-id="863e0-142">下表顯示支援的作業，如 [JSON Patch 規格](https://tools.ietf.org/html/rfc6902) \(英文\) 中所定義：</span><span class="sxs-lookup"><span data-stu-id="863e0-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="863e0-143">作業</span><span class="sxs-lookup"><span data-stu-id="863e0-143">Operation</span></span>  | <span data-ttu-id="863e0-144">備註</span><span class="sxs-lookup"><span data-stu-id="863e0-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="863e0-145">加入屬性或陣列元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-145">Add a property or array element.</span></span> <span data-ttu-id="863e0-146">針對現有的屬性：設定值。</span><span class="sxs-lookup"><span data-stu-id="863e0-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="863e0-147">移除屬性或陣列元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="863e0-148">與 `remove` 之後接著在同一個位置上 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="863e0-149">與從來源 `remove` 之後接著使用來源的值 `add` 到目的地相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="863e0-150">與使用來源的值 `add` 到目的地相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="863e0-151">如果 `path` 上的值 = 所提供的 `value`，即會傳回成功狀態碼。</span><span class="sxs-lookup"><span data-stu-id="863e0-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="863e0-152">ASP.NET Core 中的 JSON 修補程式</span><span class="sxs-lookup"><span data-stu-id="863e0-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="863e0-153">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) \(英文\) NuGet 套件中會提供 JSON Patch 的 ASP.NET Core 實作。</span><span class="sxs-lookup"><span data-stu-id="863e0-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="863e0-154">動作方法程式碼</span><span class="sxs-lookup"><span data-stu-id="863e0-154">Action method code</span></span>

<span data-ttu-id="863e0-155">在 API 控制器中，JSON Patch 的動作方法：</span><span class="sxs-lookup"><span data-stu-id="863e0-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="863e0-156">使用 `HttpPatch` 屬性來標註。</span><span class="sxs-lookup"><span data-stu-id="863e0-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="863e0-157">接受 `JsonPatchDocument<T>` ，通常是 `[FromBody]` 。</span><span class="sxs-lookup"><span data-stu-id="863e0-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="863e0-158">呼叫修補文件上的 `ApplyTo` 以套用變更。</span><span class="sxs-lookup"><span data-stu-id="863e0-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="863e0-159">以下為範例：</span><span class="sxs-lookup"><span data-stu-id="863e0-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="863e0-160">此範例應用程式中的程式碼會搭配下列 `Customer` 模型運作：</span><span class="sxs-lookup"><span data-stu-id="863e0-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="863e0-161">範例動作方法：</span><span class="sxs-lookup"><span data-stu-id="863e0-161">The sample action method:</span></span>

* <span data-ttu-id="863e0-162">建構 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="863e0-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="863e0-163">套用修補檔案。</span><span class="sxs-lookup"><span data-stu-id="863e0-163">Applies the patch.</span></span>
* <span data-ttu-id="863e0-164">在回應本文中傳回結果。</span><span class="sxs-lookup"><span data-stu-id="863e0-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="863e0-165">在實際的應用程式中，程式碼會從資料庫之類的存放區擷取資料，並在套用修補檔案之後更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="863e0-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="863e0-166">模型狀態</span><span class="sxs-lookup"><span data-stu-id="863e0-166">Model state</span></span>

<span data-ttu-id="863e0-167">上述動作方法範例會呼叫 `ApplyTo` 的多載，以取得模型狀態作為它的其中一個參數。</span><span class="sxs-lookup"><span data-stu-id="863e0-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="863e0-168">使用此選項，您就能在回應中收到錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="863e0-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="863e0-169">下列範例會針對 `test` 作業顯示「400 不正確的要求」回應的本文：</span><span class="sxs-lookup"><span data-stu-id="863e0-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="863e0-170">動態物件</span><span class="sxs-lookup"><span data-stu-id="863e0-170">Dynamic objects</span></span>

<span data-ttu-id="863e0-171">下列動作方法範例顯示如何將修補程式套用至動態物件：</span><span class="sxs-lookup"><span data-stu-id="863e0-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="863e0-172">新增作業</span><span class="sxs-lookup"><span data-stu-id="863e0-172">The add operation</span></span>

* <span data-ttu-id="863e0-173">如果 `path` 指向陣列元素：將新元素插入至 `path` 所指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="863e0-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="863e0-174">如果 `path` 指向屬性：設定屬性值。</span><span class="sxs-lookup"><span data-stu-id="863e0-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="863e0-175">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="863e0-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="863e0-176">如果要修補的資源是動態物件：加入屬性。</span><span class="sxs-lookup"><span data-stu-id="863e0-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="863e0-177">如果要修補的資源是靜態物件：要求失敗。</span><span class="sxs-lookup"><span data-stu-id="863e0-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="863e0-178">下列範例修補文件會設定 `CustomerName` 的值，並將 `Order` 物件加入至 `Orders` 陣列的結尾處。</span><span class="sxs-lookup"><span data-stu-id="863e0-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="863e0-179">移除作業</span><span class="sxs-lookup"><span data-stu-id="863e0-179">The remove operation</span></span>

* <span data-ttu-id="863e0-180">如果 `path` 指向陣列元素：移除該元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="863e0-181">如果 `path` 指向屬性：</span><span class="sxs-lookup"><span data-stu-id="863e0-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="863e0-182">如果要修補的資源是動態物件：移除屬性。</span><span class="sxs-lookup"><span data-stu-id="863e0-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="863e0-183">如果要修補的資源是靜態物件：</span><span class="sxs-lookup"><span data-stu-id="863e0-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="863e0-184">如果屬性可為 Null：將它設定為 Null。</span><span class="sxs-lookup"><span data-stu-id="863e0-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="863e0-185">如果屬性不可為 Null，則將它設定為 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="863e0-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="863e0-186">下列範例修補檔會將設定 `CustomerName` 為 null 和刪除 `Orders[0]` ：</span><span class="sxs-lookup"><span data-stu-id="863e0-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="863e0-187">取代作業</span><span class="sxs-lookup"><span data-stu-id="863e0-187">The replace operation</span></span>

<span data-ttu-id="863e0-188">此作業在功能上與 `remove` 之後接著 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="863e0-189">下列範例修補檔會設定的值 `CustomerName` ，並 `Orders[0]` 以新的物件取代 `Order` ：</span><span class="sxs-lookup"><span data-stu-id="863e0-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="863e0-190">移動作業</span><span class="sxs-lookup"><span data-stu-id="863e0-190">The move operation</span></span>

* <span data-ttu-id="863e0-191">如果 `path` 指向陣列元素：將 `from` 元素複製到 `path` 元素的位置，然後在 `from` 元素上執行 `remove` 作業。</span><span class="sxs-lookup"><span data-stu-id="863e0-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="863e0-192">如果 `path` 指向屬性：將 `from` 屬性的值複製到 `path` 屬性，然後在 `from` 屬性上執行 `remove` 作業。</span><span class="sxs-lookup"><span data-stu-id="863e0-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="863e0-193">如果 `path` 指向不存在的屬性：</span><span class="sxs-lookup"><span data-stu-id="863e0-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="863e0-194">如果要修補的資源是靜態物件：要求失敗。</span><span class="sxs-lookup"><span data-stu-id="863e0-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="863e0-195">如果要修補的資源是動態物件：將 `from` 屬性複製到 `path` 所指出的位置，然後在 `from` 屬性上執行 `remove` 作業。</span><span class="sxs-lookup"><span data-stu-id="863e0-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="863e0-196">下列範例修補文件：</span><span class="sxs-lookup"><span data-stu-id="863e0-196">The following sample patch document:</span></span>

* <span data-ttu-id="863e0-197">將 `Orders[0].OrderName` 的值複製到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="863e0-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="863e0-198">將 `Orders[0].OrderName` 設定為 Null。</span><span class="sxs-lookup"><span data-stu-id="863e0-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="863e0-199">將 `Orders[1]` 移到 `Orders[0]` 前面。</span><span class="sxs-lookup"><span data-stu-id="863e0-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="863e0-200">複製作業</span><span class="sxs-lookup"><span data-stu-id="863e0-200">The copy operation</span></span>

<span data-ttu-id="863e0-201">此作業在功能上與不含最後 `remove` 步驟的 `move` 作業相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="863e0-202">下列範例修補文件：</span><span class="sxs-lookup"><span data-stu-id="863e0-202">The following sample patch document:</span></span>

* <span data-ttu-id="863e0-203">將 `Orders[0].OrderName` 的值複製到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="863e0-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="863e0-204">在 `Orders[0]` 前面插入 `Orders[1]` 的複本。</span><span class="sxs-lookup"><span data-stu-id="863e0-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="863e0-205">測試作業</span><span class="sxs-lookup"><span data-stu-id="863e0-205">The test operation</span></span>

<span data-ttu-id="863e0-206">如果 `path` 所指出位置上的值與 `value` 中所提供的值不同，則要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="863e0-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="863e0-207">在該情況下，整個 PATCH 要求會失敗，即使修補文件中的所有其他作業都成功也一樣。</span><span class="sxs-lookup"><span data-stu-id="863e0-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="863e0-208">`test` 作業通常會用來防止在發生並行衝突時進行更新。</span><span class="sxs-lookup"><span data-stu-id="863e0-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="863e0-209">如果 `CustomerName` 的初始值是 "John"，則下列範例修補文件不會有任何作用，因為測試失敗：</span><span class="sxs-lookup"><span data-stu-id="863e0-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="863e0-210">取得程式碼</span><span class="sxs-lookup"><span data-stu-id="863e0-210">Get the code</span></span>

<span data-ttu-id="863e0-211">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples)。</span><span class="sxs-lookup"><span data-stu-id="863e0-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="863e0-212">([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="863e0-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="863e0-213">若要測試範例，請執行應用程式，並使用下列設定來傳送 HTTP 要求：</span><span class="sxs-lookup"><span data-stu-id="863e0-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="863e0-214">URL： `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="863e0-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="863e0-215">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="863e0-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="863e0-216">標題：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="863e0-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="863e0-217">主體：從 *json* 專案資料夾複製並貼上其中一個 json 修補檔範例。</span><span class="sxs-lookup"><span data-stu-id="863e0-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="863e0-218">其他資源</span><span class="sxs-lookup"><span data-stu-id="863e0-218">Additional resources</span></span>

* <span data-ttu-id="863e0-219">[IETF RFC 5789 PATCH 方法規格](https://tools.ietf.org/html/rfc5789) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="863e0-219">[IETF RFC 5789 PATCH method specification](https://tools.ietf.org/html/rfc5789)</span></span>
* <span data-ttu-id="863e0-220">[IETF RFC 6902 JSON Patch 規格](https://tools.ietf.org/html/rfc6902) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="863e0-220">[IETF RFC 6902 JSON Patch specification](https://tools.ietf.org/html/rfc6902)</span></span>
* <span data-ttu-id="863e0-221">[IETF RFC 6901 JSON Patch 路徑格式規格](https://tools.ietf.org/html/rfc6901) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="863e0-221">[IETF RFC 6901 JSON Patch path format spec](https://tools.ietf.org/html/rfc6901)</span></span>
* <span data-ttu-id="863e0-222">[JSON Patch 文件](https://jsonpatch.com/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="863e0-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="863e0-223">包含用於建立 JSON Patch 文件的資源連結。</span><span class="sxs-lookup"><span data-stu-id="863e0-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="863e0-224">ASP.NET Core JSON Patch 原始程式碼</span><span class="sxs-lookup"><span data-stu-id="863e0-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="863e0-225">本文說明如何處理 ASP.NET Core Web API 中的 JSON Patch 要求。</span><span class="sxs-lookup"><span data-stu-id="863e0-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="863e0-226">PATCH HTTP 要求方法</span><span class="sxs-lookup"><span data-stu-id="863e0-226">PATCH HTTP request method</span></span>

<span data-ttu-id="863e0-227">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) \(英文\) 方法均用來更新現有的資源。</span><span class="sxs-lookup"><span data-stu-id="863e0-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="863e0-228">它們之間的差異是 PUT 會取代整個資源，而 PATCH 只會指定變更。</span><span class="sxs-lookup"><span data-stu-id="863e0-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="863e0-229">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="863e0-229">JSON Patch</span></span>

<span data-ttu-id="863e0-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) \(英文\) 是一種格式，可用來指定要套用至資源的更新。</span><span class="sxs-lookup"><span data-stu-id="863e0-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="863e0-231">JSON Patch 文件具有一個「作業」陣列。</span><span class="sxs-lookup"><span data-stu-id="863e0-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="863e0-232">每個作業都會識別特定類型的變更，例如，加入陣列元素或取代屬性值。</span><span class="sxs-lookup"><span data-stu-id="863e0-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="863e0-233">例如，下列 JSON 文件代表一個資源、一份適用於該資源的 JSON 修補文件，以及套用修補作業的結果。</span><span class="sxs-lookup"><span data-stu-id="863e0-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="863e0-234">資源範例</span><span class="sxs-lookup"><span data-stu-id="863e0-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="863e0-235">JSON 修補範例</span><span class="sxs-lookup"><span data-stu-id="863e0-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="863e0-236">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="863e0-236">In the preceding JSON:</span></span>

* <span data-ttu-id="863e0-237">`op` 屬性會指出作業的類型。</span><span class="sxs-lookup"><span data-stu-id="863e0-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="863e0-238">`path` 屬性會指出要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="863e0-239">`value` 屬性會提供新值。</span><span class="sxs-lookup"><span data-stu-id="863e0-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="863e0-240">修補之後的資源</span><span class="sxs-lookup"><span data-stu-id="863e0-240">Resource after patch</span></span>

<span data-ttu-id="863e0-241">以下是套用上述 JSON Patch 文件之後的資源：</span><span class="sxs-lookup"><span data-stu-id="863e0-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="863e0-242">藉由將 JSON Patch 文件套用至資源所做的變更是不可部分完成的：如果清單中有任何作業失敗，就不會套用清單中的任何作業。</span><span class="sxs-lookup"><span data-stu-id="863e0-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="863e0-243">路徑語法</span><span class="sxs-lookup"><span data-stu-id="863e0-243">Path syntax</span></span>

<span data-ttu-id="863e0-244">作業物件的 [path](https://tools.ietf.org/html/rfc6901) \(英文\) 屬性在層級之間有斜線。</span><span class="sxs-lookup"><span data-stu-id="863e0-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="863e0-245">例如： `"/address/zipCode"` 。</span><span class="sxs-lookup"><span data-stu-id="863e0-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="863e0-246">以零為起始的索引可用來指定陣列元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="863e0-247">`addresses` 陣列的第一個元素會在 `/addresses/0` 上。</span><span class="sxs-lookup"><span data-stu-id="863e0-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="863e0-248">若要 `add` 到陣列結尾處，請使用連字號 (-) 而不是索引號碼：`/addresses/-`。</span><span class="sxs-lookup"><span data-stu-id="863e0-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="863e0-249">Operations</span><span class="sxs-lookup"><span data-stu-id="863e0-249">Operations</span></span>

<span data-ttu-id="863e0-250">下表顯示支援的作業，如 [JSON Patch 規格](https://tools.ietf.org/html/rfc6902) \(英文\) 中所定義：</span><span class="sxs-lookup"><span data-stu-id="863e0-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="863e0-251">作業</span><span class="sxs-lookup"><span data-stu-id="863e0-251">Operation</span></span>  | <span data-ttu-id="863e0-252">備註</span><span class="sxs-lookup"><span data-stu-id="863e0-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="863e0-253">加入屬性或陣列元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-253">Add a property or array element.</span></span> <span data-ttu-id="863e0-254">針對現有的屬性：設定值。</span><span class="sxs-lookup"><span data-stu-id="863e0-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="863e0-255">移除屬性或陣列元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="863e0-256">與 `remove` 之後接著在同一個位置上 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="863e0-257">與從來源 `remove` 之後接著使用來源的值 `add` 到目的地相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="863e0-258">與使用來源的值 `add` 到目的地相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="863e0-259">如果 `path` 上的值 = 所提供的 `value`，即會傳回成功狀態碼。</span><span class="sxs-lookup"><span data-stu-id="863e0-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="863e0-260">ASP.NET Core 中的 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="863e0-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="863e0-261">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) \(英文\) NuGet 套件中會提供 JSON Patch 的 ASP.NET Core 實作。</span><span class="sxs-lookup"><span data-stu-id="863e0-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="863e0-262">此套件隨附於 [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) 中繼套件中。</span><span class="sxs-lookup"><span data-stu-id="863e0-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="863e0-263">動作方法程式碼</span><span class="sxs-lookup"><span data-stu-id="863e0-263">Action method code</span></span>

<span data-ttu-id="863e0-264">在 API 控制器中，JSON Patch 的動作方法：</span><span class="sxs-lookup"><span data-stu-id="863e0-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="863e0-265">使用 `HttpPatch` 屬性來標註。</span><span class="sxs-lookup"><span data-stu-id="863e0-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="863e0-266">接受 `JsonPatchDocument<T>` ，通常是 `[FromBody]` 。</span><span class="sxs-lookup"><span data-stu-id="863e0-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="863e0-267">呼叫修補文件上的 `ApplyTo` 以套用變更。</span><span class="sxs-lookup"><span data-stu-id="863e0-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="863e0-268">以下為範例：</span><span class="sxs-lookup"><span data-stu-id="863e0-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="863e0-269">這段來自範例應用程式的程式碼會使用下列 `Customer` 模型。</span><span class="sxs-lookup"><span data-stu-id="863e0-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="863e0-270">範例動作方法：</span><span class="sxs-lookup"><span data-stu-id="863e0-270">The sample action method:</span></span>

* <span data-ttu-id="863e0-271">建構 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="863e0-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="863e0-272">套用修補檔案。</span><span class="sxs-lookup"><span data-stu-id="863e0-272">Applies the patch.</span></span>
* <span data-ttu-id="863e0-273">在回應本文中傳回結果。</span><span class="sxs-lookup"><span data-stu-id="863e0-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="863e0-274">在實際的應用程式中，程式碼會從資料庫之類的存放區擷取資料，並在套用修補檔案之後更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="863e0-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="863e0-275">模型狀態</span><span class="sxs-lookup"><span data-stu-id="863e0-275">Model state</span></span>

<span data-ttu-id="863e0-276">上述動作方法範例會呼叫 `ApplyTo` 的多載，以取得模型狀態作為它的其中一個參數。</span><span class="sxs-lookup"><span data-stu-id="863e0-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="863e0-277">使用此選項，您就能在回應中收到錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="863e0-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="863e0-278">下列範例會針對 `test` 作業顯示「400 不正確的要求」回應的本文：</span><span class="sxs-lookup"><span data-stu-id="863e0-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="863e0-279">動態物件</span><span class="sxs-lookup"><span data-stu-id="863e0-279">Dynamic objects</span></span>

<span data-ttu-id="863e0-280">下列動作方法範例示範如何將修補檔案套用至動態物件。</span><span class="sxs-lookup"><span data-stu-id="863e0-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="863e0-281">新增作業</span><span class="sxs-lookup"><span data-stu-id="863e0-281">The add operation</span></span>

* <span data-ttu-id="863e0-282">如果 `path` 指向陣列元素：將新元素插入至 `path` 所指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="863e0-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="863e0-283">如果 `path` 指向屬性：設定屬性值。</span><span class="sxs-lookup"><span data-stu-id="863e0-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="863e0-284">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="863e0-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="863e0-285">如果要修補的資源是動態物件：加入屬性。</span><span class="sxs-lookup"><span data-stu-id="863e0-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="863e0-286">如果要修補的資源是靜態物件：要求失敗。</span><span class="sxs-lookup"><span data-stu-id="863e0-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="863e0-287">下列範例修補文件會設定 `CustomerName` 的值，並將 `Order` 物件加入至 `Orders` 陣列的結尾處。</span><span class="sxs-lookup"><span data-stu-id="863e0-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="863e0-288">移除作業</span><span class="sxs-lookup"><span data-stu-id="863e0-288">The remove operation</span></span>

* <span data-ttu-id="863e0-289">如果 `path` 指向陣列元素：移除該元素。</span><span class="sxs-lookup"><span data-stu-id="863e0-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="863e0-290">如果 `path` 指向屬性：</span><span class="sxs-lookup"><span data-stu-id="863e0-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="863e0-291">如果要修補的資源是動態物件：移除屬性。</span><span class="sxs-lookup"><span data-stu-id="863e0-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="863e0-292">如果要修補的資源是靜態物件：</span><span class="sxs-lookup"><span data-stu-id="863e0-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="863e0-293">如果屬性可為 Null：將它設定為 Null。</span><span class="sxs-lookup"><span data-stu-id="863e0-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="863e0-294">如果屬性不可為 Null，則將它設定為 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="863e0-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="863e0-295">下列範例修補文件會將 `CustomerName` 設定為 Null 並刪除 `Orders[0]`。</span><span class="sxs-lookup"><span data-stu-id="863e0-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="863e0-296">取代作業</span><span class="sxs-lookup"><span data-stu-id="863e0-296">The replace operation</span></span>

<span data-ttu-id="863e0-297">此作業在功能上與 `remove` 之後接著 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="863e0-298">下列範例修補文件會設定 `CustomerName` 的值，並使用新的 `Order` 物件來取代 `Orders[0]`。</span><span class="sxs-lookup"><span data-stu-id="863e0-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="863e0-299">移動作業</span><span class="sxs-lookup"><span data-stu-id="863e0-299">The move operation</span></span>

* <span data-ttu-id="863e0-300">如果 `path` 指向陣列元素：將 `from` 元素複製到 `path` 元素的位置，然後在 `from` 元素上執行 `remove` 作業。</span><span class="sxs-lookup"><span data-stu-id="863e0-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="863e0-301">如果 `path` 指向屬性：將 `from` 屬性的值複製到 `path` 屬性，然後在 `from` 屬性上執行 `remove` 作業。</span><span class="sxs-lookup"><span data-stu-id="863e0-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="863e0-302">如果 `path` 指向不存在的屬性：</span><span class="sxs-lookup"><span data-stu-id="863e0-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="863e0-303">如果要修補的資源是靜態物件：要求失敗。</span><span class="sxs-lookup"><span data-stu-id="863e0-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="863e0-304">如果要修補的資源是動態物件：將 `from` 屬性複製到 `path` 所指出的位置，然後在 `from` 屬性上執行 `remove` 作業。</span><span class="sxs-lookup"><span data-stu-id="863e0-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="863e0-305">下列範例修補文件：</span><span class="sxs-lookup"><span data-stu-id="863e0-305">The following sample patch document:</span></span>

* <span data-ttu-id="863e0-306">將 `Orders[0].OrderName` 的值複製到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="863e0-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="863e0-307">將 `Orders[0].OrderName` 設定為 Null。</span><span class="sxs-lookup"><span data-stu-id="863e0-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="863e0-308">將 `Orders[1]` 移到 `Orders[0]` 前面。</span><span class="sxs-lookup"><span data-stu-id="863e0-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="863e0-309">複製作業</span><span class="sxs-lookup"><span data-stu-id="863e0-309">The copy operation</span></span>

<span data-ttu-id="863e0-310">此作業在功能上與不含最後 `remove` 步驟的 `move` 作業相同。</span><span class="sxs-lookup"><span data-stu-id="863e0-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="863e0-311">下列範例修補文件：</span><span class="sxs-lookup"><span data-stu-id="863e0-311">The following sample patch document:</span></span>

* <span data-ttu-id="863e0-312">將 `Orders[0].OrderName` 的值複製到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="863e0-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="863e0-313">在 `Orders[0]` 前面插入 `Orders[1]` 的複本。</span><span class="sxs-lookup"><span data-stu-id="863e0-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="863e0-314">測試作業</span><span class="sxs-lookup"><span data-stu-id="863e0-314">The test operation</span></span>

<span data-ttu-id="863e0-315">如果 `path` 所指出位置上的值與 `value` 中所提供的值不同，則要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="863e0-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="863e0-316">在該情況下，整個 PATCH 要求會失敗，即使修補文件中的所有其他作業都成功也一樣。</span><span class="sxs-lookup"><span data-stu-id="863e0-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="863e0-317">`test` 作業通常會用來防止在發生並行衝突時進行更新。</span><span class="sxs-lookup"><span data-stu-id="863e0-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="863e0-318">如果 `CustomerName` 的初始值是 "John"，則下列範例修補文件不會有任何作用，因為測試失敗：</span><span class="sxs-lookup"><span data-stu-id="863e0-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="863e0-319">取得程式碼</span><span class="sxs-lookup"><span data-stu-id="863e0-319">Get the code</span></span>

<span data-ttu-id="863e0-320">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2)。</span><span class="sxs-lookup"><span data-stu-id="863e0-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="863e0-321">([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="863e0-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="863e0-322">若要測試範例，請執行應用程式，並使用下列設定來傳送 HTTP 要求：</span><span class="sxs-lookup"><span data-stu-id="863e0-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="863e0-323">URL： `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="863e0-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="863e0-324">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="863e0-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="863e0-325">標題：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="863e0-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="863e0-326">主體：從 *json* 專案資料夾複製並貼上其中一個 json 修補檔範例。</span><span class="sxs-lookup"><span data-stu-id="863e0-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="863e0-327">其他資源</span><span class="sxs-lookup"><span data-stu-id="863e0-327">Additional resources</span></span>

* <span data-ttu-id="863e0-328">[IETF RFC 5789 PATCH 方法規格](https://tools.ietf.org/html/rfc5789) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="863e0-328">[IETF RFC 5789 PATCH method specification](https://tools.ietf.org/html/rfc5789)</span></span>
* <span data-ttu-id="863e0-329">[IETF RFC 6902 JSON Patch 規格](https://tools.ietf.org/html/rfc6902) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="863e0-329">[IETF RFC 6902 JSON Patch specification](https://tools.ietf.org/html/rfc6902)</span></span>
* <span data-ttu-id="863e0-330">[IETF RFC 6901 JSON Patch 路徑格式規格](https://tools.ietf.org/html/rfc6901) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="863e0-330">[IETF RFC 6901 JSON Patch path format spec](https://tools.ietf.org/html/rfc6901)</span></span>
* <span data-ttu-id="863e0-331">[JSON Patch 文件](https://jsonpatch.com/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="863e0-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="863e0-332">包含用於建立 JSON Patch 文件的資源連結。</span><span class="sxs-lookup"><span data-stu-id="863e0-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="863e0-333">ASP.NET Core JSON Patch 原始程式碼</span><span class="sxs-lookup"><span data-stu-id="863e0-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end
