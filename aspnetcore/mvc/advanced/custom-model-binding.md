---
title: ASP.NET Core 中的自訂模型繫結
author: ardalis
description: 了解模型繫結如何讓控制器動作直接使用 ASP.NET Core 中的模型類型。
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: b674d3a034124a26e2e76f2a7f0220e3f0ecea56
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588766"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="97c86-103">ASP.NET Core 中的自訂模型繫結</span><span class="sxs-lookup"><span data-stu-id="97c86-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="97c86-104">[Steve Smith](https://ardalis.com/)和[Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="97c86-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="97c86-105">模型繫結可直接透過模型類型 (傳入作為方法引數) 來執行控制器動作，而不用透過 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="97c86-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="97c86-106">內送要求資料與應用程式模型之間的對應是由模型繫結器來處理。</span><span class="sxs-lookup"><span data-stu-id="97c86-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="97c86-107">開發人員可以透過實作自訂模型繫結器，來擴充內建模型繫結功能 (不過一般而言，您並不需要撰寫自己的提供者)。</span><span class="sxs-lookup"><span data-stu-id="97c86-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="97c86-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="97c86-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="97c86-109">預設模型繫結器限制</span><span class="sxs-lookup"><span data-stu-id="97c86-109">Default model binder limitations</span></span>

<span data-ttu-id="97c86-110">預設模型繫結器支援大多數的常見 .NET Core 資料類型，因此應該符合大部分開發人員的需求。</span><span class="sxs-lookup"><span data-stu-id="97c86-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="97c86-111">這些開發人員希望能夠將要求中以文字為主的輸入直接繫結至模型類型。</span><span class="sxs-lookup"><span data-stu-id="97c86-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="97c86-112">輸入可能需要進行轉換才能繫結。</span><span class="sxs-lookup"><span data-stu-id="97c86-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="97c86-113">例如，當您有可用來查詢模型資料的索引鍵時，</span><span class="sxs-lookup"><span data-stu-id="97c86-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="97c86-114">您可以使用自訂模型繫結器，根據索引鍵來擷取資料。</span><span class="sxs-lookup"><span data-stu-id="97c86-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="97c86-115">模型繫結檢閱</span><span class="sxs-lookup"><span data-stu-id="97c86-115">Model binding review</span></span>

<span data-ttu-id="97c86-116">模型繫結使用特定定義來描述其作業類型。</span><span class="sxs-lookup"><span data-stu-id="97c86-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="97c86-117">「簡單型別」是指從輸入中的單一字串進行轉換。</span><span class="sxs-lookup"><span data-stu-id="97c86-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="97c86-118">「複雜類型」是指從多個輸入值進行轉換。</span><span class="sxs-lookup"><span data-stu-id="97c86-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="97c86-119">架構會根據是否有 `TypeConverter` 來判斷是否為不同類型。</span><span class="sxs-lookup"><span data-stu-id="97c86-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="97c86-120">如果您有不需要外部資源的簡單 `string` -> `SomeType` 對應，建議您建立型別轉換器。</span><span class="sxs-lookup"><span data-stu-id="97c86-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="97c86-121">在您建立自己的自訂模型繫結器之前，建議您先檢閱現有模型繫結器的實作方式。</span><span class="sxs-lookup"><span data-stu-id="97c86-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="97c86-122">請考慮 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> 可以用來將 base64 編碼的字串轉換成位元組陣列的。</span><span class="sxs-lookup"><span data-stu-id="97c86-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="97c86-123">位元組陣列通常會儲存為檔案或資料庫 BLOB 欄位。</span><span class="sxs-lookup"><span data-stu-id="97c86-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="97c86-124">使用 ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="97c86-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="97c86-125">Base64 編碼字串可用來代表二進位資料。</span><span class="sxs-lookup"><span data-stu-id="97c86-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="97c86-126">例如，您可以將影像編碼為字串。</span><span class="sxs-lookup"><span data-stu-id="97c86-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="97c86-127">此範例會在 [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt)中包含以 base64 編碼字串形式的影像。</span><span class="sxs-lookup"><span data-stu-id="97c86-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="97c86-128">ASP.NET Core MVC 接受 Base64 編碼字串，並使用 `ByteArrayModelBinder` 將其轉換成位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="97c86-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="97c86-129">將 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` 引數對應至 `ByteArrayModelBinder` ：</span><span class="sxs-lookup"><span data-stu-id="97c86-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="97c86-130">建立您自己的自訂模型系結器時，您可以執行自己的 `IModelBinderProvider` 型別，或使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="97c86-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="97c86-131">下列範例示範如何使用 `ByteArrayModelBinder`，將 Base64 編碼字串轉換成 `byte[]`，並將結果儲存至檔案：</span><span class="sxs-lookup"><span data-stu-id="97c86-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="97c86-132">您可以使用 [Postman](https://www.getpostman.com/) 等工具，將 Base64 編碼字串張貼至此 API 方法：</span><span class="sxs-lookup"><span data-stu-id="97c86-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="97c86-133">![郵遞員](custom-model-binding/images/postman.png "郵遞員")</span><span class="sxs-lookup"><span data-stu-id="97c86-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="97c86-134">只要繫結器可以將要求資料繫結至適當命名的屬性或引數，模型繫結就會成功。</span><span class="sxs-lookup"><span data-stu-id="97c86-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="97c86-135">下列範例示範如何使用具有檢視模型的 `ByteArrayModelBinder`：</span><span class="sxs-lookup"><span data-stu-id="97c86-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="97c86-136">自訂模型繫結器範例</span><span class="sxs-lookup"><span data-stu-id="97c86-136">Custom model binder sample</span></span>

<span data-ttu-id="97c86-137">在本節中，我們會實作自訂模型繫結：</span><span class="sxs-lookup"><span data-stu-id="97c86-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="97c86-138">將內送要求資料轉換成強型別索引鍵引數。</span><span class="sxs-lookup"><span data-stu-id="97c86-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="97c86-139">使用 Entity Framework Core 來擷取相關聯的實體。</span><span class="sxs-lookup"><span data-stu-id="97c86-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="97c86-140">將相關聯的實體當作引數傳遞至動作方法。</span><span class="sxs-lookup"><span data-stu-id="97c86-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="97c86-141">下列範例會在 `Author` 模型上使用 `ModelBinder` 屬性：</span><span class="sxs-lookup"><span data-stu-id="97c86-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="97c86-142">在上述程式碼中，`ModelBinder` 屬性指定應該用來繫結 `Author` 動作參數的 `IModelBinder` 類型。</span><span class="sxs-lookup"><span data-stu-id="97c86-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="97c86-143">下列 `AuthorEntityBinder` 類別可繫結 `Author` 參數，做法是使用 Entity Framework Core 和 `authorId` 從資料來源擷取實體：</span><span class="sxs-lookup"><span data-stu-id="97c86-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="97c86-144">上述 `AuthorEntityBinder` 類別會說明自訂模型繫結器。</span><span class="sxs-lookup"><span data-stu-id="97c86-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="97c86-145">此類別不會說明查閱情節的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="97c86-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="97c86-146">若要進行查閱，請繫結 `authorId`，並在動作方法中查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="97c86-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="97c86-147">此方法會將模型繫結失敗從 `NotFound` 案例中分離。</span><span class="sxs-lookup"><span data-stu-id="97c86-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="97c86-148">下列程式碼示範如何在動作方法中使用 `AuthorEntityBinder`：</span><span class="sxs-lookup"><span data-stu-id="97c86-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="97c86-149">您可以使用 `ModelBinder` 屬性，將 `AuthorEntityBinder` 套用至未使用預設慣例的參數：</span><span class="sxs-lookup"><span data-stu-id="97c86-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="97c86-150">在此範例中，由於引數名稱不是預設的 `authorId`，因此會使用 `ModelBinder` 屬性在參數上指定。</span><span class="sxs-lookup"><span data-stu-id="97c86-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="97c86-151">相較于在動作方法中查閱實體，控制器和動作方法都會簡化。</span><span class="sxs-lookup"><span data-stu-id="97c86-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="97c86-152">使用 Entity Framework Core 擷取作者的邏輯已移至模型繫結器。</span><span class="sxs-lookup"><span data-stu-id="97c86-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="97c86-153">當您有數個繫結至 `Author` 模型的方法時，這樣做會明顯簡化許多。</span><span class="sxs-lookup"><span data-stu-id="97c86-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="97c86-154">您可以將 `ModelBinder` 屬性套用至個別模型屬性 (例如在 ViewModel 上) 或動作方法參數，只指定該類型或動作的特定模型繫結器或模型名稱。</span><span class="sxs-lookup"><span data-stu-id="97c86-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="97c86-155">實作 ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="97c86-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="97c86-156">除了套用屬性，您還可以實作 `IModelBinderProvider`。</span><span class="sxs-lookup"><span data-stu-id="97c86-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="97c86-157">這是內建架構繫結器的實作方式。</span><span class="sxs-lookup"><span data-stu-id="97c86-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="97c86-158">當您指定繫結器的作業類型時，您會指定其所產生的引數類型，而 **不是** 繫結器接受的輸入。</span><span class="sxs-lookup"><span data-stu-id="97c86-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="97c86-159">下列繫結器提供者可搭配 `AuthorEntityBinder` 使用。</span><span class="sxs-lookup"><span data-stu-id="97c86-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="97c86-160">當它新增至提供者的 MVC 集合時，您不需要在 `Author` 或 `Author` 型別參數上使用 `ModelBinder` 屬性。</span><span class="sxs-lookup"><span data-stu-id="97c86-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="97c86-161">注意：上述程式碼會傳回 `BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="97c86-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="97c86-162">`BinderTypeModelBinder` 會作為模型繫結器的 Factory，並提供相依性插入 (DI)。</span><span class="sxs-lookup"><span data-stu-id="97c86-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="97c86-163">`AuthorEntityBinder` 需要 DI 能夠存取 EF Core。</span><span class="sxs-lookup"><span data-stu-id="97c86-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="97c86-164">如果您的模型繫結器需要來自 DI 的服務，請使用 `BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="97c86-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="97c86-165">若要使用自訂模型繫結器提供者，將它新增 `ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="97c86-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="97c86-166">評估模型繫結器時，會依序查看提供者集合，</span><span class="sxs-lookup"><span data-stu-id="97c86-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="97c86-167">使用的第一個提供者會傳回符合輸入模型的系結器。</span><span class="sxs-lookup"><span data-stu-id="97c86-167">The first provider that returns a binder that matches the input model is used.</span></span> <span data-ttu-id="97c86-168">將您的提供者加入至集合結尾可能會導致在您的自訂系結器有機會之前呼叫內建模型系結器。</span><span class="sxs-lookup"><span data-stu-id="97c86-168">Adding your provider to the end of the collection may thus result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="97c86-169">在此範例中，自訂提供者會新增至集合的開頭，以確保一律用於 `Author` 動作引數。</span><span class="sxs-lookup"><span data-stu-id="97c86-169">In this example, the custom provider is added to the beginning of the collection to ensure it's always used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="97c86-170">多型模型系結</span><span class="sxs-lookup"><span data-stu-id="97c86-170">Polymorphic model binding</span></span>

<span data-ttu-id="97c86-171">系結至不同的衍生型別模型稱為多型模型系結。</span><span class="sxs-lookup"><span data-stu-id="97c86-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="97c86-172">必須將要求值系結至特定的衍生模型類型時，才需要多型自訂模型系結。</span><span class="sxs-lookup"><span data-stu-id="97c86-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="97c86-173">多型模型系結：</span><span class="sxs-lookup"><span data-stu-id="97c86-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="97c86-174">對於設計來與所有語言交互操作的 REST API 並不常見。</span><span class="sxs-lookup"><span data-stu-id="97c86-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="97c86-175">使得系結模型的相關原因變得很困難。</span><span class="sxs-lookup"><span data-stu-id="97c86-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="97c86-176">但是，如果應用程式需要多型模型系結，則可能會如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="97c86-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="97c86-177">建議與最佳做法</span><span class="sxs-lookup"><span data-stu-id="97c86-177">Recommendations and best practices</span></span>

<span data-ttu-id="97c86-178">自訂模型繫結器：</span><span class="sxs-lookup"><span data-stu-id="97c86-178">Custom model binders:</span></span>

- <span data-ttu-id="97c86-179">不應該嘗試設定狀態碼或傳回結果 (例如 404 找不到)。</span><span class="sxs-lookup"><span data-stu-id="97c86-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="97c86-180">如果模型繫結失敗，[動作篩選](xref:mvc/controllers/filters)或動作方法本身內的邏輯應該會處理失敗。</span><span class="sxs-lookup"><span data-stu-id="97c86-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="97c86-181">最適合用來排除動作方法中的重複程式碼和交叉關注。</span><span class="sxs-lookup"><span data-stu-id="97c86-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="97c86-182">一般而言，不應該用來將字串轉換成自訂類型，<xref:System.ComponentModel.TypeConverter> 通常是較好的選擇。</span><span class="sxs-lookup"><span data-stu-id="97c86-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="97c86-183">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="97c86-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="97c86-184">模型繫結可直接透過模型類型 (傳入作為方法引數) 來執行控制器動作，而不用透過 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="97c86-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="97c86-185">內送要求資料與應用程式模型之間的對應是由模型繫結器來處理。</span><span class="sxs-lookup"><span data-stu-id="97c86-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="97c86-186">開發人員可以透過實作自訂模型繫結器，來擴充內建模型繫結功能 (不過一般而言，您並不需要撰寫自己的提供者)。</span><span class="sxs-lookup"><span data-stu-id="97c86-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="97c86-187">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="97c86-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="97c86-188">預設模型繫結器限制</span><span class="sxs-lookup"><span data-stu-id="97c86-188">Default model binder limitations</span></span>

<span data-ttu-id="97c86-189">預設模型繫結器支援大多數的常見 .NET Core 資料類型，因此應該符合大部分開發人員的需求。</span><span class="sxs-lookup"><span data-stu-id="97c86-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="97c86-190">這些開發人員希望能夠將要求中以文字為主的輸入直接繫結至模型類型。</span><span class="sxs-lookup"><span data-stu-id="97c86-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="97c86-191">輸入可能需要進行轉換才能繫結。</span><span class="sxs-lookup"><span data-stu-id="97c86-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="97c86-192">例如，當您有可用來查詢模型資料的索引鍵時，</span><span class="sxs-lookup"><span data-stu-id="97c86-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="97c86-193">您可以使用自訂模型繫結器，根據索引鍵來擷取資料。</span><span class="sxs-lookup"><span data-stu-id="97c86-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="97c86-194">模型繫結檢閱</span><span class="sxs-lookup"><span data-stu-id="97c86-194">Model binding review</span></span>

<span data-ttu-id="97c86-195">模型繫結使用特定定義來描述其作業類型。</span><span class="sxs-lookup"><span data-stu-id="97c86-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="97c86-196">「簡單型別」是指從輸入中的單一字串進行轉換。</span><span class="sxs-lookup"><span data-stu-id="97c86-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="97c86-197">「複雜類型」是指從多個輸入值進行轉換。</span><span class="sxs-lookup"><span data-stu-id="97c86-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="97c86-198">架構會根據是否有 `TypeConverter` 來判斷是否為不同類型。</span><span class="sxs-lookup"><span data-stu-id="97c86-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="97c86-199">如果您有不需要外部資源的簡單 `string` -> `SomeType` 對應，建議您建立型別轉換器。</span><span class="sxs-lookup"><span data-stu-id="97c86-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="97c86-200">在您建立自己的自訂模型繫結器之前，建議您先檢閱現有模型繫結器的實作方式。</span><span class="sxs-lookup"><span data-stu-id="97c86-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="97c86-201">請考慮 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> 可以用來將 base64 編碼的字串轉換成位元組陣列的。</span><span class="sxs-lookup"><span data-stu-id="97c86-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="97c86-202">位元組陣列通常會儲存為檔案或資料庫 BLOB 欄位。</span><span class="sxs-lookup"><span data-stu-id="97c86-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="97c86-203">使用 ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="97c86-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="97c86-204">Base64 編碼字串可用來代表二進位資料。</span><span class="sxs-lookup"><span data-stu-id="97c86-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="97c86-205">例如，您可以將影像編碼為字串。</span><span class="sxs-lookup"><span data-stu-id="97c86-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="97c86-206">此範例會在 [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt)中包含以 base64 編碼字串形式的影像。</span><span class="sxs-lookup"><span data-stu-id="97c86-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="97c86-207">ASP.NET Core MVC 接受 Base64 編碼字串，並使用 `ByteArrayModelBinder` 將其轉換成位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="97c86-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="97c86-208">將 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> `byte[]` 引數對應至 `ByteArrayModelBinder` ：</span><span class="sxs-lookup"><span data-stu-id="97c86-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="97c86-209">建立您自己的自訂模型系結器時，您可以執行自己的 `IModelBinderProvider` 型別，或使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="97c86-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="97c86-210">下列範例示範如何使用 `ByteArrayModelBinder`，將 Base64 編碼字串轉換成 `byte[]`，並將結果儲存至檔案：</span><span class="sxs-lookup"><span data-stu-id="97c86-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="97c86-211">您可以使用 [Postman](https://www.getpostman.com/) 等工具，將 Base64 編碼字串張貼至此 API 方法：</span><span class="sxs-lookup"><span data-stu-id="97c86-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="97c86-212">![郵遞員](custom-model-binding/images/postman.png "郵遞員")</span><span class="sxs-lookup"><span data-stu-id="97c86-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="97c86-213">只要繫結器可以將要求資料繫結至適當命名的屬性或引數，模型繫結就會成功。</span><span class="sxs-lookup"><span data-stu-id="97c86-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="97c86-214">下列範例示範如何使用具有檢視模型的 `ByteArrayModelBinder`：</span><span class="sxs-lookup"><span data-stu-id="97c86-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="97c86-215">自訂模型繫結器範例</span><span class="sxs-lookup"><span data-stu-id="97c86-215">Custom model binder sample</span></span>

<span data-ttu-id="97c86-216">在本節中，我們會實作自訂模型繫結：</span><span class="sxs-lookup"><span data-stu-id="97c86-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="97c86-217">將內送要求資料轉換成強型別索引鍵引數。</span><span class="sxs-lookup"><span data-stu-id="97c86-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="97c86-218">使用 Entity Framework Core 來擷取相關聯的實體。</span><span class="sxs-lookup"><span data-stu-id="97c86-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="97c86-219">將相關聯的實體當作引數傳遞至動作方法。</span><span class="sxs-lookup"><span data-stu-id="97c86-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="97c86-220">下列範例會在 `Author` 模型上使用 `ModelBinder` 屬性：</span><span class="sxs-lookup"><span data-stu-id="97c86-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="97c86-221">在上述程式碼中，`ModelBinder` 屬性指定應該用來繫結 `Author` 動作參數的 `IModelBinder` 類型。</span><span class="sxs-lookup"><span data-stu-id="97c86-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="97c86-222">下列 `AuthorEntityBinder` 類別可繫結 `Author` 參數，做法是使用 Entity Framework Core 和 `authorId` 從資料來源擷取實體：</span><span class="sxs-lookup"><span data-stu-id="97c86-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="97c86-223">上述 `AuthorEntityBinder` 類別會說明自訂模型繫結器。</span><span class="sxs-lookup"><span data-stu-id="97c86-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="97c86-224">此類別不會說明查閱情節的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="97c86-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="97c86-225">若要進行查閱，請繫結 `authorId`，並在動作方法中查詢資料庫。</span><span class="sxs-lookup"><span data-stu-id="97c86-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="97c86-226">此方法會將模型繫結失敗從 `NotFound` 案例中分離。</span><span class="sxs-lookup"><span data-stu-id="97c86-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="97c86-227">下列程式碼示範如何在動作方法中使用 `AuthorEntityBinder`：</span><span class="sxs-lookup"><span data-stu-id="97c86-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="97c86-228">您可以使用 `ModelBinder` 屬性，將 `AuthorEntityBinder` 套用至未使用預設慣例的參數：</span><span class="sxs-lookup"><span data-stu-id="97c86-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="97c86-229">在此範例中，由於引數名稱不是預設的 `authorId`，因此會使用 `ModelBinder` 屬性在參數上指定。</span><span class="sxs-lookup"><span data-stu-id="97c86-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="97c86-230">相較于在動作方法中查閱實體，控制器和動作方法都會簡化。</span><span class="sxs-lookup"><span data-stu-id="97c86-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="97c86-231">使用 Entity Framework Core 擷取作者的邏輯已移至模型繫結器。</span><span class="sxs-lookup"><span data-stu-id="97c86-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="97c86-232">當您有數個繫結至 `Author` 模型的方法時，這樣做會明顯簡化許多。</span><span class="sxs-lookup"><span data-stu-id="97c86-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="97c86-233">您可以將 `ModelBinder` 屬性套用至個別模型屬性 (例如在 ViewModel 上) 或動作方法參數，只指定該類型或動作的特定模型繫結器或模型名稱。</span><span class="sxs-lookup"><span data-stu-id="97c86-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="97c86-234">實作 ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="97c86-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="97c86-235">除了套用屬性，您還可以實作 `IModelBinderProvider`。</span><span class="sxs-lookup"><span data-stu-id="97c86-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="97c86-236">這是內建架構繫結器的實作方式。</span><span class="sxs-lookup"><span data-stu-id="97c86-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="97c86-237">當您指定繫結器的作業類型時，您會指定其所產生的引數類型，而 **不是** 繫結器接受的輸入。</span><span class="sxs-lookup"><span data-stu-id="97c86-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="97c86-238">下列繫結器提供者可搭配 `AuthorEntityBinder` 使用。</span><span class="sxs-lookup"><span data-stu-id="97c86-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="97c86-239">當它新增至提供者的 MVC 集合時，您不需要在 `Author` 或 `Author` 型別參數上使用 `ModelBinder` 屬性。</span><span class="sxs-lookup"><span data-stu-id="97c86-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="97c86-240">注意：上述程式碼會傳回 `BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="97c86-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="97c86-241">`BinderTypeModelBinder` 會作為模型繫結器的 Factory，並提供相依性插入 (DI)。</span><span class="sxs-lookup"><span data-stu-id="97c86-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="97c86-242">`AuthorEntityBinder` 需要 DI 能夠存取 EF Core。</span><span class="sxs-lookup"><span data-stu-id="97c86-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="97c86-243">如果您的模型繫結器需要來自 DI 的服務，請使用 `BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="97c86-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="97c86-244">若要使用自訂模型繫結器提供者，將它新增 `ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="97c86-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="97c86-245">評估模型繫結器時，會依序查看提供者集合，</span><span class="sxs-lookup"><span data-stu-id="97c86-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="97c86-246">並使用第一個傳回繫結器的提供者。</span><span class="sxs-lookup"><span data-stu-id="97c86-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="97c86-247">將您的提供者新增集合結尾可能會導致在有機會呼叫自訂繫結器之前，即呼叫內建模型繫結器。</span><span class="sxs-lookup"><span data-stu-id="97c86-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="97c86-248">在此範例中，會將自訂提供者新增集合開頭，以確保其用於 `Author` 動作引數。</span><span class="sxs-lookup"><span data-stu-id="97c86-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="97c86-249">多型模型系結</span><span class="sxs-lookup"><span data-stu-id="97c86-249">Polymorphic model binding</span></span>

<span data-ttu-id="97c86-250">系結至不同的衍生型別模型稱為多型模型系結。</span><span class="sxs-lookup"><span data-stu-id="97c86-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="97c86-251">必須將要求值系結至特定的衍生模型類型時，才需要多型自訂模型系結。</span><span class="sxs-lookup"><span data-stu-id="97c86-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="97c86-252">多型模型系結：</span><span class="sxs-lookup"><span data-stu-id="97c86-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="97c86-253">對於設計來與所有語言交互操作的 REST API 並不常見。</span><span class="sxs-lookup"><span data-stu-id="97c86-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="97c86-254">使得系結模型的相關原因變得很困難。</span><span class="sxs-lookup"><span data-stu-id="97c86-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="97c86-255">但是，如果應用程式需要多型模型系結，則可能會如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="97c86-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="97c86-256">建議與最佳做法</span><span class="sxs-lookup"><span data-stu-id="97c86-256">Recommendations and best practices</span></span>

<span data-ttu-id="97c86-257">自訂模型繫結器：</span><span class="sxs-lookup"><span data-stu-id="97c86-257">Custom model binders:</span></span>

- <span data-ttu-id="97c86-258">不應該嘗試設定狀態碼或傳回結果 (例如 404 找不到)。</span><span class="sxs-lookup"><span data-stu-id="97c86-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="97c86-259">如果模型繫結失敗，[動作篩選](xref:mvc/controllers/filters)或動作方法本身內的邏輯應該會處理失敗。</span><span class="sxs-lookup"><span data-stu-id="97c86-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="97c86-260">最適合用來排除動作方法中的重複程式碼和交叉關注。</span><span class="sxs-lookup"><span data-stu-id="97c86-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="97c86-261">一般而言，不應該用來將字串轉換成自訂類型，<xref:System.ComponentModel.TypeConverter> 通常是較好的選擇。</span><span class="sxs-lookup"><span data-stu-id="97c86-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
