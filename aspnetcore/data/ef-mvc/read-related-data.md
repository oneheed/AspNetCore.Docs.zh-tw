---
title: 教學課程：使用 EF Core 讀取相關資料-ASP.NET MVC
description: 在此教學課程中，您將讀取並顯示相關資料-- 也就是 Entity Framework 載入到導覽屬性的資料。
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 9ea3bd7aaa075ae4601af51c7cc90f28a884a096
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587888"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="0419d-103">教學課程：使用 EF Core 讀取相關資料-ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="0419d-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="0419d-104">在上一個教學課程中，您已完成 School 資料模型。</span><span class="sxs-lookup"><span data-stu-id="0419d-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="0419d-105">在此教學課程中，您將讀取並顯示相關資料-- 也就是 Entity Framework 載入到導覽屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="0419d-106">下列圖例顯示了您將操作的頁面。</span><span class="sxs-lookup"><span data-stu-id="0419d-106">The following illustrations show the pages that you'll work with.</span></span>

![Courses [索引] 頁面](read-related-data/_static/courses-index.png)

![Instructors [索引] 頁面](read-related-data/_static/instructors-index.png)

<span data-ttu-id="0419d-109">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="0419d-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0419d-110">了解如何載入相關資料</span><span class="sxs-lookup"><span data-stu-id="0419d-110">Learn how to load related data</span></span>
> * <span data-ttu-id="0419d-111">建立 Courses 頁面</span><span class="sxs-lookup"><span data-stu-id="0419d-111">Create a Courses page</span></span>
> * <span data-ttu-id="0419d-112">建立 Instructors 頁面</span><span class="sxs-lookup"><span data-stu-id="0419d-112">Create an Instructors page</span></span>
> * <span data-ttu-id="0419d-113">了解明確載入</span><span class="sxs-lookup"><span data-stu-id="0419d-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0419d-114">必要條件</span><span class="sxs-lookup"><span data-stu-id="0419d-114">Prerequisites</span></span>

* [<span data-ttu-id="0419d-115">建立複雜的資料模型</span><span class="sxs-lookup"><span data-stu-id="0419d-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="0419d-116">了解如何載入相關資料</span><span class="sxs-lookup"><span data-stu-id="0419d-116">Learn how to load related data</span></span>

<span data-ttu-id="0419d-117">Entity Framework 等物件關聯式對應 (ORM) 有幾種方式可以將相關資料載入到實體的導覽屬性：</span><span class="sxs-lookup"><span data-stu-id="0419d-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="0419d-118">積極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-118">Eager loading.</span></span> <span data-ttu-id="0419d-119">讀取實體時，將會同時擷取其相關資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="0419d-120">這通常會導致單一聯結查詢，其可擷取所有需要的資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="0419d-121">您可以使用 `Include` 和 `ThenInclude` 方法，在 Entity Framework Core 中指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![積極式載入範例](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="0419d-123">您可以在個別查詢中擷取一些資料，而 EF 會「修正」導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="0419d-124">也就是說，EF 會自動新增個別擷取的實體，其中它們屬於先前擷取之實體的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="0419d-125">對於擷取相關資料的查詢，您可以使用 `Load` 方法，而不是傳回清單或物件的方法，例如 `ToList` 或 `Single`。</span><span class="sxs-lookup"><span data-stu-id="0419d-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![個別查詢範例](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="0419d-127">明確式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-127">Explicit loading.</span></span> <span data-ttu-id="0419d-128">第一次讀取實體時，不會擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="0419d-129">您可以撰寫程式碼，以擷取需要的相關資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="0419d-130">如同使用個別查詢的積極式載入，明確式載入會導致多個查詢傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="0419d-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="0419d-131">不同之處在於，使用明確式載入時，程式碼會指定要載入的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="0419d-132">在 Entity Framework Core 1.1 中，您可以使用 `Load` 方法以執行明確式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="0419d-133">例如：</span><span class="sxs-lookup"><span data-stu-id="0419d-133">For example:</span></span>

  ![明確式載入範例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="0419d-135">消極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-135">Lazy loading.</span></span> <span data-ttu-id="0419d-136">第一次讀取實體時，不會擷取相關資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="0419d-137">不過，第一次嘗試存取導覽屬性時，將會自動擷取該導覽屬性所需的資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="0419d-138">每當您第一次嘗試從導覽屬性取得資料時，就會傳送查詢到資料庫。</span><span class="sxs-lookup"><span data-stu-id="0419d-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="0419d-139">Entity Framework Core 1.0 不支援消極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="0419d-140">效能考量</span><span class="sxs-lookup"><span data-stu-id="0419d-140">Performance considerations</span></span>

<span data-ttu-id="0419d-141">如果您知道擷取的每個實體需要相關資料，積極式載入通常可以提供最佳效能，因為傳送至資料庫的單一查詢通常比所擷取每個實體的個別查詢更有效率。</span><span class="sxs-lookup"><span data-stu-id="0419d-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="0419d-142">例如，假設每個部門各有十個相關課程。</span><span class="sxs-lookup"><span data-stu-id="0419d-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="0419d-143">所有相關資料的積極式載入會導致只有單一 (聯結) 查詢，以及資料庫的單一來回行程。</span><span class="sxs-lookup"><span data-stu-id="0419d-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="0419d-144">每個部門課程的個別查詢則會導致資料庫的十一個來回行程。</span><span class="sxs-lookup"><span data-stu-id="0419d-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="0419d-145">當延遲很高時，資料庫的額外來回行程對效能特別不利。</span><span class="sxs-lookup"><span data-stu-id="0419d-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="0419d-146">相反地，在某些案例中，個別查詢更有效率。</span><span class="sxs-lookup"><span data-stu-id="0419d-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="0419d-147">在單一查詢中進行所有相關資料的積極式載入時，可能會導致產生非常複雜的聯結，SQL Server 無法有效率地進行處理。</span><span class="sxs-lookup"><span data-stu-id="0419d-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="0419d-148">或者，如果您只需要針對所處理之實體集的子集存取實體的導覽屬性，則執行個別查詢可能會更好；因為預先進行所有項目的積極式載入可能會擷取比您所需更多的資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="0419d-149">如果效能嚴重不足，最好先測試這兩種方式的效能，才能做出最好的選擇。</span><span class="sxs-lookup"><span data-stu-id="0419d-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="0419d-150">建立 Courses 頁面</span><span class="sxs-lookup"><span data-stu-id="0419d-150">Create a Courses page</span></span>

<span data-ttu-id="0419d-151">Course 實體包括一個導覽屬性，其中包含已指派課程之部門的 Department 實體。</span><span class="sxs-lookup"><span data-stu-id="0419d-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="0419d-152">若要在課程清單中顯示所指派部門的名稱，您需要從位於 `Course.Department` 導覽屬性的 Department 實體中取得 Name 屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="0419d-153">針對 Course 實體類型建立名為 CoursesController 的控制器，並對 **使用 Entity Framework 執行檢視的 MVC 控制器** 框架使用先前針對學生控制器使用的相同選項，如下圖所示：</span><span class="sxs-lookup"><span data-stu-id="0419d-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![新增課程控制器](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="0419d-155">開啟 *CoursesController.cs*，並檢查 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="0419d-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="0419d-156">自動 Scaffolding 已使用 `Include` 方法，針對 `Department` 導覽屬性指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="0419d-157">以下列程式碼取代 `Index` 方法，以針對傳回 Course 實體的 `IQueryable` 使用更合適的名稱 (`courses` 而不是 `schoolContext`)：</span><span class="sxs-lookup"><span data-stu-id="0419d-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="0419d-158">開啟 *Views/Courses/Index.cshtml*，並以下列程式碼取代範本程式碼。</span><span class="sxs-lookup"><span data-stu-id="0419d-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="0419d-159">所做的變更已醒目提示：</span><span class="sxs-lookup"><span data-stu-id="0419d-159">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="0419d-160">您已對包含 Scaffold 的程式碼進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="0419d-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="0419d-161">已將標題從「索引」) 變更為「課程」。</span><span class="sxs-lookup"><span data-stu-id="0419d-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="0419d-162">新增顯示 `CourseID` 屬性值的 [編號] 資料行。</span><span class="sxs-lookup"><span data-stu-id="0419d-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="0419d-163">主索引鍵預設不會進行 Scaffold，因為它們對終端使用者通常沒有任何意義。</span><span class="sxs-lookup"><span data-stu-id="0419d-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="0419d-164">不過，在此情況下主索引鍵有意義，因此您想要顯示它。</span><span class="sxs-lookup"><span data-stu-id="0419d-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="0419d-165">變更 [部門] 資料行來顯示部門名稱。</span><span class="sxs-lookup"><span data-stu-id="0419d-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="0419d-166">此程式碼會顯示已載入到 `Department` 導覽屬性之 Department 實體的 `Name` 屬性：</span><span class="sxs-lookup"><span data-stu-id="0419d-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="0419d-167">執行應用程式，並選取 [Courses] 索引標籤來查看含有部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="0419d-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses [索引] 頁面](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="0419d-169">建立 Instructors 頁面</span><span class="sxs-lookup"><span data-stu-id="0419d-169">Create an Instructors page</span></span>

<span data-ttu-id="0419d-170">在本節中，您將建立 Instructor 實體的控制器和檢視，以顯示 Instructors 頁面：</span><span class="sxs-lookup"><span data-stu-id="0419d-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Instructors [索引] 頁面](read-related-data/_static/instructors-index.png)

<span data-ttu-id="0419d-172">此頁面將以下列方式讀取和顯示相關資料：</span><span class="sxs-lookup"><span data-stu-id="0419d-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="0419d-173">講師清單會顯示來自 OfficeAssignment 實體的相關資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="0419d-174">Instructor 與 OfficeAssignment 實體具有一對零或一關聯性。</span><span class="sxs-lookup"><span data-stu-id="0419d-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="0419d-175">您將針對 OfficeAssignment 實體使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="0419d-176">如上所述，當您需要主要資料表中所有擷取資料列的相關資料時，積極式載入通常更有效率。</span><span class="sxs-lookup"><span data-stu-id="0419d-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="0419d-177">在此情況下，您可能想要顯示所有已呈現講師的辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="0419d-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="0419d-178">當使用者選取講師時，將會顯示相關的 Course 實體。</span><span class="sxs-lookup"><span data-stu-id="0419d-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="0419d-179">Instructor 與 Course 實體具有多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0419d-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="0419d-180">您將針對 Course 實體和其相關的 Department 實體使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="0419d-181">在此情況下，個別查詢可能更有效率，因為您只需要所選取講師的課程。</span><span class="sxs-lookup"><span data-stu-id="0419d-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="0419d-182">不過，這個範例會示範如何在本身處於導覽屬性內的實體中，針對導覽屬性使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="0419d-183">當使用者選取課程時，將會顯示來自 Enrollments 實體集的相關資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="0419d-184">Course 與 Enrollment 實體具有一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="0419d-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="0419d-185">您將針對 Enrollment 實體和其相關的 Student 實體使用個別查詢。</span><span class="sxs-lookup"><span data-stu-id="0419d-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="0419d-186">建立 Instructor [索引] 檢視的檢視模型</span><span class="sxs-lookup"><span data-stu-id="0419d-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="0419d-187">Instructors 頁面會顯示下列三個不同資料表的資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="0419d-188">因此，您將建立包含三個屬性的檢視模型，每個保留其中一個資料表的資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="0419d-189">在 *SchoolViewModels* 資料夾中建立 *InstructorIndexData.cs*，然後以下列程式碼取代現有的程式碼：</span><span class="sxs-lookup"><span data-stu-id="0419d-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="0419d-190">建立 Instructor 控制器和檢視</span><span class="sxs-lookup"><span data-stu-id="0419d-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="0419d-191">使用 EF 讀取/寫入動作建立 Instructors 控制器，如下圖所示：</span><span class="sxs-lookup"><span data-stu-id="0419d-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![新增 Instructors 控制器](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="0419d-193">開啟 *InstructorsController.cs*，並針對 ViewModels 命名空間新增 using 陳述式：</span><span class="sxs-lookup"><span data-stu-id="0419d-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="0419d-194">以下列程式碼取代 Index 方法，以便執行相關資料的積極式載入，並將其置於檢視模型中。</span><span class="sxs-lookup"><span data-stu-id="0419d-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="0419d-195">此方法可接受選擇性的路由資料 (`id`) 和查詢字串參數 (`courseID`)，以提供所選取講師和所選取課程的識別碼值。</span><span class="sxs-lookup"><span data-stu-id="0419d-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="0419d-196">這些參數由頁面上的 **選取** 超連結提供。</span><span class="sxs-lookup"><span data-stu-id="0419d-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="0419d-197">此程式碼是從建立檢視模型的執行個體，並將其置於講師清單開始。</span><span class="sxs-lookup"><span data-stu-id="0419d-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="0419d-198">這個程式碼會針對 `Instructor.OfficeAssignment` 和 `Instructor.CourseAssignments` 導覽屬性指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="0419d-199">在 `CourseAssignments` 屬性內載入 `Course` 屬性，並在其中載入 `Enrollments` 和 `Department` 屬性，而在每個 `Enrollment` 實體內載入 `Student` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="0419d-200">由於檢視一律需要 OfficeAssignment 實體，因此在相同查詢中擷取該實體更有效率。</span><span class="sxs-lookup"><span data-stu-id="0419d-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="0419d-201">在網頁上選取講師時需要 Course 實體，因此只有在選取課程時的頁面顯示頻率高於未選取時，單一查詢才優於多個查詢。</span><span class="sxs-lookup"><span data-stu-id="0419d-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="0419d-202">此程式碼會重複 `CourseAssignments` 和 `Course`，因為您需要來自 `Course` 的兩個屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="0419d-203">`ThenInclude` 呼叫的第一個字串會取得 `CourseAssignment.Course`、`Course.Enrollments` 和 `Enrollment.Student`。</span><span class="sxs-lookup"><span data-stu-id="0419d-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="0419d-204">此時，程式碼中的另一個 `ThenInclude` 將用於您不需要的 `Student` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="0419d-205">但呼叫 `Include` 會使用 `Instructor`　屬性從頭開始，所以您必須再次瀏覽鏈結，這次請指定 `Course.Department` 而不是 `Course.Enrollments`。</span><span class="sxs-lookup"><span data-stu-id="0419d-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="0419d-206">下列程式碼會在已選取講師時執行。</span><span class="sxs-lookup"><span data-stu-id="0419d-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="0419d-207">選取的講師會從檢視模型的講師清單中擷取。</span><span class="sxs-lookup"><span data-stu-id="0419d-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="0419d-208">檢視模型的 `Courses` 屬性則使用 Course 實體從該講師的 `CourseAssignments` 導覽屬性載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="0419d-209">`Where` 方法會傳回集合，但在此情況下，傳遞至該方法的準則將導致只傳回單一 Instructor 實體。</span><span class="sxs-lookup"><span data-stu-id="0419d-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="0419d-210">`Single` 方法會將集合轉換單一 Instructor 實體，這可讓您存取該實體的 `CourseAssignments` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="0419d-211">`CourseAssignments` 屬性包含 `CourseAssignment` 實體，您只想要來自該實體的相關 `Course` 實體。</span><span class="sxs-lookup"><span data-stu-id="0419d-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="0419d-212">當您知道集合只會有一個項目時，就可以在集合上使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="0419d-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="0419d-213">如果傳遞的集合是空的或有多個項目，Single 方法會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0419d-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="0419d-214">替代方式是 `SingleOrDefault`，它會在集合是空的時傳回預設值 (在此情況下為 Null)。</span><span class="sxs-lookup"><span data-stu-id="0419d-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="0419d-215">不過，在此情況下仍然會造成例外狀況 (由於嘗試在 Null 參考上尋找 `Courses` 屬性)，而例外狀況訊息會不太清楚地指出問題的原因。</span><span class="sxs-lookup"><span data-stu-id="0419d-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="0419d-216">當您呼叫 `Single` 方法時，也可以傳入 Where 條件，而不是分別呼叫 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="0419d-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="0419d-217">不要這樣撰寫：</span><span class="sxs-lookup"><span data-stu-id="0419d-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="0419d-218">接下來，如果已選取課程，則會從檢視模型的課程清單中擷取選取的課程。</span><span class="sxs-lookup"><span data-stu-id="0419d-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="0419d-219">然後，檢視模型的 `Enrollments` 屬性會使用 Enrollment 實體從該課程的 `Enrollments` 導覽屬性載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="0419d-220">修改 Instructor [索引] 檢視</span><span class="sxs-lookup"><span data-stu-id="0419d-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="0419d-221">在 *Views/Instructors/Index.cshtml* 中，以下列程式碼取代範本程式碼。</span><span class="sxs-lookup"><span data-stu-id="0419d-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="0419d-222">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="0419d-222">The changes are highlighted.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

<span data-ttu-id="0419d-223">您已對現有程式碼進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="0419d-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="0419d-224">已將模型類別變更為 `InstructorIndexData`。</span><span class="sxs-lookup"><span data-stu-id="0419d-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="0419d-225">已將頁面標題從 **索引** 變更為 **講師**。</span><span class="sxs-lookup"><span data-stu-id="0419d-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="0419d-226">新增 [辦公室] 資料行，該資料行只有在 `item.OfficeAssignment` 不是 Null 時才會顯示 `item.OfficeAssignment.Location`。</span><span class="sxs-lookup"><span data-stu-id="0419d-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="0419d-227">(因為這是一對零或一關聯性，所有可能沒有相關的 OfficeAssignment 實體。)</span><span class="sxs-lookup"><span data-stu-id="0419d-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="0419d-228">新增 [課程] 資料行，以顯示每位講師所教授的課程。</span><span class="sxs-lookup"><span data-stu-id="0419d-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="0419d-229">如需詳細資訊，請參閱語法文章的 [明確行轉換](xref:mvc/views/razor#explicit-line-transition) 一節 Razor 。</span><span class="sxs-lookup"><span data-stu-id="0419d-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="0419d-230">加入程式碼，有條件地將啟動程式 CSS 類別加入至所 `tr` 選講師的元素中。</span><span class="sxs-lookup"><span data-stu-id="0419d-230">Added code that conditionally adds a Bootstrap CSS class to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="0419d-231">這個類別會設定所選取資料列的背景色彩。</span><span class="sxs-lookup"><span data-stu-id="0419d-231">This class sets a background color for the selected row.</span></span>

* <span data-ttu-id="0419d-232">在每個資料列的其他連結之前，立即新增標示為 **選取** 的超連結，這會使得選取的講師識別碼傳送到 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="0419d-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="0419d-233">執行應用程式，然後選取 [ **講師** ] 索引標籤。當沒有相關的 OfficeAssignment 實體時，此頁面會顯示相關 OfficeAssignment 實體的 Location 屬性和空白資料表單元格。</span><span class="sxs-lookup"><span data-stu-id="0419d-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Instructors [索引] 頁面未選取任何項目](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="0419d-235">在 *Views/Instructors/Index.cshtml* 檔案的結束資料表項目 (在檔案的結尾) 之後，新增下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="0419d-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="0419d-236">當選取講師時，此程式碼會顯示與講師相關的課程。</span><span class="sxs-lookup"><span data-stu-id="0419d-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=63-98)]

<span data-ttu-id="0419d-237">此程式碼會讀取檢視模型的 `Courses` 屬性以顯示課程清單。</span><span class="sxs-lookup"><span data-stu-id="0419d-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="0419d-238">它也會提供 **選取** 超連結，將所選取課程的識別碼傳送至 `Index` 動作方法。</span><span class="sxs-lookup"><span data-stu-id="0419d-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="0419d-239">重新整理頁面，然後選取講師。</span><span class="sxs-lookup"><span data-stu-id="0419d-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="0419d-240">現在您會看到一個方格，其中顯示指派給所選取講師的課程，而且在每個課程中，您可以看到指派的部門名稱。</span><span class="sxs-lookup"><span data-stu-id="0419d-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Instructors [索引] 頁面已選取講師](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="0419d-242">在您剛才新增的程式碼區塊之後，新增下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="0419d-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="0419d-243">這會在選取課程時，顯示已註冊該課程的學生清單。</span><span class="sxs-lookup"><span data-stu-id="0419d-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="0419d-244">此程式碼會讀取檢視模型的 Enrollments 屬性，以顯示已註冊課程的學生清單。</span><span class="sxs-lookup"><span data-stu-id="0419d-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="0419d-245">再次重新整理頁面，然後選取講師。</span><span class="sxs-lookup"><span data-stu-id="0419d-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="0419d-246">接著選取課程，以查看已註冊學生和其年級的清單。</span><span class="sxs-lookup"><span data-stu-id="0419d-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Instructors [索引] 頁面已選取講師和課程](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="0419d-248">關於明確載入</span><span class="sxs-lookup"><span data-stu-id="0419d-248">About explicit loading</span></span>

<span data-ttu-id="0419d-249">當您在 *InstructorsController.cs* 中擷取講師清單時，已針對 `CourseAssignments` 導覽屬性指定積極式載入。</span><span class="sxs-lookup"><span data-stu-id="0419d-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="0419d-250">假設您預期使用者很少想要查看所選取講師和課程中的註冊項目。</span><span class="sxs-lookup"><span data-stu-id="0419d-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="0419d-251">在此情況下，您可能想要只在要求時才載入註冊資料。</span><span class="sxs-lookup"><span data-stu-id="0419d-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="0419d-252">若要查看如何執行明確式載入的範例，請以下列程式碼取代 `Index` 方法，這會移除 Enrollments 的積極式載入，並明確地載入該屬性。</span><span class="sxs-lookup"><span data-stu-id="0419d-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="0419d-253">程式碼變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="0419d-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="0419d-254">新的程式碼會從擷取 Instructor 實體的程式碼中，捨棄用於註冊資料的 *ThenInclude* 方法呼叫。</span><span class="sxs-lookup"><span data-stu-id="0419d-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="0419d-255">它也會捨棄 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="0419d-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="0419d-256">如果選取了講師和課程，醒示提示的程式碼就會針對所選取的課程擷取 Enrollment 實體，並針對每個 Enrollment 擷取 Student 實體。</span><span class="sxs-lookup"><span data-stu-id="0419d-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="0419d-257">執行應用程式，並立即移至 Instructors [索引] 頁面；雖然您已變更資料的擷取方式，但您會發現頁面上顯示的內容沒有任何差異。</span><span class="sxs-lookup"><span data-stu-id="0419d-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="0419d-258">取得程式碼</span><span class="sxs-lookup"><span data-stu-id="0419d-258">Get the code</span></span>

[<span data-ttu-id="0419d-259">下載或檢視已完成的應用程式。</span><span class="sxs-lookup"><span data-stu-id="0419d-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="0419d-260">後續步驟</span><span class="sxs-lookup"><span data-stu-id="0419d-260">Next steps</span></span>

<span data-ttu-id="0419d-261">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="0419d-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0419d-262">了解如何載入相關資料</span><span class="sxs-lookup"><span data-stu-id="0419d-262">Learned how to load related data</span></span>
> * <span data-ttu-id="0419d-263">建立 Courses 頁面</span><span class="sxs-lookup"><span data-stu-id="0419d-263">Created a Courses page</span></span>
> * <span data-ttu-id="0419d-264">建立 Instructors 頁面</span><span class="sxs-lookup"><span data-stu-id="0419d-264">Created an Instructors page</span></span>
> * <span data-ttu-id="0419d-265">了解明確載入</span><span class="sxs-lookup"><span data-stu-id="0419d-265">Learned about explicit loading</span></span>

<span data-ttu-id="0419d-266">若要了解如何更新相關資料，請前往下一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="0419d-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0419d-267">更新相關資料</span><span class="sxs-lookup"><span data-stu-id="0419d-267">Update related data</span></span>](update-related-data.md)
