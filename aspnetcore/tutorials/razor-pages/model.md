---
title: 第2部分：加入模型
author: rick-anderson
description: 頁面上教學課程系列的第2部分 Razor 。 在本節中，會加入模型類別。
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 92bfda330399b43871b3ae0e6b609726f7ad4a91
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564051"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="0bfc0-104">第2部分：在 ASP.NET Core 中將模型新增至 Razor 頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="0bfc0-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="0bfc0-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0bfc0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="0bfc0-106">在本節中，您可以新增類別來管理資料庫中的電影。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="0bfc0-107">應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="0bfc0-108">EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="0bfc0-109">您會先撰寫模型類別，EF Core 建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="0bfc0-110">模型類別稱為 POCO 類別 (自 "**P**>lain-**O** ld **C** LR **O** bjects" ) ，因為它們沒有 EF Core 的相依性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="0bfc0-111">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0bfc0-112">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0bfc0-113">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="0bfc0-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0bfc0-115">在 **方案總管** 中，以滑鼠右鍵按一下 *Razor PagesMovie* 專案 **，> 新增**  >  **資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0bfc0-116">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="0bfc0-117">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="0bfc0-118">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="0bfc0-119">將類別命名為 *Movie*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="0bfc0-120">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-121">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-122">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-123">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-124">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-124">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-125">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-126">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0bfc0-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0bfc0-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0bfc0-128">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="0bfc0-129">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0bfc0-130">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-131">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-132">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-133">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-134">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-134">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-135">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-136">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0bfc0-137">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="0bfc0-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="0bfc0-138">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="0bfc0-138">Add a database context class</span></span>

1. <span data-ttu-id="0bfc0-139">在 *Razor PagesMovie* 專案中，建立一個名為 *Data* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="0bfc0-140">在 [*資料*] 資料夾中，使用下列程式碼加入名為 *Razor PagesMovieCoNtext.cs* 的檔案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="0bfc0-141">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0bfc0-142">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="0bfc0-143">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0bfc0-144">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="0bfc0-144">Add a database connection string</span></span>

<span data-ttu-id="0bfc0-145">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0bfc0-146">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="0bfc0-146">Register the database context</span></span>

1. <span data-ttu-id="0bfc0-147">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="0bfc0-148">使用中的相依性 [插入](xref:fundamentals/dependency-injection) 容器來註冊資料庫內容 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-149">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0bfc0-150">在 [**方案] 工具視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後選取 [**加入** > **新資料夾**...]。命名資料夾 *模型*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="0bfc0-151">以控制項按一下 [ *模型* ] 資料夾， **然後選取 [** > **新增檔案 ...**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="0bfc0-152">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="0bfc0-153">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="0bfc0-154">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="0bfc0-155">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="0bfc0-156">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-157">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-158">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-159">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-160">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-160">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-161">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-162">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="0bfc0-163">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="0bfc0-164">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0bfc0-165">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="0bfc0-165">Scaffold the movie model</span></span>

<span data-ttu-id="0bfc0-166">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0bfc0-167">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0bfc0-169">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="0bfc0-170">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="0bfc0-171">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="0bfc0-172">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![前述指示中的圖片。](model/_static/5/sca.png)

1. <span data-ttu-id="0bfc0-174">在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffold.png)

1. <span data-ttu-id="0bfc0-176">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="0bfc0-177">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="0bfc0-178">在 [資料內容類別] 資料列中，選取 **+** (加號)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="0bfc0-179">在 [ **加入資料內容** ] 對話方塊中， `RazorPagesMovie.Data.RazorPagesMovieContext` 會產生類別名稱。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-179">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="0bfc0-180">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-180">Select **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="0bfc0-182">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0bfc0-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0bfc0-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0bfc0-184">開啟包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案的命令 shell 至專案目錄。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="0bfc0-185">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0bfc0-186">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0bfc0-187">下表詳細說明 ASP.NET Core 程式碼產生器選項。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="0bfc0-188">選項</span><span class="sxs-lookup"><span data-stu-id="0bfc0-188">Option</span></span>               | <span data-ttu-id="0bfc0-189">描述</span><span class="sxs-lookup"><span data-stu-id="0bfc0-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0bfc0-190">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0bfc0-191">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0bfc0-192">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0bfc0-193">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0bfc0-194">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="0bfc0-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0bfc0-195">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0bfc0-196">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0bfc0-197">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="0bfc0-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0bfc0-198">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0bfc0-199">下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="0bfc0-200">`IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-201">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0bfc0-202">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="0bfc0-203">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="0bfc0-204">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="0bfc0-205">按一下 [ *頁面/電影* ] 資料夾 > **加入** > **新** 的樣板 ...]</span><span class="sxs-lookup"><span data-stu-id="0bfc0-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![前述指示中的圖片。](model/_static/scaMac.png)

1. <span data-ttu-id="0bfc0-207">在 [**新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步**] 選取 [頁面]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="0bfc0-209">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="0bfc0-210">在 **要使用的 DbCoNtext 類別中：** row，將類別命名為 `RazorPagesMovie.Data.RazorPagesMovieContext` 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-210">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="0bfc0-211">選取 [完成]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-211">Select **Finish**.</span></span>

   ![前述指示中的圖片。](model/_static/5/arpMac.png)

<span data-ttu-id="0bfc0-213">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0bfc0-214">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="0bfc0-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0bfc0-215">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0bfc0-216">下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="0bfc0-217">`IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="0bfc0-218">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="0bfc0-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-220">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0bfc0-221">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0bfc0-222">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0bfc0-223">已更新</span><span class="sxs-lookup"><span data-stu-id="0bfc0-223">Updated</span></span>

* <span data-ttu-id="0bfc0-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-224">*Startup.cs*</span></span>

<span data-ttu-id="0bfc0-225">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0bfc0-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0bfc0-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0bfc0-227">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="0bfc0-228">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="0bfc0-229">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-230">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0bfc0-231">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0bfc0-232">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0bfc0-233">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0bfc0-234">已更新</span><span class="sxs-lookup"><span data-stu-id="0bfc0-234">Updated</span></span>

* <span data-ttu-id="0bfc0-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-235">*Startup.cs*</span></span>

<span data-ttu-id="0bfc0-236">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="0bfc0-237">使用 EF 的遷移功能建立初始資料庫架構</span><span class="sxs-lookup"><span data-stu-id="0bfc0-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="0bfc0-238">Entity Framework Core 中的「遷移」功能提供了一種方法，可讓您：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="0bfc0-239">建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-239">Create the initial database schema.</span></span>
* <span data-ttu-id="0bfc0-240">以累加方式更新資料庫架構，使其與應用程式的資料模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="0bfc0-241">資料庫中的現有資料會保留下來。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-243">在本節中，會使用 **封裝管理員主控台** (PMC) 視窗：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="0bfc0-244">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-244">Add an initial migration.</span></span>
* <span data-ttu-id="0bfc0-245">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="0bfc0-246">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 功能表](model/_static/5/pmc.png)

1. <span data-ttu-id="0bfc0-248">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="0bfc0-250">執行下列 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="0bfc0-251">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0bfc0-252">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0bfc0-253">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="0bfc0-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0bfc0-254">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="0bfc0-255">`migrations` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="0bfc0-256">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="0bfc0-257">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="0bfc0-258">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="0bfc0-259">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="0bfc0-260">在此情況下，會 `update` `Up` 在建立資料庫的 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0bfc0-262">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="0bfc0-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0bfc0-263">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0bfc0-264">服務（例如 EF Core 資料庫內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0bfc0-265">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0bfc0-266">取得資料庫內容執行個體的建構函式程式碼會顯示在本教學課程稍後部分。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0bfc0-267">「樣板」工具會自動建立資料庫內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0bfc0-268">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0bfc0-269">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0bfc0-270">`RazorPagesMovieContext`協調模型 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0bfc0-271">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0bfc0-272">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0bfc0-273">上述程式碼會建立實體集的[DbSet \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601)屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="0bfc0-274">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0bfc0-275">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0bfc0-276">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0bfc0-277">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-278">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0bfc0-279">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="0bfc0-280">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="0bfc0-280">Test the app</span></span>

1. <span data-ttu-id="0bfc0-281">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="0bfc0-282">如果您收到下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="0bfc0-283">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="0bfc0-284">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-284">Test the **Create** link.</span></span>

   ![Create page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="0bfc0-286">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0bfc0-287">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0bfc0-288">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="0bfc0-289">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="0bfc0-290">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0bfc0-291">其他資源</span><span class="sxs-lookup"><span data-stu-id="0bfc0-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0bfc0-292">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="0bfc0-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="0bfc0-293">在本節中，會新增類別來管理電影。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="0bfc0-294">應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="0bfc0-295">EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="0bfc0-296">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="0bfc0-297">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0bfc0-298">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0bfc0-299">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="0bfc0-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-301">以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0bfc0-302">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-302">Name the folder *Models*.</span></span>

<span data-ttu-id="0bfc0-303">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="0bfc0-304">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="0bfc0-305">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-305">Name the class **Movie**.</span></span>

<span data-ttu-id="0bfc0-306">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-307">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-308">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-309">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-310">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-310">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-311">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-312">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0bfc0-313">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0bfc0-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0bfc0-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0bfc0-315">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="0bfc0-316">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0bfc0-317">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-318">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-319">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-320">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-321">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-321">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-322">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-323">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0bfc0-324">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0bfc0-325">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="0bfc0-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="0bfc0-326">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="0bfc0-326">Add a database context class</span></span>

* <span data-ttu-id="0bfc0-327">在 *Razor PagesMovie* 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="0bfc0-328">將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0bfc0-329">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0bfc0-330">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="0bfc0-331">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0bfc0-332">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="0bfc0-332">Add a database connection string</span></span>

<span data-ttu-id="0bfc0-333">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0bfc0-334">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="0bfc0-334">Register the database context</span></span>

<span data-ttu-id="0bfc0-335">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="0bfc0-336">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-337">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0bfc0-338">在 [**方案] 工具視窗** 中，按一下 [ **Razor PagesMovie** ] 專案，然後選取 [**加入** > **新資料夾**...]。命名資料夾 *模型*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="0bfc0-339">以滑鼠右鍵按一下 [ *模型* ] 資料夾，然後 **選取 [** > **新增檔案 ...**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="0bfc0-340">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="0bfc0-341">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="0bfc0-342">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="0bfc0-343">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="0bfc0-344">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-345">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-346">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-347">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-348">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-348">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-349">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-350">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="0bfc0-351">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="0bfc0-352">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0bfc0-353">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="0bfc0-353">Scaffold the movie model</span></span>

<span data-ttu-id="0bfc0-354">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0bfc0-355">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-357">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0bfc0-358">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0bfc0-359">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="0bfc0-360">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="0bfc0-362">在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="0bfc0-364">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0bfc0-365">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0bfc0-366">在 [ **資料內容類別] 資料** 列中，選取 **+** (加號，) 簽署並變更 PagesMovie 所產生的名稱 Razor 。**模型**。 RazorPagesMovieCoNtext 至 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="0bfc0-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="0bfc0-367">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="0bfc0-368">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="0bfc0-369">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-369">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="0bfc0-371">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0bfc0-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0bfc0-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0bfc0-373">在專案目錄中開啟命令視窗，其中包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="0bfc0-374">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0bfc0-375">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0bfc0-376">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="0bfc0-377">選項</span><span class="sxs-lookup"><span data-stu-id="0bfc0-377">Option</span></span>               | <span data-ttu-id="0bfc0-378">描述</span><span class="sxs-lookup"><span data-stu-id="0bfc0-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0bfc0-379">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0bfc0-380">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0bfc0-381">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0bfc0-382">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0bfc0-383">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="0bfc0-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0bfc0-384">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0bfc0-385">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0bfc0-386">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="0bfc0-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0bfc0-387">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0bfc0-388">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="0bfc0-389">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-390">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0bfc0-391">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0bfc0-392">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0bfc0-393">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="0bfc0-394">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新** 的樣板 ...]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="0bfc0-396">在 [**新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步**] 選取 [頁面]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="0bfc0-398">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0bfc0-399">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie (Razor PagesMovie) 模型**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0bfc0-400">在 [ **資料內容類別** ] 列中，輸入新類別的名稱 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="0bfc0-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="0bfc0-401">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="0bfc0-402">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="0bfc0-403">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-403">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="0bfc0-405">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="0bfc0-406">新增 EF 工具</span><span class="sxs-lookup"><span data-stu-id="0bfc0-406">Add EF tools</span></span>

<span data-ttu-id="0bfc0-407">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="0bfc0-408">上述命令會新增 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="0bfc0-409">如需詳細資訊，請參閱 [Entity Framework Core 工具參考-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0bfc0-410">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="0bfc0-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0bfc0-411">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0bfc0-412">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="0bfc0-413">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="0bfc0-414">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="0bfc0-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-416">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0bfc0-417">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0bfc0-418">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0bfc0-419">已更新</span><span class="sxs-lookup"><span data-stu-id="0bfc0-419">Updated</span></span>

* <span data-ttu-id="0bfc0-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-420">*Startup.cs*</span></span>

<span data-ttu-id="0bfc0-421">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-422">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0bfc0-423">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="0bfc0-424">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0bfc0-425">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="0bfc0-426">已更新</span><span class="sxs-lookup"><span data-stu-id="0bfc0-426">Updated</span></span>

* <span data-ttu-id="0bfc0-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-427">*Startup.cs*</span></span>

<span data-ttu-id="0bfc0-428">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0bfc0-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0bfc0-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0bfc0-430">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="0bfc0-431">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="0bfc0-432">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="0bfc0-433">初始移轉</span><span class="sxs-lookup"><span data-stu-id="0bfc0-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-435">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="0bfc0-436">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-436">Add an initial migration.</span></span>
* <span data-ttu-id="0bfc0-437">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="0bfc0-438">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0bfc0-440">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-441">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="0bfc0-442">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="0bfc0-443">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0bfc0-444">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0bfc0-445">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="0bfc0-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0bfc0-446">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="0bfc0-447">遷移命令會產生程式碼來建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="0bfc0-448">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="0bfc0-449">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="0bfc0-450">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="0bfc0-451">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="0bfc0-452">在此情況下，會 `update` `Up` 在建立資料庫的  *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0bfc0-454">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="0bfc0-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0bfc0-455">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0bfc0-456">服務（例如 EF Core 資料庫內容內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0bfc0-457">需要這些服務的元件（例如 Razor 頁面）是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="0bfc0-458">本教學課程稍後會顯示取得資料庫內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0bfc0-459">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0bfc0-460">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0bfc0-461">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0bfc0-462">`RazorPagesMovieContext`協調模型 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0bfc0-463">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0bfc0-464">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0bfc0-465">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0bfc0-466">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0bfc0-467">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0bfc0-468">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0bfc0-469">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-470">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0bfc0-471">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="0bfc0-472">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="0bfc0-472">Test the app</span></span>

* <span data-ttu-id="0bfc0-473">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="0bfc0-474">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="0bfc0-475">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="0bfc0-476">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-476">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="0bfc0-478">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0bfc0-479">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0bfc0-480">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0bfc0-481">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="0bfc0-482">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0bfc0-483">其他資源</span><span class="sxs-lookup"><span data-stu-id="0bfc0-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0bfc0-484">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="0bfc0-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0bfc0-485">在本節中，會新增類別來管理跨平臺 [SQLite 資料庫](https://www.sqlite.org/index.html)中的電影。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="0bfc0-486">從 ASP.NET Core 範本建立的應用程式會使用 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="0bfc0-487">應用程式的模型類別可搭配 [Entity Framework Core (EF Core) ](/ef/core) ([SQLite EF Core 資料庫提供者](/ef/core/providers/sqlite)) 使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="0bfc0-488">EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="0bfc0-489">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="0bfc0-490">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0bfc0-491">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0bfc0-492">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="0bfc0-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-494">以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0bfc0-495">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-495">Name the folder *Models*.</span></span>

<span data-ttu-id="0bfc0-496">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="0bfc0-497">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="0bfc0-498">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-498">Name the class **Movie**.</span></span>

<span data-ttu-id="0bfc0-499">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-500">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-501">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-502">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-503">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-503">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-504">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-505">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0bfc0-506">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0bfc0-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0bfc0-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0bfc0-508">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="0bfc0-509">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="0bfc0-510">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-511">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-512">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-513">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-514">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-514">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-515">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-516">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0bfc0-517">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="0bfc0-518">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="0bfc0-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="0bfc0-519">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="0bfc0-519">Add a database context class</span></span>

<span data-ttu-id="0bfc0-520">在 Razor PagesMovie 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="0bfc0-521">將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0bfc0-522">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0bfc0-523">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="0bfc0-524">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0bfc0-525">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="0bfc0-525">Add a database connection string</span></span>

<span data-ttu-id="0bfc0-526">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="0bfc0-527">新增必要的 NuGet 封裝</span><span class="sxs-lookup"><span data-stu-id="0bfc0-527">Add required NuGet packages</span></span>

<span data-ttu-id="0bfc0-528">執行下列 .NET Core CLI 命令以將 SQLite 和 >nswag.codegeneration.csharp 新增至專案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="0bfc0-529">需要 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 封裝，才能進行 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0bfc0-530">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="0bfc0-530">Register the database context</span></span>

<span data-ttu-id="0bfc0-531">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="0bfc0-532">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="0bfc0-533">建置專案來檢查錯誤。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-534">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0bfc0-535">在 [**方案工具] 視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後 **選取 [**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="0bfc0-536">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="0bfc0-537">在 [ *模型* ] 資料夾上按一下控制項，然後選取 [ **加入** > **新** 檔案]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="0bfc0-538">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="0bfc0-539">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="0bfc0-540">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="0bfc0-541">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="0bfc0-542">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0bfc0-543">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="0bfc0-544">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="0bfc0-545">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0bfc0-546">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-546">With this attribute:</span></span>

  * <span data-ttu-id="0bfc0-547">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0bfc0-548">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0bfc0-549">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="0bfc0-550">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0bfc0-551">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="0bfc0-551">Scaffold the movie model</span></span>

<span data-ttu-id="0bfc0-552">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0bfc0-553">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-555">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0bfc0-556">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0bfc0-557">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="0bfc0-558">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="0bfc0-560">在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="0bfc0-562">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="0bfc0-563">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0bfc0-564">在 [**資料內容類別] 資料** 列中，選取 **+** (加上) 號，然後接受產生的名稱 **Razor PagesMovie。 RazorPagesMovieCoNtext**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="0bfc0-565">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-565">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arp.png)

<span data-ttu-id="0bfc0-567">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0bfc0-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0bfc0-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0bfc0-569">在專案目錄中開啟命令視窗，其中包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="0bfc0-570">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0bfc0-571">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="0bfc0-572">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="0bfc0-573">選項</span><span class="sxs-lookup"><span data-stu-id="0bfc0-573">Option</span></span>               | <span data-ttu-id="0bfc0-574">描述</span><span class="sxs-lookup"><span data-stu-id="0bfc0-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="0bfc0-575">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="0bfc0-576">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="0bfc0-577">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="0bfc0-578">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="0bfc0-579">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="0bfc0-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="0bfc0-580">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="0bfc0-581">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-582">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0bfc0-583">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0bfc0-584">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0bfc0-585">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="0bfc0-586">在 [ *頁面/電影* ] 資料夾上按一下控制項，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="0bfc0-588">在 [**加入新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="0bfc0-590">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0bfc0-591">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="0bfc0-592">在 [**資料內容類別] 資料** 列中，輸入 select **Razor PagesMovieCoNtext** ，這會使用正確的命名空間來建立新的資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="0bfc0-593">在此情況下，它將會是 **Razor PagesMovie 模型。 RazorPagesMovieCoNtext**。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="0bfc0-594">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-594">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="0bfc0-596">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="0bfc0-597">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0bfc0-598">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="0bfc0-598">Files created</span></span>

* <span data-ttu-id="0bfc0-599">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0bfc0-600">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="0bfc0-601">檔案已更新</span><span class="sxs-lookup"><span data-stu-id="0bfc0-601">File updated</span></span>

* <span data-ttu-id="0bfc0-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-602">*Startup.cs*</span></span>

<span data-ttu-id="0bfc0-603">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="0bfc0-604">初始移轉</span><span class="sxs-lookup"><span data-stu-id="0bfc0-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bfc0-606">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="0bfc0-607">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-607">Add an initial migration.</span></span>
* <span data-ttu-id="0bfc0-608">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="0bfc0-609">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0bfc0-611">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="0bfc0-612">`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="0bfc0-613">架構是以 PagesMovieCoNtext.cs 檔案中指定的模型為基礎 `DbContext` 。 *Razor*</span><span class="sxs-lookup"><span data-stu-id="0bfc0-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="0bfc0-614">`InitialCreate`引數是用來命名遷移。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="0bfc0-615">您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="0bfc0-616">如需詳細資訊，請參閱<xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="0bfc0-617">此 `Update-Database` 命令會 `Up` 在 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="0bfc0-618">`Up` 方法會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-619">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="0bfc0-620">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="0bfc0-621">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0bfc0-622">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0bfc0-623">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="0bfc0-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0bfc0-624">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bfc0-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bfc0-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0bfc0-626">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="0bfc0-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0bfc0-627">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0bfc0-628">服務 (例如 EF Core 資料庫內容內容) 會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0bfc0-629">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0bfc0-630">本教學課程稍後會顯示取得資料庫內容 coNtextB 內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0bfc0-631">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0bfc0-632">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0bfc0-633">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="0bfc0-634">`RazorPagesMovieContext`座標 EF Core 的功能，例如建立、讀取、更新和刪除 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="0bfc0-635">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="0bfc0-636">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0bfc0-637">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0bfc0-638">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0bfc0-639">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0bfc0-640">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="0bfc0-641">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0bfc0-642">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0bfc0-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0bfc0-643">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="0bfc0-644">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="0bfc0-644">Test the app</span></span>

* <span data-ttu-id="0bfc0-645">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="0bfc0-646">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="0bfc0-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="0bfc0-647">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="0bfc0-648">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-648">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="0bfc0-650">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0bfc0-651">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0bfc0-652">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0bfc0-653">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="0bfc0-654">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="0bfc0-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0bfc0-655">其他資源</span><span class="sxs-lookup"><span data-stu-id="0bfc0-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0bfc0-656">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="0bfc0-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
