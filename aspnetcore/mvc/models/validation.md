---
title: ASP.NET Core MVC 中的模型驗證
author: rick-anderson
description: 瞭解 ASP.NET Core MVC 和頁面中的模型驗證 Razor 。
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: 21f2a65bd93c08f16de988381e648768debde438
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605590"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="c2436-103">ASP.NET 核心 MVC 和頁面中的模型驗證 Razor</span><span class="sxs-lookup"><span data-stu-id="c2436-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2436-104">依 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c2436-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="c2436-105">本文說明如何在 ASP.NET Core MVC 或 Pages 應用程式中驗證使用者輸入 Razor 。</span><span class="sxs-lookup"><span data-stu-id="c2436-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="c2436-106">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/samples) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c2436-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="c2436-107">模型狀態</span><span class="sxs-lookup"><span data-stu-id="c2436-107">Model state</span></span>

<span data-ttu-id="c2436-108">模型狀態代表來自兩個子系統的錯誤：模型繫結和模型驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="c2436-109">源自 [模型](model-binding.md) 系結的錯誤通常是資料轉換錯誤。</span><span class="sxs-lookup"><span data-stu-id="c2436-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="c2436-110">例如，在整數位段中輸入 "x"。</span><span class="sxs-lookup"><span data-stu-id="c2436-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="c2436-111">模型驗證會在模型系結之後發生，並報告資料不符合商務規則的錯誤。</span><span class="sxs-lookup"><span data-stu-id="c2436-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="c2436-112">例如，在需要1到5之間評等的欄位中輸入0。</span><span class="sxs-lookup"><span data-stu-id="c2436-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="c2436-113">模型系結和模型驗證都是在執行控制器動作或 Razor 頁面處理常式方法之前進行。</span><span class="sxs-lookup"><span data-stu-id="c2436-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="c2436-114">針對 Web 應用程式，此應用程式的責任為檢查 `ModelState.IsValid` 並做出適當回應。</span><span class="sxs-lookup"><span data-stu-id="c2436-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="c2436-115">Web 應用程式通常會重新顯示頁面，並出現錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="c2436-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="c2436-116">如果 Web API 控制器具有 `[ApiController]` 屬性，則該控制器就不需要檢查 `ModelState.IsValid`。</span><span class="sxs-lookup"><span data-stu-id="c2436-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="c2436-117">在此情況下，當模型狀態無效時，會傳回包含錯誤詳細資料的自動 HTTP 400 回應。</span><span class="sxs-lookup"><span data-stu-id="c2436-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="c2436-118">如需詳細資訊，請參閱[自動 HTTP 400 回應](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="c2436-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="c2436-119">重新執行驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-119">Rerun validation</span></span>

<span data-ttu-id="c2436-120">驗證會自動進行，但建議您以手動方式重複進行。</span><span class="sxs-lookup"><span data-stu-id="c2436-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="c2436-121">例如，您可能會計算屬性的值，並在將屬性設定為計算的值之後重新執行驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="c2436-122">若要重新執行驗證，請呼叫 `TryValidateModel` 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="c2436-123">驗證屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-123">Validation attributes</span></span>

<span data-ttu-id="c2436-124">驗證屬性可讓您指定模型屬性的驗證規則。</span><span class="sxs-lookup"><span data-stu-id="c2436-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="c2436-125">下列來自範例應用程式的範例，顯示以驗證屬性標註的模型類別。</span><span class="sxs-lookup"><span data-stu-id="c2436-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="c2436-126">`[ClassicMovie]` 屬性是自訂驗證屬性，其他的則是內建。</span><span class="sxs-lookup"><span data-stu-id="c2436-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="c2436-127">未顯示的是 `[ClassicMovieWithClientValidator]` 。</span><span class="sxs-lookup"><span data-stu-id="c2436-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="c2436-128">`[ClassicMovieWithClientValidator]` 顯示執行自訂屬性的替代方法。</span><span class="sxs-lookup"><span data-stu-id="c2436-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="c2436-129">內建屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-129">Built-in attributes</span></span>

<span data-ttu-id="c2436-130">下列是部分內建驗證屬性：</span><span class="sxs-lookup"><span data-stu-id="c2436-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="c2436-131">`[CreditCard]`：驗證屬性是否具有信用卡格式。</span><span class="sxs-lookup"><span data-stu-id="c2436-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="c2436-132">需要 [JQuery 驗證的其他方法](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js)。</span><span class="sxs-lookup"><span data-stu-id="c2436-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="c2436-133">`[Compare]`：驗證模型中的兩個屬性是否相符。</span><span class="sxs-lookup"><span data-stu-id="c2436-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="c2436-134">`[EmailAddress]`：驗證屬性具有電子郵件格式。</span><span class="sxs-lookup"><span data-stu-id="c2436-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="c2436-135">`[Phone]`：驗證屬性具有電話號碼格式。</span><span class="sxs-lookup"><span data-stu-id="c2436-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="c2436-136">`[Range]`：驗證屬性值是否落在指定的範圍內。</span><span class="sxs-lookup"><span data-stu-id="c2436-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="c2436-137">`[RegularExpression]`：驗證屬性值是否符合指定的正則運算式。</span><span class="sxs-lookup"><span data-stu-id="c2436-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="c2436-138">`[Required]`：驗證欄位不是 null。</span><span class="sxs-lookup"><span data-stu-id="c2436-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="c2436-139">如需此屬性行為的詳細資訊，請參閱[ `[Required]` 屬性](#required-attribute)。</span><span class="sxs-lookup"><span data-stu-id="c2436-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="c2436-140">`[StringLength]`：驗證字串屬性值不超過指定的長度限制。</span><span class="sxs-lookup"><span data-stu-id="c2436-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="c2436-141">`[Url]`：驗證屬性是否具有 URL 格式。</span><span class="sxs-lookup"><span data-stu-id="c2436-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="c2436-142">`[Remote]`：在伺服器上呼叫動作方法，以驗證用戶端上的輸入。</span><span class="sxs-lookup"><span data-stu-id="c2436-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="c2436-143">如需此屬性行為的詳細資訊，請參閱[ `[Remote]` 屬性](#remote-attribute)。</span><span class="sxs-lookup"><span data-stu-id="c2436-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="c2436-144">您可以在 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 命名空間中找到驗證屬性的完整清單。</span><span class="sxs-lookup"><span data-stu-id="c2436-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="c2436-145">錯誤訊息</span><span class="sxs-lookup"><span data-stu-id="c2436-145">Error messages</span></span>

<span data-ttu-id="c2436-146">驗證屬性可讓您指定要顯示的無效輸入錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="c2436-147">例如：</span><span class="sxs-lookup"><span data-stu-id="c2436-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="c2436-148">就內部而言，屬性會使用欄位名稱的預留位置呼叫 `String.Format`，有時候也會使用其他預留位置。</span><span class="sxs-lookup"><span data-stu-id="c2436-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="c2436-149">例如：</span><span class="sxs-lookup"><span data-stu-id="c2436-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="c2436-150">套用至 `Name` 屬性時，透過上述程式碼建立的錯誤訊息會是 「名稱長度必須介於 6 和 8 之間」。</span><span class="sxs-lookup"><span data-stu-id="c2436-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="c2436-151">若要找出哪些參數傳遞給 `String.Format` 以取得特定屬性的錯誤訊息，請參閱 [DataAnnotations 原始程式碼](https://github.com/dotnet/runtime/tree/main/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="c2436-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/main/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="non-nullable-reference-types-and-required-attribute"></a><span data-ttu-id="c2436-152">不可為 null 的參考型別和 [必要] 屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-152">Non-nullable reference types and [Required] attribute</span></span>

<span data-ttu-id="c2436-153">驗證系統會將不可為 null 的參數或系結屬性視為有 `[Required]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-153">The validation system treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="c2436-154">藉由啟用內容，MVC 會以隱含方式開始驗證不可為 null 的屬性或參數，如同它們已使用屬性來進行屬性 [ `Nullable` ](/dotnet/csharp/nullable-references#nullable-contexts) `[Required]` 。</span><span class="sxs-lookup"><span data-stu-id="c2436-154">By [enabling `Nullable` contexts](/dotnet/csharp/nullable-references#nullable-contexts), MVC implicitly starts validating non-nullable properties or parameters as if they had been attributed with the `[Required]` attribute.</span></span> <span data-ttu-id="c2436-155">請考慮下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="c2436-155">Consider the following code:</span></span>

```csharp
public class Person
{
    public string Name { get; set; }
}
```

<span data-ttu-id="c2436-156">如果已使用建立應用程式 `<Nullable>enable</Nullable>` ，則 `Name` JSON 或表單 post 中的遺漏值會導致驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="c2436-156">If the app was built with `<Nullable>enable</Nullable>`, a missing value for `Name` in a JSON or form post results in a validation error.</span></span> <span data-ttu-id="c2436-157">使用可為 null 的參考型別，以允許為屬性指定 null 或遺漏值 `Name` ：</span><span class="sxs-lookup"><span data-stu-id="c2436-157">Use a nullable reference type to allow null or missing values to be specified for the `Name` property:</span></span>

```csharp
public class Person
{
    public string? Name { get; set; }
}
```

<span data-ttu-id="c2436-158">.</span><span class="sxs-lookup"><span data-stu-id="c2436-158">.</span></span> <span data-ttu-id="c2436-159">您可以在下列設定中停用此行為 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c2436-159">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="c2436-160">[必要] 在伺服器上執行驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-160">[Required] validation on the server</span></span>

<span data-ttu-id="c2436-161">在伺服器上，如果必要的值屬性為 Null，則會視為遺失。</span><span class="sxs-lookup"><span data-stu-id="c2436-161">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="c2436-162">不可為 null 的欄位一律有效，且永遠不會 `[Required]` 顯示該屬性的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-162">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="c2436-163">不過，不可為 Null 屬性的模型繫結可能會失敗，並產生一則錯誤訊息，例如 `The value '' is invalid`。</span><span class="sxs-lookup"><span data-stu-id="c2436-163">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="c2436-164">若要針對不可為 Null 型別的伺服器端驗證指定自訂錯誤訊息，您可以使用下列選項：</span><span class="sxs-lookup"><span data-stu-id="c2436-164">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="c2436-165">將欄位設成可為 Null (例如 `decimal?`，而不是 `decimal`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-165">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="c2436-166">[可 \<T> 為 null](/dotnet/csharp/programming-guide/nullable-types/)實值型別會視為標準可為 null 的類型。</span><span class="sxs-lookup"><span data-stu-id="c2436-166">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="c2436-167">指定模型繫結要使用的預設錯誤訊息，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-167">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="c2436-168">如需您可以為其設定預設訊息之模型繫結錯誤的詳細資訊，請參閱 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>。</span><span class="sxs-lookup"><span data-stu-id="c2436-168">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="c2436-169">[必要] 在用戶端上執行驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-169">[Required] validation on the client</span></span>

<span data-ttu-id="c2436-170">不可為 Null 的型別和字串，會在用戶端上以不同於在伺服器上的方式處理。</span><span class="sxs-lookup"><span data-stu-id="c2436-170">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="c2436-171">在用戶端上：</span><span class="sxs-lookup"><span data-stu-id="c2436-171">On the client:</span></span>

* <span data-ttu-id="c2436-172">只有在為值的輸入進行輸入後，才會將該值視為存在。</span><span class="sxs-lookup"><span data-stu-id="c2436-172">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="c2436-173">因此，用戶端驗證處理非 Null 型別的方式，會與可為 Null 型別的方式相同。</span><span class="sxs-lookup"><span data-stu-id="c2436-173">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="c2436-174">字串欄位中的空白字元，會以 jQuery 驗證[必要](https://jqueryvalidation.org/required-method/)方法視為有效的輸入。</span><span class="sxs-lookup"><span data-stu-id="c2436-174">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="c2436-175">如果僅輸入空白字元，則伺服器端驗證會將必要的字串欄位視為無效。</span><span class="sxs-lookup"><span data-stu-id="c2436-175">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="c2436-176">如先前所述，不可為 Null 的型別會視為具有 `[Required]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-176">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="c2436-177">這表示即使您不套用 `[Required]` 屬性，也可以取得用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-177">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="c2436-178">但是，如果您不使用該屬性，就會出現預設的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-178">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="c2436-179">若要指定自訂錯誤訊息，請使用該屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-179">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="c2436-180">[遠端] 屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-180">[Remote] attribute</span></span>

<span data-ttu-id="c2436-181">`[Remote]` 屬性會實作用戶端驗證，該驗證需要呼叫伺服器上的方法來判斷欄位輸入是否有效。</span><span class="sxs-lookup"><span data-stu-id="c2436-181">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="c2436-182">例如，應用程式可能需要驗證使用者名稱是否已經在使用中。</span><span class="sxs-lookup"><span data-stu-id="c2436-182">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="c2436-183">實作遠端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-183">To implement remote validation:</span></span>

1. <span data-ttu-id="c2436-184">為 JavaScript 建立動作方法來呼叫。</span><span class="sxs-lookup"><span data-stu-id="c2436-184">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="c2436-185">JQuery 驗證 [遠端](https://jqueryvalidation.org/remote-method/) 方法需要 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="c2436-185">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="c2436-186">`true` 表示輸入資料有效。</span><span class="sxs-lookup"><span data-stu-id="c2436-186">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="c2436-187">`false`、`undefined` 或 `null` 表示輸入無效。</span><span class="sxs-lookup"><span data-stu-id="c2436-187">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="c2436-188">顯示預設錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-188">Display the default error message.</span></span>
   * <span data-ttu-id="c2436-189">任何其他字串都表示輸入無效。</span><span class="sxs-lookup"><span data-stu-id="c2436-189">Any other string means the input is invalid.</span></span> <span data-ttu-id="c2436-190">將字串顯示為自訂錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-190">Display the string as a custom error message.</span></span>

   <span data-ttu-id="c2436-191">自訂錯誤訊息的動作方法範例如下：</span><span class="sxs-lookup"><span data-stu-id="c2436-191">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="c2436-192">在模型類別中，使用指向驗證動作方法的 `[Remote]` 屬性 (Attribute) 來標註屬性 (Property)，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-192">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="c2436-193">`[Remote]` 屬性位於 `Microsoft.AspNetCore.Mvc` 命名空間。</span><span class="sxs-lookup"><span data-stu-id="c2436-193">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="c2436-194">其他欄位</span><span class="sxs-lookup"><span data-stu-id="c2436-194">Additional fields</span></span>

<span data-ttu-id="c2436-195">`[Remote]` 屬性 (Attribute) 的 `AdditionalFields` 屬性 (Property) 可讓您針對伺服器上的資料來驗證欄位組合。</span><span class="sxs-lookup"><span data-stu-id="c2436-195">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="c2436-196">例如，如果 `User` 模型具 `FirstName` 和 `LastName` 屬性，建議您確認沒有任何現有使用者已具有該組名稱。</span><span class="sxs-lookup"><span data-stu-id="c2436-196">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="c2436-197">下列範例顯示如何使用 `AdditionalFields`：</span><span class="sxs-lookup"><span data-stu-id="c2436-197">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="c2436-198">`AdditionalFields` 可以明確地設定為 "FirstName" 和 "LastName" 字串，但使用 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 運算子可簡化稍後的重構。</span><span class="sxs-lookup"><span data-stu-id="c2436-198">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="c2436-199">這項驗證的動作方法必須接受 `firstName` 和 `lastName` 引數：</span><span class="sxs-lookup"><span data-stu-id="c2436-199">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="c2436-200">當使用者輸入名字或姓氏時，JavaScript 會進行遠端呼叫以查看該組名稱是否已在使用中。</span><span class="sxs-lookup"><span data-stu-id="c2436-200">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="c2436-201">若要驗證兩個或多個其他欄位，請以逗號分隔清單來提供這些欄位。</span><span class="sxs-lookup"><span data-stu-id="c2436-201">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="c2436-202">例如，若要將 `MiddleName` 屬性 (Property) 新增至模型，請設定 `[Remote]` 屬性 (Attribute)，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-202">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="c2436-203">如同所有屬性引數，`AdditionalFields` 必須是常數運算式。</span><span class="sxs-lookup"><span data-stu-id="c2436-203">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="c2436-204">因此，請勿使用[內插字串](/dotnet/csharp/language-reference/keywords/interpolated-strings)或呼叫 <xref:System.String.Join*> 來初始化 `AdditionalFields`。</span><span class="sxs-lookup"><span data-stu-id="c2436-204">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="c2436-205">內建屬性的替代項目</span><span class="sxs-lookup"><span data-stu-id="c2436-205">Alternatives to built-in attributes</span></span>

<span data-ttu-id="c2436-206">如果您需要內建屬性未提供的驗證，您可以：</span><span class="sxs-lookup"><span data-stu-id="c2436-206">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="c2436-207">[建立自訂屬性](#custom-attributes)。</span><span class="sxs-lookup"><span data-stu-id="c2436-207">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="c2436-208">[實作 IValidatableObject](#ivalidatableobject)。</span><span class="sxs-lookup"><span data-stu-id="c2436-208">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="c2436-209">自訂屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-209">Custom attributes</span></span>

<span data-ttu-id="c2436-210">在內建驗證屬性無法處理的情況下，您可以建立自訂驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-210">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="c2436-211">建立繼承自 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> 的類別，並覆寫 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 方法。</span><span class="sxs-lookup"><span data-stu-id="c2436-211">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="c2436-212">`IsValid` 方法會接受名為 *value* 的物件，這是要驗證的輸入。</span><span class="sxs-lookup"><span data-stu-id="c2436-212">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="c2436-213">多載也會接受 `ValidationContext` 物件，該物件會提供其他資訊，例如模型繫結所建立的模型執行個體。</span><span class="sxs-lookup"><span data-stu-id="c2436-213">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="c2436-214">下列範例會驗證 *Classic* 內容類型中電影的發行日期，不晚於指定的年份。</span><span class="sxs-lookup"><span data-stu-id="c2436-214">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="c2436-215">`[ClassicMovie]`屬性：</span><span class="sxs-lookup"><span data-stu-id="c2436-215">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="c2436-216">只會在伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="c2436-216">Is only run on the server.</span></span>
* <span data-ttu-id="c2436-217">若是傳統電影，則會驗證發行日期：</span><span class="sxs-lookup"><span data-stu-id="c2436-217">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="c2436-218">上述範例中的 `movie` 變數代表 `Movie` 物件，該物件包含表單送出中的資料。</span><span class="sxs-lookup"><span data-stu-id="c2436-218">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="c2436-219">驗證失敗時，會傳回 `ValidationResult` 和錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-219">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="c2436-220">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="c2436-220">IValidatableObject</span></span>

<span data-ttu-id="c2436-221">上述範例僅適用於 `Movie` 型別。</span><span class="sxs-lookup"><span data-stu-id="c2436-221">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="c2436-222">類別層級驗證的另一個選項，是在模型類別中實作 `IValidatableObject`，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-222">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="c2436-223">最上層節點驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-223">Top-level node validation</span></span>

<span data-ttu-id="c2436-224">最上層節點包括：</span><span class="sxs-lookup"><span data-stu-id="c2436-224">Top-level nodes include:</span></span>

* <span data-ttu-id="c2436-225">動作參數</span><span class="sxs-lookup"><span data-stu-id="c2436-225">Action parameters</span></span>
* <span data-ttu-id="c2436-226">控制器屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-226">Controller properties</span></span>
* <span data-ttu-id="c2436-227">頁面處理常式參數</span><span class="sxs-lookup"><span data-stu-id="c2436-227">Page handler parameters</span></span>
* <span data-ttu-id="c2436-228">頁面模型屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-228">Page model properties</span></span>

<span data-ttu-id="c2436-229">除了驗證模型屬性，也會驗證模型所繫結的最上層節點。</span><span class="sxs-lookup"><span data-stu-id="c2436-229">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="c2436-230">在來自範例應用程式的下列範例中，`VerifyPhone` 方法會使用 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> 來驗證 `phone` 動作參數：</span><span class="sxs-lookup"><span data-stu-id="c2436-230">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="c2436-231">最上層節點可以搭配使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> 和驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-231">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="c2436-232">在來自範例應用程式的下列範例中，`CheckAge` 方法會指定提交表單時必須從查詢字串繫結 `age` 參數：</span><span class="sxs-lookup"><span data-stu-id="c2436-232">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="c2436-233">在 [檢查年齡] 頁面 (*CheckAge.cshtml*) 中，有兩個表單。</span><span class="sxs-lookup"><span data-stu-id="c2436-233">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="c2436-234">第一個表單會將 `Age` 的值提交 `99` 為查詢字串參數： `https://localhost:5001/Users/CheckAge?Age=99` 。</span><span class="sxs-lookup"><span data-stu-id="c2436-234">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="c2436-235">從查詢字串提交正確格式化的 `age` 時，即會驗證表單。</span><span class="sxs-lookup"><span data-stu-id="c2436-235">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="c2436-236">[檢查年齡] 頁面上的第二個表單會在要求本文中提交 `Age` 值，而且驗證會失敗。</span><span class="sxs-lookup"><span data-stu-id="c2436-236">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="c2436-237">由於 `age` 參數必須來自查詢字串，因此繫結會失敗。</span><span class="sxs-lookup"><span data-stu-id="c2436-237">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="c2436-238">最大錯誤數</span><span class="sxs-lookup"><span data-stu-id="c2436-238">Maximum errors</span></span>

<span data-ttu-id="c2436-239">達到最大錯誤數 (預設為 200) 時，就會停止驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-239">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="c2436-240">您可以在 `Startup.ConfigureServices` 中使用下列程式碼來設定此數目：</span><span class="sxs-lookup"><span data-stu-id="c2436-240">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="c2436-241">最大遞迴</span><span class="sxs-lookup"><span data-stu-id="c2436-241">Maximum recursion</span></span>

<span data-ttu-id="c2436-242"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> 會周遊所正驗證之模型的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="c2436-242"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="c2436-243">針對深層或無限遞迴的模型，驗證可能會導致堆疊溢位。</span><span class="sxs-lookup"><span data-stu-id="c2436-243">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="c2436-244">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) 可用來在訪客遞迴超過設定的深度時，提早停止驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-244">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="c2436-245">的預設值 `MvcOptions.MaxValidationDepth` 為32。</span><span class="sxs-lookup"><span data-stu-id="c2436-245">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="c2436-246">自動最少運算</span><span class="sxs-lookup"><span data-stu-id="c2436-246">Automatic short-circuit</span></span>

<span data-ttu-id="c2436-247">如果模型圖形不需要驗證，則驗證會自動進行最少運算 (略過)。</span><span class="sxs-lookup"><span data-stu-id="c2436-247">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="c2436-248">執行階段會略過驗證的物件，包含基本集合 (例如 `byte[]`、`string[]`、`Dictionary<string, string>`) 和沒有任何驗證程式的複雜物件圖形。</span><span class="sxs-lookup"><span data-stu-id="c2436-248">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="c2436-249">停用驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-249">Disable validation</span></span>

<span data-ttu-id="c2436-250">停用驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-250">To disable validation:</span></span>

1. <span data-ttu-id="c2436-251">建立不會將任何欄位標記為無效的 `IObjectModelValidator` 實作。</span><span class="sxs-lookup"><span data-stu-id="c2436-251">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="c2436-252">將下列程式碼新增至 `Startup.ConfigureServices`，以取代相依性插入容器中的預設 `IObjectModelValidator` 實作。</span><span class="sxs-lookup"><span data-stu-id="c2436-252">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="c2436-253">您仍可能會看到來自模型繫結的模型狀態錯誤。</span><span class="sxs-lookup"><span data-stu-id="c2436-253">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="c2436-254">用戶端驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-254">Client-side validation</span></span>

<span data-ttu-id="c2436-255">用戶端驗證可避免送出無效的表單。</span><span class="sxs-lookup"><span data-stu-id="c2436-255">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="c2436-256">[送出] 按鈕會執行 JavaScript 以送出表單或顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-256">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="c2436-257">當表單上存在輸入錯誤時，用戶端驗證可避免伺服器上不必要的來回往返。</span><span class="sxs-lookup"><span data-stu-id="c2436-257">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="c2436-258">*_Layout.cshtml* 和 *_ValidationScriptsPartial.cshtml* 中的下列指令碼參考支援用戶端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-258">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="c2436-259">[JQuery 不顯眼驗證](https://github.com/aspnet/jquery-validation-unobtrusive)腳本是以熱門[jQuery 驗證](https://jqueryvalidation.org/)外掛程式為基礎的自訂 Microsoft 前端程式庫。</span><span class="sxs-lookup"><span data-stu-id="c2436-259">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="c2436-260">若沒有 jQuery 低調驗證，您就必須在兩個地方撰寫相同的驗證邏輯程式碼：一次在模型屬性 (Property) 上的伺服器端驗證屬性 (Attribute)，另一次在用戶端指令碼中。</span><span class="sxs-lookup"><span data-stu-id="c2436-260">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="c2436-261">反之，[標記協助程式](xref:mvc/views/tag-helpers/intro)和 [HTML 協助程式](xref:mvc/views/overview)使用模型屬性 (Property) 中的驗證屬性 (Attribute) 和類型中繼資料，來轉譯需要驗證之表單項目的 HTML 5 `data-` 屬性 (Attribute)。</span><span class="sxs-lookup"><span data-stu-id="c2436-261">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="c2436-262">jQuery 不顯眼驗證會剖析 `data-` 屬性，並將邏輯傳遞至 JQuery 驗證，以有效地將伺服器端驗證邏輯「複製」到用戶端。</span><span class="sxs-lookup"><span data-stu-id="c2436-262">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="c2436-263">您可以使用標記協助程式來顯示用戶端的驗證錯誤，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-263">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="c2436-264">上述的標記協助程式會轉譯下列 HTML：</span><span class="sxs-lookup"><span data-stu-id="c2436-264">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="c2436-265">請注意，HTML 輸出中的 `data-` 屬性 (attribute) 會對應至 `Movie.ReleaseDate` 屬性 (property) 的驗證屬性 (attribute)。</span><span class="sxs-lookup"><span data-stu-id="c2436-265">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="c2436-266">`data-val-required` 屬性包含使用者未填入發行日期欄位時所要顯示的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-266">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="c2436-267">jQuery 不顯眼驗證會將此值傳遞至 [所需](https://jqueryvalidation.org/required-method/) 的 jquery 驗證 () 方法，然後在伴隨的元素中顯示該訊息 **\<span>** 。</span><span class="sxs-lookup"><span data-stu-id="c2436-267">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="c2436-268">資料型別驗證是根據屬性的 .NET 型別，除非是由 `[DataType]` 屬性覆寫。</span><span class="sxs-lookup"><span data-stu-id="c2436-268">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="c2436-269">瀏覽器具有自己的預設錯誤訊息，但 jQuery 驗證低調驗證套件可以覆寫這些訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-269">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="c2436-270">`[DataType]` 屬性和子類別 (例如 `[EmailAddress]`) 可讓您指定錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-270">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="c2436-271">不顯眼的驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-271">Unobtrusive validation</span></span>

<span data-ttu-id="c2436-272">如需不顯眼驗證的相關資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/1111)。</span><span class="sxs-lookup"><span data-stu-id="c2436-272">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="c2436-273">將驗證新增至動態表單</span><span class="sxs-lookup"><span data-stu-id="c2436-273">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="c2436-274">當頁面第一次載入時，jQuery 不顯眼驗證會將驗證邏輯和參數傳遞至 jQuery 驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-274">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="c2436-275">因此，驗證不會在動態產生的表單上自動運作。</span><span class="sxs-lookup"><span data-stu-id="c2436-275">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="c2436-276">若要啟用驗證，請指示 jQuery 低調驗證在建立動態表單之後立即進行剖析。</span><span class="sxs-lookup"><span data-stu-id="c2436-276">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="c2436-277">例如，下列程式碼會在透過 AJAX 新增的表單上設定用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-277">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="c2436-278">`$.validator.unobtrusive.parse()` 方法接受 jQuery 選取器的一個引數。</span><span class="sxs-lookup"><span data-stu-id="c2436-278">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="c2436-279">此方法會指示 jQuery 低調驗證在該選取器內剖析表單的 `data-` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-279">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="c2436-280">這些屬性的值接著會傳遞至 jQuery 驗證外掛程式。</span><span class="sxs-lookup"><span data-stu-id="c2436-280">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="c2436-281">將驗證新增至動態控制項</span><span class="sxs-lookup"><span data-stu-id="c2436-281">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="c2436-282">`$.validator.unobtrusive.parse()` 方法適用於整個表單，而非動態產生的個別控制項 (例如 `<input>` 和 `<select/>`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-282">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="c2436-283">若要重新剖析表單，請移除先前剖析表單時新增的驗證資料，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-283">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="c2436-284">自訂用戶端驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-284">Custom client-side validation</span></span>

<span data-ttu-id="c2436-285">自訂用戶端驗證是透過產生 `data-` 可與自訂 JQuery 驗證介面卡搭配使用的 HTML 屬性來完成。</span><span class="sxs-lookup"><span data-stu-id="c2436-285">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="c2436-286">下列配接器程式碼範例，是針對本文稍早介紹的 `[ClassicMovie]` 和 `[ClassicMovieWithClientValidator]` 屬性所撰寫：</span><span class="sxs-lookup"><span data-stu-id="c2436-286">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="c2436-287">如需有關如何撰寫介面卡的詳細資訊，請參閱 [JQuery 驗證檔](https://jqueryvalidation.org/documentation/)集。</span><span class="sxs-lookup"><span data-stu-id="c2436-287">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="c2436-288">用於指定欄位之配接器的使用，是由 `data-` 屬性觸發，因此會：</span><span class="sxs-lookup"><span data-stu-id="c2436-288">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="c2436-289">將欄位加上旗標為待驗證 (`data-val="true"`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-289">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="c2436-290">識別驗證規則名稱和錯誤訊息文字 (例如 `data-val-rulename="Error message."`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-290">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="c2436-291">提供驗證程式需要的任何其他參數 (例如 `data-val-rulename-param1="value"`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-291">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="c2436-292">下列範例顯示範例應用程式之 `ClassicMovie` 屬性的 `data-` 屬性：</span><span class="sxs-lookup"><span data-stu-id="c2436-292">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="c2436-293">如前文所述，[標記協助程式](xref:mvc/views/tag-helpers/intro)和 [HTML 協助程式](xref:mvc/views/overview) 使用來自驗證屬性的資訊來轉譯 `data-` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-293">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="c2436-294">有二個選項可撰寫用於建立自訂 `data-` HTML 屬性的程式碼：</span><span class="sxs-lookup"><span data-stu-id="c2436-294">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="c2436-295">建立衍生自 `AttributeAdapterBase<TAttribute>` 的類別，以及可實作 `IValidationAttributeAdapterProvider` 的類別，並在 DI 中註冊您的屬性及其配接器。</span><span class="sxs-lookup"><span data-stu-id="c2436-295">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="c2436-296">此方法遵循[單一職責原則](https://wikipedia.org/wiki/Single_responsibility_principle)，其中伺服器相關與用戶端相關的驗證程式碼位於不同類別中。</span><span class="sxs-lookup"><span data-stu-id="c2436-296">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="c2436-297">配接器也具有優點，由於其在 DI 中註冊，因此可以使用 DI 中的其他服務 (如需要)。</span><span class="sxs-lookup"><span data-stu-id="c2436-297">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="c2436-298">在您的 `ValidationAttribute` 類別中實作 `IClientModelValidator`。</span><span class="sxs-lookup"><span data-stu-id="c2436-298">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="c2436-299">如果屬性不執行任何伺服器端驗證，且不需要任何來自 DI 的服務，則此方法可能適合。</span><span class="sxs-lookup"><span data-stu-id="c2436-299">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="c2436-300">用戶端驗證的屬性配接器</span><span class="sxs-lookup"><span data-stu-id="c2436-300">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="c2436-301">這種在 HTML 中轉譯 `data-` 屬性的方法，由範例應用程式中的 `ClassicMovie` 屬性使用。</span><span class="sxs-lookup"><span data-stu-id="c2436-301">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="c2436-302">使用此方法來新增用戶端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-302">To add client validation by using this method:</span></span>

1. <span data-ttu-id="c2436-303">建立自訂驗證屬性的屬性配接器類別。</span><span class="sxs-lookup"><span data-stu-id="c2436-303">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="c2436-304">從 <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>衍生類別。</span><span class="sxs-lookup"><span data-stu-id="c2436-304">Derive the class from <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>.</span></span> <span data-ttu-id="c2436-305">建立會將 `data-` 屬性新增至轉譯輸出的 `AddValidation` 方法，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-305">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="c2436-306">建立實作 <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> 的配接器提供者類別。</span><span class="sxs-lookup"><span data-stu-id="c2436-306">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="c2436-307">在 `GetAttributeAdapter` 方法中將自訂屬性傳遞至配接器的建構函式，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-307">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="c2436-308">在 `Startup.ConfigureServices` 中註冊 DI 的配接器提供者：</span><span class="sxs-lookup"><span data-stu-id="c2436-308">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="c2436-309">用戶端驗證的 IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="c2436-309">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="c2436-310">這種在 HTML 中轉譯 `data-` 屬性的方法，由範例應用程式中的 `ClassicMovieWithClientValidator` 屬性使用。</span><span class="sxs-lookup"><span data-stu-id="c2436-310">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="c2436-311">使用此方法來新增用戶端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-311">To add client validation by using this method:</span></span>

* <span data-ttu-id="c2436-312">在自訂驗證屬性中，實作 `IClientModelValidator` 介面並建立 `AddValidation` 方法。</span><span class="sxs-lookup"><span data-stu-id="c2436-312">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="c2436-313">在 `AddValidation` 方法中，為驗證新增 `data-` 屬性，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-313">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="c2436-314">停用用戶端驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-314">Disable client-side validation</span></span>

<span data-ttu-id="c2436-315">下列程式碼會停用頁面中的用戶端驗證 Razor ：</span><span class="sxs-lookup"><span data-stu-id="c2436-315">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="c2436-316">停用用戶端驗證的其他選項：</span><span class="sxs-lookup"><span data-stu-id="c2436-316">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="c2436-317">批註 `_ValidationScriptsPartial` 所有 *cshtml* 檔案中的參考。</span><span class="sxs-lookup"><span data-stu-id="c2436-317">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="c2436-318">移除 *Pages\Shared \_ ValidationScriptsPartial. cshtml* 檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="c2436-318">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="c2436-319">上述方法不會防止用戶端驗證 ASP.NET Core Identity Razor 類別庫。</span><span class="sxs-lookup"><span data-stu-id="c2436-319">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="c2436-320">如需詳細資訊，請參閱<xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="c2436-320">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2436-321">其他資源</span><span class="sxs-lookup"><span data-stu-id="c2436-321">Additional resources</span></span>

* [<span data-ttu-id="c2436-322">System.ComponentModel.DataAnnotations 命名空間</span><span class="sxs-lookup"><span data-stu-id="c2436-322">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="c2436-323">模型系結</span><span class="sxs-lookup"><span data-stu-id="c2436-323">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2436-324">本文說明如何在 ASP.NET Core MVC 或 Pages 應用程式中驗證使用者輸入 Razor 。</span><span class="sxs-lookup"><span data-stu-id="c2436-324">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="c2436-325">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c2436-325">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="c2436-326">模型狀態</span><span class="sxs-lookup"><span data-stu-id="c2436-326">Model state</span></span>

<span data-ttu-id="c2436-327">模型狀態代表來自兩個子系統的錯誤：模型繫結和模型驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-327">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="c2436-328">源自[模型繫結](model-binding.md)的錯誤通常是資料轉換錯誤 (例如在必須輸入整數的欄位中輸入 "x")。</span><span class="sxs-lookup"><span data-stu-id="c2436-328">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="c2436-329">模型驗證發生於模型繫結之後，並會在資料不符合商務規則時回報錯誤 (例如在必須輸入 1 到 5 之間評等的欄位中輸入 0)。</span><span class="sxs-lookup"><span data-stu-id="c2436-329">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="c2436-330">模型系結和驗證都是在執行控制器動作或 Razor 頁面處理常式方法之前進行。</span><span class="sxs-lookup"><span data-stu-id="c2436-330">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="c2436-331">針對 Web 應用程式，此應用程式的責任為檢查 `ModelState.IsValid` 並做出適當回應。</span><span class="sxs-lookup"><span data-stu-id="c2436-331">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="c2436-332">Web 應用程式通常會重新顯示頁面，並出現錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="c2436-332">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="c2436-333">如果 Web API 控制器具有 `[ApiController]` 屬性，則該控制器就不需要檢查 `ModelState.IsValid`。</span><span class="sxs-lookup"><span data-stu-id="c2436-333">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="c2436-334">在此情況下，當模型狀態無效時，會傳回包含錯誤詳細資料的自動 HTTP 400 回應。</span><span class="sxs-lookup"><span data-stu-id="c2436-334">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="c2436-335">如需詳細資訊，請參閱[自動 HTTP 400 回應](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="c2436-335">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="c2436-336">重新執行驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-336">Rerun validation</span></span>

<span data-ttu-id="c2436-337">驗證會自動進行，但建議您以手動方式重複進行。</span><span class="sxs-lookup"><span data-stu-id="c2436-337">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="c2436-338">例如，您可能會計算屬性的值，並在將屬性設定為計算的值之後重新執行驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-338">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="c2436-339">若要重新執行驗證，請呼叫 `TryValidateModel` 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-339">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="c2436-340">驗證屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-340">Validation attributes</span></span>

<span data-ttu-id="c2436-341">驗證屬性可讓您指定模型屬性的驗證規則。</span><span class="sxs-lookup"><span data-stu-id="c2436-341">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="c2436-342">下列來自[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample)的範例，顯示以驗證屬性標註的模型類別。</span><span class="sxs-lookup"><span data-stu-id="c2436-342">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="c2436-343">`[ClassicMovie]` 屬性是自訂驗證屬性，其他的則是內建。</span><span class="sxs-lookup"><span data-stu-id="c2436-343">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="c2436-344">未顯示的是 `[ClassicMovie2]` ，它會顯示執行自訂屬性的替代方法。</span><span class="sxs-lookup"><span data-stu-id="c2436-344">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="c2436-345">內建屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-345">Built-in attributes</span></span>

<span data-ttu-id="c2436-346">內建驗證屬性包括：</span><span class="sxs-lookup"><span data-stu-id="c2436-346">Built-in validation attributes include:</span></span>

* <span data-ttu-id="c2436-347">`[CreditCard]`：驗證屬性是否具有信用卡格式。</span><span class="sxs-lookup"><span data-stu-id="c2436-347">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="c2436-348">`[Compare]`：驗證模型中的兩個屬性是否相符。</span><span class="sxs-lookup"><span data-stu-id="c2436-348">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="c2436-349">例如， *Register.cshtml.cs* 檔會使用 `[Compare]` 來驗證兩個輸入的密碼相符。</span><span class="sxs-lookup"><span data-stu-id="c2436-349">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="c2436-350">[Scaffold Identity ](xref:security/authentication/scaffold-identity)以查看註冊程式碼。</span><span class="sxs-lookup"><span data-stu-id="c2436-350">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="c2436-351">`[EmailAddress]`：驗證屬性具有電子郵件格式。</span><span class="sxs-lookup"><span data-stu-id="c2436-351">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="c2436-352">`[Phone]`：驗證屬性具有電話號碼格式。</span><span class="sxs-lookup"><span data-stu-id="c2436-352">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="c2436-353">`[Range]`：驗證屬性值是否落在指定的範圍內。</span><span class="sxs-lookup"><span data-stu-id="c2436-353">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="c2436-354">`[RegularExpression]`：驗證屬性值是否符合指定的正則運算式。</span><span class="sxs-lookup"><span data-stu-id="c2436-354">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="c2436-355">`[Required]`：驗證欄位不是 null。</span><span class="sxs-lookup"><span data-stu-id="c2436-355">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="c2436-356">如需此屬性行為的詳細資訊，請參閱[ `[Required]` 屬性](#required-attribute)。</span><span class="sxs-lookup"><span data-stu-id="c2436-356">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="c2436-357">`[StringLength]`：驗證字串屬性值不超過指定的長度限制。</span><span class="sxs-lookup"><span data-stu-id="c2436-357">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="c2436-358">`[Url]`：驗證屬性是否具有 URL 格式。</span><span class="sxs-lookup"><span data-stu-id="c2436-358">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="c2436-359">`[Remote]`：在伺服器上呼叫動作方法，以驗證用戶端上的輸入。</span><span class="sxs-lookup"><span data-stu-id="c2436-359">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="c2436-360">如需此屬性行為的詳細資訊，請參閱[ `[Remote]` 屬性](#remote-attribute)。</span><span class="sxs-lookup"><span data-stu-id="c2436-360">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="c2436-361">搭配用戶端 `[RegularExpression]` 驗證使用屬性時，會在用戶端的 JavaScript 中執行 RegEx。</span><span class="sxs-lookup"><span data-stu-id="c2436-361">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="c2436-362">這表示將會使用 [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) 相符行為。</span><span class="sxs-lookup"><span data-stu-id="c2436-362">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="c2436-363">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/corefx/issues/42487)。</span><span class="sxs-lookup"><span data-stu-id="c2436-363">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="c2436-364">您可以在 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 命名空間中找到驗證屬性的完整清單。</span><span class="sxs-lookup"><span data-stu-id="c2436-364">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="c2436-365">錯誤訊息</span><span class="sxs-lookup"><span data-stu-id="c2436-365">Error messages</span></span>

<span data-ttu-id="c2436-366">驗證屬性可讓您指定要顯示的無效輸入錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-366">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="c2436-367">例如：</span><span class="sxs-lookup"><span data-stu-id="c2436-367">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="c2436-368">就內部而言，屬性會使用欄位名稱的預留位置呼叫 `String.Format`，有時候也會使用其他預留位置。</span><span class="sxs-lookup"><span data-stu-id="c2436-368">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="c2436-369">例如：</span><span class="sxs-lookup"><span data-stu-id="c2436-369">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="c2436-370">套用至 `Name` 屬性時，透過上述程式碼建立的錯誤訊息會是 「名稱長度必須介於 6 和 8 之間」。</span><span class="sxs-lookup"><span data-stu-id="c2436-370">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="c2436-371">若要找出哪些參數傳遞給 `String.Format` 以取得特定屬性的錯誤訊息，請參閱 [DataAnnotations 原始程式碼](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="c2436-371">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="c2436-372">[必要] 屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-372">[Required] attribute</span></span>

<span data-ttu-id="c2436-373">根據預設，驗證系統會將不可為 Null 的參數或屬性視為具有 `[Required]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-373">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="c2436-374">例如 `decimal` 和 `int` 的[實值型別](/dotnet/csharp/language-reference/keywords/value-types)不可為 Null。</span><span class="sxs-lookup"><span data-stu-id="c2436-374">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="c2436-375">[必要] 在伺服器上執行驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-375">[Required] validation on the server</span></span>

<span data-ttu-id="c2436-376">在伺服器上，如果必要的值屬性為 Null，則會視為遺失。</span><span class="sxs-lookup"><span data-stu-id="c2436-376">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="c2436-377">不可為 Null 的欄位一律有效，且一律不會顯示 [必要] 屬性的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-377">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="c2436-378">不過，不可為 Null 屬性的模型繫結可能會失敗，並產生一則錯誤訊息，例如 `The value '' is invalid`。</span><span class="sxs-lookup"><span data-stu-id="c2436-378">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="c2436-379">若要針對不可為 Null 型別的伺服器端驗證指定自訂錯誤訊息，您可以使用下列選項：</span><span class="sxs-lookup"><span data-stu-id="c2436-379">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="c2436-380">將欄位設成可為 Null (例如 `decimal?`，而不是 `decimal`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-380">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="c2436-381">[可 \<T> 為 null](/dotnet/csharp/programming-guide/nullable-types/)實值型別會視為標準可為 null 的類型。</span><span class="sxs-lookup"><span data-stu-id="c2436-381">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="c2436-382">指定模型繫結要使用的預設錯誤訊息，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-382">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="c2436-383">如需您可以為其設定預設訊息之模型繫結錯誤的詳細資訊，請參閱 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>。</span><span class="sxs-lookup"><span data-stu-id="c2436-383">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="c2436-384">[必要] 在用戶端上執行驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-384">[Required] validation on the client</span></span>

<span data-ttu-id="c2436-385">不可為 Null 的型別和字串，會在用戶端上以不同於在伺服器上的方式處理。</span><span class="sxs-lookup"><span data-stu-id="c2436-385">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="c2436-386">在用戶端上：</span><span class="sxs-lookup"><span data-stu-id="c2436-386">On the client:</span></span>

* <span data-ttu-id="c2436-387">只有在為值的輸入進行輸入後，才會將該值視為存在。</span><span class="sxs-lookup"><span data-stu-id="c2436-387">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="c2436-388">因此，用戶端驗證處理非 Null 型別的方式，會與可為 Null 型別的方式相同。</span><span class="sxs-lookup"><span data-stu-id="c2436-388">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="c2436-389">字串欄位中的空白字元，會以 jQuery 驗證[必要](https://jqueryvalidation.org/required-method/)方法視為有效的輸入。</span><span class="sxs-lookup"><span data-stu-id="c2436-389">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="c2436-390">如果僅輸入空白字元，則伺服器端驗證會將必要的字串欄位視為無效。</span><span class="sxs-lookup"><span data-stu-id="c2436-390">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="c2436-391">如先前所述，不可為 Null 的型別會視為具有 `[Required]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-391">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="c2436-392">這表示即使您不套用 `[Required]` 屬性，也可以取得用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-392">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="c2436-393">但是，如果您不使用該屬性，就會出現預設的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-393">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="c2436-394">若要指定自訂錯誤訊息，請使用該屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-394">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="c2436-395">[遠端] 屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-395">[Remote] attribute</span></span>

<span data-ttu-id="c2436-396">`[Remote]` 屬性會實作用戶端驗證，該驗證需要呼叫伺服器上的方法來判斷欄位輸入是否有效。</span><span class="sxs-lookup"><span data-stu-id="c2436-396">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="c2436-397">例如，應用程式可能需要驗證使用者名稱是否已經在使用中。</span><span class="sxs-lookup"><span data-stu-id="c2436-397">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="c2436-398">實作遠端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-398">To implement remote validation:</span></span>

1. <span data-ttu-id="c2436-399">為 JavaScript 建立動作方法來呼叫。</span><span class="sxs-lookup"><span data-stu-id="c2436-399">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="c2436-400">JQuery 驗證[遠端](https://jqueryvalidation.org/remote-method/)方法會預期 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="c2436-400">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="c2436-401">`"true"` 表示輸入資料有效。</span><span class="sxs-lookup"><span data-stu-id="c2436-401">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="c2436-402">`"false"`、`undefined` 或 `null` 表示輸入無效。</span><span class="sxs-lookup"><span data-stu-id="c2436-402">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="c2436-403">顯示預設錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-403">Display the default error message.</span></span>
   * <span data-ttu-id="c2436-404">任何其他字串都表示輸入無效。</span><span class="sxs-lookup"><span data-stu-id="c2436-404">Any other string means the input is invalid.</span></span> <span data-ttu-id="c2436-405">將字串顯示為自訂錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-405">Display the string as a custom error message.</span></span>

   <span data-ttu-id="c2436-406">自訂錯誤訊息的動作方法範例如下：</span><span class="sxs-lookup"><span data-stu-id="c2436-406">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="c2436-407">在模型類別中，使用指向驗證動作方法的 `[Remote]` 屬性 (Attribute) 來標註屬性 (Property)，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-407">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="c2436-408">`[Remote]` 屬性位於 `Microsoft.AspNetCore.Mvc` 命名空間。</span><span class="sxs-lookup"><span data-stu-id="c2436-408">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="c2436-409">如不使用 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 中繼套件，請安裝 [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="c2436-409">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="c2436-410">其他欄位</span><span class="sxs-lookup"><span data-stu-id="c2436-410">Additional fields</span></span>

<span data-ttu-id="c2436-411">`[Remote]` 屬性 (Attribute) 的 `AdditionalFields` 屬性 (Property) 可讓您針對伺服器上的資料來驗證欄位組合。</span><span class="sxs-lookup"><span data-stu-id="c2436-411">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="c2436-412">例如，如果 `User` 模型具 `FirstName` 和 `LastName` 屬性，建議您確認沒有任何現有使用者已具有該組名稱。</span><span class="sxs-lookup"><span data-stu-id="c2436-412">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="c2436-413">下列範例顯示如何使用 `AdditionalFields`：</span><span class="sxs-lookup"><span data-stu-id="c2436-413">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="c2436-414">`AdditionalFields` 可以明確地設定為字串 `"FirstName"` 和 `"LastName"` ，但是使用 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 運算子可簡化稍後的重構。</span><span class="sxs-lookup"><span data-stu-id="c2436-414">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="c2436-415">這項驗證的動作方法必須接受名字和姓氏引數：</span><span class="sxs-lookup"><span data-stu-id="c2436-415">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="c2436-416">當使用者輸入名字或姓氏時，JavaScript 會進行遠端呼叫以查看該組名稱是否已在使用中。</span><span class="sxs-lookup"><span data-stu-id="c2436-416">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="c2436-417">若要驗證兩個或多個其他欄位，請以逗號分隔清單來提供這些欄位。</span><span class="sxs-lookup"><span data-stu-id="c2436-417">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="c2436-418">例如，若要將 `MiddleName` 屬性 (Property) 新增至模型，請設定 `[Remote]` 屬性 (Attribute)，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-418">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="c2436-419">如同所有屬性引數，`AdditionalFields` 必須是常數運算式。</span><span class="sxs-lookup"><span data-stu-id="c2436-419">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="c2436-420">因此，請勿使用[內插字串](/dotnet/csharp/language-reference/keywords/interpolated-strings)或呼叫 <xref:System.String.Join*> 來初始化 `AdditionalFields`。</span><span class="sxs-lookup"><span data-stu-id="c2436-420">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="c2436-421">內建屬性的替代項目</span><span class="sxs-lookup"><span data-stu-id="c2436-421">Alternatives to built-in attributes</span></span>

<span data-ttu-id="c2436-422">如果您需要內建屬性未提供的驗證，您可以：</span><span class="sxs-lookup"><span data-stu-id="c2436-422">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="c2436-423">[建立自訂屬性](#custom-attributes)。</span><span class="sxs-lookup"><span data-stu-id="c2436-423">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="c2436-424">[實作 IValidatableObject](#ivalidatableobject)。</span><span class="sxs-lookup"><span data-stu-id="c2436-424">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="c2436-425">自訂屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-425">Custom attributes</span></span>

<span data-ttu-id="c2436-426">在內建驗證屬性無法處理的情況下，您可以建立自訂驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-426">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="c2436-427">建立繼承自 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> 的類別，並覆寫 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 方法。</span><span class="sxs-lookup"><span data-stu-id="c2436-427">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="c2436-428">`IsValid` 方法會接受名為 *value* 的物件，這是要驗證的輸入。</span><span class="sxs-lookup"><span data-stu-id="c2436-428">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="c2436-429">多載也會接受 `ValidationContext` 物件，該物件會提供其他資訊，例如模型繫結所建立的模型執行個體。</span><span class="sxs-lookup"><span data-stu-id="c2436-429">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="c2436-430">下列範例會驗證 *Classic* 內容類型中電影的發行日期，不晚於指定的年份。</span><span class="sxs-lookup"><span data-stu-id="c2436-430">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="c2436-431">`[ClassicMovie2]` 屬性會先檢查內容類型，如果是 *Classic* 才會繼續。</span><span class="sxs-lookup"><span data-stu-id="c2436-431">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="c2436-432">針對識別為 Classic 的電影，會檢查發行日期，以確定其不晚於傳遞至屬性建構函式的限制。)</span><span class="sxs-lookup"><span data-stu-id="c2436-432">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="c2436-433">上述範例中的 `movie` 變數代表 `Movie` 物件，該物件包含表單送出中的資料。</span><span class="sxs-lookup"><span data-stu-id="c2436-433">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="c2436-434">`IsValid` 方法會檢查日期與內容類型。</span><span class="sxs-lookup"><span data-stu-id="c2436-434">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="c2436-435">驗證成功時，`IsValid` 會傳回 `ValidationResult.Success` 程式碼。</span><span class="sxs-lookup"><span data-stu-id="c2436-435">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="c2436-436">驗證失敗時，會傳回 `ValidationResult` 和錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-436">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="c2436-437">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="c2436-437">IValidatableObject</span></span>

<span data-ttu-id="c2436-438">上述範例僅適用於 `Movie` 型別。</span><span class="sxs-lookup"><span data-stu-id="c2436-438">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="c2436-439">類別層級驗證的另一個選項，是在模型類別中實作 `IValidatableObject`，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-439">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="c2436-440">最上層節點驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-440">Top-level node validation</span></span>

<span data-ttu-id="c2436-441">最上層節點包括：</span><span class="sxs-lookup"><span data-stu-id="c2436-441">Top-level nodes include:</span></span>

* <span data-ttu-id="c2436-442">動作參數</span><span class="sxs-lookup"><span data-stu-id="c2436-442">Action parameters</span></span>
* <span data-ttu-id="c2436-443">控制器屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-443">Controller properties</span></span>
* <span data-ttu-id="c2436-444">頁面處理常式參數</span><span class="sxs-lookup"><span data-stu-id="c2436-444">Page handler parameters</span></span>
* <span data-ttu-id="c2436-445">頁面模型屬性</span><span class="sxs-lookup"><span data-stu-id="c2436-445">Page model properties</span></span>

<span data-ttu-id="c2436-446">除了驗證模型屬性，也會驗證模型所繫結的最上層節點。</span><span class="sxs-lookup"><span data-stu-id="c2436-446">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="c2436-447">在來自範例應用程式的下列範例中，`VerifyPhone` 方法會使用 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> 來驗證 `phone` 動作參數：</span><span class="sxs-lookup"><span data-stu-id="c2436-447">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="c2436-448">最上層節點可以搭配使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> 和驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-448">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="c2436-449">在來自範例應用程式的下列範例中，`CheckAge` 方法會指定提交表單時必須從查詢字串繫結 `age` 參數：</span><span class="sxs-lookup"><span data-stu-id="c2436-449">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="c2436-450">在 [檢查年齡] 頁面 (*CheckAge.cshtml*) 中，有兩個表單。</span><span class="sxs-lookup"><span data-stu-id="c2436-450">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="c2436-451">第一個表單會提交 `Age` 值 `99` 作為查詢字串：`https://localhost:5001/Users/CheckAge?Age=99`。</span><span class="sxs-lookup"><span data-stu-id="c2436-451">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="c2436-452">從查詢字串提交正確格式化的 `age` 時，即會驗證表單。</span><span class="sxs-lookup"><span data-stu-id="c2436-452">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="c2436-453">[檢查年齡] 頁面上的第二個表單會在要求本文中提交 `Age` 值，而且驗證會失敗。</span><span class="sxs-lookup"><span data-stu-id="c2436-453">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="c2436-454">由於 `age` 參數必須來自查詢字串，因此繫結會失敗。</span><span class="sxs-lookup"><span data-stu-id="c2436-454">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="c2436-455">執行 `CompatibilityVersion.Version_2_1` 或更新版本時，根據預設會啟用最上層節點驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-455">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="c2436-456">否則，會停用最上層節點驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-456">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="c2436-457">預設選項可以藉由設定 (`Startup.ConfigureServices`) 中的 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> 屬性來覆寫，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-457">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="c2436-458">最大錯誤數</span><span class="sxs-lookup"><span data-stu-id="c2436-458">Maximum errors</span></span>

<span data-ttu-id="c2436-459">達到最大錯誤數 (預設為 200) 時，就會停止驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-459">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="c2436-460">您可以在 `Startup.ConfigureServices` 中使用下列程式碼來設定此數目：</span><span class="sxs-lookup"><span data-stu-id="c2436-460">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="c2436-461">最大遞迴</span><span class="sxs-lookup"><span data-stu-id="c2436-461">Maximum recursion</span></span>

<span data-ttu-id="c2436-462"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> 會周遊所正驗證之模型的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="c2436-462"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="c2436-463">針對非常深或無限遞迴的模型，驗證可能會導致堆疊溢位。</span><span class="sxs-lookup"><span data-stu-id="c2436-463">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="c2436-464">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) 可用來在訪客遞迴超過設定的深度時，提早停止驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-464">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="c2436-465">`MvcOptions.MaxValidationDepth`使用或更新版本執行時，的預設值為 32 `CompatibilityVersion.Version_2_2` 。</span><span class="sxs-lookup"><span data-stu-id="c2436-465">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="c2436-466">針對舊版，值為 Null，表示沒有深度條件約束。</span><span class="sxs-lookup"><span data-stu-id="c2436-466">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="c2436-467">自動最少運算</span><span class="sxs-lookup"><span data-stu-id="c2436-467">Automatic short-circuit</span></span>

<span data-ttu-id="c2436-468">如果模型圖形不需要驗證，則驗證會自動進行最少運算 (略過)。</span><span class="sxs-lookup"><span data-stu-id="c2436-468">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="c2436-469">執行階段會略過驗證的物件，包含基本集合 (例如 `byte[]`、`string[]`、`Dictionary<string, string>`) 和沒有任何驗證程式的複雜物件圖形。</span><span class="sxs-lookup"><span data-stu-id="c2436-469">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="c2436-470">停用驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-470">Disable validation</span></span>

<span data-ttu-id="c2436-471">停用驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-471">To disable validation:</span></span>

1. <span data-ttu-id="c2436-472">建立不會將任何欄位標記為無效的 `IObjectModelValidator` 實作。</span><span class="sxs-lookup"><span data-stu-id="c2436-472">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="c2436-473">將下列程式碼新增至 `Startup.ConfigureServices`，以取代相依性插入容器中的預設 `IObjectModelValidator` 實作。</span><span class="sxs-lookup"><span data-stu-id="c2436-473">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="c2436-474">您仍可能會看到來自模型繫結的模型狀態錯誤。</span><span class="sxs-lookup"><span data-stu-id="c2436-474">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="c2436-475">用戶端驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-475">Client-side validation</span></span>

<span data-ttu-id="c2436-476">用戶端驗證可避免送出無效的表單。</span><span class="sxs-lookup"><span data-stu-id="c2436-476">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="c2436-477">[送出] 按鈕會執行 JavaScript 以送出表單或顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-477">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="c2436-478">當表單上存在輸入錯誤時，用戶端驗證可避免伺服器上不必要的來回往返。</span><span class="sxs-lookup"><span data-stu-id="c2436-478">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="c2436-479">*_Layout.cshtml* 和 *_ValidationScriptsPartial.cshtml* 中的下列指令碼參考支援用戶端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-479">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="c2436-480">[jQuery 低調驗證](https://github.com/aspnet/jquery-validation-unobtrusive) (jQuery Unobtrusive Validation) 指令碼是建置在熱門 [jQuery 驗證](https://jqueryvalidation.org/) 外掛程式上的自訂 Microsoft 前端程式庫。</span><span class="sxs-lookup"><span data-stu-id="c2436-480">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="c2436-481">若沒有 jQuery 低調驗證，您就必須在兩個地方撰寫相同的驗證邏輯程式碼：一次在模型屬性 (Property) 上的伺服器端驗證屬性 (Attribute)，另一次在用戶端指令碼中。</span><span class="sxs-lookup"><span data-stu-id="c2436-481">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="c2436-482">反之，[標記協助程式](xref:mvc/views/tag-helpers/intro)和 [HTML 協助程式](xref:mvc/views/overview)使用模型屬性 (Property) 中的驗證屬性 (Attribute) 和類型中繼資料，來轉譯需要驗證之表單項目的 HTML 5 `data-` 屬性 (Attribute)。</span><span class="sxs-lookup"><span data-stu-id="c2436-482">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="c2436-483">jQuery 低調驗證會剖析 `data-` 屬性並將邏輯傳遞至 jQuery 驗證，以有效地將伺服器端驗證邏輯「複製」到用戶端。</span><span class="sxs-lookup"><span data-stu-id="c2436-483">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="c2436-484">您可以使用標記協助程式來顯示用戶端的驗證錯誤，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-484">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="c2436-485">上述標記協助程式會轉譯下列 HTML。</span><span class="sxs-lookup"><span data-stu-id="c2436-485">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="c2436-486">請注意，HTML 輸出中的 `data-` 屬性 (attribute) 會對應至 `ReleaseDate` 屬性 (property) 的驗證屬性 (attribute)。</span><span class="sxs-lookup"><span data-stu-id="c2436-486">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="c2436-487">`data-val-required` 屬性包含使用者未填入發行日期欄位時所要顯示的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-487">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="c2436-488">jQuery 不顯眼驗證會將此值傳遞給 jQuery Validate [ ()](https://jqueryvalidation.org/required-method/) 方法，然後在伴隨的元素中顯示該訊息 **\<span>** 。</span><span class="sxs-lookup"><span data-stu-id="c2436-488">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="c2436-489">資料型別驗證是根據屬性的 .NET 型別，除非是由 `[DataType]` 屬性覆寫。</span><span class="sxs-lookup"><span data-stu-id="c2436-489">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="c2436-490">瀏覽器具有自己的預設錯誤訊息，但 jQuery 驗證低調驗證套件可以覆寫這些訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-490">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="c2436-491">`[DataType]` 屬性和子類別 (例如 `[EmailAddress]`) 可讓您指定錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="c2436-491">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="c2436-492">將驗證新增至動態表單</span><span class="sxs-lookup"><span data-stu-id="c2436-492">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="c2436-493">jQuery 低調驗證會在第一次載入頁面時將驗證邏輯和傳遞參數至 jQuery 驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-493">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="c2436-494">因此，驗證不會在動態產生的表單上自動運作。</span><span class="sxs-lookup"><span data-stu-id="c2436-494">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="c2436-495">若要啟用驗證，請指示 jQuery 低調驗證在建立動態表單之後立即進行剖析。</span><span class="sxs-lookup"><span data-stu-id="c2436-495">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="c2436-496">例如，下列程式碼會在透過 AJAX 新增的表單上設定用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="c2436-496">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="c2436-497">`$.validator.unobtrusive.parse()` 方法接受 jQuery 選取器的一個引數。</span><span class="sxs-lookup"><span data-stu-id="c2436-497">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="c2436-498">此方法會指示 jQuery 低調驗證在該選取器內剖析表單的 `data-` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-498">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="c2436-499">然後，這些屬性的值會傳遞至 jQuery 驗證外掛程式。</span><span class="sxs-lookup"><span data-stu-id="c2436-499">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="c2436-500">將驗證新增至動態控制項</span><span class="sxs-lookup"><span data-stu-id="c2436-500">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="c2436-501">`$.validator.unobtrusive.parse()` 方法適用於整個表單，而非動態產生的個別控制項 (例如 `<input>` 和 `<select/>`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-501">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="c2436-502">若要重新剖析表單，請移除先前剖析表單時新增的驗證資料，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-502">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="c2436-503">自訂用戶端驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-503">Custom client-side validation</span></span>

<span data-ttu-id="c2436-504">自訂用戶端驗證是透過產生使用自訂 jQuery 驗證配接器的 `data-` HTML 屬性來進行。</span><span class="sxs-lookup"><span data-stu-id="c2436-504">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="c2436-505">下列配接器程式碼範例，是針對本文稍早介紹的 `ClassicMovie` 和 `ClassicMovie2` 屬性所撰寫：</span><span class="sxs-lookup"><span data-stu-id="c2436-505">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="c2436-506">如需如何撰寫配接器的資訊，請參閱 [jQuery 驗證文件](https://jqueryvalidation.org/documentation/)。</span><span class="sxs-lookup"><span data-stu-id="c2436-506">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="c2436-507">用於指定欄位之配接器的使用，是由 `data-` 屬性觸發，因此會：</span><span class="sxs-lookup"><span data-stu-id="c2436-507">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="c2436-508">將欄位加上旗標為待驗證 (`data-val="true"`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-508">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="c2436-509">識別驗證規則名稱和錯誤訊息文字 (例如 `data-val-rulename="Error message."`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-509">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="c2436-510">提供驗證程式需要的任何其他參數 (例如 `data-val-rulename-parm1="value"`)。</span><span class="sxs-lookup"><span data-stu-id="c2436-510">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="c2436-511">下列範例顯示範例應用程式之 `ClassicMovie` 屬性的 `data-` 屬性：</span><span class="sxs-lookup"><span data-stu-id="c2436-511">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="c2436-512">如前文所述，[標記協助程式](xref:mvc/views/tag-helpers/intro)和 [HTML 協助程式](xref:mvc/views/overview) 使用來自驗證屬性的資訊來轉譯 `data-` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c2436-512">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="c2436-513">有二個選項可撰寫用於建立自訂 `data-` HTML 屬性的程式碼：</span><span class="sxs-lookup"><span data-stu-id="c2436-513">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="c2436-514">建立衍生自 `AttributeAdapterBase<TAttribute>` 的類別，以及可實作 `IValidationAttributeAdapterProvider` 的類別，並在 DI 中註冊您的屬性及其配接器。</span><span class="sxs-lookup"><span data-stu-id="c2436-514">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="c2436-515">此方法遵循[單一職責原則](https://wikipedia.org/wiki/Single_responsibility_principle)，其中伺服器相關與用戶端相關的驗證程式碼位於不同類別中。</span><span class="sxs-lookup"><span data-stu-id="c2436-515">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="c2436-516">配接器也具有優點，由於其在 DI 中註冊，因此可以使用 DI 中的其他服務 (如需要)。</span><span class="sxs-lookup"><span data-stu-id="c2436-516">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="c2436-517">在您的 `ValidationAttribute` 類別中實作 `IClientModelValidator`。</span><span class="sxs-lookup"><span data-stu-id="c2436-517">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="c2436-518">如果屬性不執行任何伺服器端驗證，且不需要任何來自 DI 的服務，則此方法可能適合。</span><span class="sxs-lookup"><span data-stu-id="c2436-518">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="c2436-519">用戶端驗證的屬性配接器</span><span class="sxs-lookup"><span data-stu-id="c2436-519">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="c2436-520">這種在 HTML 中轉譯 `data-` 屬性的方法，由範例應用程式中的 `ClassicMovie` 屬性使用。</span><span class="sxs-lookup"><span data-stu-id="c2436-520">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="c2436-521">使用此方法來新增用戶端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-521">To add client validation by using this method:</span></span>

1. <span data-ttu-id="c2436-522">建立自訂驗證屬性的屬性配接器類別。</span><span class="sxs-lookup"><span data-stu-id="c2436-522">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="c2436-523">從 <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>衍生類別。</span><span class="sxs-lookup"><span data-stu-id="c2436-523">Derive the class from <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>.</span></span> <span data-ttu-id="c2436-524">建立會將 `data-` 屬性新增至轉譯輸出的 `AddValidation` 方法，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-524">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="c2436-525">建立實作 <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> 的配接器提供者類別。</span><span class="sxs-lookup"><span data-stu-id="c2436-525">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="c2436-526">在 `GetAttributeAdapter` 方法中將自訂屬性傳遞至配接器的建構函式，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-526">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="c2436-527">在 `Startup.ConfigureServices` 中註冊 DI 的配接器提供者：</span><span class="sxs-lookup"><span data-stu-id="c2436-527">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="c2436-528">用戶端驗證的 IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="c2436-528">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="c2436-529">這種在 HTML 中轉譯 `data-` 屬性的方法，由範例應用程式中的 `ClassicMovie2` 屬性使用。</span><span class="sxs-lookup"><span data-stu-id="c2436-529">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="c2436-530">使用此方法來新增用戶端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-530">To add client validation by using this method:</span></span>

* <span data-ttu-id="c2436-531">在自訂驗證屬性中，實作 `IClientModelValidator` 介面並建立 `AddValidation` 方法。</span><span class="sxs-lookup"><span data-stu-id="c2436-531">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="c2436-532">在 `AddValidation` 方法中，為驗證新增 `data-` 屬性，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="c2436-532">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="c2436-533">停用用戶端驗證</span><span class="sxs-lookup"><span data-stu-id="c2436-533">Disable client-side validation</span></span>

<span data-ttu-id="c2436-534">下列程式碼會停用 MVC 檢視中的用戶端驗證：</span><span class="sxs-lookup"><span data-stu-id="c2436-534">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="c2436-535">和 Razor 頁面中：</span><span class="sxs-lookup"><span data-stu-id="c2436-535">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="c2436-536">停用用戶端驗證的另一個選項，是將 *.cshtml* 檔案中對 `_ValidationScriptsPartial` 的參考標記為註解。</span><span class="sxs-lookup"><span data-stu-id="c2436-536">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2436-537">其他資源</span><span class="sxs-lookup"><span data-stu-id="c2436-537">Additional resources</span></span>

* [<span data-ttu-id="c2436-538">System.ComponentModel.DataAnnotations 命名空間</span><span class="sxs-lookup"><span data-stu-id="c2436-538">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="c2436-539">模型系結</span><span class="sxs-lookup"><span data-stu-id="c2436-539">Model Binding</span></span>](model-binding.md)

::: moniker-end
