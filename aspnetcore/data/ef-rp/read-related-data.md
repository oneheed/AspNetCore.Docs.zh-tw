---
title: 第6部分： Razor ASP.NET Core 讀取相關資料的 EF Core 頁面
author: rick-anderson
description: 第 6 Razor 頁和 Entity Framework 教學課程系列。
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 6e9933b7e4745bb59313b939f6499d6732460730
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957765"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a>第6部分： Razor ASP.NET Core 讀取相關資料的 EF Core 頁面

作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

本教學課程說明如何讀取及顯示相關資料。 相關資料是 EF Core 載入到導覽屬性的資料。

下圖顯示本教學課程的已完成頁面：

![Courses [索引] 頁面](read-related-data/_static/courses-index30.png)

![Instructors [索引] 頁面](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>積極式、明確式和消極式載入

EF Core 有幾種方式可以將相關資料載入到實體的導覽屬性：

* [積極式載入](/ef/core/querying/related-data#eager-loading)。 積極式載入是指某一類型實體的查詢同時也會載入相關實體。 讀取實體時，將會擷取其相關資料。 這通常會導致單一聯結查詢，其可擷取所有需要的資料。 EF Core 將針對某些類型的積極式載入發出多個查詢。 發出多個查詢可能比大型單一查詢更有效率。 積極式載入是使用 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Include%2A> 和 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.ThenInclude%2A> 方法加以指定。

  ![積極式載入範例](read-related-data/_static/eager-loading.png)

  當集合導覽包含在內時，積極式載入會傳送多個查詢：

  * 針對主查詢傳送一個查詢 
  * 針對負載樹狀結構中的每個集合「邊緣」傳送一個查詢。

* 使用 `Load` 的個別查詢：資料可以在個別查詢中擷取，而 EF Core 會「修正」導覽屬性。 「修正」表示 EF Core 會自動填入導覽屬性。 使用 `Load` 的個別查詢更像是明確式載入，而不是積極式載入。

  ![個別查詢範例](read-related-data/_static/separate-queries.png)

  **注意：** EF Core 會將導覽屬性自動修正為先前已載入至內容實例的任何其他實體。 即使「未」明確包含導覽屬性的資料，如果先前已載入某些或所有相關實體，仍然可能會填入該屬性。

* [明確載入](/ef/core/querying/related-data#explicit-loading)。 第一次讀取實體時，不會擷取相關資料。 必須撰寫程式碼，才能在需要時擷取相關資料。 使用個別查詢的明確式載入會導致多個查詢傳送至資料庫。 透過明確式載入，程式碼會指定要載入的導覽屬性。 請使用 `Load` 方法來執行明確式載入。 例如：

  ![明確式載入範例](read-related-data/_static/explicit-loading.png)

* [延遲載入](/ef/core/querying/related-data#lazy-loading)。 第一次讀取實體時，不會擷取相關資料。 第一次存取導覽屬性時，將會自動擷取該導覽屬性所需的資料。 每當第一次存取導覽屬性時，查詢會傳送至資料庫。 延遲載入可能會降低效能，例如，當開發人員使用 [N + 1 個查詢](https://www.bing.com/search?q=N%2B1+queries)時。 N + 1 個查詢會載入父系，並列舉子系。

## <a name="create-course-pages"></a>建立 Course 頁面

`Course` 實體包含導覽屬性，其中包含相關的 `Department` 實體。

![Course.Department](read-related-data/_static/dep-crs.png)

若要顯示針對課程指派的部門名稱：

* 將相關的 `Department` 實體載入 `Course.Department` 導覽屬性。
* 從 `Department` 實體的 `Name` 屬性取得名稱。

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Scaffold Course 頁面

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 遵循 [Scaffold Student 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：

  * 建立 *Pages/Courses* 資料夾。
  * 使用 `Course` 作為模型類別。
  * 使用現有內容類別，而非建立新的類別。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 建立 *Pages/Courses* 資料夾。

* 執行下列命令來 scaffold Course 頁面。

  **在 Windows 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **在 Linux 或 macOS 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* 開啟 *Pages/Courses/Index.cshtml.cs* 並檢查 `OnGetAsync` 方法。 Scaffolding 引擎已針對 `Department` 導覽屬性指定積極式載入。 `Include` 方法可指定積極式載入。

* 執行應用程式並選取 **課程** 連結。 部門資料行便會顯示沒有用的 `DepartmentID`。

### <a name="display-the-department-name"></a>顯示部門名稱

使用下列程式碼更新 Pages/Courses/Index.cshtml.cs：

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

上述程式碼會將 `Course` 屬性變更為 `Courses`，並新增 `AsNoTracking`。 `AsNoTracking` 可改善效能，因為不會追蹤傳回的實體。 無需追蹤實體的原因是它們不會在目前內容中更新。

使用下列程式碼更新 *Pages/Courses/Index.cshtml*。

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

已對包含 Scaffold 的程式碼進行下列變更：

* 已將 `Course` 屬性名稱變更為 `Courses`。
* 新增顯示 `CourseID` 屬性值的 [編號] 資料行。 主索引鍵預設不會進行 Scaffold，因為它們對終端使用者通常沒有任何意義。 不過，在此情況下，主索引鍵有意義。
* 變更 [部門] 資料行來顯示部門名稱。 此程式碼會顯示已載入到 `Department` 導覽屬性之 `Department` 實體的 `Name` 屬性：

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

執行應用程式，並選取 [Courses] 索引標籤來查看含有部門名稱的清單。

![Courses [索引] 頁面](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>使用 Select 載入相關資料

`OnGetAsync` 方法使用 `Include` 方法載入相關資料。 `Select` 方法是一種替代方案，只會載入所需的相關資料。 針對單一專案，例如， `Department.Name` 它會使用 `SQL INNER JOIN` 。 如果是集合，它會使用另一種資料庫存取，但集合上的 `Include` 運算子也是如此。

下列程式碼使用 `Select` 方法載入相關資料：

[!code-csharp[](intro/samples/cu50/Pages/Courses/IndexSelectModel.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

上述程式碼不會傳回任何實體類型，因此不會進行追蹤。 如需 EF 追蹤的詳細資訊，請參閱 [追蹤與 No-Tracking 查詢](/ef/core/querying/tracking)。

`CourseViewModel`：

[!code-csharp[](intro/samples/cu50/Models/SchoolViewModels/CourseViewModel.cs)]

如需完整的頁面，請參閱 [IndexSelectModel](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu50/Pages/Courses) Razor 。

## <a name="create-instructor-pages"></a>建立 Instructor 頁面

本節會 scaffold Instructor 頁面，並將相關的課程和註冊新增至 Instructors 索引頁面。

<a name="IP"></a>
![講師索引頁面](read-related-data/_static/instructors-index30.png)

此頁面將以下列方式讀取和顯示相關資料：

* 講師清單會顯示來自 `OfficeAssignment` 實體 (上述映像中的 Office) 的相關資料。 `Instructor` 與 `OfficeAssignment` 實體具有一對零或一關聯性。 積極式載入用於 `OfficeAssignment` 實體。 若需要顯示相關資料，積極式載入通常更有效率。 在此情況下，將會顯示講師的辦公室指派。
* 當使用者選取講師時，將會顯示相關的 `Course` 實體。 `Instructor` 與 `Course` 實體具有多對多關聯性。 將會針對 `Course` 實體和其相關 `Department` 實體使用積極式載入。 在此情況下，個別查詢可能更有效率，因為只需要所選取講師的課程。 這個範例示範如何使用導覽屬性中實體之導覽屬性的積極式載入。
* 當使用者選取課程時，將會顯示來自 `Enrollments` 實體的相關資料。 在上述映像中，將會顯示學生姓名和年級。 `Course` 與 `Enrollment` 實體具有一對多關聯性。

### <a name="create-a-view-model"></a>建立檢視模型

講師頁面會顯示下列三個不同資料表的資料。 需要檢視模型，其包含代表三個資料表的三個屬性。

使用下列程式碼建立 *SchoolViewModels/InstructorIndexData.cs*：

[!code-csharp[](intro/samples/cu50/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Scaffold Instructor 頁面

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 遵循 [Scaffold Students 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：

  * 建立 *Pages/Instructors* 資料夾。
  * 使用 `Instructor` 作為模型類別。
  * 使用現有內容類別，而非建立新的類別。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 建立 *Pages/Instructors* 資料夾。

* 執行下列命令來 Scaffold Instructor 頁面。

  **在 Windows 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **在 Linux 或 macOS 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

執行應用程式，並流覽至講師頁面。

使用下列程式碼更新 *Pages/講師/Index. .cs* ：

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_all)]

`OnGetAsync` 方法會針對所選取講師的識別碼接受選擇性的路由資料。

檢查 *Pages/Instructors/Index.cshtml.cs* 檔案中的查詢：

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_query)]

這個程式碼會針對下列導覽屬性指定積極式載入：

* `Instructor.OfficeAssignment`
* `Instructor.Course`
    * `Course.Department`

當選取講師時，會執行下列程式碼，也就是 `id != null` 。

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_id)]

選取的講師會從檢視模型的講師清單中擷取。 視圖模型的 `Courses` 屬性會與所 `Course` 選講師導覽屬性中的實體一起載入 `Courses` 。

`Where` 方法會傳回集合。 在此情況下，篩選準則會選取單一實體，因此 `Single` 會呼叫方法將集合轉換成單一 `Instructor` 實體。 `Instructor`實體提供對 `Course` 導覽屬性的存取。

當集合只有一個項目時，將會在集合上使用 <xref:System.Linq.Enumerable.Single%2A> 方法。 如果集合是空的或是有多個項目，`Single` 方法會擲回例外狀況。 替代方法是 <xref:System.Linq.Enumerable.SingleOrDefault%2A> ，如果集合是空的，則會傳回預設值。 針對此查詢，會 `null` 在傳回的預設中。

選取課程時，下列程式碼會填入檢視模型的 `Enrollments` 屬性：

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_enrollment)]

### <a name="update-the-instructors-index-page"></a>更新 Instructors [索引] 頁面

使用下列程式碼更新 *Pages/Instructors/Index.cshtml*。

[!code-cshtml[](intro/samples/cu50/Pages/Instructors/Index.cshtml?highlight=1)]

上述程式碼會進行下列變更：

  * 將指示詞更新 `page` 為 `@page "{id:int?}"` 。 `"{id:int?}"` 是路由範本。 [路由範本](xref:fundamentals/routing#route-template-reference)會將 URL 中的整數查詢字串變更為路由資料。 例如，只有在 `@page` 指示詞產生如下的 URL 時，按一下講師的 [選取] 連結：

    `https://localhost:5001/Instructors?id=2`

    當頁面指示詞為時 `@page "{id:int?}"` ，URL 為： `https://localhost:5001/Instructors/2`

  * 新增 [辦公室] 資料行，該資料行只有在 `item.OfficeAssignment` 不是 Null 時才會顯示 `item.OfficeAssignment.Location`。 因為這是一對零或一關聯性，所有可能沒有相關的 OfficeAssignment 實體。

    ```html
    @if (item.OfficeAssignment != null)
    {
        @item.OfficeAssignment.Location
    }
    ```

  * 新增 [課程] 資料行，以顯示每位講師所教授的課程。 如需此 razor 語法的詳細資訊，請參閱 [明確的行轉換](xref:mvc/views/razor#explicit-line-transition) 。
  * 新增程式碼，將 `class="success"` 動態新增至所選講師和課程的 `tr` 項目。 這會使用啟動程序類別設定所選取資料列的背景色彩。

    ```html
    string selectedRow = "";
    if (item.CourseID == Model.CourseID)
    {
        selectedRow = "success";
    }
    <tr class="@selectedRow">
    ```

  * 新增標示為 **選取** 的超連結。 此連結會將所選取的講師識別碼傳送至 `Index` 方法，並設定背景色彩。

    ```html
    <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
    ```

  * 新增所選講師的課程資料表。
  * 新增所選課程的學生註冊資料表。

執行應用程式，然後選取 [ **講師** ] 索引標籤。此頁面會顯示 `Location` 來自相關實體的 (office) `OfficeAssignment` 。 如果 `OfficeAssignment` 為 Null，則會顯示空的資料表資料格。

按一下講師的 [選取] 連結。 資料列樣式會變更，並會顯示指派給該講師的課程。

選取課程，以查看已註冊學生和其年級的清單。

![Instructors [索引] 頁面已選取講師和課程](read-related-data/_static/instructors-index30.png)

## <a name="next-steps"></a>下一步

下一個教學課程會示範如何更新相關資料。

>[!div class="step-by-step"]
>[上一個教學](xref:data/ef-rp/complex-data-model) 
> 課程[下一個教學](xref:data/ef-rp/update-related-data)課程

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

本教學課程說明如何讀取及顯示相關資料。 相關資料是 EF Core 載入到導覽屬性的資料。

下圖顯示本教學課程的已完成頁面：

![Courses [索引] 頁面](read-related-data/_static/courses-index30.png)

![Instructors [索引] 頁面](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a>積極式、明確式和消極式載入

EF Core 有幾種方式可以將相關資料載入到實體的導覽屬性：

* [積極式載入](/ef/core/querying/related-data#eager-loading)。 積極式載入是指某一類型實體的查詢同時也會載入相關實體。 讀取實體時，將會擷取其相關資料。 這通常會導致單一聯結查詢，其可擷取所有需要的資料。 EF Core 將針對某些類型的積極式載入發出多個查詢。 發出多個查詢可能比大型單一查詢更有效率。 積極式載入是使用 `Include` 和 `ThenInclude` 方法加以指定。

  ![積極式載入範例](read-related-data/_static/eager-loading.png)
 
  當集合導覽包含在內時，積極式載入會傳送多個查詢：

  * 針對主查詢傳送一個查詢 
  * 針對負載樹狀結構中的每個集合「邊緣」傳送一個查詢。

* 使用 `Load` 的個別查詢：資料可以在個別查詢中擷取，而 EF Core 會「修正」導覽屬性。 「修正」表示 EF Core 會自動填入導覽屬性。 使用 `Load` 的個別查詢更像是明確式載入，而不是積極式載入。

  ![個別查詢範例](read-related-data/_static/separate-queries.png)

  **注意：** EF Core 會將導覽屬性自動修正為先前已載入至內容實例的任何其他實體。 即使「未」明確包含導覽屬性的資料，如果先前已載入某些或所有相關實體，仍然可能會填入該屬性。

* [明確載入](/ef/core/querying/related-data#explicit-loading)。 第一次讀取實體時，不會擷取相關資料。 必須撰寫程式碼，才能在需要時擷取相關資料。 使用個別查詢的明確式載入會導致多個查詢傳送至資料庫。 透過明確式載入，程式碼會指定要載入的導覽屬性。 請使用 `Load` 方法來執行明確式載入。 例如：

  ![明確式載入範例](read-related-data/_static/explicit-loading.png)

* [延遲載入](/ef/core/querying/related-data#lazy-loading)。 第一次讀取實體時，不會擷取相關資料。 第一次存取導覽屬性時，將會自動擷取該導覽屬性所需的資料。 每當第一次存取導覽屬性時，查詢會傳送至資料庫。 消極式載入可能會降低效能，例如，當開發人員使用 N + 1 模式、載入父系和列舉子系時。

## <a name="create-course-pages"></a>建立 Course 頁面

`Course` 實體包含導覽屬性，其中包含相關的 `Department` 實體。

![Course.Department](read-related-data/_static/dep-crs.png)

若要顯示針對課程指派的部門名稱：

* 將相關的 `Department` 實體載入 `Course.Department` 導覽屬性。
* 從 `Department` 實體的 `Name` 屬性取得名稱。

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a>Scaffold Course 頁面

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 遵循 [Scaffold Student 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：

  * 建立 *Pages/Courses* 資料夾。
  * 使用 `Course` 作為模型類別。
  * 使用現有內容類別，而非建立新的類別。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 建立 *Pages/Courses* 資料夾。

* 執行下列命令來 scaffold Course 頁面。

  **在 Windows 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  **在 Linux 或 macOS 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* 開啟 *Pages/Courses/Index.cshtml.cs* 並檢查 `OnGetAsync` 方法。 Scaffolding 引擎已針對 `Department` 導覽屬性指定積極式載入。 `Include` 方法可指定積極式載入。

* 執行應用程式並選取 **課程** 連結。 部門資料行便會顯示沒有用的 `DepartmentID`。

### <a name="display-the-department-name"></a>顯示部門名稱

使用下列程式碼更新 Pages/Courses/Index.cshtml.cs：

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

上述程式碼會將 `Course` 屬性變更為 `Courses`，並新增 `AsNoTracking`。 `AsNoTracking` 可改善效能，因為不會追蹤傳回的實體。 無需追蹤實體的原因是它們不會在目前內容中更新。

使用下列程式碼更新 *Pages/Courses/Index.cshtml*。

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

已對包含 Scaffold 的程式碼進行下列變更：

* 已將 `Course` 屬性名稱變更為 `Courses`。
* 新增顯示 `CourseID` 屬性值的 [編號] 資料行。 主索引鍵預設不會進行 Scaffold，因為它們對終端使用者通常沒有任何意義。 不過，在此情況下，主索引鍵有意義。
* 變更 [部門] 資料行來顯示部門名稱。 此程式碼會顯示已載入到 `Department` 導覽屬性之 `Department` 實體的 `Name` 屬性：

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

執行應用程式，並選取 [Courses] 索引標籤來查看含有部門名稱的清單。

![Courses [索引] 頁面](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>使用 Select 載入相關資料

`OnGetAsync` 方法使用 `Include` 方法載入相關資料。 `Select` 方法是一種替代方案，只會載入所需的相關資料。 如果是單一項目 (例如 `Department.Name`)，它會使用 SQL INNER JOIN。 如果是集合，它會使用另一種資料庫存取，但集合上的 `Include` 運算子也是如此。

下列程式碼使用 `Select` 方法載入相關資料：

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

上述程式碼不會傳回任何實體類型，因此不會進行追蹤。 如需 EF 追蹤的詳細資訊，請參閱 [追蹤與 No-Tracking 查詢](/ef/core/querying/tracking)。

`CourseViewModel`：

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

如需完整範例，請參閱 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)。

## <a name="create-instructor-pages"></a>建立 Instructor 頁面

本節會 scaffold Instructor 頁面，並將相關的課程和註冊新增至 Instructors 索引頁面。

<a name="IP"></a>
![講師索引頁面](read-related-data/_static/instructors-index30.png)

此頁面將以下列方式讀取和顯示相關資料：

* 講師清單會顯示來自 `OfficeAssignment` 實體 (上述映像中的 Office) 的相關資料。 `Instructor` 與 `OfficeAssignment` 實體具有一對零或一關聯性。 積極式載入用於 `OfficeAssignment` 實體。 若需要顯示相關資料，積極式載入通常更有效率。 在此情況下，將會顯示講師的辦公室指派。
* 當使用者選取講師時，將會顯示相關的 `Course` 實體。 `Instructor` 與 `Course` 實體具有多對多關聯性。 將會針對 `Course` 實體和其相關 `Department` 實體使用積極式載入。 在此情況下，個別查詢可能更有效率，因為只需要所選取講師的課程。 這個範例示範如何使用導覽屬性中實體之導覽屬性的積極式載入。
* 當使用者選取課程時，將會顯示來自 `Enrollments` 實體的相關資料。 在上述映像中，將會顯示學生姓名和年級。 `Course` 與 `Enrollment` 實體具有一對多關聯性。

### <a name="create-a-view-model"></a>建立檢視模型

講師頁面會顯示下列三個不同資料表的資料。 需要檢視模型，其包含代表三個資料表的三個屬性。

使用下列程式碼建立 *SchoolViewModels/InstructorIndexData.cs*：

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a>Scaffold Instructor 頁面

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 遵循 [Scaffold Students 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：

  * 建立 *Pages/Instructors* 資料夾。
  * 使用 `Instructor` 作為模型類別。
  * 使用現有內容類別，而非建立新的類別。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 建立 *Pages/Instructors* 資料夾。

* 執行下列命令來 Scaffold Instructor 頁面。

  **在 Windows 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  **在 Linux 或 macOS 上：**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

若要在更新之前查看 Scaffold 頁面的外觀，請執行應用程式並巡覽至 Instructors 頁面。

使用下列程式碼更新 *Pages/講師/Index. .cs* ：

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

`OnGetAsync` 方法會針對所選取講師的識別碼接受選擇性的路由資料。

檢查 *Pages/Instructors/Index.cshtml.cs* 檔案中的查詢：

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

這個程式碼會針對下列導覽屬性指定積極式載入：

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

請注意 `CourseAssignments` 和 `Course` 之 `Include` 和 `ThenInclude` 方法的重複。 若要針對 `Course` 實體的兩個導覽屬性指定積極式載入，則重複是必要的。

下列程式碼會在已選取講師 (`id != null`) 時執行。

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

選取的講師會從檢視模型的講師清單中擷取。 檢視模型的 `Courses` 屬性則使用 `Course` 實體從該講師的 `CourseAssignments` 導覽屬性載入。

`Where` 方法會傳回集合。 但是在這種情況下，篩選器會選取單一實體，因此 `Single` 會呼叫方法將集合轉換成單一 `Instructor` 實體。 `Instructor` 實體提供對 `CourseAssignments` 屬性的存取。 `CourseAssignments` 提供對相關 `Course` 實體的存取。

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

當集合只有一個項目時，將會在集合上使用 `Single` 方法。 如果集合是空的或是有多個項目，`Single` 方法會擲回例外狀況。 替代方式是 `SingleOrDefault`，它會在集合是空的時傳回預設值 (在此情況下為 Null)。

選取課程時，下列程式碼會填入檢視模型的 `Enrollments` 屬性：

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a>更新 Instructors [索引] 頁面

使用下列程式碼更新 *Pages/Instructors/Index.cshtml*。

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

上述程式碼會進行下列變更：

* 將 `page` 指示詞從 `@page` 更新為 `@page "{id:int?}"`。 `"{id:int?}"` 是路由範本。 路由範本將 URL 中的整數查詢字串變更為路由資料。 例如，只有在 `@page` 指示詞產生如下的 URL 時，按一下講師的 [選取] 連結：

  `https://localhost:5001/Instructors?id=2`

  頁面指示詞是 `@page "{id:int?}"` 時，URL 為：

  `https://localhost:5001/Instructors/2`

* 新增 [辦公室] 資料行，該資料行只有在 `item.OfficeAssignment` 不是 Null 時才會顯示 `item.OfficeAssignment.Location`。 因為這是一對零或一關聯性，所有可能沒有相關的 OfficeAssignment 實體。

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 新增 [課程] 資料行，以顯示每位講師所教授的課程。 如需此 razor 語法的詳細資訊，請參閱 [明確的行轉換](xref:mvc/views/razor#explicit-line-transition) 。

* 新增程式碼，將 `class="success"` 動態新增至所選講師和課程的 `tr` 項目。 這會使用啟動程序類別設定所選取資料列的背景色彩。

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* 新增標示為 **選取** 的超連結。 此連結會將所選取的講師識別碼傳送至 `Index` 方法，並設定背景色彩。

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* 新增所選講師的課程資料表。

* 新增所選課程的學生註冊資料表。

執行應用程式，然後選取 [ **講師** ] 索引標籤。此頁面會顯示 `Location` 來自相關實體的 (office) `OfficeAssignment` 。 如果 `OfficeAssignment` 為 Null，則會顯示空的資料表資料格。

按一下講師的 [選取] 連結。 資料列樣式會變更，並會顯示指派給該講師的課程。

選取課程，以查看已註冊學生和其年級的清單。

![Instructors [索引] 頁面已選取講師和課程](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a>使用 Single

`Single` 方法可以傳入 `Where` 條件，而不是個別呼叫 `Where` 方法：

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

使用 `Single` 搭配 Where 條件是個人喜好設定。 其不會對使用 `Where` 方法提供任何益處。

## <a name="explicit-loading"></a>明確式載入

目前程式碼針對 `Enrollments` 和 `Students` 指定積極式載入：

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

假設使用者很少會想要查看課程中的註冊項目。 在此情況下，最佳方法就是在要求時，只載入註冊資料。 在本節中，`OnGetAsync` 更新為針對 `Enrollments` 和 `Students` 使用明確式載入。

使用下列程式碼更新 *Pages/Instructors/Index.cshtml.cs*。

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

上述程式碼會捨棄註冊和學生資料的 *ThenInclude* 方法呼叫。 如果選取了課程，則明確載入的程式碼會擷取：

* 所選取課程的 `Enrollment` 實體。
* 每個 `Enrollment` 的 `Student` 實體。

請注意，上述程式碼會將 `.AsNoTracking()` 標記為註解。 針對所追蹤的實體，導覽屬性只能進行明確式載入。

測試應用程式。 就使用者的觀點而言，應用程式行為與先前版本相同。

## <a name="next-steps"></a>下一步

下一個教學課程會示範如何更新相關資料。

>[!div class="step-by-step"]
>[上一個教學](xref:data/ef-rp/complex-data-model) 
> 課程[下一個教學](xref:data/ef-rp/update-related-data)課程

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在本教學課程中，將會讀取和顯示相關資料。 相關資料是 EF Core 載入到導覽屬性的資料。

若您遇到無法解決的問題，請[下載或檢視完整應用程式。](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples) [下載指示](xref:index#how-to-download-a-sample)。

下圖顯示本教學課程的已完成頁面：

![Courses [索引] 頁面](read-related-data/_static/courses-index.png)

![Instructors [索引] 頁面](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a>相關資料的積極式、明確式和消極式載入

EF Core 有幾種方式可以將相關資料載入到實體的導覽屬性：

* [積極式載入](/ef/core/querying/related-data#eager-loading)。 積極式載入是指某一類型實體的查詢同時也會載入相關實體。 讀取實體時，將會擷取其相關資料。 這通常會導致單一聯結查詢，其可擷取所有需要的資料。 EF Core 將針對某些類型的積極式載入發出多個查詢。 相較於 EF6 中的某些查詢只有單一查詢的情況，發出多個查詢可能更有效率。 積極式載入是使用 `Include` 和 `ThenInclude` 方法加以指定。

  ![積極式載入範例](read-related-data/_static/eager-loading.png)
 
  當集合導覽包含在內時，積極式載入會傳送多個查詢：

  * 針對主查詢傳送一個查詢 
  * 針對負載樹狀結構中的每個集合「邊緣」傳送一個查詢。

* 使用 `Load` 的個別查詢：資料可以在個別查詢中擷取，而 EF Core 會「修正」導覽屬性。 「修正」表示 EF Core 會自動填入導覽屬性。 使用 `Load` 的個別查詢更像是明確式載入，而不是積極式載入。

  ![個別查詢範例](read-related-data/_static/separate-queries.png)

  注意：EF Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。 即使「未」明確包含導覽屬性的資料，如果先前已載入某些或所有相關實體，仍然可能會填入該屬性。

* [明確載入](/ef/core/querying/related-data#explicit-loading)。 第一次讀取實體時，不會擷取相關資料。 必須撰寫程式碼，才能在需要時擷取相關資料。 使用個別查詢的明確式載入會導致多個查詢傳送至資料庫。 透過明確式載入，程式碼會指定要載入的導覽屬性。 請使用 `Load` 方法來執行明確式載入。 例如：

  ![明確式載入範例](read-related-data/_static/explicit-loading.png)

* [延遲載入](/ef/core/querying/related-data#lazy-loading)。 [EF Core 已在 2.1 版中新增消極式載入](/ef/core/querying/related-data#lazy-loading)。 第一次讀取實體時，不會擷取相關資料。 第一次存取導覽屬性時，將會自動擷取該導覽屬性所需的資料。 每當第一次存取導覽屬性時，查詢會傳送至資料庫。

* `Select` 運算子只會載入所需的相關資料。

## <a name="create-a-course-page-that-displays-department-name"></a>建立顯示部門名稱的 Course 頁面

Course 實體包含導覽屬性，其中包含 `Department` 實體。 `Department` 實體包含已指派課程的部門。

若要在課程清單中顯示所指派部門的名稱：

* 從 `Department` 實體取得 `Name` 屬性。
* `Department` 實體來自 `Course.Department` 導覽屬性。

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a>Scaffold Course 模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

請遵循[建立學生結構模型](xref:data/ef-rp/intro#scaffold-the-student-model)中的指示，並為模型類別使用 `Course`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 執行以下命令：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

上述命令會 Scaffold `Course` 模型。 在 Visual Studio 中開啟專案。

開啟 *Pages/Courses/Index.cshtml.cs* 並檢查 `OnGetAsync` 方法。 Scaffolding 引擎已針對 `Department` 導覽屬性指定積極式載入。 `Include` 方法可指定積極式載入。

執行應用程式並選取 **課程** 連結。 部門資料行便會顯示沒有用的 `DepartmentID`。

以下列程式碼更新 `OnGetAsync` 方法：

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

上述程式碼會新增 `AsNoTracking`。 `AsNoTracking` 可改善效能，因為不會追蹤傳回的實體。 不會追蹤實體的原因是它們不會在目前的內容中更新。

以下列醒目提示的標記更新 *Pages/Courses/Index.cshtml*：

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

已對包含 Scaffold 的程式碼進行下列變更：

* 已將標題從「索引」) 變更為「課程」。
* 新增顯示 `CourseID` 屬性值的 [編號] 資料行。 主索引鍵預設不會進行 Scaffold，因為它們對終端使用者通常沒有任何意義。 不過，在此情況下，主索引鍵有意義。
* 變更 [部門] 資料行來顯示部門名稱。 此程式碼會顯示已載入到 `Department` 導覽屬性之 `Department` 實體的 `Name` 屬性：

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

執行應用程式，並選取 [Courses] 索引標籤來查看含有部門名稱的清單。

![Courses [索引] 頁面](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a>使用 Select 載入相關資料

`OnGetAsync` 方法使用 `Include` 方法載入相關資料：

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`Select` 運算子只會載入所需的相關資料。 如果是單一項目 (例如 `Department.Name`)，它會使用 SQL INNER JOIN。 如果是集合，它會使用另一種資料庫存取，但集合上的 `Include` 運算子也是如此。

下列程式碼使用 `Select` 方法載入相關資料：

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

`CourseViewModel`：

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

如需完整範例，請參閱 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)。

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a>建立顯示課程和註冊的 Instructors 頁面

在本節中，將會建立 Instructors 頁面。

<a name="IP"></a>
![講師索引頁面](read-related-data/_static/instructors-index.png)

此頁面將以下列方式讀取和顯示相關資料：

* 講師清單會顯示來自 `OfficeAssignment` 實體 (上述映像中的 Office) 的相關資料。 `Instructor` 與 `OfficeAssignment` 實體具有一對零或一關聯性。 積極式載入用於 `OfficeAssignment` 實體。 若需要顯示相關資料，積極式載入通常更有效率。 在此情況下，將會顯示講師的辦公室指派。
* 當使用者選取講師 (上述映像中的 Harui) 時，便會顯示相關的 `Course` 實體。 `Instructor` 與 `Course` 實體具有多對多關聯性。 將會針對 `Course` 實體和其相關 `Department` 實體使用積極式載入。 在此情況下，個別查詢可能更有效率，因為只需要所選取講師的課程。 這個範例示範如何使用導覽屬性中實體之導覽屬性的積極式載入。
* 當使用者選取課程 (上述映像中的 Chemistry)，隨即顯示 `Enrollments` 中的相關資料。 在上述映像中，將會顯示學生姓名和年級。 `Course` 與 `Enrollment` 實體具有一對多關聯性。

### <a name="create-a-view-model-for-the-instructor-index-view"></a>建立 Instructor [索引] 檢視的檢視模型

講師頁面會顯示下列三個不同資料表的資料。 建立的檢視模型會包含三個實體代表三個資料表。

在 *SchoolViewModels* 資料夾中，以下列程式碼建立 *InstructorIndexData.cs*：

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a>Scaffold Instructor 模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

請遵循[建立學生結構模型](xref:data/ef-rp/intro#scaffold-the-student-model)中的指示，並為模型類別使用 `Instructor`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 執行以下命令：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

上述命令會 Scaffold `Instructor` 模型。 
執行應用程式並巡覽至講師頁面。

以下列程式碼取代 *Pages/Instructors/Index.cshtml.cs*：

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

`OnGetAsync` 方法會針對所選取講師的識別碼接受選擇性的路由資料。

檢查 *Pages/Instructors/Index.cshtml.cs* 檔案中的查詢：

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

查詢具有兩個 Include：

* `OfficeAssignment`：顯示在 [Instructors 檢視](#IP)。
* `CourseAssignments`：它顯示所教授的課程。

### <a name="update-the-instructors-index-page"></a>更新 Instructors [索引] 頁面

以下列標記更新 *Pages/Instructors/Index.cshtml*：

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

上述標記會進行下列變更：

* 將 `page` 指示詞從 `@page` 更新為 `@page "{id:int?}"`。 `"{id:int?}"` 是路由範本。 路由範本將 URL 中的整數查詢字串變更為路由資料。 例如，只有在 `@page` 指示詞產生如下的 URL 時，按一下講師的 [選取] 連結：

  `http://localhost:1234/Instructors?id=2`

  頁面指示詞是 `@page "{id:int?}"` 時，先前的 URL 為：

  `http://localhost:1234/Instructors/2`

* 頁面標題是 **Instructors**。
* 新增 [辦公室] 資料行，該資料行只有在 `item.OfficeAssignment` 不是 Null 時才會顯示 `item.OfficeAssignment.Location`。 因為這是一對零或一關聯性，所有可能沒有相關的 OfficeAssignment 實體。

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* 新增 [課程] 資料行，以顯示每位講師所教授的課程。 如需此 razor 語法的詳細資訊，請參閱 [明確的行轉換](xref:mvc/views/razor#explicit-line-transition) 。

* 新增程式碼，將 `class="success"` 動態新增至所選取講師的 `tr` 項目。 這會使用啟動程序類別設定所選取資料列的背景色彩。

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* 新增標示為 **選取** 的超連結。 此連結會將所選取的講師識別碼傳送至 `Index` 方法，並設定背景色彩。

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

執行應用程式，然後選取 [ **講師** ] 索引標籤。此頁面會顯示 `Location` 來自相關實體的 (office) `OfficeAssignment` 。 如果 OfficeAssignment 是 Null，就會顯示空的資料表資料格。

按一下 **選取** 連結。 資料列樣式變更。

### <a name="add-courses-taught-by-selected-instructor"></a>新增選取的講師所教授的課程

以下列程式碼更新 *Pages/Instructors/Index.cshtml.cs* 中的 `OnGetAsync` 方法：

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

新增 `public int CourseID { get; set; }`

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

檢查已更新的查詢：

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

上述查詢會新增 `Department` 實體。

下列程式碼會在已選取講師 (`id != null`) 時執行。 選取的講師會從檢視模型的講師清單中擷取。 檢視模型的 `Courses` 屬性則使用 `Course` 實體從該講師的 `CourseAssignments` 導覽屬性載入。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

`Where` 方法會傳回集合。 在上述 `Where` 方法中，只會傳回一個單一 `Instructor` 實體。 `Single` 方法會將集合轉換成單一 `Instructor` 實體。 `Instructor` 實體提供對 `CourseAssignments` 屬性的存取。 `CourseAssignments` 提供對相關 `Course` 實體的存取。

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

當集合只有一個項目時，將會在集合上使用 `Single` 方法。 如果集合是空的或是有多個項目，`Single` 方法會擲回例外狀況。 替代方式是 `SingleOrDefault`，它會在集合是空的時傳回預設值 (在此情況下為 Null)。 在空集合上使用 `SingleOrDefault`：

* 造成例外狀況 (由於嘗試在 Null 參考上尋找 `Courses` 屬性)。
* 例外狀況訊息會不太清楚地指出問題的原因。

選取課程時，下列程式碼會填入檢視模型的 `Enrollments` 屬性：

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

將下列標記新增至 *Pages/講師/Index. cshtml* Razor 頁面的結尾：

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

當選取講師時，上述標記會顯示與講師相關的課程。

測試應用程式。 按一下講師頁面上的 **選取** 連結。

### <a name="show-student-data"></a>顯示學生資料

在本節中，應用程式會更新以顯示所選取課程的學生資料。

以下列程式碼更新 *Pages/Instructors/Index.cshtml.cs* 中 `OnGetAsync` 方法的查詢：

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

更新 *Pages/Instructors/Index.cshtml*。 將下列標記新增至檔案結尾：

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

上述標記會顯示註冊所選取課程的學生清單。

重新整理頁面，然後選取講師。 選取課程，以查看已註冊學生和其年級的清單。

![Instructors [索引] 頁面已選取講師和課程](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a>使用 Single

`Single` 方法可以傳入 `Where` 條件，而不是個別呼叫 `Where` 方法：

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

比起使用 `Where`，上述 `Single` 方法並沒有任何優勢。 某些開發人員偏好使用 `Single` 方法樣式。

## <a name="explicit-loading"></a>明確式載入

目前程式碼針對 `Enrollments` 和 `Students` 指定積極式載入：

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

假設使用者很少會想要查看課程中的註冊項目。 在此情況下，最佳方法就是在要求時，只載入註冊資料。 在本節中，`OnGetAsync` 更新為針對 `Enrollments` 和 `Students` 使用明確式載入。

使用下列程式碼更新 `OnGetAsync`：

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

上述程式碼會捨棄註冊和學生資料的 *ThenInclude* 方法呼叫。 如果選取了課程，醒目提示的程式碼就會擷取：

* 所選取課程的 `Enrollment` 實體。
* 每個 `Enrollment` 的 `Student` 實體。

請注意，上述程式碼會將 `.AsNoTracking()` 註解化。 針對所追蹤的實體，導覽屬性只能進行明確式載入。

測試應用程式。 就使用者的觀點而言，應用程式行為與之前的版本相同。

下一個教學課程會示範如何更新相關資料。

## <a name="additional-resources"></a>其他資源

* [這個教學課程的 YouTube 版本 (第 1 部分)](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [這個教學課程的 YouTube 版本 (第 2 部分)](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
>[上一個](xref:data/ef-rp/complex-data-model) 
>[下一步](xref:data/ef-rp/update-related-data)

::: moniker-end