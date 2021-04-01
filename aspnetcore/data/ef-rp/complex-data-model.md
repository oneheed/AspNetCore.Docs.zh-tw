---
title: 第5部分： Razor ASP.NET Core 資料模型中有 EF Core 的頁面
author: rick-anderson
description: 第 5 Razor 頁和 Entity Framework 教學課程系列。
ms.author: riande
ms.custom: mvc
ms.date: 3/3/2021
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 029decea6ce3d39442f50c76fdf089f6052b2bb0
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957524"
---
<!-- Removed from V 5.0, CourseAssignment -->

# <a name="part-5-razor-pages-with-ef-core-in-aspnet-core---data-model"></a><span data-ttu-id="317de-103">第5部分： Razor ASP.NET Core 資料模型中有 EF Core 的頁面</span><span class="sxs-lookup"><span data-stu-id="317de-103">Part 5, Razor Pages with EF Core in ASP.NET Core - Data Model</span></span>

<span data-ttu-id="317de-104">由 [Tom Dykstra](https://github.com/tdykstra)、 [Jeremy Likness](https://twitter.com/jeremylikness)和 [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="317de-104">By [Tom Dykstra](https://github.com/tdykstra), [Jeremy Likness](https://twitter.com/jeremylikness), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="317de-105">先前的教學課程建立了基本的資料模型，該模型由三個實體組成。</span><span class="sxs-lookup"><span data-stu-id="317de-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="317de-106">在本教學課程中：</span><span class="sxs-lookup"><span data-stu-id="317de-106">In this tutorial:</span></span>

* <span data-ttu-id="317de-107">新增更多實體和關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="317de-108">藉由指定格式、驗證和資料庫對應規則來自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="317de-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="317de-109">已完成的資料模型如下圖所示：</span><span class="sxs-lookup"><span data-stu-id="317de-109">The completed data model is shown in the following illustration:</span></span>

![實體圖表](complex-data-model/_static/EF.png)

## <a name="the-student-entity"></a><span data-ttu-id="317de-111">Student 實體</span><span class="sxs-lookup"><span data-stu-id="317de-111">The Student entity</span></span>

<span data-ttu-id="317de-112">將 *Models/Student.cs* 中的程式碼替換成下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="317de-112">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="317de-113">上述程式碼會新增 `FullName` 屬性，並將下列屬性新增到現有屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-113">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)
* [`[DisplayFormat]`](xref:System.ComponentModel.DataAnnotations.DisplayFormatAttribute)
* [`[StringLength]`](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)
* [`[Column]`](xref:System.ComponentModel.DataAnnotations.Schema.ColumnAttribute)
* [`[Required]`](xref:System.ComponentModel.DataAnnotations.RequiredAttribute)
* [`[Display]`](xref:System.ComponentModel.DataAnnotations.DisplayAttribute)

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="317de-114">FullName 計算屬性</span><span class="sxs-lookup"><span data-stu-id="317de-114">The FullName calculated property</span></span>

<span data-ttu-id="317de-115">`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。</span><span class="sxs-lookup"><span data-stu-id="317de-115">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="317de-116">您無法設定 `FullName`，因此它只會有 get 存取子。</span><span class="sxs-lookup"><span data-stu-id="317de-116">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="317de-117">資料庫中不會建立 `FullName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="317de-117">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="317de-118">DataType 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-118">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="317de-119">針對學生註冊日期，所有頁面目前都會同時顯示日期和一天當中的時間，雖然只要日期才是重要項目。</span><span class="sxs-lookup"><span data-stu-id="317de-119">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="317de-120">透過使用資料註解屬性，您便可以只透過單一程式碼變更來修正每個顯示資料頁面中的顯示格式。</span><span class="sxs-lookup"><span data-stu-id="317de-120">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="317de-121">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。</span><span class="sxs-lookup"><span data-stu-id="317de-121">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="317de-122">在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="317de-122">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="317de-123">[DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。</span><span class="sxs-lookup"><span data-stu-id="317de-123">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="317de-124">例如：</span><span class="sxs-lookup"><span data-stu-id="317de-124">For example:</span></span>

* <span data-ttu-id="317de-125">`DataType.EmailAddress` 會自動建立 `mailto:` 連結。</span><span class="sxs-lookup"><span data-stu-id="317de-125">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="317de-126">`DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。</span><span class="sxs-lookup"><span data-stu-id="317de-126">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="317de-127">`DataType` 屬性會發出 HTML5 `data-` (發音為 data dash) 屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-127">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="317de-128">`DataType` 屬性不會提供驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-128">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="317de-129">DisplayFormat 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-129">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="317de-130">`DataType.Date` 未指定顯示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="317de-130">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="317de-131">根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-131">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="317de-132">`DisplayFormat` 屬性會用來明確指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="317de-132">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="317de-133">`ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。</span><span class="sxs-lookup"><span data-stu-id="317de-133">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="317de-134">某些欄位不應該使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="317de-134">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="317de-135">例如，貨幣符號通常不應顯示在編輯文字方塊中。</span><span class="sxs-lookup"><span data-stu-id="317de-135">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="317de-136">`DisplayFormat` 屬性可由自身使用。</span><span class="sxs-lookup"><span data-stu-id="317de-136">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="317de-137">通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。</span><span class="sxs-lookup"><span data-stu-id="317de-137">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="317de-138">`DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。</span><span class="sxs-lookup"><span data-stu-id="317de-138">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="317de-139">`DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：</span><span class="sxs-lookup"><span data-stu-id="317de-139">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="317de-140">瀏覽器可以啟用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="317de-140">The browser can enable HTML5 features.</span></span> <span data-ttu-id="317de-141">例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結和用戶端輸入驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-141">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="317de-142">根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。</span><span class="sxs-lookup"><span data-stu-id="317de-142">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="317de-143">如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="317de-143">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="317de-144">StringLength 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-144">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="317de-145">您可使用屬性指定資料驗證規則和驗證錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="317de-145">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="317de-146">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。</span><span class="sxs-lookup"><span data-stu-id="317de-146">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="317de-147">此處所顯示的程式碼會限制名稱不得超過 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="317de-147">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="317de-148">設定字串長度下限的範例會在[稍後](#the-required-attribute)顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-148">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="317de-149">`StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-149">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="317de-150">最小值對資料庫結構描述不會造成任何影響。</span><span class="sxs-lookup"><span data-stu-id="317de-150">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="317de-151">`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。</span><span class="sxs-lookup"><span data-stu-id="317de-151">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="317de-152">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用來將限制套用到輸入。</span><span class="sxs-lookup"><span data-stu-id="317de-152">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="317de-153">例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：</span><span class="sxs-lookup"><span data-stu-id="317de-153">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="317de-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="317de-155">在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。</span><span class="sxs-lookup"><span data-stu-id="317de-155">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="317de-157">上述影像顯示了 `Student` 資料表的結構描述。</span><span class="sxs-lookup"><span data-stu-id="317de-157">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="317de-158">名稱欄位的型別為 `nvarchar(MAX)`。</span><span class="sxs-lookup"><span data-stu-id="317de-158">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="317de-159">在本教學課程稍後建立移轉並套用時，名稱欄位會因為字串長度屬性而變更為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="317de-159">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-160">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-160">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="317de-161">使用 SQLite 工具，檢查資料表的資料行定義 `Student` 。</span><span class="sxs-lookup"><span data-stu-id="317de-161">Using a SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="317de-162">名稱欄位的型別為 `Text`。</span><span class="sxs-lookup"><span data-stu-id="317de-162">The name fields have type `Text`.</span></span> <span data-ttu-id="317de-163">請注意，第一個名稱欄位稱為 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="317de-163">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="317de-164">在下一節中， `FirstMidName` 會變更為 `FirstName` 。</span><span class="sxs-lookup"><span data-stu-id="317de-164">In the next section, `FirstMidName` is changed to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="317de-165">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-165">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="317de-166">可控制類別和屬性如何對應到資料庫的屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-166">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="317de-167">在 `Student` 模型中，`Column` 屬性會用來將 `FirstMidName` 屬性名稱對應到資料庫中的 "FirstName"。</span><span class="sxs-lookup"><span data-stu-id="317de-167">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="317de-168">建立資料庫時，模型上的屬性名稱會用來作為資料行名稱 (使用 `Column` 屬性時除外)。</span><span class="sxs-lookup"><span data-stu-id="317de-168">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="317de-169">`Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。</span><span class="sxs-lookup"><span data-stu-id="317de-169">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="317de-170">透過 `[Column]` 屬性，資料模型中 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="317de-170">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="317de-171">新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。</span><span class="sxs-lookup"><span data-stu-id="317de-171">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="317de-172">支援 `SchoolContext` 的模型不再符合資料庫。</span><span class="sxs-lookup"><span data-stu-id="317de-172">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="317de-173">這種不一致會透過在本教學課程中稍後部分新增移轉來解決。</span><span class="sxs-lookup"><span data-stu-id="317de-173">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="317de-174">Required 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-174">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="317de-175">`Required` 屬性會讓名稱屬性成為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="317de-175">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="317de-176">針對不可為 Null 型別的實值型別 (例如 `DateTime`、`int` 和 `double`)，`Required` 屬性並非必要項目。</span><span class="sxs-lookup"><span data-stu-id="317de-176">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="317de-177">不可為 Null 的類型會自動視為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="317de-177">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="317de-178">`Required` 屬性必須搭配 `MinimumLength` 使用，才能強制執行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="317de-178">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="317de-179">`MinimumLength` 與 `Required` 允許空白字元以滿足驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-179">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="317de-180">使用 `RegularExpression` 屬性來完全控制字串。</span><span class="sxs-lookup"><span data-stu-id="317de-180">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="317de-181">Display 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-181">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="317de-182">`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。</span><span class="sxs-lookup"><span data-stu-id="317de-182">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="317de-183">預設標題中沒有使用空白分隔文字，例如「姓氏」。</span><span class="sxs-lookup"><span data-stu-id="317de-183">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="317de-184">建立移轉</span><span class="sxs-lookup"><span data-stu-id="317de-184">Create a migration</span></span>

<span data-ttu-id="317de-185">執行應用程式並移至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="317de-185">Run the app and go to the Students page.</span></span> <span data-ttu-id="317de-186">隨即擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="317de-186">An exception is thrown.</span></span> <span data-ttu-id="317de-187">`[Column]` 屬性會造成 EF 預期尋找名為 `FirstName` 的資料行，但資料庫中的資料行名稱仍是 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="317de-187">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-188">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="317de-189">錯誤訊息會與下列範例相似：</span><span class="sxs-lookup"><span data-stu-id="317de-189">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
There are pending model changes
Pending model changes are detected in the following:

SchoolContext
```

* <span data-ttu-id="317de-190">在 PMC 中，輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-190">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
   
  ```

  <span data-ttu-id="317de-191">這些命令中的第一個命令會產生下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="317de-191">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="317de-192">產生警告的理由是名稱欄位現在限制長度為 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="317de-192">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="317de-193">若資料庫中的名稱超過 50 個字元，則第 51 到最後一個字元便會遺失。</span><span class="sxs-lookup"><span data-stu-id="317de-193">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="317de-194">在 SSOX 中開啟 Student 資料表：</span><span class="sxs-lookup"><span data-stu-id="317de-194">Open the Student table in SSOX:</span></span>

  ![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="317de-196">在套用移轉前，名稱資料行的型別為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="317de-196">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="317de-197">名稱資料行現在為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="317de-197">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="317de-198">資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="317de-198">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="317de-200">錯誤訊息會與下列範例相似：</span><span class="sxs-lookup"><span data-stu-id="317de-200">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="317de-201">在專案資料夾中開啟命令視窗。</span><span class="sxs-lookup"><span data-stu-id="317de-201">Open a command window in the project folder.</span></span> <span data-ttu-id="317de-202">輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-202">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="317de-203">資料庫更新命令會顯示與下列範例相似的錯誤：</span><span class="sxs-lookup"><span data-stu-id="317de-203">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="317de-204">針對本教學課程，通過此錯誤的方法是刪除和重新建立初始移轉。</span><span class="sxs-lookup"><span data-stu-id="317de-204">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="317de-205">如需詳細資訊，請參閱[移轉教學課程](xref:data/ef-rp/migrations)頂端的 SQLite 警告附註。</span><span class="sxs-lookup"><span data-stu-id="317de-205">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="317de-206">刪除 *Migrations* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="317de-206">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="317de-207">執行下列命令來卸除資料庫、建立新的初始移轉，然後套用移轉：</span><span class="sxs-lookup"><span data-stu-id="317de-207">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
   
  ```

* <span data-ttu-id="317de-208">使用 SQLite 工具檢查 Student 資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-208">Examine the Student table with a SQLite tool.</span></span> <span data-ttu-id="317de-209">目前的資料行 `FirstMidName` `FirstName` 。</span><span class="sxs-lookup"><span data-stu-id="317de-209">The column that was `FirstMidName` is now `FirstName`.</span></span>

---

* <span data-ttu-id="317de-210">執行應用程式並移至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="317de-210">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="317de-211">請注意時間並未輸入或和日期一同顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-211">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="317de-212">選取 [新建] 然後嘗試輸入超過 50 個字元的名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-212">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="317de-213">在下列各節中，在某些階段建置應用程式會產生編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="317de-213">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="317de-214">指令會指定何時應建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-214">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="317de-215">Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="317de-215">The Instructor Entity</span></span>

<!-- no longer using PJT
![Instructor entity](complex-data-model/_static/instructor-entity.png) -->

<span data-ttu-id="317de-216">使用下列程式碼建立 *Models/Instructor.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-216">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="317de-217">在同一行上可以有多個屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-217">Multiple attributes can be on one line.</span></span> <span data-ttu-id="317de-218">`HireDate` 屬性可以下列方式撰寫：</span><span class="sxs-lookup"><span data-stu-id="317de-218">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="317de-219">導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-219">Navigation properties</span></span>

<span data-ttu-id="317de-220">`Courses` 和 `OfficeAssignment` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-220">The `Courses` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="317de-221">由於講師可以教授任何數量的課程，因此 `Courses` 已定義為一個集合。</span><span class="sxs-lookup"><span data-stu-id="317de-221">An instructor can teach any number of courses, so `Courses` is defined as a collection.</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="317de-222">講師最多只能擁有一間辦公室，因此 `OfficeAssignment` 屬性會保存單一 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-222">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="317de-223">若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-223">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="317de-224">OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="317de-224">The OfficeAssignment entity</span></span>

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="317de-226">使用下列程式碼建立 *Models/OfficeAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-226">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="317de-227">Key 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-227">The Key attribute</span></span>

<span data-ttu-id="317de-228">屬性 [`[Key]`](xref:System.ComponentModel.DataAnnotations.KeyAttribute) （attribute）會用來將屬性（attribute）識別為 primary key (PK) 當屬性名稱不是 `classnameID` 或時 `ID` 。</span><span class="sxs-lookup"><span data-stu-id="317de-228">The [`[Key]`](xref:System.ComponentModel.DataAnnotations.KeyAttribute) attribute is used to identify a property as the primary key (PK) when the property name is something other than `classnameID` or `ID`.</span></span>

<span data-ttu-id="317de-229">在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-229">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="317de-230">辦公室指派只存在於與其受指派的講師關聯中。</span><span class="sxs-lookup"><span data-stu-id="317de-230">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="317de-231">`OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。</span><span class="sxs-lookup"><span data-stu-id="317de-231">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="317de-232">當某個資料表中的 PK 同時為 PK 和另一個資料表中的 FK 時，就會發生一到零或一種關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-232">A one-to-zero-or-one relationship occurs when a PK in one table is both a PK and a FK in another table.</span></span>

<span data-ttu-id="317de-233">EF Core 無法將 `InstructorID` 自動識別為 `OfficeAssignment` 的主索引鍵，因為 `InstructorID` 並未遵循識別碼或 classnameID 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="317de-233">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="317de-234">因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：</span><span class="sxs-lookup"><span data-stu-id="317de-234">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="317de-235">根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-235">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span> <span data-ttu-id="317de-236">如需詳細資訊，請參閱 [EF 金鑰](/ef/core/modeling/keys)。</span><span class="sxs-lookup"><span data-stu-id="317de-236">For more information, see [EF Keys](/ef/core/modeling/keys).</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="317de-237">Instructor 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-237">The Instructor navigation property</span></span>

<span data-ttu-id="317de-238">`Instructor.OfficeAssignment` 導覽屬性可以是 Null，因為指定講師可能不會有 `OfficeAssignment` 資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="317de-239">講師可能沒有辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="317de-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="317de-240">`OfficeAssignment.Instructor` 導覽屬性一律會擁有一個講師實體，因為外部索引鍵 `InstructorID` 的型別是 `int`，其為不可為 Null 的實值型別。</span><span class="sxs-lookup"><span data-stu-id="317de-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="317de-241">辦公室指派無法獨立於講師之外存在。</span><span class="sxs-lookup"><span data-stu-id="317de-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="317de-242">當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。</span><span class="sxs-lookup"><span data-stu-id="317de-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="317de-243">Course 實體</span><span class="sxs-lookup"><span data-stu-id="317de-243">The Course Entity</span></span>

<span data-ttu-id="317de-244">使用下列程式碼更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-244">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="317de-245">`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="317de-245">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="317de-246">`DepartmentID` 會指向相關的 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-246">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="317de-247">`Course` 實體具有一個 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-247">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="317de-248">若模型針對相關實體已有導覽屬性，則 EF Core 針對資料模型便不需要外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-248">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="317de-249">EF Core 會自動在資料庫中需要的任何地方建立 FK。</span><span class="sxs-lookup"><span data-stu-id="317de-249">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="317de-250">EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="317de-250">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="317de-251">但是，在資料模型中明確包含 FK (外部索引鍵) 可讓更新更簡單且更有效率。</span><span class="sxs-lookup"><span data-stu-id="317de-251">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="317de-252">例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-252">For example, consider a model where the FK property `DepartmentID` is ***not*** included.</span></span> <span data-ttu-id="317de-253">當擷取課程實體以進行編輯時：</span><span class="sxs-lookup"><span data-stu-id="317de-253">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="317de-254">`Department` `null` 如果未明確載入，則為屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-254">The `Department` property is `null` if it's not explicitly loaded.</span></span>
* <span data-ttu-id="317de-255">若要更新課程實體，必須先擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-255">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="317de-256">當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-256">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="317de-257">DatabaseGenerated 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-257">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="317de-258">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="317de-258">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="317de-259">根據預設，EF Core 會假設 PK (主索引鍵) 的值是由資料庫所產生。</span><span class="sxs-lookup"><span data-stu-id="317de-259">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="317de-260">由資料庫產生主索引鍵通常是最佳做法。</span><span class="sxs-lookup"><span data-stu-id="317de-260">Database-generated is generally the best approach.</span></span> <span data-ttu-id="317de-261">針對 `Course` 實體，使用者指定了 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-261">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="317de-262">例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。</span><span class="sxs-lookup"><span data-stu-id="317de-262">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="317de-263">`DatabaseGenerated` 屬性也可用於產生預設值。</span><span class="sxs-lookup"><span data-stu-id="317de-263">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="317de-264">例如，資料庫可以自動產生日期欄位來記錄建立或更新資料列的日期。</span><span class="sxs-lookup"><span data-stu-id="317de-264">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="317de-265">如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="317de-265">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-266">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-266">Foreign key and navigation properties</span></span>

<span data-ttu-id="317de-267">`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-267">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="317de-268">由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-268">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="317de-269">由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="317de-269">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="317de-270">課程可由多個講師進行教授，因此 `Instructors` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="317de-270">A course may be taught by multiple instructors, so the `Instructors` navigation property is a collection:</span></span>

```csharp
        public ICollection<Instructor> Instructors { get; set; }
```

## <a name="the-department-entity"></a><span data-ttu-id="317de-271">Department 實體</span><span class="sxs-lookup"><span data-stu-id="317de-271">The Department entity</span></span>

<span data-ttu-id="317de-272">使用下列程式碼建立 *Models/Department.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-272">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippet1)]

### <a name="the-column-attribute"></a><span data-ttu-id="317de-273">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-273">The Column attribute</span></span>

<span data-ttu-id="317de-274">先前，`Column` 屬性主要用於變更資料行的名稱對應。</span><span class="sxs-lookup"><span data-stu-id="317de-274">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="317de-275">在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。</span><span class="sxs-lookup"><span data-stu-id="317de-275">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="317de-276">`Budget` 資料行在資料庫中是使用 SQL Server 的 money 型別來定義：</span><span class="sxs-lookup"><span data-stu-id="317de-276">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="317de-277">通常您不需要資料行對應。</span><span class="sxs-lookup"><span data-stu-id="317de-277">Column mapping is generally not required.</span></span> <span data-ttu-id="317de-278">EF Core 會根據屬性的 CLR 型別來選擇適當 SQL Server 資料型別。</span><span class="sxs-lookup"><span data-stu-id="317de-278">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="317de-279">CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。</span><span class="sxs-lookup"><span data-stu-id="317de-279">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="317de-280">由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。</span><span class="sxs-lookup"><span data-stu-id="317de-280">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-281">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-281">Foreign key and navigation properties</span></span>

<span data-ttu-id="317de-282">FK 及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-282">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="317de-283">部門可能有或可能沒有系統管理員。</span><span class="sxs-lookup"><span data-stu-id="317de-283">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="317de-284">系統管理員一律為講師。</span><span class="sxs-lookup"><span data-stu-id="317de-284">An administrator is always an instructor.</span></span> <span data-ttu-id="317de-285">因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。</span><span class="sxs-lookup"><span data-stu-id="317de-285">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="317de-286">導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：</span><span class="sxs-lookup"><span data-stu-id="317de-286">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="317de-287">`?`上述程式碼中的會指定屬性為可為 null。</span><span class="sxs-lookup"><span data-stu-id="317de-287">The `?` in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="317de-288">部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-288">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="317de-289">根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="317de-289">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="317de-290">此預設行為可能會導致循環串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="317de-290">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="317de-291">循環串聯刪除規則會在新增移轉時造成例外狀況。</span><span class="sxs-lookup"><span data-stu-id="317de-291">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="317de-292">例如，若 `Department.InstructorID` 屬性已定義成不可為 Null，EF Core 便會設定串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="317de-292">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="317de-293">在這種情況下，若指派為部門管理員的講師遭到刪除，則會同時刪除部門。</span><span class="sxs-lookup"><span data-stu-id="317de-293">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="317de-294">在這種情況下，限制規則會更有意義。</span><span class="sxs-lookup"><span data-stu-id="317de-294">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="317de-295">下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，並停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="317de-295">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

<!-- todo review: Why update, just go with this from the start 
## The Enrollment entity

An enrollment record is for one course taken by one student.

![Enrollment entity](complex-data-model/_static/enrollment-entity.png)

Update *Models/Enrollment.cs* with the following code:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

-->

### <a name="the-enrollment-foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-296">註冊外鍵和導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-296">The Enrollment foreign key and navigation properties</span></span>

<span data-ttu-id="317de-297">註冊記錄是某位學生參加的一門課程。</span><span class="sxs-lookup"><span data-stu-id="317de-297">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png) 

<span data-ttu-id="317de-299">使用下列程式碼更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-299">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Enrollment.cs?highlight=1,15)]

<span data-ttu-id="317de-300">FK 屬性及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-300">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="317de-301">註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-301">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="317de-302">註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-302">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="317de-303">Many-to-Many Relationships</span><span class="sxs-lookup"><span data-stu-id="317de-303">Many-to-Many Relationships</span></span>

<span data-ttu-id="317de-304">在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-304">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="317de-305">`Enrollment`實體會在資料庫中做為多對多聯結資料表 \***與** 裝載 _。</span><span class="sxs-lookup"><span data-stu-id="317de-305">The `Enrollment` entity functions as a many-to-many join table \***with payload** _ in the database.</span></span> <span data-ttu-id="317de-306">_ *_With_* 裝載 \* 表示 `Enrollment` 除了 fk 聯結資料表以外，資料表還包含額外的資料。</span><span class="sxs-lookup"><span data-stu-id="317de-306">_ *_With payload_*\* means that the `Enrollment` table contains additional data besides FKs for the joined tables.</span></span> <span data-ttu-id="317de-307">在 `Enrollment` 實體中，fk 以外的其他資料是 PK 和 `Grade` 。</span><span class="sxs-lookup"><span data-stu-id="317de-307">In the `Enrollment` entity, the additional data besides FKs are the PK and `Grade`.</span></span>

<span data-ttu-id="317de-308">下列圖例展示了在實體圖表中這些關聯性的樣子。</span><span class="sxs-lookup"><span data-stu-id="317de-308">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="317de-309">(此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。</span><span class="sxs-lookup"><span data-stu-id="317de-309">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="317de-310">建立圖表並不是此教學課程的一部分)。</span><span class="sxs-lookup"><span data-stu-id="317de-310">Creating the diagram isn't part of the tutorial.)</span></span>

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

<span data-ttu-id="317de-312">每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (\*)」，顯示其為一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-312">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="317de-313">如果 `Enrollment` 資料表不包含等級資訊，則只需要包含這兩個 fk `CourseID` 和 `StudentID` 。</span><span class="sxs-lookup"><span data-stu-id="317de-313">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs, `CourseID` and `StudentID`.</span></span> <span data-ttu-id="317de-314">沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。</span><span class="sxs-lookup"><span data-stu-id="317de-314">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="317de-315">`Instructor`和 `Course` 實體具有多對多關聯性，並使用 PJT。</span><span class="sxs-lookup"><span data-stu-id="317de-315">The `Instructor` and `Course` entities have a many-to-many relationship using a PJT.</span></span>

<!--
## The CourseAssignment entity

![CourseAssignment entity](complex-data-model/_static/courseassignment-entity.png)

Create *Models/CourseAssignment.cs* with the following code:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

It's common to name a join entity `EntityName1EntityName2`. For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`. However, we recommend using a name that describes the relationship.

Data models start out simple and grow. Join tables without payload (PJTs) frequently evolve to include payload. By starting with a descriptive entity name, the name doesn't need to change when the join table changes. Ideally, the join entity would have its own natural (possibly single word) name in the business domain. For example, Books and Customers could be linked with a join entity called Ratings. For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.


### Composite key

The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table. `CourseAssignment` doesn't require a dedicated PK. The `InstructorID` and `CourseID` properties function as a composite PK. The only way to specify composite PKs to EF Core is with the *fluent API*. The next section shows how to configure the composite PK.

The composite key ensures that:

* Multiple rows are allowed for one course.
* Multiple rows are allowed for one instructor.
* Multiple rows aren't allowed for the same instructor and course.

The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible. To prevent such duplicates:

* Add a unique index on the FK fields, or
* Configure `Enrollment` with a primary composite key similar to `CourseAssignment`. For more information, see [Indexes](/ef/core/modeling/indexes).

-->

## <a name="update-the-database-context"></a><span data-ttu-id="317de-316">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="317de-316">Update the database context</span></span>

<span data-ttu-id="317de-317">使用下列程式碼來更新 *Data/SchoolContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-317">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Data/SchoolContext.cs?name=snippet_SS&highlight=15-17,21-28)]

<!-- TODO review -->
<span data-ttu-id="317de-318">上述程式碼會新增新的實體和：</span><span class="sxs-lookup"><span data-stu-id="317de-318">The preceding code adds the new entities and:</span></span>

* <span data-ttu-id="317de-319">設定和實體之間的多對多關聯性 `Instructor` `Course` 。</span><span class="sxs-lookup"><span data-stu-id="317de-319">Configures the many-to-many relationship between the `Instructor` and `Course` entities.</span></span>
* <span data-ttu-id="317de-320">設定實體的並行存取權杖 `Department` 。</span><span class="sxs-lookup"><span data-stu-id="317de-320">Configures a concurrency token for the `Department` entity.</span></span> <span data-ttu-id="317de-321">稍後會在本教學課程中討論並行。</span><span class="sxs-lookup"><span data-stu-id="317de-321">Concurrency is discussed later in the tutorial.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="317de-322">屬性的 Fluent API 替代項目</span><span class="sxs-lookup"><span data-stu-id="317de-322">Fluent API alternative to attributes</span></span>

<span data-ttu-id="317de-323">上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。</span><span class="sxs-lookup"><span data-stu-id="317de-323">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="317de-324">此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。</span><span class="sxs-lookup"><span data-stu-id="317de-324">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="317de-325">[下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="317de-325">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="317de-326">在本教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。</span><span class="sxs-lookup"><span data-stu-id="317de-326">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="317de-327">然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。</span><span class="sxs-lookup"><span data-stu-id="317de-327">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="317de-328">某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。</span><span class="sxs-lookup"><span data-stu-id="317de-328">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="317de-329">`MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。</span><span class="sxs-lookup"><span data-stu-id="317de-329">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="317de-330">有些開發人員偏好以獨佔方式使用流暢的 API，讓它們可以保持實體類別的 *清晰*。</span><span class="sxs-lookup"><span data-stu-id="317de-330">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes *clean*.</span></span> <span data-ttu-id="317de-331">屬性和 Fluent API 可混合使用。</span><span class="sxs-lookup"><span data-stu-id="317de-331">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="317de-332">有一些設定只能透過流暢的 API 來完成，例如，指定複合 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-332">There are some configurations that can only be done with the fluent API, for example, , specifying a composite PK.</span></span> <span data-ttu-id="317de-333">有一些設定只能透過屬性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="317de-333">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="317de-334">使用 Fluent API 或屬性的建議做法為：</span><span class="sxs-lookup"><span data-stu-id="317de-334">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="317de-335">從這兩種方法中選擇一項。</span><span class="sxs-lookup"><span data-stu-id="317de-335">Choose one of these two approaches.</span></span>
* <span data-ttu-id="317de-336">持續且盡量使用您選擇的方法。</span><span class="sxs-lookup"><span data-stu-id="317de-336">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="317de-337">本教學課程中使用到的某些屬性主要用於：</span><span class="sxs-lookup"><span data-stu-id="317de-337">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="317de-338">僅驗證 (例如，`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="317de-338">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="317de-339">僅 EF Core 組態 (例如，`HasKey`)。</span><span class="sxs-lookup"><span data-stu-id="317de-339">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="317de-340">驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。</span><span class="sxs-lookup"><span data-stu-id="317de-340">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="317de-341">如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="317de-341">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

<!-- 
## Entity diagram

The following illustration shows the diagram that EF Power Tools create for the completed School model.

![Entity diagram](complex-data-model/_static/diagram.png)

The preceding diagram shows:

* Several one-to-many relationship lines (1 to \*).
* The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.
* The zero-or-one-to-many relationship line (0..1 to *) between the `Instructor` and `Department` entities.
-->

## <a name="seed-the-database"></a><span data-ttu-id="317de-342">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="317de-342">Seed the database</span></span>

<span data-ttu-id="317de-343">更新 *Data/DbInitializer.cs* 中的程式碼：</span><span class="sxs-lookup"><span data-stu-id="317de-343">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu50/Data/DbInitializer2.cs?name=snippet)]

<span data-ttu-id="317de-344">上述程式碼為新的實體提供了種子資料。</span><span class="sxs-lookup"><span data-stu-id="317de-344">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="317de-345">此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。</span><span class="sxs-lookup"><span data-stu-id="317de-345">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="317de-346">範例資料主要用於測試。</span><span class="sxs-lookup"><span data-stu-id="317de-346">The sample data is used for testing.</span></span>

<!-- This is beyond the scope of this tutorial too expensive to maintain, so removing
## Add a migration

Build the project.

# [Visual Studio](#tab/visual-studio)

In PMC, run the following command.

```powershell
Add-Migration ComplexDataModel
```

The preceding command displays a warning about possible data loss.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

If the `database update` command is run, the following error is produced:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

The next section fixes this error.

# [Visual Studio Code](#tab/visual-studio-code)

If migration is added and the `database update` command is run, the following error is returned:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

The next section fixes this error.

---

-->

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="317de-347">套用移轉或卸除並重新建立</span><span class="sxs-lookup"><span data-stu-id="317de-347">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="317de-348">使用現有的資料庫時，有兩種方法可以變更資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-348">With the existing database, there are two approaches to changing the database:</span></span>

* <span data-ttu-id="317de-349">[卸除並重新建立資料庫](#drop)。</span><span class="sxs-lookup"><span data-stu-id="317de-349">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="317de-350">如果使用 SQLite，請選擇此區段。</span><span class="sxs-lookup"><span data-stu-id="317de-350">Choose this section if when using SQLite.</span></span>
* <span data-ttu-id="317de-351">[將遷移套用至現有的資料庫](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="317de-351">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="317de-352">本節中的說明僅適用於 SQL Server，不適用於 ***SQLite***。</span><span class="sxs-lookup"><span data-stu-id="317de-352">The instructions in this section work for SQL Server only, ***not for SQLite***.</span></span>

<span data-ttu-id="317de-353">這兩種選擇都適用於 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="317de-353">Either choice works for SQL Server.</span></span> <span data-ttu-id="317de-354">雖然套用移轉方法更複雜且耗時，但它是現實世界生產環境的慣用方法。</span><span class="sxs-lookup"><span data-stu-id="317de-354">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span>

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="317de-355">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="317de-355">Drop and re-create the database</span></span>

<span data-ttu-id="317de-356">強制 EF Core 建立新的資料庫、卸除並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-356">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-357">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-357">Visual Studio</span></span>](#tab/visual-studio)

  * <span data-ttu-id="317de-358">刪除 *Migrations* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="317de-358">Delete the *Migrations* folder.</span></span>
  * <span data-ttu-id="317de-359">在 **封裝管理員主控台** 中 (PMC) 上，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-359">In the **Package Manager Console** (PMC), run the following commands:</span></span>

  ```powershell
  Drop-Database
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-360">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-360">Visual Studio Code</span></span>](#tab/visual-studio-code)

  * <span data-ttu-id="317de-361">開啟命令視窗並巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="317de-361">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="317de-362">專案資料夾包含 *ContosoUniversity.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="317de-362">The project folder contains the *ContosoUniversity.csproj* file.</span></span>
  * <span data-ttu-id="317de-363">刪除 *Migrations* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="317de-363">Delete the *Migrations* folder.</span></span>
  * <span data-ttu-id="317de-364">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-364">Run the following commands:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

 ---

<span data-ttu-id="317de-365">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-365">Run the app.</span></span> <span data-ttu-id="317de-366">執行應用程式會執行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="317de-366">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="317de-367">`DbInitializer.Initialize` 會填入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="317de-367">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-368">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-368">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="317de-369">在 SSOX 中開啟資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-369">Open the database in SSOX:</span></span>

  * <span data-ttu-id="317de-370">若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="317de-370">If SSOX was opened previously, click the **Refresh** button.</span></span>
  * <span data-ttu-id="317de-371">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="317de-371">Expand the **Tables** node.</span></span> <span data-ttu-id="317de-372">建立的資料表便會顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-372">The created tables are displayed.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-373">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-373">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="317de-374">使用 SQLite 工具來檢查資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-374">Use a SQLite tool to examine the database:</span></span>

* <span data-ttu-id="317de-375">新的資料表和資料行。</span><span class="sxs-lookup"><span data-stu-id="317de-375">New tables and columns.</span></span>
* <span data-ttu-id="317de-376">在資料表中植入資料。</span><span class="sxs-lookup"><span data-stu-id="317de-376">Seeded data in tables.</span></span>

---

<!-- Dropped, see previous comment 
<a name="applyexisting"></a>

## Apply the migration

This section is optional. These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.

When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data. With production data, steps must be taken to migrate the existing data. This section provides an example of fixing FK constraint violations. Don't make these code changes without a backup. Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.

The *{timestamp}_ComplexDataModel.cs* file contains the following code:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table. The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.

To make the `ComplexDataModel` migration work with existing data:

* Change the code to give the new column (`DepartmentID`) a default value.
* Create a fake department named "Temp" to act as the default department.

#### Fix the foreign key constraints

In the `ComplexDataModel` migration class, update the `Up` method:

* Open the *{timestamp}_ComplexDataModel.cs* file.
* Comment out the line of code that adds the `DepartmentID` column to the `Course` table.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Add the following highlighted code. The new code goes after the `.CreateTable( name: "Department"` block:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.

The way of handling the situation shown here is simplified for this tutorial. A production app would:

* Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.
* Not use the "Temp" department or the default value for `Course.DepartmentID`.

# [Visual Studio](#tab/visual-studio)

* In the **Package Manager Console** (PMC), run the following command:

  ```powershell
  Update-Database
  ```

Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables. (Cascade delete will take care of the Enrollment table.)

# [Visual Studio Code](#tab/visual-studio-code)

* If you're using SQL Server LocalDB with Visual Studio Code, run the following command:

  ```dotnetcli
  dotnet ef database update
  ```

---

Run the app. Running the app runs the `DbInitializer.Initialize` method. The `DbInitializer.Initialize` populates the new database.
-->

## <a name="next-steps"></a><span data-ttu-id="317de-377">下一步</span><span class="sxs-lookup"><span data-stu-id="317de-377">Next steps</span></span>

<span data-ttu-id="317de-378">接下來的兩個教學課程會示範如何讀取和更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="317de-378">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="317de-379">[上一個教學](xref:data/ef-rp/migrations) 
>  課程[下一個教學](xref:data/ef-rp/read-related-data)課程</span><span class="sxs-lookup"><span data-stu-id="317de-379">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="317de-380">先前的教學課程建立了基本的資料模型，該模型由三個實體組成。</span><span class="sxs-lookup"><span data-stu-id="317de-380">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="317de-381">在本教學課程中：</span><span class="sxs-lookup"><span data-stu-id="317de-381">In this tutorial:</span></span>

* <span data-ttu-id="317de-382">新增更多實體和關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-382">More entities and relationships are added.</span></span>
* <span data-ttu-id="317de-383">藉由指定格式、驗證和資料庫對應規則來自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="317de-383">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="317de-384">已完成的資料模型如下圖所示：</span><span class="sxs-lookup"><span data-stu-id="317de-384">The completed data model is shown in the following illustration:</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="317de-386">Student 實體</span><span class="sxs-lookup"><span data-stu-id="317de-386">The Student entity</span></span>

![Student 實體](complex-data-model/_static/student-entity.png)

<span data-ttu-id="317de-388">將 *Models/Student.cs* 中的程式碼替換成下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="317de-388">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="317de-389">上述程式碼會新增 `FullName` 屬性，並將下列屬性新增到現有屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-389">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="317de-390">FullName 計算屬性</span><span class="sxs-lookup"><span data-stu-id="317de-390">The FullName calculated property</span></span>

<span data-ttu-id="317de-391">`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。</span><span class="sxs-lookup"><span data-stu-id="317de-391">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="317de-392">您無法設定 `FullName`，因此它只會有 get 存取子。</span><span class="sxs-lookup"><span data-stu-id="317de-392">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="317de-393">資料庫中不會建立 `FullName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="317de-393">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="317de-394">DataType 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-394">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="317de-395">針對學生註冊日期，所有頁面目前都會同時顯示日期和一天當中的時間，雖然只要日期才是重要項目。</span><span class="sxs-lookup"><span data-stu-id="317de-395">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="317de-396">透過使用資料註解屬性，您便可以只透過單一程式碼變更來修正每個顯示資料頁面中的顯示格式。</span><span class="sxs-lookup"><span data-stu-id="317de-396">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="317de-397">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。</span><span class="sxs-lookup"><span data-stu-id="317de-397">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="317de-398">在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="317de-398">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="317de-399">[DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。</span><span class="sxs-lookup"><span data-stu-id="317de-399">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="317de-400">例如：</span><span class="sxs-lookup"><span data-stu-id="317de-400">For example:</span></span>

* <span data-ttu-id="317de-401">`DataType.EmailAddress` 會自動建立 `mailto:` 連結。</span><span class="sxs-lookup"><span data-stu-id="317de-401">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="317de-402">`DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。</span><span class="sxs-lookup"><span data-stu-id="317de-402">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="317de-403">`DataType` 屬性會發出 HTML5 `data-` (發音為 data dash) 屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-403">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="317de-404">`DataType` 屬性不會提供驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-404">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="317de-405">DisplayFormat 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-405">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="317de-406">`DataType.Date` 未指定顯示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="317de-406">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="317de-407">根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-407">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="317de-408">`DisplayFormat` 屬性會用來明確指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="317de-408">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="317de-409">`ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。</span><span class="sxs-lookup"><span data-stu-id="317de-409">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="317de-410">某些欄位不應該使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="317de-410">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="317de-411">例如，貨幣符號通常不應顯示在編輯文字方塊中。</span><span class="sxs-lookup"><span data-stu-id="317de-411">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="317de-412">`DisplayFormat` 屬性可由自身使用。</span><span class="sxs-lookup"><span data-stu-id="317de-412">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="317de-413">通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。</span><span class="sxs-lookup"><span data-stu-id="317de-413">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="317de-414">`DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。</span><span class="sxs-lookup"><span data-stu-id="317de-414">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="317de-415">`DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：</span><span class="sxs-lookup"><span data-stu-id="317de-415">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="317de-416">瀏覽器可以啟用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="317de-416">The browser can enable HTML5 features.</span></span> <span data-ttu-id="317de-417">例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結和用戶端輸入驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-417">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="317de-418">根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。</span><span class="sxs-lookup"><span data-stu-id="317de-418">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="317de-419">如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="317de-419">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="317de-420">StringLength 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-420">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="317de-421">您可使用屬性指定資料驗證規則和驗證錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="317de-421">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="317de-422">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。</span><span class="sxs-lookup"><span data-stu-id="317de-422">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="317de-423">此處所顯示的程式碼會限制名稱不得超過 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="317de-423">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="317de-424">設定字串長度下限的範例會在[稍後](#the-required-attribute)顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-424">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="317de-425">`StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-425">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="317de-426">最小值對資料庫結構描述不會造成任何影響。</span><span class="sxs-lookup"><span data-stu-id="317de-426">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="317de-427">`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。</span><span class="sxs-lookup"><span data-stu-id="317de-427">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="317de-428">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用來將限制套用到輸入。</span><span class="sxs-lookup"><span data-stu-id="317de-428">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="317de-429">例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：</span><span class="sxs-lookup"><span data-stu-id="317de-429">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="317de-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="317de-431">在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。</span><span class="sxs-lookup"><span data-stu-id="317de-431">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="317de-433">上述影像顯示了 `Student` 資料表的結構描述。</span><span class="sxs-lookup"><span data-stu-id="317de-433">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="317de-434">名稱欄位的型別為 `nvarchar(MAX)`。</span><span class="sxs-lookup"><span data-stu-id="317de-434">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="317de-435">在本教學課程稍後建立移轉並套用時，名稱欄位會因為字串長度屬性而變更為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="317de-435">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="317de-437">在您的 SQLite 工具中，檢查 `Student` 資料表的資料行定義。</span><span class="sxs-lookup"><span data-stu-id="317de-437">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="317de-438">名稱欄位的型別為 `Text`。</span><span class="sxs-lookup"><span data-stu-id="317de-438">The name fields have type `Text`.</span></span> <span data-ttu-id="317de-439">請注意，第一個名稱欄位稱為 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="317de-439">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="317de-440">在下一節中，您會將該資料行的名稱變更為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="317de-440">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="317de-441">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-441">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="317de-442">可控制類別和屬性如何對應到資料庫的屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-442">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="317de-443">在 `Student` 模型中，`Column` 屬性會用來將 `FirstMidName` 屬性名稱對應到資料庫中的 "FirstName"。</span><span class="sxs-lookup"><span data-stu-id="317de-443">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="317de-444">建立資料庫時，模型上的屬性名稱會用來作為資料行名稱 (使用 `Column` 屬性時除外)。</span><span class="sxs-lookup"><span data-stu-id="317de-444">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="317de-445">`Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。</span><span class="sxs-lookup"><span data-stu-id="317de-445">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="317de-446">透過 `[Column]` 屬性，資料模型中 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="317de-446">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="317de-447">新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。</span><span class="sxs-lookup"><span data-stu-id="317de-447">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="317de-448">支援 `SchoolContext` 的模型不再符合資料庫。</span><span class="sxs-lookup"><span data-stu-id="317de-448">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="317de-449">這種不一致會透過在本教學課程中稍後部分新增移轉來解決。</span><span class="sxs-lookup"><span data-stu-id="317de-449">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="317de-450">Required 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-450">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="317de-451">`Required` 屬性會讓名稱屬性成為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="317de-451">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="317de-452">針對不可為 Null 型別的實值型別 (例如 `DateTime`、`int` 和 `double`)，`Required` 屬性並非必要項目。</span><span class="sxs-lookup"><span data-stu-id="317de-452">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="317de-453">不可為 Null 的類型會自動視為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="317de-453">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="317de-454">`Required` 屬性必須搭配 `MinimumLength` 使用，才能強制執行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="317de-454">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="317de-455">`MinimumLength` 與 `Required` 允許空白字元以滿足驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-455">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="317de-456">使用 `RegularExpression` 屬性來完全控制字串。</span><span class="sxs-lookup"><span data-stu-id="317de-456">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="317de-457">Display 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-457">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="317de-458">`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。</span><span class="sxs-lookup"><span data-stu-id="317de-458">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="317de-459">預設標題中沒有使用空白分隔文字，例如「姓氏」。</span><span class="sxs-lookup"><span data-stu-id="317de-459">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="317de-460">建立移轉</span><span class="sxs-lookup"><span data-stu-id="317de-460">Create a migration</span></span>

<span data-ttu-id="317de-461">執行應用程式並移至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="317de-461">Run the app and go to the Students page.</span></span> <span data-ttu-id="317de-462">隨即擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="317de-462">An exception is thrown.</span></span> <span data-ttu-id="317de-463">`[Column]` 屬性會造成 EF 預期尋找名為 `FirstName` 的資料行，但資料庫中的資料行名稱仍是 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="317de-463">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-464">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="317de-465">錯誤訊息會與下列範例相似：</span><span class="sxs-lookup"><span data-stu-id="317de-465">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="317de-466">在 PMC 中，輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-466">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="317de-467">這些命令中的第一個命令會產生下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="317de-467">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="317de-468">產生警告的理由是名稱欄位現在限制長度為 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="317de-468">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="317de-469">若資料庫中的名稱超過 50 個字元，則第 51 到最後一個字元便會遺失。</span><span class="sxs-lookup"><span data-stu-id="317de-469">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="317de-470">在 SSOX 中開啟 Student 資料表：</span><span class="sxs-lookup"><span data-stu-id="317de-470">Open the Student table in SSOX:</span></span>

  ![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="317de-472">在套用移轉前，名稱資料行的型別為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="317de-472">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="317de-473">名稱資料行現在為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="317de-473">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="317de-474">資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="317de-474">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="317de-476">錯誤訊息會與下列範例相似：</span><span class="sxs-lookup"><span data-stu-id="317de-476">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="317de-477">在專案資料夾中開啟命令視窗。</span><span class="sxs-lookup"><span data-stu-id="317de-477">Open a command window in the project folder.</span></span> <span data-ttu-id="317de-478">輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-478">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="317de-479">資料庫更新命令會顯示與下列範例相似的錯誤：</span><span class="sxs-lookup"><span data-stu-id="317de-479">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="317de-480">針對本教學課程，通過此錯誤的方法是刪除和重新建立初始移轉。</span><span class="sxs-lookup"><span data-stu-id="317de-480">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="317de-481">如需詳細資訊，請參閱[移轉教學課程](xref:data/ef-rp/migrations)頂端的 SQLite 警告附註。</span><span class="sxs-lookup"><span data-stu-id="317de-481">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="317de-482">刪除 *Migrations* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="317de-482">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="317de-483">執行下列命令來卸除資料庫、建立新的初始移轉，然後套用移轉：</span><span class="sxs-lookup"><span data-stu-id="317de-483">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="317de-484">在您的 SQLite 工具中檢查 Student 資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-484">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="317de-485">原先為 FirstMidName 的資料行現在已變更為 FirstName。</span><span class="sxs-lookup"><span data-stu-id="317de-485">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="317de-486">執行應用程式並移至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="317de-486">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="317de-487">請注意時間並未輸入或和日期一同顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-487">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="317de-488">選取 [新建] 然後嘗試輸入超過 50 個字元的名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-488">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="317de-489">在下列各節中，在某些階段建置應用程式會產生編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="317de-489">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="317de-490">指令會指定何時應建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-490">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="317de-491">Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="317de-491">The Instructor Entity</span></span>

![Instructor 實體](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="317de-493">使用下列程式碼建立 *Models/Instructor.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-493">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="317de-494">在同一行上可以有多個屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-494">Multiple attributes can be on one line.</span></span> <span data-ttu-id="317de-495">`HireDate` 屬性可以下列方式撰寫：</span><span class="sxs-lookup"><span data-stu-id="317de-495">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="317de-496">導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-496">Navigation properties</span></span>

<span data-ttu-id="317de-497">`CourseAssignments` 和 `OfficeAssignment` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-497">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="317de-498">由於講師可以教授任何數量的課程，因此 `CourseAssignments` 已定義為一個集合。</span><span class="sxs-lookup"><span data-stu-id="317de-498">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="317de-499">講師最多只能擁有一間辦公室，因此 `OfficeAssignment` 屬性會保存單一 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-499">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="317de-500">若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-500">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="317de-501">OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="317de-501">The OfficeAssignment entity</span></span>

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="317de-503">使用下列程式碼建立 *Models/OfficeAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-503">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="317de-504">Key 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-504">The Key attribute</span></span>

<span data-ttu-id="317de-505">`[Key]` 屬性用於將某個屬性名稱不是 classnameID 或 ID 的屬性識別為主索引鍵 (PK)。</span><span class="sxs-lookup"><span data-stu-id="317de-505">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="317de-506">在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-506">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="317de-507">辦公室指派只存在於與其受指派的講師關聯中。</span><span class="sxs-lookup"><span data-stu-id="317de-507">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="317de-508">`OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。</span><span class="sxs-lookup"><span data-stu-id="317de-508">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="317de-509">EF Core 無法將 `InstructorID` 自動識別為 `OfficeAssignment` 的主索引鍵，因為 `InstructorID` 並未遵循識別碼或 classnameID 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="317de-509">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="317de-510">因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：</span><span class="sxs-lookup"><span data-stu-id="317de-510">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="317de-511">根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-511">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="317de-512">Instructor 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-512">The Instructor navigation property</span></span>

<span data-ttu-id="317de-513">`Instructor.OfficeAssignment` 導覽屬性可以是 Null，因為指定講師可能不會有 `OfficeAssignment` 資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-513">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="317de-514">講師可能沒有辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="317de-514">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="317de-515">`OfficeAssignment.Instructor` 導覽屬性一律會擁有一個講師實體，因為外部索引鍵 `InstructorID` 的型別是 `int`，其為不可為 Null 的實值型別。</span><span class="sxs-lookup"><span data-stu-id="317de-515">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="317de-516">辦公室指派無法獨立於講師之外存在。</span><span class="sxs-lookup"><span data-stu-id="317de-516">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="317de-517">當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。</span><span class="sxs-lookup"><span data-stu-id="317de-517">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="317de-518">Course 實體</span><span class="sxs-lookup"><span data-stu-id="317de-518">The Course Entity</span></span>

![Course 實體](complex-data-model/_static/course-entity.png)

<span data-ttu-id="317de-520">使用下列程式碼更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-520">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="317de-521">`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="317de-521">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="317de-522">`DepartmentID` 會指向相關的 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-522">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="317de-523">`Course` 實體具有一個 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-523">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="317de-524">若模型針對相關實體已有導覽屬性，則 EF Core 針對資料模型便不需要外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-524">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="317de-525">EF Core 會自動在資料庫中需要的任何地方建立 FK。</span><span class="sxs-lookup"><span data-stu-id="317de-525">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="317de-526">EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="317de-526">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="317de-527">但是，在資料模型中明確包含 FK (外部索引鍵) 可讓更新更簡單且更有效率。</span><span class="sxs-lookup"><span data-stu-id="317de-527">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="317de-528">例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-528">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="317de-529">當擷取課程實體以進行編輯時：</span><span class="sxs-lookup"><span data-stu-id="317de-529">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="317de-530">若 `Department` 屬性未明確載入，則其為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-530">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="317de-531">若要更新課程實體，必須先擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-531">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="317de-532">當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-532">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="317de-533">DatabaseGenerated 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-533">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="317de-534">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="317de-534">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="317de-535">根據預設，EF Core 會假設 PK (主索引鍵) 的值是由資料庫所產生。</span><span class="sxs-lookup"><span data-stu-id="317de-535">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="317de-536">由資料庫產生主索引鍵通常是最佳做法。</span><span class="sxs-lookup"><span data-stu-id="317de-536">Database-generated is generally the best approach.</span></span> <span data-ttu-id="317de-537">針對 `Course` 實體，使用者指定了 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-537">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="317de-538">例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。</span><span class="sxs-lookup"><span data-stu-id="317de-538">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="317de-539">`DatabaseGenerated` 屬性也可用於產生預設值。</span><span class="sxs-lookup"><span data-stu-id="317de-539">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="317de-540">例如，資料庫可以自動產生日期欄位來記錄建立或更新資料列的日期。</span><span class="sxs-lookup"><span data-stu-id="317de-540">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="317de-541">如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="317de-541">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-542">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-542">Foreign key and navigation properties</span></span>

<span data-ttu-id="317de-543">`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-543">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="317de-544">由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-544">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="317de-545">由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="317de-545">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="317de-546">課程可由多個講師進行教授，因此 `CourseAssignments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="317de-546">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="317de-547">`CourseAssignment` 會在[稍後](#many-to-many-relationships)進行解釋。</span><span class="sxs-lookup"><span data-stu-id="317de-547">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="317de-548">Department 實體</span><span class="sxs-lookup"><span data-stu-id="317de-548">The Department entity</span></span>

![部門實體](complex-data-model/_static/department-entity.png)

<span data-ttu-id="317de-550">使用下列程式碼建立 *Models/Department.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-550">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="317de-551">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-551">The Column attribute</span></span>

<span data-ttu-id="317de-552">先前，`Column` 屬性主要用於變更資料行的名稱對應。</span><span class="sxs-lookup"><span data-stu-id="317de-552">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="317de-553">在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。</span><span class="sxs-lookup"><span data-stu-id="317de-553">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="317de-554">`Budget` 資料行在資料庫中是使用 SQL Server 的 money 型別來定義：</span><span class="sxs-lookup"><span data-stu-id="317de-554">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="317de-555">通常您不需要資料行對應。</span><span class="sxs-lookup"><span data-stu-id="317de-555">Column mapping is generally not required.</span></span> <span data-ttu-id="317de-556">EF Core 會根據屬性的 CLR 型別來選擇適當 SQL Server 資料型別。</span><span class="sxs-lookup"><span data-stu-id="317de-556">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="317de-557">CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。</span><span class="sxs-lookup"><span data-stu-id="317de-557">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="317de-558">由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。</span><span class="sxs-lookup"><span data-stu-id="317de-558">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-559">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-559">Foreign key and navigation properties</span></span>

<span data-ttu-id="317de-560">FK 及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-560">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="317de-561">部門可能有或可能沒有系統管理員。</span><span class="sxs-lookup"><span data-stu-id="317de-561">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="317de-562">系統管理員一律為講師。</span><span class="sxs-lookup"><span data-stu-id="317de-562">An administrator is always an instructor.</span></span> <span data-ttu-id="317de-563">因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。</span><span class="sxs-lookup"><span data-stu-id="317de-563">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="317de-564">導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：</span><span class="sxs-lookup"><span data-stu-id="317de-564">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="317de-565">上述程式碼中的問號 (?) 表示屬性可為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-565">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="317de-566">部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-566">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="317de-567">根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="317de-567">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="317de-568">此預設行為可能會導致循環串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="317de-568">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="317de-569">循環串聯刪除規則會在新增移轉時造成例外狀況。</span><span class="sxs-lookup"><span data-stu-id="317de-569">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="317de-570">例如，若 `Department.InstructorID` 屬性已定義成不可為 Null，EF Core 便會設定串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="317de-570">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="317de-571">在這種情況下，若指派為部門管理員的講師遭到刪除，則會同時刪除部門。</span><span class="sxs-lookup"><span data-stu-id="317de-571">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="317de-572">在這種情況下，限制規則會更有意義。</span><span class="sxs-lookup"><span data-stu-id="317de-572">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="317de-573">下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，並停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="317de-573">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="317de-574">Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="317de-574">The Enrollment entity</span></span>

<span data-ttu-id="317de-575">註冊記錄是某位學生參加的一門課程。</span><span class="sxs-lookup"><span data-stu-id="317de-575">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="317de-577">使用下列程式碼更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-577">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-578">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-578">Foreign key and navigation properties</span></span>

<span data-ttu-id="317de-579">FK 屬性及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-579">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="317de-580">註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-580">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="317de-581">註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-581">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="317de-582">Many-to-Many Relationships</span><span class="sxs-lookup"><span data-stu-id="317de-582">Many-to-Many Relationships</span></span>

<span data-ttu-id="317de-583">在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-583">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="317de-584">`Enrollment` 實體的功能為資料庫中一個「具有承載」的多對多聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-584">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="317de-585">「具有承載」表示 `Enrollment` 資料表除了聯結資料表 (在此案例中為 PK 和 `Grade`) 的 FK 之外，還包含了額外的資料。</span><span class="sxs-lookup"><span data-stu-id="317de-585">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="317de-586">下列圖例展示了在實體圖表中這些關聯性的樣子。</span><span class="sxs-lookup"><span data-stu-id="317de-586">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="317de-587">(此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。</span><span class="sxs-lookup"><span data-stu-id="317de-587">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="317de-588">建立圖表並不是此教學課程的一部分)。</span><span class="sxs-lookup"><span data-stu-id="317de-588">Creating the diagram isn't part of the tutorial.)</span></span>

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

<span data-ttu-id="317de-590">每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (\*)」，顯示其為一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-590">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="317de-591">若 `Enrollment` 資料表並未包含成績資訊，則其便只需要包含兩個 FK (`CourseID` 和 `StudentID`)。</span><span class="sxs-lookup"><span data-stu-id="317de-591">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="317de-592">沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。</span><span class="sxs-lookup"><span data-stu-id="317de-592">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="317de-593">`Instructor` 和 `Course` 實體具有使用了純聯結資料表的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-593">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="317de-594">注意：EF 6.x 支援多對多關聯性的隱含聯結資料表，但 EF Core 並不支援。</span><span class="sxs-lookup"><span data-stu-id="317de-594">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="317de-595">如需詳細資訊，請參閱 [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (EF Core 2.0 中的多對多關聯性)。</span><span class="sxs-lookup"><span data-stu-id="317de-595">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="317de-596">CourseAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="317de-596">The CourseAssignment entity</span></span>

![CourseAssignment 實體](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="317de-598">使用下列程式碼建立 *Models/CourseAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-598">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="317de-599">Instructor 對 Courses 的多對多關聯性需要聯結資料表，而該聯結資料表的實體為 CourseAssignment。</span><span class="sxs-lookup"><span data-stu-id="317de-599">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="317de-601">通常會將聯結實體命名為 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="317de-601">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="317de-602">例如，使用此模式的 Instructor 對 Courses 聯結資料表將會是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="317de-602">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="317de-603">不過，我們建議使用可描述關聯性的名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-603">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="317de-604">資料模型一開始都是簡單的，之後便會持續成長。</span><span class="sxs-lookup"><span data-stu-id="317de-604">Data models start out simple and grow.</span></span> <span data-ttu-id="317de-605">不含承載的聯結資料表 (PJT) 常常會演變成包含承載。</span><span class="sxs-lookup"><span data-stu-id="317de-605">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="317de-606">藉由在一開始便使用描述性的實體名稱，當聯結資料表變更時便不需要變更名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-606">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="317de-607">理想情況下，聯結實體在公司網域中會有自己的自然 (可能為一個單字) 名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-607">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="317de-608">例如，「書籍」和「客戶」可連結為一個名為「評分」 的聯結實體。</span><span class="sxs-lookup"><span data-stu-id="317de-608">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="317de-609">針對講師-課程多對多關聯性，`CourseAssignment` 會比 `CourseInstructor` 來得好。</span><span class="sxs-lookup"><span data-stu-id="317de-609">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="317de-610">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="317de-610">Composite key</span></span>

<span data-ttu-id="317de-611">`CourseAssignment` (`InstructorID` 及 `CourseID`) 中的兩個 FK 一起搭配使用，便可唯一的識別 `CourseAssignment` 資料表中的每一個資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-611">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="317de-612">`CourseAssignment` 並不需要其專屬的 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-612">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="317de-613">`InstructorID` 及 `CourseID` 屬性的功能便是複合 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-613">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="317de-614">為 EF Core 指定複合 PK 的唯一方法是使用 *Fluent API*。</span><span class="sxs-lookup"><span data-stu-id="317de-614">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="317de-615">下節會說明如何設定複合 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-615">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="317de-616">複合索引鍵可確保：</span><span class="sxs-lookup"><span data-stu-id="317de-616">The composite key ensures that:</span></span>

* <span data-ttu-id="317de-617">一個課程可以有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-617">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="317de-618">一個講師可以有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-618">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="317de-619">不允許相同講師和課程的多個資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-619">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="317de-620">由於 `Enrollment` 聯結實體定義了其自身的 PK，因此這種種類的重複項目是可能的。</span><span class="sxs-lookup"><span data-stu-id="317de-620">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="317de-621">若要防止這類重複項目：</span><span class="sxs-lookup"><span data-stu-id="317de-621">To prevent such duplicates:</span></span>

* <span data-ttu-id="317de-622">在 FK 欄位中新增一個唯一的索引，或</span><span class="sxs-lookup"><span data-stu-id="317de-622">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="317de-623">為 `Enrollment` 設定一個主複合索引鍵，與 `CourseAssignment` 相似。</span><span class="sxs-lookup"><span data-stu-id="317de-623">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="317de-624">如需詳細資訊，請參閱[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="317de-624">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="317de-625">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="317de-625">Update the database context</span></span>

<span data-ttu-id="317de-626">使用下列程式碼來更新 *Data/SchoolContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-626">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="317de-627">上述程式碼會新增一個新實體，並設定 `CourseAssignment` 實體的複合 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-627">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="317de-628">屬性的 Fluent API 替代項目</span><span class="sxs-lookup"><span data-stu-id="317de-628">Fluent API alternative to attributes</span></span>

<span data-ttu-id="317de-629">上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。</span><span class="sxs-lookup"><span data-stu-id="317de-629">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="317de-630">此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。</span><span class="sxs-lookup"><span data-stu-id="317de-630">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="317de-631">[下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="317de-631">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="317de-632">在本教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。</span><span class="sxs-lookup"><span data-stu-id="317de-632">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="317de-633">然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。</span><span class="sxs-lookup"><span data-stu-id="317de-633">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="317de-634">某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。</span><span class="sxs-lookup"><span data-stu-id="317de-634">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="317de-635">`MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。</span><span class="sxs-lookup"><span data-stu-id="317de-635">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="317de-636">某些開發人員偏好單獨使用 Fluent API，使其實體類別保持「整潔」。</span><span class="sxs-lookup"><span data-stu-id="317de-636">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="317de-637">屬性和 Fluent API 可混合使用。</span><span class="sxs-lookup"><span data-stu-id="317de-637">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="317de-638">有一些設定只能透過 Fluent API 完成 (指定複合 PK)。</span><span class="sxs-lookup"><span data-stu-id="317de-638">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="317de-639">有一些設定只能透過屬性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="317de-639">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="317de-640">使用 Fluent API 或屬性的建議做法為：</span><span class="sxs-lookup"><span data-stu-id="317de-640">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="317de-641">從這兩種方法中選擇一項。</span><span class="sxs-lookup"><span data-stu-id="317de-641">Choose one of these two approaches.</span></span>
* <span data-ttu-id="317de-642">持續且盡量使用您選擇的方法。</span><span class="sxs-lookup"><span data-stu-id="317de-642">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="317de-643">本教學課程中使用到的某些屬性主要用於：</span><span class="sxs-lookup"><span data-stu-id="317de-643">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="317de-644">僅驗證 (例如，`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="317de-644">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="317de-645">僅 EF Core 組態 (例如，`HasKey`)。</span><span class="sxs-lookup"><span data-stu-id="317de-645">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="317de-646">驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。</span><span class="sxs-lookup"><span data-stu-id="317de-646">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="317de-647">如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="317de-647">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="317de-648">實體圖表</span><span class="sxs-lookup"><span data-stu-id="317de-648">Entity diagram</span></span>

<span data-ttu-id="317de-649">下圖顯示了 EF Power Tools 為完成的 School 模型建立的圖表。</span><span class="sxs-lookup"><span data-stu-id="317de-649">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

<span data-ttu-id="317de-651">上圖顯示︰</span><span class="sxs-lookup"><span data-stu-id="317de-651">The preceding diagram shows:</span></span>

* <span data-ttu-id="317de-652">數個一對多關聯性線條 (1 對 \*)。</span><span class="sxs-lookup"><span data-stu-id="317de-652">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="317de-653">`Instructor` 和 `OfficeAssignment` 實體之間的一對零或一關聯性線條 (1 對 0..1)。</span><span class="sxs-lookup"><span data-stu-id="317de-653">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="317de-654">`Instructor` 和 `Department` 實體之間的零或一對多關聯性線條 (0..1 對 \*)。</span><span class="sxs-lookup"><span data-stu-id="317de-654">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="317de-655">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="317de-655">Seed the database</span></span>

<span data-ttu-id="317de-656">更新 *Data/DbInitializer.cs* 中的程式碼：</span><span class="sxs-lookup"><span data-stu-id="317de-656">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="317de-657">上述程式碼為新的實體提供了種子資料。</span><span class="sxs-lookup"><span data-stu-id="317de-657">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="317de-658">此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。</span><span class="sxs-lookup"><span data-stu-id="317de-658">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="317de-659">範例資料主要用於測試。</span><span class="sxs-lookup"><span data-stu-id="317de-659">The sample data is used for testing.</span></span> <span data-ttu-id="317de-660">如需如何植入多對多聯結資料表的範例，請參閱 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="317de-660">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="317de-661">新增移轉</span><span class="sxs-lookup"><span data-stu-id="317de-661">Add a migration</span></span>

<span data-ttu-id="317de-662">建置專案。</span><span class="sxs-lookup"><span data-stu-id="317de-662">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-663">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-663">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="317de-664">在 PMC 中，執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="317de-664">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="317de-665">上述命令會顯示關於可能發生資料遺失的警告。</span><span class="sxs-lookup"><span data-stu-id="317de-665">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="317de-666">若執行 `database update` 命令，便會產生下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="317de-666">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="317de-667">在下一節中，您會看到如何針對此錯誤採取動作。</span><span class="sxs-lookup"><span data-stu-id="317de-667">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-668">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-668">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="317de-669">若您新增移轉和執行 `database update` 命令，即會產生下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="317de-669">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="317de-670">在下一節中，您會了解如何避免此錯誤。</span><span class="sxs-lookup"><span data-stu-id="317de-670">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="317de-671">套用移轉或卸除並重新建立</span><span class="sxs-lookup"><span data-stu-id="317de-671">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="317de-672">現在您已經具有現有的資料庫，您需要思考如何將變更套用到該資料庫。</span><span class="sxs-lookup"><span data-stu-id="317de-672">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="317de-673">本教學課程示範兩種替代方法：</span><span class="sxs-lookup"><span data-stu-id="317de-673">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="317de-674">[卸除並重新建立資料庫](#drop)。</span><span class="sxs-lookup"><span data-stu-id="317de-674">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="317de-675">若您正在使用 SQLite，請選擇此節。</span><span class="sxs-lookup"><span data-stu-id="317de-675">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="317de-676">[將遷移套用至現有的資料庫](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="317de-676">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="317de-677">本節中的說明僅適用於 SQL Server，不適用於 **SQLite**。</span><span class="sxs-lookup"><span data-stu-id="317de-677">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="317de-678">這兩種選擇都適用於 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="317de-678">Either choice works for SQL Server.</span></span> <span data-ttu-id="317de-679">雖然套用移轉方法更複雜且耗時，但它是現實世界生產環境的慣用方法。</span><span class="sxs-lookup"><span data-stu-id="317de-679">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="317de-680">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="317de-680">Drop and re-create the database</span></span>

<span data-ttu-id="317de-681">若您正在使用 SQL Server 且想要在下一節中執行套用移轉方法，請[跳過此節](#apply-the-migration)。</span><span class="sxs-lookup"><span data-stu-id="317de-681">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="317de-682">強制 EF Core 建立新的資料庫、卸除並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-682">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-683">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-683">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="317de-684">在 [套件管理員主控台] (PMC) 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-684">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="317de-685">刪除 *Migrations* 資料夾，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-685">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-686">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-686">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="317de-687">開啟命令視窗並巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="317de-687">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="317de-688">專案資料夾包含 *ContosoUniversity.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="317de-688">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="317de-689">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="317de-689">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="317de-690">刪除 *Migrations* 資料夾，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-690">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="317de-691">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-691">Run the app.</span></span> <span data-ttu-id="317de-692">執行應用程式會執行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="317de-692">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="317de-693">`DbInitializer.Initialize` 會填入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="317de-693">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-694">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-694">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="317de-695">在 SSOX 中開啟資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-695">Open the database in SSOX:</span></span>

* <span data-ttu-id="317de-696">若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="317de-696">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="317de-697">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="317de-697">Expand the **Tables** node.</span></span> <span data-ttu-id="317de-698">建立的資料表便會顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-698">The created tables are displayed.</span></span>

  ![SSOX 中的資料表](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="317de-700">檢查 **CourseAssignment** 資料表：</span><span class="sxs-lookup"><span data-stu-id="317de-700">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="317de-701">以滑鼠右鍵按一下 **CourseAssignment** 資料表，然後選取 [檢視資料]。</span><span class="sxs-lookup"><span data-stu-id="317de-701">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="317de-702">驗證 **CourseAssignment** 資料表中是否包含資料。</span><span class="sxs-lookup"><span data-stu-id="317de-702">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX 中的 CourseAssignment 資料](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-704">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-704">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="317de-705">使用您的 SQLite 工具來檢查資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-705">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="317de-706">新的資料表和資料行。</span><span class="sxs-lookup"><span data-stu-id="317de-706">New tables and columns.</span></span>
* <span data-ttu-id="317de-707">在資料表中植入資料，例如 **CourseAssignment** 資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-707">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="317de-708">套用移轉</span><span class="sxs-lookup"><span data-stu-id="317de-708">Apply the migration</span></span>

<span data-ttu-id="317de-709">此為選擇性區段。</span><span class="sxs-lookup"><span data-stu-id="317de-709">This section is optional.</span></span> <span data-ttu-id="317de-710">這些步驟僅適用於 SQL Server LocalDB，且只有在您跳過先前的[卸除並重新建立資料庫](#drop)一節時才適用。</span><span class="sxs-lookup"><span data-stu-id="317de-710">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="317de-711">當使用現有的資料執行移轉作業時，某些 FK 條件約束可能會無法透過現有資料滿足。</span><span class="sxs-lookup"><span data-stu-id="317de-711">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="317de-712">當您使用的是生產資料時，您必須進行幾個步驟才能移轉現有資料。</span><span class="sxs-lookup"><span data-stu-id="317de-712">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="317de-713">本節提供了修正 FK 條件約束違規的範例。</span><span class="sxs-lookup"><span data-stu-id="317de-713">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="317de-714">請不要在沒有備份的情況下進行這些程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="317de-714">Don't make these code changes without a backup.</span></span> <span data-ttu-id="317de-715">若您已完成先前的[卸除並重新建立資料庫](#drop)一節，請不要變更這些程式碼。</span><span class="sxs-lookup"><span data-stu-id="317de-715">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="317de-716">*{timestamp}_ComplexDataModel.cs* 檔案包含了下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="317de-716">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="317de-717">上述程式碼將一個不可為 Null 的 `DepartmentID` FK 新增至 `Course` 資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-717">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="317de-718">先前教學課程中的資料庫在 `Course` 中包含了資料列，因此無法使用移轉來更新資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-718">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="317de-719">若要讓 `ComplexDataModel` 與現有資料進行移轉：</span><span class="sxs-lookup"><span data-stu-id="317de-719">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="317de-720">變更程式碼，以給予新資料行 (`DepartmentID`) 一個新的預設值。</span><span class="sxs-lookup"><span data-stu-id="317de-720">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="317de-721">建立一個名為 "Temp" 的假部門以作為預設部門之用。</span><span class="sxs-lookup"><span data-stu-id="317de-721">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="317de-722">修正外部索引鍵條件約束</span><span class="sxs-lookup"><span data-stu-id="317de-722">Fix the foreign key constraints</span></span>

<span data-ttu-id="317de-723">在 `ComplexDataModel` 移轉類別中，更新 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="317de-723">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="317de-724">開啟 *{timestamp}_ComplexDataModel.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="317de-724">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="317de-725">將新增 `DepartmentID` 資料行至 `Course` 資料表的程式碼全部標為註解。</span><span class="sxs-lookup"><span data-stu-id="317de-725">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="317de-726">新增下列醒目提示程式碼。</span><span class="sxs-lookup"><span data-stu-id="317de-726">Add the following highlighted code.</span></span> <span data-ttu-id="317de-727">新的程式碼位於 `.CreateTable( name: "Department"` 區塊後方：</span><span class="sxs-lookup"><span data-stu-id="317de-727">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="317de-728">透過上述變更，現有的 `Course` 資料列將會在執行 `ComplexDataModel.Up` 方法後與 "Temp" 部門相關。</span><span class="sxs-lookup"><span data-stu-id="317de-728">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="317de-729">此處顯示處理這種情況的方式已針對本教學課程進行簡化。</span><span class="sxs-lookup"><span data-stu-id="317de-729">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="317de-730">生產環境的應用程式會：</span><span class="sxs-lookup"><span data-stu-id="317de-730">A production app would:</span></span>

* <span data-ttu-id="317de-731">包含將 `Department` 資料列及相關 `Course` 資料列新增到新 `Department` 資料列的程式碼或指令碼。</span><span class="sxs-lookup"><span data-stu-id="317de-731">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="317de-732">不使用 "Temp" 部門或 `Course.DepartmentID` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="317de-732">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-733">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-733">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="317de-734">在 [套件管理員主控台] (PMC) 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-734">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="317de-735">因為 `DbInitializer.Initialize` 方法的設計僅適用於空白資料庫，所以請使用 SSOX 刪除 Student 和 Course 資料表中的所有資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-735">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="317de-736">(串聯刪除會負責處理 Enrollment 資料表。)</span><span class="sxs-lookup"><span data-stu-id="317de-736">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-737">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-737">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="317de-738">若您正在搭配 Visual Studio Code 使用 SQL Server LocalDB，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-738">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="317de-739">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-739">Run the app.</span></span> <span data-ttu-id="317de-740">執行應用程式會執行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="317de-740">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="317de-741">`DbInitializer.Initialize` 會填入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="317de-741">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="317de-742">下一步</span><span class="sxs-lookup"><span data-stu-id="317de-742">Next steps</span></span>

<span data-ttu-id="317de-743">接下來的兩個教學課程會示範如何讀取和更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="317de-743">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="317de-744">[上一個教學](xref:data/ef-rp/migrations) 
>  課程[下一個教學](xref:data/ef-rp/read-related-data)課程</span><span class="sxs-lookup"><span data-stu-id="317de-744">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="317de-745">先前的教學課程建立了基本的資料模型，該模型由三個實體組成。</span><span class="sxs-lookup"><span data-stu-id="317de-745">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="317de-746">在本教學課程中：</span><span class="sxs-lookup"><span data-stu-id="317de-746">In this tutorial:</span></span>

* <span data-ttu-id="317de-747">新增更多實體和關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-747">More entities and relationships are added.</span></span>
* <span data-ttu-id="317de-748">藉由指定格式、驗證和資料庫對應規則來自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="317de-748">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="317de-749">下圖顯示已完成資料模型的實體類別：</span><span class="sxs-lookup"><span data-stu-id="317de-749">The entity classes for the completed data model are shown in the following illustration:</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

<span data-ttu-id="317de-751">若您遇到無法解決的問題，請下載[完整應用程式](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="317de-751">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="317de-752">使用屬性自訂資料模型</span><span class="sxs-lookup"><span data-stu-id="317de-752">Customize the data model with attributes</span></span>

<span data-ttu-id="317de-753">在本節中，您會使用屬性自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="317de-753">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="317de-754">DataType 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-754">The DataType attribute</span></span>

<span data-ttu-id="317de-755">學生頁面目前顯示了註冊日期的時間。</span><span class="sxs-lookup"><span data-stu-id="317de-755">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="317de-756">一般而言，日期欄位只會顯示日期，而非時間。</span><span class="sxs-lookup"><span data-stu-id="317de-756">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="317de-757">使用下列醒目提示的程式碼更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-757">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="317de-758">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。</span><span class="sxs-lookup"><span data-stu-id="317de-758">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="317de-759">在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="317de-759">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="317de-760">[DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。</span><span class="sxs-lookup"><span data-stu-id="317de-760">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="317de-761">例如：</span><span class="sxs-lookup"><span data-stu-id="317de-761">For example:</span></span>

* <span data-ttu-id="317de-762">`DataType.EmailAddress` 會自動建立 `mailto:` 連結。</span><span class="sxs-lookup"><span data-stu-id="317de-762">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="317de-763">`DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。</span><span class="sxs-lookup"><span data-stu-id="317de-763">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="317de-764">`DataType` 屬性會發出 HTML 5 `data-` (發音為 data dash) 屬性，可讓 HTML 5 瀏覽器取用。</span><span class="sxs-lookup"><span data-stu-id="317de-764">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="317de-765">`DataType` 屬性不會提供驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-765">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="317de-766">`DataType.Date` 未指定顯示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="317de-766">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="317de-767">根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-767">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="317de-768">`DisplayFormat` 屬性用來明確指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="317de-768">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="317de-769">`ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。</span><span class="sxs-lookup"><span data-stu-id="317de-769">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="317de-770">某些欄位不應該使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="317de-770">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="317de-771">例如，貨幣符號通常不應顯示在編輯文字方塊中。</span><span class="sxs-lookup"><span data-stu-id="317de-771">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="317de-772">`DisplayFormat` 屬性可由自身使用。</span><span class="sxs-lookup"><span data-stu-id="317de-772">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="317de-773">通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。</span><span class="sxs-lookup"><span data-stu-id="317de-773">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="317de-774">`DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。</span><span class="sxs-lookup"><span data-stu-id="317de-774">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="317de-775">`DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：</span><span class="sxs-lookup"><span data-stu-id="317de-775">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="317de-776">瀏覽器可以啟用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="317de-776">The browser can enable HTML5 features.</span></span> <span data-ttu-id="317de-777">例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結、用戶端輸入驗證等。</span><span class="sxs-lookup"><span data-stu-id="317de-777">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="317de-778">根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。</span><span class="sxs-lookup"><span data-stu-id="317de-778">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="317de-779">如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="317de-779">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="317de-780">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-780">Run the app.</span></span> <span data-ttu-id="317de-781">巡覽至 Students [索引] 頁面。</span><span class="sxs-lookup"><span data-stu-id="317de-781">Navigate to the Students Index page.</span></span> <span data-ttu-id="317de-782">時間將不再顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-782">Times are no longer displayed.</span></span> <span data-ttu-id="317de-783">使用 `Student` 模型的每個檢視現在都只會顯示日期，而不會顯示時間。</span><span class="sxs-lookup"><span data-stu-id="317de-783">Every view that uses the `Student` model displays the date without time.</span></span>

![顯示日期而不顯示時間的 Students [索引] 頁面](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="317de-785">StringLength 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-785">The StringLength attribute</span></span>

<span data-ttu-id="317de-786">您可使用屬性指定資料驗證規則和驗證錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="317de-786">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="317de-787">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。</span><span class="sxs-lookup"><span data-stu-id="317de-787">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="317de-788">`StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。</span><span class="sxs-lookup"><span data-stu-id="317de-788">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="317de-789">最小值對資料庫結構描述不會造成任何影響。</span><span class="sxs-lookup"><span data-stu-id="317de-789">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="317de-790">使用下列程式碼更新 `Student` 模型：</span><span class="sxs-lookup"><span data-stu-id="317de-790">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="317de-791">上述的程式碼會限制名稱不得超過 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="317de-791">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="317de-792">`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。</span><span class="sxs-lookup"><span data-stu-id="317de-792">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="317de-793">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用於對輸入套用限制。</span><span class="sxs-lookup"><span data-stu-id="317de-793">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="317de-794">例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：</span><span class="sxs-lookup"><span data-stu-id="317de-794">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="317de-795">執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="317de-795">Run the app:</span></span>

* <span data-ttu-id="317de-796">巡覽至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="317de-796">Navigate to the Students page.</span></span>
* <span data-ttu-id="317de-797">選取 [新建]，然後輸入超過 50 個字元的名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-797">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="317de-798">選取 [建立]，用戶端驗證便會顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="317de-798">Select **Create**, client-side validation shows an error message.</span></span>

![顯示字元長度錯誤的 Students [索引] 頁面](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="317de-800">在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。</span><span class="sxs-lookup"><span data-stu-id="317de-800">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="317de-802">上述影像顯示了 `Student` 資料表的結構描述。</span><span class="sxs-lookup"><span data-stu-id="317de-802">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="317de-803">名稱欄位的類型為 `nvarchar(MAX)`，因為移轉尚未在資料庫中執行。</span><span class="sxs-lookup"><span data-stu-id="317de-803">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="317de-804">在本教學課程的稍後執行移轉後，名稱欄位便會成為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="317de-804">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="317de-805">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-805">The Column attribute</span></span>

<span data-ttu-id="317de-806">可控制類別和屬性如何對應到資料庫的屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-806">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="317de-807">在本節中，`Column` 屬性會用於將 `FirstMidName` 屬性的名稱對應到資料庫中的 "FirstName"。</span><span class="sxs-lookup"><span data-stu-id="317de-807">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="317de-808">當建立資料庫時，模型上的屬性名稱會用於資料行名稱 (除了使用 `Column` 屬性時之外)。</span><span class="sxs-lookup"><span data-stu-id="317de-808">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="317de-809">`Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。</span><span class="sxs-lookup"><span data-stu-id="317de-809">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="317de-810">使用下列醒目提示程式碼更新 *Student.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="317de-810">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="317de-811">經過上述變更之後，應用程式中的 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="317de-811">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="317de-812">新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。</span><span class="sxs-lookup"><span data-stu-id="317de-812">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="317de-813">支援 `SchoolContext` 的模型不再符合資料庫。</span><span class="sxs-lookup"><span data-stu-id="317de-813">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="317de-814">若應用程式在套用移轉之前執行，便會產生下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="317de-814">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="317de-815">若要更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-815">To update the DB:</span></span>

* <span data-ttu-id="317de-816">建置專案。</span><span class="sxs-lookup"><span data-stu-id="317de-816">Build the project.</span></span>
* <span data-ttu-id="317de-817">在專案資料夾中開啟命令視窗。</span><span class="sxs-lookup"><span data-stu-id="317de-817">Open a command window in the project folder.</span></span> <span data-ttu-id="317de-818">輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-818">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-819">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-819">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-820">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-820">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="317de-821">`migrations add ColumnFirstName` 命令會產生下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="317de-821">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="317de-822">產生警告的理由是名稱欄位現在限制長度為 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="317de-822">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="317de-823">若在資料庫中有名稱超過 50 個字元，第 51 個字元到最後一個字元便會遺失。</span><span class="sxs-lookup"><span data-stu-id="317de-823">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="317de-824">測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-824">Test the app.</span></span>

<span data-ttu-id="317de-825">在 SSOX 中開啟 Student 資料表：</span><span class="sxs-lookup"><span data-stu-id="317de-825">Open the Student table in SSOX:</span></span>

![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="317de-827">在移轉套用之前，名稱資料行的類型為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="317de-827">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="317de-828">名稱資料行現在為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="317de-828">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="317de-829">資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="317de-829">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="317de-830">在下節中，在某些階段建置應用程式會產生編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="317de-830">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="317de-831">指令會指定何時應建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-831">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="317de-832">Student 實體更新</span><span class="sxs-lookup"><span data-stu-id="317de-832">Student entity update</span></span>

![Student 實體](complex-data-model/_static/student-entity.png)

<span data-ttu-id="317de-834">使用下列程式碼更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-834">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="317de-835">Required 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-835">The Required attribute</span></span>

<span data-ttu-id="317de-836">`Required` 屬性會讓名稱屬性成為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="317de-836">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="317de-837">針對不可為 Null 的類型，例如實值型別 (`DateTime`、`int`、`double` 等) 等，`Required` 屬性是不需要的。</span><span class="sxs-lookup"><span data-stu-id="317de-837">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="317de-838">不可為 Null 的類型會自動視為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="317de-838">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="317de-839">`Required` 屬性可由 `StringLength` 屬性中的最小長度參數取代：</span><span class="sxs-lookup"><span data-stu-id="317de-839">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="317de-840">Display 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-840">The Display attribute</span></span>

<span data-ttu-id="317de-841">`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。</span><span class="sxs-lookup"><span data-stu-id="317de-841">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="317de-842">預設標題中沒有使用空白分隔文字，例如「姓氏」。</span><span class="sxs-lookup"><span data-stu-id="317de-842">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="317de-843">FullName 計算屬性</span><span class="sxs-lookup"><span data-stu-id="317de-843">The FullName calculated property</span></span>

<span data-ttu-id="317de-844">`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。</span><span class="sxs-lookup"><span data-stu-id="317de-844">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="317de-845">`FullName` 無法進行設定，其只有 get 存取子。</span><span class="sxs-lookup"><span data-stu-id="317de-845">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="317de-846">資料庫中不會建立 `FullName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="317de-846">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="317de-847">建立 Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="317de-847">Create the Instructor Entity</span></span>

![Instructor 實體](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="317de-849">使用下列程式碼建立 *Models/Instructor.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-849">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="317de-850">在同一行上可以有多個屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-850">Multiple attributes can be on one line.</span></span> <span data-ttu-id="317de-851">`HireDate` 屬性可以下列方式撰寫：</span><span class="sxs-lookup"><span data-stu-id="317de-851">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="317de-852">CourseAssignments 和 OfficeAssignment 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-852">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="317de-853">`CourseAssignments` 和 `OfficeAssignment` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-853">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="317de-854">由於講師可以教授任何數量的課程，因此 `CourseAssignments` 已定義為一個集合。</span><span class="sxs-lookup"><span data-stu-id="317de-854">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="317de-855">若導覽屬性中保留了多個實體：</span><span class="sxs-lookup"><span data-stu-id="317de-855">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="317de-856">它必須是一種清單類型，可對其中的實體進行新增、刪除和更新。</span><span class="sxs-lookup"><span data-stu-id="317de-856">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="317de-857">導覽屬性類型包括：</span><span class="sxs-lookup"><span data-stu-id="317de-857">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="317de-858">若已指定 `ICollection<T>`，EF Core 會根據預設建立一個 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="317de-858">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="317de-859">`CourseAssignment` 實體會在本節的多對多關聯性中解釋。</span><span class="sxs-lookup"><span data-stu-id="317de-859">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="317de-860">Contoso 大學商務規則訂定了講師最多只能有一間辦公室。</span><span class="sxs-lookup"><span data-stu-id="317de-860">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="317de-861">`OfficeAssignment` 屬性保留了一個單一 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-861">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="317de-862">若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-862">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="317de-863">建立 OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="317de-863">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="317de-865">使用下列程式碼建立 *Models/OfficeAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-865">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="317de-866">Key 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-866">The Key attribute</span></span>

<span data-ttu-id="317de-867">`[Key]` 屬性用於將某個屬性名稱不是 classnameID 或 ID 的屬性識別為主索引鍵 (PK)。</span><span class="sxs-lookup"><span data-stu-id="317de-867">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="317de-868">在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-868">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="317de-869">辦公室指派只存在於與其受指派的講師關聯中。</span><span class="sxs-lookup"><span data-stu-id="317de-869">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="317de-870">`OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。</span><span class="sxs-lookup"><span data-stu-id="317de-870">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="317de-871">EF Core 無法自動將 `InstructorID` 識別為 `OfficeAssignment` 的主索引鍵，因為：</span><span class="sxs-lookup"><span data-stu-id="317de-871">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="317de-872">`InstructorID` 並未遵循 ID 或 classnameID 的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="317de-872">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="317de-873">因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：</span><span class="sxs-lookup"><span data-stu-id="317de-873">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="317de-874">根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-874">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="317de-875">Instructor 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-875">The Instructor navigation property</span></span>

<span data-ttu-id="317de-876">`Instructor` 實體的 `OfficeAssignment` 導覽屬性可為 Null，因為：</span><span class="sxs-lookup"><span data-stu-id="317de-876">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="317de-877">參考型別 (例如類別可為 Null)。</span><span class="sxs-lookup"><span data-stu-id="317de-877">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="317de-878">講師可能沒有辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="317de-878">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="317de-879">`OfficeAssignment` 實體有不可為 Null 的`Instructor` 導覽屬性，因為：</span><span class="sxs-lookup"><span data-stu-id="317de-879">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="317de-880">`InstructorID` 不可為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-880">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="317de-881">辦公室指派無法獨立於講師之外存在。</span><span class="sxs-lookup"><span data-stu-id="317de-881">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="317de-882">當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。</span><span class="sxs-lookup"><span data-stu-id="317de-882">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="317de-883">`[Required]` 屬性可套用到 `Instructor` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-883">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="317de-884">上述程式碼指定了必須要有相關的講師。</span><span class="sxs-lookup"><span data-stu-id="317de-884">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="317de-885">上述程式碼是不必要的，因為 `InstructorID` 外部索引鍵 (同時也是 PK) 不可為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-885">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="317de-886">修改 Course 實體</span><span class="sxs-lookup"><span data-stu-id="317de-886">Modify the Course Entity</span></span>

![Course 實體](complex-data-model/_static/course-entity.png)

<span data-ttu-id="317de-888">使用下列程式碼更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-888">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="317de-889">`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="317de-889">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="317de-890">`DepartmentID` 會指向相關的 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-890">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="317de-891">`Course` 實體具有一個 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-891">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="317de-892">當資料模型針對相關實體有一個導覽屬性時，EF Core 不需要針對該模型具備 FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-892">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="317de-893">EF Core 會自動在資料庫中需要的任何地方建立 FK。</span><span class="sxs-lookup"><span data-stu-id="317de-893">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="317de-894">EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="317de-894">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="317de-895">在資料模型中具備 FK 可讓更新變得更為簡單和有效率。</span><span class="sxs-lookup"><span data-stu-id="317de-895">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="317de-896">例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-896">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="317de-897">當擷取課程實體以進行編輯時：</span><span class="sxs-lookup"><span data-stu-id="317de-897">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="317de-898">若沒有明確載入，`Department` 實體將為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-898">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="317de-899">若要更新課程實體，必須先擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-899">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="317de-900">當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="317de-900">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="317de-901">DatabaseGenerated 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-901">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="317de-902">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="317de-902">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="317de-903">根據預設，EF Core 會假設 PK 值都是由資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="317de-903">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="317de-904">由資料庫產生 PK 值通常都是最佳做法。</span><span class="sxs-lookup"><span data-stu-id="317de-904">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="317de-905">針對 `Course` 實體，使用者指定了 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-905">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="317de-906">例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。</span><span class="sxs-lookup"><span data-stu-id="317de-906">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="317de-907">`DatabaseGenerated` 屬性也可用於產生預設值。</span><span class="sxs-lookup"><span data-stu-id="317de-907">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="317de-908">例如，資料庫會自動產生日期欄位來記錄資料列建立或更新的日期。</span><span class="sxs-lookup"><span data-stu-id="317de-908">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="317de-909">如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="317de-909">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-910">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-910">Foreign key and navigation properties</span></span>

<span data-ttu-id="317de-911">`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-911">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="317de-912">由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="317de-912">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="317de-913">由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="317de-913">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="317de-914">課程可由多個講師進行教授，因此 `CourseAssignments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="317de-914">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="317de-915">`CourseAssignment` 會在[稍後](#many-to-many-relationships)進行解釋。</span><span class="sxs-lookup"><span data-stu-id="317de-915">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="317de-916">建立 Department 實體</span><span class="sxs-lookup"><span data-stu-id="317de-916">Create the Department entity</span></span>

![部門實體](complex-data-model/_static/department-entity.png)

<span data-ttu-id="317de-918">使用下列程式碼建立 *Models/Department.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-918">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="317de-919">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="317de-919">The Column attribute</span></span>

<span data-ttu-id="317de-920">先前，`Column` 屬性主要用於變更資料行的名稱對應。</span><span class="sxs-lookup"><span data-stu-id="317de-920">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="317de-921">在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。</span><span class="sxs-lookup"><span data-stu-id="317de-921">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="317de-922">`Budget` 資料行則是使用資料庫中的 SQL Server 金額類型定義：</span><span class="sxs-lookup"><span data-stu-id="317de-922">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="317de-923">通常您不需要資料行對應。</span><span class="sxs-lookup"><span data-stu-id="317de-923">Column mapping is generally not required.</span></span> <span data-ttu-id="317de-924">EF Core 通常會根據屬性的 CLR 類型選擇適當的 SQL Server 資料類型。</span><span class="sxs-lookup"><span data-stu-id="317de-924">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="317de-925">CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。</span><span class="sxs-lookup"><span data-stu-id="317de-925">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="317de-926">由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。</span><span class="sxs-lookup"><span data-stu-id="317de-926">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-927">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-927">Foreign key and navigation properties</span></span>

<span data-ttu-id="317de-928">FK 及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-928">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="317de-929">部門可能有或可能沒有系統管理員。</span><span class="sxs-lookup"><span data-stu-id="317de-929">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="317de-930">系統管理員一律為講師。</span><span class="sxs-lookup"><span data-stu-id="317de-930">An administrator is always an instructor.</span></span> <span data-ttu-id="317de-931">因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。</span><span class="sxs-lookup"><span data-stu-id="317de-931">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="317de-932">導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：</span><span class="sxs-lookup"><span data-stu-id="317de-932">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="317de-933">上述程式碼中的問號 (?) 表示屬性可為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-933">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="317de-934">部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-934">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="317de-935">注意：根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="317de-935">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="317de-936">串聯刪除可能會導致循環的串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="317de-936">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="317de-937">循環串聯刪除規則會在新增移轉時造成例外狀況。</span><span class="sxs-lookup"><span data-stu-id="317de-937">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="317de-938">例如，若已將 `Department.InstructorID` 屬性定義為不可為 Null：</span><span class="sxs-lookup"><span data-stu-id="317de-938">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="317de-939">EF Core 會設定串聯刪除規則，以便在刪除講師時刪除部門。</span><span class="sxs-lookup"><span data-stu-id="317de-939">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="317de-940">在刪除講師時刪除部門並非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="317de-940">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="317de-941">下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，而不是 cascade。</span><span class="sxs-lookup"><span data-stu-id="317de-941">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="317de-942">上述的程式碼會在部門-講師關聯性上停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="317de-942">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="317de-943">更新 Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="317de-943">Update the Enrollment entity</span></span>

<span data-ttu-id="317de-944">註冊記錄是某位學生參加的一門課程。</span><span class="sxs-lookup"><span data-stu-id="317de-944">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="317de-946">使用下列程式碼更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-946">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="317de-947">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="317de-947">Foreign key and navigation properties</span></span>

<span data-ttu-id="317de-948">FK 屬性及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-948">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="317de-949">註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-949">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="317de-950">註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="317de-950">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="317de-951">Many-to-Many Relationships</span><span class="sxs-lookup"><span data-stu-id="317de-951">Many-to-Many Relationships</span></span>

<span data-ttu-id="317de-952">在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-952">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="317de-953">`Enrollment` 實體的功能為資料庫中一個「具有承載」的多對多聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-953">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="317de-954">「具有承載」表示 `Enrollment` 資料表除了聯結資料表 (在此案例中為 PK 和 `Grade`) 的 FK 之外，還包含了額外的資料。</span><span class="sxs-lookup"><span data-stu-id="317de-954">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="317de-955">下列圖例展示了在實體圖表中這些關聯性的樣子。</span><span class="sxs-lookup"><span data-stu-id="317de-955">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="317de-956">(此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。</span><span class="sxs-lookup"><span data-stu-id="317de-956">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="317de-957">建立圖表並不是此教學課程的一部分)。</span><span class="sxs-lookup"><span data-stu-id="317de-957">Creating the diagram isn't part of the tutorial.)</span></span>

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

<span data-ttu-id="317de-959">每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (\*)」，顯示其為一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-959">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="317de-960">若 `Enrollment` 資料表並未包含成績資訊，則其便只需要包含兩個 FK (`CourseID` 和 `StudentID`)。</span><span class="sxs-lookup"><span data-stu-id="317de-960">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="317de-961">沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。</span><span class="sxs-lookup"><span data-stu-id="317de-961">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="317de-962">`Instructor` 和 `Course` 實體具有使用了純聯結資料表的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="317de-962">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="317de-963">注意：EF 6.x 支援多對多關聯性的隱含聯結資料表，但 EF Core 並不支援。</span><span class="sxs-lookup"><span data-stu-id="317de-963">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="317de-964">如需詳細資訊，請參閱 [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (EF Core 2.0 中的多對多關聯性)。</span><span class="sxs-lookup"><span data-stu-id="317de-964">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="317de-965">CourseAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="317de-965">The CourseAssignment entity</span></span>

![CourseAssignment 實體](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="317de-967">使用下列程式碼建立 *Models/CourseAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-967">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="317de-968">講師-課程</span><span class="sxs-lookup"><span data-stu-id="317de-968">Instructor-to-Courses</span></span>

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="317de-970">講師-課程多對多關聯性：</span><span class="sxs-lookup"><span data-stu-id="317de-970">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="317de-971">需要一個由實體集代表的聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-971">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="317de-972">為一個純聯結資料表 (沒有承載的資料表)。</span><span class="sxs-lookup"><span data-stu-id="317de-972">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="317de-973">通常會將聯結實體命名為 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="317de-973">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="317de-974">例如，使用此模式的講師-課程聯結資料表為 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="317de-974">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="317de-975">不過，我們建議使用可描述關聯性的名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-975">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="317de-976">資料模型一開始都是簡單的，之後便會持續成長。</span><span class="sxs-lookup"><span data-stu-id="317de-976">Data models start out simple and grow.</span></span> <span data-ttu-id="317de-977">無承載聯結 (PJT) 常常會演變為包含承載。</span><span class="sxs-lookup"><span data-stu-id="317de-977">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="317de-978">藉由在一開始便使用描述性的實體名稱，當聯結資料表變更時便不需要變更名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-978">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="317de-979">理想情況下，聯結實體在公司網域中會有自己的自然 (可能為一個單字) 名稱。</span><span class="sxs-lookup"><span data-stu-id="317de-979">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="317de-980">例如，「書籍」和「客戶」可連結為一個名為「評分」 的聯結實體。</span><span class="sxs-lookup"><span data-stu-id="317de-980">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="317de-981">針對講師-課程多對多關聯性，`CourseAssignment` 會比 `CourseInstructor` 來得好。</span><span class="sxs-lookup"><span data-stu-id="317de-981">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="317de-982">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="317de-982">Composite key</span></span>

<span data-ttu-id="317de-983">FK 不可為 Null。</span><span class="sxs-lookup"><span data-stu-id="317de-983">FKs are not nullable.</span></span> <span data-ttu-id="317de-984">`CourseAssignment` (`InstructorID` 及 `CourseID`) 中的兩個 FK 一起搭配使用，便可唯一的識別 `CourseAssignment` 資料表中的每一個資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-984">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="317de-985">`CourseAssignment` 並不需要其專屬的 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-985">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="317de-986">`InstructorID` 及 `CourseID` 屬性的功能便是複合 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-986">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="317de-987">為 EF Core 指定複合 PK 的唯一方法是使用 *Fluent API*。</span><span class="sxs-lookup"><span data-stu-id="317de-987">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="317de-988">下節會說明如何設定複合 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-988">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="317de-989">複合 PK 可確保：</span><span class="sxs-lookup"><span data-stu-id="317de-989">The composite key ensures:</span></span>

* <span data-ttu-id="317de-990">一個課程可以有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-990">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="317de-991">一個講師可以有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-991">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="317de-992">相同講師和課程不可有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="317de-992">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="317de-993">由於 `Enrollment` 聯結實體定義了其自身的 PK，因此這種種類的重複項目是可能的。</span><span class="sxs-lookup"><span data-stu-id="317de-993">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="317de-994">若要防止這類重複項目：</span><span class="sxs-lookup"><span data-stu-id="317de-994">To prevent such duplicates:</span></span>

* <span data-ttu-id="317de-995">在 FK 欄位中新增一個唯一的索引，或</span><span class="sxs-lookup"><span data-stu-id="317de-995">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="317de-996">為 `Enrollment` 設定一個主複合索引鍵，與 `CourseAssignment` 相似。</span><span class="sxs-lookup"><span data-stu-id="317de-996">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="317de-997">如需詳細資訊，請參閱[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="317de-997">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="317de-998">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="317de-998">Update the DB context</span></span>

<span data-ttu-id="317de-999">將下列醒目提示的程式碼新增至 *Data/SchoolContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="317de-999">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="317de-1000">上述程式碼會新增一個新實體，並設定 `CourseAssignment` 實體的複合 PK。</span><span class="sxs-lookup"><span data-stu-id="317de-1000">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="317de-1001">屬性的 Fluent API 替代項目</span><span class="sxs-lookup"><span data-stu-id="317de-1001">Fluent API alternative to attributes</span></span>

<span data-ttu-id="317de-1002">上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。</span><span class="sxs-lookup"><span data-stu-id="317de-1002">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="317de-1003">此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。</span><span class="sxs-lookup"><span data-stu-id="317de-1003">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="317de-1004">[下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="317de-1004">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="317de-1005">在此教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。</span><span class="sxs-lookup"><span data-stu-id="317de-1005">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="317de-1006">然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。</span><span class="sxs-lookup"><span data-stu-id="317de-1006">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="317de-1007">某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。</span><span class="sxs-lookup"><span data-stu-id="317de-1007">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="317de-1008">`MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。</span><span class="sxs-lookup"><span data-stu-id="317de-1008">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="317de-1009">某些開發人員偏好單獨使用 Fluent API，使其實體類別保持「整潔」。</span><span class="sxs-lookup"><span data-stu-id="317de-1009">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="317de-1010">屬性和 Fluent API 可混合使用。</span><span class="sxs-lookup"><span data-stu-id="317de-1010">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="317de-1011">有一些設定只能透過 Fluent API 完成 (指定複合 PK)。</span><span class="sxs-lookup"><span data-stu-id="317de-1011">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="317de-1012">有一些設定只能透過屬性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="317de-1012">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="317de-1013">使用 Fluent API 或屬性的建議做法為：</span><span class="sxs-lookup"><span data-stu-id="317de-1013">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="317de-1014">從這兩種方法中選擇一項。</span><span class="sxs-lookup"><span data-stu-id="317de-1014">Choose one of these two approaches.</span></span>
* <span data-ttu-id="317de-1015">持續且盡量使用您選擇的方法。</span><span class="sxs-lookup"><span data-stu-id="317de-1015">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="317de-1016">此教學課程中使用到的某些屬性主要用於：</span><span class="sxs-lookup"><span data-stu-id="317de-1016">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="317de-1017">僅驗證 (例如，`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="317de-1017">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="317de-1018">僅 EF Core 組態 (例如，`HasKey`)。</span><span class="sxs-lookup"><span data-stu-id="317de-1018">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="317de-1019">驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。</span><span class="sxs-lookup"><span data-stu-id="317de-1019">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="317de-1020">如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="317de-1020">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="317de-1021">顯示關聯性的實體圖表</span><span class="sxs-lookup"><span data-stu-id="317de-1021">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="317de-1022">下圖顯示了 EF Power Tools 為完成的 School 模型建立的圖表。</span><span class="sxs-lookup"><span data-stu-id="317de-1022">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

<span data-ttu-id="317de-1024">上圖顯示︰</span><span class="sxs-lookup"><span data-stu-id="317de-1024">The preceding diagram shows:</span></span>

* <span data-ttu-id="317de-1025">數個一對多關聯性線條 (1 對 \*)。</span><span class="sxs-lookup"><span data-stu-id="317de-1025">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="317de-1026">`Instructor` 和 `OfficeAssignment` 實體之間的一對零或一關聯性線條 (1 對 0..1)。</span><span class="sxs-lookup"><span data-stu-id="317de-1026">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="317de-1027">`Instructor` 和 `Department` 實體之間的零或一對多關聯性線條 (0..1 對 \*)。</span><span class="sxs-lookup"><span data-stu-id="317de-1027">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="317de-1028">使用測試資料植入資料庫</span><span class="sxs-lookup"><span data-stu-id="317de-1028">Seed the DB with Test Data</span></span>

<span data-ttu-id="317de-1029">更新 *Data/DbInitializer.cs* 中的程式碼：</span><span class="sxs-lookup"><span data-stu-id="317de-1029">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="317de-1030">上述程式碼為新的實體提供了種子資料。</span><span class="sxs-lookup"><span data-stu-id="317de-1030">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="317de-1031">此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。</span><span class="sxs-lookup"><span data-stu-id="317de-1031">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="317de-1032">範例資料主要用於測試。</span><span class="sxs-lookup"><span data-stu-id="317de-1032">The sample data is used for testing.</span></span> <span data-ttu-id="317de-1033">如需如何植入多對多聯結資料表的範例，請參閱 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="317de-1033">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="317de-1034">新增移轉</span><span class="sxs-lookup"><span data-stu-id="317de-1034">Add a migration</span></span>

<span data-ttu-id="317de-1035">建置專案。</span><span class="sxs-lookup"><span data-stu-id="317de-1035">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-1036">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-1036">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-1037">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-1037">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="317de-1038">上述命令會顯示關於可能發生資料遺失的警告。</span><span class="sxs-lookup"><span data-stu-id="317de-1038">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="317de-1039">若執行 `database update` 命令，便會產生下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="317de-1039">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="317de-1040">套用移轉</span><span class="sxs-lookup"><span data-stu-id="317de-1040">Apply the migration</span></span>

<span data-ttu-id="317de-1041">現在您有了現有的資料庫，您需要思考如何對其套用未來變更。</span><span class="sxs-lookup"><span data-stu-id="317de-1041">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="317de-1042">本教學課程示範兩種方法：</span><span class="sxs-lookup"><span data-stu-id="317de-1042">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="317de-1043">捨棄並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="317de-1043">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="317de-1044">[將遷移套用至現有的資料庫](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="317de-1044">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="317de-1045">雖然這個方法更複雜且耗時，卻是實際生產環境的慣用方法。</span><span class="sxs-lookup"><span data-stu-id="317de-1045">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="317de-1046">**請注意**：這是本教學課程的選擇性章節。</span><span class="sxs-lookup"><span data-stu-id="317de-1046">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="317de-1047">您可以執行卸除並重新建立步驟，然後略過本節。</span><span class="sxs-lookup"><span data-stu-id="317de-1047">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="317de-1048">如果您希望遵循本章節中的步驟，請不要執行卸除並重新建立的步驟。</span><span class="sxs-lookup"><span data-stu-id="317de-1048">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="317de-1049">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="317de-1049">Drop and re-create the database</span></span>

<span data-ttu-id="317de-1050">更新的 `DbInitializer` 中的程式碼會為新的實體新增種子資料。</span><span class="sxs-lookup"><span data-stu-id="317de-1050">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="317de-1051">若要強制 EF Core 建立新的資料庫，請卸除並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-1051">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="317de-1052">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="317de-1052">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="317de-1053">在 [套件管理員主控台] (PMC) 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-1053">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="317de-1054">從 PMC 執行 `Get-Help about_EntityFrameworkCore` 以取得說明資訊。</span><span class="sxs-lookup"><span data-stu-id="317de-1054">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="317de-1055">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="317de-1055">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="317de-1056">開啟命令視窗並巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="317de-1056">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="317de-1057">專案資料夾中包含 *Startup.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="317de-1057">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="317de-1058">在命令視窗中輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="317de-1058">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="317de-1059">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="317de-1059">Run the app.</span></span> <span data-ttu-id="317de-1060">執行應用程式會執行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="317de-1060">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="317de-1061">`DbInitializer.Initialize` 會填入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="317de-1061">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="317de-1062">在 SSOX 中開啟資料庫：</span><span class="sxs-lookup"><span data-stu-id="317de-1062">Open the DB in SSOX:</span></span>

* <span data-ttu-id="317de-1063">若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="317de-1063">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="317de-1064">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="317de-1064">Expand the **Tables** node.</span></span> <span data-ttu-id="317de-1065">建立的資料表便會顯示。</span><span class="sxs-lookup"><span data-stu-id="317de-1065">The created tables are displayed.</span></span>

![SSOX 中的資料表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="317de-1067">檢查 **CourseAssignment** 資料表：</span><span class="sxs-lookup"><span data-stu-id="317de-1067">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="317de-1068">以滑鼠右鍵按一下 **CourseAssignment** 資料表，然後選取 [檢視資料]。</span><span class="sxs-lookup"><span data-stu-id="317de-1068">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="317de-1069">驗證 **CourseAssignment** 資料表中是否包含資料。</span><span class="sxs-lookup"><span data-stu-id="317de-1069">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX 中的 CourseAssignment 資料](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="317de-1071">將移轉套用至現有資料庫</span><span class="sxs-lookup"><span data-stu-id="317de-1071">Apply the migration to the existing database</span></span>

<span data-ttu-id="317de-1072">此為選擇性區段。</span><span class="sxs-lookup"><span data-stu-id="317de-1072">This section is optional.</span></span> <span data-ttu-id="317de-1073">只有當您略過先前[卸除並重新建立資料庫](#drop)一節，這些步驟才有效。</span><span class="sxs-lookup"><span data-stu-id="317de-1073">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="317de-1074">當使用現有的資料執行移轉作業時，某些 FK 條件約束可能會無法透過現有資料滿足。</span><span class="sxs-lookup"><span data-stu-id="317de-1074">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="317de-1075">當您使用的是生產資料時，您必須進行幾個步驟才能移轉現有資料。</span><span class="sxs-lookup"><span data-stu-id="317de-1075">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="317de-1076">本節提供了修正 FK 條件約束違規的範例。</span><span class="sxs-lookup"><span data-stu-id="317de-1076">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="317de-1077">請不要在沒有備份的情況下進行這些程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="317de-1077">Don't make these code changes without a backup.</span></span> <span data-ttu-id="317de-1078">若您已完成了先前的章節並已更新資料庫，請不要進行這些程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="317de-1078">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="317de-1079">*{timestamp}_ComplexDataModel.cs* 檔案包含了下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="317de-1079">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="317de-1080">上述程式碼將一個不可為 Null 的 `DepartmentID` FK 新增至 `Course` 資料表。</span><span class="sxs-lookup"><span data-stu-id="317de-1080">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="317de-1081">先前教學課程中的資料庫在 `Course` 中包含了資料列，導致資料表無法藉由移轉進行更新。</span><span class="sxs-lookup"><span data-stu-id="317de-1081">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="317de-1082">若要讓 `ComplexDataModel` 與現有資料進行移轉：</span><span class="sxs-lookup"><span data-stu-id="317de-1082">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="317de-1083">變更程式碼，以給予新資料行 (`DepartmentID`) 一個新的預設值。</span><span class="sxs-lookup"><span data-stu-id="317de-1083">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="317de-1084">建立一個名為 "Temp" 的假部門以作為預設部門之用。</span><span class="sxs-lookup"><span data-stu-id="317de-1084">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="317de-1085">修正外部索引鍵條件約束</span><span class="sxs-lookup"><span data-stu-id="317de-1085">Fix the foreign key constraints</span></span>

<span data-ttu-id="317de-1086">更新 `ComplexDataModel` 類別 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="317de-1086">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="317de-1087">開啟 *{timestamp}_ComplexDataModel.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="317de-1087">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="317de-1088">將新增 `DepartmentID` 資料行至 `Course` 資料表的程式碼全部標為註解。</span><span class="sxs-lookup"><span data-stu-id="317de-1088">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="317de-1089">新增下列醒目提示程式碼。</span><span class="sxs-lookup"><span data-stu-id="317de-1089">Add the following highlighted code.</span></span> <span data-ttu-id="317de-1090">新的程式碼位於 `.CreateTable( name: "Department"` 區塊後方：</span><span class="sxs-lookup"><span data-stu-id="317de-1090">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="317de-1091">透過上述變更，現有的 `Course` 資料列將會在執行 `ComplexDataModel` `Up` 方法後與 "Temp" 部門相關。</span><span class="sxs-lookup"><span data-stu-id="317de-1091">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="317de-1092">生產環境的應用程式會：</span><span class="sxs-lookup"><span data-stu-id="317de-1092">A production app would:</span></span>

* <span data-ttu-id="317de-1093">包含將 `Department` 資料列及相關 `Course` 資料列新增到新 `Department` 資料列的程式碼或指令碼。</span><span class="sxs-lookup"><span data-stu-id="317de-1093">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="317de-1094">不使用 "Temp" 部門或 `Course.DepartmentID` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="317de-1094">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="317de-1095">下一個教學課程會涵蓋相關資料。</span><span class="sxs-lookup"><span data-stu-id="317de-1095">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="317de-1096">其他資源</span><span class="sxs-lookup"><span data-stu-id="317de-1096">Additional resources</span></span>

* [<span data-ttu-id="317de-1097">這個教學課程的 YouTube 版本 (第 1 部分)</span><span class="sxs-lookup"><span data-stu-id="317de-1097">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="317de-1098">這個教學課程的 YouTube 版本 (第 2 部分)</span><span class="sxs-lookup"><span data-stu-id="317de-1098">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="317de-1099">[上一個](xref:data/ef-rp/migrations) 
> [下一步](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="317de-1099">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end