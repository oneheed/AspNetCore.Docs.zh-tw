---
title: ASP.NET Core 中的資料繫結
author: rick-anderson
description: 了解 ASP.NET Core 中的模型繫結如何運作，以及如何自訂其行為。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 4de34a75da932b41190caa8434ac5be8cc0710fd
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981930"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="0348c-103">ASP.NET Core 中的資料繫結</span><span class="sxs-lookup"><span data-stu-id="0348c-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0348c-104">本文會說明何謂模型繫結、其運作方式，以及如何自訂其行為。</span><span class="sxs-lookup"><span data-stu-id="0348c-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="0348c-105">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0348c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="0348c-106">何謂模型繫結</span><span class="sxs-lookup"><span data-stu-id="0348c-106">What is Model binding</span></span>

<span data-ttu-id="0348c-107">控制器和 Razor 頁面會處理來自 HTTP 要求的資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="0348c-108">例如，路由資料可能會提供記錄索引鍵，而已張貼的表單欄位可能會提供模型屬性的值。</span><span class="sxs-lookup"><span data-stu-id="0348c-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="0348c-109">撰寫程式碼來擷取這些值的每一個並將它們從字串轉換成 .NET 類型，不但繁瑣又容易發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="0348c-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="0348c-110">模型繫結會自動化此程序。</span><span class="sxs-lookup"><span data-stu-id="0348c-110">Model binding automates this process.</span></span> <span data-ttu-id="0348c-111">模型繫結系統：</span><span class="sxs-lookup"><span data-stu-id="0348c-111">The model binding system:</span></span>

* <span data-ttu-id="0348c-112">從各種來源（例如，路由資料、表單欄位和查詢字串）抓取資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="0348c-113">提供資料給 Razor 方法參數和公用屬性中的控制器和頁面。</span><span class="sxs-lookup"><span data-stu-id="0348c-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="0348c-114">將字串資料轉換成 .NET 類型。</span><span class="sxs-lookup"><span data-stu-id="0348c-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="0348c-115">更新複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="0348c-116">範例</span><span class="sxs-lookup"><span data-stu-id="0348c-116">Example</span></span>

<span data-ttu-id="0348c-117">假設您有下列動作方法：</span><span class="sxs-lookup"><span data-stu-id="0348c-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="0348c-118">而應用程式收到具有此 URL 的要求：</span><span class="sxs-lookup"><span data-stu-id="0348c-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="0348c-119">在路由系統選取動作方法之後，模型系結會經歷下列步驟：</span><span class="sxs-lookup"><span data-stu-id="0348c-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="0348c-120">尋找第一個參數 `GetByID`，它是名為 `id` 的整數。</span><span class="sxs-lookup"><span data-stu-id="0348c-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="0348c-121">查看 HTTP 要求中所有可用的來源，在路由資料中找到 `id` = "2"。</span><span class="sxs-lookup"><span data-stu-id="0348c-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="0348c-122">將字串 "2" 轉換成整數 2。</span><span class="sxs-lookup"><span data-stu-id="0348c-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="0348c-123">尋找的下一個參數 `GetByID` ，也就是名為的布林值 `dogsOnly` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="0348c-124">查看來源，在查詢字串中找到 "DogsOnly=true"。</span><span class="sxs-lookup"><span data-stu-id="0348c-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="0348c-125">名稱比對不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="0348c-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="0348c-126">將字串 "true" 轉換成布林值 `true` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="0348c-127">架構接著會呼叫 `GetById` 方法，針對 `id` 參數傳送 2、`dogsOnly` 參數傳送 `true`。</span><span class="sxs-lookup"><span data-stu-id="0348c-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="0348c-128">在上例中，模型繫結目標都是簡單型別的方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="0348c-129">目標也可以是複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="0348c-130">成功系結每個屬性之後，就會針對該屬性進行 [模型驗證](xref:mvc/models/validation) 。</span><span class="sxs-lookup"><span data-stu-id="0348c-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="0348c-131">哪些資料繫結至模型，以及任何繫結或驗證錯誤的記錄，都會儲存在 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 或 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)。</span><span class="sxs-lookup"><span data-stu-id="0348c-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="0348c-132">為了解此程序是否成功，應用程式會檢查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 旗標。</span><span class="sxs-lookup"><span data-stu-id="0348c-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="0348c-133">目標</span><span class="sxs-lookup"><span data-stu-id="0348c-133">Targets</span></span>

<span data-ttu-id="0348c-134">模型繫結會嘗試尋找下列幾種目標的值：</span><span class="sxs-lookup"><span data-stu-id="0348c-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="0348c-135">要求路由目標的控制器動作方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="0348c-136">Razor傳送要求的頁面處理常式方法的參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="0348c-137">控制站的公用屬性或 `PageModel` 類別，如由屬性指定。</span><span class="sxs-lookup"><span data-stu-id="0348c-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="0348c-138">[BindProperty] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-138">[BindProperty] attribute</span></span>

<span data-ttu-id="0348c-139">可以套用至控制器或類別的公用屬性 `PageModel` ，使模型系結成為該屬性的目標：</span><span class="sxs-lookup"><span data-stu-id="0348c-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="0348c-140">[BindProperties] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-140">[BindProperties] attribute</span></span>

<span data-ttu-id="0348c-141">可在 ASP.NET Core 2.1 和更新版本中使用。</span><span class="sxs-lookup"><span data-stu-id="0348c-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="0348c-142">可以套用至控制器或 `PageModel` 類別，以告知模型系結將目標設為類別的所有公用屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="0348c-143">適用於 HTTP GET 要求的模型繫結</span><span class="sxs-lookup"><span data-stu-id="0348c-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="0348c-144">根據預設，屬性不會針對 HTTP GET 要求繫結。</span><span class="sxs-lookup"><span data-stu-id="0348c-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="0348c-145">一般而言，GET 要求只需要記錄識別碼參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="0348c-146">此記錄識別碼用來查詢資料庫中的項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="0348c-147">因此，不需要系結保存模型實例的屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="0348c-148">在您要從 GET 要求將屬性繫結至資料的案例中，請將 `SupportsGet` 屬性設為 `true`：</span><span class="sxs-lookup"><span data-stu-id="0348c-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="0348c-149">來源</span><span class="sxs-lookup"><span data-stu-id="0348c-149">Sources</span></span>

<span data-ttu-id="0348c-150">根據預設，模型繫結會從下列 HTTP 要求的來源中，取得索引鍵/值組形式的資料：</span><span class="sxs-lookup"><span data-stu-id="0348c-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="0348c-151">表單欄位</span><span class="sxs-lookup"><span data-stu-id="0348c-151">Form fields</span></span>
1. <span data-ttu-id="0348c-152">[具有 [ApiController] 屬性之控制器](xref:web-api/index#binding-source-parameter-inference)的要求主體 (。 ) </span><span class="sxs-lookup"><span data-stu-id="0348c-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="0348c-153">路由傳送資料</span><span class="sxs-lookup"><span data-stu-id="0348c-153">Route data</span></span>
1. <span data-ttu-id="0348c-154">查詢字串參數</span><span class="sxs-lookup"><span data-stu-id="0348c-154">Query string parameters</span></span>
1. <span data-ttu-id="0348c-155">已上傳的檔案</span><span class="sxs-lookup"><span data-stu-id="0348c-155">Uploaded files</span></span>

<span data-ttu-id="0348c-156">針對每個目標參數或屬性，系統會依照上述清單中指出的順序掃描來源。</span><span class="sxs-lookup"><span data-stu-id="0348c-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="0348c-157">但也有一些例外：</span><span class="sxs-lookup"><span data-stu-id="0348c-157">There are a few exceptions:</span></span>

* <span data-ttu-id="0348c-158">路由資料和查詢字串值只用於簡單型別。</span><span class="sxs-lookup"><span data-stu-id="0348c-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="0348c-159">上傳的檔案只會系結至執行或的目標型別 `IFormFile` `IEnumerable<IFormFile>` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="0348c-160">如果預設來源不正確，請使用下列其中一個屬性來指定來源：</span><span class="sxs-lookup"><span data-stu-id="0348c-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="0348c-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -取得查詢字串中的值。</span><span class="sxs-lookup"><span data-stu-id="0348c-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="0348c-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -從路由資料取得值。</span><span class="sxs-lookup"><span data-stu-id="0348c-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="0348c-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -從張貼的表單欄位取得值。</span><span class="sxs-lookup"><span data-stu-id="0348c-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="0348c-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -從要求主體取得值。</span><span class="sxs-lookup"><span data-stu-id="0348c-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="0348c-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -取得 HTTP 標頭的值。</span><span class="sxs-lookup"><span data-stu-id="0348c-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="0348c-166">這些屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-166">These attributes:</span></span>

* <span data-ttu-id="0348c-167">會個別新增至模型屬性 (不是模型類別)，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="0348c-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="0348c-168">（選擇性）在函式中接受模型名稱值。</span><span class="sxs-lookup"><span data-stu-id="0348c-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="0348c-169">如果屬性名稱與要求中的值不符，就會提供這個選項。</span><span class="sxs-lookup"><span data-stu-id="0348c-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="0348c-170">例如，要求中的值可能是名稱中有連字號的標頭，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="0348c-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="0348c-171">[FromBody] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-171">[FromBody] attribute</span></span>

<span data-ttu-id="0348c-172">將 `[FromBody]` 屬性套用至參數，以從 HTTP 要求的主體填入其屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="0348c-173">ASP.NET Core 執行時間會將讀取主體的責任委派給輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="0348c-174">[本文稍後](#input-formatters)會說明輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="0348c-175">當套用 `[FromBody]` 至複雜型別參數時，會忽略套用至其屬性的任何系結來源屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="0348c-176">例如，下列 `Create` 動作會指定其 `pet` 參數已從主體填入：</span><span class="sxs-lookup"><span data-stu-id="0348c-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="0348c-177">`Pet`類別 `Breed` 會指定從查詢字串參數填入其屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="0348c-178">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="0348c-178">In the preceding example:</span></span>

* <span data-ttu-id="0348c-179">`[FromQuery]`忽略屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="0348c-180">`Breed`屬性不會從查詢字串參數填入。</span><span class="sxs-lookup"><span data-stu-id="0348c-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="0348c-181">輸入格式器只會讀取本文，而不會瞭解系結來源屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="0348c-182">如果在主體中找到適當的值，則會使用該值來填入 `Breed` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="0348c-183">請勿套用 `[FromBody]` 至每個動作方法的一個以上參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="0348c-184">一旦輸入格式器讀取要求資料流程之後，就無法再讀取它來系結其他 `[FromBody]` 參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="0348c-185">其他來源</span><span class="sxs-lookup"><span data-stu-id="0348c-185">Additional sources</span></span>

<span data-ttu-id="0348c-186">*值提供* 者會將來源資料提供給模型系結系統。</span><span class="sxs-lookup"><span data-stu-id="0348c-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="0348c-187">您可以撰寫並註冊自訂值提供者，其從其他來源取得模型繫結資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="0348c-188">例如，您可能會想要來自 cookie s 或會話狀態的資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="0348c-189">若要從新的來源取得資料：</span><span class="sxs-lookup"><span data-stu-id="0348c-189">To get data from a new source:</span></span>

* <span data-ttu-id="0348c-190">建立會實作 `IValueProvider` 的類別。</span><span class="sxs-lookup"><span data-stu-id="0348c-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="0348c-191">建立會實作 `IValueProviderFactory` 的類別。</span><span class="sxs-lookup"><span data-stu-id="0348c-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="0348c-192">在 `Startup.ConfigureServices` 中註冊 Factory 類別。</span><span class="sxs-lookup"><span data-stu-id="0348c-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="0348c-193">範例應用程式包含 [值提供者](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) 和 [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) 範例，可取得 s 的值 cookie 。</span><span class="sxs-lookup"><span data-stu-id="0348c-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="0348c-194">以下是 `Startup.ConfigureServices` 中的註冊碼：</span><span class="sxs-lookup"><span data-stu-id="0348c-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="0348c-195">顯示的程式碼會將自訂值提供者放在所有內建值提供者的後面。</span><span class="sxs-lookup"><span data-stu-id="0348c-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="0348c-196">若要讓它成為清單中的第一個，請呼叫， `Insert(0, new CookieValueProviderFactory())` 而不是 `Add` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="0348c-197">無模型屬性的來源</span><span class="sxs-lookup"><span data-stu-id="0348c-197">No source for a model property</span></span>

<span data-ttu-id="0348c-198">依預設，如果找不到模型屬性的值，則不會建立模型狀態錯誤。</span><span class="sxs-lookup"><span data-stu-id="0348c-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="0348c-199">屬性設定為 null 或預設值：</span><span class="sxs-lookup"><span data-stu-id="0348c-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="0348c-200">可為 null 的簡單類型會設定為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="0348c-201">不可為 Null 的實值型別會設為 `default(T)`。</span><span class="sxs-lookup"><span data-stu-id="0348c-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="0348c-202">例如，參數 `int id` 設為 0。</span><span class="sxs-lookup"><span data-stu-id="0348c-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="0348c-203">針對複雜類型，模型系結會使用預設的函式來建立實例，而不需要設定屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="0348c-204">陣列設為 `Array.Empty<T>()`，但 `byte[]` 陣列設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="0348c-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="0348c-205">如果模型狀態在模型屬性的表單欄位中找不到任何專案時，應該會失效，請使用 [`[BindRequired]`](#bindrequired-attribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="0348c-206">請注意，此 `[BindRequired]` 行為適用於來自已張貼表單資料的模型繫結，不適合要求本文中的 JSON 或 XML 資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="0348c-207">要求主體資料是由 [輸入](#input-formatters)格式器處理。</span><span class="sxs-lookup"><span data-stu-id="0348c-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="0348c-208">類型轉換錯誤</span><span class="sxs-lookup"><span data-stu-id="0348c-208">Type conversion errors</span></span>

<span data-ttu-id="0348c-209">如果找到來源但無法轉換成目標型別，則會將模型狀態標示為無效。</span><span class="sxs-lookup"><span data-stu-id="0348c-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="0348c-210">目標參數或屬性會設為 null 或預設值，如上一節中所述。</span><span class="sxs-lookup"><span data-stu-id="0348c-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="0348c-211">在具有 `[ApiController]` 屬性的 API 控制器中，無效的模型狀態會導致自動 HTTP 400 回應。</span><span class="sxs-lookup"><span data-stu-id="0348c-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="0348c-212">在 Razor 頁面中，重新顯示包含錯誤訊息的頁面：</span><span class="sxs-lookup"><span data-stu-id="0348c-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="0348c-213">用戶端驗證會攔截將會提交至頁面表單的最不正確資料 Razor 。</span><span class="sxs-lookup"><span data-stu-id="0348c-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="0348c-214">此驗證會讓您更難觸發上述醒目提示的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0348c-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="0348c-215">範例應用程式包含 [ **提交** 日期] 不正確 [提交日期] 按鈕，會將錯誤的資料放入 [ **雇用日期** ] 欄位並提交表單。</span><span class="sxs-lookup"><span data-stu-id="0348c-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="0348c-216">此按鈕會顯示當資料轉換錯誤發生時，重新顯示頁面的程式碼如何運作。</span><span class="sxs-lookup"><span data-stu-id="0348c-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="0348c-217">當先前的程式碼重新顯示頁面時，表單欄位中不會顯示不正確輸入。</span><span class="sxs-lookup"><span data-stu-id="0348c-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="0348c-218">這是因為模型屬性已設為 null 或預設值。</span><span class="sxs-lookup"><span data-stu-id="0348c-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="0348c-219">無效的輸入確實會出現在錯誤訊息中。</span><span class="sxs-lookup"><span data-stu-id="0348c-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="0348c-220">但是，如果您想要在表單欄位中重新顯示不正確的資料，請考慮讓模型屬性成為字串，以手動方式執行資料轉換。</span><span class="sxs-lookup"><span data-stu-id="0348c-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="0348c-221">如果您不想要類型轉換錯誤導致模型狀態錯誤，則建議使用相同的策略。</span><span class="sxs-lookup"><span data-stu-id="0348c-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="0348c-222">在這種情況下，請將 model 屬性設為字串。</span><span class="sxs-lookup"><span data-stu-id="0348c-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="0348c-223">簡單型別</span><span class="sxs-lookup"><span data-stu-id="0348c-223">Simple types</span></span>

<span data-ttu-id="0348c-224">模型繫結器可將來源字串轉換成的簡單型別包括：</span><span class="sxs-lookup"><span data-stu-id="0348c-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="0348c-225">布林值</span><span class="sxs-lookup"><span data-stu-id="0348c-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="0348c-226">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="0348c-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="0348c-227">字元</span><span class="sxs-lookup"><span data-stu-id="0348c-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="0348c-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="0348c-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="0348c-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="0348c-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="0348c-230">十進位</span><span class="sxs-lookup"><span data-stu-id="0348c-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="0348c-231">Double</span><span class="sxs-lookup"><span data-stu-id="0348c-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="0348c-232">枚舉</span><span class="sxs-lookup"><span data-stu-id="0348c-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="0348c-233">Guid</span><span class="sxs-lookup"><span data-stu-id="0348c-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="0348c-234">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="0348c-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="0348c-235">Single</span><span class="sxs-lookup"><span data-stu-id="0348c-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="0348c-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="0348c-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="0348c-237">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="0348c-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="0348c-238">Uri</span><span class="sxs-lookup"><span data-stu-id="0348c-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="0348c-239">版本</span><span class="sxs-lookup"><span data-stu-id="0348c-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="0348c-240">複雜類型</span><span class="sxs-lookup"><span data-stu-id="0348c-240">Complex types</span></span>

<span data-ttu-id="0348c-241">複雜型別必須具有公用預設的函式和可系結的公用可寫入屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="0348c-242">發生模型繫結時，類別會使用公用預設建構函式具現化。</span><span class="sxs-lookup"><span data-stu-id="0348c-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="0348c-243">針對複雜類型的每個屬性，模型繫結會查看名稱模式 *prefix.property_name* 的來源。</span><span class="sxs-lookup"><span data-stu-id="0348c-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="0348c-244">如果找不到，它會只尋找沒有前置詞的 *property_name*。</span><span class="sxs-lookup"><span data-stu-id="0348c-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="0348c-245">若要繫結至參數，則前置詞是參數名稱。</span><span class="sxs-lookup"><span data-stu-id="0348c-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="0348c-246">若要繫結至 `PageModel` 公用屬性，則前置詞為公用屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="0348c-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="0348c-247">某些屬性 (Attribute) 具有 `Prefix` 屬性 (Property)，其可讓您覆寫參數或屬性名稱的預設使用方法。</span><span class="sxs-lookup"><span data-stu-id="0348c-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="0348c-248">例如，假設複雜類型是下列 `Instructor` 類別：</span><span class="sxs-lookup"><span data-stu-id="0348c-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="0348c-249">前置詞 = 參數名稱</span><span class="sxs-lookup"><span data-stu-id="0348c-249">Prefix = parameter name</span></span>

<span data-ttu-id="0348c-250">如果要繫結的模型是名為 `instructorToUpdate` 的參數：</span><span class="sxs-lookup"><span data-stu-id="0348c-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="0348c-251">模型繫結從查看索引鍵 `instructorToUpdate.ID` 的來源開始。</span><span class="sxs-lookup"><span data-stu-id="0348c-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="0348c-252">如果找不到，則會尋找 `ID` 沒有前置詞的。</span><span class="sxs-lookup"><span data-stu-id="0348c-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="0348c-253">前置詞 = 屬性名稱</span><span class="sxs-lookup"><span data-stu-id="0348c-253">Prefix = property name</span></span>

<span data-ttu-id="0348c-254">如果要繫結的模型是控制器或 `PageModel` 類別名為 `Instructor` 的屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="0348c-255">模型繫結從查看索引鍵 `Instructor.ID` 的來源開始。</span><span class="sxs-lookup"><span data-stu-id="0348c-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="0348c-256">如果找不到，則會尋找 `ID` 沒有前置詞的。</span><span class="sxs-lookup"><span data-stu-id="0348c-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="0348c-257">自訂前置詞</span><span class="sxs-lookup"><span data-stu-id="0348c-257">Custom prefix</span></span>

<span data-ttu-id="0348c-258">如果要繫結的模型是名為 `instructorToUpdate` 的參數，且 `Bind` 屬性指定 `Instructor` 為前置詞：</span><span class="sxs-lookup"><span data-stu-id="0348c-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="0348c-259">模型繫結從查看索引鍵 `Instructor.ID` 的來源開始。</span><span class="sxs-lookup"><span data-stu-id="0348c-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="0348c-260">如果找不到，則會尋找 `ID` 沒有前置詞的。</span><span class="sxs-lookup"><span data-stu-id="0348c-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="0348c-261">複雜類型目標的屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-261">Attributes for complex type targets</span></span>

<span data-ttu-id="0348c-262">有數個內建屬性可用來控制複雜類型的模型系結：</span><span class="sxs-lookup"><span data-stu-id="0348c-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="0348c-263">當張貼的表單資料為值來源時，這些屬性會影響模型繫結。</span><span class="sxs-lookup"><span data-stu-id="0348c-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="0348c-264">它們 **會影響** 輸入格式子，以處理張貼的 JSON 和 XML 要求主體。</span><span class="sxs-lookup"><span data-stu-id="0348c-264">They do \***not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="0348c-265">[本文稍後](#input-formatters)會說明輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="0348c-266">[Bind] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-266">[Bind] attribute</span></span>

<span data-ttu-id="0348c-267">可以套用至類別或方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="0348c-268">指定模型繫結應包含哪些模型屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="0348c-269">`[Bind]` 不 _\*_會影響輸入_\*_ 格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="0348c-270">在下列範例中，當呼叫任何處理常式或動作方法時，只會繫結 `Instructor` 模型的指定屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="0348c-271">在下列範例中，當呼叫 `OnPost` 方法時，只會繫結 `Instructor` 模型的指定屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="0348c-272">`[Bind]`屬性可以用來防止 _create \* 案例中的大量指派。</span><span class="sxs-lookup"><span data-stu-id="0348c-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="0348c-273">因為排除的屬性是設為 null 或預設值，而不是保持不變，所以在編輯案例中無法正常運作。</span><span class="sxs-lookup"><span data-stu-id="0348c-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="0348c-274">建議使用檢視模型而非 `[Bind]` 屬性來防禦大量指派。</span><span class="sxs-lookup"><span data-stu-id="0348c-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="0348c-275">如需詳細資訊，請參閱 [關於大量指派的安全性注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)。</span><span class="sxs-lookup"><span data-stu-id="0348c-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="0348c-276">[ModelBinder] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="0348c-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> 可以套用至類型、屬性或參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="0348c-278">它可讓您指定用來系結特定實例或型別的模型系結器類型。</span><span class="sxs-lookup"><span data-stu-id="0348c-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="0348c-279">例如：</span><span class="sxs-lookup"><span data-stu-id="0348c-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="0348c-280">屬性（ `[ModelBinder]` attribute）也可以用來在模型系結時，變更屬性或參數的名稱：</span><span class="sxs-lookup"><span data-stu-id="0348c-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="0348c-281">[BindRequired] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-281">[BindRequired] attribute</span></span>

<span data-ttu-id="0348c-282">只能套用至模型屬性，不能套用到方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="0348c-283">如果模型的屬性不能發生繫結，則會造成模型繫結新增模型狀態錯誤。</span><span class="sxs-lookup"><span data-stu-id="0348c-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="0348c-284">以下是範例：</span><span class="sxs-lookup"><span data-stu-id="0348c-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="0348c-285">另請參閱 `[Required]` [模型驗證](xref:mvc/models/validation#required-attribute)中的屬性討論。</span><span class="sxs-lookup"><span data-stu-id="0348c-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="0348c-286">[BindNever] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-286">[BindNever] attribute</span></span>

<span data-ttu-id="0348c-287">只能套用至模型屬性，不能套用到方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="0348c-288">避免模型繫結設定模型的屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="0348c-289">以下是範例：</span><span class="sxs-lookup"><span data-stu-id="0348c-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="0348c-290">集合</span><span class="sxs-lookup"><span data-stu-id="0348c-290">Collections</span></span>

<span data-ttu-id="0348c-291">針對簡單型別集合的目標，模型繫結會尋找符合 *parameter_name* 或 *property_name* 的項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="0348c-292">如果找不到相符項目，它會尋找其中一種沒有前置詞的受支援格式。</span><span class="sxs-lookup"><span data-stu-id="0348c-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="0348c-293">例如：</span><span class="sxs-lookup"><span data-stu-id="0348c-293">For example:</span></span>

* <span data-ttu-id="0348c-294">假設要繫結的參數是名為 `selectedCourses` 的陣列：</span><span class="sxs-lookup"><span data-stu-id="0348c-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="0348c-295">表單或查詢字串資料可以是下列其中一種格式：</span><span class="sxs-lookup"><span data-stu-id="0348c-295">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="0348c-296">只有表單資料支援下列格式：</span><span class="sxs-lookup"><span data-stu-id="0348c-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="0348c-297">針對前列所有範例格式，模型繫結會將有兩個項目的陣列傳遞至 `selectedCourses` 參數：</span><span class="sxs-lookup"><span data-stu-id="0348c-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="0348c-298">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="0348c-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="0348c-299">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="0348c-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="0348c-300">使用注標編號 ( 的資料格式 .。。[0] .。。[1] ... ) 必須確保從零開始依序編號。</span><span class="sxs-lookup"><span data-stu-id="0348c-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="0348c-301">下標編號中如有任何間距，則會忽略間隔後的所有項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="0348c-302">例如，如果下標是 0 和 2，而不是 0 和 1，則忽略第二個項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="0348c-303">字典</span><span class="sxs-lookup"><span data-stu-id="0348c-303">Dictionaries</span></span>

<span data-ttu-id="0348c-304">針對 `Dictionary` 目標，模型系結會尋找對 *parameter_name* 或 *property_name* 的相符專案。</span><span class="sxs-lookup"><span data-stu-id="0348c-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="0348c-305">如果找不到相符項目，它會尋找其中一種沒有前置詞的受支援格式。</span><span class="sxs-lookup"><span data-stu-id="0348c-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="0348c-306">例如：</span><span class="sxs-lookup"><span data-stu-id="0348c-306">For example:</span></span>

* <span data-ttu-id="0348c-307">假設目標參數是 `Dictionary<int, string>` 名為的 `selectedCourses` ：</span><span class="sxs-lookup"><span data-stu-id="0348c-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="0348c-308">已張貼的表單或查詢字串資料看起來會像下列其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="0348c-308">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="0348c-309">針對前列所有範例格式，模型繫結會將有兩個項目的字典傳遞至 `selectedCourses` 參數：</span><span class="sxs-lookup"><span data-stu-id="0348c-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="0348c-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="0348c-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="0348c-311">selectedCourses ["2000"] = "經濟效益"</span><span class="sxs-lookup"><span data-stu-id="0348c-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="0348c-312">函數系結和記錄類型</span><span class="sxs-lookup"><span data-stu-id="0348c-312">Constructor binding and record types</span></span>

<span data-ttu-id="0348c-313">模型系結需要複雜類型具有無參數的函式。</span><span class="sxs-lookup"><span data-stu-id="0348c-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="0348c-314">和型輸入格式子都支援還原序列化 `System.Text.Json` `Newtonsoft.Json` 沒有無參數函式的類別。</span><span class="sxs-lookup"><span data-stu-id="0348c-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="0348c-315">C # 9 引進了一種記錄類型，這是在網路上簡潔表示資料的絕佳方式。</span><span class="sxs-lookup"><span data-stu-id="0348c-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="0348c-316">ASP.NET Core 使用單一的函式加入模型系結和驗證記錄類型的支援：</span><span class="sxs-lookup"><span data-stu-id="0348c-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="0348c-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="0348c-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="0348c-318">驗證記錄類型時，執行時間會特別在參數上搜尋驗證中繼資料，而不是在屬性上搜尋。</span><span class="sxs-lookup"><span data-stu-id="0348c-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="0348c-319">模型系結路由資料和查詢字串的全球化行為</span><span class="sxs-lookup"><span data-stu-id="0348c-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="0348c-320">ASP.NET Core 路由值提供者和查詢字串值提供者：</span><span class="sxs-lookup"><span data-stu-id="0348c-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="0348c-321">將值視為不變的文化特性。</span><span class="sxs-lookup"><span data-stu-id="0348c-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="0348c-322">預期 Url 的文化特性不變。</span><span class="sxs-lookup"><span data-stu-id="0348c-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="0348c-323">相反地，來自表單資料的值會進行區分文化特性的轉換。</span><span class="sxs-lookup"><span data-stu-id="0348c-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="0348c-324">這是設計的，因此可以跨地區設定共用 Url。</span><span class="sxs-lookup"><span data-stu-id="0348c-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="0348c-325">若要讓 ASP.NET Core 路由值提供者和查詢字串值提供者進行區分文化特性的轉換：</span><span class="sxs-lookup"><span data-stu-id="0348c-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="0348c-326">繼承自 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="0348c-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="0348c-327">從[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs)或[RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)複製程式碼</span><span class="sxs-lookup"><span data-stu-id="0348c-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="0348c-328">使用[CultureInfo CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)取代傳遞給值提供者函式的[文化特性值](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)。</span><span class="sxs-lookup"><span data-stu-id="0348c-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="0348c-329">以您的新值取代 MVC 選項中的預設值提供者 factory：</span><span class="sxs-lookup"><span data-stu-id="0348c-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="0348c-330">特殊資料類型</span><span class="sxs-lookup"><span data-stu-id="0348c-330">Special data types</span></span>

<span data-ttu-id="0348c-331">模型繫結可以處理某些特殊資料類型。</span><span class="sxs-lookup"><span data-stu-id="0348c-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="0348c-332">IFormFile 和 IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="0348c-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="0348c-333">HTTP 要求包含上傳的檔案。</span><span class="sxs-lookup"><span data-stu-id="0348c-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="0348c-334">也支援多個檔案的 `IEnumerable<IFormFile>`。</span><span class="sxs-lookup"><span data-stu-id="0348c-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="0348c-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="0348c-335">CancellationToken</span></span>

<span data-ttu-id="0348c-336">動作可以選擇性地系結 `CancellationToken` 做為參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-336">Actions can optionally bind a `CancellationToken` as a parameter.</span></span> <span data-ttu-id="0348c-337"><xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted>當 HTTP 要求的基礎連接中止時，此系結會發出信號。</span><span class="sxs-lookup"><span data-stu-id="0348c-337">This binds <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> that signals when the connection underlying the HTTP request is aborted.</span></span> <span data-ttu-id="0348c-338">動作可以使用此參數來取消長時間執行的非同步作業，而這些作業會做為控制器動作的一部分執行。</span><span class="sxs-lookup"><span data-stu-id="0348c-338">Actions can use this parameter to cancel long running async operations that are executed as part of the controller actions.</span></span>

### <a name="formcollection"></a><span data-ttu-id="0348c-339">FormCollection</span><span class="sxs-lookup"><span data-stu-id="0348c-339">FormCollection</span></span>

<span data-ttu-id="0348c-340">用來擷取已張貼表單資料中的所有值。</span><span class="sxs-lookup"><span data-stu-id="0348c-340">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="0348c-341">輸入格式器</span><span class="sxs-lookup"><span data-stu-id="0348c-341">Input formatters</span></span>

<span data-ttu-id="0348c-342">要求主體中的資料可以是 JSON、XML 或其他格式。</span><span class="sxs-lookup"><span data-stu-id="0348c-342">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="0348c-343">模型繫結會使用設定處理特定內容類型的「輸入格式器」，來剖析此資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-343">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="0348c-344">根據預設，ASP.NET Core 包含以 JSON 為基礎的輸入格式器，用來處理 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-344">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="0348c-345">您可以新增其他內容類型的格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-345">You can add other formatters for other content types.</span></span>

<span data-ttu-id="0348c-346">ASP.NET Core 選取以 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 屬性為基礎的輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-346">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="0348c-347">若無任何屬性，則它會使用 [Content-Type 標頭](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)。</span><span class="sxs-lookup"><span data-stu-id="0348c-347">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="0348c-348">使用內建的 XML 輸入格式器：</span><span class="sxs-lookup"><span data-stu-id="0348c-348">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="0348c-349">安裝 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="0348c-349">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="0348c-350">在 `Startup.ConfigureServices` 中，呼叫 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 或 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>。</span><span class="sxs-lookup"><span data-stu-id="0348c-350">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="0348c-351">將 `Consumes` 屬性套用至要求本文應為 XML 的控制器類別或動作方法。</span><span class="sxs-lookup"><span data-stu-id="0348c-351">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="0348c-352">如需詳細資訊，請參閱 [XML 序列化簡介](/dotnet/standard/serialization/introducing-xml-serialization)。</span><span class="sxs-lookup"><span data-stu-id="0348c-352">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="0348c-353">使用輸入格式子自訂模型系結</span><span class="sxs-lookup"><span data-stu-id="0348c-353">Customize model binding with input formatters</span></span>

<span data-ttu-id="0348c-354">輸入格式器完全負責從要求主體讀取資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-354">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="0348c-355">若要自訂此程式，請設定輸入格式器所使用的 Api。</span><span class="sxs-lookup"><span data-stu-id="0348c-355">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="0348c-356">本節說明如何自訂 `System.Text.Json` 型輸入格式器，以瞭解名為的自訂型別 `ObjectId` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-356">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="0348c-357">請考慮下列模型，其中包含名為的自訂 `ObjectId` 屬性 `Id` ：</span><span class="sxs-lookup"><span data-stu-id="0348c-357">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="0348c-358">若要在使用時自訂模型系結處理常式 `System.Text.Json` ，請建立衍生自的類別 <xref:System.Text.Json.Serialization.JsonConverter%601> ：</span><span class="sxs-lookup"><span data-stu-id="0348c-358">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="0348c-359">若要使用自訂轉換器，請將 <xref:System.Text.Json.Serialization.JsonConverterAttribute> 屬性套用至類型。</span><span class="sxs-lookup"><span data-stu-id="0348c-359">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="0348c-360">在下列範例中， `ObjectId` 會將類型設定 `ObjectIdConverter` 為其自訂轉換器：</span><span class="sxs-lookup"><span data-stu-id="0348c-360">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="0348c-361">如需詳細資訊，請參閱 [如何撰寫自訂轉換器](/dotnet/standard/serialization/system-text-json-converters-how-to)。</span><span class="sxs-lookup"><span data-stu-id="0348c-361">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="0348c-362">排除模型繫結中的指定類型</span><span class="sxs-lookup"><span data-stu-id="0348c-362">Exclude specified types from model binding</span></span>

<span data-ttu-id="0348c-363">模型繫結和驗證系統的行為是由 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) 所驅動。</span><span class="sxs-lookup"><span data-stu-id="0348c-363">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="0348c-364">您可以將詳細資料提供者新增至 [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)，藉以自訂 `ModelMetadata`。</span><span class="sxs-lookup"><span data-stu-id="0348c-364">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="0348c-365">內建的詳細資料提供者可用於停用模型繫結或驗證所指定類型。</span><span class="sxs-lookup"><span data-stu-id="0348c-365">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="0348c-366">若要停用指定類型之所有模型的模型繫結，請在 `Startup.ConfigureServices` 中新增 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="0348c-366">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0348c-367">例如，若要對類型為 `System.Version` 的所有模型停用模型繫結：</span><span class="sxs-lookup"><span data-stu-id="0348c-367">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="0348c-368">若要停用指定類型屬性的驗證，請在 `Startup.ConfigureServices` 中新增 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="0348c-368">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0348c-369">例如，若要針對類型為 `System.Guid` 的屬性停用驗證：</span><span class="sxs-lookup"><span data-stu-id="0348c-369">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="0348c-370">自訂模型繫結器</span><span class="sxs-lookup"><span data-stu-id="0348c-370">Custom model binders</span></span>

<span data-ttu-id="0348c-371">您可以撰寫自訂的模型繫結器來擴充模型繫結，並使用 `[ModelBinder]` 屬性以針對特定目標來選取它。</span><span class="sxs-lookup"><span data-stu-id="0348c-371">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="0348c-372">深入了解[自訂模型繫結](xref:mvc/advanced/custom-model-binding)。</span><span class="sxs-lookup"><span data-stu-id="0348c-372">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="0348c-373">手動模型系結</span><span class="sxs-lookup"><span data-stu-id="0348c-373">Manual model binding</span></span> 

<span data-ttu-id="0348c-374">使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 方法即可手動叫用模型繫結。</span><span class="sxs-lookup"><span data-stu-id="0348c-374">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="0348c-375">此方法已於 `ControllerBase` 和 `PageModel` 類別中定義。</span><span class="sxs-lookup"><span data-stu-id="0348c-375">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="0348c-376">方法多載可讓您指定要使用的前置詞和值提供者。</span><span class="sxs-lookup"><span data-stu-id="0348c-376">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="0348c-377">如果模型繫結失敗，此方法會傳回 `false`。</span><span class="sxs-lookup"><span data-stu-id="0348c-377">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="0348c-378">以下是範例：</span><span class="sxs-lookup"><span data-stu-id="0348c-378">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="0348c-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  使用值提供者從表單主體、查詢字串和路由資料取得資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="0348c-380">`TryUpdateModelAsync` 通常是：</span><span class="sxs-lookup"><span data-stu-id="0348c-380">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="0348c-381">搭配 Razor 使用控制器和 views 的頁面和 MVC 應用程式來防止過度張貼。</span><span class="sxs-lookup"><span data-stu-id="0348c-381">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="0348c-382">除非從表單資料、查詢字串和路由資料使用，否則不會與 web API 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="0348c-382">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="0348c-383">使用 JSON 的 Web API 端點會使用 [輸入](#input-formatters) 格式子將要求本文還原序列化為物件。</span><span class="sxs-lookup"><span data-stu-id="0348c-383">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="0348c-384">如需詳細資訊，請參閱 [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync)。</span><span class="sxs-lookup"><span data-stu-id="0348c-384">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="0348c-385">[FromServices] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-385">[FromServices] attribute</span></span>

<span data-ttu-id="0348c-386">這個屬性的名稱會遵循指定資料來源之模型系結屬性的模式。</span><span class="sxs-lookup"><span data-stu-id="0348c-386">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="0348c-387">但是，它並不是從值提供者系結資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-387">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="0348c-388">它從[相依性插入](xref:fundamentals/dependency-injection)容器取得類型的執行個體。</span><span class="sxs-lookup"><span data-stu-id="0348c-388">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0348c-389">其目的是只有在呼叫特定方法時，才在您需要服務時提供建構函式插入的替代項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-389">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0348c-390">其他資源</span><span class="sxs-lookup"><span data-stu-id="0348c-390">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0348c-391">本文會說明何謂模型繫結、其運作方式，以及如何自訂其行為。</span><span class="sxs-lookup"><span data-stu-id="0348c-391">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="0348c-392">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0348c-392">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="0348c-393">何謂模型繫結</span><span class="sxs-lookup"><span data-stu-id="0348c-393">What is Model binding</span></span>

<span data-ttu-id="0348c-394">控制器和 Razor 頁面會處理來自 HTTP 要求的資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-394">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="0348c-395">例如，路由資料可能會提供記錄索引鍵，而已張貼的表單欄位可能會提供模型屬性的值。</span><span class="sxs-lookup"><span data-stu-id="0348c-395">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="0348c-396">撰寫程式碼來擷取這些值的每一個並將它們從字串轉換成 .NET 類型，不但繁瑣又容易發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="0348c-396">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="0348c-397">模型繫結會自動化此程序。</span><span class="sxs-lookup"><span data-stu-id="0348c-397">Model binding automates this process.</span></span> <span data-ttu-id="0348c-398">模型繫結系統：</span><span class="sxs-lookup"><span data-stu-id="0348c-398">The model binding system:</span></span>

* <span data-ttu-id="0348c-399">從各種來源（例如，路由資料、表單欄位和查詢字串）抓取資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-399">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="0348c-400">提供資料給 Razor 方法參數和公用屬性中的控制器和頁面。</span><span class="sxs-lookup"><span data-stu-id="0348c-400">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="0348c-401">將字串資料轉換成 .NET 類型。</span><span class="sxs-lookup"><span data-stu-id="0348c-401">Converts string data to .NET types.</span></span>
* <span data-ttu-id="0348c-402">更新複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-402">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="0348c-403">範例</span><span class="sxs-lookup"><span data-stu-id="0348c-403">Example</span></span>

<span data-ttu-id="0348c-404">假設您有下列動作方法：</span><span class="sxs-lookup"><span data-stu-id="0348c-404">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="0348c-405">而應用程式收到具有此 URL 的要求：</span><span class="sxs-lookup"><span data-stu-id="0348c-405">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="0348c-406">在路由系統選取動作方法之後，模型系結會經歷下列步驟：</span><span class="sxs-lookup"><span data-stu-id="0348c-406">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="0348c-407">尋找第一個參數 `GetByID`，它是名為 `id` 的整數。</span><span class="sxs-lookup"><span data-stu-id="0348c-407">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="0348c-408">查看 HTTP 要求中所有可用的來源，在路由資料中找到 `id` = "2"。</span><span class="sxs-lookup"><span data-stu-id="0348c-408">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="0348c-409">將字串 "2" 轉換成整數 2。</span><span class="sxs-lookup"><span data-stu-id="0348c-409">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="0348c-410">尋找的下一個參數 `GetByID` ，也就是名為的布林值 `dogsOnly` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-410">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="0348c-411">查看來源，在查詢字串中找到 "DogsOnly=true"。</span><span class="sxs-lookup"><span data-stu-id="0348c-411">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="0348c-412">名稱比對不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="0348c-412">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="0348c-413">將字串 "true" 轉換成布林值 `true` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-413">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="0348c-414">架構接著會呼叫 `GetById` 方法，針對 `id` 參數傳送 2、`dogsOnly` 參數傳送 `true`。</span><span class="sxs-lookup"><span data-stu-id="0348c-414">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="0348c-415">在上例中，模型繫結目標都是簡單型別的方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-415">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="0348c-416">目標也可以是複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-416">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="0348c-417">成功系結每個屬性之後，就會針對該屬性進行 [模型驗證](xref:mvc/models/validation) 。</span><span class="sxs-lookup"><span data-stu-id="0348c-417">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="0348c-418">哪些資料繫結至模型，以及任何繫結或驗證錯誤的記錄，都會儲存在 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 或 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)。</span><span class="sxs-lookup"><span data-stu-id="0348c-418">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="0348c-419">為了解此程序是否成功，應用程式會檢查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 旗標。</span><span class="sxs-lookup"><span data-stu-id="0348c-419">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="0348c-420">目標</span><span class="sxs-lookup"><span data-stu-id="0348c-420">Targets</span></span>

<span data-ttu-id="0348c-421">模型繫結會嘗試尋找下列幾種目標的值：</span><span class="sxs-lookup"><span data-stu-id="0348c-421">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="0348c-422">要求路由目標的控制器動作方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-422">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="0348c-423">Razor傳送要求的頁面處理常式方法的參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-423">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="0348c-424">控制站的公用屬性或 `PageModel` 類別，如由屬性指定。</span><span class="sxs-lookup"><span data-stu-id="0348c-424">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="0348c-425">[BindProperty] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-425">[BindProperty] attribute</span></span>

<span data-ttu-id="0348c-426">可以套用至控制器或類別的公用屬性 `PageModel` ，使模型系結成為該屬性的目標：</span><span class="sxs-lookup"><span data-stu-id="0348c-426">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="0348c-427">[BindProperties] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-427">[BindProperties] attribute</span></span>

<span data-ttu-id="0348c-428">可在 ASP.NET Core 2.1 和更新版本中使用。</span><span class="sxs-lookup"><span data-stu-id="0348c-428">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="0348c-429">可以套用至控制器或 `PageModel` 類別，以告知模型系結將目標設為類別的所有公用屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-429">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="0348c-430">適用於 HTTP GET 要求的模型繫結</span><span class="sxs-lookup"><span data-stu-id="0348c-430">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="0348c-431">根據預設，屬性不會針對 HTTP GET 要求繫結。</span><span class="sxs-lookup"><span data-stu-id="0348c-431">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="0348c-432">一般而言，GET 要求只需要記錄識別碼參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-432">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="0348c-433">此記錄識別碼用來查詢資料庫中的項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-433">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="0348c-434">因此，不需要系結保存模型實例的屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-434">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="0348c-435">在您要從 GET 要求將屬性繫結至資料的案例中，請將 `SupportsGet` 屬性設為 `true`：</span><span class="sxs-lookup"><span data-stu-id="0348c-435">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="0348c-436">來源</span><span class="sxs-lookup"><span data-stu-id="0348c-436">Sources</span></span>

<span data-ttu-id="0348c-437">根據預設，模型繫結會從下列 HTTP 要求的來源中，取得索引鍵/值組形式的資料：</span><span class="sxs-lookup"><span data-stu-id="0348c-437">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="0348c-438">表單欄位</span><span class="sxs-lookup"><span data-stu-id="0348c-438">Form fields</span></span>
1. <span data-ttu-id="0348c-439">[具有 [ApiController] 屬性之控制器](xref:web-api/index#binding-source-parameter-inference)的要求主體 (。 ) </span><span class="sxs-lookup"><span data-stu-id="0348c-439">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="0348c-440">路由傳送資料</span><span class="sxs-lookup"><span data-stu-id="0348c-440">Route data</span></span>
1. <span data-ttu-id="0348c-441">查詢字串參數</span><span class="sxs-lookup"><span data-stu-id="0348c-441">Query string parameters</span></span>
1. <span data-ttu-id="0348c-442">已上傳的檔案</span><span class="sxs-lookup"><span data-stu-id="0348c-442">Uploaded files</span></span>

<span data-ttu-id="0348c-443">針對每個目標參數或屬性，系統會依照上述清單中指出的順序掃描來源。</span><span class="sxs-lookup"><span data-stu-id="0348c-443">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="0348c-444">但也有一些例外：</span><span class="sxs-lookup"><span data-stu-id="0348c-444">There are a few exceptions:</span></span>

* <span data-ttu-id="0348c-445">路由資料和查詢字串值只用於簡單型別。</span><span class="sxs-lookup"><span data-stu-id="0348c-445">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="0348c-446">上傳的檔案只會系結至執行或的目標型別 `IFormFile` `IEnumerable<IFormFile>` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-446">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="0348c-447">如果預設來源不正確，請使用下列其中一個屬性來指定來源：</span><span class="sxs-lookup"><span data-stu-id="0348c-447">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="0348c-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -取得查詢字串中的值。</span><span class="sxs-lookup"><span data-stu-id="0348c-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="0348c-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -從路由資料取得值。</span><span class="sxs-lookup"><span data-stu-id="0348c-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="0348c-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -從張貼的表單欄位取得值。</span><span class="sxs-lookup"><span data-stu-id="0348c-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="0348c-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -從要求主體取得值。</span><span class="sxs-lookup"><span data-stu-id="0348c-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="0348c-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -取得 HTTP 標頭的值。</span><span class="sxs-lookup"><span data-stu-id="0348c-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="0348c-453">這些屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-453">These attributes:</span></span>

* <span data-ttu-id="0348c-454">會個別新增至模型屬性 (不是模型類別)，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="0348c-454">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="0348c-455">（選擇性）在函式中接受模型名稱值。</span><span class="sxs-lookup"><span data-stu-id="0348c-455">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="0348c-456">如果屬性名稱與要求中的值不符，就會提供這個選項。</span><span class="sxs-lookup"><span data-stu-id="0348c-456">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="0348c-457">例如，要求中的值可能是名稱中有連字號的標頭，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="0348c-457">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="0348c-458">[FromBody] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-458">[FromBody] attribute</span></span>

<span data-ttu-id="0348c-459">將 `[FromBody]` 屬性套用至參數，以從 HTTP 要求的主體填入其屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-459">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="0348c-460">ASP.NET Core 執行時間會將讀取主體的責任委派給輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-460">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="0348c-461">[本文稍後](#input-formatters)會說明輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-461">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="0348c-462">當套用 `[FromBody]` 至複雜型別參數時，會忽略套用至其屬性的任何系結來源屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-462">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="0348c-463">例如，下列 `Create` 動作會指定其 `pet` 參數已從主體填入：</span><span class="sxs-lookup"><span data-stu-id="0348c-463">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="0348c-464">`Pet`類別 `Breed` 會指定從查詢字串參數填入其屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-464">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="0348c-465">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="0348c-465">In the preceding example:</span></span>

* <span data-ttu-id="0348c-466">`[FromQuery]`忽略屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-466">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="0348c-467">`Breed`屬性不會從查詢字串參數填入。</span><span class="sxs-lookup"><span data-stu-id="0348c-467">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="0348c-468">輸入格式器只會讀取本文，而不會瞭解系結來源屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-468">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="0348c-469">如果在主體中找到適當的值，則會使用該值來填入 `Breed` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-469">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="0348c-470">請勿套用 `[FromBody]` 至每個動作方法的一個以上參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-470">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="0348c-471">一旦輸入格式器讀取要求資料流程之後，就無法再讀取它來系結其他 `[FromBody]` 參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-471">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="0348c-472">其他來源</span><span class="sxs-lookup"><span data-stu-id="0348c-472">Additional sources</span></span>

<span data-ttu-id="0348c-473">*值提供* 者會將來源資料提供給模型系結系統。</span><span class="sxs-lookup"><span data-stu-id="0348c-473">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="0348c-474">您可以撰寫並註冊自訂值提供者，其從其他來源取得模型繫結資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-474">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="0348c-475">例如，您可能會想要來自 cookie s 或會話狀態的資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-475">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="0348c-476">若要從新的來源取得資料：</span><span class="sxs-lookup"><span data-stu-id="0348c-476">To get data from a new source:</span></span>

* <span data-ttu-id="0348c-477">建立會實作 `IValueProvider` 的類別。</span><span class="sxs-lookup"><span data-stu-id="0348c-477">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="0348c-478">建立會實作 `IValueProviderFactory` 的類別。</span><span class="sxs-lookup"><span data-stu-id="0348c-478">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="0348c-479">在 `Startup.ConfigureServices` 中註冊 Factory 類別。</span><span class="sxs-lookup"><span data-stu-id="0348c-479">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="0348c-480">範例應用程式包含 [值提供者](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) 和 [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) 範例，可取得 s 的值 cookie 。</span><span class="sxs-lookup"><span data-stu-id="0348c-480">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="0348c-481">以下是 `Startup.ConfigureServices` 中的註冊碼：</span><span class="sxs-lookup"><span data-stu-id="0348c-481">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="0348c-482">顯示的程式碼會將自訂值提供者放在所有內建值提供者的後面。</span><span class="sxs-lookup"><span data-stu-id="0348c-482">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="0348c-483">若要讓它成為清單中的第一個，請呼叫， `Insert(0, new CookieValueProviderFactory())` 而不是 `Add` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-483">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="0348c-484">無模型屬性的來源</span><span class="sxs-lookup"><span data-stu-id="0348c-484">No source for a model property</span></span>

<span data-ttu-id="0348c-485">依預設，如果找不到模型屬性的值，則不會建立模型狀態錯誤。</span><span class="sxs-lookup"><span data-stu-id="0348c-485">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="0348c-486">屬性設定為 null 或預設值：</span><span class="sxs-lookup"><span data-stu-id="0348c-486">The property is set to null or a default value:</span></span>

* <span data-ttu-id="0348c-487">可為 null 的簡單類型會設定為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="0348c-487">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="0348c-488">不可為 Null 的實值型別會設為 `default(T)`。</span><span class="sxs-lookup"><span data-stu-id="0348c-488">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="0348c-489">例如，參數 `int id` 設為 0。</span><span class="sxs-lookup"><span data-stu-id="0348c-489">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="0348c-490">針對複雜類型，模型系結會使用預設的函式來建立實例，而不需要設定屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-490">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="0348c-491">陣列設為 `Array.Empty<T>()`，但 `byte[]` 陣列設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="0348c-491">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="0348c-492">如果模型狀態在模型屬性的表單欄位中找不到任何專案時，應該會失效，請使用 [`[BindRequired]`](#bindrequired-attribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-492">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="0348c-493">請注意，此 `[BindRequired]` 行為適用於來自已張貼表單資料的模型繫結，不適合要求本文中的 JSON 或 XML 資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-493">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="0348c-494">要求主體資料是由 [輸入](#input-formatters)格式器處理。</span><span class="sxs-lookup"><span data-stu-id="0348c-494">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="0348c-495">類型轉換錯誤</span><span class="sxs-lookup"><span data-stu-id="0348c-495">Type conversion errors</span></span>

<span data-ttu-id="0348c-496">如果找到來源但無法轉換成目標型別，則會將模型狀態標示為無效。</span><span class="sxs-lookup"><span data-stu-id="0348c-496">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="0348c-497">目標參數或屬性會設為 null 或預設值，如上一節中所述。</span><span class="sxs-lookup"><span data-stu-id="0348c-497">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="0348c-498">在具有 `[ApiController]` 屬性的 API 控制器中，無效的模型狀態會導致自動 HTTP 400 回應。</span><span class="sxs-lookup"><span data-stu-id="0348c-498">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="0348c-499">在 Razor 頁面中，重新顯示包含錯誤訊息的頁面：</span><span class="sxs-lookup"><span data-stu-id="0348c-499">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="0348c-500">用戶端驗證會攔截將會提交至頁面表單的最不正確資料 Razor 。</span><span class="sxs-lookup"><span data-stu-id="0348c-500">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="0348c-501">此驗證會讓您更難觸發上述醒目提示的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0348c-501">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="0348c-502">範例應用程式包含 [ **提交** 日期] 不正確 [提交日期] 按鈕，會將錯誤的資料放入 [ **雇用日期** ] 欄位並提交表單。</span><span class="sxs-lookup"><span data-stu-id="0348c-502">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="0348c-503">此按鈕會顯示當資料轉換錯誤發生時，重新顯示頁面的程式碼如何運作。</span><span class="sxs-lookup"><span data-stu-id="0348c-503">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="0348c-504">當先前的程式碼重新顯示頁面時，表單欄位中不會顯示不正確輸入。</span><span class="sxs-lookup"><span data-stu-id="0348c-504">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="0348c-505">這是因為模型屬性已設為 null 或預設值。</span><span class="sxs-lookup"><span data-stu-id="0348c-505">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="0348c-506">無效的輸入確實會出現在錯誤訊息中。</span><span class="sxs-lookup"><span data-stu-id="0348c-506">The invalid input does appear in an error message.</span></span> <span data-ttu-id="0348c-507">但是，如果您想要在表單欄位中重新顯示不正確的資料，請考慮讓模型屬性成為字串，以手動方式執行資料轉換。</span><span class="sxs-lookup"><span data-stu-id="0348c-507">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="0348c-508">如果您不想要類型轉換錯誤導致模型狀態錯誤，則建議使用相同的策略。</span><span class="sxs-lookup"><span data-stu-id="0348c-508">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="0348c-509">在這種情況下，請將 model 屬性設為字串。</span><span class="sxs-lookup"><span data-stu-id="0348c-509">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="0348c-510">簡單型別</span><span class="sxs-lookup"><span data-stu-id="0348c-510">Simple types</span></span>

<span data-ttu-id="0348c-511">模型繫結器可將來源字串轉換成的簡單型別包括：</span><span class="sxs-lookup"><span data-stu-id="0348c-511">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="0348c-512">布林值</span><span class="sxs-lookup"><span data-stu-id="0348c-512">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="0348c-513">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="0348c-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="0348c-514">字元</span><span class="sxs-lookup"><span data-stu-id="0348c-514">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="0348c-515">DateTime</span><span class="sxs-lookup"><span data-stu-id="0348c-515">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="0348c-516">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="0348c-516">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="0348c-517">十進位</span><span class="sxs-lookup"><span data-stu-id="0348c-517">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="0348c-518">Double</span><span class="sxs-lookup"><span data-stu-id="0348c-518">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="0348c-519">枚舉</span><span class="sxs-lookup"><span data-stu-id="0348c-519">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="0348c-520">Guid</span><span class="sxs-lookup"><span data-stu-id="0348c-520">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="0348c-521">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="0348c-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="0348c-522">Single</span><span class="sxs-lookup"><span data-stu-id="0348c-522">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="0348c-523">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="0348c-523">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="0348c-524">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="0348c-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="0348c-525">Uri</span><span class="sxs-lookup"><span data-stu-id="0348c-525">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="0348c-526">版本</span><span class="sxs-lookup"><span data-stu-id="0348c-526">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="0348c-527">複雜類型</span><span class="sxs-lookup"><span data-stu-id="0348c-527">Complex types</span></span>

<span data-ttu-id="0348c-528">複雜型別必須具有公用預設的函式和可系結的公用可寫入屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-528">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="0348c-529">發生模型繫結時，類別會使用公用預設建構函式具現化。</span><span class="sxs-lookup"><span data-stu-id="0348c-529">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="0348c-530">針對複雜類型的每個屬性，模型繫結會查看名稱模式 *prefix.property_name* 的來源。</span><span class="sxs-lookup"><span data-stu-id="0348c-530">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="0348c-531">如果找不到，它會只尋找沒有前置詞的 *property_name*。</span><span class="sxs-lookup"><span data-stu-id="0348c-531">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="0348c-532">若要繫結至參數，則前置詞是參數名稱。</span><span class="sxs-lookup"><span data-stu-id="0348c-532">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="0348c-533">若要繫結至 `PageModel` 公用屬性，則前置詞為公用屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="0348c-533">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="0348c-534">某些屬性 (Attribute) 具有 `Prefix` 屬性 (Property)，其可讓您覆寫參數或屬性名稱的預設使用方法。</span><span class="sxs-lookup"><span data-stu-id="0348c-534">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="0348c-535">例如，假設複雜類型是下列 `Instructor` 類別：</span><span class="sxs-lookup"><span data-stu-id="0348c-535">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="0348c-536">前置詞 = 參數名稱</span><span class="sxs-lookup"><span data-stu-id="0348c-536">Prefix = parameter name</span></span>

<span data-ttu-id="0348c-537">如果要繫結的模型是名為 `instructorToUpdate` 的參數：</span><span class="sxs-lookup"><span data-stu-id="0348c-537">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="0348c-538">模型繫結從查看索引鍵 `instructorToUpdate.ID` 的來源開始。</span><span class="sxs-lookup"><span data-stu-id="0348c-538">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="0348c-539">如果找不到，則會尋找 `ID` 沒有前置詞的。</span><span class="sxs-lookup"><span data-stu-id="0348c-539">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="0348c-540">前置詞 = 屬性名稱</span><span class="sxs-lookup"><span data-stu-id="0348c-540">Prefix = property name</span></span>

<span data-ttu-id="0348c-541">如果要繫結的模型是控制器或 `PageModel` 類別名為 `Instructor` 的屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-541">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="0348c-542">模型繫結從查看索引鍵 `Instructor.ID` 的來源開始。</span><span class="sxs-lookup"><span data-stu-id="0348c-542">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="0348c-543">如果找不到，則會尋找 `ID` 沒有前置詞的。</span><span class="sxs-lookup"><span data-stu-id="0348c-543">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="0348c-544">自訂前置詞</span><span class="sxs-lookup"><span data-stu-id="0348c-544">Custom prefix</span></span>

<span data-ttu-id="0348c-545">如果要繫結的模型是名為 `instructorToUpdate` 的參數，且 `Bind` 屬性指定 `Instructor` 為前置詞：</span><span class="sxs-lookup"><span data-stu-id="0348c-545">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="0348c-546">模型繫結從查看索引鍵 `Instructor.ID` 的來源開始。</span><span class="sxs-lookup"><span data-stu-id="0348c-546">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="0348c-547">如果找不到，則會尋找 `ID` 沒有前置詞的。</span><span class="sxs-lookup"><span data-stu-id="0348c-547">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="0348c-548">複雜類型目標的屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-548">Attributes for complex type targets</span></span>

<span data-ttu-id="0348c-549">有數個內建屬性可用來控制複雜類型的模型系結：</span><span class="sxs-lookup"><span data-stu-id="0348c-549">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="0348c-550">當張貼的表單資料為值來源時，這些屬性會影響模型繫結。</span><span class="sxs-lookup"><span data-stu-id="0348c-550">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="0348c-551">它們不會影響處理已張貼 JSON 和 XML 要求本文的輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-551">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="0348c-552">[本文稍後](#input-formatters)會說明輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-552">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="0348c-553">另請參閱 `[Required]` [模型驗證](xref:mvc/models/validation#required-attribute)中的屬性討論。</span><span class="sxs-lookup"><span data-stu-id="0348c-553">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="0348c-554">[BindRequired] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-554">[BindRequired] attribute</span></span>

<span data-ttu-id="0348c-555">只能套用至模型屬性，不能套用到方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-555">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="0348c-556">如果模型的屬性不能發生繫結，則會造成模型繫結新增模型狀態錯誤。</span><span class="sxs-lookup"><span data-stu-id="0348c-556">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="0348c-557">以下是範例：</span><span class="sxs-lookup"><span data-stu-id="0348c-557">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="0348c-558">[BindNever] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-558">[BindNever] attribute</span></span>

<span data-ttu-id="0348c-559">只能套用至模型屬性，不能套用到方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-559">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="0348c-560">避免模型繫結設定模型的屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-560">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="0348c-561">以下是範例：</span><span class="sxs-lookup"><span data-stu-id="0348c-561">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="0348c-562">[Bind] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-562">[Bind] attribute</span></span>

<span data-ttu-id="0348c-563">可以套用至類別或方法參數。</span><span class="sxs-lookup"><span data-stu-id="0348c-563">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="0348c-564">指定模型繫結應包含哪些模型屬性。</span><span class="sxs-lookup"><span data-stu-id="0348c-564">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="0348c-565">在下列範例中，當呼叫任何處理常式或動作方法時，只會繫結 `Instructor` 模型的指定屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-565">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="0348c-566">在下列範例中，當呼叫 `OnPost` 方法時，只會繫結 `Instructor` 模型的指定屬性：</span><span class="sxs-lookup"><span data-stu-id="0348c-566">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="0348c-567">`[Bind]` 屬性可用來防止「建立」案例中的大量指派。</span><span class="sxs-lookup"><span data-stu-id="0348c-567">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="0348c-568">因為排除的屬性是設為 null 或預設值，而不是保持不變，所以在編輯案例中無法正常運作。</span><span class="sxs-lookup"><span data-stu-id="0348c-568">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="0348c-569">建議使用檢視模型而非 `[Bind]` 屬性來防禦大量指派。</span><span class="sxs-lookup"><span data-stu-id="0348c-569">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="0348c-570">如需詳細資訊，請參閱 [關於大量指派的安全性注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)。</span><span class="sxs-lookup"><span data-stu-id="0348c-570">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="0348c-571">集合</span><span class="sxs-lookup"><span data-stu-id="0348c-571">Collections</span></span>

<span data-ttu-id="0348c-572">針對簡單型別集合的目標，模型繫結會尋找符合 *parameter_name* 或 *property_name* 的項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-572">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="0348c-573">如果找不到相符項目，它會尋找其中一種沒有前置詞的受支援格式。</span><span class="sxs-lookup"><span data-stu-id="0348c-573">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="0348c-574">例如：</span><span class="sxs-lookup"><span data-stu-id="0348c-574">For example:</span></span>

* <span data-ttu-id="0348c-575">假設要繫結的參數是名為 `selectedCourses` 的陣列：</span><span class="sxs-lookup"><span data-stu-id="0348c-575">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="0348c-576">表單或查詢字串資料可以是下列其中一種格式：</span><span class="sxs-lookup"><span data-stu-id="0348c-576">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="0348c-577">只有表單資料支援下列格式：</span><span class="sxs-lookup"><span data-stu-id="0348c-577">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="0348c-578">針對前列所有範例格式，模型繫結會將有兩個項目的陣列傳遞至 `selectedCourses` 參數：</span><span class="sxs-lookup"><span data-stu-id="0348c-578">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="0348c-579">selectedCourses [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="0348c-579">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="0348c-580">selectedCourses [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="0348c-580">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="0348c-581">使用注標編號 ( 的資料格式 .。。[0] .。。[1] ... ) 必須確保從零開始依序編號。</span><span class="sxs-lookup"><span data-stu-id="0348c-581">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="0348c-582">下標編號中如有任何間距，則會忽略間隔後的所有項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-582">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="0348c-583">例如，如果下標是 0 和 2，而不是 0 和 1，則忽略第二個項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-583">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="0348c-584">字典</span><span class="sxs-lookup"><span data-stu-id="0348c-584">Dictionaries</span></span>

<span data-ttu-id="0348c-585">針對 `Dictionary` 目標，模型系結會尋找對 *parameter_name* 或 *property_name* 的相符專案。</span><span class="sxs-lookup"><span data-stu-id="0348c-585">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="0348c-586">如果找不到相符項目，它會尋找其中一種沒有前置詞的受支援格式。</span><span class="sxs-lookup"><span data-stu-id="0348c-586">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="0348c-587">例如：</span><span class="sxs-lookup"><span data-stu-id="0348c-587">For example:</span></span>

* <span data-ttu-id="0348c-588">假設目標參數是 `Dictionary<int, string>` 名為的 `selectedCourses` ：</span><span class="sxs-lookup"><span data-stu-id="0348c-588">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="0348c-589">已張貼的表單或查詢字串資料看起來會像下列其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="0348c-589">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="0348c-590">針對前列所有範例格式，模型繫結會將有兩個項目的字典傳遞至 `selectedCourses` 參數：</span><span class="sxs-lookup"><span data-stu-id="0348c-590">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="0348c-591">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="0348c-591">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="0348c-592">selectedCourses ["2000"] = "經濟效益"</span><span class="sxs-lookup"><span data-stu-id="0348c-592">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="0348c-593">模型系結路由資料和查詢字串的全球化行為</span><span class="sxs-lookup"><span data-stu-id="0348c-593">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="0348c-594">ASP.NET Core 路由值提供者和查詢字串值提供者：</span><span class="sxs-lookup"><span data-stu-id="0348c-594">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="0348c-595">將值視為不變的文化特性。</span><span class="sxs-lookup"><span data-stu-id="0348c-595">Treat values as invariant culture.</span></span>
* <span data-ttu-id="0348c-596">預期 Url 的文化特性不變。</span><span class="sxs-lookup"><span data-stu-id="0348c-596">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="0348c-597">相反地，來自表單資料的值會進行區分文化特性的轉換。</span><span class="sxs-lookup"><span data-stu-id="0348c-597">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="0348c-598">這是設計的，因此可以跨地區設定共用 Url。</span><span class="sxs-lookup"><span data-stu-id="0348c-598">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="0348c-599">若要讓 ASP.NET Core 路由值提供者和查詢字串值提供者進行區分文化特性的轉換：</span><span class="sxs-lookup"><span data-stu-id="0348c-599">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="0348c-600">繼承自 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="0348c-600">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="0348c-601">從[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs)或[RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)複製程式碼</span><span class="sxs-lookup"><span data-stu-id="0348c-601">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="0348c-602">使用[CultureInfo CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)取代傳遞給值提供者函式的[文化特性值](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)。</span><span class="sxs-lookup"><span data-stu-id="0348c-602">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="0348c-603">以您的新值取代 MVC 選項中的預設值提供者 factory：</span><span class="sxs-lookup"><span data-stu-id="0348c-603">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="0348c-604">特殊資料類型</span><span class="sxs-lookup"><span data-stu-id="0348c-604">Special data types</span></span>

<span data-ttu-id="0348c-605">模型繫結可以處理某些特殊資料類型。</span><span class="sxs-lookup"><span data-stu-id="0348c-605">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="0348c-606">IFormFile 和 IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="0348c-606">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="0348c-607">HTTP 要求包含上傳的檔案。</span><span class="sxs-lookup"><span data-stu-id="0348c-607">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="0348c-608">也支援多個檔案的 `IEnumerable<IFormFile>`。</span><span class="sxs-lookup"><span data-stu-id="0348c-608">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="0348c-609">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="0348c-609">CancellationToken</span></span>

<span data-ttu-id="0348c-610">用來取消非同步控制器中的活動。</span><span class="sxs-lookup"><span data-stu-id="0348c-610">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="0348c-611">FormCollection</span><span class="sxs-lookup"><span data-stu-id="0348c-611">FormCollection</span></span>

<span data-ttu-id="0348c-612">用來擷取已張貼表單資料中的所有值。</span><span class="sxs-lookup"><span data-stu-id="0348c-612">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="0348c-613">輸入格式器</span><span class="sxs-lookup"><span data-stu-id="0348c-613">Input formatters</span></span>

<span data-ttu-id="0348c-614">要求主體中的資料可以是 JSON、XML 或其他格式。</span><span class="sxs-lookup"><span data-stu-id="0348c-614">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="0348c-615">模型繫結會使用設定處理特定內容類型的「輸入格式器」，來剖析此資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-615">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="0348c-616">根據預設，ASP.NET Core 包含以 JSON 為基礎的輸入格式器，用來處理 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-616">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="0348c-617">您可以新增其他內容類型的格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-617">You can add other formatters for other content types.</span></span>

<span data-ttu-id="0348c-618">ASP.NET Core 選取以 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 屬性為基礎的輸入格式器。</span><span class="sxs-lookup"><span data-stu-id="0348c-618">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="0348c-619">若無任何屬性，則它會使用 [Content-Type 標頭](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)。</span><span class="sxs-lookup"><span data-stu-id="0348c-619">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="0348c-620">使用內建的 XML 輸入格式器：</span><span class="sxs-lookup"><span data-stu-id="0348c-620">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="0348c-621">安裝 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="0348c-621">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="0348c-622">在 `Startup.ConfigureServices` 中，呼叫 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 或 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>。</span><span class="sxs-lookup"><span data-stu-id="0348c-622">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="0348c-623">將 `Consumes` 屬性套用至要求本文應為 XML 的控制器類別或動作方法。</span><span class="sxs-lookup"><span data-stu-id="0348c-623">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="0348c-624">如需詳細資訊，請參閱 [XML 序列化簡介](/dotnet/standard/serialization/introducing-xml-serialization)。</span><span class="sxs-lookup"><span data-stu-id="0348c-624">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="0348c-625">排除模型繫結中的指定類型</span><span class="sxs-lookup"><span data-stu-id="0348c-625">Exclude specified types from model binding</span></span>

<span data-ttu-id="0348c-626">模型繫結和驗證系統的行為是由 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) 所驅動。</span><span class="sxs-lookup"><span data-stu-id="0348c-626">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="0348c-627">您可以將詳細資料提供者新增至 [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)，藉以自訂 `ModelMetadata`。</span><span class="sxs-lookup"><span data-stu-id="0348c-627">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="0348c-628">內建的詳細資料提供者可用於停用模型繫結或驗證所指定類型。</span><span class="sxs-lookup"><span data-stu-id="0348c-628">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="0348c-629">若要停用指定類型之所有模型的模型繫結，請在 `Startup.ConfigureServices` 中新增 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="0348c-629">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0348c-630">例如，若要對類型為 `System.Version` 的所有模型停用模型繫結：</span><span class="sxs-lookup"><span data-stu-id="0348c-630">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="0348c-631">若要停用指定類型屬性的驗證，請在 `Startup.ConfigureServices` 中新增 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="0348c-631">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0348c-632">例如，若要針對類型為 `System.Guid` 的屬性停用驗證：</span><span class="sxs-lookup"><span data-stu-id="0348c-632">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="0348c-633">自訂模型繫結器</span><span class="sxs-lookup"><span data-stu-id="0348c-633">Custom model binders</span></span>

<span data-ttu-id="0348c-634">您可以撰寫自訂的模型繫結器來擴充模型繫結，並使用 `[ModelBinder]` 屬性以針對特定目標來選取它。</span><span class="sxs-lookup"><span data-stu-id="0348c-634">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="0348c-635">深入了解[自訂模型繫結](xref:mvc/advanced/custom-model-binding)。</span><span class="sxs-lookup"><span data-stu-id="0348c-635">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="0348c-636">手動模型系結</span><span class="sxs-lookup"><span data-stu-id="0348c-636">Manual model binding</span></span>

<span data-ttu-id="0348c-637">使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 方法即可手動叫用模型繫結。</span><span class="sxs-lookup"><span data-stu-id="0348c-637">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="0348c-638">此方法已於 `ControllerBase` 和 `PageModel` 類別中定義。</span><span class="sxs-lookup"><span data-stu-id="0348c-638">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="0348c-639">方法多載可讓您指定要使用的前置詞和值提供者。</span><span class="sxs-lookup"><span data-stu-id="0348c-639">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="0348c-640">如果模型繫結失敗，此方法會傳回 `false`。</span><span class="sxs-lookup"><span data-stu-id="0348c-640">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="0348c-641">以下是範例：</span><span class="sxs-lookup"><span data-stu-id="0348c-641">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="0348c-642">[FromServices] 屬性</span><span class="sxs-lookup"><span data-stu-id="0348c-642">[FromServices] attribute</span></span>

<span data-ttu-id="0348c-643">這個屬性的名稱會遵循指定資料來源之模型系結屬性的模式。</span><span class="sxs-lookup"><span data-stu-id="0348c-643">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="0348c-644">但是，它並不是從值提供者系結資料。</span><span class="sxs-lookup"><span data-stu-id="0348c-644">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="0348c-645">它從[相依性插入](xref:fundamentals/dependency-injection)容器取得類型的執行個體。</span><span class="sxs-lookup"><span data-stu-id="0348c-645">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0348c-646">其目的是只有在呼叫特定方法時，才在您需要服務時提供建構函式插入的替代項目。</span><span class="sxs-lookup"><span data-stu-id="0348c-646">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0348c-647">其他資源</span><span class="sxs-lookup"><span data-stu-id="0348c-647">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
