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
ms.openlocfilehash: 23ac7fa83d6ee313e6695e3829a50ae94c967572
ms.sourcegitcommit: 4864500b0f43e33dc0445ce632bfbe1e43e79320
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105994152"
---
<!-- Removed from V 5.0, CourseAssignment -->

# <a name="part-5-razor-pages-with-ef-core-in-aspnet-core---data-model"></a><span data-ttu-id="ced34-103">第5部分： Razor ASP.NET Core 資料模型中有 EF Core 的頁面</span><span class="sxs-lookup"><span data-stu-id="ced34-103">Part 5, Razor Pages with EF Core in ASP.NET Core - Data Model</span></span>

<span data-ttu-id="ced34-104">由 [Tom Dykstra](https://github.com/tdykstra)、 [Jeremy Likness](https://twitter.com/jeremylikness)和 [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="ced34-104">By [Tom Dykstra](https://github.com/tdykstra), [Jeremy Likness](https://twitter.com/jeremylikness), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ced34-105">先前的教學課程建立了基本的資料模型，該模型由三個實體組成。</span><span class="sxs-lookup"><span data-stu-id="ced34-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="ced34-106">在本教學課程中：</span><span class="sxs-lookup"><span data-stu-id="ced34-106">In this tutorial:</span></span>

* <span data-ttu-id="ced34-107">新增更多實體和關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="ced34-108">藉由指定格式、驗證和資料庫對應規則來自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="ced34-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="ced34-109">已完成的資料模型如下圖所示：</span><span class="sxs-lookup"><span data-stu-id="ced34-109">The completed data model is shown in the following illustration:</span></span>

![實體圖表](complex-data-model/_static/EF.png)

<span data-ttu-id="ced34-111">以下是使用 [Dataedo](https://dataedo.com/)建立的資料庫關係圖：</span><span class="sxs-lookup"><span data-stu-id="ced34-111">The following database diagram was made with [Dataedo](https://dataedo.com/):</span></span>

<!--Image added in https://github.com/dotnet/AspNetCore.Docs/pull/21922  -->

![Dataedo 圖](intro/_static/5/AzureSQL_DB_ERD_madeIn_Dataedo.png)

<span data-ttu-id="ced34-113">若要使用 Dataedo 建立資料庫關係圖：</span><span class="sxs-lookup"><span data-stu-id="ced34-113">To create a database diagram with Dataedo:</span></span>

* [<span data-ttu-id="ced34-114">將應用程式部署至 Azure</span><span class="sxs-lookup"><span data-stu-id="ced34-114">Deploy the app to Azure</span></span>](/azure/app-service/tutorial-dotnetcore-sqldb-app)
* <span data-ttu-id="ced34-115">在您的電腦上下載並安裝 [Dataedo](https://dataedo.com/) 。</span><span class="sxs-lookup"><span data-stu-id="ced34-115">Download and install [Dataedo](https://dataedo.com/) on your computer.</span></span>
* <span data-ttu-id="ced34-116">[在5分鐘內依照指示產生 Azure SQL Database 的檔](https://dataedo.com/tutorials/generate-documentation-for-azure-sql-database)</span><span class="sxs-lookup"><span data-stu-id="ced34-116">Follow the instructions [Generate documentation for Azure SQL Database in 5 minutes ](https://dataedo.com/tutorials/generate-documentation-for-azure-sql-database)</span></span>

<span data-ttu-id="ced34-117">在上述的 Dataedo 圖中， `CourseInstructor` 是 Entity Framework 所建立的聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-117">In the preceding Dataedo diagram, the `CourseInstructor` is a join table created by Entity Framework.</span></span> <span data-ttu-id="ced34-118">如需詳細資訊，請參閱 [多對多](/ef/core/modeling/relationships#many-to-many)</span><span class="sxs-lookup"><span data-stu-id="ced34-118">For more information, see [Many-to-many](/ef/core/modeling/relationships#many-to-many)</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="ced34-119">Student 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-119">The Student entity</span></span>

<span data-ttu-id="ced34-120">將 *Models/Student.cs* 中的程式碼替換成下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="ced34-120">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="ced34-121">上述程式碼會新增 `FullName` 屬性，並將下列屬性新增到現有屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-121">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)
* [`[DisplayFormat]`](xref:System.ComponentModel.DataAnnotations.DisplayFormatAttribute)
* [`[StringLength]`](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)
* [`[Column]`](xref:System.ComponentModel.DataAnnotations.Schema.ColumnAttribute)
* [`[Required]`](xref:System.ComponentModel.DataAnnotations.RequiredAttribute)
* [`[Display]`](xref:System.ComponentModel.DataAnnotations.DisplayAttribute)

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="ced34-122">FullName 計算屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-122">The FullName calculated property</span></span>

<span data-ttu-id="ced34-123">`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。</span><span class="sxs-lookup"><span data-stu-id="ced34-123">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="ced34-124">您無法設定 `FullName`，因此它只會有 get 存取子。</span><span class="sxs-lookup"><span data-stu-id="ced34-124">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="ced34-125">資料庫中不會建立 `FullName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="ced34-125">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="ced34-126">DataType 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-126">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="ced34-127">針對學生註冊日期，所有頁面目前都會同時顯示日期和一天當中的時間，雖然只要日期才是重要項目。</span><span class="sxs-lookup"><span data-stu-id="ced34-127">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="ced34-128">透過使用資料註解屬性，您便可以只透過單一程式碼變更來修正每個顯示資料頁面中的顯示格式。</span><span class="sxs-lookup"><span data-stu-id="ced34-128">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="ced34-129">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。</span><span class="sxs-lookup"><span data-stu-id="ced34-129">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="ced34-130">在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="ced34-130">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="ced34-131">[DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。</span><span class="sxs-lookup"><span data-stu-id="ced34-131">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="ced34-132">例如：</span><span class="sxs-lookup"><span data-stu-id="ced34-132">For example:</span></span>

* <span data-ttu-id="ced34-133">`DataType.EmailAddress` 會自動建立 `mailto:` 連結。</span><span class="sxs-lookup"><span data-stu-id="ced34-133">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="ced34-134">`DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。</span><span class="sxs-lookup"><span data-stu-id="ced34-134">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="ced34-135">`DataType` 屬性會發出 HTML5 `data-` (發音為 data dash) 屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-135">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="ced34-136">`DataType` 屬性不會提供驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-136">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="ced34-137">DisplayFormat 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-137">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="ced34-138">`DataType.Date` 未指定顯示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="ced34-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="ced34-139">根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-139">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="ced34-140">`DisplayFormat` 屬性會用來明確指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="ced34-140">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="ced34-141">`ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。</span><span class="sxs-lookup"><span data-stu-id="ced34-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="ced34-142">某些欄位不應該使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="ced34-142">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="ced34-143">例如，貨幣符號通常不應顯示在編輯文字方塊中。</span><span class="sxs-lookup"><span data-stu-id="ced34-143">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="ced34-144">`DisplayFormat` 屬性可由自身使用。</span><span class="sxs-lookup"><span data-stu-id="ced34-144">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="ced34-145">通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。</span><span class="sxs-lookup"><span data-stu-id="ced34-145">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="ced34-146">`DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。</span><span class="sxs-lookup"><span data-stu-id="ced34-146">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="ced34-147">`DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：</span><span class="sxs-lookup"><span data-stu-id="ced34-147">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="ced34-148">瀏覽器可以啟用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="ced34-148">The browser can enable HTML5 features.</span></span> <span data-ttu-id="ced34-149">例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結和用戶端輸入驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-149">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="ced34-150">根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-150">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="ced34-151">如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="ced34-151">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="ced34-152">StringLength 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-152">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="ced34-153">您可使用屬性指定資料驗證規則和驗證錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="ced34-153">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="ced34-154">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。</span><span class="sxs-lookup"><span data-stu-id="ced34-154">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="ced34-155">此處所顯示的程式碼會限制名稱不得超過 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-155">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="ced34-156">設定字串長度下限的範例會在[稍後](#the-required-attribute)顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-156">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="ced34-157">`StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-157">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="ced34-158">最小值對資料庫結構描述不會造成任何影響。</span><span class="sxs-lookup"><span data-stu-id="ced34-158">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="ced34-159">`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-159">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="ced34-160">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用來將限制套用到輸入。</span><span class="sxs-lookup"><span data-stu-id="ced34-160">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="ced34-161">例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：</span><span class="sxs-lookup"><span data-stu-id="ced34-161">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ced34-163">在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。</span><span class="sxs-lookup"><span data-stu-id="ced34-163">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="ced34-165">上述影像顯示了 `Student` 資料表的結構描述。</span><span class="sxs-lookup"><span data-stu-id="ced34-165">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="ced34-166">名稱欄位的型別為 `nvarchar(MAX)`。</span><span class="sxs-lookup"><span data-stu-id="ced34-166">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="ced34-167">在本教學課程稍後建立移轉並套用時，名稱欄位會因為字串長度屬性而變更為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="ced34-167">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ced34-169">使用 SQLite 工具，檢查資料表的資料行定義 `Student` 。</span><span class="sxs-lookup"><span data-stu-id="ced34-169">Using a SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="ced34-170">名稱欄位的型別為 `Text`。</span><span class="sxs-lookup"><span data-stu-id="ced34-170">The name fields have type `Text`.</span></span> <span data-ttu-id="ced34-171">請注意，第一個名稱欄位稱為 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="ced34-171">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="ced34-172">在下一節中， `FirstMidName` 會變更為 `FirstName` 。</span><span class="sxs-lookup"><span data-stu-id="ced34-172">In the next section, `FirstMidName` is changed to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="ced34-173">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-173">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="ced34-174">可控制類別和屬性如何對應到資料庫的屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-174">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="ced34-175">在 `Student` 模型中，`Column` 屬性會用來將 `FirstMidName` 屬性名稱對應到資料庫中的 "FirstName"。</span><span class="sxs-lookup"><span data-stu-id="ced34-175">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="ced34-176">建立資料庫時，模型上的屬性名稱會用來作為資料行名稱 (使用 `Column` 屬性時除外)。</span><span class="sxs-lookup"><span data-stu-id="ced34-176">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="ced34-177">`Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。</span><span class="sxs-lookup"><span data-stu-id="ced34-177">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="ced34-178">透過 `[Column]` 屬性，資料模型中 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="ced34-178">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="ced34-179">新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。</span><span class="sxs-lookup"><span data-stu-id="ced34-179">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="ced34-180">支援 `SchoolContext` 的模型不再符合資料庫。</span><span class="sxs-lookup"><span data-stu-id="ced34-180">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="ced34-181">這種不一致會透過在本教學課程中稍後部分新增移轉來解決。</span><span class="sxs-lookup"><span data-stu-id="ced34-181">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="ced34-182">Required 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-182">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="ced34-183">`Required` 屬性會讓名稱屬性成為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="ced34-183">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="ced34-184">針對不可為 Null 型別的實值型別 (例如 `DateTime`、`int` 和 `double`)，`Required` 屬性並非必要項目。</span><span class="sxs-lookup"><span data-stu-id="ced34-184">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="ced34-185">不可為 Null 的類型會自動視為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="ced34-185">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="ced34-186">`Required` 屬性必須搭配 `MinimumLength` 使用，才能強制執行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="ced34-186">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="ced34-187">`MinimumLength` 與 `Required` 允許空白字元以滿足驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-187">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="ced34-188">使用 `RegularExpression` 屬性來完全控制字串。</span><span class="sxs-lookup"><span data-stu-id="ced34-188">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="ced34-189">Display 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-189">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="ced34-190">`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。</span><span class="sxs-lookup"><span data-stu-id="ced34-190">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="ced34-191">預設標題中沒有使用空白分隔文字，例如「姓氏」。</span><span class="sxs-lookup"><span data-stu-id="ced34-191">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="ced34-192">建立移轉</span><span class="sxs-lookup"><span data-stu-id="ced34-192">Create a migration</span></span>

<span data-ttu-id="ced34-193">執行應用程式並移至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="ced34-193">Run the app and go to the Students page.</span></span> <span data-ttu-id="ced34-194">隨即擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ced34-194">An exception is thrown.</span></span> <span data-ttu-id="ced34-195">`[Column]` 屬性會造成 EF 預期尋找名為 `FirstName` 的資料行，但資料庫中的資料行名稱仍是 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="ced34-195">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-196">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ced34-197">錯誤訊息會與下列範例相似：</span><span class="sxs-lookup"><span data-stu-id="ced34-197">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
There are pending model changes
Pending model changes are detected in the following:

SchoolContext
```

* <span data-ttu-id="ced34-198">在 PMC 中，輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-198">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
   
  ```

  <span data-ttu-id="ced34-199">這些命令中的第一個命令會產生下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="ced34-199">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="ced34-200">產生警告的理由是名稱欄位現在限制長度為 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-200">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="ced34-201">若資料庫中的名稱超過 50 個字元，則第 51 到最後一個字元便會遺失。</span><span class="sxs-lookup"><span data-stu-id="ced34-201">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="ced34-202">在 SSOX 中開啟 Student 資料表：</span><span class="sxs-lookup"><span data-stu-id="ced34-202">Open the Student table in SSOX:</span></span>

  ![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="ced34-204">在套用移轉前，名稱資料行的型別為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="ced34-204">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="ced34-205">名稱資料行現在為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="ced34-205">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="ced34-206">資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="ced34-206">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ced34-208">錯誤訊息會與下列範例相似：</span><span class="sxs-lookup"><span data-stu-id="ced34-208">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="ced34-209">在專案資料夾中開啟命令視窗。</span><span class="sxs-lookup"><span data-stu-id="ced34-209">Open a command window in the project folder.</span></span> <span data-ttu-id="ced34-210">輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-210">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="ced34-211">資料庫更新命令會顯示與下列範例相似的錯誤：</span><span class="sxs-lookup"><span data-stu-id="ced34-211">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="ced34-212">針對本教學課程，通過此錯誤的方法是刪除和重新建立初始移轉。</span><span class="sxs-lookup"><span data-stu-id="ced34-212">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="ced34-213">如需詳細資訊，請參閱[移轉教學課程](xref:data/ef-rp/migrations)頂端的 SQLite 警告附註。</span><span class="sxs-lookup"><span data-stu-id="ced34-213">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="ced34-214">刪除 *Migrations* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ced34-214">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="ced34-215">執行下列命令來卸除資料庫、建立新的初始移轉，然後套用移轉：</span><span class="sxs-lookup"><span data-stu-id="ced34-215">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
   
  ```

* <span data-ttu-id="ced34-216">使用 SQLite 工具檢查 Student 資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-216">Examine the Student table with a SQLite tool.</span></span> <span data-ttu-id="ced34-217">目前的資料行 `FirstMidName` `FirstName` 。</span><span class="sxs-lookup"><span data-stu-id="ced34-217">The column that was `FirstMidName` is now `FirstName`.</span></span>

---

* <span data-ttu-id="ced34-218">執行應用程式並移至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="ced34-218">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="ced34-219">請注意時間並未輸入或和日期一同顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-219">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="ced34-220">選取 [新建] 然後嘗試輸入超過 50 個字元的名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-220">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="ced34-221">在下列各節中，在某些階段建置應用程式會產生編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="ced34-221">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="ced34-222">指令會指定何時應建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-222">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="ced34-223">Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-223">The Instructor Entity</span></span>

<!-- no longer using PJT
![Instructor entity](complex-data-model/_static/instructor-entity.png) -->

<span data-ttu-id="ced34-224">使用下列程式碼建立 *Models/Instructor.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-224">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="ced34-225">在同一行上可以有多個屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-225">Multiple attributes can be on one line.</span></span> <span data-ttu-id="ced34-226">`HireDate` 屬性可以下列方式撰寫：</span><span class="sxs-lookup"><span data-stu-id="ced34-226">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="ced34-227">導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-227">Navigation properties</span></span>

<span data-ttu-id="ced34-228">`Courses` 和 `OfficeAssignment` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-228">The `Courses` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="ced34-229">由於講師可以教授任何數量的課程，因此 `Courses` 已定義為一個集合。</span><span class="sxs-lookup"><span data-stu-id="ced34-229">An instructor can teach any number of courses, so `Courses` is defined as a collection.</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="ced34-230">講師最多只能擁有一間辦公室，因此 `OfficeAssignment` 屬性會保存單一 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-230">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="ced34-231">若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-231">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="ced34-232">OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-232">The OfficeAssignment entity</span></span>

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="ced34-234">使用下列程式碼建立 *Models/OfficeAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-234">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="ced34-235">Key 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-235">The Key attribute</span></span>

<span data-ttu-id="ced34-236">屬性 [`[Key]`](xref:System.ComponentModel.DataAnnotations.KeyAttribute) （attribute）會用來將屬性（attribute）識別為 primary key (PK) 當屬性名稱不是 `classnameID` 或時 `ID` 。</span><span class="sxs-lookup"><span data-stu-id="ced34-236">The [`[Key]`](xref:System.ComponentModel.DataAnnotations.KeyAttribute) attribute is used to identify a property as the primary key (PK) when the property name is something other than `classnameID` or `ID`.</span></span>

<span data-ttu-id="ced34-237">在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-237">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="ced34-238">辦公室指派只存在於與其受指派的講師關聯中。</span><span class="sxs-lookup"><span data-stu-id="ced34-238">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="ced34-239">`OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。</span><span class="sxs-lookup"><span data-stu-id="ced34-239">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="ced34-240">當某個資料表中的 PK 同時為 PK 和另一個資料表中的 FK 時，就會發生一到零或一種關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-240">A one-to-zero-or-one relationship occurs when a PK in one table is both a PK and a FK in another table.</span></span>

<span data-ttu-id="ced34-241">EF Core 無法將 `InstructorID` 自動識別為 `OfficeAssignment` 的主索引鍵，因為 `InstructorID` 並未遵循識別碼或 classnameID 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="ced34-241">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="ced34-242">因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：</span><span class="sxs-lookup"><span data-stu-id="ced34-242">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="ced34-243">根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-243">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span> <span data-ttu-id="ced34-244">如需詳細資訊，請參閱 [EF 金鑰](/ef/core/modeling/keys)。</span><span class="sxs-lookup"><span data-stu-id="ced34-244">For more information, see [EF Keys](/ef/core/modeling/keys).</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="ced34-245">Instructor 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-245">The Instructor navigation property</span></span>

<span data-ttu-id="ced34-246">`Instructor.OfficeAssignment` 導覽屬性可以是 Null，因為指定講師可能不會有 `OfficeAssignment` 資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-246">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="ced34-247">講師可能沒有辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="ced34-247">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="ced34-248">`OfficeAssignment.Instructor` 導覽屬性一律會擁有一個講師實體，因為外部索引鍵 `InstructorID` 的型別是 `int`，其為不可為 Null 的實值型別。</span><span class="sxs-lookup"><span data-stu-id="ced34-248">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="ced34-249">辦公室指派無法獨立於講師之外存在。</span><span class="sxs-lookup"><span data-stu-id="ced34-249">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="ced34-250">當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。</span><span class="sxs-lookup"><span data-stu-id="ced34-250">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="ced34-251">Course 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-251">The Course Entity</span></span>

<span data-ttu-id="ced34-252">使用下列程式碼更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-252">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="ced34-253">`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="ced34-253">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="ced34-254">`DepartmentID` 會指向相關的 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-254">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="ced34-255">`Course` 實體具有一個 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-255">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="ced34-256">若模型針對相關實體已有導覽屬性，則 EF Core 針對資料模型便不需要外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-256">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="ced34-257">EF Core 會自動在資料庫中需要的任何地方建立 FK。</span><span class="sxs-lookup"><span data-stu-id="ced34-257">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="ced34-258">EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="ced34-258">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="ced34-259">但是，在資料模型中明確包含 FK (外部索引鍵) 可讓更新更簡單且更有效率。</span><span class="sxs-lookup"><span data-stu-id="ced34-259">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="ced34-260">例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-260">For example, consider a model where the FK property `DepartmentID` is ***not*** included.</span></span> <span data-ttu-id="ced34-261">當擷取課程實體以進行編輯時：</span><span class="sxs-lookup"><span data-stu-id="ced34-261">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="ced34-262">`Department` `null` 如果未明確載入，則為屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-262">The `Department` property is `null` if it's not explicitly loaded.</span></span>
* <span data-ttu-id="ced34-263">若要更新課程實體，必須先擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-263">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="ced34-264">當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-264">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="ced34-265">DatabaseGenerated 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-265">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="ced34-266">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="ced34-266">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="ced34-267">根據預設，EF Core 會假設 PK (主索引鍵) 的值是由資料庫所產生。</span><span class="sxs-lookup"><span data-stu-id="ced34-267">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="ced34-268">由資料庫產生主索引鍵通常是最佳做法。</span><span class="sxs-lookup"><span data-stu-id="ced34-268">Database-generated is generally the best approach.</span></span> <span data-ttu-id="ced34-269">針對 `Course` 實體，使用者指定了 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-269">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="ced34-270">例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。</span><span class="sxs-lookup"><span data-stu-id="ced34-270">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="ced34-271">`DatabaseGenerated` 屬性也可用於產生預設值。</span><span class="sxs-lookup"><span data-stu-id="ced34-271">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="ced34-272">例如，資料庫可以自動產生日期欄位來記錄建立或更新資料列的日期。</span><span class="sxs-lookup"><span data-stu-id="ced34-272">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="ced34-273">如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="ced34-273">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-274">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-274">Foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-275">`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-275">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="ced34-276">由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-276">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="ced34-277">由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="ced34-277">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="ced34-278">課程可由多個講師進行教授，因此 `Instructors` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="ced34-278">A course may be taught by multiple instructors, so the `Instructors` navigation property is a collection:</span></span>

```csharp
        public ICollection<Instructor> Instructors { get; set; }
```

## <a name="the-department-entity"></a><span data-ttu-id="ced34-279">Department 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-279">The Department entity</span></span>

<span data-ttu-id="ced34-280">使用下列程式碼建立 *Models/Department.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-280">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippet1)]

### <a name="the-column-attribute"></a><span data-ttu-id="ced34-281">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-281">The Column attribute</span></span>

<span data-ttu-id="ced34-282">先前，`Column` 屬性主要用於變更資料行的名稱對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-282">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="ced34-283">在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-283">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="ced34-284">`Budget` 資料行在資料庫中是使用 SQL Server 的 money 型別來定義：</span><span class="sxs-lookup"><span data-stu-id="ced34-284">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="ced34-285">通常您不需要資料行對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-285">Column mapping is generally not required.</span></span> <span data-ttu-id="ced34-286">EF Core 會根據屬性的 CLR 型別來選擇適當 SQL Server 資料型別。</span><span class="sxs-lookup"><span data-stu-id="ced34-286">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="ced34-287">CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。</span><span class="sxs-lookup"><span data-stu-id="ced34-287">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="ced34-288">由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。</span><span class="sxs-lookup"><span data-stu-id="ced34-288">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-289">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-289">Foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-290">FK 及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-290">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="ced34-291">部門可能有或可能沒有系統管理員。</span><span class="sxs-lookup"><span data-stu-id="ced34-291">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="ced34-292">系統管理員一律為講師。</span><span class="sxs-lookup"><span data-stu-id="ced34-292">An administrator is always an instructor.</span></span> <span data-ttu-id="ced34-293">因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。</span><span class="sxs-lookup"><span data-stu-id="ced34-293">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="ced34-294">導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：</span><span class="sxs-lookup"><span data-stu-id="ced34-294">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="ced34-295">`?`上述程式碼中的會指定屬性為可為 null。</span><span class="sxs-lookup"><span data-stu-id="ced34-295">The `?` in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="ced34-296">部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-296">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="ced34-297">根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="ced34-297">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="ced34-298">此預設行為可能會導致循環串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-298">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="ced34-299">循環串聯刪除規則會在新增移轉時造成例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ced34-299">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="ced34-300">例如，若 `Department.InstructorID` 屬性已定義成不可為 Null，EF Core 便會設定串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-300">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="ced34-301">在這種情況下，若指派為部門管理員的講師遭到刪除，則會同時刪除部門。</span><span class="sxs-lookup"><span data-stu-id="ced34-301">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="ced34-302">在這種情況下，限制規則會更有意義。</span><span class="sxs-lookup"><span data-stu-id="ced34-302">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="ced34-303">下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，並停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="ced34-303">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

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

### <a name="the-enrollment-foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-304">註冊外鍵和導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-304">The Enrollment foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-305">註冊記錄是某位學生參加的一門課程。</span><span class="sxs-lookup"><span data-stu-id="ced34-305">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png) 

<span data-ttu-id="ced34-307">使用下列程式碼更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-307">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/Enrollment.cs?highlight=1,15)]

<span data-ttu-id="ced34-308">FK 屬性及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-308">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="ced34-309">註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-309">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="ced34-310">註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-310">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="ced34-311">Many-to-Many Relationships</span><span class="sxs-lookup"><span data-stu-id="ced34-311">Many-to-Many Relationships</span></span>

<span data-ttu-id="ced34-312">在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-312">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="ced34-313">`Enrollment`實體會在資料庫中做為多對多聯結資料表 \***與** 裝載 _。</span><span class="sxs-lookup"><span data-stu-id="ced34-313">The `Enrollment` entity functions as a many-to-many join table \***with payload** _ in the database.</span></span> <span data-ttu-id="ced34-314">_ *_With_* 裝載 \* 表示 `Enrollment` 除了 fk 聯結資料表以外，資料表還包含額外的資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-314">_ *_With payload_*\* means that the `Enrollment` table contains additional data besides FKs for the joined tables.</span></span> <span data-ttu-id="ced34-315">在 `Enrollment` 實體中，fk 以外的其他資料是 PK 和 `Grade` 。</span><span class="sxs-lookup"><span data-stu-id="ced34-315">In the `Enrollment` entity, the additional data besides FKs are the PK and `Grade`.</span></span>

<span data-ttu-id="ced34-316">下列圖例展示了在實體圖表中這些關聯性的樣子。</span><span class="sxs-lookup"><span data-stu-id="ced34-316">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="ced34-317">(此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。</span><span class="sxs-lookup"><span data-stu-id="ced34-317">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="ced34-318">建立圖表並不是此教學課程的一部分)。</span><span class="sxs-lookup"><span data-stu-id="ced34-318">Creating the diagram isn't part of the tutorial.)</span></span>

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

<span data-ttu-id="ced34-320">每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (\*)」，顯示其為一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-320">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="ced34-321">如果 `Enrollment` 資料表不包含等級資訊，則只需要包含這兩個 fk `CourseID` 和 `StudentID` 。</span><span class="sxs-lookup"><span data-stu-id="ced34-321">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs, `CourseID` and `StudentID`.</span></span> <span data-ttu-id="ced34-322">沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。</span><span class="sxs-lookup"><span data-stu-id="ced34-322">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="ced34-323">`Instructor`和 `Course` 實體具有多對多關聯性，並使用 PJT。</span><span class="sxs-lookup"><span data-stu-id="ced34-323">The `Instructor` and `Course` entities have a many-to-many relationship using a PJT.</span></span>

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

## <a name="update-the-database-context"></a><span data-ttu-id="ced34-324">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ced34-324">Update the database context</span></span>

<span data-ttu-id="ced34-325">使用下列程式碼來更新 *Data/SchoolContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-325">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Data/SchoolContext.cs?name=snippet_SS&highlight=15-17,21-28)]

<!-- TODO review -->
<span data-ttu-id="ced34-326">上述程式碼會新增新的實體和：</span><span class="sxs-lookup"><span data-stu-id="ced34-326">The preceding code adds the new entities and:</span></span>

* <span data-ttu-id="ced34-327">設定和實體之間的多對多關聯性 `Instructor` `Course` 。</span><span class="sxs-lookup"><span data-stu-id="ced34-327">Configures the many-to-many relationship between the `Instructor` and `Course` entities.</span></span>
* <span data-ttu-id="ced34-328">設定實體的並行存取權杖 `Department` 。</span><span class="sxs-lookup"><span data-stu-id="ced34-328">Configures a concurrency token for the `Department` entity.</span></span> <span data-ttu-id="ced34-329">稍後會在本教學課程中討論並行。</span><span class="sxs-lookup"><span data-stu-id="ced34-329">Concurrency is discussed later in the tutorial.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="ced34-330">屬性的 Fluent API 替代項目</span><span class="sxs-lookup"><span data-stu-id="ced34-330">Fluent API alternative to attributes</span></span>

<span data-ttu-id="ced34-331">上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。</span><span class="sxs-lookup"><span data-stu-id="ced34-331">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="ced34-332">此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。</span><span class="sxs-lookup"><span data-stu-id="ced34-332">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="ced34-333">[下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="ced34-333">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="ced34-334">在本教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-334">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="ced34-335">然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-335">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="ced34-336">某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。</span><span class="sxs-lookup"><span data-stu-id="ced34-336">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="ced34-337">`MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-337">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="ced34-338">有些開發人員偏好以獨佔方式使用流暢的 API，讓它們可以保持實體類別的 *清晰*。</span><span class="sxs-lookup"><span data-stu-id="ced34-338">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes *clean*.</span></span> <span data-ttu-id="ced34-339">屬性和 Fluent API 可混合使用。</span><span class="sxs-lookup"><span data-stu-id="ced34-339">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="ced34-340">有一些設定只能透過流暢的 API 來完成，例如，指定複合 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-340">There are some configurations that can only be done with the fluent API, for example, , specifying a composite PK.</span></span> <span data-ttu-id="ced34-341">有一些設定只能透過屬性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-341">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="ced34-342">使用 Fluent API 或屬性的建議做法為：</span><span class="sxs-lookup"><span data-stu-id="ced34-342">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="ced34-343">從這兩種方法中選擇一項。</span><span class="sxs-lookup"><span data-stu-id="ced34-343">Choose one of these two approaches.</span></span>
* <span data-ttu-id="ced34-344">持續且盡量使用您選擇的方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-344">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="ced34-345">本教學課程中使用到的某些屬性主要用於：</span><span class="sxs-lookup"><span data-stu-id="ced34-345">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="ced34-346">僅驗證 (例如，`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-346">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="ced34-347">僅 EF Core 組態 (例如，`HasKey`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-347">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="ced34-348">驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-348">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="ced34-349">如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="ced34-349">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

<!-- 
## Entity diagram

The following illustration shows the diagram that EF Power Tools create for the completed School model.

![Entity diagram](complex-data-model/_static/diagram.png)

The preceding diagram shows:

* Several one-to-many relationship lines (1 to \*).
* The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.
* The zero-or-one-to-many relationship line (0..1 to *) between the `Instructor` and `Department` entities.
-->

## <a name="seed-the-database"></a><span data-ttu-id="ced34-350">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="ced34-350">Seed the database</span></span>

<span data-ttu-id="ced34-351">更新 *Data/DbInitializer.cs* 中的程式碼：</span><span class="sxs-lookup"><span data-stu-id="ced34-351">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu50/Data/DbInitializer2.cs?name=snippet)]

<span data-ttu-id="ced34-352">上述程式碼為新的實體提供了種子資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-352">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="ced34-353">此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-353">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="ced34-354">範例資料主要用於測試。</span><span class="sxs-lookup"><span data-stu-id="ced34-354">The sample data is used for testing.</span></span>

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

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="ced34-355">套用移轉或卸除並重新建立</span><span class="sxs-lookup"><span data-stu-id="ced34-355">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="ced34-356">使用現有的資料庫時，有兩種方法可以變更資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-356">With the existing database, there are two approaches to changing the database:</span></span>

* <span data-ttu-id="ced34-357">[卸除並重新建立資料庫](#drop)。</span><span class="sxs-lookup"><span data-stu-id="ced34-357">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="ced34-358">如果使用 SQLite，請選擇此區段。</span><span class="sxs-lookup"><span data-stu-id="ced34-358">Choose this section if when using SQLite.</span></span>
* <span data-ttu-id="ced34-359">[將遷移套用至現有的資料庫](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="ced34-359">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="ced34-360">本節中的說明僅適用於 SQL Server，不適用於 ***SQLite***。</span><span class="sxs-lookup"><span data-stu-id="ced34-360">The instructions in this section work for SQL Server only, ***not for SQLite***.</span></span>

<span data-ttu-id="ced34-361">這兩種選擇都適用於 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="ced34-361">Either choice works for SQL Server.</span></span> <span data-ttu-id="ced34-362">雖然套用移轉方法更複雜且耗時，但它是現實世界生產環境的慣用方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-362">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span>

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="ced34-363">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="ced34-363">Drop and re-create the database</span></span>

<span data-ttu-id="ced34-364">強制 EF Core 建立新的資料庫、卸除並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-364">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-365">Visual Studio</span></span>](#tab/visual-studio)

  * <span data-ttu-id="ced34-366">刪除 *Migrations* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ced34-366">Delete the *Migrations* folder.</span></span>
  * <span data-ttu-id="ced34-367">在 **封裝管理員主控台** 中 (PMC) 上，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-367">In the **Package Manager Console** (PMC), run the following commands:</span></span>

  ```powershell
  Drop-Database
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-368">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-368">Visual Studio Code</span></span>](#tab/visual-studio-code)

  * <span data-ttu-id="ced34-369">開啟命令視窗並巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="ced34-369">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="ced34-370">專案資料夾包含 *ContosoUniversity.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="ced34-370">The project folder contains the *ContosoUniversity.csproj* file.</span></span>
  * <span data-ttu-id="ced34-371">刪除 *Migrations* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ced34-371">Delete the *Migrations* folder.</span></span>
  * <span data-ttu-id="ced34-372">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-372">Run the following commands:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

 ---

<span data-ttu-id="ced34-373">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-373">Run the app.</span></span> <span data-ttu-id="ced34-374">執行應用程式會執行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-374">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="ced34-375">`DbInitializer.Initialize` 會填入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ced34-375">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-376">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-376">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ced34-377">在 SSOX 中開啟資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-377">Open the database in SSOX:</span></span>

  * <span data-ttu-id="ced34-378">若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="ced34-378">If SSOX was opened previously, click the **Refresh** button.</span></span>
  * <span data-ttu-id="ced34-379">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="ced34-379">Expand the **Tables** node.</span></span> <span data-ttu-id="ced34-380">建立的資料表便會顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-380">The created tables are displayed.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-381">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-381">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ced34-382">使用 SQLite 工具來檢查資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-382">Use a SQLite tool to examine the database:</span></span>

* <span data-ttu-id="ced34-383">新的資料表和資料行。</span><span class="sxs-lookup"><span data-stu-id="ced34-383">New tables and columns.</span></span>
* <span data-ttu-id="ced34-384">在資料表中植入資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-384">Seeded data in tables.</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="ced34-385">下一步</span><span class="sxs-lookup"><span data-stu-id="ced34-385">Next steps</span></span>

<span data-ttu-id="ced34-386">接下來的兩個教學課程會示範如何讀取和更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-386">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ced34-387">[上一個教學](xref:data/ef-rp/migrations) 
>  課程[下一個教學](xref:data/ef-rp/read-related-data)課程</span><span class="sxs-lookup"><span data-stu-id="ced34-387">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ced34-388">先前的教學課程建立了基本的資料模型，該模型由三個實體組成。</span><span class="sxs-lookup"><span data-stu-id="ced34-388">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="ced34-389">在本教學課程中：</span><span class="sxs-lookup"><span data-stu-id="ced34-389">In this tutorial:</span></span>

* <span data-ttu-id="ced34-390">新增更多實體和關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-390">More entities and relationships are added.</span></span>
* <span data-ttu-id="ced34-391">藉由指定格式、驗證和資料庫對應規則來自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="ced34-391">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="ced34-392">已完成的資料模型如下圖所示：</span><span class="sxs-lookup"><span data-stu-id="ced34-392">The completed data model is shown in the following illustration:</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="ced34-394">Student 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-394">The Student entity</span></span>

![Student 實體](complex-data-model/_static/student-entity.png)

<span data-ttu-id="ced34-396">將 *Models/Student.cs* 中的程式碼替換成下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="ced34-396">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="ced34-397">上述程式碼會新增 `FullName` 屬性，並將下列屬性新增到現有屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-397">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="ced34-398">FullName 計算屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-398">The FullName calculated property</span></span>

<span data-ttu-id="ced34-399">`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。</span><span class="sxs-lookup"><span data-stu-id="ced34-399">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="ced34-400">您無法設定 `FullName`，因此它只會有 get 存取子。</span><span class="sxs-lookup"><span data-stu-id="ced34-400">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="ced34-401">資料庫中不會建立 `FullName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="ced34-401">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="ced34-402">DataType 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-402">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="ced34-403">針對學生註冊日期，所有頁面目前都會同時顯示日期和一天當中的時間，雖然只要日期才是重要項目。</span><span class="sxs-lookup"><span data-stu-id="ced34-403">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="ced34-404">透過使用資料註解屬性，您便可以只透過單一程式碼變更來修正每個顯示資料頁面中的顯示格式。</span><span class="sxs-lookup"><span data-stu-id="ced34-404">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="ced34-405">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。</span><span class="sxs-lookup"><span data-stu-id="ced34-405">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="ced34-406">在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="ced34-406">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="ced34-407">[DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。</span><span class="sxs-lookup"><span data-stu-id="ced34-407">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="ced34-408">例如：</span><span class="sxs-lookup"><span data-stu-id="ced34-408">For example:</span></span>

* <span data-ttu-id="ced34-409">`DataType.EmailAddress` 會自動建立 `mailto:` 連結。</span><span class="sxs-lookup"><span data-stu-id="ced34-409">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="ced34-410">`DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。</span><span class="sxs-lookup"><span data-stu-id="ced34-410">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="ced34-411">`DataType` 屬性會發出 HTML5 `data-` (發音為 data dash) 屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-411">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="ced34-412">`DataType` 屬性不會提供驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-412">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="ced34-413">DisplayFormat 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-413">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="ced34-414">`DataType.Date` 未指定顯示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="ced34-414">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="ced34-415">根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-415">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="ced34-416">`DisplayFormat` 屬性會用來明確指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="ced34-416">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="ced34-417">`ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。</span><span class="sxs-lookup"><span data-stu-id="ced34-417">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="ced34-418">某些欄位不應該使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="ced34-418">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="ced34-419">例如，貨幣符號通常不應顯示在編輯文字方塊中。</span><span class="sxs-lookup"><span data-stu-id="ced34-419">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="ced34-420">`DisplayFormat` 屬性可由自身使用。</span><span class="sxs-lookup"><span data-stu-id="ced34-420">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="ced34-421">通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。</span><span class="sxs-lookup"><span data-stu-id="ced34-421">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="ced34-422">`DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。</span><span class="sxs-lookup"><span data-stu-id="ced34-422">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="ced34-423">`DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：</span><span class="sxs-lookup"><span data-stu-id="ced34-423">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="ced34-424">瀏覽器可以啟用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="ced34-424">The browser can enable HTML5 features.</span></span> <span data-ttu-id="ced34-425">例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結和用戶端輸入驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-425">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="ced34-426">根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-426">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="ced34-427">如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="ced34-427">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="ced34-428">StringLength 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-428">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="ced34-429">您可使用屬性指定資料驗證規則和驗證錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="ced34-429">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="ced34-430">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。</span><span class="sxs-lookup"><span data-stu-id="ced34-430">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="ced34-431">此處所顯示的程式碼會限制名稱不得超過 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-431">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="ced34-432">設定字串長度下限的範例會在[稍後](#the-required-attribute)顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-432">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="ced34-433">`StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-433">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="ced34-434">最小值對資料庫結構描述不會造成任何影響。</span><span class="sxs-lookup"><span data-stu-id="ced34-434">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="ced34-435">`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-435">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="ced34-436">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用來將限制套用到輸入。</span><span class="sxs-lookup"><span data-stu-id="ced34-436">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="ced34-437">例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：</span><span class="sxs-lookup"><span data-stu-id="ced34-437">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-438">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ced34-439">在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。</span><span class="sxs-lookup"><span data-stu-id="ced34-439">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="ced34-441">上述影像顯示了 `Student` 資料表的結構描述。</span><span class="sxs-lookup"><span data-stu-id="ced34-441">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="ced34-442">名稱欄位的型別為 `nvarchar(MAX)`。</span><span class="sxs-lookup"><span data-stu-id="ced34-442">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="ced34-443">在本教學課程稍後建立移轉並套用時，名稱欄位會因為字串長度屬性而變更為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="ced34-443">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ced34-445">在您的 SQLite 工具中，檢查 `Student` 資料表的資料行定義。</span><span class="sxs-lookup"><span data-stu-id="ced34-445">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="ced34-446">名稱欄位的型別為 `Text`。</span><span class="sxs-lookup"><span data-stu-id="ced34-446">The name fields have type `Text`.</span></span> <span data-ttu-id="ced34-447">請注意，第一個名稱欄位稱為 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="ced34-447">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="ced34-448">在下一節中，您會將該資料行的名稱變更為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="ced34-448">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="ced34-449">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-449">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="ced34-450">可控制類別和屬性如何對應到資料庫的屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-450">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="ced34-451">在 `Student` 模型中，`Column` 屬性會用來將 `FirstMidName` 屬性名稱對應到資料庫中的 "FirstName"。</span><span class="sxs-lookup"><span data-stu-id="ced34-451">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="ced34-452">建立資料庫時，模型上的屬性名稱會用來作為資料行名稱 (使用 `Column` 屬性時除外)。</span><span class="sxs-lookup"><span data-stu-id="ced34-452">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="ced34-453">`Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。</span><span class="sxs-lookup"><span data-stu-id="ced34-453">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="ced34-454">透過 `[Column]` 屬性，資料模型中 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="ced34-454">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="ced34-455">新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。</span><span class="sxs-lookup"><span data-stu-id="ced34-455">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="ced34-456">支援 `SchoolContext` 的模型不再符合資料庫。</span><span class="sxs-lookup"><span data-stu-id="ced34-456">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="ced34-457">這種不一致會透過在本教學課程中稍後部分新增移轉來解決。</span><span class="sxs-lookup"><span data-stu-id="ced34-457">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="ced34-458">Required 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-458">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="ced34-459">`Required` 屬性會讓名稱屬性成為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="ced34-459">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="ced34-460">針對不可為 Null 型別的實值型別 (例如 `DateTime`、`int` 和 `double`)，`Required` 屬性並非必要項目。</span><span class="sxs-lookup"><span data-stu-id="ced34-460">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="ced34-461">不可為 Null 的類型會自動視為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="ced34-461">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="ced34-462">`Required` 屬性必須搭配 `MinimumLength` 使用，才能強制執行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="ced34-462">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="ced34-463">`MinimumLength` 與 `Required` 允許空白字元以滿足驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-463">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="ced34-464">使用 `RegularExpression` 屬性來完全控制字串。</span><span class="sxs-lookup"><span data-stu-id="ced34-464">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="ced34-465">Display 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-465">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="ced34-466">`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。</span><span class="sxs-lookup"><span data-stu-id="ced34-466">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="ced34-467">預設標題中沒有使用空白分隔文字，例如「姓氏」。</span><span class="sxs-lookup"><span data-stu-id="ced34-467">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="ced34-468">建立移轉</span><span class="sxs-lookup"><span data-stu-id="ced34-468">Create a migration</span></span>

<span data-ttu-id="ced34-469">執行應用程式並移至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="ced34-469">Run the app and go to the Students page.</span></span> <span data-ttu-id="ced34-470">隨即擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ced34-470">An exception is thrown.</span></span> <span data-ttu-id="ced34-471">`[Column]` 屬性會造成 EF 預期尋找名為 `FirstName` 的資料行，但資料庫中的資料行名稱仍是 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="ced34-471">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-472">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ced34-473">錯誤訊息會與下列範例相似：</span><span class="sxs-lookup"><span data-stu-id="ced34-473">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="ced34-474">在 PMC 中，輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-474">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="ced34-475">這些命令中的第一個命令會產生下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="ced34-475">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="ced34-476">產生警告的理由是名稱欄位現在限制長度為 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-476">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="ced34-477">若資料庫中的名稱超過 50 個字元，則第 51 到最後一個字元便會遺失。</span><span class="sxs-lookup"><span data-stu-id="ced34-477">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="ced34-478">在 SSOX 中開啟 Student 資料表：</span><span class="sxs-lookup"><span data-stu-id="ced34-478">Open the Student table in SSOX:</span></span>

  ![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="ced34-480">在套用移轉前，名稱資料行的型別為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="ced34-480">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="ced34-481">名稱資料行現在為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="ced34-481">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="ced34-482">資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="ced34-482">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-483">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-483">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ced34-484">錯誤訊息會與下列範例相似：</span><span class="sxs-lookup"><span data-stu-id="ced34-484">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="ced34-485">在專案資料夾中開啟命令視窗。</span><span class="sxs-lookup"><span data-stu-id="ced34-485">Open a command window in the project folder.</span></span> <span data-ttu-id="ced34-486">輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-486">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="ced34-487">資料庫更新命令會顯示與下列範例相似的錯誤：</span><span class="sxs-lookup"><span data-stu-id="ced34-487">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="ced34-488">針對本教學課程，通過此錯誤的方法是刪除和重新建立初始移轉。</span><span class="sxs-lookup"><span data-stu-id="ced34-488">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="ced34-489">如需詳細資訊，請參閱[移轉教學課程](xref:data/ef-rp/migrations)頂端的 SQLite 警告附註。</span><span class="sxs-lookup"><span data-stu-id="ced34-489">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="ced34-490">刪除 *Migrations* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="ced34-490">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="ced34-491">執行下列命令來卸除資料庫、建立新的初始移轉，然後套用移轉：</span><span class="sxs-lookup"><span data-stu-id="ced34-491">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="ced34-492">在您的 SQLite 工具中檢查 Student 資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-492">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="ced34-493">原先為 FirstMidName 的資料行現在已變更為 FirstName。</span><span class="sxs-lookup"><span data-stu-id="ced34-493">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="ced34-494">執行應用程式並移至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="ced34-494">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="ced34-495">請注意時間並未輸入或和日期一同顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-495">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="ced34-496">選取 [新建] 然後嘗試輸入超過 50 個字元的名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-496">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="ced34-497">在下列各節中，在某些階段建置應用程式會產生編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="ced34-497">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="ced34-498">指令會指定何時應建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-498">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="ced34-499">Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-499">The Instructor Entity</span></span>

![Instructor 實體](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="ced34-501">使用下列程式碼建立 *Models/Instructor.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-501">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="ced34-502">在同一行上可以有多個屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-502">Multiple attributes can be on one line.</span></span> <span data-ttu-id="ced34-503">`HireDate` 屬性可以下列方式撰寫：</span><span class="sxs-lookup"><span data-stu-id="ced34-503">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="ced34-504">導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-504">Navigation properties</span></span>

<span data-ttu-id="ced34-505">`CourseAssignments` 和 `OfficeAssignment` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-505">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="ced34-506">由於講師可以教授任何數量的課程，因此 `CourseAssignments` 已定義為一個集合。</span><span class="sxs-lookup"><span data-stu-id="ced34-506">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="ced34-507">講師最多只能擁有一間辦公室，因此 `OfficeAssignment` 屬性會保存單一 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-507">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="ced34-508">若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-508">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="ced34-509">OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-509">The OfficeAssignment entity</span></span>

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="ced34-511">使用下列程式碼建立 *Models/OfficeAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-511">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="ced34-512">Key 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-512">The Key attribute</span></span>

<span data-ttu-id="ced34-513">`[Key]` 屬性用於將某個屬性名稱不是 classnameID 或 ID 的屬性識別為主索引鍵 (PK)。</span><span class="sxs-lookup"><span data-stu-id="ced34-513">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="ced34-514">在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-514">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="ced34-515">辦公室指派只存在於與其受指派的講師關聯中。</span><span class="sxs-lookup"><span data-stu-id="ced34-515">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="ced34-516">`OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。</span><span class="sxs-lookup"><span data-stu-id="ced34-516">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="ced34-517">EF Core 無法將 `InstructorID` 自動識別為 `OfficeAssignment` 的主索引鍵，因為 `InstructorID` 並未遵循識別碼或 classnameID 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="ced34-517">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="ced34-518">因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：</span><span class="sxs-lookup"><span data-stu-id="ced34-518">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="ced34-519">根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-519">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="ced34-520">Instructor 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-520">The Instructor navigation property</span></span>

<span data-ttu-id="ced34-521">`Instructor.OfficeAssignment` 導覽屬性可以是 Null，因為指定講師可能不會有 `OfficeAssignment` 資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-521">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="ced34-522">講師可能沒有辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="ced34-522">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="ced34-523">`OfficeAssignment.Instructor` 導覽屬性一律會擁有一個講師實體，因為外部索引鍵 `InstructorID` 的型別是 `int`，其為不可為 Null 的實值型別。</span><span class="sxs-lookup"><span data-stu-id="ced34-523">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="ced34-524">辦公室指派無法獨立於講師之外存在。</span><span class="sxs-lookup"><span data-stu-id="ced34-524">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="ced34-525">當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。</span><span class="sxs-lookup"><span data-stu-id="ced34-525">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="ced34-526">Course 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-526">The Course Entity</span></span>

![Course 實體](complex-data-model/_static/course-entity.png)

<span data-ttu-id="ced34-528">使用下列程式碼更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-528">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="ced34-529">`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="ced34-529">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="ced34-530">`DepartmentID` 會指向相關的 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-530">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="ced34-531">`Course` 實體具有一個 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-531">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="ced34-532">若模型針對相關實體已有導覽屬性，則 EF Core 針對資料模型便不需要外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-532">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="ced34-533">EF Core 會自動在資料庫中需要的任何地方建立 FK。</span><span class="sxs-lookup"><span data-stu-id="ced34-533">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="ced34-534">EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="ced34-534">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="ced34-535">但是，在資料模型中明確包含 FK (外部索引鍵) 可讓更新更簡單且更有效率。</span><span class="sxs-lookup"><span data-stu-id="ced34-535">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="ced34-536">例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-536">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="ced34-537">當擷取課程實體以進行編輯時：</span><span class="sxs-lookup"><span data-stu-id="ced34-537">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="ced34-538">若 `Department` 屬性未明確載入，則其為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-538">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="ced34-539">若要更新課程實體，必須先擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-539">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="ced34-540">當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-540">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="ced34-541">DatabaseGenerated 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-541">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="ced34-542">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="ced34-542">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="ced34-543">根據預設，EF Core 會假設 PK (主索引鍵) 的值是由資料庫所產生。</span><span class="sxs-lookup"><span data-stu-id="ced34-543">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="ced34-544">由資料庫產生主索引鍵通常是最佳做法。</span><span class="sxs-lookup"><span data-stu-id="ced34-544">Database-generated is generally the best approach.</span></span> <span data-ttu-id="ced34-545">針對 `Course` 實體，使用者指定了 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-545">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="ced34-546">例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。</span><span class="sxs-lookup"><span data-stu-id="ced34-546">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="ced34-547">`DatabaseGenerated` 屬性也可用於產生預設值。</span><span class="sxs-lookup"><span data-stu-id="ced34-547">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="ced34-548">例如，資料庫可以自動產生日期欄位來記錄建立或更新資料列的日期。</span><span class="sxs-lookup"><span data-stu-id="ced34-548">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="ced34-549">如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="ced34-549">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-550">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-550">Foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-551">`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-551">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="ced34-552">由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-552">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="ced34-553">由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="ced34-553">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="ced34-554">課程可由多個講師進行教授，因此 `CourseAssignments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="ced34-554">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="ced34-555">`CourseAssignment` 會在[稍後](#many-to-many-relationships)進行解釋。</span><span class="sxs-lookup"><span data-stu-id="ced34-555">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="ced34-556">Department 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-556">The Department entity</span></span>

![部門實體](complex-data-model/_static/department-entity.png)

<span data-ttu-id="ced34-558">使用下列程式碼建立 *Models/Department.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-558">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="ced34-559">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-559">The Column attribute</span></span>

<span data-ttu-id="ced34-560">先前，`Column` 屬性主要用於變更資料行的名稱對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-560">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="ced34-561">在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-561">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="ced34-562">`Budget` 資料行在資料庫中是使用 SQL Server 的 money 型別來定義：</span><span class="sxs-lookup"><span data-stu-id="ced34-562">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="ced34-563">通常您不需要資料行對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-563">Column mapping is generally not required.</span></span> <span data-ttu-id="ced34-564">EF Core 會根據屬性的 CLR 型別來選擇適當 SQL Server 資料型別。</span><span class="sxs-lookup"><span data-stu-id="ced34-564">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="ced34-565">CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。</span><span class="sxs-lookup"><span data-stu-id="ced34-565">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="ced34-566">由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。</span><span class="sxs-lookup"><span data-stu-id="ced34-566">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-567">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-567">Foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-568">FK 及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-568">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="ced34-569">部門可能有或可能沒有系統管理員。</span><span class="sxs-lookup"><span data-stu-id="ced34-569">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="ced34-570">系統管理員一律為講師。</span><span class="sxs-lookup"><span data-stu-id="ced34-570">An administrator is always an instructor.</span></span> <span data-ttu-id="ced34-571">因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。</span><span class="sxs-lookup"><span data-stu-id="ced34-571">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="ced34-572">導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：</span><span class="sxs-lookup"><span data-stu-id="ced34-572">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="ced34-573">上述程式碼中的問號 (?) 表示屬性可為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-573">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="ced34-574">部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-574">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="ced34-575">根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="ced34-575">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="ced34-576">此預設行為可能會導致循環串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-576">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="ced34-577">循環串聯刪除規則會在新增移轉時造成例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ced34-577">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="ced34-578">例如，若 `Department.InstructorID` 屬性已定義成不可為 Null，EF Core 便會設定串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-578">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="ced34-579">在這種情況下，若指派為部門管理員的講師遭到刪除，則會同時刪除部門。</span><span class="sxs-lookup"><span data-stu-id="ced34-579">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="ced34-580">在這種情況下，限制規則會更有意義。</span><span class="sxs-lookup"><span data-stu-id="ced34-580">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="ced34-581">下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，並停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="ced34-581">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="ced34-582">Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-582">The Enrollment entity</span></span>

<span data-ttu-id="ced34-583">註冊記錄是某位學生參加的一門課程。</span><span class="sxs-lookup"><span data-stu-id="ced34-583">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="ced34-585">使用下列程式碼更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-585">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-586">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-586">Foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-587">FK 屬性及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-587">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="ced34-588">註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-588">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="ced34-589">註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-589">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="ced34-590">Many-to-Many Relationships</span><span class="sxs-lookup"><span data-stu-id="ced34-590">Many-to-Many Relationships</span></span>

<span data-ttu-id="ced34-591">在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-591">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="ced34-592">`Enrollment` 實體的功能為資料庫中一個「具有承載」的多對多聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-592">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="ced34-593">「具有承載」表示 `Enrollment` 資料表除了聯結資料表 (在此案例中為 PK 和 `Grade`) 的 FK 之外，還包含了額外的資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-593">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="ced34-594">下列圖例展示了在實體圖表中這些關聯性的樣子。</span><span class="sxs-lookup"><span data-stu-id="ced34-594">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="ced34-595">(此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。</span><span class="sxs-lookup"><span data-stu-id="ced34-595">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="ced34-596">建立圖表並不是此教學課程的一部分)。</span><span class="sxs-lookup"><span data-stu-id="ced34-596">Creating the diagram isn't part of the tutorial.)</span></span>

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

<span data-ttu-id="ced34-598">每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (\*)」，顯示其為一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-598">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="ced34-599">若 `Enrollment` 資料表並未包含成績資訊，則其便只需要包含兩個 FK (`CourseID` 和 `StudentID`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-599">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="ced34-600">沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。</span><span class="sxs-lookup"><span data-stu-id="ced34-600">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="ced34-601">`Instructor` 和 `Course` 實體具有使用了純聯結資料表的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-601">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="ced34-602">注意：EF 6.x 支援多對多關聯性的隱含聯結資料表，但 EF Core 並不支援。</span><span class="sxs-lookup"><span data-stu-id="ced34-602">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="ced34-603">如需詳細資訊，請參閱 [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (EF Core 2.0 中的多對多關聯性)。</span><span class="sxs-lookup"><span data-stu-id="ced34-603">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="ced34-604">CourseAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-604">The CourseAssignment entity</span></span>

![CourseAssignment 實體](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="ced34-606">使用下列程式碼建立 *Models/CourseAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-606">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="ced34-607">Instructor 對 Courses 的多對多關聯性需要聯結資料表，而該聯結資料表的實體為 CourseAssignment。</span><span class="sxs-lookup"><span data-stu-id="ced34-607">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ced34-609">通常會將聯結實體命名為 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="ced34-609">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="ced34-610">例如，使用此模式的 Instructor 對 Courses 聯結資料表將會是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="ced34-610">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="ced34-611">不過，我們建議使用可描述關聯性的名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-611">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="ced34-612">資料模型一開始都是簡單的，之後便會持續成長。</span><span class="sxs-lookup"><span data-stu-id="ced34-612">Data models start out simple and grow.</span></span> <span data-ttu-id="ced34-613">不含承載的聯結資料表 (PJT) 常常會演變成包含承載。</span><span class="sxs-lookup"><span data-stu-id="ced34-613">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="ced34-614">藉由在一開始便使用描述性的實體名稱，當聯結資料表變更時便不需要變更名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-614">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="ced34-615">理想情況下，聯結實體在公司網域中會有自己的自然 (可能為一個單字) 名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-615">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="ced34-616">例如，「書籍」和「客戶」可連結為一個名為「評分」 的聯結實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-616">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="ced34-617">針對講師-課程多對多關聯性，`CourseAssignment` 會比 `CourseInstructor` 來得好。</span><span class="sxs-lookup"><span data-stu-id="ced34-617">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="ced34-618">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="ced34-618">Composite key</span></span>

<span data-ttu-id="ced34-619">`CourseAssignment` (`InstructorID` 及 `CourseID`) 中的兩個 FK 一起搭配使用，便可唯一的識別 `CourseAssignment` 資料表中的每一個資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-619">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="ced34-620">`CourseAssignment` 並不需要其專屬的 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-620">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="ced34-621">`InstructorID` 及 `CourseID` 屬性的功能便是複合 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-621">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="ced34-622">為 EF Core 指定複合 PK 的唯一方法是使用 *Fluent API*。</span><span class="sxs-lookup"><span data-stu-id="ced34-622">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="ced34-623">下節會說明如何設定複合 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-623">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="ced34-624">複合索引鍵可確保：</span><span class="sxs-lookup"><span data-stu-id="ced34-624">The composite key ensures that:</span></span>

* <span data-ttu-id="ced34-625">一個課程可以有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-625">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="ced34-626">一個講師可以有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-626">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="ced34-627">不允許相同講師和課程的多個資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-627">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="ced34-628">由於 `Enrollment` 聯結實體定義了其自身的 PK，因此這種種類的重複項目是可能的。</span><span class="sxs-lookup"><span data-stu-id="ced34-628">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="ced34-629">若要防止這類重複項目：</span><span class="sxs-lookup"><span data-stu-id="ced34-629">To prevent such duplicates:</span></span>

* <span data-ttu-id="ced34-630">在 FK 欄位中新增一個唯一的索引，或</span><span class="sxs-lookup"><span data-stu-id="ced34-630">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="ced34-631">為 `Enrollment` 設定一個主複合索引鍵，與 `CourseAssignment` 相似。</span><span class="sxs-lookup"><span data-stu-id="ced34-631">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="ced34-632">如需詳細資訊，請參閱[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="ced34-632">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="ced34-633">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ced34-633">Update the database context</span></span>

<span data-ttu-id="ced34-634">使用下列程式碼來更新 *Data/SchoolContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-634">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="ced34-635">上述程式碼會新增一個新實體，並設定 `CourseAssignment` 實體的複合 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-635">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="ced34-636">屬性的 Fluent API 替代項目</span><span class="sxs-lookup"><span data-stu-id="ced34-636">Fluent API alternative to attributes</span></span>

<span data-ttu-id="ced34-637">上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。</span><span class="sxs-lookup"><span data-stu-id="ced34-637">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="ced34-638">此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。</span><span class="sxs-lookup"><span data-stu-id="ced34-638">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="ced34-639">[下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="ced34-639">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="ced34-640">在本教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-640">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="ced34-641">然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-641">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="ced34-642">某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。</span><span class="sxs-lookup"><span data-stu-id="ced34-642">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="ced34-643">`MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-643">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="ced34-644">某些開發人員偏好單獨使用 Fluent API，使其實體類別保持「整潔」。</span><span class="sxs-lookup"><span data-stu-id="ced34-644">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="ced34-645">屬性和 Fluent API 可混合使用。</span><span class="sxs-lookup"><span data-stu-id="ced34-645">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="ced34-646">有一些設定只能透過 Fluent API 完成 (指定複合 PK)。</span><span class="sxs-lookup"><span data-stu-id="ced34-646">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="ced34-647">有一些設定只能透過屬性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-647">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="ced34-648">使用 Fluent API 或屬性的建議做法為：</span><span class="sxs-lookup"><span data-stu-id="ced34-648">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="ced34-649">從這兩種方法中選擇一項。</span><span class="sxs-lookup"><span data-stu-id="ced34-649">Choose one of these two approaches.</span></span>
* <span data-ttu-id="ced34-650">持續且盡量使用您選擇的方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-650">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="ced34-651">本教學課程中使用到的某些屬性主要用於：</span><span class="sxs-lookup"><span data-stu-id="ced34-651">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="ced34-652">僅驗證 (例如，`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-652">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="ced34-653">僅 EF Core 組態 (例如，`HasKey`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-653">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="ced34-654">驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-654">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="ced34-655">如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="ced34-655">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="ced34-656">實體圖表</span><span class="sxs-lookup"><span data-stu-id="ced34-656">Entity diagram</span></span>

<span data-ttu-id="ced34-657">下圖顯示了 EF Power Tools 為完成的 School 模型建立的圖表。</span><span class="sxs-lookup"><span data-stu-id="ced34-657">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

<span data-ttu-id="ced34-659">上圖顯示︰</span><span class="sxs-lookup"><span data-stu-id="ced34-659">The preceding diagram shows:</span></span>

* <span data-ttu-id="ced34-660">數個一對多關聯性線條 (1 對 \*)。</span><span class="sxs-lookup"><span data-stu-id="ced34-660">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="ced34-661">`Instructor` 和 `OfficeAssignment` 實體之間的一對零或一關聯性線條 (1 對 0..1)。</span><span class="sxs-lookup"><span data-stu-id="ced34-661">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="ced34-662">`Instructor` 和 `Department` 實體之間的零或一對多關聯性線條 (0..1 對 \*)。</span><span class="sxs-lookup"><span data-stu-id="ced34-662">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="ced34-663">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="ced34-663">Seed the database</span></span>

<span data-ttu-id="ced34-664">更新 *Data/DbInitializer.cs* 中的程式碼：</span><span class="sxs-lookup"><span data-stu-id="ced34-664">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="ced34-665">上述程式碼為新的實體提供了種子資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-665">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="ced34-666">此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-666">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="ced34-667">範例資料主要用於測試。</span><span class="sxs-lookup"><span data-stu-id="ced34-667">The sample data is used for testing.</span></span> <span data-ttu-id="ced34-668">如需如何植入多對多聯結資料表的範例，請參閱 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="ced34-668">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="ced34-669">新增移轉</span><span class="sxs-lookup"><span data-stu-id="ced34-669">Add a migration</span></span>

<span data-ttu-id="ced34-670">建置專案。</span><span class="sxs-lookup"><span data-stu-id="ced34-670">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-671">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-671">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ced34-672">在 PMC 中，執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="ced34-672">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="ced34-673">上述命令會顯示關於可能發生資料遺失的警告。</span><span class="sxs-lookup"><span data-stu-id="ced34-673">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="ced34-674">若執行 `database update` 命令，便會產生下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="ced34-674">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="ced34-675">在下一節中，您會看到如何針對此錯誤採取動作。</span><span class="sxs-lookup"><span data-stu-id="ced34-675">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-676">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-676">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ced34-677">若您新增移轉和執行 `database update` 命令，即會產生下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="ced34-677">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="ced34-678">在下一節中，您會了解如何避免此錯誤。</span><span class="sxs-lookup"><span data-stu-id="ced34-678">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="ced34-679">套用移轉或卸除並重新建立</span><span class="sxs-lookup"><span data-stu-id="ced34-679">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="ced34-680">現在您已經具有現有的資料庫，您需要思考如何將變更套用到該資料庫。</span><span class="sxs-lookup"><span data-stu-id="ced34-680">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="ced34-681">本教學課程示範兩種替代方法：</span><span class="sxs-lookup"><span data-stu-id="ced34-681">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="ced34-682">[卸除並重新建立資料庫](#drop)。</span><span class="sxs-lookup"><span data-stu-id="ced34-682">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="ced34-683">若您正在使用 SQLite，請選擇此節。</span><span class="sxs-lookup"><span data-stu-id="ced34-683">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="ced34-684">[將遷移套用至現有的資料庫](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="ced34-684">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="ced34-685">本節中的說明僅適用於 SQL Server，不適用於 **SQLite**。</span><span class="sxs-lookup"><span data-stu-id="ced34-685">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="ced34-686">這兩種選擇都適用於 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="ced34-686">Either choice works for SQL Server.</span></span> <span data-ttu-id="ced34-687">雖然套用移轉方法更複雜且耗時，但它是現實世界生產環境的慣用方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-687">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="ced34-688">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="ced34-688">Drop and re-create the database</span></span>

<span data-ttu-id="ced34-689">若您正在使用 SQL Server 且想要在下一節中執行套用移轉方法，請[跳過此節](#apply-the-migration)。</span><span class="sxs-lookup"><span data-stu-id="ced34-689">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="ced34-690">強制 EF Core 建立新的資料庫、卸除並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-690">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-691">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-691">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ced34-692">在 [套件管理員主控台] (PMC) 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-692">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="ced34-693">刪除 *Migrations* 資料夾，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-693">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-694">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-694">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ced34-695">開啟命令視窗並巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="ced34-695">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="ced34-696">專案資料夾包含 *ContosoUniversity.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="ced34-696">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="ced34-697">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-697">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="ced34-698">刪除 *Migrations* 資料夾，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-698">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="ced34-699">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-699">Run the app.</span></span> <span data-ttu-id="ced34-700">執行應用程式會執行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-700">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="ced34-701">`DbInitializer.Initialize` 會填入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ced34-701">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-702">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-702">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ced34-703">在 SSOX 中開啟資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-703">Open the database in SSOX:</span></span>

* <span data-ttu-id="ced34-704">若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="ced34-704">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="ced34-705">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="ced34-705">Expand the **Tables** node.</span></span> <span data-ttu-id="ced34-706">建立的資料表便會顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-706">The created tables are displayed.</span></span>

  ![SSOX 中的資料表](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="ced34-708">檢查 **CourseAssignment** 資料表：</span><span class="sxs-lookup"><span data-stu-id="ced34-708">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="ced34-709">以滑鼠右鍵按一下 **CourseAssignment** 資料表，然後選取 [檢視資料]。</span><span class="sxs-lookup"><span data-stu-id="ced34-709">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="ced34-710">驗證 **CourseAssignment** 資料表中是否包含資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-710">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX 中的 CourseAssignment 資料](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-712">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-712">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ced34-713">使用您的 SQLite 工具來檢查資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-713">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="ced34-714">新的資料表和資料行。</span><span class="sxs-lookup"><span data-stu-id="ced34-714">New tables and columns.</span></span>
* <span data-ttu-id="ced34-715">在資料表中植入資料，例如 **CourseAssignment** 資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-715">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="ced34-716">套用移轉</span><span class="sxs-lookup"><span data-stu-id="ced34-716">Apply the migration</span></span>

<span data-ttu-id="ced34-717">此為選擇性區段。</span><span class="sxs-lookup"><span data-stu-id="ced34-717">This section is optional.</span></span> <span data-ttu-id="ced34-718">這些步驟僅適用於 SQL Server LocalDB，且只有在您跳過先前的[卸除並重新建立資料庫](#drop)一節時才適用。</span><span class="sxs-lookup"><span data-stu-id="ced34-718">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="ced34-719">當使用現有的資料執行移轉作業時，某些 FK 條件約束可能會無法透過現有資料滿足。</span><span class="sxs-lookup"><span data-stu-id="ced34-719">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="ced34-720">當您使用的是生產資料時，您必須進行幾個步驟才能移轉現有資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-720">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="ced34-721">本節提供了修正 FK 條件約束違規的範例。</span><span class="sxs-lookup"><span data-stu-id="ced34-721">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="ced34-722">請不要在沒有備份的情況下進行這些程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="ced34-722">Don't make these code changes without a backup.</span></span> <span data-ttu-id="ced34-723">若您已完成先前的[卸除並重新建立資料庫](#drop)一節，請不要變更這些程式碼。</span><span class="sxs-lookup"><span data-stu-id="ced34-723">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="ced34-724">*{timestamp}_ComplexDataModel.cs* 檔案包含了下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="ced34-724">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="ced34-725">上述程式碼將一個不可為 Null 的 `DepartmentID` FK 新增至 `Course` 資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-725">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="ced34-726">先前教學課程中的資料庫在 `Course` 中包含了資料列，因此無法使用移轉來更新資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-726">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="ced34-727">若要讓 `ComplexDataModel` 與現有資料進行移轉：</span><span class="sxs-lookup"><span data-stu-id="ced34-727">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="ced34-728">變更程式碼，以給予新資料行 (`DepartmentID`) 一個新的預設值。</span><span class="sxs-lookup"><span data-stu-id="ced34-728">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="ced34-729">建立一個名為 "Temp" 的假部門以作為預設部門之用。</span><span class="sxs-lookup"><span data-stu-id="ced34-729">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="ced34-730">修正外部索引鍵條件約束</span><span class="sxs-lookup"><span data-stu-id="ced34-730">Fix the foreign key constraints</span></span>

<span data-ttu-id="ced34-731">在 `ComplexDataModel` 移轉類別中，更新 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="ced34-731">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="ced34-732">開啟 *{timestamp}_ComplexDataModel.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="ced34-732">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="ced34-733">將新增 `DepartmentID` 資料行至 `Course` 資料表的程式碼全部標為註解。</span><span class="sxs-lookup"><span data-stu-id="ced34-733">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="ced34-734">新增下列醒目提示程式碼。</span><span class="sxs-lookup"><span data-stu-id="ced34-734">Add the following highlighted code.</span></span> <span data-ttu-id="ced34-735">新的程式碼位於 `.CreateTable( name: "Department"` 區塊後方：</span><span class="sxs-lookup"><span data-stu-id="ced34-735">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="ced34-736">透過上述變更，現有的 `Course` 資料列將會在執行 `ComplexDataModel.Up` 方法後與 "Temp" 部門相關。</span><span class="sxs-lookup"><span data-stu-id="ced34-736">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="ced34-737">此處顯示處理這種情況的方式已針對本教學課程進行簡化。</span><span class="sxs-lookup"><span data-stu-id="ced34-737">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="ced34-738">生產環境的應用程式會：</span><span class="sxs-lookup"><span data-stu-id="ced34-738">A production app would:</span></span>

* <span data-ttu-id="ced34-739">包含將 `Department` 資料列及相關 `Course` 資料列新增到新 `Department` 資料列的程式碼或指令碼。</span><span class="sxs-lookup"><span data-stu-id="ced34-739">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="ced34-740">不使用 "Temp" 部門或 `Course.DepartmentID` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="ced34-740">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-741">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-741">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ced34-742">在 [套件管理員主控台] (PMC) 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-742">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="ced34-743">因為 `DbInitializer.Initialize` 方法的設計僅適用於空白資料庫，所以請使用 SSOX 刪除 Student 和 Course 資料表中的所有資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-743">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="ced34-744">(串聯刪除會負責處理 Enrollment 資料表。)</span><span class="sxs-lookup"><span data-stu-id="ced34-744">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-745">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-745">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ced34-746">若您正在搭配 Visual Studio Code 使用 SQL Server LocalDB，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-746">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="ced34-747">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-747">Run the app.</span></span> <span data-ttu-id="ced34-748">執行應用程式會執行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-748">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="ced34-749">`DbInitializer.Initialize` 會填入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ced34-749">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ced34-750">下一步</span><span class="sxs-lookup"><span data-stu-id="ced34-750">Next steps</span></span>

<span data-ttu-id="ced34-751">接下來的兩個教學課程會示範如何讀取和更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-751">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ced34-752">[上一個教學](xref:data/ef-rp/migrations) 
>  課程[下一個教學](xref:data/ef-rp/read-related-data)課程</span><span class="sxs-lookup"><span data-stu-id="ced34-752">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ced34-753">先前的教學課程建立了基本的資料模型，該模型由三個實體組成。</span><span class="sxs-lookup"><span data-stu-id="ced34-753">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="ced34-754">在本教學課程中：</span><span class="sxs-lookup"><span data-stu-id="ced34-754">In this tutorial:</span></span>

* <span data-ttu-id="ced34-755">新增更多實體和關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-755">More entities and relationships are added.</span></span>
* <span data-ttu-id="ced34-756">藉由指定格式、驗證和資料庫對應規則來自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="ced34-756">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="ced34-757">下圖顯示已完成資料模型的實體類別：</span><span class="sxs-lookup"><span data-stu-id="ced34-757">The entity classes for the completed data model are shown in the following illustration:</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

<span data-ttu-id="ced34-759">若您遇到無法解決的問題，請下載[完整應用程式](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="ced34-759">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="ced34-760">使用屬性自訂資料模型</span><span class="sxs-lookup"><span data-stu-id="ced34-760">Customize the data model with attributes</span></span>

<span data-ttu-id="ced34-761">在本節中，您會使用屬性自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="ced34-761">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="ced34-762">DataType 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-762">The DataType attribute</span></span>

<span data-ttu-id="ced34-763">學生頁面目前顯示了註冊日期的時間。</span><span class="sxs-lookup"><span data-stu-id="ced34-763">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="ced34-764">一般而言，日期欄位只會顯示日期，而非時間。</span><span class="sxs-lookup"><span data-stu-id="ced34-764">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="ced34-765">使用下列醒目提示的程式碼更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-765">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="ced34-766">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。</span><span class="sxs-lookup"><span data-stu-id="ced34-766">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="ced34-767">在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="ced34-767">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="ced34-768">[DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。</span><span class="sxs-lookup"><span data-stu-id="ced34-768">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="ced34-769">例如：</span><span class="sxs-lookup"><span data-stu-id="ced34-769">For example:</span></span>

* <span data-ttu-id="ced34-770">`DataType.EmailAddress` 會自動建立 `mailto:` 連結。</span><span class="sxs-lookup"><span data-stu-id="ced34-770">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="ced34-771">`DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。</span><span class="sxs-lookup"><span data-stu-id="ced34-771">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="ced34-772">`DataType` 屬性會發出 HTML 5 `data-` (發音為 data dash) 屬性，可讓 HTML 5 瀏覽器取用。</span><span class="sxs-lookup"><span data-stu-id="ced34-772">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="ced34-773">`DataType` 屬性不會提供驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-773">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="ced34-774">`DataType.Date` 未指定顯示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="ced34-774">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="ced34-775">根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-775">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="ced34-776">`DisplayFormat` 屬性用來明確指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="ced34-776">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="ced34-777">`ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。</span><span class="sxs-lookup"><span data-stu-id="ced34-777">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="ced34-778">某些欄位不應該使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="ced34-778">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="ced34-779">例如，貨幣符號通常不應顯示在編輯文字方塊中。</span><span class="sxs-lookup"><span data-stu-id="ced34-779">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="ced34-780">`DisplayFormat` 屬性可由自身使用。</span><span class="sxs-lookup"><span data-stu-id="ced34-780">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="ced34-781">通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。</span><span class="sxs-lookup"><span data-stu-id="ced34-781">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="ced34-782">`DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。</span><span class="sxs-lookup"><span data-stu-id="ced34-782">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="ced34-783">`DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：</span><span class="sxs-lookup"><span data-stu-id="ced34-783">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="ced34-784">瀏覽器可以啟用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="ced34-784">The browser can enable HTML5 features.</span></span> <span data-ttu-id="ced34-785">例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結、用戶端輸入驗證等。</span><span class="sxs-lookup"><span data-stu-id="ced34-785">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="ced34-786">根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-786">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="ced34-787">如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="ced34-787">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="ced34-788">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-788">Run the app.</span></span> <span data-ttu-id="ced34-789">巡覽至 Students [索引] 頁面。</span><span class="sxs-lookup"><span data-stu-id="ced34-789">Navigate to the Students Index page.</span></span> <span data-ttu-id="ced34-790">時間將不再顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-790">Times are no longer displayed.</span></span> <span data-ttu-id="ced34-791">使用 `Student` 模型的每個檢視現在都只會顯示日期，而不會顯示時間。</span><span class="sxs-lookup"><span data-stu-id="ced34-791">Every view that uses the `Student` model displays the date without time.</span></span>

![顯示日期而不顯示時間的 Students [索引] 頁面](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="ced34-793">StringLength 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-793">The StringLength attribute</span></span>

<span data-ttu-id="ced34-794">您可使用屬性指定資料驗證規則和驗證錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="ced34-794">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="ced34-795">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。</span><span class="sxs-lookup"><span data-stu-id="ced34-795">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="ced34-796">`StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。</span><span class="sxs-lookup"><span data-stu-id="ced34-796">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="ced34-797">最小值對資料庫結構描述不會造成任何影響。</span><span class="sxs-lookup"><span data-stu-id="ced34-797">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="ced34-798">使用下列程式碼更新 `Student` 模型：</span><span class="sxs-lookup"><span data-stu-id="ced34-798">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="ced34-799">上述的程式碼會限制名稱不得超過 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-799">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="ced34-800">`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-800">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="ced34-801">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用於對輸入套用限制。</span><span class="sxs-lookup"><span data-stu-id="ced34-801">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="ced34-802">例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：</span><span class="sxs-lookup"><span data-stu-id="ced34-802">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="ced34-803">執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="ced34-803">Run the app:</span></span>

* <span data-ttu-id="ced34-804">巡覽至 Students 頁面。</span><span class="sxs-lookup"><span data-stu-id="ced34-804">Navigate to the Students page.</span></span>
* <span data-ttu-id="ced34-805">選取 [新建]，然後輸入超過 50 個字元的名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-805">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="ced34-806">選取 [建立]，用戶端驗證便會顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="ced34-806">Select **Create**, client-side validation shows an error message.</span></span>

![顯示字元長度錯誤的 Students [索引] 頁面](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="ced34-808">在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。</span><span class="sxs-lookup"><span data-stu-id="ced34-808">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="ced34-810">上述影像顯示了 `Student` 資料表的結構描述。</span><span class="sxs-lookup"><span data-stu-id="ced34-810">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="ced34-811">名稱欄位的類型為 `nvarchar(MAX)`，因為移轉尚未在資料庫中執行。</span><span class="sxs-lookup"><span data-stu-id="ced34-811">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="ced34-812">在本教學課程的稍後執行移轉後，名稱欄位便會成為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="ced34-812">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="ced34-813">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-813">The Column attribute</span></span>

<span data-ttu-id="ced34-814">可控制類別和屬性如何對應到資料庫的屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-814">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="ced34-815">在本節中，`Column` 屬性會用於將 `FirstMidName` 屬性的名稱對應到資料庫中的 "FirstName"。</span><span class="sxs-lookup"><span data-stu-id="ced34-815">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="ced34-816">當建立資料庫時，模型上的屬性名稱會用於資料行名稱 (除了使用 `Column` 屬性時之外)。</span><span class="sxs-lookup"><span data-stu-id="ced34-816">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="ced34-817">`Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。</span><span class="sxs-lookup"><span data-stu-id="ced34-817">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="ced34-818">使用下列醒目提示程式碼更新 *Student.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="ced34-818">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="ced34-819">經過上述變更之後，應用程式中的 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="ced34-819">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="ced34-820">新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。</span><span class="sxs-lookup"><span data-stu-id="ced34-820">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="ced34-821">支援 `SchoolContext` 的模型不再符合資料庫。</span><span class="sxs-lookup"><span data-stu-id="ced34-821">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="ced34-822">若應用程式在套用移轉之前執行，便會產生下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="ced34-822">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="ced34-823">若要更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-823">To update the DB:</span></span>

* <span data-ttu-id="ced34-824">建置專案。</span><span class="sxs-lookup"><span data-stu-id="ced34-824">Build the project.</span></span>
* <span data-ttu-id="ced34-825">在專案資料夾中開啟命令視窗。</span><span class="sxs-lookup"><span data-stu-id="ced34-825">Open a command window in the project folder.</span></span> <span data-ttu-id="ced34-826">輸入下列命令來建立新的移轉並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-826">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-827">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-827">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-828">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-828">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="ced34-829">`migrations add ColumnFirstName` 命令會產生下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="ced34-829">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="ced34-830">產生警告的理由是名稱欄位現在限制長度為 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="ced34-830">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="ced34-831">若在資料庫中有名稱超過 50 個字元，第 51 個字元到最後一個字元便會遺失。</span><span class="sxs-lookup"><span data-stu-id="ced34-831">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="ced34-832">測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-832">Test the app.</span></span>

<span data-ttu-id="ced34-833">在 SSOX 中開啟 Student 資料表：</span><span class="sxs-lookup"><span data-stu-id="ced34-833">Open the Student table in SSOX:</span></span>

![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="ced34-835">在移轉套用之前，名稱資料行的類型為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="ced34-835">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="ced34-836">名稱資料行現在為 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="ced34-836">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="ced34-837">資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="ced34-837">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="ced34-838">在下節中，在某些階段建置應用程式會產生編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="ced34-838">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="ced34-839">指令會指定何時應建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-839">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="ced34-840">Student 實體更新</span><span class="sxs-lookup"><span data-stu-id="ced34-840">Student entity update</span></span>

![Student 實體](complex-data-model/_static/student-entity.png)

<span data-ttu-id="ced34-842">使用下列程式碼更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-842">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="ced34-843">Required 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-843">The Required attribute</span></span>

<span data-ttu-id="ced34-844">`Required` 屬性會讓名稱屬性成為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="ced34-844">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="ced34-845">針對不可為 Null 的類型，例如實值型別 (`DateTime`、`int`、`double` 等) 等，`Required` 屬性是不需要的。</span><span class="sxs-lookup"><span data-stu-id="ced34-845">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="ced34-846">不可為 Null 的類型會自動視為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="ced34-846">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="ced34-847">`Required` 屬性可由 `StringLength` 屬性中的最小長度參數取代：</span><span class="sxs-lookup"><span data-stu-id="ced34-847">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="ced34-848">Display 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-848">The Display attribute</span></span>

<span data-ttu-id="ced34-849">`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。</span><span class="sxs-lookup"><span data-stu-id="ced34-849">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="ced34-850">預設標題中沒有使用空白分隔文字，例如「姓氏」。</span><span class="sxs-lookup"><span data-stu-id="ced34-850">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="ced34-851">FullName 計算屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-851">The FullName calculated property</span></span>

<span data-ttu-id="ced34-852">`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。</span><span class="sxs-lookup"><span data-stu-id="ced34-852">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="ced34-853">`FullName` 無法進行設定，其只有 get 存取子。</span><span class="sxs-lookup"><span data-stu-id="ced34-853">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="ced34-854">資料庫中不會建立 `FullName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="ced34-854">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="ced34-855">建立 Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-855">Create the Instructor Entity</span></span>

![Instructor 實體](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="ced34-857">使用下列程式碼建立 *Models/Instructor.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-857">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="ced34-858">在同一行上可以有多個屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-858">Multiple attributes can be on one line.</span></span> <span data-ttu-id="ced34-859">`HireDate` 屬性可以下列方式撰寫：</span><span class="sxs-lookup"><span data-stu-id="ced34-859">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="ced34-860">CourseAssignments 和 OfficeAssignment 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-860">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="ced34-861">`CourseAssignments` 和 `OfficeAssignment` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-861">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="ced34-862">由於講師可以教授任何數量的課程，因此 `CourseAssignments` 已定義為一個集合。</span><span class="sxs-lookup"><span data-stu-id="ced34-862">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="ced34-863">若導覽屬性中保留了多個實體：</span><span class="sxs-lookup"><span data-stu-id="ced34-863">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="ced34-864">它必須是一種清單類型，可對其中的實體進行新增、刪除和更新。</span><span class="sxs-lookup"><span data-stu-id="ced34-864">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="ced34-865">導覽屬性類型包括：</span><span class="sxs-lookup"><span data-stu-id="ced34-865">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="ced34-866">若已指定 `ICollection<T>`，EF Core 會根據預設建立一個 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="ced34-866">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="ced34-867">`CourseAssignment` 實體會在本節的多對多關聯性中解釋。</span><span class="sxs-lookup"><span data-stu-id="ced34-867">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="ced34-868">Contoso 大學商務規則訂定了講師最多只能有一間辦公室。</span><span class="sxs-lookup"><span data-stu-id="ced34-868">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="ced34-869">`OfficeAssignment` 屬性保留了一個單一 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-869">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="ced34-870">若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-870">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="ced34-871">建立 OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-871">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="ced34-873">使用下列程式碼建立 *Models/OfficeAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-873">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="ced34-874">Key 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-874">The Key attribute</span></span>

<span data-ttu-id="ced34-875">`[Key]` 屬性用於將某個屬性名稱不是 classnameID 或 ID 的屬性識別為主索引鍵 (PK)。</span><span class="sxs-lookup"><span data-stu-id="ced34-875">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="ced34-876">在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-876">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="ced34-877">辦公室指派只存在於與其受指派的講師關聯中。</span><span class="sxs-lookup"><span data-stu-id="ced34-877">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="ced34-878">`OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。</span><span class="sxs-lookup"><span data-stu-id="ced34-878">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="ced34-879">EF Core 無法自動將 `InstructorID` 識別為 `OfficeAssignment` 的主索引鍵，因為：</span><span class="sxs-lookup"><span data-stu-id="ced34-879">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="ced34-880">`InstructorID` 並未遵循 ID 或 classnameID 的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="ced34-880">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="ced34-881">因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：</span><span class="sxs-lookup"><span data-stu-id="ced34-881">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="ced34-882">根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-882">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="ced34-883">Instructor 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-883">The Instructor navigation property</span></span>

<span data-ttu-id="ced34-884">`Instructor` 實體的 `OfficeAssignment` 導覽屬性可為 Null，因為：</span><span class="sxs-lookup"><span data-stu-id="ced34-884">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="ced34-885">參考型別 (例如類別可為 Null)。</span><span class="sxs-lookup"><span data-stu-id="ced34-885">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="ced34-886">講師可能沒有辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="ced34-886">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="ced34-887">`OfficeAssignment` 實體有不可為 Null 的`Instructor` 導覽屬性，因為：</span><span class="sxs-lookup"><span data-stu-id="ced34-887">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="ced34-888">`InstructorID` 不可為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-888">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="ced34-889">辦公室指派無法獨立於講師之外存在。</span><span class="sxs-lookup"><span data-stu-id="ced34-889">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="ced34-890">當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。</span><span class="sxs-lookup"><span data-stu-id="ced34-890">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="ced34-891">`[Required]` 屬性可套用到 `Instructor` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-891">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="ced34-892">上述程式碼指定了必須要有相關的講師。</span><span class="sxs-lookup"><span data-stu-id="ced34-892">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="ced34-893">上述程式碼是不必要的，因為 `InstructorID` 外部索引鍵 (同時也是 PK) 不可為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-893">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="ced34-894">修改 Course 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-894">Modify the Course Entity</span></span>

![Course 實體](complex-data-model/_static/course-entity.png)

<span data-ttu-id="ced34-896">使用下列程式碼更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-896">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="ced34-897">`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="ced34-897">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="ced34-898">`DepartmentID` 會指向相關的 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-898">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="ced34-899">`Course` 實體具有一個 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-899">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="ced34-900">當資料模型針對相關實體有一個導覽屬性時，EF Core 不需要針對該模型具備 FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-900">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="ced34-901">EF Core 會自動在資料庫中需要的任何地方建立 FK。</span><span class="sxs-lookup"><span data-stu-id="ced34-901">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="ced34-902">EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="ced34-902">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="ced34-903">在資料模型中具備 FK 可讓更新變得更為簡單和有效率。</span><span class="sxs-lookup"><span data-stu-id="ced34-903">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="ced34-904">例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-904">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="ced34-905">當擷取課程實體以進行編輯時：</span><span class="sxs-lookup"><span data-stu-id="ced34-905">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="ced34-906">若沒有明確載入，`Department` 實體將為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-906">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="ced34-907">若要更新課程實體，必須先擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-907">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="ced34-908">當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-908">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="ced34-909">DatabaseGenerated 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-909">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="ced34-910">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="ced34-910">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="ced34-911">根據預設，EF Core 會假設 PK 值都是由資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="ced34-911">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="ced34-912">由資料庫產生 PK 值通常都是最佳做法。</span><span class="sxs-lookup"><span data-stu-id="ced34-912">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="ced34-913">針對 `Course` 實體，使用者指定了 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-913">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="ced34-914">例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。</span><span class="sxs-lookup"><span data-stu-id="ced34-914">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="ced34-915">`DatabaseGenerated` 屬性也可用於產生預設值。</span><span class="sxs-lookup"><span data-stu-id="ced34-915">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="ced34-916">例如，資料庫會自動產生日期欄位來記錄資料列建立或更新的日期。</span><span class="sxs-lookup"><span data-stu-id="ced34-916">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="ced34-917">如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="ced34-917">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-918">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-918">Foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-919">`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-919">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="ced34-920">由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="ced34-920">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="ced34-921">由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="ced34-921">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="ced34-922">課程可由多個講師進行教授，因此 `CourseAssignments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="ced34-922">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="ced34-923">`CourseAssignment` 會在[稍後](#many-to-many-relationships)進行解釋。</span><span class="sxs-lookup"><span data-stu-id="ced34-923">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="ced34-924">建立 Department 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-924">Create the Department entity</span></span>

![部門實體](complex-data-model/_static/department-entity.png)

<span data-ttu-id="ced34-926">使用下列程式碼建立 *Models/Department.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-926">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="ced34-927">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-927">The Column attribute</span></span>

<span data-ttu-id="ced34-928">先前，`Column` 屬性主要用於變更資料行的名稱對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-928">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="ced34-929">在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-929">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="ced34-930">`Budget` 資料行則是使用資料庫中的 SQL Server 金額類型定義：</span><span class="sxs-lookup"><span data-stu-id="ced34-930">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="ced34-931">通常您不需要資料行對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-931">Column mapping is generally not required.</span></span> <span data-ttu-id="ced34-932">EF Core 通常會根據屬性的 CLR 類型選擇適當的 SQL Server 資料類型。</span><span class="sxs-lookup"><span data-stu-id="ced34-932">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="ced34-933">CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。</span><span class="sxs-lookup"><span data-stu-id="ced34-933">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="ced34-934">由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。</span><span class="sxs-lookup"><span data-stu-id="ced34-934">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-935">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-935">Foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-936">FK 及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-936">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="ced34-937">部門可能有或可能沒有系統管理員。</span><span class="sxs-lookup"><span data-stu-id="ced34-937">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="ced34-938">系統管理員一律為講師。</span><span class="sxs-lookup"><span data-stu-id="ced34-938">An administrator is always an instructor.</span></span> <span data-ttu-id="ced34-939">因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。</span><span class="sxs-lookup"><span data-stu-id="ced34-939">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="ced34-940">導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：</span><span class="sxs-lookup"><span data-stu-id="ced34-940">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="ced34-941">上述程式碼中的問號 (?) 表示屬性可為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-941">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="ced34-942">部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-942">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="ced34-943">注意：根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="ced34-943">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="ced34-944">串聯刪除可能會導致循環的串聯刪除規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-944">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="ced34-945">循環串聯刪除規則會在新增移轉時造成例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ced34-945">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="ced34-946">例如，若已將 `Department.InstructorID` 屬性定義為不可為 Null：</span><span class="sxs-lookup"><span data-stu-id="ced34-946">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="ced34-947">EF Core 會設定串聯刪除規則，以便在刪除講師時刪除部門。</span><span class="sxs-lookup"><span data-stu-id="ced34-947">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="ced34-948">在刪除講師時刪除部門並非預期的行為。</span><span class="sxs-lookup"><span data-stu-id="ced34-948">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="ced34-949">下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，而不是 cascade。</span><span class="sxs-lookup"><span data-stu-id="ced34-949">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="ced34-950">上述的程式碼會在部門-講師關聯性上停用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="ced34-950">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="ced34-951">更新 Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-951">Update the Enrollment entity</span></span>

<span data-ttu-id="ced34-952">註冊記錄是某位學生參加的一門課程。</span><span class="sxs-lookup"><span data-stu-id="ced34-952">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="ced34-954">使用下列程式碼更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-954">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="ced34-955">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="ced34-955">Foreign key and navigation properties</span></span>

<span data-ttu-id="ced34-956">FK 屬性及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-956">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="ced34-957">註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-957">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="ced34-958">註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="ced34-958">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="ced34-959">Many-to-Many Relationships</span><span class="sxs-lookup"><span data-stu-id="ced34-959">Many-to-Many Relationships</span></span>

<span data-ttu-id="ced34-960">在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-960">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="ced34-961">`Enrollment` 實體的功能為資料庫中一個「具有承載」的多對多聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-961">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="ced34-962">「具有承載」表示 `Enrollment` 資料表除了聯結資料表 (在此案例中為 PK 和 `Grade`) 的 FK 之外，還包含了額外的資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-962">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="ced34-963">下列圖例展示了在實體圖表中這些關聯性的樣子。</span><span class="sxs-lookup"><span data-stu-id="ced34-963">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="ced34-964">(此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。</span><span class="sxs-lookup"><span data-stu-id="ced34-964">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="ced34-965">建立圖表並不是此教學課程的一部分)。</span><span class="sxs-lookup"><span data-stu-id="ced34-965">Creating the diagram isn't part of the tutorial.)</span></span>

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

<span data-ttu-id="ced34-967">每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (\*)」，顯示其為一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-967">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="ced34-968">若 `Enrollment` 資料表並未包含成績資訊，則其便只需要包含兩個 FK (`CourseID` 和 `StudentID`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-968">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="ced34-969">沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。</span><span class="sxs-lookup"><span data-stu-id="ced34-969">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="ced34-970">`Instructor` 和 `Course` 實體具有使用了純聯結資料表的多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="ced34-970">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="ced34-971">注意：EF 6.x 支援多對多關聯性的隱含聯結資料表，但 EF Core 並不支援。</span><span class="sxs-lookup"><span data-stu-id="ced34-971">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="ced34-972">如需詳細資訊，請參閱 [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (EF Core 2.0 中的多對多關聯性)。</span><span class="sxs-lookup"><span data-stu-id="ced34-972">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="ced34-973">CourseAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="ced34-973">The CourseAssignment entity</span></span>

![CourseAssignment 實體](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="ced34-975">使用下列程式碼建立 *Models/CourseAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-975">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="ced34-976">講師-課程</span><span class="sxs-lookup"><span data-stu-id="ced34-976">Instructor-to-Courses</span></span>

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ced34-978">講師-課程多對多關聯性：</span><span class="sxs-lookup"><span data-stu-id="ced34-978">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="ced34-979">需要一個由實體集代表的聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-979">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="ced34-980">為一個純聯結資料表 (沒有承載的資料表)。</span><span class="sxs-lookup"><span data-stu-id="ced34-980">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="ced34-981">通常會將聯結實體命名為 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="ced34-981">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="ced34-982">例如，使用此模式的講師-課程聯結資料表為 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="ced34-982">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="ced34-983">不過，我們建議使用可描述關聯性的名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-983">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="ced34-984">資料模型一開始都是簡單的，之後便會持續成長。</span><span class="sxs-lookup"><span data-stu-id="ced34-984">Data models start out simple and grow.</span></span> <span data-ttu-id="ced34-985">無承載聯結 (PJT) 常常會演變為包含承載。</span><span class="sxs-lookup"><span data-stu-id="ced34-985">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="ced34-986">藉由在一開始便使用描述性的實體名稱，當聯結資料表變更時便不需要變更名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-986">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="ced34-987">理想情況下，聯結實體在公司網域中會有自己的自然 (可能為一個單字) 名稱。</span><span class="sxs-lookup"><span data-stu-id="ced34-987">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="ced34-988">例如，「書籍」和「客戶」可連結為一個名為「評分」 的聯結實體。</span><span class="sxs-lookup"><span data-stu-id="ced34-988">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="ced34-989">針對講師-課程多對多關聯性，`CourseAssignment` 會比 `CourseInstructor` 來得好。</span><span class="sxs-lookup"><span data-stu-id="ced34-989">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="ced34-990">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="ced34-990">Composite key</span></span>

<span data-ttu-id="ced34-991">FK 不可為 Null。</span><span class="sxs-lookup"><span data-stu-id="ced34-991">FKs are not nullable.</span></span> <span data-ttu-id="ced34-992">`CourseAssignment` (`InstructorID` 及 `CourseID`) 中的兩個 FK 一起搭配使用，便可唯一的識別 `CourseAssignment` 資料表中的每一個資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-992">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="ced34-993">`CourseAssignment` 並不需要其專屬的 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-993">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="ced34-994">`InstructorID` 及 `CourseID` 屬性的功能便是複合 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-994">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="ced34-995">為 EF Core 指定複合 PK 的唯一方法是使用 *Fluent API*。</span><span class="sxs-lookup"><span data-stu-id="ced34-995">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="ced34-996">下節會說明如何設定複合 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-996">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="ced34-997">複合 PK 可確保：</span><span class="sxs-lookup"><span data-stu-id="ced34-997">The composite key ensures:</span></span>

* <span data-ttu-id="ced34-998">一個課程可以有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-998">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="ced34-999">一個講師可以有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-999">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="ced34-1000">相同講師和課程不可有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="ced34-1000">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="ced34-1001">由於 `Enrollment` 聯結實體定義了其自身的 PK，因此這種種類的重複項目是可能的。</span><span class="sxs-lookup"><span data-stu-id="ced34-1001">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="ced34-1002">若要防止這類重複項目：</span><span class="sxs-lookup"><span data-stu-id="ced34-1002">To prevent such duplicates:</span></span>

* <span data-ttu-id="ced34-1003">在 FK 欄位中新增一個唯一的索引，或</span><span class="sxs-lookup"><span data-stu-id="ced34-1003">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="ced34-1004">為 `Enrollment` 設定一個主複合索引鍵，與 `CourseAssignment` 相似。</span><span class="sxs-lookup"><span data-stu-id="ced34-1004">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="ced34-1005">如需詳細資訊，請參閱[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1005">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="ced34-1006">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="ced34-1006">Update the DB context</span></span>

<span data-ttu-id="ced34-1007">將下列醒目提示的程式碼新增至 *Data/SchoolContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="ced34-1007">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="ced34-1008">上述程式碼會新增一個新實體，並設定 `CourseAssignment` 實體的複合 PK。</span><span class="sxs-lookup"><span data-stu-id="ced34-1008">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="ced34-1009">屬性的 Fluent API 替代項目</span><span class="sxs-lookup"><span data-stu-id="ced34-1009">Fluent API alternative to attributes</span></span>

<span data-ttu-id="ced34-1010">上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。</span><span class="sxs-lookup"><span data-stu-id="ced34-1010">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="ced34-1011">此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。</span><span class="sxs-lookup"><span data-stu-id="ced34-1011">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="ced34-1012">[下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：</span><span class="sxs-lookup"><span data-stu-id="ced34-1012">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="ced34-1013">在此教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。</span><span class="sxs-lookup"><span data-stu-id="ced34-1013">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="ced34-1014">然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-1014">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="ced34-1015">某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。</span><span class="sxs-lookup"><span data-stu-id="ced34-1015">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="ced34-1016">`MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。</span><span class="sxs-lookup"><span data-stu-id="ced34-1016">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="ced34-1017">某些開發人員偏好單獨使用 Fluent API，使其實體類別保持「整潔」。</span><span class="sxs-lookup"><span data-stu-id="ced34-1017">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="ced34-1018">屬性和 Fluent API 可混合使用。</span><span class="sxs-lookup"><span data-stu-id="ced34-1018">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="ced34-1019">有一些設定只能透過 Fluent API 完成 (指定複合 PK)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1019">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="ced34-1020">有一些設定只能透過屬性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1020">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="ced34-1021">使用 Fluent API 或屬性的建議做法為：</span><span class="sxs-lookup"><span data-stu-id="ced34-1021">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="ced34-1022">從這兩種方法中選擇一項。</span><span class="sxs-lookup"><span data-stu-id="ced34-1022">Choose one of these two approaches.</span></span>
* <span data-ttu-id="ced34-1023">持續且盡量使用您選擇的方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-1023">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="ced34-1024">此教學課程中使用到的某些屬性主要用於：</span><span class="sxs-lookup"><span data-stu-id="ced34-1024">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="ced34-1025">僅驗證 (例如，`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1025">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="ced34-1026">僅 EF Core 組態 (例如，`HasKey`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1026">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="ced34-1027">驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1027">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="ced34-1028">如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1028">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="ced34-1029">顯示關聯性的實體圖表</span><span class="sxs-lookup"><span data-stu-id="ced34-1029">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="ced34-1030">下圖顯示了 EF Power Tools 為完成的 School 模型建立的圖表。</span><span class="sxs-lookup"><span data-stu-id="ced34-1030">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

<span data-ttu-id="ced34-1032">上圖顯示︰</span><span class="sxs-lookup"><span data-stu-id="ced34-1032">The preceding diagram shows:</span></span>

* <span data-ttu-id="ced34-1033">數個一對多關聯性線條 (1 對 \*)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1033">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="ced34-1034">`Instructor` 和 `OfficeAssignment` 實體之間的一對零或一關聯性線條 (1 對 0..1)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1034">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="ced34-1035">`Instructor` 和 `Department` 實體之間的零或一對多關聯性線條 (0..1 對 \*)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1035">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="ced34-1036">使用測試資料植入資料庫</span><span class="sxs-lookup"><span data-stu-id="ced34-1036">Seed the DB with Test Data</span></span>

<span data-ttu-id="ced34-1037">更新 *Data/DbInitializer.cs* 中的程式碼：</span><span class="sxs-lookup"><span data-stu-id="ced34-1037">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="ced34-1038">上述程式碼為新的實體提供了種子資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-1038">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="ced34-1039">此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-1039">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="ced34-1040">範例資料主要用於測試。</span><span class="sxs-lookup"><span data-stu-id="ced34-1040">The sample data is used for testing.</span></span> <span data-ttu-id="ced34-1041">如需如何植入多對多聯結資料表的範例，請參閱 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="ced34-1041">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="ced34-1042">新增移轉</span><span class="sxs-lookup"><span data-stu-id="ced34-1042">Add a migration</span></span>

<span data-ttu-id="ced34-1043">建置專案。</span><span class="sxs-lookup"><span data-stu-id="ced34-1043">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-1044">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-1044">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-1045">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-1045">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="ced34-1046">上述命令會顯示關於可能發生資料遺失的警告。</span><span class="sxs-lookup"><span data-stu-id="ced34-1046">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="ced34-1047">若執行 `database update` 命令，便會產生下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="ced34-1047">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="ced34-1048">套用移轉</span><span class="sxs-lookup"><span data-stu-id="ced34-1048">Apply the migration</span></span>

<span data-ttu-id="ced34-1049">現在您有了現有的資料庫，您需要思考如何對其套用未來變更。</span><span class="sxs-lookup"><span data-stu-id="ced34-1049">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="ced34-1050">本教學課程示範兩種方法：</span><span class="sxs-lookup"><span data-stu-id="ced34-1050">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="ced34-1051">捨棄並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="ced34-1051">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="ced34-1052">[將遷移套用至現有的資料庫](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="ced34-1052">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="ced34-1053">雖然這個方法更複雜且耗時，卻是實際生產環境的慣用方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-1053">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="ced34-1054">**請注意**：這是本教學課程的選擇性章節。</span><span class="sxs-lookup"><span data-stu-id="ced34-1054">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="ced34-1055">您可以執行卸除並重新建立步驟，然後略過本節。</span><span class="sxs-lookup"><span data-stu-id="ced34-1055">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="ced34-1056">如果您希望遵循本章節中的步驟，請不要執行卸除並重新建立的步驟。</span><span class="sxs-lookup"><span data-stu-id="ced34-1056">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="ced34-1057">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="ced34-1057">Drop and re-create the database</span></span>

<span data-ttu-id="ced34-1058">更新的 `DbInitializer` 中的程式碼會為新的實體新增種子資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-1058">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="ced34-1059">若要強制 EF Core 建立新的資料庫，請卸除並更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-1059">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ced34-1060">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ced34-1060">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ced34-1061">在 [套件管理員主控台] (PMC) 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-1061">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="ced34-1062">從 PMC 執行 `Get-Help about_EntityFrameworkCore` 以取得說明資訊。</span><span class="sxs-lookup"><span data-stu-id="ced34-1062">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ced34-1063">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ced34-1063">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ced34-1064">開啟命令視窗並巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="ced34-1064">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="ced34-1065">專案資料夾中包含 *Startup.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="ced34-1065">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="ced34-1066">在命令視窗中輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="ced34-1066">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="ced34-1067">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ced34-1067">Run the app.</span></span> <span data-ttu-id="ced34-1068">執行應用程式會執行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="ced34-1068">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="ced34-1069">`DbInitializer.Initialize` 會填入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ced34-1069">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="ced34-1070">在 SSOX 中開啟資料庫：</span><span class="sxs-lookup"><span data-stu-id="ced34-1070">Open the DB in SSOX:</span></span>

* <span data-ttu-id="ced34-1071">若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="ced34-1071">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="ced34-1072">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="ced34-1072">Expand the **Tables** node.</span></span> <span data-ttu-id="ced34-1073">建立的資料表便會顯示。</span><span class="sxs-lookup"><span data-stu-id="ced34-1073">The created tables are displayed.</span></span>

![SSOX 中的資料表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="ced34-1075">檢查 **CourseAssignment** 資料表：</span><span class="sxs-lookup"><span data-stu-id="ced34-1075">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="ced34-1076">以滑鼠右鍵按一下 **CourseAssignment** 資料表，然後選取 [檢視資料]。</span><span class="sxs-lookup"><span data-stu-id="ced34-1076">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="ced34-1077">驗證 **CourseAssignment** 資料表中是否包含資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-1077">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX 中的 CourseAssignment 資料](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="ced34-1079">將移轉套用至現有資料庫</span><span class="sxs-lookup"><span data-stu-id="ced34-1079">Apply the migration to the existing database</span></span>

<span data-ttu-id="ced34-1080">此為選擇性區段。</span><span class="sxs-lookup"><span data-stu-id="ced34-1080">This section is optional.</span></span> <span data-ttu-id="ced34-1081">只有當您略過先前[卸除並重新建立資料庫](#drop)一節，這些步驟才有效。</span><span class="sxs-lookup"><span data-stu-id="ced34-1081">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="ced34-1082">當使用現有的資料執行移轉作業時，某些 FK 條件約束可能會無法透過現有資料滿足。</span><span class="sxs-lookup"><span data-stu-id="ced34-1082">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="ced34-1083">當您使用的是生產資料時，您必須進行幾個步驟才能移轉現有資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-1083">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="ced34-1084">本節提供了修正 FK 條件約束違規的範例。</span><span class="sxs-lookup"><span data-stu-id="ced34-1084">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="ced34-1085">請不要在沒有備份的情況下進行這些程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="ced34-1085">Don't make these code changes without a backup.</span></span> <span data-ttu-id="ced34-1086">若您已完成了先前的章節並已更新資料庫，請不要進行這些程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="ced34-1086">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="ced34-1087">*{timestamp}_ComplexDataModel.cs* 檔案包含了下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="ced34-1087">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="ced34-1088">上述程式碼將一個不可為 Null 的 `DepartmentID` FK 新增至 `Course` 資料表。</span><span class="sxs-lookup"><span data-stu-id="ced34-1088">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="ced34-1089">先前教學課程中的資料庫在 `Course` 中包含了資料列，導致資料表無法藉由移轉進行更新。</span><span class="sxs-lookup"><span data-stu-id="ced34-1089">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="ced34-1090">若要讓 `ComplexDataModel` 與現有資料進行移轉：</span><span class="sxs-lookup"><span data-stu-id="ced34-1090">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="ced34-1091">變更程式碼，以給予新資料行 (`DepartmentID`) 一個新的預設值。</span><span class="sxs-lookup"><span data-stu-id="ced34-1091">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="ced34-1092">建立一個名為 "Temp" 的假部門以作為預設部門之用。</span><span class="sxs-lookup"><span data-stu-id="ced34-1092">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="ced34-1093">修正外部索引鍵條件約束</span><span class="sxs-lookup"><span data-stu-id="ced34-1093">Fix the foreign key constraints</span></span>

<span data-ttu-id="ced34-1094">更新 `ComplexDataModel` 類別 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="ced34-1094">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="ced34-1095">開啟 *{timestamp}_ComplexDataModel.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="ced34-1095">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="ced34-1096">將新增 `DepartmentID` 資料行至 `Course` 資料表的程式碼全部標為註解。</span><span class="sxs-lookup"><span data-stu-id="ced34-1096">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="ced34-1097">新增下列醒目提示程式碼。</span><span class="sxs-lookup"><span data-stu-id="ced34-1097">Add the following highlighted code.</span></span> <span data-ttu-id="ced34-1098">新的程式碼位於 `.CreateTable( name: "Department"` 區塊後方：</span><span class="sxs-lookup"><span data-stu-id="ced34-1098">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="ced34-1099">透過上述變更，現有的 `Course` 資料列將會在執行 `ComplexDataModel` `Up` 方法後與 "Temp" 部門相關。</span><span class="sxs-lookup"><span data-stu-id="ced34-1099">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="ced34-1100">生產環境的應用程式會：</span><span class="sxs-lookup"><span data-stu-id="ced34-1100">A production app would:</span></span>

* <span data-ttu-id="ced34-1101">包含將 `Department` 資料列及相關 `Course` 資料列新增到新 `Department` 資料列的程式碼或指令碼。</span><span class="sxs-lookup"><span data-stu-id="ced34-1101">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="ced34-1102">不使用 "Temp" 部門或 `Course.DepartmentID` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="ced34-1102">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="ced34-1103">下一個教學課程會涵蓋相關資料。</span><span class="sxs-lookup"><span data-stu-id="ced34-1103">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ced34-1104">其他資源</span><span class="sxs-lookup"><span data-stu-id="ced34-1104">Additional resources</span></span>

* [<span data-ttu-id="ced34-1105">這個教學課程的 YouTube 版本 (第 1 部分)</span><span class="sxs-lookup"><span data-stu-id="ced34-1105">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="ced34-1106">這個教學課程的 YouTube 版本 (第 2 部分)</span><span class="sxs-lookup"><span data-stu-id="ced34-1106">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="ced34-1107">[上一個](xref:data/ef-rp/migrations) 
> [下一步](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="ced34-1107">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end