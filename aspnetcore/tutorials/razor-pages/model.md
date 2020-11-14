---
title: 第2部分：加入模型
author: rick-anderson
description: '頁面上教學課程系列的第2部分 Razor 。 在本節中，會加入模型類別。'
ms.author: riande
ms.date: 11/11/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550664"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="3ad87-104">第2部分：在 ASP.NET Core 中將模型新增至 Razor 頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="3ad87-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="3ad87-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3ad87-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="3ad87-106">在本節中，您可以新增類別來管理資料庫中的電影。</span><span class="sxs-lookup"><span data-stu-id="3ad87-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="3ad87-107">應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="3ad87-108">EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="3ad87-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="3ad87-109">您會先撰寫模型類別，EF Core 建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="3ad87-110">模型類別稱為 POCO 類別 (自 " **P** >lain- **O** ld **C** LR **O** bjects" ) ，因為它們沒有 EF Core 的相依性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="3ad87-111">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3ad87-112">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3ad87-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3ad87-113">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="3ad87-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3ad87-115">在 **方案總管** 中，以滑鼠右鍵按一下 *Razor PagesMovie* 專案 **，> 新增**  >  **資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-115">In **Solution Explorer** , right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3ad87-116">將資料夾命名為 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="3ad87-117">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="3ad87-118">選取 [ **新增**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="3ad87-119">將類別命名為 *Movie* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="3ad87-120">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-121">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-122">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-123">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-124">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-124">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-125">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-126">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ad87-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ad87-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3ad87-128">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="3ad87-129">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3ad87-130">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-131">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-132">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-133">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-134">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-134">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-135">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-136">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3ad87-137">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="3ad87-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="3ad87-138">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="3ad87-138">Add a database context class</span></span>

1. <span data-ttu-id="3ad87-139">在 *Razor PagesMovie* 專案中，建立一個名為 *Data* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="3ad87-140">在 [ *資料* ] 資料夾中，使用下列程式碼加入名為 *Razor PagesMovieCoNtext.cs* 的檔案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="3ad87-141">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="3ad87-142">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3ad87-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="3ad87-143">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="3ad87-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="3ad87-144">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="3ad87-144">Add a database connection string</span></span>

<span data-ttu-id="3ad87-145">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="3ad87-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="3ad87-146">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="3ad87-146">Register the database context</span></span>

1. <span data-ttu-id="3ad87-147">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="3ad87-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="3ad87-148">使用中的相依性 [插入](xref:fundamentals/dependency-injection) 容器來註冊資料庫內容 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="3ad87-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-149">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3ad87-150">在 [ **方案] 工具視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後選取 [ **加入** > **新資料夾**...]。命名資料夾 *模型* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-150">In the **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="3ad87-151">以控制項按一下 [ *模型* ] 資料夾， **然後選取 [** > **新增檔案 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="3ad87-152">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="3ad87-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="3ad87-153">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="3ad87-154">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="3ad87-155">將類別命名為 **Movie** ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="3ad87-156">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-157">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-158">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-159">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-160">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-160">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-161">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-162">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="3ad87-163">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="3ad87-164">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="3ad87-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3ad87-165">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="3ad87-165">Scaffold the movie model</span></span>

<span data-ttu-id="3ad87-166">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="3ad87-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3ad87-167">也就是說，「樣板」工具會針對 Create movie 模型產生、讀取、更新和 Delete (CRUD) 作業的頁面。</span><span class="sxs-lookup"><span data-stu-id="3ad87-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3ad87-169">Create*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3ad87-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="3ad87-170">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="3ad87-171">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="3ad87-172">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![前述指示中的圖片。](model/_static/5/sca.png)

1. <span data-ttu-id="3ad87-174">在 [ **新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffold.png)

1. <span data-ttu-id="3ad87-176">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="3ad87-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="3ad87-177">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="3ad87-178">在 [資料內容類別] 資料列中，選取 **+** (加號)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="3ad87-179">在 [ **加入資料內容** ] 對話方塊中，類別名稱 *Razor PagesMovie。 Razor產生 PagesMovieCoNtext* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="3ad87-180">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="3ad87-180">Select **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="3ad87-182">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3ad87-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ad87-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ad87-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3ad87-184">開啟包含 *Program.cs* 、 *Startup.cs* 和 *.csproj* 檔案的命令 shell 至專案目錄。</span><span class="sxs-lookup"><span data-stu-id="3ad87-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="3ad87-185">若 **為 Windows** ：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3ad87-186">**針對 macOS 與 Linux** ：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3ad87-187">下表詳細說明 ASP.NET Core 程式碼產生器選項。</span><span class="sxs-lookup"><span data-stu-id="3ad87-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="3ad87-188">選項</span><span class="sxs-lookup"><span data-stu-id="3ad87-188">Option</span></span>               | <span data-ttu-id="3ad87-189">Description</span><span class="sxs-lookup"><span data-stu-id="3ad87-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3ad87-190">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="3ad87-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3ad87-191">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="3ad87-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3ad87-192">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="3ad87-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3ad87-193">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="3ad87-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3ad87-194">新增 `_ValidationScriptsPartial` 至編輯和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="3ad87-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3ad87-195">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="3ad87-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3ad87-196">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3ad87-197">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="3ad87-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3ad87-198">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="3ad87-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3ad87-199">下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="3ad87-200">`IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="3ad87-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-201">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3ad87-202">Create*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3ad87-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="3ad87-203">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="3ad87-204">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="3ad87-205">按一下 [ *頁面/電影* ] 資料夾 > **加入** > **新** 的樣板 ...]</span><span class="sxs-lookup"><span data-stu-id="3ad87-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![前述指示中的圖片。](model/_static/scaMac.png)

1. <span data-ttu-id="3ad87-207">在 [ **新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步** ] 選取 [頁面]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="3ad87-209">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="3ad87-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="3ad87-210">在 **要使用的 DbCoNtext 類別中：** row，將類別命名為 *Razor PagesMovie。 RazorPagesMovieCoNtext* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="3ad87-211">選取 [完成]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-211">Select **Finish**.</span></span>

   ![前述指示中的圖片。](model/_static/5/arpMac.png)

<span data-ttu-id="3ad87-213">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3ad87-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3ad87-214">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="3ad87-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3ad87-215">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="3ad87-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3ad87-216">下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="3ad87-217">`IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="3ad87-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="3ad87-218">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="3ad87-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-220">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3ad87-221">*Pages/電影* ： Create 、 Delete 、Details、Edit 和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-221">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ad87-222">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3ad87-223">已更新</span><span class="sxs-lookup"><span data-stu-id="3ad87-223">Updated</span></span>

* <span data-ttu-id="3ad87-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-224">*Startup.cs*</span></span>

<span data-ttu-id="3ad87-225">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ad87-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ad87-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3ad87-227">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="3ad87-228">*Pages/電影* ： Create 、 Delete 、Details、Edit 和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-228">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="3ad87-229">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-230">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3ad87-231">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3ad87-232">*Pages/電影* ： Create 、 Delete 、Details、Edit 和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-232">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ad87-233">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3ad87-234">已更新</span><span class="sxs-lookup"><span data-stu-id="3ad87-234">Updated</span></span>

* <span data-ttu-id="3ad87-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-235">*Startup.cs*</span></span>

<span data-ttu-id="3ad87-236">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="3ad87-237">Create 使用 EF 的遷移功能的初始資料庫架構</span><span class="sxs-lookup"><span data-stu-id="3ad87-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="3ad87-238">Entity Framework Core 中的「遷移」功能提供了一種方法，可讓您：</span><span class="sxs-lookup"><span data-stu-id="3ad87-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="3ad87-239">Create 初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="3ad87-239">Create the initial database schema.</span></span>
* <span data-ttu-id="3ad87-240">以累加方式更新資料庫架構，使其與應用程式的資料模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="3ad87-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="3ad87-241">資料庫中的現有資料會保留下來。</span><span class="sxs-lookup"><span data-stu-id="3ad87-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-243">在本節中，會使用 **封裝管理員主控台** (PMC) 視窗：</span><span class="sxs-lookup"><span data-stu-id="3ad87-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="3ad87-244">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="3ad87-244">Add an initial migration.</span></span>
* <span data-ttu-id="3ad87-245">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="3ad87-246">從 [工具] 功能表中，選取 [NuGet 封裝管理員] **[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 功能表](model/_static/5/pmc.png)

1. <span data-ttu-id="3ad87-248">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="3ad87-250">執行下列 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="3ad87-251">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="3ad87-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3ad87-252">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="3ad87-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3ad87-253">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="3ad87-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3ad87-254">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="3ad87-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="3ad87-255">`migrations` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="3ad87-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3ad87-256">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="3ad87-257">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="3ad87-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="3ad87-258">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="3ad87-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="3ad87-259">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="3ad87-260">在此情況下，會 `update` `Up` 在建立資料庫的 *遷移/ \<time-stamp> _Initial Create .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3ad87-262">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="3ad87-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3ad87-263">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3ad87-264">服務（例如 EF Core 資料庫內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3ad87-265">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3ad87-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3ad87-266">取得資料庫內容執行個體的建構函式程式碼會顯示在本教學課程稍後部分。</span><span class="sxs-lookup"><span data-stu-id="3ad87-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3ad87-267">「樣板」工具會自動建立資料庫內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="3ad87-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3ad87-268">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3ad87-269">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="3ad87-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3ad87-270">`RazorPagesMovieContext`協調模型 EF Core 功能，例如 Create 讀取、更新和 Delete `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3ad87-271">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3ad87-272">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="3ad87-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ad87-273">上述程式碼會建立實體集的[DbSet \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601)屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="3ad87-274">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3ad87-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3ad87-275">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3ad87-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3ad87-276">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3ad87-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3ad87-277">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-278">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3ad87-279">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="3ad87-280">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3ad87-280">Test the app</span></span>

1. <span data-ttu-id="3ad87-281">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="3ad87-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="3ad87-282">如果您收到下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="3ad87-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="3ad87-283">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="3ad87-284">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="3ad87-284">Test the **Create** link.</span></span>

   ![：：：非 loc (建立) ：：：頁面](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="3ad87-286">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="3ad87-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3ad87-287">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="3ad87-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3ad87-288">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="3ad87-289">測試 [ **編輯** ]、[ **詳細資料** ] 和 [ **Delete** 連結]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-289">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="3ad87-290">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ad87-291">其他資源</span><span class="sxs-lookup"><span data-stu-id="3ad87-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3ad87-292">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3ad87-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="3ad87-293">在本節中，會新增類別來管理電影。</span><span class="sxs-lookup"><span data-stu-id="3ad87-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="3ad87-294">應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="3ad87-295">EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="3ad87-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="3ad87-296">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3ad87-297">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3ad87-298">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3ad87-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3ad87-299">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="3ad87-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-301">以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3ad87-302">將資料夾命名為 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-302">Name the folder *Models*.</span></span>

<span data-ttu-id="3ad87-303">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="3ad87-304">選取 [ **新增**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="3ad87-305">將類別命名為 **Movie** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-305">Name the class **Movie**.</span></span>

<span data-ttu-id="3ad87-306">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-307">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-308">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-309">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-310">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-310">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-311">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-312">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ad87-313">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ad87-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ad87-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3ad87-315">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="3ad87-316">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3ad87-317">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-318">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-319">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-320">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-321">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-321">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-322">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-323">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ad87-324">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3ad87-325">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="3ad87-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="3ad87-326">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="3ad87-326">Add a database context class</span></span>

* <span data-ttu-id="3ad87-327">在 *Razor PagesMovie* 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="3ad87-328">將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3ad87-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ad87-329">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="3ad87-330">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3ad87-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="3ad87-331">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="3ad87-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="3ad87-332">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="3ad87-332">Add a database connection string</span></span>

<span data-ttu-id="3ad87-333">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="3ad87-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="3ad87-334">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="3ad87-334">Register the database context</span></span>

<span data-ttu-id="3ad87-335">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="3ad87-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="3ad87-336">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="3ad87-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-337">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3ad87-338">在 [ **方案] 工具視窗** 中，按一下 [ **Razor PagesMovie** ] 專案，然後選取 [ **加入** > **新資料夾**...]。命名資料夾 *模型* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-338">In the **Solution Tool Window** , control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="3ad87-339">以滑鼠右鍵按一下 [ *模型* ] 資料夾，然後 **選取 [** > **新增檔案 ...** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="3ad87-340">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="3ad87-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="3ad87-341">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="3ad87-342">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="3ad87-343">將類別命名為 **Movie** ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="3ad87-344">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-345">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-346">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-347">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-348">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-348">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-349">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-350">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="3ad87-351">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="3ad87-352">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="3ad87-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3ad87-353">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="3ad87-353">Scaffold the movie model</span></span>

<span data-ttu-id="3ad87-354">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="3ad87-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3ad87-355">也就是說，「樣板」工具會針對 Create movie 模型產生、讀取、更新和 Delete (CRUD) 作業的頁面。</span><span class="sxs-lookup"><span data-stu-id="3ad87-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-357">Create*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3ad87-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3ad87-358">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3ad87-359">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="3ad87-360">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="3ad87-362">在 [ **新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="3ad87-364">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="3ad87-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3ad87-365">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3ad87-366">在 [ **資料內容類別] 資料** 列中，選取 **+** (加號，) 簽署並變更 PagesMovie 所產生的名稱 Razor 。 **模型** 。 RazorPagesMovieCoNtext 至 Razor PagesMovie。 **資料** 。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="3ad87-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie. **Models**.RazorPagesMovieContext to RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="3ad87-367">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="3ad87-368">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3ad87-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="3ad87-369">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="3ad87-369">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="3ad87-371">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3ad87-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ad87-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ad87-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3ad87-373">在專案目錄中開啟命令視窗，其中包含 *Program.cs* 、 *Startup.cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="3ad87-374">若 **為 Windows** ：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3ad87-375">**針對 macOS 與 Linux** ：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3ad87-376">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="3ad87-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="3ad87-377">選項</span><span class="sxs-lookup"><span data-stu-id="3ad87-377">Option</span></span>               | <span data-ttu-id="3ad87-378">Description</span><span class="sxs-lookup"><span data-stu-id="3ad87-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3ad87-379">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="3ad87-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3ad87-380">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="3ad87-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3ad87-381">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="3ad87-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3ad87-382">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="3ad87-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3ad87-383">新增 `_ValidationScriptsPartial` 至編輯和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="3ad87-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3ad87-384">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="3ad87-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3ad87-385">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3ad87-386">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="3ad87-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3ad87-387">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="3ad87-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3ad87-388">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="3ad87-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="3ad87-389">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="3ad87-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-390">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3ad87-391">Create*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3ad87-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3ad87-392">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3ad87-393">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="3ad87-394">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新** 的樣板 ...]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="3ad87-396">在 [ **新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步** ] 選取 [頁面]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="3ad87-398">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="3ad87-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3ad87-399">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie (Razor PagesMovie) 模型** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3ad87-400">在 [ **資料內容類別** ] 列中，輸入新類別的名稱 Razor PagesMovie。 **資料** 。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="3ad87-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="3ad87-401">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="3ad87-402">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3ad87-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="3ad87-403">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="3ad87-403">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="3ad87-405">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3ad87-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="3ad87-406">新增 EF 工具</span><span class="sxs-lookup"><span data-stu-id="3ad87-406">Add EF tools</span></span>

<span data-ttu-id="3ad87-407">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="3ad87-408">上述命令會新增 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="3ad87-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="3ad87-409">如需詳細資訊，請參閱 [Entity Framework Core 工具參考-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3ad87-410">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="3ad87-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3ad87-411">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="3ad87-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3ad87-412">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="3ad87-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="3ad87-413">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="3ad87-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="3ad87-414">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="3ad87-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-416">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3ad87-417">*Pages/電影* ： Create 、 Delete 、Details、Edit 和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-417">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ad87-418">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3ad87-419">已更新</span><span class="sxs-lookup"><span data-stu-id="3ad87-419">Updated</span></span>

* <span data-ttu-id="3ad87-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-420">*Startup.cs*</span></span>

<span data-ttu-id="3ad87-421">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-422">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3ad87-423">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3ad87-424">*Pages/電影* ： Create 、 Delete 、Details、Edit 和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-424">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ad87-425">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3ad87-426">已更新</span><span class="sxs-lookup"><span data-stu-id="3ad87-426">Updated</span></span>

* <span data-ttu-id="3ad87-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-427">*Startup.cs*</span></span>

<span data-ttu-id="3ad87-428">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ad87-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ad87-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3ad87-430">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="3ad87-431">*Pages/電影* ： Create 、 Delete 、Details、Edit 和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-431">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="3ad87-432">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3ad87-433">初始移轉</span><span class="sxs-lookup"><span data-stu-id="3ad87-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-435">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="3ad87-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="3ad87-436">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="3ad87-436">Add an initial migration.</span></span>
* <span data-ttu-id="3ad87-437">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="3ad87-438">從 [工具] 功能表中，選取 [NuGet 封裝管理員] **[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3ad87-440">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-441">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3ad87-442">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="3ad87-443">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="3ad87-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3ad87-444">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="3ad87-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3ad87-445">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="3ad87-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3ad87-446">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="3ad87-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="3ad87-447">遷移命令會產生程式碼來建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="3ad87-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="3ad87-448">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="3ad87-449">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="3ad87-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="3ad87-450">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="3ad87-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="3ad87-451">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="3ad87-452">在此情況下，會 `update` `Up` 在建立資料庫的  *遷移/ \<time-stamp> _Initial Create .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3ad87-454">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="3ad87-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3ad87-455">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3ad87-456">服務（例如 EF Core 資料庫內容內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3ad87-457">需要這些服務的元件（例如 Razor 頁面）是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3ad87-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="3ad87-458">本教學課程稍後會顯示取得資料庫內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="3ad87-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3ad87-459">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="3ad87-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3ad87-460">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3ad87-461">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="3ad87-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3ad87-462">`RazorPagesMovieContext`協調模型 EF Core 功能，例如 Create 讀取、更新和 Delete `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3ad87-463">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3ad87-464">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="3ad87-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ad87-465">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3ad87-466">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3ad87-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3ad87-467">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3ad87-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3ad87-468">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3ad87-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3ad87-469">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-470">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3ad87-471">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3ad87-472">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3ad87-472">Test the app</span></span>

* <span data-ttu-id="3ad87-473">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="3ad87-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3ad87-474">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="3ad87-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3ad87-475">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3ad87-476">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="3ad87-476">Test the **Create** link.</span></span>

  ![：：：非 loc (建立) ：：：頁面](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="3ad87-478">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="3ad87-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3ad87-479">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="3ad87-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3ad87-480">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3ad87-481">測試 [ **編輯** ]、[ **詳細資料** ] 和 [ **Delete** 連結]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-481">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="3ad87-482">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ad87-483">其他資源</span><span class="sxs-lookup"><span data-stu-id="3ad87-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3ad87-484">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3ad87-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3ad87-485">在本節中，會新增類別來管理跨平臺 [SQLite 資料庫](https://www.sqlite.org/index.html)中的電影。</span><span class="sxs-lookup"><span data-stu-id="3ad87-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="3ad87-486">從 ASP.NET Core 範本建立的應用程式會使用 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="3ad87-487">應用程式的模型類別可搭配 [Entity Framework Core (EF Core) ](/ef/core) ([SQLite EF Core 資料庫提供者](/ef/core/providers/sqlite)) 使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="3ad87-488">EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="3ad87-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="3ad87-489">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3ad87-490">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3ad87-491">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3ad87-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3ad87-492">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="3ad87-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-494">以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3ad87-495">將資料夾命名為 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-495">Name the folder *Models*.</span></span>

<span data-ttu-id="3ad87-496">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="3ad87-497">選取 [ **新增**  >  **類別** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="3ad87-498">將類別命名為 **Movie** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-498">Name the class **Movie**.</span></span>

<span data-ttu-id="3ad87-499">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-500">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-501">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-502">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-503">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-503">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-504">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-505">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ad87-506">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ad87-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ad87-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3ad87-508">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="3ad87-509">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3ad87-510">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-511">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-512">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-513">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-514">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-514">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-515">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-516">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ad87-517">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3ad87-518">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="3ad87-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="3ad87-519">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="3ad87-519">Add a database context class</span></span>

<span data-ttu-id="3ad87-520">在 Razor PagesMovie 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="3ad87-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="3ad87-521">將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3ad87-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ad87-522">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="3ad87-523">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3ad87-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="3ad87-524">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="3ad87-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="3ad87-525">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="3ad87-525">Add a database connection string</span></span>

<span data-ttu-id="3ad87-526">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="3ad87-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="3ad87-527">新增必要的 NuGet 封裝</span><span class="sxs-lookup"><span data-stu-id="3ad87-527">Add required NuGet packages</span></span>

<span data-ttu-id="3ad87-528">執行下列 .NET Core CLI 命令以將 SQLite 和 >nswag.codegeneration.csharp 新增至專案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="3ad87-529">需要 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 封裝，才能進行 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="3ad87-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="3ad87-530">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="3ad87-530">Register the database context</span></span>

<span data-ttu-id="3ad87-531">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="3ad87-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="3ad87-532">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="3ad87-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="3ad87-533">建置專案來檢查錯誤。</span><span class="sxs-lookup"><span data-stu-id="3ad87-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-534">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3ad87-535">在 [ **方案工具] 視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後 **選取 [**  >  **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-535">In **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="3ad87-536">將資料夾命名為 *Models* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="3ad87-537">在 [ *模型* ] 資料夾上按一下控制項，然後選取 [ **加入** > **新** 檔案]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="3ad87-538">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="3ad87-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="3ad87-539">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="3ad87-540">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="3ad87-541">將類別命名為 **Movie** ，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="3ad87-542">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3ad87-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3ad87-543">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3ad87-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="3ad87-544">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3ad87-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3ad87-545">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3ad87-546">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3ad87-546">With this attribute:</span></span>

  * <span data-ttu-id="3ad87-547">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3ad87-548">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3ad87-549">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="3ad87-550">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="3ad87-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3ad87-551">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="3ad87-551">Scaffold the movie model</span></span>

<span data-ttu-id="3ad87-552">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="3ad87-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3ad87-553">也就是說，「樣板」工具會針對 Create movie 模型產生、讀取、更新和 Delete (CRUD) 作業的頁面。</span><span class="sxs-lookup"><span data-stu-id="3ad87-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-555">Create*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3ad87-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3ad87-556">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3ad87-557">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="3ad87-558">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="3ad87-560">在 [ **新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="3ad87-562">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="3ad87-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="3ad87-563">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3ad87-564">在 [ **資料內容類別] 資料** 列中，選取 **+** (加上) 號，然後接受產生的名稱 **Razor PagesMovie。 RazorPagesMovieCoNtext** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="3ad87-565">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="3ad87-565">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arp.png)

<span data-ttu-id="3ad87-567">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3ad87-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ad87-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ad87-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3ad87-569">在專案目錄中開啟命令視窗，其中包含 *Program.cs* 、 *Startup.cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="3ad87-570">若 **為 Windows** ：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3ad87-571">**針對 macOS 與 Linux** ：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3ad87-572">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="3ad87-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="3ad87-573">選項</span><span class="sxs-lookup"><span data-stu-id="3ad87-573">Option</span></span>               | <span data-ttu-id="3ad87-574">Description</span><span class="sxs-lookup"><span data-stu-id="3ad87-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3ad87-575">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="3ad87-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3ad87-576">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="3ad87-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3ad87-577">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="3ad87-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3ad87-578">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="3ad87-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3ad87-579">新增 `_ValidationScriptsPartial` 至編輯和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="3ad87-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3ad87-580">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="3ad87-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3ad87-581">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-582">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3ad87-583">Create*Pages/電影* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3ad87-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3ad87-584">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3ad87-585">將資料夾命名為 *電影* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="3ad87-586">在 [ *頁面/電影* ] 資料夾上按一下控制項，> **加入** > **新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="3ad87-588">在 [ **加入新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="3ad87-590">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="3ad87-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3ad87-591">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="3ad87-592">在 [ **資料內容類別] 資料** 列中，輸入 select **Razor PagesMovieCoNtext** ，這會使用正確的命名空間來建立新的資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3ad87-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="3ad87-593">在此情況下，它將會是 **Razor PagesMovie 模型。 RazorPagesMovieCoNtext** 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="3ad87-594">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="3ad87-594">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="3ad87-596">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3ad87-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="3ad87-597">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3ad87-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="3ad87-598">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="3ad87-598">Files created</span></span>

* <span data-ttu-id="3ad87-599">*Pages/電影* ： Create 、 Delete 、Details、Edit 和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-599">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3ad87-600">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="3ad87-601">檔案已更新</span><span class="sxs-lookup"><span data-stu-id="3ad87-601">File updated</span></span>

* <span data-ttu-id="3ad87-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3ad87-602">*Startup.cs*</span></span>

<span data-ttu-id="3ad87-603">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3ad87-604">初始移轉</span><span class="sxs-lookup"><span data-stu-id="3ad87-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ad87-606">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="3ad87-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="3ad87-607">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="3ad87-607">Add an initial migration.</span></span>
* <span data-ttu-id="3ad87-608">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="3ad87-609">從 [工具] 功能表中，選取 [NuGet 封裝管理員] **[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3ad87-611">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="3ad87-612">`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="3ad87-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3ad87-613">架構是以 PagesMovieCoNtext.cs 檔案中指定的模型為基礎 `DbContext` 。 *Razor*</span><span class="sxs-lookup"><span data-stu-id="3ad87-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="3ad87-614">`InitialCreate`引數是用來命名遷移。</span><span class="sxs-lookup"><span data-stu-id="3ad87-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="3ad87-615">您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="3ad87-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="3ad87-616">如需詳細資訊，請參閱<xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="3ad87-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="3ad87-617">此 `Update-Database` 命令會 `Up` 在 *遷移/ \<time-stamp> _Initial Create .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="3ad87-618">`Up` 方法會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="3ad87-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-619">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3ad87-620">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3ad87-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="3ad87-621">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="3ad87-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3ad87-622">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="3ad87-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3ad87-623">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="3ad87-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3ad87-624">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="3ad87-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ad87-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ad87-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3ad87-626">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="3ad87-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3ad87-627">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3ad87-628">服務 (例如 EF Core 資料庫內容內容) 會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="3ad87-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3ad87-629">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3ad87-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3ad87-630">本教學課程稍後會顯示取得資料庫內容 coNtextB 內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="3ad87-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3ad87-631">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="3ad87-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3ad87-632">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3ad87-633">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="3ad87-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="3ad87-634">`RazorPagesMovieContext`協調模型的 EF Core 功能，例如 Create 、讀取、更新和 Delete `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3ad87-635">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3ad87-636">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="3ad87-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3ad87-637">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="3ad87-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3ad87-638">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3ad87-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3ad87-639">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3ad87-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3ad87-640">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3ad87-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3ad87-641">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3ad87-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3ad87-642">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3ad87-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3ad87-643">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ad87-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3ad87-644">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3ad87-644">Test the app</span></span>

* <span data-ttu-id="3ad87-645">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="3ad87-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3ad87-646">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="3ad87-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3ad87-647">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3ad87-648">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="3ad87-648">Test the **Create** link.</span></span>

  ![：：：非 loc (建立) ：：：頁面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="3ad87-650">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="3ad87-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3ad87-651">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="3ad87-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3ad87-652">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3ad87-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3ad87-653">測試 [ **編輯** ]、[ **詳細資料** ] 和 [ **Delete** 連結]。</span><span class="sxs-lookup"><span data-stu-id="3ad87-653">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="3ad87-654">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3ad87-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ad87-655">其他資源</span><span class="sxs-lookup"><span data-stu-id="3ad87-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3ad87-656">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3ad87-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
