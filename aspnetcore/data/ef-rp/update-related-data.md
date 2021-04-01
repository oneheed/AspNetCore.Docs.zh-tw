---
title: 第7部分： Razor ASP.NET Core 更新相關資料中有 EF Core 的頁面
author: rick-anderson
description: 第 7 Razor 頁和 Entity Framework 教學課程系列。
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 14080174d35326aa4f412fa0ecd106b5b1bac7c4
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957583"
---
# <a name="part-7-razor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="e8a56-103">第7部分： Razor ASP.NET Core 更新相關資料中有 EF Core 的頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="e8a56-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e8a56-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e8a56-105">本教學課程會示範如何更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="e8a56-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="e8a56-106">下圖顯示一些已完成的頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="e8a56-107">![課程 [編輯] 頁面](update-related-data/_static/course-edit30.png)
![講師 [編輯] 頁面](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="e8a56-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="e8a56-108">更新 Course Create 和 Edit 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="e8a56-109">課程的 [建立] 和 [編輯] 頁面的 scaffold 程式碼具有 [部門] 下拉式清單，其中顯示 `DepartmentID` `int` 。</span><span class="sxs-lookup"><span data-stu-id="e8a56-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows `DepartmentID`, an `int`.</span></span> <span data-ttu-id="e8a56-110">下拉式清單應該會顯示 Department 名稱，因此這兩個頁面需要部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="e8a56-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="e8a56-111">若要提供該清單，請使用 Create 和 Edit 頁面的基底類別。</span><span class="sxs-lookup"><span data-stu-id="e8a56-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="e8a56-112">建立 Course Create 和 Edit 的基底類別</span><span class="sxs-lookup"><span data-stu-id="e8a56-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="e8a56-113">使用下列程式碼建立 *Pages/Courses/DepartmentNamePageModel.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="e8a56-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="e8a56-114">上述程式碼會建立 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) 以包含部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="e8a56-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="e8a56-115">如果指定了 `selectedDepartment`，就會在 `SelectList` 中選取該部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e8a56-116">*Create* 和 *Edit* 頁面模型類別將衍生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="e8a56-117">更新 Course Create 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-117">Update the Course Create page model</span></span>

<span data-ttu-id="e8a56-118">Course 會指派給 Department。</span><span class="sxs-lookup"><span data-stu-id="e8a56-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="e8a56-119">Create 和 Edit 頁面的基底類別會提供 `SelectList` 以用來選取部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="e8a56-120">使用 `SelectList` 的下拉式清單會設定 `Course.DepartmentID` 外部索引鍵 (FK) 屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e8a56-121">EF Core 則使用 `Course.DepartmentID` FK 來載入 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![建立課程](update-related-data/_static/ddl30.png)

<span data-ttu-id="e8a56-123">使用下列程式碼更新 *Pages/Courses/Create.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[loc comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e8a56-124">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-124">The preceding code:</span></span>

* <span data-ttu-id="e8a56-125">衍生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e8a56-126">使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> 來防止[大量指派](xref:data/ef-rp/crud#overposting) (overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-126">Uses <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8a56-127">移除 `ViewData["DepartmentID"]`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="e8a56-128">`DepartmentNameSL` `SelectList` 是強型別模型，而且會由 Razor 頁面使用。</span><span class="sxs-lookup"><span data-stu-id="e8a56-128">The `DepartmentNameSL` `SelectList` is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="e8a56-129">強型別的模型優先於弱型別。</span><span class="sxs-lookup"><span data-stu-id="e8a56-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e8a56-130">如需詳細資訊，請參閱[弱型別資料 (ViewData 和 ViewBag)](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="e8a56-131">更新課程的 [建立] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="e8a56-132">使用下列程式碼更新 *Pages/Courses/Create.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e8a56-133">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8a56-134">將標題從 **DepartmentID** 變更為 **Department**。</span><span class="sxs-lookup"><span data-stu-id="e8a56-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8a56-135">以 `DepartmentNameSL` (來自基底類別) 取代 `"ViewBag.DepartmentID"`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e8a56-136">新增 [選取部門] 選項。</span><span class="sxs-lookup"><span data-stu-id="e8a56-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="e8a56-137">此變更會在尚未選取任何部門時，在下拉式清單中轉譯「選取部門」而非第一個部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="e8a56-138">未選取部門時，請新增驗證訊息。</span><span class="sxs-lookup"><span data-stu-id="e8a56-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e8a56-139">此 Razor 頁面會使用 [選取標記](xref:mvc/views/working-with-forms#the-select-tag-helper)協助程式：</span><span class="sxs-lookup"><span data-stu-id="e8a56-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e8a56-140">測試 *Create* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-140">Test the Create page.</span></span> <span data-ttu-id="e8a56-141">*Create* 頁面會顯示部門名稱，而不是部門識別碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="e8a56-142">更新 Course Edit 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-142">Update the Course Edit page model</span></span>

<span data-ttu-id="e8a56-143">使用下列程式碼更新 *Pages/Courses/Edit.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="e8a56-144">這些變更類似於 *Create* 頁面模型中所做的變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e8a56-145">在上述程式碼中，`PopulateDepartmentsDropDownList` 會傳遞部門識別碼，其在下拉式清單中選取部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="e8a56-146">更新課程的 [編輯] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="e8a56-147">使用下列程式碼更新 *Pages/Courses/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e8a56-148">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8a56-149">顯示課程識別碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-149">Displays the course ID.</span></span> <span data-ttu-id="e8a56-150">通常不會顯示實體的主索引鍵 (PK)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e8a56-151">PK 對使用者來說通常是沒有意義的。</span><span class="sxs-lookup"><span data-stu-id="e8a56-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e8a56-152">在此情況下，PK 是課程編號。</span><span class="sxs-lookup"><span data-stu-id="e8a56-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e8a56-153">將 Department 下拉式清單的標題從 **DepartmentID** 變更為 **Department**。</span><span class="sxs-lookup"><span data-stu-id="e8a56-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8a56-154">取代 `"ViewBag.DepartmentID"` 為 `DepartmentNameSL` 基類中的。</span><span class="sxs-lookup"><span data-stu-id="e8a56-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL`, which is in the base class.</span></span>

<span data-ttu-id="e8a56-155">此頁面包含課程編號的隱藏欄位 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e8a56-156">新增 `<label>` 標籤協助程式與 `asp-for="Course.CourseID"` 無法免除隱藏欄位的需求。</span><span class="sxs-lookup"><span data-stu-id="e8a56-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e8a56-157">`<input type="hidden">` 當使用者選取 [ **儲存**] 時，要包含在張貼的資料中的課程號碼需要。</span><span class="sxs-lookup"><span data-stu-id="e8a56-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user selects **Save**.</span></span>

## <a name="update-the-course-page-models"></a><span data-ttu-id="e8a56-158">更新 Course 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-158">Update the Course page models</span></span>

<span data-ttu-id="e8a56-159">不需要追蹤時，[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以改善效能。</span><span class="sxs-lookup"><span data-stu-id="e8a56-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

<span data-ttu-id="e8a56-160">藉由新增至方法，來更新 *Pages/課程/刪除. cshtml* 和 *Pages/Details. .cs* `AsNoTracking` `OnGetAsync` ：</span><span class="sxs-lookup"><span data-stu-id="e8a56-160">Update *Pages/Courses/Delete.cshtml.cs* and *Pages/Courses/Details.cshtml.cs* by adding `AsNoTracking` to the `OnGetAsync` methods:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Courses/Delete.cshtml.cs?highlight=8-11&name=snippet)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="e8a56-161">更新課程 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-161">Update the Course Razor pages</span></span>

<span data-ttu-id="e8a56-162">使用下列程式碼更新 *Pages/Courses/Delete.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-162">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="e8a56-163">對 *Details* 頁面進行相同的變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-163">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="e8a56-164">測試 Course 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-164">Test the Course pages</span></span>

<span data-ttu-id="e8a56-165">測試建立、編輯、詳細資料和刪除頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-165">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="e8a56-166">更新講師 Create 和 Edit 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-166">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="e8a56-167">講師可教授任何數量的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-167">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e8a56-168">下圖顯示講師的 Edit 頁面，其中包含一個課程核取方塊的陣列。</span><span class="sxs-lookup"><span data-stu-id="e8a56-168">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Instructor [編輯] 頁面與課程](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="e8a56-170">核取方塊可讓您變更指派給講師的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-170">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e8a56-171">資料庫中的每個課程都會顯示一個核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e8a56-171">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="e8a56-172">指派給講師的課程會處於選取狀態。</span><span class="sxs-lookup"><span data-stu-id="e8a56-172">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="e8a56-173">使用者可以選取或清除核取方塊來變更課程指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-173">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="e8a56-174">若課程數要多上許多，則使用不同 UI 的效能可能會更好。</span><span class="sxs-lookup"><span data-stu-id="e8a56-174">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="e8a56-175">但此處所顯示管理多對多關聯性的方法不會變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-175">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="e8a56-176">若要建立或刪除關聯性，您可以操作聯結實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-176">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="e8a56-177">建立受指派課程資料的類別</span><span class="sxs-lookup"><span data-stu-id="e8a56-177">Create a class for assigned courses data</span></span>

<span data-ttu-id="e8a56-178">以下列程式碼建立 *SchoolViewModels/AssignedCourseData.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-178">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e8a56-179">`AssignedCourseData` 類別包含資料，用來建立指派給講師課程的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e8a56-179">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="e8a56-180">建立 Instructor 頁面模型基底類別</span><span class="sxs-lookup"><span data-stu-id="e8a56-180">Create an Instructor page model base class</span></span>

<span data-ttu-id="e8a56-181">建立 *Pages/Instructors/InstructorCoursesPageModel.cs* 基底類別：</span><span class="sxs-lookup"><span data-stu-id="e8a56-181">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="e8a56-182">`InstructorCoursesPageModel`是編輯和建立頁面模型的基類。</span><span class="sxs-lookup"><span data-stu-id="e8a56-182">The `InstructorCoursesPageModel` is the base class for the Edit and Create page models.</span></span> <span data-ttu-id="e8a56-183">`PopulateAssignedCourseData` 會讀取所有 `Course` 實體來擴展 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-183">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e8a56-184">對於每個課程，此程式碼設定 `CourseID`、標題以及是否將講師指派給課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-184">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e8a56-185">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 會用來進行有效率的查閱。</span><span class="sxs-lookup"><span data-stu-id="e8a56-185">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

### <a name="handle-office-location"></a><span data-ttu-id="e8a56-186">處理辦公室位置</span><span class="sxs-lookup"><span data-stu-id="e8a56-186">Handle office location</span></span>

<span data-ttu-id="e8a56-187">另一個編輯頁面必須處理的關聯性是 Instructor 實體針對 `OfficeAssignment` 實體所具有一對零或一對一關聯性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-187">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e8a56-188">講師編輯程式碼必須處理下列案例：</span><span class="sxs-lookup"><span data-stu-id="e8a56-188">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="e8a56-189">如果使用者清除辦公室指派，請刪除 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-189">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8a56-190">如果使用者輸入辦公室指派，但它是空的，請建立新的 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-190">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8a56-191">如果使用者變更辦公室指派，請更新 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-191">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

## <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="e8a56-192">更新 Instructor Edit 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-192">Update the Instructor Edit page model</span></span>

<span data-ttu-id="e8a56-193">使用下列程式碼更新 *Pages/Instructors/Edit.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-193">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="e8a56-194">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-194">The preceding code:</span></span>

* <span data-ttu-id="e8a56-195">使用 `OfficeAssignment`、`CourseAssignment` 和 `CourseAssignment.Course` 導覽屬性的積極式載入，從資料庫取得目前的 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-195">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="e8a56-196">使用從模型繫結器取得的值更新擷取的 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-196">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e8a56-197"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> 會防止[大量指派](xref:data/ef-rp/crud#overposting) (overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-197"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8a56-198">如果辦公室位置為空白，請將 `Instructor.OfficeAssignment` 設定為 Null。</span><span class="sxs-lookup"><span data-stu-id="e8a56-198">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e8a56-199">當 `Instructor.OfficeAssignment` 為 Null 時，將會刪除 `OfficeAssignment` 資料表中的相關資料列。</span><span class="sxs-lookup"><span data-stu-id="e8a56-199">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="e8a56-200">在 `OnGetAsync` 中呼叫 `PopulateAssignedCourseData` 來使用 `AssignedCourseData` 檢視模型類別，以提供資訊給核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e8a56-200">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="e8a56-201">在 `OnPostAsync` 中呼叫 `UpdateInstructorCourses` 來將核取方塊的資訊套用到正在編輯的 Instructor 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-201">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="e8a56-202">若 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> 失敗，則在 `OnPostAsync` 中呼叫 `PopulateAssignedCourseData` 和 `UpdateInstructorCourses`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-202">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> fails.</span></span> <span data-ttu-id="e8a56-203">這些方法呼叫會在重新顯示並附帶錯誤訊息時，還原在頁面上輸入的已指派課程資料。</span><span class="sxs-lookup"><span data-stu-id="e8a56-203">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

<span data-ttu-id="e8a56-204">因為 Razor 頁面沒有課程實體的集合，所以模型系結器無法自動更新 `Courses` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-204">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `Courses` navigation property.</span></span> <span data-ttu-id="e8a56-205">不使用模型系結器來更新 `Courses` 導覽屬性，而是在新的方法中完成 `UpdateInstructorCourses` 。</span><span class="sxs-lookup"><span data-stu-id="e8a56-205">Instead of using the model binder to update the `Courses` navigation property, that's done in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="e8a56-206">因此您必須從模型繫結器中排除 `Courses` 屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-206">Therefore you need to exclude the `Courses` property from model binding.</span></span> <span data-ttu-id="e8a56-207">這並不需要對呼叫的程式碼進行任何變更， <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> 因為您是使用具有宣告屬性的多載，而 `Courses` 不是在包含清單中。</span><span class="sxs-lookup"><span data-stu-id="e8a56-207">This doesn't require any change to the code that calls <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A> because you're using the overload with declared properties and `Courses` isn't in the include list.</span></span>

<span data-ttu-id="e8a56-208">如果未選取任何核取方塊，則中的程式碼會 `UpdateInstructorCourses` `instructorToUpdate.Courses` 使用空集合初始化，並傳回：</span><span class="sxs-lookup"><span data-stu-id="e8a56-208">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `instructorToUpdate.Courses` with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Edit.cshtml.cs?name=snippet_IfNull)]

<span data-ttu-id="e8a56-209">然後程式碼會以迴圈逐一巡覽資料庫中的所有課程，並檢查每個已指派給講師的課程，以及在頁面中選取的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-209">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="e8a56-210">為了協助達成有效率的搜尋，後者的兩個集合會儲存在 `HashSet` 物件中。</span><span class="sxs-lookup"><span data-stu-id="e8a56-210">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="e8a56-211">如果已選取課程的核取方塊，但課程 ***不在*** `Instructor.Courses` 導覽屬性中，則會將課程新增至導覽屬性中的集合。</span><span class="sxs-lookup"><span data-stu-id="e8a56-211">If the check box for a course is selected but the course is ***not*** in the `Instructor.Courses` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Edit.cshtml.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="e8a56-212">如果 ***未*** 選取課程的核取方塊，但課程在 `Instructor.Courses` 導覽屬性中，則會從導覽屬性中移除課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-212">If the check box for a course is ***not*** selected, but the course is in the `Instructor.Courses` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Edit.cshtml.cs?name=snippet_UpdateCoursesElse)]

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="e8a56-213">更新講師 [編輯] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-213">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="e8a56-214">使用下列程式碼更新 *Pages/Instructors/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-214">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="e8a56-215">上述程式碼會建立一個 HTML 資料表，該資料表中有三個資料行。</span><span class="sxs-lookup"><span data-stu-id="e8a56-215">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e8a56-216">每個資料行都有一個核取方塊和包含課程號碼及標題的標題。</span><span class="sxs-lookup"><span data-stu-id="e8a56-216">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="e8a56-217">核取方塊全部都具有相同的名稱 ("selectedCourses")。</span><span class="sxs-lookup"><span data-stu-id="e8a56-217">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e8a56-218">使用相同的名稱可告知模型繫結器將它們視為一個群組。</span><span class="sxs-lookup"><span data-stu-id="e8a56-218">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e8a56-219">每個核取方塊的 Value 屬性都會設為 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-219">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="e8a56-220">張貼頁面時，模型繫結器會傳遞陣列，該陣列當中只包含已選取核取方塊的 `CourseID` 值。</span><span class="sxs-lookup"><span data-stu-id="e8a56-220">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="e8a56-221">一開始轉譯核取方塊時，指派給講師的課程會呈現選取狀態。</span><span class="sxs-lookup"><span data-stu-id="e8a56-221">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="e8a56-222">注意：這裡所用來編輯講師課程資料的方法在課程的數量有限時運作相當良好。</span><span class="sxs-lookup"><span data-stu-id="e8a56-222">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e8a56-223">針對更大的集合，不同的 UI 和不同的更新方法可能更有用且更有效率。</span><span class="sxs-lookup"><span data-stu-id="e8a56-223">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="e8a56-224">執行應用程式並測試更新後的 Instructors Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-224">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="e8a56-225">變更一些課程指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-225">Change some course assignments.</span></span> <span data-ttu-id="e8a56-226">所做的變更會反映在 [索引] 頁面上。</span><span class="sxs-lookup"><span data-stu-id="e8a56-226">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="e8a56-227">更新 Instructor Create 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-227">Update the Instructor Create page</span></span>

<span data-ttu-id="e8a56-228">更新講師建立頁面模型，並使用類似于 [編輯] 頁面的程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-228">Update the Instructor Create page model and with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Create.cshtml.cs?name=snippet_all)]

<span data-ttu-id="e8a56-229">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-229">The preceding code:</span></span>

  * <span data-ttu-id="e8a56-230">新增警告和錯誤訊息的 [記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="e8a56-230">Adds [logging](xref:fundamentals/logging/index) for warning and error messages.</span></span>
  * <span data-ttu-id="e8a56-231">呼叫 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> ，以在一個資料庫呼叫中提取所有課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-231">Calls <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A>, which fetches all the Courses in one database call.</span></span> <span data-ttu-id="e8a56-232">針對較小的集合，這是使用時的優化 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e8a56-232">For small collections this is an optimization when using <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A>.</span></span> <span data-ttu-id="e8a56-233">`FindAsync` 傳回未要求資料庫的追蹤實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-233">`FindAsync` returns the tracked entity without a request to the database.</span></span>

    [!code-csharp[](intro/samples/cu50/Pages/Instructors/Create.cshtml.cs?name=snippet_find&highlight=9,15,34)]

  * <span data-ttu-id="e8a56-234">`_context.Instructors.Add(newInstructor)``Instructor`使用[多對多](/ef/core/modeling/relationships#many-to-many)關聯性建立新的，而不需要明確地對應聯結資料表。</span><span class="sxs-lookup"><span data-stu-id="e8a56-234">`_context.Instructors.Add(newInstructor)` creates a new `Instructor` using [many-to-many](/ef/core/modeling/relationships#many-to-many) relationships without explicitly mapping the join table.</span></span> <span data-ttu-id="e8a56-235">[EF 5.0 已新增多對多](/ef/core/what-is-new/ef-core-5.0/whatsnew)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-235">[Many-to-many was added in EF 5.0](/ef/core/what-is-new/ef-core-5.0/whatsnew).</span></span>

<span data-ttu-id="e8a56-236">測試講師 *Create* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-236">Test the instructor Create page.</span></span>

<span data-ttu-id="e8a56-237">Razor使用與編輯頁面類似的程式碼來更新講師建立頁面：</span><span class="sxs-lookup"><span data-stu-id="e8a56-237">Update the Instructor Create Razor page with code similar to the Edit page:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Instructors/Create.cshtml)]

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="e8a56-238">更新 Instructor Delete 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-238">Update the Instructor Delete page</span></span>

<span data-ttu-id="e8a56-239">使用下列程式碼更新 *Pages/Instructors/Delete.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-239">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="e8a56-240">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-240">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8a56-241">為 `Courses` 導覽屬性使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="e8a56-241">Uses eager loading for the `Courses` navigation property.</span></span> <span data-ttu-id="e8a56-242">必須包含 `Courses`，否則刪除講師時不會刪除它們。</span><span class="sxs-lookup"><span data-stu-id="e8a56-242">`Courses` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e8a56-243">若要避免需要讀取們，您可以在資料庫中設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="e8a56-243">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e8a56-244">若要刪除的講師已指派為任何部門的系統管理員，請先從部門中移除講師的指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-244">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="e8a56-245">執行應用程式及測試 Delete 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-245">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e8a56-246">下一步</span><span class="sxs-lookup"><span data-stu-id="e8a56-246">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e8a56-247">[上一個教學](xref:data/ef-rp/read-related-data) 
>  課程[下一個教學](xref:data/ef-rp/concurrency)課程</span><span class="sxs-lookup"><span data-stu-id="e8a56-247">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"


<span data-ttu-id="e8a56-248">本教學課程會示範如何更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="e8a56-248">This tutorial shows how to update related data.</span></span> <span data-ttu-id="e8a56-249">下圖顯示一些已完成的頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-249">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="e8a56-250">![課程 [編輯] 頁面](update-related-data/_static/course-edit30.png)
![講師 [編輯] 頁面](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="e8a56-250">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="e8a56-251">更新 Course Create 和 Edit 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-251">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="e8a56-252">Course Create 和 Edit 頁面的 scaffold 程式碼包含一個 Department 下拉式清單，其顯示 Department 識別碼 (整數)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-252">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="e8a56-253">下拉式清單應該會顯示 Department 名稱，因此這兩個頁面需要部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="e8a56-253">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="e8a56-254">若要提供該清單，請使用 Create 和 Edit 頁面的基底類別。</span><span class="sxs-lookup"><span data-stu-id="e8a56-254">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="e8a56-255">建立 Course Create 和 Edit 的基底類別</span><span class="sxs-lookup"><span data-stu-id="e8a56-255">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="e8a56-256">使用下列程式碼建立 *Pages/Courses/DepartmentNamePageModel.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="e8a56-256">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="e8a56-257">上述程式碼會建立 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) 以包含部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="e8a56-257">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="e8a56-258">如果指定了 `selectedDepartment`，就會在 `SelectList` 中選取該部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-258">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e8a56-259">*Create* 和 *Edit* 頁面模型類別將衍生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-259">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="e8a56-260">更新 Course Create 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-260">Update the Course Create page model</span></span>

<span data-ttu-id="e8a56-261">Course 會指派給 Department。</span><span class="sxs-lookup"><span data-stu-id="e8a56-261">A Course is assigned to a Department.</span></span> <span data-ttu-id="e8a56-262">Create 和 Edit 頁面的基底類別會提供 `SelectList` 以用來選取部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-262">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="e8a56-263">使用 `SelectList` 的下拉式清單會設定 `Course.DepartmentID` 外部索引鍵 (FK) 屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-263">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e8a56-264">EF Core 則使用 `Course.DepartmentID` FK 來載入 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-264">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![建立課程](update-related-data/_static/ddl30.png)

<span data-ttu-id="e8a56-266">使用下列程式碼更新 *Pages/Courses/Create.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-266">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e8a56-267">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-267">The preceding code:</span></span>

* <span data-ttu-id="e8a56-268">衍生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-268">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e8a56-269">使用 `TryUpdateModelAsync` 來防止[大量指派](xref:data/ef-rp/crud#overposting) (overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-269">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8a56-270">移除 `ViewData["DepartmentID"]`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-270">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="e8a56-271">`DepartmentNameSL` 基類是強型別模型，而且會由 Razor 頁面使用。</span><span class="sxs-lookup"><span data-stu-id="e8a56-271">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="e8a56-272">強型別的模型優先於弱型別。</span><span class="sxs-lookup"><span data-stu-id="e8a56-272">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e8a56-273">如需詳細資訊，請參閱[弱型別資料 (ViewData 和 ViewBag)](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-273">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="e8a56-274">更新課程的 [建立] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-274">Update the Course Create Razor page</span></span>

<span data-ttu-id="e8a56-275">使用下列程式碼更新 *Pages/Courses/Create.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-275">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e8a56-276">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-276">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8a56-277">將標題從 **DepartmentID** 變更為 **Department**。</span><span class="sxs-lookup"><span data-stu-id="e8a56-277">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8a56-278">以 `DepartmentNameSL` (來自基底類別) 取代 `"ViewBag.DepartmentID"`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-278">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e8a56-279">新增 [選取部門] 選項。</span><span class="sxs-lookup"><span data-stu-id="e8a56-279">Adds the "Select Department" option.</span></span> <span data-ttu-id="e8a56-280">此變更會在尚未選取任何部門時，在下拉式清單中轉譯「選取部門」而非第一個部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-280">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="e8a56-281">未選取部門時，請新增驗證訊息。</span><span class="sxs-lookup"><span data-stu-id="e8a56-281">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e8a56-282">此 Razor 頁面會使用 [選取標記](xref:mvc/views/working-with-forms#the-select-tag-helper)協助程式：</span><span class="sxs-lookup"><span data-stu-id="e8a56-282">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e8a56-283">測試 *Create* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-283">Test the Create page.</span></span> <span data-ttu-id="e8a56-284">*Create* 頁面會顯示部門名稱，而不是部門識別碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-284">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="e8a56-285">更新 Course Edit 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-285">Update the Course Edit page model</span></span>

<span data-ttu-id="e8a56-286">使用下列程式碼更新 *Pages/Courses/Edit.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-286">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="e8a56-287">這些變更類似於 *Create* 頁面模型中所做的變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-287">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e8a56-288">在上述程式碼中，`PopulateDepartmentsDropDownList` 會傳遞部門識別碼，其在下拉式清單中選取部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-288">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="e8a56-289">更新課程的 [編輯] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-289">Update the Course Edit Razor page</span></span>

<span data-ttu-id="e8a56-290">使用下列程式碼更新 *Pages/Courses/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-290">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e8a56-291">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-291">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8a56-292">顯示課程識別碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-292">Displays the course ID.</span></span> <span data-ttu-id="e8a56-293">通常不會顯示實體的主索引鍵 (PK)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-293">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e8a56-294">PK 對使用者來說通常是沒有意義的。</span><span class="sxs-lookup"><span data-stu-id="e8a56-294">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e8a56-295">在此情況下，PK 是課程編號。</span><span class="sxs-lookup"><span data-stu-id="e8a56-295">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e8a56-296">將 Department 下拉式清單的標題從 **DepartmentID** 變更為 **Department**。</span><span class="sxs-lookup"><span data-stu-id="e8a56-296">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8a56-297">以 `DepartmentNameSL` (來自基底類別) 取代 `"ViewBag.DepartmentID"`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-297">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e8a56-298">此頁面包含課程編號的隱藏欄位 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-298">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e8a56-299">新增 `<label>` 標籤協助程式與 `asp-for="Course.CourseID"` 無法免除隱藏欄位的需求。</span><span class="sxs-lookup"><span data-stu-id="e8a56-299">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e8a56-300">當使用者按一下 [儲存]  時，需要有 `<input type="hidden">` 才能將課程編號包含在張貼資料中。</span><span class="sxs-lookup"><span data-stu-id="e8a56-300">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="e8a56-301">更新 Course Detail 和 Delete 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-301">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="e8a56-302">不需要追蹤時，[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以改善效能。</span><span class="sxs-lookup"><span data-stu-id="e8a56-302">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="e8a56-303">更新 Course 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-303">Update the Course page models</span></span>

<span data-ttu-id="e8a56-304">使用下列程式碼更新 *Pages/Courses/Delete.cshtml.cs* 來新增 `AsNoTracking`：</span><span class="sxs-lookup"><span data-stu-id="e8a56-304">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="e8a56-305">在 *Pages/Courses/Details.cshtml.cs* 檔案中進行相同的變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-305">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="e8a56-306">更新課程 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-306">Update the Course Razor pages</span></span>

<span data-ttu-id="e8a56-307">使用下列程式碼更新 *Pages/Courses/Delete.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-307">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="e8a56-308">對 *Details* 頁面進行相同的變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-308">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="e8a56-309">測試 Course 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-309">Test the Course pages</span></span>

<span data-ttu-id="e8a56-310">測試建立、編輯、詳細資料和刪除頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-310">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="e8a56-311">更新講師 Create 和 Edit 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-311">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="e8a56-312">講師可教授任何數量的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-312">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e8a56-313">下圖顯示講師的 Edit 頁面，其中包含一個課程核取方塊的陣列。</span><span class="sxs-lookup"><span data-stu-id="e8a56-313">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Instructor [編輯] 頁面與課程](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="e8a56-315">核取方塊可讓您變更指派給講師的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-315">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e8a56-316">資料庫中的每個課程都會顯示一個核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e8a56-316">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="e8a56-317">指派給講師的課程會處於選取狀態。</span><span class="sxs-lookup"><span data-stu-id="e8a56-317">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="e8a56-318">使用者可以選取或清除核取方塊來變更課程指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-318">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="e8a56-319">若課程數要多上許多，則使用不同 UI 的效能可能會更好。</span><span class="sxs-lookup"><span data-stu-id="e8a56-319">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="e8a56-320">但此處所顯示管理多對多關聯性的方法不會變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-320">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="e8a56-321">若要建立或刪除關聯性，您可以操作聯結實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-321">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="e8a56-322">建立受指派課程資料的類別</span><span class="sxs-lookup"><span data-stu-id="e8a56-322">Create a class for assigned courses data</span></span>

<span data-ttu-id="e8a56-323">以下列程式碼建立 *SchoolViewModels/AssignedCourseData.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-323">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e8a56-324">`AssignedCourseData` 類別包含資料，用來建立指派給講師課程的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e8a56-324">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="e8a56-325">建立 Instructor 頁面模型基底類別</span><span class="sxs-lookup"><span data-stu-id="e8a56-325">Create an Instructor page model base class</span></span>

<span data-ttu-id="e8a56-326">建立 *Pages/Instructors/InstructorCoursesPageModel.cs* 基底類別：</span><span class="sxs-lookup"><span data-stu-id="e8a56-326">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="e8a56-327">`InstructorCoursesPageModel` 是您將用於 *Edit* 和 *Create* 頁面模型的基底類別。</span><span class="sxs-lookup"><span data-stu-id="e8a56-327">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="e8a56-328">`PopulateAssignedCourseData` 會讀取所有 `Course` 實體來擴展 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-328">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e8a56-329">對於每個課程，此程式碼設定 `CourseID`、標題以及是否將講師指派給課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-329">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e8a56-330">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 會用來進行有效率的查閱。</span><span class="sxs-lookup"><span data-stu-id="e8a56-330">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="e8a56-331">因為 Razor 頁面沒有課程實體的集合，所以模型系結器無法自動更新 `CourseAssignments` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-331">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e8a56-332">相較於使用模型繫結器更新 `CourseAssignments` 導覽屬性，您會在新的 `UpdateInstructorCourses` 方法中進行相同的操作。</span><span class="sxs-lookup"><span data-stu-id="e8a56-332">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="e8a56-333">因此您必須從模型繫結器中排除 `CourseAssignments` 屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-333">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="e8a56-334">這並不需要對呼叫的程式碼進行任何變更， `TryUpdateModel` 因為您是使用具有宣告屬性的多載，而 `CourseAssignments` 不是在包含清單中。</span><span class="sxs-lookup"><span data-stu-id="e8a56-334">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload with declared properties and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="e8a56-335">若沒有選取任何核取方塊，`UpdateInstructorCourses` 中的程式碼會使用空集合初始化 `CourseAssignments` 導覽屬性並傳回：</span><span class="sxs-lookup"><span data-stu-id="e8a56-335">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="e8a56-336">然後程式碼會以迴圈逐一巡覽資料庫中的所有課程，並檢查每個已指派給講師的課程，以及在頁面中選取的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-336">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="e8a56-337">為了協助達成有效率的搜尋，後者的兩個集合會儲存在 `HashSet` 物件中。</span><span class="sxs-lookup"><span data-stu-id="e8a56-337">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="e8a56-338">若課程的核取方塊已被選取，但課程並未位於 `Instructor.CourseAssignments` 導覽屬性中，則課程便會新增至導覽屬性的集合中。</span><span class="sxs-lookup"><span data-stu-id="e8a56-338">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="e8a56-339">若課程的核取方塊未被選取，但課程卻位於 `Instructor.CourseAssignments` 導覽屬性中，則課程便會從導覽屬性的集合中移除。</span><span class="sxs-lookup"><span data-stu-id="e8a56-339">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="e8a56-340">處理辦公室位置</span><span class="sxs-lookup"><span data-stu-id="e8a56-340">Handle office location</span></span>

<span data-ttu-id="e8a56-341">另一個編輯頁面必須處理的關聯性是 Instructor 實體針對 `OfficeAssignment` 實體所具有一對零或一對一關聯性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-341">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e8a56-342">講師編輯程式碼必須處理下列案例：</span><span class="sxs-lookup"><span data-stu-id="e8a56-342">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="e8a56-343">如果使用者清除辦公室指派，請刪除 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-343">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8a56-344">如果使用者輸入辦公室指派，但它是空的，請建立新的 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-344">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8a56-345">如果使用者變更辦公室指派，請更新 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-345">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="e8a56-346">更新 Instructor Edit 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-346">Update the Instructor Edit page model</span></span>

<span data-ttu-id="e8a56-347">使用下列程式碼更新 *Pages/Instructors/Edit.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-347">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="e8a56-348">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-348">The preceding code:</span></span>

* <span data-ttu-id="e8a56-349">使用 `OfficeAssignment`、`CourseAssignment` 和 `CourseAssignment.Course` 導覽屬性的積極式載入，從資料庫取得目前的 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-349">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="e8a56-350">使用從模型繫結器取得的值更新擷取的 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-350">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e8a56-351">`TryUpdateModel` 會防止[大量指派](xref:data/ef-rp/crud#overposting) (overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-351">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8a56-352">如果辦公室位置為空白，請將 `Instructor.OfficeAssignment` 設定為 Null。</span><span class="sxs-lookup"><span data-stu-id="e8a56-352">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e8a56-353">當 `Instructor.OfficeAssignment` 為 Null 時，將會刪除 `OfficeAssignment` 資料表中的相關資料列。</span><span class="sxs-lookup"><span data-stu-id="e8a56-353">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="e8a56-354">在 `OnGetAsync` 中呼叫 `PopulateAssignedCourseData` 來使用 `AssignedCourseData` 檢視模型類別，以提供資訊給核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e8a56-354">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="e8a56-355">在 `OnPostAsync` 中呼叫 `UpdateInstructorCourses` 來將核取方塊的資訊套用到正在編輯的 Instructor 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-355">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="e8a56-356">若 `TryUpdateModel` 失敗，則在 `OnPostAsync` 中呼叫 `PopulateAssignedCourseData` 和 `UpdateInstructorCourses`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-356">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="e8a56-357">這些方法呼叫會在重新顯示並附帶錯誤訊息時，還原在頁面上輸入的已指派課程資料。</span><span class="sxs-lookup"><span data-stu-id="e8a56-357">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="e8a56-358">更新講師 [編輯] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-358">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="e8a56-359">使用下列程式碼更新 *Pages/Instructors/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-359">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="e8a56-360">上述程式碼會建立一個 HTML 資料表，該資料表中有三個資料行。</span><span class="sxs-lookup"><span data-stu-id="e8a56-360">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e8a56-361">每個資料行都有一個核取方塊和包含課程號碼及標題的標題。</span><span class="sxs-lookup"><span data-stu-id="e8a56-361">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="e8a56-362">核取方塊全部都具有相同的名稱 ("selectedCourses")。</span><span class="sxs-lookup"><span data-stu-id="e8a56-362">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e8a56-363">使用相同的名稱可告知模型繫結器將它們視為一個群組。</span><span class="sxs-lookup"><span data-stu-id="e8a56-363">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e8a56-364">每個核取方塊的 Value 屬性都會設為 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-364">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="e8a56-365">張貼頁面時，模型繫結器會傳遞陣列，該陣列當中只包含已選取核取方塊的 `CourseID` 值。</span><span class="sxs-lookup"><span data-stu-id="e8a56-365">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="e8a56-366">一開始轉譯核取方塊時，指派給講師的課程會呈現選取狀態。</span><span class="sxs-lookup"><span data-stu-id="e8a56-366">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="e8a56-367">注意：這裡所用來編輯講師課程資料的方法在課程的數量有限時運作相當良好。</span><span class="sxs-lookup"><span data-stu-id="e8a56-367">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e8a56-368">針對更大的集合，不同的 UI 和不同的更新方法可能更有用且更有效率。</span><span class="sxs-lookup"><span data-stu-id="e8a56-368">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="e8a56-369">執行應用程式並測試更新後的 Instructors Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-369">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="e8a56-370">變更一些課程指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-370">Change some course assignments.</span></span> <span data-ttu-id="e8a56-371">所做的變更會反映在 [索引] 頁面上。</span><span class="sxs-lookup"><span data-stu-id="e8a56-371">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="e8a56-372">更新 Instructor Create 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-372">Update the Instructor Create page</span></span>

<span data-ttu-id="e8a56-373">使用與編輯頁面類似的程式碼，更新講師建立頁面模型和 Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="e8a56-373">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="e8a56-374">測試講師 *Create* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-374">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="e8a56-375">更新 Instructor Delete 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-375">Update the Instructor Delete page</span></span>

<span data-ttu-id="e8a56-376">使用下列程式碼更新 *Pages/Instructors/Delete.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-376">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="e8a56-377">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-377">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8a56-378">為 `CourseAssignments` 導覽屬性使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="e8a56-378">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e8a56-379">必須包含 `CourseAssignments`，否則刪除講師時不會刪除它們。</span><span class="sxs-lookup"><span data-stu-id="e8a56-379">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e8a56-380">若要避免需要讀取們，您可以在資料庫中設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="e8a56-380">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e8a56-381">若要刪除的講師已指派為任何部門的系統管理員，請先從部門中移除講師的指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-381">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="e8a56-382">執行應用程式及測試 Delete 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-382">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e8a56-383">下一步</span><span class="sxs-lookup"><span data-stu-id="e8a56-383">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e8a56-384">[上一個教學](xref:data/ef-rp/read-related-data) 
>  課程[下一個教學](xref:data/ef-rp/concurrency)課程</span><span class="sxs-lookup"><span data-stu-id="e8a56-384">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8a56-385">本教學課程將示範如何更新相關資料。</span><span class="sxs-lookup"><span data-stu-id="e8a56-385">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="e8a56-386">若您遇到無法解決的問題，請[下載或檢視完整應用程式。](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="e8a56-386">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="e8a56-387">[下載指示](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-387">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e8a56-388">下圖顯示一些完成的頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-388">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="e8a56-389">![課程 [編輯] 頁面](update-related-data/_static/course-edit.png)
![講師 [編輯] 頁面](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="e8a56-389">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="e8a56-390">檢查並測試 *Create* 與 *Edit* 課程頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-390">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="e8a56-391">建立新的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-391">Create a new course.</span></span> <span data-ttu-id="e8a56-392">部門是依照其主索引鍵 (整數) 來進行選取，而不是它的名稱。</span><span class="sxs-lookup"><span data-stu-id="e8a56-392">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="e8a56-393">編輯新的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-393">Edit the new course.</span></span> <span data-ttu-id="e8a56-394">當您完成測試時，請刪除新的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-394">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="e8a56-395">建立要共用通用程式碼的基底類別</span><span class="sxs-lookup"><span data-stu-id="e8a56-395">Create a base class to share common code</span></span>

<span data-ttu-id="e8a56-396">`Courses/Create` 和 `Courses/Edit` 頁面每個都需要部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="e8a56-396">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="e8a56-397">請針對 *Create* 和 *Edit* 頁面建立 *Pages/Courses/DepartmentNamePageModel.cshtml.cs* 基底類別：</span><span class="sxs-lookup"><span data-stu-id="e8a56-397">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="e8a56-398">上述程式碼會建立 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) 以包含部門名稱的清單。</span><span class="sxs-lookup"><span data-stu-id="e8a56-398">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist) to contain the list of department names.</span></span> <span data-ttu-id="e8a56-399">如果指定了 `selectedDepartment`，就會在 `SelectList` 中選取該部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-399">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="e8a56-400">*Create* 和 *Edit* 頁面模型類別將衍生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-400">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="e8a56-401">自訂 [課程] 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-401">Customize the Courses Pages</span></span>

<span data-ttu-id="e8a56-402">當新的課程實體建立時，其必須要與現有的部門具有關聯性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-402">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="e8a56-403">為了在建立課程新增部門，*Create* 和 *Edit* 的基底類別包含用來選取部門的下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="e8a56-403">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="e8a56-404">下拉式清單會設定 `Course.DepartmentID` 外部索引鍵 (FK) 屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-404">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="e8a56-405">EF Core 則使用 `Course.DepartmentID` FK 來載入 `Department` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-405">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![建立課程](update-related-data/_static/ddl.png)

<span data-ttu-id="e8a56-407">以下列程式碼更新 [建立] 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="e8a56-407">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="e8a56-408">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-408">The preceding code:</span></span>

* <span data-ttu-id="e8a56-409">衍生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-409">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="e8a56-410">使用 `TryUpdateModelAsync` 來防止[大量指派](xref:data/ef-rp/crud#overposting) (overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-410">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8a56-411">以 `DepartmentNameSL` (來自基底類別) 取代 `ViewData["DepartmentID"]`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-411">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e8a56-412">`ViewData["DepartmentID"]` 已取代為強型別的 `DepartmentNameSL`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-412">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="e8a56-413">強型別的模型優先於弱型別。</span><span class="sxs-lookup"><span data-stu-id="e8a56-413">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="e8a56-414">如需詳細資訊，請參閱[弱型別資料 (ViewData 和 ViewBag)](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-414">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="e8a56-415">更新 Courses 的 *Create* 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-415">Update the Courses Create page</span></span>

<span data-ttu-id="e8a56-416">使用下列程式碼更新 *Pages/Courses/Create.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-416">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="e8a56-417">上述標記會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-417">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="e8a56-418">將標題從 **DepartmentID** 變更為 **Department**。</span><span class="sxs-lookup"><span data-stu-id="e8a56-418">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8a56-419">以 `DepartmentNameSL` (來自基底類別) 取代 `"ViewBag.DepartmentID"`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-419">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="e8a56-420">新增 [選取部門] 選項。</span><span class="sxs-lookup"><span data-stu-id="e8a56-420">Adds the "Select Department" option.</span></span> <span data-ttu-id="e8a56-421">這項變更會呈現 [選取部門] ，而不是第一個部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-421">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="e8a56-422">未選取部門時，請新增驗證訊息。</span><span class="sxs-lookup"><span data-stu-id="e8a56-422">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="e8a56-423">此 Razor 頁面會使用 [選取標記](xref:mvc/views/working-with-forms#the-select-tag-helper)協助程式：</span><span class="sxs-lookup"><span data-stu-id="e8a56-423">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="e8a56-424">測試 *Create* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-424">Test the Create page.</span></span> <span data-ttu-id="e8a56-425">*Create* 頁面會顯示部門名稱，而不是部門識別碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-425">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="e8a56-426">更新 Courses 的 *Edit* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-426">Update the Courses Edit page.</span></span>

<span data-ttu-id="e8a56-427">使用下列程式碼取代 *Pages/Courses/Edit.cshtml.cs* 中的程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-427">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="e8a56-428">這些變更類似於 *Create* 頁面模型中所做的變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-428">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="e8a56-429">在上述程式碼中，`PopulateDepartmentsDropDownList` 會傳入部門識別碼，以選取下拉式清單中指定的部門。</span><span class="sxs-lookup"><span data-stu-id="e8a56-429">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="e8a56-430">以下列標記更新 *Pages/Courses/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-430">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="e8a56-431">上述標記會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-431">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="e8a56-432">顯示課程識別碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-432">Displays the course ID.</span></span> <span data-ttu-id="e8a56-433">通常不會顯示實體的主索引鍵 (PK)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-433">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="e8a56-434">PK 對使用者來說通常是沒有意義的。</span><span class="sxs-lookup"><span data-stu-id="e8a56-434">PKs are usually meaningless to users.</span></span> <span data-ttu-id="e8a56-435">在此情況下，PK 是課程編號。</span><span class="sxs-lookup"><span data-stu-id="e8a56-435">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="e8a56-436">將標題從 **DepartmentID** 變更為 **Department**。</span><span class="sxs-lookup"><span data-stu-id="e8a56-436">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="e8a56-437">以 `DepartmentNameSL` (來自基底類別) 取代 `"ViewBag.DepartmentID"`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-437">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="e8a56-438">此頁面包含課程編號的隱藏欄位 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-438">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="e8a56-439">新增 `<label>` 標籤協助程式與 `asp-for="Course.CourseID"` 無法免除隱藏欄位的需求。</span><span class="sxs-lookup"><span data-stu-id="e8a56-439">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="e8a56-440">當使用者按一下 [儲存]  時，需要有 `<input type="hidden">` 才能將課程編號包含在張貼資料中。</span><span class="sxs-lookup"><span data-stu-id="e8a56-440">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="e8a56-441">測試更新過的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-441">Test the updated code.</span></span> <span data-ttu-id="e8a56-442">建立、編輯和刪除課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-442">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="e8a56-443">將 AsNoTracking 新增至 *Details* 和 *Delete* 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-443">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="e8a56-444">不需要追蹤時，[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以改善效能。</span><span class="sxs-lookup"><span data-stu-id="e8a56-444">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="e8a56-445">將 `AsNoTracking` 新增至 *Delete* 和 *Details* 頁面模型。</span><span class="sxs-lookup"><span data-stu-id="e8a56-445">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="e8a56-446">下列程式碼顯示已更新的 *Delete* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="e8a56-446">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="e8a56-447">在 *Pages/Courses/Details.cshtml.cs* 檔案中更新 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="e8a56-447">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="e8a56-448">修改 *Delete* 和 *Details* 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-448">Modify the Delete and Details pages</span></span>

<span data-ttu-id="e8a56-449">以下列標記更新 [刪除] Razor 頁面：</span><span class="sxs-lookup"><span data-stu-id="e8a56-449">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="e8a56-450">對 *Details* 頁面進行相同的變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-450">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="e8a56-451">測試 Course 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-451">Test the Course pages</span></span>

<span data-ttu-id="e8a56-452">測試建立、編輯、詳細資料和刪除。</span><span class="sxs-lookup"><span data-stu-id="e8a56-452">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="e8a56-453">更新講師頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-453">Update the instructor pages</span></span>

<span data-ttu-id="e8a56-454">下列各節會更新講師頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-454">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="e8a56-455">新增辦公室位置</span><span class="sxs-lookup"><span data-stu-id="e8a56-455">Add office location</span></span>

<span data-ttu-id="e8a56-456">當您編輯講師記錄時，可能會想要更新講師的辦公室指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-456">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="e8a56-457">`Instructor` 實體與 `OfficeAssignment` 實體具有一對零或一的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-457">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="e8a56-458">必須處理講師程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-458">The instructor code must handle:</span></span>

* <span data-ttu-id="e8a56-459">如果使用者清除辦公室指派，請刪除 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-459">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8a56-460">如果使用者輸入辦公室指派，但它是空的，請建立新的 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-460">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="e8a56-461">如果使用者變更辦公室指派，請更新 `OfficeAssignment` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-461">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="e8a56-462">以下列程式碼更新講師 [編輯] 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="e8a56-462">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="e8a56-463">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="e8a56-463">The preceding code:</span></span>

* <span data-ttu-id="e8a56-464">針對 `OfficeAssignment` 導覽屬性使用積極式載入從資料庫中取得目前的 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-464">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="e8a56-465">使用從模型繫結器取得的值更新擷取的 `Instructor` 實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-465">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="e8a56-466">`TryUpdateModel` 會防止[大量指派](xref:data/ef-rp/crud#overposting) (overposting)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-466">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="e8a56-467">如果辦公室位置為空白，請將 `Instructor.OfficeAssignment` 設定為 Null。</span><span class="sxs-lookup"><span data-stu-id="e8a56-467">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="e8a56-468">當 `Instructor.OfficeAssignment` 為 Null 時，將會刪除 `OfficeAssignment` 資料表中的相關資料列。</span><span class="sxs-lookup"><span data-stu-id="e8a56-468">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="e8a56-469">更新講師 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-469">Update the instructor Edit page</span></span>

<span data-ttu-id="e8a56-470">使用辦公室位置更新 *Pages/Instructors/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-470">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="e8a56-471">請確認您可以變更講師辦公室位置。</span><span class="sxs-lookup"><span data-stu-id="e8a56-471">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="e8a56-472">將課程指派新增至講師 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-472">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="e8a56-473">講師可教授任何數量的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-473">Instructors may teach any number of courses.</span></span> <span data-ttu-id="e8a56-474">在本節中，您可以新增變更課程指派的能力。</span><span class="sxs-lookup"><span data-stu-id="e8a56-474">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="e8a56-475">下圖顯示已更新的講師 [編輯] 頁面：</span><span class="sxs-lookup"><span data-stu-id="e8a56-475">The following image shows the updated instructor Edit page:</span></span>

![Instructor [編輯] 頁面與課程](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="e8a56-477">`Course` 和 `Instructor` 具有多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-477">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="e8a56-478">若要新增和移除關聯性，您必須在 `CourseAssignments` 聯結實體集中新增和移除實體。</span><span class="sxs-lookup"><span data-stu-id="e8a56-478">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="e8a56-479">核取方塊可變更指派給講師的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-479">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="e8a56-480">資料庫中的每個課程各顯示一個核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e8a56-480">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="e8a56-481">已核取指派給講師的課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-481">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="e8a56-482">使用者可選取或清除核取方塊來變更課程指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-482">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="e8a56-483">如果課程數目大上許多：</span><span class="sxs-lookup"><span data-stu-id="e8a56-483">If the number of courses were much greater:</span></span>

* <span data-ttu-id="e8a56-484">您可能會使用不同的使用者介面來顯示課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-484">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="e8a56-485">操作聯結實體來建立或刪除關聯性的方法不會變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-485">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="e8a56-486">新增類別來支援 *Create* 和 *Edit* 講師頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-486">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="e8a56-487">以下列程式碼建立 *SchoolViewModels/AssignedCourseData.cs*：</span><span class="sxs-lookup"><span data-stu-id="e8a56-487">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="e8a56-488">`AssignedCourseData` 類別包含資料，用來建立講師所指派課程的核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e8a56-488">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="e8a56-489">建立 *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* 基底類別：</span><span class="sxs-lookup"><span data-stu-id="e8a56-489">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="e8a56-490">`InstructorCoursesPageModel` 是您將用於 *Edit* 和 *Create* 頁面模型的基底類別。</span><span class="sxs-lookup"><span data-stu-id="e8a56-490">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="e8a56-491">`PopulateAssignedCourseData` 會讀取所有 `Course` 實體來擴展 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-491">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="e8a56-492">對於每個課程，此程式碼設定 `CourseID`、標題以及是否將講師指派給課程。</span><span class="sxs-lookup"><span data-stu-id="e8a56-492">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="e8a56-493">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 用來建立有效查閱。</span><span class="sxs-lookup"><span data-stu-id="e8a56-493">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="e8a56-494">講師 *Edit* 頁面模型</span><span class="sxs-lookup"><span data-stu-id="e8a56-494">Instructors Edit page model</span></span>

<span data-ttu-id="e8a56-495">以下列程式碼更新講師 [編輯] 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="e8a56-495">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="e8a56-496">上述程式碼會處理辦公室指派變更。</span><span class="sxs-lookup"><span data-stu-id="e8a56-496">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="e8a56-497">更新講師 Razor 觀點：</span><span class="sxs-lookup"><span data-stu-id="e8a56-497">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="e8a56-498">當您將程式碼貼上到 Visual Studio 時，分行符號可能會產生變更，致使程式碼失效。</span><span class="sxs-lookup"><span data-stu-id="e8a56-498">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="e8a56-499">按 Ctrl+Z 來復原自動格式化。</span><span class="sxs-lookup"><span data-stu-id="e8a56-499">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="e8a56-500">Ctrl+Z 會修正分行符號，使它們看起來就跟您在這裡看到的一樣。</span><span class="sxs-lookup"><span data-stu-id="e8a56-500">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="e8a56-501">縮排不一定要是完美的，但 `@:</tr><tr>`、`@:<td>`、`@:</td>` 和 `@:</tr>` 必須要如顯示般各自在獨立的一行上。</span><span class="sxs-lookup"><span data-stu-id="e8a56-501">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="e8a56-502">當選取新的程式碼區塊時，按 Tab 鍵三次來讓新的程式碼對準現有的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-502">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="e8a56-503">[使用此連結](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)票選或檢閱這個錯誤的狀態。</span><span class="sxs-lookup"><span data-stu-id="e8a56-503">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="e8a56-504">上述程式碼會建立一個 HTML 資料表，該資料表中有三個資料行。</span><span class="sxs-lookup"><span data-stu-id="e8a56-504">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="e8a56-505">每個資料行都有一個核取方塊，以及內含課程編號和標題 (title) 的標題 (caption)。</span><span class="sxs-lookup"><span data-stu-id="e8a56-505">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="e8a56-506">所有核取方塊都具有相同的名稱 ("selectedCourses")。</span><span class="sxs-lookup"><span data-stu-id="e8a56-506">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="e8a56-507">使用相同的名稱可告知模型繫結器將它們視為一個群組。</span><span class="sxs-lookup"><span data-stu-id="e8a56-507">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="e8a56-508">每個核取方塊的 Value 屬性都會設定為 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="e8a56-508">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="e8a56-509">當頁面發佈時，模型繫結器便會傳遞只包含所選取核取方塊之 `CourseID` 值的陣列。</span><span class="sxs-lookup"><span data-stu-id="e8a56-509">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="e8a56-510">一開始呈現核取方塊時，指派給講師的課程已核取屬性。</span><span class="sxs-lookup"><span data-stu-id="e8a56-510">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="e8a56-511">執行應用程式，並測試已更新的講師 [編輯] 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-511">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="e8a56-512">變更一些課程指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-512">Change some course assignments.</span></span> <span data-ttu-id="e8a56-513">所做的變更會反映在 [索引] 頁面上。</span><span class="sxs-lookup"><span data-stu-id="e8a56-513">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="e8a56-514">注意：這裡所用來編輯講師課程資料的方法在課程的數量有限時運作相當良好。</span><span class="sxs-lookup"><span data-stu-id="e8a56-514">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="e8a56-515">針對更大的集合，不同的 UI 和不同的更新方法可能更有用且更有效率。</span><span class="sxs-lookup"><span data-stu-id="e8a56-515">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="e8a56-516">更新講師 *Create* 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-516">Update the instructors Create page</span></span>

<span data-ttu-id="e8a56-517">以下列程式碼更新講師 *Create* 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="e8a56-517">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="e8a56-518">上述程式碼類似於 *Pages/Instructors/Edit.cshtml.cs* 程式碼。</span><span class="sxs-lookup"><span data-stu-id="e8a56-518">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="e8a56-519">Razor以下列標記更新講師建立頁面：</span><span class="sxs-lookup"><span data-stu-id="e8a56-519">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="e8a56-520">測試講師 *Create* 頁面。</span><span class="sxs-lookup"><span data-stu-id="e8a56-520">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="e8a56-521">更新 [刪除] 頁面</span><span class="sxs-lookup"><span data-stu-id="e8a56-521">Update the Delete page</span></span>

<span data-ttu-id="e8a56-522">以下列程式碼更新 [刪除] 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="e8a56-522">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="e8a56-523">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="e8a56-523">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="e8a56-524">為 `CourseAssignments` 導覽屬性使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="e8a56-524">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="e8a56-525">必須包含 `CourseAssignments`，否則刪除講師時不會刪除它們。</span><span class="sxs-lookup"><span data-stu-id="e8a56-525">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="e8a56-526">若要避免需要讀取們，您可以在資料庫中設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="e8a56-526">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="e8a56-527">若要刪除的講師已指派為任何部門的系統管理員，請先從部門中移除講師的指派。</span><span class="sxs-lookup"><span data-stu-id="e8a56-527">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8a56-528">其他資源</span><span class="sxs-lookup"><span data-stu-id="e8a56-528">Additional resources</span></span>

* [<span data-ttu-id="e8a56-529">這個教學課程的 YouTube 版本 (第 1 部分)</span><span class="sxs-lookup"><span data-stu-id="e8a56-529">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="e8a56-530">這個教學課程的 YouTube 版本 (第 2 部分)</span><span class="sxs-lookup"><span data-stu-id="e8a56-530">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="e8a56-531">[上一個](xref:data/ef-rp/read-related-data) 
> [下一步](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="e8a56-531">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
