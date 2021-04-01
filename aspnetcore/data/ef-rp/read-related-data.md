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
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="1cea8-103">第6部分： Razor ASP.NET Core 讀取相關資料的 EF Core 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="1cea8-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1cea8-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1cea8-105">本教學課程說明如何讀取及顯示相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="1cea8-106">相關資料是 EF Core 載入到導覽屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="1cea8-107">下圖顯示本教學課程的已完成頁面：</span><span class="sxs-lookup"><span data-stu-id="1cea8-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses [索引] 頁面](read-related-data/_static/courses-index30.png)

![Instructors [索引] 頁面](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="1cea8-110">積極式、明確式和消極式載入</span><span class="sxs-lookup"><span data-stu-id="1cea8-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="1cea8-111">EF Core 有幾種方式可以將相關資料載入到實體的導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="1cea8-112">[積極式載入](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="1cea8-113">積極式載入是指某一類型實體的查詢同時也會載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="1cea8-114">讀取實體時，將會擷取其相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="1cea8-115">這通常會導致單一聯結查詢，其可擷取所有需要的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="1cea8-116">EF Core 將針對某些類型的積極式載入發出多個查詢。</span><span class="sxs-lookup"><span data-stu-id="1cea8-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="1cea8-117">發出多個查詢可能比大型單一查詢更有效率。</span><span class="sxs-lookup"><span data-stu-id="1cea8-117">Issuing multiple queries can be more efficient than a large single query.</span></span> <span data-ttu-id="1cea8-118">積極式載入是使用 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Include%2A> 和 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.ThenInclude%2A> 方法加以指定。</span><span class="sxs-lookup"><span data-stu-id="1cea8-118">Eager loading is specified with the <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Include%2A> and <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.ThenInclude%2A> methods.</span></span>

  ![積極式載入範例](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="1cea8-120">當集合導覽包含在內時，積極式載入會傳送多個查詢：</span><span class="sxs-lookup"><span data-stu-id="1cea8-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="1cea8-121">針對主查詢傳送一個查詢</span><span class="sxs-lookup"><span data-stu-id="1cea8-121">One query for the main query</span></span> 
  * <span data-ttu-id="1cea8-122">針對負載樹狀結構中的每個集合「邊緣」傳送一個查詢。</span><span class="sxs-lookup"><span data-stu-id="1cea8-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="1cea8-123">使用 `Load` 的個別查詢：資料可以在個別查詢中擷取，而 EF Core 會「修正」導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="1cea8-124">「修正」表示 EF Core 會自動填入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="1cea8-125">使用 `Load` 的個別查詢更像是明確式載入，而不是積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![個別查詢範例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="1cea8-127">**注意：** EF Core 會將導覽屬性自動修正為先前已載入至內容實例的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1cea8-128">即使「未」明確包含導覽屬性的資料，如果先前已載入某些或所有相關實體，仍然可能會填入該屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="1cea8-129">[明確載入](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="1cea8-130">第一次讀取實體時，不會擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1cea8-131">必須撰寫程式碼，才能在需要時擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="1cea8-132">使用個別查詢的明確式載入會導致多個查詢傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="1cea8-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="1cea8-133">透過明確式載入，程式碼會指定要載入的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="1cea8-134">請使用 `Load` 方法來執行明確式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="1cea8-135">例如：</span><span class="sxs-lookup"><span data-stu-id="1cea8-135">For example:</span></span>

  ![明確式載入範例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="1cea8-137">[延遲載入](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1cea8-138">第一次讀取實體時，不會擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1cea8-139">第一次存取導覽屬性時，將會自動擷取該導覽屬性所需的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="1cea8-140">每當第一次存取導覽屬性時，查詢會傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="1cea8-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="1cea8-141">延遲載入可能會降低效能，例如，當開發人員使用 [N + 1 個查詢](https://www.bing.com/search?q=N%2B1+queries)時。</span><span class="sxs-lookup"><span data-stu-id="1cea8-141">Lazy loading can hurt performance, for example when developers use [N+1 queries](https://www.bing.com/search?q=N%2B1+queries).</span></span> <span data-ttu-id="1cea8-142">N + 1 個查詢會載入父系，並列舉子系。</span><span class="sxs-lookup"><span data-stu-id="1cea8-142">N+1 queries load a parent and enumerate through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="1cea8-143">建立 Course 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-143">Create Course pages</span></span>

<span data-ttu-id="1cea8-144">`Course` 實體包含導覽屬性，其中包含相關的 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-144">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="1cea8-146">若要顯示針對課程指派的部門名稱：</span><span class="sxs-lookup"><span data-stu-id="1cea8-146">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="1cea8-147">將相關的 `Department` 實體載入 `Course.Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-147">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="1cea8-148">從 `Department` 實體的 `Name` 屬性取得名稱。</span><span class="sxs-lookup"><span data-stu-id="1cea8-148">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="1cea8-149">Scaffold Course 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-149">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1cea8-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1cea8-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1cea8-151">遵循 [Scaffold Student 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：</span><span class="sxs-lookup"><span data-stu-id="1cea8-151">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="1cea8-152">建立 *Pages/Courses* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1cea8-152">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="1cea8-153">使用 `Course` 作為模型類別。</span><span class="sxs-lookup"><span data-stu-id="1cea8-153">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="1cea8-154">使用現有內容類別，而非建立新的類別。</span><span class="sxs-lookup"><span data-stu-id="1cea8-154">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1cea8-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1cea8-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1cea8-156">建立 *Pages/Courses* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1cea8-156">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="1cea8-157">執行下列命令來 scaffold Course 頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-157">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="1cea8-158">**在 Windows 上：**</span><span class="sxs-lookup"><span data-stu-id="1cea8-158">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="1cea8-159">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="1cea8-159">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="1cea8-160">開啟 *Pages/Courses/Index.cshtml.cs* 並檢查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="1cea8-160">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="1cea8-161">Scaffolding 引擎已針對 `Department` 導覽屬性指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-161">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="1cea8-162">`Include` 方法可指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-162">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="1cea8-163">執行應用程式並選取 **課程** 連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-163">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="1cea8-164">部門資料行便會顯示沒有用的 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-164">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="1cea8-165">顯示部門名稱</span><span class="sxs-lookup"><span data-stu-id="1cea8-165">Display the department name</span></span>

<span data-ttu-id="1cea8-166">使用下列程式碼更新 Pages/Courses/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="1cea8-166">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="1cea8-167">上述程式碼會將 `Course` 屬性變更為 `Courses`，並新增 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-167">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="1cea8-168">`AsNoTracking` 可改善效能，因為不會追蹤傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-168">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="1cea8-169">無需追蹤實體的原因是它們不會在目前內容中更新。</span><span class="sxs-lookup"><span data-stu-id="1cea8-169">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="1cea8-170">使用下列程式碼更新 *Pages/Courses/Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="1cea8-170">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="1cea8-171">已對包含 Scaffold 的程式碼進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="1cea8-171">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="1cea8-172">已將 `Course` 屬性名稱變更為 `Courses`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-172">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="1cea8-173">新增顯示 `CourseID` 屬性值的 [編號] 資料行。</span><span class="sxs-lookup"><span data-stu-id="1cea8-173">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="1cea8-174">主索引鍵預設不會進行 Scaffold，因為它們對終端使用者通常沒有任何意義。</span><span class="sxs-lookup"><span data-stu-id="1cea8-174">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="1cea8-175">不過，在此情況下，主索引鍵有意義。</span><span class="sxs-lookup"><span data-stu-id="1cea8-175">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="1cea8-176">變更 [部門] 資料行來顯示部門名稱。</span><span class="sxs-lookup"><span data-stu-id="1cea8-176">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="1cea8-177">此程式碼會顯示已載入到 `Department` 導覽屬性之 `Department` 實體的 `Name` 屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-177">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="1cea8-178">執行應用程式，並選取 [Courses] 索引標籤來查看含有部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="1cea8-178">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses [索引] 頁面](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="1cea8-180">使用 Select 載入相關資料</span><span class="sxs-lookup"><span data-stu-id="1cea8-180">Loading related data with Select</span></span>

<span data-ttu-id="1cea8-181">`OnGetAsync` 方法使用 `Include` 方法載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-181">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="1cea8-182">`Select` 方法是一種替代方案，只會載入所需的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-182">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="1cea8-183">針對單一專案，例如， `Department.Name` 它會使用 `SQL INNER JOIN` 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-183">For single items, like the `Department.Name` it uses a `SQL INNER JOIN`.</span></span> <span data-ttu-id="1cea8-184">如果是集合，它會使用另一種資料庫存取，但集合上的 `Include` 運算子也是如此。</span><span class="sxs-lookup"><span data-stu-id="1cea8-184">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="1cea8-185">下列程式碼使用 `Select` 方法載入相關資料：</span><span class="sxs-lookup"><span data-stu-id="1cea8-185">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/IndexSelectModel.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="1cea8-186">上述程式碼不會傳回任何實體類型，因此不會進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="1cea8-186">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="1cea8-187">如需 EF 追蹤的詳細資訊，請參閱 [追蹤與 No-Tracking 查詢](/ef/core/querying/tracking)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-187">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="1cea8-188">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="1cea8-188">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu50/Models/SchoolViewModels/CourseViewModel.cs)]

<span data-ttu-id="1cea8-189">如需完整的頁面，請參閱 [IndexSelectModel](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu50/Pages/Courses) Razor 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-189">See [IndexSelectModel](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu50/Pages/Courses) for the complete Razor Pages.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="1cea8-190">建立 Instructor 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-190">Create Instructor pages</span></span>

<span data-ttu-id="1cea8-191">本節會 scaffold Instructor 頁面，並將相關的課程和註冊新增至 Instructors 索引頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-191">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="1cea8-192">![講師索引頁面](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="1cea8-192">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="1cea8-193">此頁面將以下列方式讀取和顯示相關資料：</span><span class="sxs-lookup"><span data-stu-id="1cea8-193">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="1cea8-194">講師清單會顯示來自 `OfficeAssignment` 實體 (上述映像中的 Office) 的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-194">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="1cea8-195">`Instructor` 與 `OfficeAssignment` 實體具有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-195">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1cea8-196">積極式載入用於 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-196">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="1cea8-197">若需要顯示相關資料，積極式載入通常更有效率。</span><span class="sxs-lookup"><span data-stu-id="1cea8-197">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="1cea8-198">在此情況下，將會顯示講師的辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="1cea8-198">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="1cea8-199">當使用者選取講師時，將會顯示相關的 `Course` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-199">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="1cea8-200">`Instructor` 與 `Course` 實體具有多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-200">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="1cea8-201">將會針對 `Course` 實體和其相關 `Department` 實體使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-201">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="1cea8-202">在此情況下，個別查詢可能更有效率，因為只需要所選取講師的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-202">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="1cea8-203">這個範例示範如何使用導覽屬性中實體之導覽屬性的積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-203">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="1cea8-204">當使用者選取課程時，將會顯示來自 `Enrollments` 實體的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-204">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="1cea8-205">在上述映像中，將會顯示學生姓名和年級。</span><span class="sxs-lookup"><span data-stu-id="1cea8-205">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="1cea8-206">`Course` 與 `Enrollment` 實體具有一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-206">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="1cea8-207">建立檢視模型</span><span class="sxs-lookup"><span data-stu-id="1cea8-207">Create a view model</span></span>

<span data-ttu-id="1cea8-208">講師頁面會顯示下列三個不同資料表的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-208">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="1cea8-209">需要檢視模型，其包含代表三個資料表的三個屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-209">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="1cea8-210">使用下列程式碼建立 *SchoolViewModels/InstructorIndexData.cs*：</span><span class="sxs-lookup"><span data-stu-id="1cea8-210">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="1cea8-211">Scaffold Instructor 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-211">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1cea8-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1cea8-212">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1cea8-213">遵循 [Scaffold Students 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：</span><span class="sxs-lookup"><span data-stu-id="1cea8-213">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="1cea8-214">建立 *Pages/Instructors* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1cea8-214">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="1cea8-215">使用 `Instructor` 作為模型類別。</span><span class="sxs-lookup"><span data-stu-id="1cea8-215">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="1cea8-216">使用現有內容類別，而非建立新的類別。</span><span class="sxs-lookup"><span data-stu-id="1cea8-216">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1cea8-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1cea8-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1cea8-218">建立 *Pages/Instructors* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1cea8-218">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="1cea8-219">執行下列命令來 Scaffold Instructor 頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-219">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="1cea8-220">**在 Windows 上：**</span><span class="sxs-lookup"><span data-stu-id="1cea8-220">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="1cea8-221">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="1cea8-221">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="1cea8-222">執行應用程式，並流覽至講師頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-222">Run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="1cea8-223">使用下列程式碼更新 *Pages/講師/Index. .cs* ：</span><span class="sxs-lookup"><span data-stu-id="1cea8-223">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_all)]

<span data-ttu-id="1cea8-224">`OnGetAsync` 方法會針對所選取講師的識別碼接受選擇性的路由資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-224">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="1cea8-225">檢查 *Pages/Instructors/Index.cshtml.cs* 檔案中的查詢：</span><span class="sxs-lookup"><span data-stu-id="1cea8-225">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_query)]

<span data-ttu-id="1cea8-226">這個程式碼會針對下列導覽屬性指定積極式載入：</span><span class="sxs-lookup"><span data-stu-id="1cea8-226">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.Course`
    * `Course.Department`

<span data-ttu-id="1cea8-227">當選取講師時，會執行下列程式碼，也就是 `id != null` 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-227">The following code executes when an instructor is selected, that is, `id != null`.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_id)]

<span data-ttu-id="1cea8-228">選取的講師會從檢視模型的講師清單中擷取。</span><span class="sxs-lookup"><span data-stu-id="1cea8-228">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="1cea8-229">視圖模型的 `Courses` 屬性會與所 `Course` 選講師導覽屬性中的實體一起載入 `Courses` 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-229">The view model's `Courses` property is loaded with the `Course` entities from the selected instructor's `Courses` navigation property.</span></span>

<span data-ttu-id="1cea8-230">`Where` 方法會傳回集合。</span><span class="sxs-lookup"><span data-stu-id="1cea8-230">The `Where` method returns a collection.</span></span> <span data-ttu-id="1cea8-231">在此情況下，篩選準則會選取單一實體，因此 `Single` 會呼叫方法將集合轉換成單一 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-231">In this case, the filter select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="1cea8-232">`Instructor`實體提供對 `Course` 導覽屬性的存取。</span><span class="sxs-lookup"><span data-stu-id="1cea8-232">The `Instructor` entity provides access to the `Course` navigation property.</span></span>

<span data-ttu-id="1cea8-233">當集合只有一個項目時，將會在集合上使用 <xref:System.Linq.Enumerable.Single%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="1cea8-233">The <xref:System.Linq.Enumerable.Single%2A> method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="1cea8-234">如果集合是空的或是有多個項目，`Single` 方法會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1cea8-234">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="1cea8-235">替代方法是 <xref:System.Linq.Enumerable.SingleOrDefault%2A> ，如果集合是空的，則會傳回預設值。</span><span class="sxs-lookup"><span data-stu-id="1cea8-235">An alternative is <xref:System.Linq.Enumerable.SingleOrDefault%2A>, which returns a default value if the collection is empty.</span></span> <span data-ttu-id="1cea8-236">針對此查詢，會 `null` 在傳回的預設中。</span><span class="sxs-lookup"><span data-stu-id="1cea8-236">For this query, `null` in the default returned.</span></span>

<span data-ttu-id="1cea8-237">選取課程時，下列程式碼會填入檢視模型的 `Enrollments` 屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-237">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Index.cshtml.cs?name=snippet_enrollment)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="1cea8-238">更新 Instructors [索引] 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-238">Update the instructors Index page</span></span>

<span data-ttu-id="1cea8-239">使用下列程式碼更新 *Pages/Instructors/Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="1cea8-239">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Instructors/Index.cshtml?highlight=1)]

<span data-ttu-id="1cea8-240">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="1cea8-240">The preceding code makes the following changes:</span></span>

  * <span data-ttu-id="1cea8-241">將指示詞更新 `page` 為 `@page "{id:int?}"` 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-241">Updates the `page` directive to `@page "{id:int?}"`.</span></span> <span data-ttu-id="1cea8-242">`"{id:int?}"` 是路由範本。</span><span class="sxs-lookup"><span data-stu-id="1cea8-242">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="1cea8-243">[路由範本](xref:fundamentals/routing#route-template-reference)會將 URL 中的整數查詢字串變更為路由資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-243">The [route template](xref:fundamentals/routing#route-template-reference) changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="1cea8-244">例如，只有在 `@page` 指示詞產生如下的 URL 時，按一下講師的 [選取] 連結：</span><span class="sxs-lookup"><span data-stu-id="1cea8-244">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

    `https://localhost:5001/Instructors?id=2`

    <span data-ttu-id="1cea8-245">當頁面指示詞為時 `@page "{id:int?}"` ，URL 為： `https://localhost:5001/Instructors/2`</span><span class="sxs-lookup"><span data-stu-id="1cea8-245">When the page directive is `@page "{id:int?}"`, the URL is: `https://localhost:5001/Instructors/2`</span></span>

  * <span data-ttu-id="1cea8-246">新增 [辦公室] 資料行，該資料行只有在 `item.OfficeAssignment` 不是 Null 時才會顯示 `item.OfficeAssignment.Location`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-246">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="1cea8-247">因為這是一對零或一關聯性，所有可能沒有相關的 OfficeAssignment 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-247">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

    ```html
    @if (item.OfficeAssignment != null)
    {
        @item.OfficeAssignment.Location
    }
    ```

  * <span data-ttu-id="1cea8-248">新增 [課程] 資料行，以顯示每位講師所教授的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-248">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="1cea8-249">如需此 razor 語法的詳細資訊，請參閱 [明確的行轉換](xref:mvc/views/razor#explicit-line-transition) 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-249">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>
  * <span data-ttu-id="1cea8-250">新增程式碼，將 `class="success"` 動態新增至所選講師和課程的 `tr` 項目。</span><span class="sxs-lookup"><span data-stu-id="1cea8-250">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="1cea8-251">這會使用啟動程序類別設定所選取資料列的背景色彩。</span><span class="sxs-lookup"><span data-stu-id="1cea8-251">This sets a background color for the selected row using a Bootstrap class.</span></span>

    ```html
    string selectedRow = "";
    if (item.CourseID == Model.CourseID)
    {
        selectedRow = "success";
    }
    <tr class="@selectedRow">
    ```

  * <span data-ttu-id="1cea8-252">新增標示為 **選取** 的超連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-252">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="1cea8-253">此連結會將所選取的講師識別碼傳送至 `Index` 方法，並設定背景色彩。</span><span class="sxs-lookup"><span data-stu-id="1cea8-253">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

    ```html
    <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
    ```

  * <span data-ttu-id="1cea8-254">新增所選講師的課程資料表。</span><span class="sxs-lookup"><span data-stu-id="1cea8-254">Adds a table of courses for the selected Instructor.</span></span>
  * <span data-ttu-id="1cea8-255">新增所選課程的學生註冊資料表。</span><span class="sxs-lookup"><span data-stu-id="1cea8-255">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="1cea8-256">執行應用程式，然後選取 [ **講師** ] 索引標籤。此頁面會顯示 `Location` 來自相關實體的 (office) `OfficeAssignment` 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-256">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="1cea8-257">如果 `OfficeAssignment` 為 Null，則會顯示空的資料表資料格。</span><span class="sxs-lookup"><span data-stu-id="1cea8-257">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="1cea8-258">按一下講師的 [選取] 連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-258">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="1cea8-259">資料列樣式會變更，並會顯示指派給該講師的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-259">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="1cea8-260">選取課程，以查看已註冊學生和其年級的清單。</span><span class="sxs-lookup"><span data-stu-id="1cea8-260">Select a course to see the list of enrolled students and their grades.</span></span>

![Instructors [索引] 頁面已選取講師和課程](read-related-data/_static/instructors-index30.png)

## <a name="next-steps"></a><span data-ttu-id="1cea8-262">下一步</span><span class="sxs-lookup"><span data-stu-id="1cea8-262">Next steps</span></span>

<span data-ttu-id="1cea8-263">下一個教學課程會示範如何更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-263">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="1cea8-264">[上一個教學](xref:data/ef-rp/complex-data-model) 
> 課程[下一個教學](xref:data/ef-rp/update-related-data)課程</span><span class="sxs-lookup"><span data-stu-id="1cea8-264">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="1cea8-265">本教學課程說明如何讀取及顯示相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-265">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="1cea8-266">相關資料是 EF Core 載入到導覽屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-266">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="1cea8-267">下圖顯示本教學課程的已完成頁面：</span><span class="sxs-lookup"><span data-stu-id="1cea8-267">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses [索引] 頁面](read-related-data/_static/courses-index30.png)

![Instructors [索引] 頁面](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="1cea8-270">積極式、明確式和消極式載入</span><span class="sxs-lookup"><span data-stu-id="1cea8-270">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="1cea8-271">EF Core 有幾種方式可以將相關資料載入到實體的導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-271">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="1cea8-272">[積極式載入](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-272">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="1cea8-273">積極式載入是指某一類型實體的查詢同時也會載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-273">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="1cea8-274">讀取實體時，將會擷取其相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-274">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="1cea8-275">這通常會導致單一聯結查詢，其可擷取所有需要的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-275">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="1cea8-276">EF Core 將針對某些類型的積極式載入發出多個查詢。</span><span class="sxs-lookup"><span data-stu-id="1cea8-276">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="1cea8-277">發出多個查詢可能比大型單一查詢更有效率。</span><span class="sxs-lookup"><span data-stu-id="1cea8-277">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="1cea8-278">積極式載入是使用 `Include` 和 `ThenInclude` 方法加以指定。</span><span class="sxs-lookup"><span data-stu-id="1cea8-278">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![積極式載入範例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="1cea8-280">當集合導覽包含在內時，積極式載入會傳送多個查詢：</span><span class="sxs-lookup"><span data-stu-id="1cea8-280">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="1cea8-281">針對主查詢傳送一個查詢</span><span class="sxs-lookup"><span data-stu-id="1cea8-281">One query for the main query</span></span> 
  * <span data-ttu-id="1cea8-282">針對負載樹狀結構中的每個集合「邊緣」傳送一個查詢。</span><span class="sxs-lookup"><span data-stu-id="1cea8-282">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="1cea8-283">使用 `Load` 的個別查詢：資料可以在個別查詢中擷取，而 EF Core 會「修正」導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-283">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="1cea8-284">「修正」表示 EF Core 會自動填入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-284">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="1cea8-285">使用 `Load` 的個別查詢更像是明確式載入，而不是積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-285">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![個別查詢範例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="1cea8-287">**注意：** EF Core 會將導覽屬性自動修正為先前已載入至內容實例的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-287">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1cea8-288">即使「未」明確包含導覽屬性的資料，如果先前已載入某些或所有相關實體，仍然可能會填入該屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-288">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="1cea8-289">[明確載入](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-289">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="1cea8-290">第一次讀取實體時，不會擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-290">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1cea8-291">必須撰寫程式碼，才能在需要時擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-291">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="1cea8-292">使用個別查詢的明確式載入會導致多個查詢傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="1cea8-292">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="1cea8-293">透過明確式載入，程式碼會指定要載入的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-293">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="1cea8-294">請使用 `Load` 方法來執行明確式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-294">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="1cea8-295">例如：</span><span class="sxs-lookup"><span data-stu-id="1cea8-295">For example:</span></span>

  ![明確式載入範例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="1cea8-297">[延遲載入](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-297">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1cea8-298">第一次讀取實體時，不會擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-298">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1cea8-299">第一次存取導覽屬性時，將會自動擷取該導覽屬性所需的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-299">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="1cea8-300">每當第一次存取導覽屬性時，查詢會傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="1cea8-300">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="1cea8-301">消極式載入可能會降低效能，例如，當開發人員使用 N + 1 模式、載入父系和列舉子系時。</span><span class="sxs-lookup"><span data-stu-id="1cea8-301">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="1cea8-302">建立 Course 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-302">Create Course pages</span></span>

<span data-ttu-id="1cea8-303">`Course` 實體包含導覽屬性，其中包含相關的 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-303">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="1cea8-305">若要顯示針對課程指派的部門名稱：</span><span class="sxs-lookup"><span data-stu-id="1cea8-305">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="1cea8-306">將相關的 `Department` 實體載入 `Course.Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-306">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="1cea8-307">從 `Department` 實體的 `Name` 屬性取得名稱。</span><span class="sxs-lookup"><span data-stu-id="1cea8-307">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="1cea8-308">Scaffold Course 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-308">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1cea8-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1cea8-309">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1cea8-310">遵循 [Scaffold Student 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：</span><span class="sxs-lookup"><span data-stu-id="1cea8-310">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="1cea8-311">建立 *Pages/Courses* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1cea8-311">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="1cea8-312">使用 `Course` 作為模型類別。</span><span class="sxs-lookup"><span data-stu-id="1cea8-312">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="1cea8-313">使用現有內容類別，而非建立新的類別。</span><span class="sxs-lookup"><span data-stu-id="1cea8-313">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1cea8-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1cea8-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1cea8-315">建立 *Pages/Courses* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1cea8-315">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="1cea8-316">執行下列命令來 scaffold Course 頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-316">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="1cea8-317">**在 Windows 上：**</span><span class="sxs-lookup"><span data-stu-id="1cea8-317">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="1cea8-318">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="1cea8-318">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="1cea8-319">開啟 *Pages/Courses/Index.cshtml.cs* 並檢查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="1cea8-319">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="1cea8-320">Scaffolding 引擎已針對 `Department` 導覽屬性指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-320">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="1cea8-321">`Include` 方法可指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-321">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="1cea8-322">執行應用程式並選取 **課程** 連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-322">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="1cea8-323">部門資料行便會顯示沒有用的 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-323">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="1cea8-324">顯示部門名稱</span><span class="sxs-lookup"><span data-stu-id="1cea8-324">Display the department name</span></span>

<span data-ttu-id="1cea8-325">使用下列程式碼更新 Pages/Courses/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="1cea8-325">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="1cea8-326">上述程式碼會將 `Course` 屬性變更為 `Courses`，並新增 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-326">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="1cea8-327">`AsNoTracking` 可改善效能，因為不會追蹤傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-327">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="1cea8-328">無需追蹤實體的原因是它們不會在目前內容中更新。</span><span class="sxs-lookup"><span data-stu-id="1cea8-328">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="1cea8-329">使用下列程式碼更新 *Pages/Courses/Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="1cea8-329">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="1cea8-330">已對包含 Scaffold 的程式碼進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="1cea8-330">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="1cea8-331">已將 `Course` 屬性名稱變更為 `Courses`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-331">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="1cea8-332">新增顯示 `CourseID` 屬性值的 [編號] 資料行。</span><span class="sxs-lookup"><span data-stu-id="1cea8-332">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="1cea8-333">主索引鍵預設不會進行 Scaffold，因為它們對終端使用者通常沒有任何意義。</span><span class="sxs-lookup"><span data-stu-id="1cea8-333">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="1cea8-334">不過，在此情況下，主索引鍵有意義。</span><span class="sxs-lookup"><span data-stu-id="1cea8-334">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="1cea8-335">變更 [部門] 資料行來顯示部門名稱。</span><span class="sxs-lookup"><span data-stu-id="1cea8-335">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="1cea8-336">此程式碼會顯示已載入到 `Department` 導覽屬性之 `Department` 實體的 `Name` 屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-336">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="1cea8-337">執行應用程式，並選取 [Courses] 索引標籤來查看含有部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="1cea8-337">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses [索引] 頁面](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="1cea8-339">使用 Select 載入相關資料</span><span class="sxs-lookup"><span data-stu-id="1cea8-339">Loading related data with Select</span></span>

<span data-ttu-id="1cea8-340">`OnGetAsync` 方法使用 `Include` 方法載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-340">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="1cea8-341">`Select` 方法是一種替代方案，只會載入所需的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-341">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="1cea8-342">如果是單一項目 (例如 `Department.Name`)，它會使用 SQL INNER JOIN。</span><span class="sxs-lookup"><span data-stu-id="1cea8-342">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="1cea8-343">如果是集合，它會使用另一種資料庫存取，但集合上的 `Include` 運算子也是如此。</span><span class="sxs-lookup"><span data-stu-id="1cea8-343">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="1cea8-344">下列程式碼使用 `Select` 方法載入相關資料：</span><span class="sxs-lookup"><span data-stu-id="1cea8-344">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="1cea8-345">上述程式碼不會傳回任何實體類型，因此不會進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="1cea8-345">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="1cea8-346">如需 EF 追蹤的詳細資訊，請參閱 [追蹤與 No-Tracking 查詢](/ef/core/querying/tracking)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-346">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="1cea8-347">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="1cea8-347">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="1cea8-348">如需完整範例，請參閱 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-348">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="1cea8-349">建立 Instructor 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-349">Create Instructor pages</span></span>

<span data-ttu-id="1cea8-350">本節會 scaffold Instructor 頁面，並將相關的課程和註冊新增至 Instructors 索引頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-350">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="1cea8-351">![講師索引頁面](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="1cea8-351">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="1cea8-352">此頁面將以下列方式讀取和顯示相關資料：</span><span class="sxs-lookup"><span data-stu-id="1cea8-352">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="1cea8-353">講師清單會顯示來自 `OfficeAssignment` 實體 (上述映像中的 Office) 的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-353">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="1cea8-354">`Instructor` 與 `OfficeAssignment` 實體具有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-354">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1cea8-355">積極式載入用於 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-355">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="1cea8-356">若需要顯示相關資料，積極式載入通常更有效率。</span><span class="sxs-lookup"><span data-stu-id="1cea8-356">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="1cea8-357">在此情況下，將會顯示講師的辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="1cea8-357">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="1cea8-358">當使用者選取講師時，將會顯示相關的 `Course` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-358">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="1cea8-359">`Instructor` 與 `Course` 實體具有多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-359">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="1cea8-360">將會針對 `Course` 實體和其相關 `Department` 實體使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-360">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="1cea8-361">在此情況下，個別查詢可能更有效率，因為只需要所選取講師的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-361">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="1cea8-362">這個範例示範如何使用導覽屬性中實體之導覽屬性的積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-362">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="1cea8-363">當使用者選取課程時，將會顯示來自 `Enrollments` 實體的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-363">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="1cea8-364">在上述映像中，將會顯示學生姓名和年級。</span><span class="sxs-lookup"><span data-stu-id="1cea8-364">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="1cea8-365">`Course` 與 `Enrollment` 實體具有一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-365">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="1cea8-366">建立檢視模型</span><span class="sxs-lookup"><span data-stu-id="1cea8-366">Create a view model</span></span>

<span data-ttu-id="1cea8-367">講師頁面會顯示下列三個不同資料表的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-367">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="1cea8-368">需要檢視模型，其包含代表三個資料表的三個屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-368">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="1cea8-369">使用下列程式碼建立 *SchoolViewModels/InstructorIndexData.cs*：</span><span class="sxs-lookup"><span data-stu-id="1cea8-369">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="1cea8-370">Scaffold Instructor 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-370">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1cea8-371">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1cea8-371">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1cea8-372">遵循 [Scaffold Students 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：</span><span class="sxs-lookup"><span data-stu-id="1cea8-372">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="1cea8-373">建立 *Pages/Instructors* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1cea8-373">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="1cea8-374">使用 `Instructor` 作為模型類別。</span><span class="sxs-lookup"><span data-stu-id="1cea8-374">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="1cea8-375">使用現有內容類別，而非建立新的類別。</span><span class="sxs-lookup"><span data-stu-id="1cea8-375">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1cea8-376">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1cea8-376">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1cea8-377">建立 *Pages/Instructors* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="1cea8-377">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="1cea8-378">執行下列命令來 Scaffold Instructor 頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-378">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="1cea8-379">**在 Windows 上：**</span><span class="sxs-lookup"><span data-stu-id="1cea8-379">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="1cea8-380">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="1cea8-380">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="1cea8-381">若要在更新之前查看 Scaffold 頁面的外觀，請執行應用程式並巡覽至 Instructors 頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-381">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="1cea8-382">使用下列程式碼更新 *Pages/講師/Index. .cs* ：</span><span class="sxs-lookup"><span data-stu-id="1cea8-382">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="1cea8-383">`OnGetAsync` 方法會針對所選取講師的識別碼接受選擇性的路由資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-383">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="1cea8-384">檢查 *Pages/Instructors/Index.cshtml.cs* 檔案中的查詢：</span><span class="sxs-lookup"><span data-stu-id="1cea8-384">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="1cea8-385">這個程式碼會針對下列導覽屬性指定積極式載入：</span><span class="sxs-lookup"><span data-stu-id="1cea8-385">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="1cea8-386">請注意 `CourseAssignments` 和 `Course` 之 `Include` 和 `ThenInclude` 方法的重複。</span><span class="sxs-lookup"><span data-stu-id="1cea8-386">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="1cea8-387">若要針對 `Course` 實體的兩個導覽屬性指定積極式載入，則重複是必要的。</span><span class="sxs-lookup"><span data-stu-id="1cea8-387">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="1cea8-388">下列程式碼會在已選取講師 (`id != null`) 時執行。</span><span class="sxs-lookup"><span data-stu-id="1cea8-388">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="1cea8-389">選取的講師會從檢視模型的講師清單中擷取。</span><span class="sxs-lookup"><span data-stu-id="1cea8-389">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="1cea8-390">檢視模型的 `Courses` 屬性則使用 `Course` 實體從該講師的 `CourseAssignments` 導覽屬性載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-390">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="1cea8-391">`Where` 方法會傳回集合。</span><span class="sxs-lookup"><span data-stu-id="1cea8-391">The `Where` method returns a collection.</span></span> <span data-ttu-id="1cea8-392">但是在這種情況下，篩選器會選取單一實體，因此 `Single` 會呼叫方法將集合轉換成單一 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-392">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="1cea8-393">`Instructor` 實體提供對 `CourseAssignments` 屬性的存取。</span><span class="sxs-lookup"><span data-stu-id="1cea8-393">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="1cea8-394">`CourseAssignments` 提供對相關 `Course` 實體的存取。</span><span class="sxs-lookup"><span data-stu-id="1cea8-394">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="1cea8-396">當集合只有一個項目時，將會在集合上使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="1cea8-396">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="1cea8-397">如果集合是空的或是有多個項目，`Single` 方法會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1cea8-397">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="1cea8-398">替代方式是 `SingleOrDefault`，它會在集合是空的時傳回預設值 (在此情況下為 Null)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-398">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="1cea8-399">選取課程時，下列程式碼會填入檢視模型的 `Enrollments` 屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-399">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="1cea8-400">更新 Instructors [索引] 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-400">Update the instructors Index page</span></span>

<span data-ttu-id="1cea8-401">使用下列程式碼更新 *Pages/Instructors/Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="1cea8-401">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="1cea8-402">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="1cea8-402">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1cea8-403">將 `page` 指示詞從 `@page` 更新為 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="1cea8-404">`"{id:int?}"` 是路由範本。</span><span class="sxs-lookup"><span data-stu-id="1cea8-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="1cea8-405">路由範本將 URL 中的整數查詢字串變更為路由資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="1cea8-406">例如，只有在 `@page` 指示詞產生如下的 URL 時，按一下講師的 [選取] 連結：</span><span class="sxs-lookup"><span data-stu-id="1cea8-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="1cea8-407">頁面指示詞是 `@page "{id:int?}"` 時，URL 為：</span><span class="sxs-lookup"><span data-stu-id="1cea8-407">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="1cea8-408">新增 [辦公室] 資料行，該資料行只有在 `item.OfficeAssignment` 不是 Null 時才會顯示 `item.OfficeAssignment.Location`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-408">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="1cea8-409">因為這是一對零或一關聯性，所有可能沒有相關的 OfficeAssignment 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="1cea8-410">新增 [課程] 資料行，以顯示每位講師所教授的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-410">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="1cea8-411">如需此 razor 語法的詳細資訊，請參閱 [明確的行轉換](xref:mvc/views/razor#explicit-line-transition) 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-411">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="1cea8-412">新增程式碼，將 `class="success"` 動態新增至所選講師和課程的 `tr` 項目。</span><span class="sxs-lookup"><span data-stu-id="1cea8-412">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="1cea8-413">這會使用啟動程序類別設定所選取資料列的背景色彩。</span><span class="sxs-lookup"><span data-stu-id="1cea8-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="1cea8-414">新增標示為 **選取** 的超連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-414">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="1cea8-415">此連結會將所選取的講師識別碼傳送至 `Index` 方法，並設定背景色彩。</span><span class="sxs-lookup"><span data-stu-id="1cea8-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="1cea8-416">新增所選講師的課程資料表。</span><span class="sxs-lookup"><span data-stu-id="1cea8-416">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="1cea8-417">新增所選課程的學生註冊資料表。</span><span class="sxs-lookup"><span data-stu-id="1cea8-417">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="1cea8-418">執行應用程式，然後選取 [ **講師** ] 索引標籤。此頁面會顯示 `Location` 來自相關實體的 (office) `OfficeAssignment` 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="1cea8-419">如果 `OfficeAssignment` 為 Null，則會顯示空的資料表資料格。</span><span class="sxs-lookup"><span data-stu-id="1cea8-419">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="1cea8-420">按一下講師的 [選取] 連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-420">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="1cea8-421">資料列樣式會變更，並會顯示指派給該講師的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-421">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="1cea8-422">選取課程，以查看已註冊學生和其年級的清單。</span><span class="sxs-lookup"><span data-stu-id="1cea8-422">Select a course to see the list of enrolled students and their grades.</span></span>

![Instructors [索引] 頁面已選取講師和課程](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="1cea8-424">使用 Single</span><span class="sxs-lookup"><span data-stu-id="1cea8-424">Using Single</span></span>

<span data-ttu-id="1cea8-425">`Single` 方法可以傳入 `Where` 條件，而不是個別呼叫 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="1cea8-425">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="1cea8-426">使用 `Single` 搭配 Where 條件是個人喜好設定。</span><span class="sxs-lookup"><span data-stu-id="1cea8-426">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="1cea8-427">其不會對使用 `Where` 方法提供任何益處。</span><span class="sxs-lookup"><span data-stu-id="1cea8-427">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="1cea8-428">明確式載入</span><span class="sxs-lookup"><span data-stu-id="1cea8-428">Explicit loading</span></span>

<span data-ttu-id="1cea8-429">目前程式碼針對 `Enrollments` 和 `Students` 指定積極式載入：</span><span class="sxs-lookup"><span data-stu-id="1cea8-429">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="1cea8-430">假設使用者很少會想要查看課程中的註冊項目。</span><span class="sxs-lookup"><span data-stu-id="1cea8-430">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="1cea8-431">在此情況下，最佳方法就是在要求時，只載入註冊資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-431">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="1cea8-432">在本節中，`OnGetAsync` 更新為針對 `Enrollments` 和 `Students` 使用明確式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-432">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="1cea8-433">使用下列程式碼更新 *Pages/Instructors/Index.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="1cea8-433">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="1cea8-434">上述程式碼會捨棄註冊和學生資料的 *ThenInclude* 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="1cea8-434">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="1cea8-435">如果選取了課程，則明確載入的程式碼會擷取：</span><span class="sxs-lookup"><span data-stu-id="1cea8-435">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="1cea8-436">所選取課程的 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-436">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="1cea8-437">每個 `Enrollment` 的 `Student` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-437">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="1cea8-438">請注意，上述程式碼會將 `.AsNoTracking()` 標記為註解。</span><span class="sxs-lookup"><span data-stu-id="1cea8-438">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="1cea8-439">針對所追蹤的實體，導覽屬性只能進行明確式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-439">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="1cea8-440">測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="1cea8-440">Test the app.</span></span> <span data-ttu-id="1cea8-441">就使用者的觀點而言，應用程式行為與先前版本相同。</span><span class="sxs-lookup"><span data-stu-id="1cea8-441">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1cea8-442">下一步</span><span class="sxs-lookup"><span data-stu-id="1cea8-442">Next steps</span></span>

<span data-ttu-id="1cea8-443">下一個教學課程會示範如何更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-443">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="1cea8-444">[上一個教學](xref:data/ef-rp/complex-data-model) 
> 課程[下一個教學](xref:data/ef-rp/update-related-data)課程</span><span class="sxs-lookup"><span data-stu-id="1cea8-444">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1cea8-445">在本教學課程中，將會讀取和顯示相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-445">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="1cea8-446">相關資料是 EF Core 載入到導覽屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-446">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="1cea8-447">若您遇到無法解決的問題，請[下載或檢視完整應用程式。](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="1cea8-447">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="1cea8-448">[下載指示](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-448">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="1cea8-449">下圖顯示本教學課程的已完成頁面：</span><span class="sxs-lookup"><span data-stu-id="1cea8-449">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses [索引] 頁面](read-related-data/_static/courses-index.png)

![Instructors [索引] 頁面](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="1cea8-452">相關資料的積極式、明確式和消極式載入</span><span class="sxs-lookup"><span data-stu-id="1cea8-452">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="1cea8-453">EF Core 有幾種方式可以將相關資料載入到實體的導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-453">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="1cea8-454">[積極式載入](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-454">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="1cea8-455">積極式載入是指某一類型實體的查詢同時也會載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-455">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="1cea8-456">讀取實體時，將會擷取其相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-456">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="1cea8-457">這通常會導致單一聯結查詢，其可擷取所有需要的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-457">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="1cea8-458">EF Core 將針對某些類型的積極式載入發出多個查詢。</span><span class="sxs-lookup"><span data-stu-id="1cea8-458">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="1cea8-459">相較於 EF6 中的某些查詢只有單一查詢的情況，發出多個查詢可能更有效率。</span><span class="sxs-lookup"><span data-stu-id="1cea8-459">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="1cea8-460">積極式載入是使用 `Include` 和 `ThenInclude` 方法加以指定。</span><span class="sxs-lookup"><span data-stu-id="1cea8-460">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![積極式載入範例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="1cea8-462">當集合導覽包含在內時，積極式載入會傳送多個查詢：</span><span class="sxs-lookup"><span data-stu-id="1cea8-462">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="1cea8-463">針對主查詢傳送一個查詢</span><span class="sxs-lookup"><span data-stu-id="1cea8-463">One query for the main query</span></span> 
  * <span data-ttu-id="1cea8-464">針對負載樹狀結構中的每個集合「邊緣」傳送一個查詢。</span><span class="sxs-lookup"><span data-stu-id="1cea8-464">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="1cea8-465">使用 `Load` 的個別查詢：資料可以在個別查詢中擷取，而 EF Core 會「修正」導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-465">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="1cea8-466">「修正」表示 EF Core 會自動填入導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-466">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="1cea8-467">使用 `Load` 的個別查詢更像是明確式載入，而不是積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-467">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![個別查詢範例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="1cea8-469">注意：EF Core 會將導覽屬性自動修正為先前已載入至內容執行個體的任何其他實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-469">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="1cea8-470">即使「未」明確包含導覽屬性的資料，如果先前已載入某些或所有相關實體，仍然可能會填入該屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-470">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="1cea8-471">[明確載入](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-471">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="1cea8-472">第一次讀取實體時，不會擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-472">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1cea8-473">必須撰寫程式碼，才能在需要時擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-473">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="1cea8-474">使用個別查詢的明確式載入會導致多個查詢傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="1cea8-474">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="1cea8-475">透過明確式載入，程式碼會指定要載入的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-475">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="1cea8-476">請使用 `Load` 方法來執行明確式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-476">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="1cea8-477">例如：</span><span class="sxs-lookup"><span data-stu-id="1cea8-477">For example:</span></span>

  ![明確式載入範例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="1cea8-479">[延遲載入](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-479">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1cea8-480">[EF Core 已在 2.1 版中新增消極式載入](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-480">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="1cea8-481">第一次讀取實體時，不會擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-481">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="1cea8-482">第一次存取導覽屬性時，將會自動擷取該導覽屬性所需的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-482">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="1cea8-483">每當第一次存取導覽屬性時，查詢會傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="1cea8-483">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="1cea8-484">`Select` 運算子只會載入所需的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-484">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="1cea8-485">建立顯示部門名稱的 Course 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-485">Create a Course page that displays department name</span></span>

<span data-ttu-id="1cea8-486">Course 實體包含導覽屬性，其中包含 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-486">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="1cea8-487">`Department` 實體包含已指派課程的部門。</span><span class="sxs-lookup"><span data-stu-id="1cea8-487">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="1cea8-488">若要在課程清單中顯示所指派部門的名稱：</span><span class="sxs-lookup"><span data-stu-id="1cea8-488">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="1cea8-489">從 `Department` 實體取得 `Name` 屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-489">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="1cea8-490">`Department` 實體來自 `Course.Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-490">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="1cea8-492">Scaffold Course 模型</span><span class="sxs-lookup"><span data-stu-id="1cea8-492">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1cea8-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1cea8-493">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1cea8-494">請遵循[建立學生結構模型](xref:data/ef-rp/intro#scaffold-the-student-model)中的指示，並為模型類別使用 `Course`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-494">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1cea8-495">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1cea8-495">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="1cea8-496">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1cea8-496">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="1cea8-497">上述命令會 Scaffold `Course` 模型。</span><span class="sxs-lookup"><span data-stu-id="1cea8-497">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="1cea8-498">在 Visual Studio 中開啟專案。</span><span class="sxs-lookup"><span data-stu-id="1cea8-498">Open the project in Visual Studio.</span></span>

<span data-ttu-id="1cea8-499">開啟 *Pages/Courses/Index.cshtml.cs* 並檢查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="1cea8-499">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="1cea8-500">Scaffolding 引擎已針對 `Department` 導覽屬性指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-500">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="1cea8-501">`Include` 方法可指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-501">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="1cea8-502">執行應用程式並選取 **課程** 連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-502">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="1cea8-503">部門資料行便會顯示沒有用的 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-503">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="1cea8-504">以下列程式碼更新 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1cea8-504">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="1cea8-505">上述程式碼會新增 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-505">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="1cea8-506">`AsNoTracking` 可改善效能，因為不會追蹤傳回的實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-506">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="1cea8-507">不會追蹤實體的原因是它們不會在目前的內容中更新。</span><span class="sxs-lookup"><span data-stu-id="1cea8-507">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="1cea8-508">以下列醒目提示的標記更新 *Pages/Courses/Index.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="1cea8-508">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="1cea8-509">已對包含 Scaffold 的程式碼進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="1cea8-509">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="1cea8-510">已將標題從「索引」) 變更為「課程」。</span><span class="sxs-lookup"><span data-stu-id="1cea8-510">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="1cea8-511">新增顯示 `CourseID` 屬性值的 [編號] 資料行。</span><span class="sxs-lookup"><span data-stu-id="1cea8-511">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="1cea8-512">主索引鍵預設不會進行 Scaffold，因為它們對終端使用者通常沒有任何意義。</span><span class="sxs-lookup"><span data-stu-id="1cea8-512">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="1cea8-513">不過，在此情況下，主索引鍵有意義。</span><span class="sxs-lookup"><span data-stu-id="1cea8-513">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="1cea8-514">變更 [部門] 資料行來顯示部門名稱。</span><span class="sxs-lookup"><span data-stu-id="1cea8-514">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="1cea8-515">此程式碼會顯示已載入到 `Department` 導覽屬性之 `Department` 實體的 `Name` 屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-515">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="1cea8-516">執行應用程式，並選取 [Courses] 索引標籤來查看含有部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="1cea8-516">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses [索引] 頁面](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="1cea8-518">使用 Select 載入相關資料</span><span class="sxs-lookup"><span data-stu-id="1cea8-518">Loading related data with Select</span></span>

<span data-ttu-id="1cea8-519">`OnGetAsync` 方法使用 `Include` 方法載入相關資料：</span><span class="sxs-lookup"><span data-stu-id="1cea8-519">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="1cea8-520">`Select` 運算子只會載入所需的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-520">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="1cea8-521">如果是單一項目 (例如 `Department.Name`)，它會使用 SQL INNER JOIN。</span><span class="sxs-lookup"><span data-stu-id="1cea8-521">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="1cea8-522">如果是集合，它會使用另一種資料庫存取，但集合上的 `Include` 運算子也是如此。</span><span class="sxs-lookup"><span data-stu-id="1cea8-522">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="1cea8-523">下列程式碼使用 `Select` 方法載入相關資料：</span><span class="sxs-lookup"><span data-stu-id="1cea8-523">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="1cea8-524">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="1cea8-524">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="1cea8-525">如需完整範例，請參閱 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-525">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="1cea8-526">建立顯示課程和註冊的 Instructors 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-526">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="1cea8-527">在本節中，將會建立 Instructors 頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-527">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="1cea8-528">![講師索引頁面](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="1cea8-528">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="1cea8-529">此頁面將以下列方式讀取和顯示相關資料：</span><span class="sxs-lookup"><span data-stu-id="1cea8-529">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="1cea8-530">講師清單會顯示來自 `OfficeAssignment` 實體 (上述映像中的 Office) 的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-530">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="1cea8-531">`Instructor` 與 `OfficeAssignment` 實體具有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-531">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="1cea8-532">積極式載入用於 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-532">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="1cea8-533">若需要顯示相關資料，積極式載入通常更有效率。</span><span class="sxs-lookup"><span data-stu-id="1cea8-533">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="1cea8-534">在此情況下，將會顯示講師的辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="1cea8-534">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="1cea8-535">當使用者選取講師 (上述映像中的 Harui) 時，便會顯示相關的 `Course` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-535">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="1cea8-536">`Instructor` 與 `Course` 實體具有多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-536">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="1cea8-537">將會針對 `Course` 實體和其相關 `Department` 實體使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-537">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="1cea8-538">在此情況下，個別查詢可能更有效率，因為只需要所選取講師的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-538">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="1cea8-539">這個範例示範如何使用導覽屬性中實體之導覽屬性的積極式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-539">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="1cea8-540">當使用者選取課程 (上述映像中的 Chemistry)，隨即顯示 `Enrollments` 中的相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-540">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="1cea8-541">在上述映像中，將會顯示學生姓名和年級。</span><span class="sxs-lookup"><span data-stu-id="1cea8-541">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="1cea8-542">`Course` 與 `Enrollment` 實體具有一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="1cea8-542">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="1cea8-543">建立 Instructor [索引] 檢視的檢視模型</span><span class="sxs-lookup"><span data-stu-id="1cea8-543">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="1cea8-544">講師頁面會顯示下列三個不同資料表的資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-544">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="1cea8-545">建立的檢視模型會包含三個實體代表三個資料表。</span><span class="sxs-lookup"><span data-stu-id="1cea8-545">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="1cea8-546">在 *SchoolViewModels* 資料夾中，以下列程式碼建立 *InstructorIndexData.cs*：</span><span class="sxs-lookup"><span data-stu-id="1cea8-546">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="1cea8-547">Scaffold Instructor 模型</span><span class="sxs-lookup"><span data-stu-id="1cea8-547">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1cea8-548">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1cea8-548">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1cea8-549">請遵循[建立學生結構模型](xref:data/ef-rp/intro#scaffold-the-student-model)中的指示，並為模型類別使用 `Instructor`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-549">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1cea8-550">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1cea8-550">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="1cea8-551">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1cea8-551">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="1cea8-552">上述命令會 Scaffold `Instructor` 模型。</span><span class="sxs-lookup"><span data-stu-id="1cea8-552">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="1cea8-553">執行應用程式並巡覽至講師頁面。</span><span class="sxs-lookup"><span data-stu-id="1cea8-553">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="1cea8-554">以下列程式碼取代 *Pages/Instructors/Index.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="1cea8-554">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="1cea8-555">`OnGetAsync` 方法會針對所選取講師的識別碼接受選擇性的路由資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-555">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="1cea8-556">檢查 *Pages/Instructors/Index.cshtml.cs* 檔案中的查詢：</span><span class="sxs-lookup"><span data-stu-id="1cea8-556">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="1cea8-557">查詢具有兩個 Include：</span><span class="sxs-lookup"><span data-stu-id="1cea8-557">The query has two includes:</span></span>

* <span data-ttu-id="1cea8-558">`OfficeAssignment`：顯示在 [Instructors 檢視](#IP)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-558">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="1cea8-559">`CourseAssignments`：它顯示所教授的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-559">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="1cea8-560">更新 Instructors [索引] 頁面</span><span class="sxs-lookup"><span data-stu-id="1cea8-560">Update the instructors Index page</span></span>

<span data-ttu-id="1cea8-561">以下列標記更新 *Pages/Instructors/Index.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="1cea8-561">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="1cea8-562">上述標記會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="1cea8-562">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="1cea8-563">將 `page` 指示詞從 `@page` 更新為 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-563">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="1cea8-564">`"{id:int?}"` 是路由範本。</span><span class="sxs-lookup"><span data-stu-id="1cea8-564">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="1cea8-565">路由範本將 URL 中的整數查詢字串變更為路由資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-565">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="1cea8-566">例如，只有在 `@page` 指示詞產生如下的 URL 時，按一下講師的 [選取] 連結：</span><span class="sxs-lookup"><span data-stu-id="1cea8-566">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="1cea8-567">頁面指示詞是 `@page "{id:int?}"` 時，先前的 URL 為：</span><span class="sxs-lookup"><span data-stu-id="1cea8-567">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="1cea8-568">頁面標題是 **Instructors**。</span><span class="sxs-lookup"><span data-stu-id="1cea8-568">Page title is **Instructors**.</span></span>
* <span data-ttu-id="1cea8-569">新增 [辦公室] 資料行，該資料行只有在 `item.OfficeAssignment` 不是 Null 時才會顯示 `item.OfficeAssignment.Location`。</span><span class="sxs-lookup"><span data-stu-id="1cea8-569">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="1cea8-570">因為這是一對零或一關聯性，所有可能沒有相關的 OfficeAssignment 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-570">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="1cea8-571">新增 [課程] 資料行，以顯示每位講師所教授的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-571">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="1cea8-572">如需此 razor 語法的詳細資訊，請參閱 [明確的行轉換](xref:mvc/views/razor#explicit-line-transition) 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-572">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="1cea8-573">新增程式碼，將 `class="success"` 動態新增至所選取講師的 `tr` 項目。</span><span class="sxs-lookup"><span data-stu-id="1cea8-573">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="1cea8-574">這會使用啟動程序類別設定所選取資料列的背景色彩。</span><span class="sxs-lookup"><span data-stu-id="1cea8-574">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="1cea8-575">新增標示為 **選取** 的超連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-575">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="1cea8-576">此連結會將所選取的講師識別碼傳送至 `Index` 方法，並設定背景色彩。</span><span class="sxs-lookup"><span data-stu-id="1cea8-576">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="1cea8-577">執行應用程式，然後選取 [ **講師** ] 索引標籤。此頁面會顯示 `Location` 來自相關實體的 (office) `OfficeAssignment` 。</span><span class="sxs-lookup"><span data-stu-id="1cea8-577">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="1cea8-578">如果 OfficeAssignment 是 Null，就會顯示空的資料表資料格。</span><span class="sxs-lookup"><span data-stu-id="1cea8-578">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="1cea8-579">按一下 **選取** 連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-579">Click on the **Select** link.</span></span> <span data-ttu-id="1cea8-580">資料列樣式變更。</span><span class="sxs-lookup"><span data-stu-id="1cea8-580">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="1cea8-581">新增選取的講師所教授的課程</span><span class="sxs-lookup"><span data-stu-id="1cea8-581">Add courses taught by selected instructor</span></span>

<span data-ttu-id="1cea8-582">以下列程式碼更新 *Pages/Instructors/Index.cshtml.cs* 中的 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1cea8-582">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="1cea8-583">新增 `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="1cea8-583">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="1cea8-584">檢查已更新的查詢：</span><span class="sxs-lookup"><span data-stu-id="1cea8-584">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="1cea8-585">上述查詢會新增 `Department` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-585">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="1cea8-586">下列程式碼會在已選取講師 (`id != null`) 時執行。</span><span class="sxs-lookup"><span data-stu-id="1cea8-586">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="1cea8-587">選取的講師會從檢視模型的講師清單中擷取。</span><span class="sxs-lookup"><span data-stu-id="1cea8-587">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="1cea8-588">檢視模型的 `Courses` 屬性則使用 `Course` 實體從該講師的 `CourseAssignments` 導覽屬性載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-588">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="1cea8-589">`Where` 方法會傳回集合。</span><span class="sxs-lookup"><span data-stu-id="1cea8-589">The `Where` method returns a collection.</span></span> <span data-ttu-id="1cea8-590">在上述 `Where` 方法中，只會傳回一個單一 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-590">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="1cea8-591">`Single` 方法會將集合轉換成單一 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-591">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="1cea8-592">`Instructor` 實體提供對 `CourseAssignments` 屬性的存取。</span><span class="sxs-lookup"><span data-stu-id="1cea8-592">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="1cea8-593">`CourseAssignments` 提供對相關 `Course` 實體的存取。</span><span class="sxs-lookup"><span data-stu-id="1cea8-593">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="1cea8-595">當集合只有一個項目時，將會在集合上使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="1cea8-595">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="1cea8-596">如果集合是空的或是有多個項目，`Single` 方法會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="1cea8-596">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="1cea8-597">替代方式是 `SingleOrDefault`，它會在集合是空的時傳回預設值 (在此情況下為 Null)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-597">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="1cea8-598">在空集合上使用 `SingleOrDefault`：</span><span class="sxs-lookup"><span data-stu-id="1cea8-598">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="1cea8-599">造成例外狀況 (由於嘗試在 Null 參考上尋找 `Courses` 屬性)。</span><span class="sxs-lookup"><span data-stu-id="1cea8-599">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="1cea8-600">例外狀況訊息會不太清楚地指出問題的原因。</span><span class="sxs-lookup"><span data-stu-id="1cea8-600">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="1cea8-601">選取課程時，下列程式碼會填入檢視模型的 `Enrollments` 屬性：</span><span class="sxs-lookup"><span data-stu-id="1cea8-601">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="1cea8-602">將下列標記新增至 *Pages/講師/Index. cshtml* Razor 頁面的結尾：</span><span class="sxs-lookup"><span data-stu-id="1cea8-602">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="1cea8-603">當選取講師時，上述標記會顯示與講師相關的課程。</span><span class="sxs-lookup"><span data-stu-id="1cea8-603">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="1cea8-604">測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="1cea8-604">Test the app.</span></span> <span data-ttu-id="1cea8-605">按一下講師頁面上的 **選取** 連結。</span><span class="sxs-lookup"><span data-stu-id="1cea8-605">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="1cea8-606">顯示學生資料</span><span class="sxs-lookup"><span data-stu-id="1cea8-606">Show student data</span></span>

<span data-ttu-id="1cea8-607">在本節中，應用程式會更新以顯示所選取課程的學生資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-607">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="1cea8-608">以下列程式碼更新 *Pages/Instructors/Index.cshtml.cs* 中 `OnGetAsync` 方法的查詢：</span><span class="sxs-lookup"><span data-stu-id="1cea8-608">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="1cea8-609">更新 *Pages/Instructors/Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="1cea8-609">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="1cea8-610">將下列標記新增至檔案結尾：</span><span class="sxs-lookup"><span data-stu-id="1cea8-610">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="1cea8-611">上述標記會顯示註冊所選取課程的學生清單。</span><span class="sxs-lookup"><span data-stu-id="1cea8-611">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="1cea8-612">重新整理頁面，然後選取講師。</span><span class="sxs-lookup"><span data-stu-id="1cea8-612">Refresh the page and select an instructor.</span></span> <span data-ttu-id="1cea8-613">選取課程，以查看已註冊學生和其年級的清單。</span><span class="sxs-lookup"><span data-stu-id="1cea8-613">Select a course to see the list of enrolled students and their grades.</span></span>

![Instructors [索引] 頁面已選取講師和課程](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="1cea8-615">使用 Single</span><span class="sxs-lookup"><span data-stu-id="1cea8-615">Using Single</span></span>

<span data-ttu-id="1cea8-616">`Single` 方法可以傳入 `Where` 條件，而不是個別呼叫 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="1cea8-616">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="1cea8-617">比起使用 `Where`，上述 `Single` 方法並沒有任何優勢。</span><span class="sxs-lookup"><span data-stu-id="1cea8-617">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="1cea8-618">某些開發人員偏好使用 `Single` 方法樣式。</span><span class="sxs-lookup"><span data-stu-id="1cea8-618">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="1cea8-619">明確式載入</span><span class="sxs-lookup"><span data-stu-id="1cea8-619">Explicit loading</span></span>

<span data-ttu-id="1cea8-620">目前程式碼針對 `Enrollments` 和 `Students` 指定積極式載入：</span><span class="sxs-lookup"><span data-stu-id="1cea8-620">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="1cea8-621">假設使用者很少會想要查看課程中的註冊項目。</span><span class="sxs-lookup"><span data-stu-id="1cea8-621">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="1cea8-622">在此情況下，最佳方法就是在要求時，只載入註冊資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-622">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="1cea8-623">在本節中，`OnGetAsync` 更新為針對 `Enrollments` 和 `Students` 使用明確式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-623">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="1cea8-624">使用下列程式碼更新 `OnGetAsync`：</span><span class="sxs-lookup"><span data-stu-id="1cea8-624">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="1cea8-625">上述程式碼會捨棄註冊和學生資料的 *ThenInclude* 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="1cea8-625">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="1cea8-626">如果選取了課程，醒目提示的程式碼就會擷取：</span><span class="sxs-lookup"><span data-stu-id="1cea8-626">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="1cea8-627">所選取課程的 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-627">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="1cea8-628">每個 `Enrollment` 的 `Student` 實體。</span><span class="sxs-lookup"><span data-stu-id="1cea8-628">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="1cea8-629">請注意，上述程式碼會將 `.AsNoTracking()` 註解化。</span><span class="sxs-lookup"><span data-stu-id="1cea8-629">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="1cea8-630">針對所追蹤的實體，導覽屬性只能進行明確式載入。</span><span class="sxs-lookup"><span data-stu-id="1cea8-630">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="1cea8-631">測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="1cea8-631">Test the app.</span></span> <span data-ttu-id="1cea8-632">就使用者的觀點而言，應用程式行為與之前的版本相同。</span><span class="sxs-lookup"><span data-stu-id="1cea8-632">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="1cea8-633">下一個教學課程會示範如何更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="1cea8-633">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1cea8-634">其他資源</span><span class="sxs-lookup"><span data-stu-id="1cea8-634">Additional resources</span></span>

* [<span data-ttu-id="1cea8-635">這個教學課程的 YouTube 版本 (第 1 部分)</span><span class="sxs-lookup"><span data-stu-id="1cea8-635">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="1cea8-636">這個教學課程的 YouTube 版本 (第 2 部分)</span><span class="sxs-lookup"><span data-stu-id="1cea8-636">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="1cea8-637">[上一個](xref:data/ef-rp/complex-data-model) 
>[下一步](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="1cea8-637">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end