---
title: 教學課程：建立複雜的資料模型-使用 EF Core ASP.NET MVC
description: 在本教學課程中，請新增更多實體和關聯性，並透過指定格式、驗證和對應規則來自訂資料模型。
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: a881ff28d41a272ade559c60efbd884f2a3c4e3e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587784"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="b5892-103">教學課程：建立複雜的資料模型-使用 EF Core ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="b5892-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="b5892-104">在先前的教學課程中，您建立了由三個實體組成的簡單資料模型。</span><span class="sxs-lookup"><span data-stu-id="b5892-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="b5892-105">在本教學課程中，您會新增更多實體和關聯性，並透過指定格式、驗證和資料庫對應規則來自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="b5892-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="b5892-106">當您完成時，實體類別會構成如下列圖例中所顯示的完整資料模型：</span><span class="sxs-lookup"><span data-stu-id="b5892-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

<span data-ttu-id="b5892-108">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="b5892-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b5892-109">自訂資料模型</span><span class="sxs-lookup"><span data-stu-id="b5892-109">Customize the Data model</span></span>
> * <span data-ttu-id="b5892-110">對 Student 實體進行變更</span><span class="sxs-lookup"><span data-stu-id="b5892-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="b5892-111">建立 Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-111">Create Instructor entity</span></span>
> * <span data-ttu-id="b5892-112">建立 OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="b5892-113">修改 Course 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-113">Modify Course entity</span></span>
> * <span data-ttu-id="b5892-114">建立 Department 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-114">Create Department entity</span></span>
> * <span data-ttu-id="b5892-115">修改 Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="b5892-116">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b5892-116">Update the database context</span></span>
> * <span data-ttu-id="b5892-117">將測試資料植入資料庫</span><span class="sxs-lookup"><span data-stu-id="b5892-117">Seed database with test data</span></span>
> * <span data-ttu-id="b5892-118">新增移轉</span><span class="sxs-lookup"><span data-stu-id="b5892-118">Add a migration</span></span>
> * <span data-ttu-id="b5892-119">變更連接字串</span><span class="sxs-lookup"><span data-stu-id="b5892-119">Change the connection string</span></span>
> * <span data-ttu-id="b5892-120">更新資料庫</span><span class="sxs-lookup"><span data-stu-id="b5892-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5892-121">必要條件</span><span class="sxs-lookup"><span data-stu-id="b5892-121">Prerequisites</span></span>

* [<span data-ttu-id="b5892-122">使用 EF Core 移轉</span><span class="sxs-lookup"><span data-stu-id="b5892-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="b5892-123">自訂資料模型</span><span class="sxs-lookup"><span data-stu-id="b5892-123">Customize the Data model</span></span>

<span data-ttu-id="b5892-124">在本節中，您會了解到如何使用指定格式、驗證和資料庫對應規則的屬性來自訂資料模型。</span><span class="sxs-lookup"><span data-stu-id="b5892-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="b5892-125">然後在下列幾個章節中，您會透過將屬性新增到您已建立的類別，以及為模型中剩餘的實體類型建立新的類別，來建立完整的 School 資料模型。</span><span class="sxs-lookup"><span data-stu-id="b5892-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="b5892-126">DataType 屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-126">The DataType attribute</span></span>

<span data-ttu-id="b5892-127">針對學生的註冊日期，所有網頁目前都會同時顯示時間和日期，即使您針對此欄位只需要日期而已。</span><span class="sxs-lookup"><span data-stu-id="b5892-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="b5892-128">使用資料註解屬性，您可以透過僅對一個程式碼進行變更，來修正每個顯示資料的檢視上的顯示格式。</span><span class="sxs-lookup"><span data-stu-id="b5892-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="b5892-129">為了查看如何進行此操作的範例，您將會新增一個屬性至 `Student` 類別中的 `EnrollmentDate` 屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="b5892-130">在 *Models/Student.cs* 中，為 `System.ComponentModel.DataAnnotations` 命名空間新增一個 `using` 陳述式，然後將 `DataType` 和 `DisplayFormat` 屬性新增到 `EnrollmentDate` 屬性，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="b5892-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="b5892-131">`DataType` 屬性可用於指定比資料庫內建類型更特定的資料類型。</span><span class="sxs-lookup"><span data-stu-id="b5892-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="b5892-132">在此案例中，我們只想要追蹤日期，而非日期和時間。</span><span class="sxs-lookup"><span data-stu-id="b5892-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="b5892-133">`DataType` 列舉提供了許多資料類型，例如　Date、Time、PhoneNumber、Currency、EmailAddress 等。</span><span class="sxs-lookup"><span data-stu-id="b5892-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="b5892-134">`DataType` 屬性也可讓應用程式自動提供類型的特定功能。</span><span class="sxs-lookup"><span data-stu-id="b5892-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="b5892-135">例如，可建立 `DataType.EmailAddress` 的 `mailto:` 連結，而且可以在支援 HTML5 的瀏覽器中提供 `DataType.Date` 的日期選擇器。</span><span class="sxs-lookup"><span data-stu-id="b5892-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="b5892-136">`DataType` 屬性會發出 HTML 5 瀏覽器了解的 HTML 5 `data-` (讀音為 data dash) 屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="b5892-137">`DataType` 屬性不會提供任何驗證。</span><span class="sxs-lookup"><span data-stu-id="b5892-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="b5892-138">`DataType.Date` 未指定顯示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="b5892-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="b5892-139">根據預設，資料欄位會依照根據伺服器的 CultureInfo 為基礎的預設格式顯示。</span><span class="sxs-lookup"><span data-stu-id="b5892-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="b5892-140">`DisplayFormat` 屬性用來明確指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="b5892-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="b5892-141">`ApplyFormatInEditMode` 設定可指定在文字方塊中顯示值以供編輯時，也應該套用的格式。</span><span class="sxs-lookup"><span data-stu-id="b5892-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="b5892-142">(您也許會不想讓它出現在某些欄位中 - 例如貨幣值，您可能會不希望在進行編輯的文字方塊中出現貨幣符號。)</span><span class="sxs-lookup"><span data-stu-id="b5892-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="b5892-143">您可單獨使用 `DisplayFormat` 屬性，但通常最好是也使用 `DataType` 屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="b5892-144">`DataType` 屬性會傳逹資料的語意，而不是在螢幕上呈現資料的方式，並提供下列使用 `DisplayFormat` 無法取得的優點：</span><span class="sxs-lookup"><span data-stu-id="b5892-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="b5892-145">瀏覽器可以啟用 HTML5 功能 (例如顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結、一些用戶端輸入驗證等等)。</span><span class="sxs-lookup"><span data-stu-id="b5892-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="b5892-146">根據預設，瀏覽器將根據您的地區設定，使用正確的格式呈現資料。</span><span class="sxs-lookup"><span data-stu-id="b5892-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="b5892-147">如需詳細資訊，請參閱[ \<input> tag helper 檔](../../mvc/views/working-with-forms.md#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="b5892-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="b5892-148">執行應用程式，移至 Students [索引] 頁面，您會注意到註冊日期欄位中不再顯示時間。</span><span class="sxs-lookup"><span data-stu-id="b5892-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="b5892-149">任何使用 Student 模型的檢視都會有相同的結果。</span><span class="sxs-lookup"><span data-stu-id="b5892-149">The same will be true for any view that uses the Student model.</span></span>

![顯示日期而不顯示時間的 Students [索引] 頁面](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="b5892-151">StringLength 屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-151">The StringLength attribute</span></span>

<span data-ttu-id="b5892-152">您也可以使用屬性指定資料驗證規則和驗證錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b5892-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="b5892-153">`StringLength` 屬性會設定資料庫中的最大長度，並為 ASP.NET Core MVC 提供用戶端和伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="b5892-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="b5892-154">您也可以在此屬性中指定最小字串長度，但最小值不會對資料庫結構描述造成任何影響。</span><span class="sxs-lookup"><span data-stu-id="b5892-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="b5892-155">假設您想要確保使用者不會在名稱中輸入超過 50 個字元。</span><span class="sxs-lookup"><span data-stu-id="b5892-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="b5892-156">若要新增這項限制，請將 `StringLength` 屬性新增到 `LastName` 及 `FirstMidName` 屬性，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="b5892-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="b5892-157">`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。</span><span class="sxs-lookup"><span data-stu-id="b5892-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="b5892-158">您可以使用 `RegularExpression` 屬性來將限制套用至輸入。</span><span class="sxs-lookup"><span data-stu-id="b5892-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="b5892-159">例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：</span><span class="sxs-lookup"><span data-stu-id="b5892-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="b5892-160">`MaxLength` 屬性提供了與 `StringLength` 屬性類似的功能，但不會提供用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="b5892-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="b5892-161">資料庫模型現已變更，需要變更資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="b5892-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="b5892-162">您會使用移轉來更新結構描述，而不會遺失任何您可能已透過應用程式 UI 新增到資料庫中的資料。</span><span class="sxs-lookup"><span data-stu-id="b5892-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="b5892-163">儲存您的變更，並建置專案。</span><span class="sxs-lookup"><span data-stu-id="b5892-163">Save your changes and build the project.</span></span> <span data-ttu-id="b5892-164">然後，在專案資料夾中開啟命令視窗，並輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="b5892-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="b5892-165">`migrations add` 命令會警告可能發生資料遺失，因為該項變更縮短了兩個資料行的最大長度。</span><span class="sxs-lookup"><span data-stu-id="b5892-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="b5892-166">遷移會建立名為 *\<timeStamp> _MaxLengthOnNames .cs* 的檔案。</span><span class="sxs-lookup"><span data-stu-id="b5892-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="b5892-167">此檔案包含了 `Up` 方法中的程式碼，可更新資料庫，使其符合目前的資料模型。</span><span class="sxs-lookup"><span data-stu-id="b5892-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="b5892-168">`database update` 命令執行了該程式碼。</span><span class="sxs-lookup"><span data-stu-id="b5892-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="b5892-169">Entity Framework 會使用移轉檔案名稱前置的時間戳記來排序移轉。</span><span class="sxs-lookup"><span data-stu-id="b5892-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="b5892-170">您可以在執行 update-database 命令前建立多個移轉，然後所有的移轉便會依照其建立的先後順序套用。</span><span class="sxs-lookup"><span data-stu-id="b5892-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="b5892-171">請執行應用程式、選取 [Students] 索引標籤、按一下 [建立新項目]，然後嘗試輸入長度超過 50 個字元的名稱。</span><span class="sxs-lookup"><span data-stu-id="b5892-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="b5892-172">應用程式應該會防止您這麼做。</span><span class="sxs-lookup"><span data-stu-id="b5892-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="b5892-173">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-173">The Column attribute</span></span>

<span data-ttu-id="b5892-174">您也可以使用屬性控制您的類別和屬性對應到資料庫的方式。</span><span class="sxs-lookup"><span data-stu-id="b5892-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="b5892-175">假設您已針對名字欄位使用 `FirstMidName` 作為名稱，因為欄位中可能也會包含中間名。</span><span class="sxs-lookup"><span data-stu-id="b5892-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="b5892-176">但您想要將資料庫資料行命名為 `FirstName`，因為撰寫臨機操作查詢資料庫的使用者比較習慣該名稱。</span><span class="sxs-lookup"><span data-stu-id="b5892-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="b5892-177">若要進行此對應，您可以使用 `Column` 屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="b5892-178">`Column` 屬性指定當建立資料庫時，`Student` 資料表中對應到 `FirstMidName` 屬性的資料行會命名為 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="b5892-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="b5892-179">換句話說，當您的程式碼參照 `Student.FirstMidName` 時，資料便會來自 `Student` 資料表中的 `FirstName` 資料行或在其中更新。</span><span class="sxs-lookup"><span data-stu-id="b5892-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="b5892-180">若您沒有指定資料行名稱，則它們便會命名為與屬性名稱相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="b5892-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="b5892-181">在 *Student.cs* 檔案中，為 `System.ComponentModel.DataAnnotations.Schema` 新增一個 `using` 陳述式，然後將資料行名稱屬性新增到 `FirstMidName` 屬性，如下列醒目提示程式碼中所示：</span><span class="sxs-lookup"><span data-stu-id="b5892-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="b5892-182">新增 `Column` 屬性會變更支援 `SchoolContext` 的模型，因此它將不會符合資料庫。</span><span class="sxs-lookup"><span data-stu-id="b5892-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="b5892-183">儲存您的變更，並建置專案。</span><span class="sxs-lookup"><span data-stu-id="b5892-183">Save your changes and build the project.</span></span> <span data-ttu-id="b5892-184">然後，在專案資料夾中開啟命令視窗，並輸入下列命令以建立另一個移轉：</span><span class="sxs-lookup"><span data-stu-id="b5892-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="b5892-185">在 [SQL Server 物件總管] 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。</span><span class="sxs-lookup"><span data-stu-id="b5892-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="b5892-187">在您套用前兩個移轉前，名稱資料行的類型為 nvarchar(MAX)。</span><span class="sxs-lookup"><span data-stu-id="b5892-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="b5892-188">它們現在已是 nvarchar (50)，並且資料行名稱也已從 FirstMidName 變更為 FirstName。</span><span class="sxs-lookup"><span data-stu-id="b5892-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="b5892-189">若您嘗試在完成建立下列章節中所有的實體類別前進行編譯，您可能會收到編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="b5892-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="b5892-190">對 Student 實體進行變更</span><span class="sxs-lookup"><span data-stu-id="b5892-190">Changes to Student entity</span></span>

![Student 實體](complex-data-model/_static/student-entity.png)

<span data-ttu-id="b5892-192">在 *Models/Student.cs* 中，以下列程式碼取代您在先前新增的程式碼。</span><span class="sxs-lookup"><span data-stu-id="b5892-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="b5892-193">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="b5892-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="b5892-194">Required 屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-194">The Required attribute</span></span>

<span data-ttu-id="b5892-195">`Required` 屬性會讓名稱屬性成為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="b5892-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="b5892-196">針對不可為 Null 的類型，例如實值型別 (DateTime、int、double、float 等) 等，`Required` 屬性是不需要的。</span><span class="sxs-lookup"><span data-stu-id="b5892-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="b5892-197">不可為 Null 的類型會自動視為必要欄位。</span><span class="sxs-lookup"><span data-stu-id="b5892-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="b5892-198">`Required` 屬性必須搭配 `MinimumLength` 使用，才能強制執行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="b5892-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="b5892-199">Display 屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-199">The Display attribute</span></span>

<span data-ttu-id="b5892-200">`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」，而非每個執行個體中的屬性名稱 (沒有使用空格鍵分隔單字的名稱)。</span><span class="sxs-lookup"><span data-stu-id="b5892-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="b5892-201">FullName 計算屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-201">The FullName calculated property</span></span>

<span data-ttu-id="b5892-202">`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。</span><span class="sxs-lookup"><span data-stu-id="b5892-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="b5892-203">因此其僅具有 get 存取子，且資料庫中不會產生 `FullName` 資料行。</span><span class="sxs-lookup"><span data-stu-id="b5892-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="b5892-204">建立 Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-204">Create Instructor entity</span></span>

![Instructor 實體](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="b5892-206">建立 *Models/Instructor.cs* 並以下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="b5892-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="b5892-207">請注意，Student 和 Instructor 實體中有幾個屬性是一樣的。</span><span class="sxs-lookup"><span data-stu-id="b5892-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="b5892-208">在本系列稍後的[實作繼承](inheritance.md)教學課程中，您會對此程式碼進行重構以消除冗餘。</span><span class="sxs-lookup"><span data-stu-id="b5892-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="b5892-209">您也可以將多個屬性放在同一行上，並以下列方式撰寫 `HireDate` 屬性：</span><span class="sxs-lookup"><span data-stu-id="b5892-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="b5892-210">CourseAssignments 和 OfficeAssignment 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="b5892-211">`CourseAssignments` 和 `OfficeAssignment` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="b5892-212">由於講師可以教授任何數量的課程，因此 `CourseAssignments` 已定義為一個集合。</span><span class="sxs-lookup"><span data-stu-id="b5892-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="b5892-213">若導覽屬性可保有多個實體，其類型必須為一個清單，使得實體可以在該清單中新增、刪除或更新。</span><span class="sxs-lookup"><span data-stu-id="b5892-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="b5892-214">您可以指定 `ICollection<T>` 或如 `List<T>` 或 `HashSet<T>` 等類型。</span><span class="sxs-lookup"><span data-stu-id="b5892-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="b5892-215">若您指定了 `ICollection<T>`，EF 會根據預設建立一個 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="b5892-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="b5892-216">這些之所以為 `CourseAssignment` 實體的原因會在此節下方關於多對多關聯性中解釋。</span><span class="sxs-lookup"><span data-stu-id="b5892-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="b5892-217">Contoso 大學的商務規則要求講師最多只能擁有一間辦公室，因此 `OfficeAssignment` 屬性會保有單一 OfficeAssignment 實體 (若沒有指派任何辦公室，則其可為 Null)。</span><span class="sxs-lookup"><span data-stu-id="b5892-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="b5892-218">建立 OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-218">Create OfficeAssignment entity</span></span>

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="b5892-220">使用下列程式碼建立 *Models/OfficeAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="b5892-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="b5892-221">Key 屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-221">The Key attribute</span></span>

<span data-ttu-id="b5892-222">在 Instructor 和 OfficeAssignment 實體之間存在一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="b5892-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="b5892-223">辦公室指派只有在有指派的講師時才會存在，因此其主索引鍵也是其繫結到 Instructor 實體的外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b5892-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="b5892-224">但 Entity Framework 無法自動將 InstructorID 識別為此實體的主索引鍵，因為其名稱並未遵循 ID 或 classnameID 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="b5892-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="b5892-225">因此，必須使用 `Key` 屬性將其識別為 PK：</span><span class="sxs-lookup"><span data-stu-id="b5892-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="b5892-226">若實體確實有其自身的主索引鍵，但您想要將該屬性命名為其他名稱，而非 classnameID 或 ID，則您也可以使用 `Key` 屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="b5892-227">根據預設，EF 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。</span><span class="sxs-lookup"><span data-stu-id="b5892-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="b5892-228">Instructor 導覽屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-228">The Instructor navigation property</span></span>

<span data-ttu-id="b5892-229">Instructor 實體具有一個可為 Null 的 `OfficeAssignment` 導覽屬性 (因為一名講師可能會沒有任何辦公室指派)，而 OfficeAssignment 實體則有一個不可為 Null 的 `Instructor` 導覽屬性 (因為辦公室指派不可獨立於講師之外存在 - `InstructorID` 不可為 Null)。</span><span class="sxs-lookup"><span data-stu-id="b5892-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="b5892-230">當 Instructor 實體有相關的 OfficeAssignment 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。</span><span class="sxs-lookup"><span data-stu-id="b5892-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="b5892-231">您可以將 `[Required]` 屬性置於 Instructor 導覽屬性上，以指定其必須要有一個相關的講師，但您不需要這麼做，因為 `InstructorID` 外部索引鍵 (同時也是此資料表的索引鍵) 不可為 Null。</span><span class="sxs-lookup"><span data-stu-id="b5892-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="b5892-232">修改 Course 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-232">Modify Course entity</span></span>

![Course 實體](complex-data-model/_static/course-entity.png)

<span data-ttu-id="b5892-234">在 *Models/Course.cs* 中，以下列程式碼取代您在先前新增的程式碼。</span><span class="sxs-lookup"><span data-stu-id="b5892-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="b5892-235">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="b5892-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="b5892-236">課程實體有一個外部索引鍵屬性 (`DepartmentID`)，該索引鍵指向了相關的 Department 實體，並且其擁有一個 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="b5892-237">當您針對相關實體具有一個導覽屬性時，Entity Framework 便不需要您為資料模型新增一個外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="b5892-238">每當需要的時候，EF 便會自動在資料庫中建立外部索引鍵，並為他們建立[陰影屬性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="b5892-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="b5892-239">但在資料模型中擁有外部索引鍵，可讓更新變得更為簡單和有效率。</span><span class="sxs-lookup"><span data-stu-id="b5892-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="b5892-240">例如，當您擷取了一個要編輯的課程實體，若您沒有載入它，Department 實體便會為 Null，因此當您要更新課程實體時，您必須先擷取 Department 實體。</span><span class="sxs-lookup"><span data-stu-id="b5892-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="b5892-241">當外部索引鍵屬性 `DepartmentID` 包含在資料模型中時，您便不需要在更新前擷取 Department 實體。</span><span class="sxs-lookup"><span data-stu-id="b5892-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="b5892-242">DatabaseGenerated 屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="b5892-243">`DatabaseGenerated` 屬性和 `CourseID` 屬性上的 `None` 參數指定主索引鍵由使用者提供，而非由資料庫產生。</span><span class="sxs-lookup"><span data-stu-id="b5892-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="b5892-244">根據預設，Entity Framework 會假設主索引鍵的值是由資料庫產生。</span><span class="sxs-lookup"><span data-stu-id="b5892-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="b5892-245">這是您在大多數案例下所希望的情況。</span><span class="sxs-lookup"><span data-stu-id="b5892-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="b5892-246">然而，針對 Course 實體，您會使用使用者定義的課程號碼，例如讓一個部門使用 1000 系列，另一個部門則使用 2000 系列等等。</span><span class="sxs-lookup"><span data-stu-id="b5892-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="b5892-247">如果是用於記錄資料列建立或更新的資料庫資料行，則 `DatabaseGenerated` 屬性也能用於產生預設值。</span><span class="sxs-lookup"><span data-stu-id="b5892-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="b5892-248">如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="b5892-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b5892-249">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="b5892-250">Course 實體中的外部索引鍵屬性和導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="b5892-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="b5892-251">課程會指派給一個部門，因此基於上述理由，會有一個 `DepartmentID` 外部索引鍵和一個 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="b5892-252">由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：</span><span class="sxs-lookup"><span data-stu-id="b5892-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="b5892-253">課程可由多個講師進行教授，因此 `CourseAssignments` 導覽屬性為一個集合 (`CourseAssignment` 類型會在[稍後](#many-to-many-relationships)說明)：</span><span class="sxs-lookup"><span data-stu-id="b5892-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="b5892-254">建立 Department 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-254">Create Department entity</span></span>

![部門實體](complex-data-model/_static/department-entity.png)

<span data-ttu-id="b5892-256">使用下列程式碼建立 *Models/Department.cs*：</span><span class="sxs-lookup"><span data-stu-id="b5892-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="b5892-257">Column 屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-257">The Column attribute</span></span>

<span data-ttu-id="b5892-258">先前您使用了 `Column` 屬性來變更資料行的名稱對應。</span><span class="sxs-lookup"><span data-stu-id="b5892-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="b5892-259">在 Department 實體的程式碼中，`Column` 屬性則會用於變更 SQL 資料類型對應，讓資料行在資料庫中會使用 SQL Server 的貨幣類型進行定義：</span><span class="sxs-lookup"><span data-stu-id="b5892-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="b5892-260">資料行對應通常都是不必要的，因為 Entity Framework 會根據您為該屬性定義的 CLR 類型選擇適合的 SQL Server 資料類型。</span><span class="sxs-lookup"><span data-stu-id="b5892-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="b5892-261">CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。</span><span class="sxs-lookup"><span data-stu-id="b5892-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="b5892-262">但在此案例下，您知道資料行會儲存貨幣數量，而貨幣資料類型是最適合的。</span><span class="sxs-lookup"><span data-stu-id="b5892-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b5892-263">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="b5892-264">外部索引鍵及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="b5892-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="b5892-265">部門可以有或沒有一位系統管理員，而系統管理員一律為講師。</span><span class="sxs-lookup"><span data-stu-id="b5892-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="b5892-266">因此 `InstructorID` 屬性會作為繫結到 Instructor 實體的外部索引鍵包含在其中，並且在 `int` 類型指定後會新增一個問號，標示該屬性可為 Null。</span><span class="sxs-lookup"><span data-stu-id="b5892-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="b5892-267">導覽屬性會命名為 `Administrator`，但其包含的內容為一個 Instructor 實體：</span><span class="sxs-lookup"><span data-stu-id="b5892-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="b5892-268">部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="b5892-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="b5892-269">根據慣例，Entity Framework 會為不可為 Null 的外部索引鍵和多對多關聯性啟用串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="b5892-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="b5892-270">這可能會導致循環串聯刪除規則，並在您嘗試新增移轉時造成例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b5892-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="b5892-271">例如，若您未將 Department.InstructorID 屬性定義成可為 Null，EF 就會設定串聯刪除規則，在您刪除講師時刪除部門，而這可能是您不願見到的情況。</span><span class="sxs-lookup"><span data-stu-id="b5892-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="b5892-272">若您的商務規則要求 `InstructorID` 屬性不可為 Null，則您將必須使用 Fluent API 陳述式來在關聯性上停用串聯刪除：</span><span class="sxs-lookup"><span data-stu-id="b5892-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="b5892-273">修改 Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-273">Modify Enrollment entity</span></span>

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="b5892-275">在 *Models/Enrollment.cs* 中，以下列程式碼取代您在先前新增的程式碼：</span><span class="sxs-lookup"><span data-stu-id="b5892-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="b5892-276">外部索引鍵及導覽屬性</span><span class="sxs-lookup"><span data-stu-id="b5892-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="b5892-277">外部索引鍵屬性及導覽屬性反映了下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="b5892-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="b5892-278">註冊記錄乃針對單一課程，因此當中包含了一個 `CourseID` 外部索引鍵屬性及一個 `Course` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="b5892-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="b5892-279">註冊記錄乃針對單一學生，因此當中包含了一個 `StudentID` 外部索引鍵屬性及一個 `Student` 導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="b5892-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="b5892-280">多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="b5892-280">Many-to-Many relationships</span></span>

<span data-ttu-id="b5892-281">Student 和 Course 實體之間存在一個多對多關聯性，且 Enrollment 實體的功能便是多對多聯結資料表，其在資料庫中帶有 *承載*。</span><span class="sxs-lookup"><span data-stu-id="b5892-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="b5892-282">「帶有承載」的意思是 Enrollment 資料表除了聯結資料表的外部索引鍵之外，還包含了額外的資料 (在此案例中為主索引鍵和 Grade 屬性)。</span><span class="sxs-lookup"><span data-stu-id="b5892-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="b5892-283">下列圖例展示了在實體圖表中這些關聯性的樣子。</span><span class="sxs-lookup"><span data-stu-id="b5892-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="b5892-284">(此圖表使用了 EF 6.x 的 Entity Framework Power Tools 產生。建立圖表不是此教學課程的一部分，其僅作為展示之用。)</span><span class="sxs-lookup"><span data-stu-id="b5892-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

<span data-ttu-id="b5892-286">每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (\*)」，顯示其為一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="b5892-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="b5892-287">若 Enrollment 資料表並未包含年級資訊，則其便只需要包含兩個外部索引鍵 (CourseID 和 StudentID)。</span><span class="sxs-lookup"><span data-stu-id="b5892-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="b5892-288">在此案例下，其在資料庫中將會是不具有承載的多對多聯結資料表 (或純聯結資料表)。</span><span class="sxs-lookup"><span data-stu-id="b5892-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="b5892-289">Instructor 和 Course 實體具有此類型的多對多關聯性，並且您的下一步驟便是建立一個實體類別作為不具有承載的聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="b5892-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="b5892-290">(EF 6.x 支援多對多關聯性的隱含聯結資料表，但 EF Core 並不支援。</span><span class="sxs-lookup"><span data-stu-id="b5892-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="b5892-291">如需詳細資訊，請參閱 [EF Core GitHub 存放庫中的討論](https://github.com/aspnet/EntityFramework/issues/1368)。)</span><span class="sxs-lookup"><span data-stu-id="b5892-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="b5892-292">CourseAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-292">The CourseAssignment entity</span></span>

![CourseAssignment 實體](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="b5892-294">使用下列程式碼建立 *Models/CourseAssignment.cs*：</span><span class="sxs-lookup"><span data-stu-id="b5892-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="b5892-295">聯結實體名稱</span><span class="sxs-lookup"><span data-stu-id="b5892-295">Join entity names</span></span>

<span data-ttu-id="b5892-296">針對「講師-課程」多對多關聯性，資料庫中必須要有一個聯結資料表，且其必須由一個實體集代表。</span><span class="sxs-lookup"><span data-stu-id="b5892-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="b5892-297">將聯結實體命名為 `EntityName1EntityName2` 的做法非常常見。在此案例中，即為 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="b5892-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="b5892-298">不過，我們建議您選擇可描述關聯性的名稱。</span><span class="sxs-lookup"><span data-stu-id="b5892-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="b5892-299">資料模型是從簡單開始慢慢成長的，並且常常一開始沒有承載的聯結在之後便會變成具有承載。</span><span class="sxs-lookup"><span data-stu-id="b5892-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="b5892-300">若您在一開始便使用了描述性的實體名稱，您在之後便不需要變更該名稱。</span><span class="sxs-lookup"><span data-stu-id="b5892-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="b5892-301">理想情況下，聯結實體在公司網域中會有自己的自然 (可能為一個單字) 名稱。</span><span class="sxs-lookup"><span data-stu-id="b5892-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="b5892-302">例如，「書籍」和「客戶」可透過「評分」進行連結。</span><span class="sxs-lookup"><span data-stu-id="b5892-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="b5892-303">針對此關聯性，相較於 `CourseInstructor`，`CourseAssignment` 是較佳的選擇。</span><span class="sxs-lookup"><span data-stu-id="b5892-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="b5892-304">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="b5892-304">Composite key</span></span>

<span data-ttu-id="b5892-305">由於外部索引鍵不可為 Null，並且當一起使用時便可唯一識別資料表的每一個資料列，因此您不需要擁有一個個別的主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b5892-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="b5892-306">*InstructorID* 和 *CourseID* 屬性可作為複合主索引鍵發揮功能。</span><span class="sxs-lookup"><span data-stu-id="b5892-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="b5892-307">針對 EF 識別複合主索引鍵的唯一方法便是使用 *Fluent API* (您無法使用屬性完成這項操作)。</span><span class="sxs-lookup"><span data-stu-id="b5892-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="b5892-308">您會在下節中了解如何設定複合主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b5892-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="b5892-309">複合索引鍵可確保當您針對一個課程擁有多個資料列，且針對一位講師擁有多個資料列時，您無法針對相同的講師和課程擁有多個資料列。</span><span class="sxs-lookup"><span data-stu-id="b5892-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="b5892-310">由於 `Enrollment` 聯結實體定義了其自身的主索引鍵，因此這種種類的重複項目是可能的。</span><span class="sxs-lookup"><span data-stu-id="b5892-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="b5892-311">若要避免這種重複的項目，您可以在外部索引鍵欄位上新增一個唯一的索引，或使用與 `CourseAssignment` 相似的主複合索引鍵來設定 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="b5892-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="b5892-312">如需詳細資訊，請參閱[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="b5892-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="b5892-313">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b5892-313">Update the database context</span></span>

<span data-ttu-id="b5892-314">將下列醒目提示的程式碼新增至 *Data/SchoolContext.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="b5892-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="b5892-315">此程式碼會新增一個新實體，並設定 CourseAssignment 實體的複合主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="b5892-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="b5892-316">關於 Fluent API 替代項目</span><span class="sxs-lookup"><span data-stu-id="b5892-316">About a fluent API alternative</span></span>

<span data-ttu-id="b5892-317">`DbContext` 類別的 `OnModelCreating` 方法中的程式碼使用了 *Fluent API* 來設定 EF 行為。</span><span class="sxs-lookup"><span data-stu-id="b5892-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="b5892-318">此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式，例如這個從 [EF Core 文件](/ef/core/modeling/#use-fluent-api-to-configure-a-model)取得的範例：</span><span class="sxs-lookup"><span data-stu-id="b5892-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="b5892-319">在本教學課程中，您僅會使用 Fluent API 作為無法使用屬性操作時的資料庫對應之用。</span><span class="sxs-lookup"><span data-stu-id="b5892-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="b5892-320">然而，您也可以使用 Fluent API 指定大部分透過屬性可完成的格式、驗證及對應規則。</span><span class="sxs-lookup"><span data-stu-id="b5892-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="b5892-321">某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。</span><span class="sxs-lookup"><span data-stu-id="b5892-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="b5892-322">如前文所述，`MinimumLength` 不會變更結構描述，其僅會套用用戶端及伺服器端驗證規則。</span><span class="sxs-lookup"><span data-stu-id="b5892-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="b5892-323">某些開發人員偏好單獨使用 Fluent API，使其實體類別保持「整潔」。</span><span class="sxs-lookup"><span data-stu-id="b5892-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="b5892-324">若想要的話，您也可以混合使用屬性和 Fluent API，並且有一些自訂項目只能使用 Fluent API 完成。但一般來說，建議的做法是在這兩種方法中選擇其中一項，然後盡可能的一致使用該方法。</span><span class="sxs-lookup"><span data-stu-id="b5892-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="b5892-325">若您確實同時使用了兩者，則請注意當發生衝突時，Fluent API 會覆寫屬性。</span><span class="sxs-lookup"><span data-stu-id="b5892-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="b5892-326">如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="b5892-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="b5892-327">顯示關聯性的實體圖表</span><span class="sxs-lookup"><span data-stu-id="b5892-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="b5892-328">下列圖例顯示了 Entity Framework Power Tools 為完成的 School 模型建立的圖表。</span><span class="sxs-lookup"><span data-stu-id="b5892-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![實體圖表](complex-data-model/_static/diagram.png)

<span data-ttu-id="b5892-330">除了一對多關聯性線條外 (1 到 \*)，您也可以在 Instructor 和 OfficeAssignment 實體間看到一對零或一關聯性線條 (1 到 0..1)，以及介於 Instructor 和 Department 實體間的零或一對多關聯性線條 (0..1 到 \*)。</span><span class="sxs-lookup"><span data-stu-id="b5892-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="b5892-331">將測試資料植入資料庫</span><span class="sxs-lookup"><span data-stu-id="b5892-331">Seed database with test data</span></span>

<span data-ttu-id="b5892-332">使用下列程式碼取代 *Data/DbInitializer.cs* 中的程式碼，以為您建立的新實體提供種子資料。</span><span class="sxs-lookup"><span data-stu-id="b5892-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="b5892-333">如同您在第一個課程中所看到的，此程式碼中大部分僅只是用於建立新實體物件，並針對測試需求載入範例資料。</span><span class="sxs-lookup"><span data-stu-id="b5892-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="b5892-334">請注意程式碼處理多對多關聯性的方式：程式碼會藉由在 `Enrollments` 和 `CourseAssignment` 聯結實體集中建立實體來建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="b5892-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="b5892-335">新增移轉</span><span class="sxs-lookup"><span data-stu-id="b5892-335">Add a migration</span></span>

<span data-ttu-id="b5892-336">儲存您的變更，並建置專案。</span><span class="sxs-lookup"><span data-stu-id="b5892-336">Save your changes and build the project.</span></span> <span data-ttu-id="b5892-337">然後在專案資料夾中開啟命令視窗，並輸入 `migrations add` 命令 (請還不要執行 update-database 命令)：</span><span class="sxs-lookup"><span data-stu-id="b5892-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="b5892-338">您會收到有關可能發生資料遺失的警告。</span><span class="sxs-lookup"><span data-stu-id="b5892-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="b5892-339">若您嘗試在這個時間點執行 `database update` 命令，您會接收到下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="b5892-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="b5892-340">ALTER TABLE 陳述式與 FOREIGN KEY 條件約束 "FK_dbo.Course_dbo.Department_DepartmentID" 發生衝突。</span><span class="sxs-lookup"><span data-stu-id="b5892-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="b5892-341">衝突發生在 "ContoseUniversity" 資料庫、"dbo.Department" 資料表、"DepartmentID" 資料行中。</span><span class="sxs-lookup"><span data-stu-id="b5892-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="b5892-342">有時候當您使用現有資料執行移轉時，您必須將 Stub 資料插入資料庫中以滿足外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="b5892-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="b5892-343">`Up` 方法中產生的程式碼會新增一個不可為 Null 的 DepartmentID 外部索引鍵至 Course 資料表。</span><span class="sxs-lookup"><span data-stu-id="b5892-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="b5892-344">若在程式碼執行時 Course 資料表中已有資料列，則 `AddColumn` 作業會失敗，因為 SQL Server 無法得知要在不可為 Null 的資料行中填入何值。</span><span class="sxs-lookup"><span data-stu-id="b5892-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="b5892-345">針對此教學課程，您會在一個新的資料庫中執行移轉，但在生產環境下的應用程式中，您必須讓移轉能夠處理現有的資料，因此下列指引顯示了如何進行處理的範例。</span><span class="sxs-lookup"><span data-stu-id="b5892-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="b5892-346">若要使用現有資料完成移轉，您必須變更程式碼，給予新的資料行預設值，然後建立名為 "Temp" 的 Stub 部門，以作為預設部門。</span><span class="sxs-lookup"><span data-stu-id="b5892-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="b5892-347">其結果為現有的 Course 資料列便會在執行 `Up` 方法後與 "Temp" 部門產生關聯。</span><span class="sxs-lookup"><span data-stu-id="b5892-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="b5892-348">開啟 *{timestamp}_ComplexDataModel.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="b5892-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="b5892-349">將新增 DepartmentID 資料行至 Course 資料表的程式碼全部標為註解。</span><span class="sxs-lookup"><span data-stu-id="b5892-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="b5892-350">在建立 Department 資料表的程式碼之後新增下列醒目提示程式碼：</span><span class="sxs-lookup"><span data-stu-id="b5892-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="b5892-351">在生產環境的應用程式中，您會撰寫程式碼或指令碼以新增 Department 資料列，並使 Course 資料列與新的 Department 資料列產生關聯。</span><span class="sxs-lookup"><span data-stu-id="b5892-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="b5892-352">屆時您便不再需要為 Course.DepartmentID 資料行設定 "Temp" 部門或預設值。</span><span class="sxs-lookup"><span data-stu-id="b5892-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="b5892-353">儲存您的變更，並建置專案。</span><span class="sxs-lookup"><span data-stu-id="b5892-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="b5892-354">變更連接字串</span><span class="sxs-lookup"><span data-stu-id="b5892-354">Change the connection string</span></span>

<span data-ttu-id="b5892-355">您現在在 `DbInitializer` 類別中已有了新的程式碼，可將新實體的種子資料新增至空白資料庫中。</span><span class="sxs-lookup"><span data-stu-id="b5892-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="b5892-356">若要讓 EF 建立新的空白資料庫，請將連接字串中的資料庫名稱變更 *appsettings.json* 為 ContosoUniversity3，或是您所使用的電腦上未使用的其他名稱。</span><span class="sxs-lookup"><span data-stu-id="b5892-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="b5892-357">將您的變更儲存至 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="b5892-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="b5892-358">如果您不想變更資料庫名稱，替代方法是刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="b5892-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="b5892-359">使用 [SQL Server 物件總管] (SSOX) 或 `database drop` CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="b5892-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="b5892-360">更新資料庫</span><span class="sxs-lookup"><span data-stu-id="b5892-360">Update the database</span></span>

<span data-ttu-id="b5892-361">在您變更資料庫名稱或刪除資料庫之後，在命令視窗中執行 `database update` 命令以執行移轉。</span><span class="sxs-lookup"><span data-stu-id="b5892-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="b5892-362">執行應用程式以執行 `DbInitializer.Initialize` 方法並填入新資料庫。</span><span class="sxs-lookup"><span data-stu-id="b5892-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="b5892-363">如同先前操作，在 SSOX 中開啟資料庫，展開 [資料表] 節點以查看所有已建立的資料表。</span><span class="sxs-lookup"><span data-stu-id="b5892-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="b5892-364">(若您先前開啟的 SSOX 還在，請按一下 [重新整理] 按鈕。)</span><span class="sxs-lookup"><span data-stu-id="b5892-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![SSOX 中的資料表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="b5892-366">執行應用程式以觸發植入資料庫的初始設定式程式碼。</span><span class="sxs-lookup"><span data-stu-id="b5892-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="b5892-367">以滑鼠右鍵按一下 **CourseAssignment** 資料表，然後選取 [檢視資料] 以驗證其中已有資料。</span><span class="sxs-lookup"><span data-stu-id="b5892-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![SSOX 中的 CourseAssignment 資料](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="b5892-369">取得程式碼</span><span class="sxs-lookup"><span data-stu-id="b5892-369">Get the code</span></span>

[<span data-ttu-id="b5892-370">下載或檢視已完成的應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5892-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="b5892-371">後續步驟</span><span class="sxs-lookup"><span data-stu-id="b5892-371">Next steps</span></span>

<span data-ttu-id="b5892-372">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="b5892-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b5892-373">自訂資料模型</span><span class="sxs-lookup"><span data-stu-id="b5892-373">Customized the Data model</span></span>
> * <span data-ttu-id="b5892-374">對 Student 實體進行變更</span><span class="sxs-lookup"><span data-stu-id="b5892-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="b5892-375">建立 Instructor 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-375">Created Instructor entity</span></span>
> * <span data-ttu-id="b5892-376">建立 OfficeAssignment 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="b5892-377">修改 Course 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-377">Modified Course entity</span></span>
> * <span data-ttu-id="b5892-378">建立 Department 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-378">Created Department entity</span></span>
> * <span data-ttu-id="b5892-379">修改 Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="b5892-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="b5892-380">更新資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b5892-380">Updated the database context</span></span>
> * <span data-ttu-id="b5892-381">將測試資料植入資料庫</span><span class="sxs-lookup"><span data-stu-id="b5892-381">Seeded database with test data</span></span>
> * <span data-ttu-id="b5892-382">新增移轉</span><span class="sxs-lookup"><span data-stu-id="b5892-382">Added a migration</span></span>
> * <span data-ttu-id="b5892-383">變更連接字串</span><span class="sxs-lookup"><span data-stu-id="b5892-383">Changed the connection string</span></span>
> * <span data-ttu-id="b5892-384">更新資料庫</span><span class="sxs-lookup"><span data-stu-id="b5892-384">Updated the database</span></span>

<span data-ttu-id="b5892-385">若要深入了解如何存取相關資料，請前往下個教學課程。</span><span class="sxs-lookup"><span data-stu-id="b5892-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b5892-386">下一步：存取相關資料</span><span class="sxs-lookup"><span data-stu-id="b5892-386">Next: Access related data</span></span>](read-related-data.md)
