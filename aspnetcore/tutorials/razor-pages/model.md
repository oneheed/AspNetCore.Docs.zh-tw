---
title: 第2部分，將模型新增至 Razor 頁面應用程式中的 ASP.NET Core
author: rick-anderson
description: 頁面上教學課程系列的第2部分 Razor 。
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: 6b50f46863a6dabb01bcf0976a42abb504e6f7b7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020453"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="b62c9-103">第2部分，將模型新增至 Razor 頁面應用程式中的 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b62c9-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="b62c9-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b62c9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="b62c9-105">在本節中，會加入類別來管理電影。</span><span class="sxs-lookup"><span data-stu-id="b62c9-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="b62c9-106">應用程式的模型類別會使用[Entity Framework Core (EF Core) ](/ef/core)來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="b62c9-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="b62c9-107">EF Core 是可簡化資料存取的物件關聯式對應程式， (O/RM) 。</span><span class="sxs-lookup"><span data-stu-id="b62c9-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="b62c9-108">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="b62c9-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="b62c9-109">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="b62c9-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="b62c9-110">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="b62c9-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b62c9-112">以滑鼠右鍵按一下 [ \*\* Razor PagesMovie\*\* ] 專案 **，>**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="b62c9-113">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-113">Name the folder *Models*.</span></span>

<span data-ttu-id="b62c9-114">以滑鼠右鍵按一下 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b62c9-114">Right click the *Models* folder.</span></span> <span data-ttu-id="b62c9-115">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="b62c9-116">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="b62c9-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b62c9-118">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b62c9-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="b62c9-119">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b62c9-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b62c9-121">在 Solution Pad 中，以滑鼠右鍵按一下\*\* Razor PagesMovie**專案，然後選取 [**加入\*\* > **新資料夾**]。將資料夾命名為*模型*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="b62c9-122">以滑鼠右鍵按一下 [*模型*] 資料夾，然後**選取 [** > **新增檔案 ...**]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="b62c9-123">在 [新增檔案]\*\*\*\* 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="b62c9-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b62c9-124">在左窗格中選取 [一般]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="b62c9-125">在中央窗格中選取 [類別是空的]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="b62c9-126">將類別命名為 **Movie**，然後選取 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="b62c9-127">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="b62c9-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="b62c9-128">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="b62c9-128">Scaffold the movie model</span></span>

<span data-ttu-id="b62c9-129">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="b62c9-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="b62c9-130">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="b62c9-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b62c9-132">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="b62c9-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="b62c9-133">以滑鼠右鍵按一下 [Pages]\*\* 資料夾 > [新增]\*\* [新增資料夾]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="b62c9-134">將資料夾命名為 *Movies*</span><span class="sxs-lookup"><span data-stu-id="b62c9-134">Name the folder *Movies*</span></span>

<span data-ttu-id="b62c9-135">以滑鼠右鍵按一下 [Pages/Movies]\*\* 資料夾 > [新增]\*\* [新增 Scaffolded 項目]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="b62c9-137">在 [**新增 Scaffold** ] 對話方塊中， \*\* Razor 使用 Entity Framework (CRUD) \*\* [新增] 選取頁面 > \*\* \*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="b62c9-139">完成\*\* Razor 使用 ENTITY FRAMEWORK (CRUD) \*\* ] 對話方塊中的 [新增頁面]：</span><span class="sxs-lookup"><span data-stu-id="b62c9-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b62c9-140">在 [**模型類別**] 下拉式下拉中，選取 [ \*\*Movie (Razor PagesMovie]) \*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="b62c9-141">在 [**資料內容類別**] 列中，選取 **+** (加上) 簽署]，然後從 PagesMovie 變更產生的名稱 Razor 。**模型**。 RazorPagesMovieCoNtext 至 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="b62c9-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="b62c9-142">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="b62c9-143">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="b62c9-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="b62c9-144">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-144">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="b62c9-146">*appsettings.json* 檔案會隨即更新用來連線到本機資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="b62c9-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="b62c9-148">在專案目錄 (包含 *Program.cs*、*Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。</span><span class="sxs-lookup"><span data-stu-id="b62c9-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b62c9-149">安裝 Scaffolding 工具：</span><span class="sxs-lookup"><span data-stu-id="b62c9-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b62c9-150">**針對 Windows**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b62c9-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="b62c9-151">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b62c9-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b62c9-153">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="b62c9-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="b62c9-154">以滑鼠右鍵按一下 [Pages]\*\* 資料夾 > [新增]\*\* [新增資料夾]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="b62c9-155">將資料夾命名為 *Movies*</span><span class="sxs-lookup"><span data-stu-id="b62c9-155">Name the folder *Movies*</span></span>

<span data-ttu-id="b62c9-156">以滑鼠右鍵按一下 [ *Pages/電影*] 資料夾 >**加入** > **新**的 [樣板]]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="b62c9-158">在 [**新**的架構] 對話方塊中，選取 [ \*\* Razor 使用 Entity Framework (CRUD) \*\* > **下一個頁面]**。</span><span class="sxs-lookup"><span data-stu-id="b62c9-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="b62c9-160">完成\*\* Razor 使用 ENTITY FRAMEWORK (CRUD) \*\* ] 對話方塊中的 [新增頁面]：</span><span class="sxs-lookup"><span data-stu-id="b62c9-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b62c9-161">在 [**模型類別**] 下拉式選中，選取或輸入\*\*Movie (Razor PagesMovie) \*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="b62c9-162">在 [**資料內容類別**] 列中，輸入新類別的名稱 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="b62c9-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="b62c9-163">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="b62c9-164">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="b62c9-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="b62c9-165">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-165">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="b62c9-167">*appsettings.json* 檔案會隨即更新用來連線到本機資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="b62c9-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="b62c9-168">新增 EF 工具</span><span class="sxs-lookup"><span data-stu-id="b62c9-168">Add EF tools</span></span>

<span data-ttu-id="b62c9-169">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="b62c9-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="b62c9-170">上述命令會加入 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="b62c9-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="b62c9-171">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="b62c9-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b62c9-173">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="b62c9-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="b62c9-174">*Pages/Movies*：建立、刪除、詳細資料、編輯和索引。</span><span class="sxs-lookup"><span data-stu-id="b62c9-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="b62c9-175">*資料/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="b62c9-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="b62c9-176">已更新</span><span class="sxs-lookup"><span data-stu-id="b62c9-176">Updated</span></span>

* <span data-ttu-id="b62c9-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b62c9-177">*Startup.cs*</span></span>

<span data-ttu-id="b62c9-178">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="b62c9-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b62c9-180">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="b62c9-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="b62c9-181">*Pages/Movies*：建立、刪除、詳細資料、編輯和索引。</span><span class="sxs-lookup"><span data-stu-id="b62c9-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="b62c9-182">*資料/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="b62c9-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="b62c9-183">已更新</span><span class="sxs-lookup"><span data-stu-id="b62c9-183">Updated</span></span>

* <span data-ttu-id="b62c9-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b62c9-184">*Startup.cs*</span></span>

<span data-ttu-id="b62c9-185">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="b62c9-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b62c9-187">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="b62c9-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="b62c9-188">*Pages/Movies*：建立、刪除、詳細資料、編輯和索引。</span><span class="sxs-lookup"><span data-stu-id="b62c9-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="b62c9-189">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="b62c9-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="b62c9-190">初始移轉</span><span class="sxs-lookup"><span data-stu-id="b62c9-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b62c9-192">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="b62c9-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="b62c9-193">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="b62c9-193">Add an initial migration.</span></span>
* <span data-ttu-id="b62c9-194">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="b62c9-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="b62c9-195">從 [工具]\*\*\*\* 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="b62c9-197">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="b62c9-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="b62c9-200">上述命令會產生下列警告：「實體類型 ' Movie ' 的十進位資料行 ' Price ' 未指定任何類型。</span><span class="sxs-lookup"><span data-stu-id="b62c9-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="b62c9-201">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="b62c9-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="b62c9-202">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="b62c9-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="b62c9-203">您可以忽略該警告，稍後的教學課程中將修正此問題。</span><span class="sxs-lookup"><span data-stu-id="b62c9-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="b62c9-204">[遷移] 命令會產生程式碼來建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="b62c9-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="b62c9-205">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="b62c9-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="b62c9-206">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="b62c9-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="b62c9-207">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="b62c9-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="b62c9-208">命令會在尚未套用的 `update` `Up` 遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="b62c9-209">在此情況下，會 `update` `Up` 在建立資料庫的「*遷移/ \<time-stamp> _InitialCreate .cs*檔案」中執行方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b62c9-211">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="b62c9-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b62c9-212">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b62c9-213">服務 (例如 EF Core DB 內容) 是在應用程式啟動期間使用相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="b62c9-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b62c9-214">需要這些服務的元件 (例如頁面) 會透過「函式 Razor 參數」提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="b62c9-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b62c9-215">取得資料庫內容執行個體的建構函式程式碼，本教學課程中稍後會示範。</span><span class="sxs-lookup"><span data-stu-id="b62c9-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b62c9-216">Scaffolding 工具會自動建立資料庫內容，並向相依性插入容器註冊。</span><span class="sxs-lookup"><span data-stu-id="b62c9-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b62c9-217">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b62c9-218">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="b62c9-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="b62c9-219">`RazorPagesMovieContext` 會協調 `Movie` 模型的 EF Core 功能 (建立、更新、刪除等)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="b62c9-220">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b62c9-221">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="b62c9-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="b62c9-222">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="b62c9-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b62c9-223">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="b62c9-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b62c9-224">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="b62c9-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b62c9-225">連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="b62c9-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b62c9-226">作為本機開發之用，[ASP.NET Core configuration system](xref:fundamentals/configuration/index) 會從 *appsettings.json* 檔案讀取連接字串。</span><span class="sxs-lookup"><span data-stu-id="b62c9-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b62c9-228">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-229">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b62c9-230">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="b62c9-231">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="b62c9-231">Test the app</span></span>

* <span data-ttu-id="b62c9-232">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="b62c9-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="b62c9-233">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="b62c9-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="b62c9-234">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="b62c9-235">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="b62c9-235">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="b62c9-237">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="b62c9-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b62c9-238">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="b62c9-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b62c9-239">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b62c9-240">測試 [編輯]\*\*\*\*、[詳細資料]\*\*\*\* 和 [刪除]\*\*\*\* 連結。</span><span class="sxs-lookup"><span data-stu-id="b62c9-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="b62c9-241">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="b62c9-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b62c9-242">其他資源</span><span class="sxs-lookup"><span data-stu-id="b62c9-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b62c9-243">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
>  使用[下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="b62c9-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b62c9-244">在本節中，會加入類別來管理跨平臺[SQLite 資料庫](https://www.sqlite.org/index.html)中的電影。</span><span class="sxs-lookup"><span data-stu-id="b62c9-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="b62c9-245">從 ASP.NET Core 範本建立的應用程式會使用 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="b62c9-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="b62c9-246">應用程式的模型類別會與[Entity Framework Core (EF Core) ](/ef/core) ([SQLite EF Core 資料庫提供者](/ef/core/providers/sqlite)) 用來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="b62c9-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="b62c9-247">EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="b62c9-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="b62c9-248">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="b62c9-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="b62c9-249">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="b62c9-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="b62c9-250">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="b62c9-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b62c9-252">以滑鼠右鍵按一下 [ \*\* Razor PagesMovie\*\* ] 專案 **，>**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="b62c9-253">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-253">Name the folder *Models*.</span></span>

<span data-ttu-id="b62c9-254">以滑鼠右鍵按一下 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b62c9-254">Right click the *Models* folder.</span></span> <span data-ttu-id="b62c9-255">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="b62c9-256">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="b62c9-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b62c9-258">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b62c9-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="b62c9-259">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="b62c9-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-260">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b62c9-261">在方案總管中，以滑鼠右鍵按一下\*\* Razor PagesMovie**專案，然後選取 [**加入\*\*  >  **新資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="b62c9-262">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="b62c9-263">以滑鼠右鍵按一下 [Models]\*\* 資料夾，然後選取 [新增]\*\* [新增檔案]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="b62c9-264">在 [新增檔案]\*\*\*\* 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="b62c9-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b62c9-265">在左窗格中選取 [一般]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="b62c9-266">在中央窗格中選取 [類別是空的]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="b62c9-267">將類別命名為 **Movie**，然後選取 [新增]\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="b62c9-268">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="b62c9-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="b62c9-269">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="b62c9-269">Scaffold the movie model</span></span>

<span data-ttu-id="b62c9-270">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="b62c9-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="b62c9-271">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="b62c9-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b62c9-273">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="b62c9-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="b62c9-274">以滑鼠右鍵按一下 [Pages]\*\* 資料夾 > [新增]\*\* [新增資料夾]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="b62c9-275">將資料夾命名為 *Movies*</span><span class="sxs-lookup"><span data-stu-id="b62c9-275">Name the folder *Movies*</span></span>

<span data-ttu-id="b62c9-276">以滑鼠右鍵按一下 [Pages/Movies]\*\* 資料夾 > [新增]\*\* [新增 Scaffolded 項目]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="b62c9-278">在 [**新增 Scaffold** ] 對話方塊中， \*\* Razor 使用 Entity Framework (CRUD) \*\* [新增] 選取頁面 > \*\* \*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="b62c9-280">完成\*\* Razor 使用 ENTITY FRAMEWORK (CRUD) \*\* ] 對話方塊中的 [新增頁面]：</span><span class="sxs-lookup"><span data-stu-id="b62c9-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="b62c9-281">在 [**模型類別**] 下拉式下拉中，選取 [ \*\*Movie (Razor PagesMovie]) \*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="b62c9-282">在 [**資料內容類別**] 列中，選取 **+** (加上) 簽署]，並接受產生的名稱\*\* Razor PagesMovie。 RazorPagesMovieCoNtext\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="b62c9-283">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-283">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arp.png)

<span data-ttu-id="b62c9-285">*appsettings.json* 檔案會隨即更新用來連線到本機資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="b62c9-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="b62c9-287">在專案目錄 (包含 *Program.cs*、*Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。</span><span class="sxs-lookup"><span data-stu-id="b62c9-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="b62c9-288">**針對 Windows**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b62c9-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="b62c9-289">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="b62c9-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-290">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b62c9-291">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="b62c9-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="b62c9-292">以滑鼠右鍵按一下 [Pages]\*\* 資料夾 > [新增]\*\* [新增資料夾]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="b62c9-293">將資料夾命名為 *Movies*</span><span class="sxs-lookup"><span data-stu-id="b62c9-293">Name the folder *Movies*</span></span>

<span data-ttu-id="b62c9-294">以滑鼠右鍵按一下 [Pages/Movies]\*\* 資料夾 > [新增]\*\* [新增 Scaffolded 項目]\*\* > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="b62c9-296">在 [**加入新**的架構] 對話方塊中，選取 [ \*\* Razor 使用 Entity Framework (CRUD) \*\*新增] 的頁面 > \*\* \*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="b62c9-298">完成\*\* Razor 使用 ENTITY FRAMEWORK (CRUD) \*\* ] 對話方塊中的 [新增頁面]：</span><span class="sxs-lookup"><span data-stu-id="b62c9-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b62c9-299">在 [**模型類別**] 下拉式選，選取或輸入**Movie**。</span><span class="sxs-lookup"><span data-stu-id="b62c9-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="b62c9-300">在 [**資料內容類別**] 列中，輸入選取\*\* Razor PagesMovieCoNtext\*\* ，這會使用正確的命名空間建立新的 db 內容類別。</span><span class="sxs-lookup"><span data-stu-id="b62c9-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="b62c9-301">在此情況下，它會是\*\* Razor PagesMovie。 RazorPagesMovieCoNtext\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="b62c9-302">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="b62c9-302">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="b62c9-304">*appsettings.json* 檔案會隨即更新用來連線到本機資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="b62c9-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="b62c9-305">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="b62c9-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="b62c9-306">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="b62c9-306">Files created</span></span>

* <span data-ttu-id="b62c9-307">*Pages/Movies*：建立、刪除、詳細資料、編輯和索引。</span><span class="sxs-lookup"><span data-stu-id="b62c9-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="b62c9-308">*資料/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="b62c9-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="b62c9-309">檔案已更新</span><span class="sxs-lookup"><span data-stu-id="b62c9-309">File updated</span></span>

* <span data-ttu-id="b62c9-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b62c9-310">*Startup.cs*</span></span>

<span data-ttu-id="b62c9-311">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="b62c9-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="b62c9-312">初始移轉</span><span class="sxs-lookup"><span data-stu-id="b62c9-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b62c9-314">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="b62c9-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="b62c9-315">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="b62c9-315">Add an initial migration.</span></span>
* <span data-ttu-id="b62c9-316">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="b62c9-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="b62c9-317">從 [工具]\*\*\*\* 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b62c9-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="b62c9-319">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="b62c9-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="b62c9-320">`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="b62c9-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="b62c9-321">架構是以 `DbContext` \* Razor PagesMovieCoNtext.cs\*檔案) 的 (中指定的模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="b62c9-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="b62c9-322">`InitialCreate`引數是用來命名遷移。</span><span class="sxs-lookup"><span data-stu-id="b62c9-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="b62c9-323">您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="b62c9-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="b62c9-324">如需詳細資訊，請參閱<xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="b62c9-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="b62c9-325">`Update-Database`命令會 `Up` 在*遷移/ \<time-stamp> _InitialCreate .cs*檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="b62c9-326">`Up` 方法會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="b62c9-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-328">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="b62c9-329">上述命令會產生下列警告：「*實體類型 ' Movie ' 的十進位資料行 ' Price ' 未指定任何類型。如果值不符合預設的有效位數和小數位數，這會導致以無訊息模式截斷值。使用 ' HasColumnType ( # A1 ' 明確指定可容納所有值的 SQL server 資料行類型。*」您可以忽略該警告，其將在稍後的教學課程中修正。</span><span class="sxs-lookup"><span data-stu-id="b62c9-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62c9-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62c9-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b62c9-331">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="b62c9-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b62c9-332">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b62c9-333">服務 (例如 EF Core DB 內容) 是在應用程式啟動期間使用相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="b62c9-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b62c9-334">需要這些服務的元件 (例如頁面) 會透過「函式 Razor 參數」提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="b62c9-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b62c9-335">取得資料庫內容執行個體的建構函式程式碼，本教學課程中稍後會示範。</span><span class="sxs-lookup"><span data-stu-id="b62c9-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b62c9-336">Scaffolding 工具會自動建立資料庫內容，並向相依性插入容器註冊。</span><span class="sxs-lookup"><span data-stu-id="b62c9-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b62c9-337">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b62c9-338">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="b62c9-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="b62c9-339">`RazorPagesMovieContext` 會協調 `Movie` 模型的 EF Core 功能 (建立、更新、刪除等)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="b62c9-340">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b62c9-341">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="b62c9-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="b62c9-342">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="b62c9-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b62c9-343">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="b62c9-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b62c9-344">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="b62c9-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b62c9-345">連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="b62c9-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b62c9-346">作為本機開發之用，[ASP.NET Core configuration system](xref:fundamentals/configuration/index) 會從 *appsettings.json* 檔案讀取連接字串。</span><span class="sxs-lookup"><span data-stu-id="b62c9-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b62c9-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b62c9-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b62c9-348">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62c9-349">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b62c9-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b62c9-350">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b62c9-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="b62c9-351">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="b62c9-351">Test the app</span></span>

* <span data-ttu-id="b62c9-352">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="b62c9-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="b62c9-353">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="b62c9-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="b62c9-354">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="b62c9-355">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="b62c9-355">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="b62c9-357">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="b62c9-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b62c9-358">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="b62c9-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b62c9-359">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b62c9-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b62c9-360">測試 [編輯]\*\*\*\*、[詳細資料]\*\*\*\* 和 [刪除]\*\*\*\* 連結。</span><span class="sxs-lookup"><span data-stu-id="b62c9-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="b62c9-361">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="b62c9-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b62c9-362">其他資源</span><span class="sxs-lookup"><span data-stu-id="b62c9-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b62c9-363">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
>  使用[下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="b62c9-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
