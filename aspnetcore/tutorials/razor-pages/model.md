---
title: 第2部分：加入模型
author: rick-anderson
description: '頁面上教學課程系列的第2部分 :::no-loc(Razor)::: 。 在本節中，會加入模型類別。'
ms.author: riande
ms.date: 09/30/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/model
ms.openlocfilehash: ee1b7d77d8d209a11669ba29c8a951c59368180e
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361026"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="fa5a3-104">第2部分：在 ASP.NET Core 中將模型新增至 :::no-loc(Razor)::: 頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="fa5a3-104">Part 2, add a model to a :::no-loc(Razor)::: Pages app in ASP.NET Core</span></span>

<span data-ttu-id="fa5a3-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fa5a3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="fa5a3-106">在本節中，您可以新增類別來管理資料庫中的電影。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="fa5a3-107">應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="fa5a3-108">EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="fa5a3-109">您會先撰寫模型類別，EF Core 建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="fa5a3-110">模型類別稱為 POCO 類別 (自 " **P** >lain- **O** ld **C** LR **O** bjects" ) ，因為它們沒有 EF Core 的相依性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="fa5a3-111">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="fa5a3-112">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="fa5a3-113">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="fa5a3-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fa5a3-115">在 **方案總管** 中，以滑鼠右鍵按一下 *:::no-loc(Razor)::: PagesMovie* 專案 **，> 新增**  >  **資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-115">In **Solution Explorer** , right-click the *:::no-loc(Razor):::PagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="fa5a3-116">將資料夾命名為 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="fa5a3-117">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="fa5a3-118">選取 [ **新增**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="fa5a3-119">將類別命名為 *Movie* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="fa5a3-120">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-121">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-122">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-123">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-124">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-124">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-125">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-126">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa5a3-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa5a3-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="fa5a3-128">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="fa5a3-129">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="fa5a3-130">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-131">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-132">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-133">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-134">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-134">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-135">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-136">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="fa5a3-137">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="fa5a3-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="fa5a3-138">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="fa5a3-138">Add a database context class</span></span>

1. <span data-ttu-id="fa5a3-139">在 *:::no-loc(Razor)::: PagesMovie* 專案中，建立一個名為 *Data* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-139">In the *:::no-loc(Razor):::PagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="fa5a3-140">在 [ *資料* ] 資料夾中，使用下列程式碼加入名為 *:::no-loc(Razor)::: PagesMovieCoNtext.cs* 的檔案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-140">In the *Data* folder, add a file named *:::no-loc(Razor):::PagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

   <span data-ttu-id="fa5a3-141">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="fa5a3-142">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="fa5a3-143">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="fa5a3-144">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="fa5a3-144">Add a database connection string</span></span>

<span data-ttu-id="fa5a3-145">將連接字串新增至檔案，如下列醒目提示的程式 *:::no-loc(appsettings.json):::* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-145">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="fa5a3-146">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="fa5a3-146">Register the database context</span></span>

1. <span data-ttu-id="fa5a3-147">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using :::no-loc(Razor):::PagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="fa5a3-148">使用中的相依性 [插入](xref:fundamentals/dependency-injection) 容器來註冊資料庫內容 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-149">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="fa5a3-150">在 [ **方案] 工具視窗** 中，按一下 [ *:::no-loc(Razor)::: PagesMovie* ] 專案，然後選取 [ **加入** > **新資料夾**...]。命名資料夾 *模型* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-150">In the **Solution Tool Window** , control-click the *:::no-loc(Razor):::PagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="fa5a3-151">以控制項按一下 [ *模型* ] 資料夾， **然後選取 [** > **新增檔案 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="fa5a3-152">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="fa5a3-153">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="fa5a3-154">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="fa5a3-155">將類別命名為 **Movie** ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="fa5a3-156">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-157">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-158">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-159">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-160">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-160">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-161">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-162">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="fa5a3-163">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="fa5a3-164">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="fa5a3-165">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="fa5a3-165">Scaffold the movie model</span></span>

<span data-ttu-id="fa5a3-166">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="fa5a3-167">也就是說，「樣板」工具會針對 :::no-loc(Create)::: movie 模型產生、讀取、更新和 :::no-loc(Delete)::: (CRUD) 作業的頁面。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-167">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fa5a3-169">:::no-loc(Create):::*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-169">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="fa5a3-170">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="fa5a3-171">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="fa5a3-172">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![前述指示中的圖片。](model/_static/5/sca.png)

1. <span data-ttu-id="fa5a3-174">在 [ **新增 Scaffold** ] 對話方塊中， **:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-174">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffold.png)

1. <span data-ttu-id="fa5a3-176">**:::no-loc(Razor)::: 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-176">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="fa5a3-177">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (:::no-loc(Razor)::: PagesMovie])** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-177">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="fa5a3-178">在 [資料內容類別] 資料列中，選取 **+** (加號)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="fa5a3-179">在 [ **加入資料內容** ] 對話方塊中，類別名稱 *:::no-loc(Razor)::: PagesMovie。 :::no-loc(Razor):::產生 PagesMovieCoNtext* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-179">In the **Add Data Context** dialog, the class name *:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="fa5a3-180">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-180">Select **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="fa5a3-182">檔案 *:::no-loc(appsettings.json):::* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-182">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa5a3-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa5a3-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="fa5a3-184">開啟包含 *Program.cs* 、 *Startup.cs* 和 *.csproj* 檔案的命令 shell 至專案目錄。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="fa5a3-185">若 **為 Windows** ：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="fa5a3-186">**針對 macOS 與 Linux** ：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="fa5a3-187">下表詳細說明 ASP.NET Core 程式碼產生器選項。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="fa5a3-188">選項</span><span class="sxs-lookup"><span data-stu-id="fa5a3-188">Option</span></span>               | <span data-ttu-id="fa5a3-189">說明</span><span class="sxs-lookup"><span data-stu-id="fa5a3-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="fa5a3-190">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="fa5a3-191">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="fa5a3-192">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="fa5a3-193">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="fa5a3-194">新增 `_ValidationScriptsPartial` 至編輯和 :::no-loc(Create)::: 頁面</span><span class="sxs-lookup"><span data-stu-id="fa5a3-194">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="fa5a3-195">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="fa5a3-196">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="fa5a3-197">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="fa5a3-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="fa5a3-198">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="fa5a3-199">下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="fa5a3-200">`IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-201">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="fa5a3-202">:::no-loc(Create):::*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-202">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="fa5a3-203">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="fa5a3-204">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="fa5a3-205">按一下 [ *頁面/電影* ] 資料夾 > **加入** > **新** 的樣板 ...]</span><span class="sxs-lookup"><span data-stu-id="fa5a3-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![前述指示中的圖片。](model/_static/scaMac.png)

1. <span data-ttu-id="fa5a3-207">在 [ **新** 的樣板] 對話方塊中， **:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** > **下一步** ] 選取 [頁面]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-207">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="fa5a3-209">**:::no-loc(Razor)::: 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-209">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="fa5a3-210">在 **要使用的 DbCoNtext 類別中：** row，將類別命名為 *:::no-loc(Razor)::: PagesMovie。 :::no-loc(Razor):::PagesMovieCoNtext* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-210">In the **DbContext Class to use:** row, name the class *:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext*.</span></span>
   1. <span data-ttu-id="fa5a3-211">選取 [完成]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-211">Select **Finish**.</span></span>

   ![前述指示中的圖片。](model/_static/5/arpMac.png)

<span data-ttu-id="fa5a3-213">檔案 *:::no-loc(appsettings.json):::* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-213">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="fa5a3-214">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="fa5a3-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="fa5a3-215">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="fa5a3-216">下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="fa5a3-217">`IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="fa5a3-218">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="fa5a3-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-220">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="fa5a3-221">*Pages/電影* ： :::no-loc(Create)::: 、 :::no-loc(Delete)::: 、Details、Edit 和 :::no-loc(Index)::: 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-221">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="fa5a3-222">*Data/ :::no-loc(Razor)::: PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-222">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="fa5a3-223">已更新</span><span class="sxs-lookup"><span data-stu-id="fa5a3-223">Updated</span></span>

* <span data-ttu-id="fa5a3-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-224">*Startup.cs*</span></span>

<span data-ttu-id="fa5a3-225">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa5a3-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa5a3-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fa5a3-227">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="fa5a3-228">*Pages/電影* ： :::no-loc(Create)::: 、 :::no-loc(Delete)::: 、Details、Edit 和 :::no-loc(Index)::: 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-228">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>

<span data-ttu-id="fa5a3-229">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-230">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fa5a3-231">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="fa5a3-232">*Pages/電影* ： :::no-loc(Create)::: 、 :::no-loc(Delete)::: 、Details、Edit 和 :::no-loc(Index)::: 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-232">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="fa5a3-233">*Data/ :::no-loc(Razor)::: PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-233">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="fa5a3-234">已更新</span><span class="sxs-lookup"><span data-stu-id="fa5a3-234">Updated</span></span>

* <span data-ttu-id="fa5a3-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-235">*Startup.cs*</span></span>

<span data-ttu-id="fa5a3-236">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="fa5a3-237">:::no-loc(Create)::: 使用 EF 的遷移功能的初始資料庫架構</span><span class="sxs-lookup"><span data-stu-id="fa5a3-237">:::no-loc(Create)::: the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="fa5a3-238">Entity Framework Core 中的「遷移」功能提供了一種方法，可讓您：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="fa5a3-239">:::no-loc(Create)::: 初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-239">:::no-loc(Create)::: the initial database schema.</span></span>
* <span data-ttu-id="fa5a3-240">以累加方式更新資料庫架構，使其與應用程式的資料模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="fa5a3-241">資料庫中的現有資料會保留下來。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-243">在本節中，會使用 **封裝管理員主控台** (PMC) 視窗：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="fa5a3-244">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-244">Add an initial migration.</span></span>
* <span data-ttu-id="fa5a3-245">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="fa5a3-246">從 [工具] 功能表中，選取 [NuGet 封裝管理員] **[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 功能表](model/_static/5/pmc.png)

1. <span data-ttu-id="fa5a3-248">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial:::no-loc(Create):::
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="fa5a3-250">執行下列 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add Initial:::no-loc(Create):::
  dotnet ef database update
  ```

---

<span data-ttu-id="fa5a3-251">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="fa5a3-252">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="fa5a3-253">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="fa5a3-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="fa5a3-254">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="fa5a3-255">`migrations` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="fa5a3-256">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="fa5a3-257">`Initial:::no-loc(Create):::` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-257">The `Initial:::no-loc(Create):::` argument is used to name the migrations.</span></span> <span data-ttu-id="fa5a3-258">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="fa5a3-259">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="fa5a3-260">在此情況下，會 `update` `Up` 在建立資料庫的 *遷移/ \<time-stamp> _Initial :::no-loc(Create)::: .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="fa5a3-262">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="fa5a3-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="fa5a3-263">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fa5a3-264">服務（例如 EF Core 資料庫內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="fa5a3-265">需要這些服務的元件 (例如 :::no-loc(Razor)::: 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-265">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="fa5a3-266">取得資料庫內容執行個體的建構函式程式碼會顯示在本教學課程稍後部分。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="fa5a3-267">「樣板」工具會自動建立資料庫內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="fa5a3-268">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fa5a3-269">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="fa5a3-270">`:::no-loc(Razor):::PagesMovieContext`協調模型 EF Core 功能，例如 :::no-loc(Create)::: 讀取、更新和 :::no-loc(Delete)::: `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-270">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="fa5a3-271">資料內容 (`:::no-loc(Razor):::PagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-271">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="fa5a3-272">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="fa5a3-273">上述程式碼會建立實體集的[DbSet \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601)屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="fa5a3-274">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="fa5a3-275">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="fa5a3-276">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="fa5a3-277">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-278">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fa5a3-279">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="fa5a3-280">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="fa5a3-280">Test the app</span></span>

1. <span data-ttu-id="fa5a3-281">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="fa5a3-282">如果您收到下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="fa5a3-283">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="fa5a3-284">測試 **:::no-loc(Create):::** 連結。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-284">Test the **:::no-loc(Create):::** link.</span></span>

   ![：：：非 loc (建立) ：：：頁面](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="fa5a3-286">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="fa5a3-287">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="fa5a3-288">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="fa5a3-289">測試 [ **編輯** ]、[ **詳細資料** ] 和 [ **:::no-loc(Delete):::** 連結]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-289">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="fa5a3-290">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fa5a3-291">其他資源</span><span class="sxs-lookup"><span data-stu-id="fa5a3-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fa5a3-292">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold :::no-loc(Razor):::頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="fa5a3-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="fa5a3-293">在本節中，會新增類別來管理電影。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="fa5a3-294">應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="fa5a3-295">EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="fa5a3-296">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="fa5a3-297">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="fa5a3-298">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="fa5a3-299">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="fa5a3-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-301">以滑鼠右鍵按一下 **:::no-loc(Razor)::: PagesMovie** 專案， **>**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-301">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="fa5a3-302">將資料夾命名為 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-302">Name the folder *Models*.</span></span>

<span data-ttu-id="fa5a3-303">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="fa5a3-304">選取 [ **新增**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="fa5a3-305">將類別命名為 **Movie** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-305">Name the class **Movie**.</span></span>

<span data-ttu-id="fa5a3-306">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-307">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-308">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-309">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-310">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-310">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-311">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-312">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="fa5a3-313">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa5a3-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa5a3-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fa5a3-315">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="fa5a3-316">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="fa5a3-317">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-318">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-319">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-320">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-321">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-321">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-322">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-323">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="fa5a3-324">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="fa5a3-325">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="fa5a3-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="fa5a3-326">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="fa5a3-326">Add a database context class</span></span>

* <span data-ttu-id="fa5a3-327">在 *:::no-loc(Razor)::: PagesMovie* 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-327">In the *:::no-loc(Razor):::PagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="fa5a3-328">將下列 `:::no-loc(Razor):::PagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-328">Add the following `:::no-loc(Razor):::PagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="fa5a3-329">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="fa5a3-330">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="fa5a3-331">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="fa5a3-332">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="fa5a3-332">Add a database connection string</span></span>

<span data-ttu-id="fa5a3-333">將連接字串新增至檔案，如下列醒目提示的程式 *:::no-loc(appsettings.json):::* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-333">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="fa5a3-334">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="fa5a3-334">Register the database context</span></span>

<span data-ttu-id="fa5a3-335">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using :::no-loc(Razor):::PagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="fa5a3-336">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-337">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fa5a3-338">在 [ **方案] 工具視窗** 中，按一下 [ **:::no-loc(Razor)::: PagesMovie** ] 專案，然後選取 [ **加入** > **新資料夾**...]。命名資料夾 *模型* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-338">In the **Solution Tool Window** , control-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="fa5a3-339">以滑鼠右鍵按一下 [ *模型* ] 資料夾，然後 **選取 [** > **新增檔案 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="fa5a3-340">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="fa5a3-341">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="fa5a3-342">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="fa5a3-343">將類別命名為 **Movie** ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="fa5a3-344">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-345">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-346">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-347">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-348">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-348">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-349">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-350">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="fa5a3-351">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="fa5a3-352">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="fa5a3-353">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="fa5a3-353">Scaffold the movie model</span></span>

<span data-ttu-id="fa5a3-354">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="fa5a3-355">也就是說，「樣板」工具會針對 :::no-loc(Create)::: movie 模型產生、讀取、更新和 :::no-loc(Delete)::: (CRUD) 作業的頁面。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-355">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-357">:::no-loc(Create):::*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-357">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="fa5a3-358">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="fa5a3-359">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="fa5a3-360">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="fa5a3-362">在 [ **新增 Scaffold** ] 對話方塊中， **:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-362">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="fa5a3-364">**:::no-loc(Razor)::: 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-364">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="fa5a3-365">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (:::no-loc(Razor)::: PagesMovie])** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-365">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="fa5a3-366">在 [ **資料內容類別] 資料** 列中，選取 **+** (加號，) 簽署並變更 PagesMovie 所產生的名稱 :::no-loc(Razor)::: 。 **模型** 。 :::no-loc(Razor):::PagesMovieCoNtext 至 :::no-loc(Razor)::: PagesMovie。 **資料** 。 :::no-loc(Razor):::PagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="fa5a3-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from :::no-loc(Razor):::PagesMovie. **Models**.:::no-loc(Razor):::PagesMovieContext to :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="fa5a3-367">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="fa5a3-368">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="fa5a3-369">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-369">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="fa5a3-371">檔案 *:::no-loc(appsettings.json):::* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-371">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa5a3-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa5a3-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="fa5a3-373">在專案目錄中開啟命令視窗，其中包含 *Program.cs* 、 *Startup.cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="fa5a3-374">若 **為 Windows** ：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="fa5a3-375">**針對 macOS 與 Linux** ：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="fa5a3-376">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="fa5a3-377">選項</span><span class="sxs-lookup"><span data-stu-id="fa5a3-377">Option</span></span>               | <span data-ttu-id="fa5a3-378">說明</span><span class="sxs-lookup"><span data-stu-id="fa5a3-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="fa5a3-379">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="fa5a3-380">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="fa5a3-381">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="fa5a3-382">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="fa5a3-383">新增 `_ValidationScriptsPartial` 至編輯和 :::no-loc(Create)::: 頁面</span><span class="sxs-lookup"><span data-stu-id="fa5a3-383">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="fa5a3-384">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="fa5a3-385">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="fa5a3-386">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="fa5a3-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="fa5a3-387">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="fa5a3-388">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="fa5a3-389">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-390">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fa5a3-391">:::no-loc(Create):::*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-391">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="fa5a3-392">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="fa5a3-393">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="fa5a3-394">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新** 的樣板 ...]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="fa5a3-396">在 [ **新** 的樣板] 對話方塊中， **:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** > **下一步** ] 選取 [頁面]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-396">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="fa5a3-398">**:::no-loc(Razor)::: 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-398">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="fa5a3-399">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie (:::no-loc(Razor)::: PagesMovie) 模型** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-399">In the **Model class** drop down, select, or type, **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="fa5a3-400">在 [ **資料內容類別** ] 列中，輸入新類別的名稱 :::no-loc(Razor)::: PagesMovie。 **資料** 。 :::no-loc(Razor):::PagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="fa5a3-400">In the **Data context class** row, type the name for the new class, :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="fa5a3-401">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="fa5a3-402">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="fa5a3-403">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-403">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="fa5a3-405">檔案 *:::no-loc(appsettings.json):::* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-405">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="fa5a3-406">新增 EF 工具</span><span class="sxs-lookup"><span data-stu-id="fa5a3-406">Add EF tools</span></span>

<span data-ttu-id="fa5a3-407">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="fa5a3-408">上述命令會新增 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="fa5a3-409">如需詳細資訊，請參閱 [Entity Framework Core 工具參考-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="fa5a3-410">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="fa5a3-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="fa5a3-411">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="fa5a3-412">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="fa5a3-413">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="fa5a3-414">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="fa5a3-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-416">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="fa5a3-417">*Pages/電影* ： :::no-loc(Create)::: 、 :::no-loc(Delete)::: 、Details、Edit 和 :::no-loc(Index)::: 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-417">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="fa5a3-418">*Data/ :::no-loc(Razor)::: PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-418">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="fa5a3-419">已更新</span><span class="sxs-lookup"><span data-stu-id="fa5a3-419">Updated</span></span>

* <span data-ttu-id="fa5a3-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-420">*Startup.cs*</span></span>

<span data-ttu-id="fa5a3-421">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-422">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fa5a3-423">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="fa5a3-424">*Pages/電影* ： :::no-loc(Create)::: 、 :::no-loc(Delete)::: 、Details、Edit 和 :::no-loc(Index)::: 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-424">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="fa5a3-425">*Data/ :::no-loc(Razor)::: PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-425">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="fa5a3-426">已更新</span><span class="sxs-lookup"><span data-stu-id="fa5a3-426">Updated</span></span>

* <span data-ttu-id="fa5a3-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-427">*Startup.cs*</span></span>

<span data-ttu-id="fa5a3-428">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa5a3-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa5a3-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fa5a3-430">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="fa5a3-431">*Pages/電影* ： :::no-loc(Create)::: 、 :::no-loc(Delete)::: 、Details、Edit 和 :::no-loc(Index)::: 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-431">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>

<span data-ttu-id="fa5a3-432">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="fa5a3-433">初始移轉</span><span class="sxs-lookup"><span data-stu-id="fa5a3-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-435">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="fa5a3-436">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-436">Add an initial migration.</span></span>
* <span data-ttu-id="fa5a3-437">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="fa5a3-438">從 [工具] 功能表中，選取 [NuGet 封裝管理員] **[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="fa5a3-440">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial:::no-loc(Create):::
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-441">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="fa5a3-442">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add Initial:::no-loc(Create):::
dotnet ef database update
```

---

<span data-ttu-id="fa5a3-443">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="fa5a3-444">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="fa5a3-445">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="fa5a3-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="fa5a3-446">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="fa5a3-447">遷移命令會產生程式碼來建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="fa5a3-448">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="fa5a3-449">`Initial:::no-loc(Create):::` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-449">The `Initial:::no-loc(Create):::` argument is used to name the migrations.</span></span> <span data-ttu-id="fa5a3-450">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="fa5a3-451">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="fa5a3-452">在此情況下，會 `update` `Up` 在建立資料庫的  *遷移/ \<time-stamp> _Initial :::no-loc(Create)::: .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="fa5a3-454">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="fa5a3-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="fa5a3-455">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fa5a3-456">服務（例如 EF Core 資料庫內容內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="fa5a3-457">需要這些服務的元件（例如 :::no-loc(Razor)::: 頁面）是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-457">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="fa5a3-458">本教學課程稍後會顯示取得資料庫內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="fa5a3-459">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="fa5a3-460">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fa5a3-461">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="fa5a3-462">`:::no-loc(Razor):::PagesMovieContext`協調模型 EF Core 功能，例如 :::no-loc(Create)::: 讀取、更新和 :::no-loc(Delete)::: `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-462">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="fa5a3-463">資料內容 (`:::no-loc(Razor):::PagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-463">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="fa5a3-464">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="fa5a3-465">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="fa5a3-466">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="fa5a3-467">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="fa5a3-468">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="fa5a3-469">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-470">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fa5a3-471">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="fa5a3-472">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="fa5a3-472">Test the app</span></span>

* <span data-ttu-id="fa5a3-473">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="fa5a3-474">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-474">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="fa5a3-475">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="fa5a3-476">測試 **:::no-loc(Create):::** 連結。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-476">Test the **:::no-loc(Create):::** link.</span></span>

  ![：：：非 loc (建立) ：：：頁面](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="fa5a3-478">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="fa5a3-479">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="fa5a3-480">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="fa5a3-481">測試 [ **編輯** ]、[ **詳細資料** ] 和 [ **:::no-loc(Delete):::** 連結]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-481">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="fa5a3-482">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fa5a3-483">其他資源</span><span class="sxs-lookup"><span data-stu-id="fa5a3-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fa5a3-484">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold :::no-loc(Razor):::頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="fa5a3-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fa5a3-485">在本節中，會新增類別來管理跨平臺 [SQLite 資料庫](https://www.sqlite.org/index.html)中的電影。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="fa5a3-486">從 ASP.NET Core 範本建立的應用程式會使用 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="fa5a3-487">應用程式的模型類別可搭配 [Entity Framework Core (EF Core) ](/ef/core) ([SQLite EF Core 資料庫提供者](/ef/core/providers/sqlite)) 使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="fa5a3-488">EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="fa5a3-489">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="fa5a3-490">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="fa5a3-491">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="fa5a3-492">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="fa5a3-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-494">以滑鼠右鍵按一下 **:::no-loc(Razor)::: PagesMovie** 專案， **>**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-494">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="fa5a3-495">將資料夾命名為 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-495">Name the folder *Models*.</span></span>

<span data-ttu-id="fa5a3-496">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="fa5a3-497">選取 [ **新增**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="fa5a3-498">將類別命名為 **Movie** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-498">Name the class **Movie**.</span></span>

<span data-ttu-id="fa5a3-499">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-500">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-501">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-502">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-503">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-503">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-504">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-505">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="fa5a3-506">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa5a3-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa5a3-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fa5a3-508">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="fa5a3-509">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="fa5a3-510">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-511">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-512">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-513">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-514">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-514">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-515">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-516">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="fa5a3-517">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="fa5a3-518">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="fa5a3-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="fa5a3-519">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="fa5a3-519">Add a database context class</span></span>

<span data-ttu-id="fa5a3-520">在 :::no-loc(Razor)::: PagesMovie 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-520">In the :::no-loc(Razor):::PagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="fa5a3-521">將下列 `:::no-loc(Razor):::PagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-521">Add the following `:::no-loc(Razor):::PagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="fa5a3-522">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="fa5a3-523">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="fa5a3-524">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="fa5a3-525">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="fa5a3-525">Add a database connection string</span></span>

<span data-ttu-id="fa5a3-526">將連接字串新增至檔案，如下列醒目提示的程式 *:::no-loc(appsettings.json):::* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-526">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="fa5a3-527">新增必要的 NuGet 封裝</span><span class="sxs-lookup"><span data-stu-id="fa5a3-527">Add required NuGet packages</span></span>

<span data-ttu-id="fa5a3-528">執行下列 .NET Core CLI 命令以將 SQLite 和 >nswag.codegeneration.csharp 新增至專案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="fa5a3-529">需要 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 封裝，才能進行 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="fa5a3-530">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="fa5a3-530">Register the database context</span></span>

<span data-ttu-id="fa5a3-531">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using :::no-loc(Razor):::PagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="fa5a3-532">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="fa5a3-533">建置專案來檢查錯誤。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-534">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fa5a3-535">在 [ **方案工具] 視窗** 中，按一下 [ *:::no-loc(Razor)::: PagesMovie* ] 專案，然後 **選取 [**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-535">In **Solution Tool Window** , control-click the *:::no-loc(Razor):::PagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="fa5a3-536">將資料夾命名為 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="fa5a3-537">在 [ *模型* ] 資料夾上按一下控制項，然後選取 [ **加入** > **新** 檔案]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="fa5a3-538">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="fa5a3-539">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="fa5a3-540">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="fa5a3-541">將類別命名為 **Movie** ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="fa5a3-542">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="fa5a3-543">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="fa5a3-544">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="fa5a3-545">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="fa5a3-546">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-546">With this attribute:</span></span>

  * <span data-ttu-id="fa5a3-547">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="fa5a3-548">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="fa5a3-549">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="fa5a3-550">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="fa5a3-551">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="fa5a3-551">Scaffold the movie model</span></span>

<span data-ttu-id="fa5a3-552">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="fa5a3-553">也就是說，「樣板」工具會針對 :::no-loc(Create)::: movie 模型產生、讀取、更新和 :::no-loc(Delete)::: (CRUD) 作業的頁面。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-553">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-555">:::no-loc(Create):::*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-555">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="fa5a3-556">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="fa5a3-557">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="fa5a3-558">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="fa5a3-560">在 [ **新增 Scaffold** ] 對話方塊中， **:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-560">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="fa5a3-562">**:::no-loc(Razor)::: 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-562">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="fa5a3-563">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (:::no-loc(Razor)::: PagesMovie])** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-563">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="fa5a3-564">在 [ **資料內容類別] 資料** 列中，選取 **+** (加上) 號，然後接受產生的名稱 **:::no-loc(Razor)::: PagesMovie。 :::no-loc(Razor):::PagesMovieCoNtext** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="fa5a3-565">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-565">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arp.png)

<span data-ttu-id="fa5a3-567">檔案 *:::no-loc(appsettings.json):::* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-567">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fa5a3-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fa5a3-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="fa5a3-569">在專案目錄中開啟命令視窗，其中包含 *Program.cs* 、 *Startup.cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="fa5a3-570">若 **為 Windows** ：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="fa5a3-571">**針對 macOS 與 Linux** ：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="fa5a3-572">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="fa5a3-573">選項</span><span class="sxs-lookup"><span data-stu-id="fa5a3-573">Option</span></span>               | <span data-ttu-id="fa5a3-574">說明</span><span class="sxs-lookup"><span data-stu-id="fa5a3-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="fa5a3-575">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="fa5a3-576">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="fa5a3-577">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="fa5a3-578">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="fa5a3-579">新增 `_ValidationScriptsPartial` 至編輯和 :::no-loc(Create)::: 頁面</span><span class="sxs-lookup"><span data-stu-id="fa5a3-579">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="fa5a3-580">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="fa5a3-581">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-582">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fa5a3-583">:::no-loc(Create):::*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-583">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="fa5a3-584">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="fa5a3-585">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="fa5a3-586">在 [ *頁面/電影* ] 資料夾上按一下控制項，> **加入** > **新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="fa5a3-588">在 [ **加入新** 的樣板] 對話方塊中， **:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-588">In the **Add New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="fa5a3-590">**:::no-loc(Razor)::: 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-590">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="fa5a3-591">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="fa5a3-592">在 [ **資料內容類別] 資料** 列中，輸入 select **:::no-loc(Razor)::: PagesMovieCoNtext** ，這會使用正確的命名空間來建立新的資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-592">In the **Data context class** row, type select the **:::no-loc(Razor):::PagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="fa5a3-593">在此情況下，它將會是 **:::no-loc(Razor)::: PagesMovie 模型。 :::no-loc(Razor):::PagesMovieCoNtext** 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-593">In this case it will be  **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="fa5a3-594">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-594">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="fa5a3-596">檔案 *:::no-loc(appsettings.json):::* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-596">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="fa5a3-597">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="fa5a3-598">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="fa5a3-598">Files created</span></span>

* <span data-ttu-id="fa5a3-599">*Pages/電影* ： :::no-loc(Create)::: 、 :::no-loc(Delete)::: 、Details、Edit 和 :::no-loc(Index)::: 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-599">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="fa5a3-600">*Data/ :::no-loc(Razor)::: PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-600">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="fa5a3-601">檔案已更新</span><span class="sxs-lookup"><span data-stu-id="fa5a3-601">File updated</span></span>

* <span data-ttu-id="fa5a3-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-602">*Startup.cs*</span></span>

<span data-ttu-id="fa5a3-603">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="fa5a3-604">初始移轉</span><span class="sxs-lookup"><span data-stu-id="fa5a3-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa5a3-606">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="fa5a3-607">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-607">Add an initial migration.</span></span>
* <span data-ttu-id="fa5a3-608">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="fa5a3-609">從 [工具] 功能表中，選取 [NuGet 封裝管理員] **[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="fa5a3-611">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="fa5a3-612">`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="fa5a3-613">架構是以 PagesMovieCoNtext.cs 檔案中指定的模型為基礎 `DbContext` 。 *:::no-loc(Razor):::*</span><span class="sxs-lookup"><span data-stu-id="fa5a3-613">The schema is based on the model specified in the `DbContext`, in the *:::no-loc(Razor):::PagesMovieContext.cs* file.</span></span> <span data-ttu-id="fa5a3-614">`Initial:::no-loc(Create):::`引數是用來命名遷移。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-614">The `Initial:::no-loc(Create):::` argument is used to name the migration.</span></span> <span data-ttu-id="fa5a3-615">您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="fa5a3-616">如需詳細資訊，請參閱<xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="fa5a3-617">此 `Update-Database` 命令會 `Up` 在 *遷移/ \<time-stamp> _Initial :::no-loc(Create)::: .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file.</span></span> <span data-ttu-id="fa5a3-618">`Up` 方法會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-619">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="fa5a3-620">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add Initial:::no-loc(Create):::
dotnet ef database update
```

---

<span data-ttu-id="fa5a3-621">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="fa5a3-622">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="fa5a3-623">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="fa5a3-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="fa5a3-624">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa5a3-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa5a3-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="fa5a3-626">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="fa5a3-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="fa5a3-627">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fa5a3-628">服務 (例如 EF Core 資料庫內容內容) 會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="fa5a3-629">需要這些服務的元件 (例如 :::no-loc(Razor)::: 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-629">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="fa5a3-630">本教學課程稍後會顯示取得資料庫內容 coNtextB 內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="fa5a3-631">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="fa5a3-632">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fa5a3-633">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="fa5a3-634">`:::no-loc(Razor):::PagesMovieContext`協調模型的 EF Core 功能，例如 :::no-loc(Create)::: 、讀取、更新和 :::no-loc(Delete)::: `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-634">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update, and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="fa5a3-635">資料內容 (`:::no-loc(Razor):::PagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-635">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="fa5a3-636">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="fa5a3-637">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="fa5a3-638">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="fa5a3-639">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="fa5a3-640">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="fa5a3-641">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fa5a3-642">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa5a3-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fa5a3-643">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="fa5a3-644">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="fa5a3-644">Test the app</span></span>

* <span data-ttu-id="fa5a3-645">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="fa5a3-646">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="fa5a3-646">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="fa5a3-647">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="fa5a3-648">測試 **:::no-loc(Create):::** 連結。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-648">Test the **:::no-loc(Create):::** link.</span></span>

  ![：：：非 loc (建立) ：：：頁面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="fa5a3-650">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="fa5a3-651">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="fa5a3-652">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="fa5a3-653">測試 [ **編輯** ]、[ **詳細資料** ] 和 [ **:::no-loc(Delete):::** 連結]。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-653">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="fa5a3-654">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="fa5a3-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fa5a3-655">其他資源</span><span class="sxs-lookup"><span data-stu-id="fa5a3-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fa5a3-656">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold :::no-loc(Razor):::頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="fa5a3-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
