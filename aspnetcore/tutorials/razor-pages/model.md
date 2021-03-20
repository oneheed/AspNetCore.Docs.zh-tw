---
title: 第2部分：加入模型
author: rick-anderson
description: 頁面上教學課程系列的第2部分 Razor 。 在本節中，會加入模型類別。
ms.author: riande
ms.date: 03/10/2021
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
ms.openlocfilehash: 1173113b8bb035212cb9b84e7763970b9d5092a3
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711606"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="3827e-104">第2部分：在 ASP.NET Core 中將模型新增至 Razor 頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="3827e-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="3827e-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3827e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="3827e-106">在本節中，您可以新增類別來管理資料庫中的電影。</span><span class="sxs-lookup"><span data-stu-id="3827e-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="3827e-107">應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="3827e-108">EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="3827e-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="3827e-109">您會先撰寫模型類別，EF Core 建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="3827e-110">模型類別稱為 POCO 類別 (自 "**P**>lain-**O** ld **C** LR **O** bjects" ) ，因為它們沒有 EF Core 的相依性。</span><span class="sxs-lookup"><span data-stu-id="3827e-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="3827e-111">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="3827e-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3827e-112">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3827e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3827e-113">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="3827e-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3827e-115">在 **方案總管** 中，以滑鼠右鍵按一下 *Razor PagesMovie* 專案 **，> 新增**  >  **資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3827e-116">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="3827e-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="3827e-117">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="3827e-118">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="3827e-119">將類別命名為 *Movie*。</span><span class="sxs-lookup"><span data-stu-id="3827e-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="3827e-120">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-121">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-122">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-123">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-124">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-124">With this attribute:</span></span>

  * <span data-ttu-id="3827e-125">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-126">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3827e-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3827e-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3827e-128">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="3827e-129">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3827e-130">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-131">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-132">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-133">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-134">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-134">With this attribute:</span></span>

  * <span data-ttu-id="3827e-135">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-136">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3827e-137">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="3827e-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3827e-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3827e-139">在 [**方案] 工具視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後選取 [**加入** > **新資料夾**...]。命名資料夾 *模型*。</span><span class="sxs-lookup"><span data-stu-id="3827e-139">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="3827e-140">以控制項按一下 [ *模型* ] 資料夾， **然後選取 [** > **新增檔案 ...**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-140">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="3827e-141">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="3827e-141">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="3827e-142">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="3827e-142">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="3827e-143">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="3827e-143">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="3827e-144">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3827e-144">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="3827e-145">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-145">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-146">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-146">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-147">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-147">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-148">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-148">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-149">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-149">With this attribute:</span></span>

  * <span data-ttu-id="3827e-150">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-150">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-151">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-151">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="3827e-152">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3827e-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="3827e-153">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="3827e-153">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3827e-154">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="3827e-154">Scaffold the movie model</span></span>

<span data-ttu-id="3827e-155">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="3827e-155">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3827e-156">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="3827e-156">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-157">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3827e-158">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3827e-158">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="3827e-159">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-159">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="3827e-160">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="3827e-160">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="3827e-161">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-161">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![前述指示中的圖片。](model/_static/5/sca.png)

1. <span data-ttu-id="3827e-163">在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3827e-163">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffold.png)

1. <span data-ttu-id="3827e-165">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="3827e-165">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="3827e-166">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="3827e-166">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="3827e-167">在 [資料內容類別] 資料列中，選取 **+** (加號)。</span><span class="sxs-lookup"><span data-stu-id="3827e-167">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="3827e-168">在 [ **加入資料內容** ] 對話方塊中， `RazorPagesMovie.Data.RazorPagesMovieContext` 會產生類別名稱。</span><span class="sxs-lookup"><span data-stu-id="3827e-168">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="3827e-169">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3827e-169">Select **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="3827e-171">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3827e-171">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3827e-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3827e-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3827e-173">在專案目錄中開啟命令 shell，其中包含 *.cs*、 *Startup .cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-173">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="3827e-174">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-174">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3827e-175">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-175">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3827e-176">下表詳細說明 ASP.NET Core 程式碼產生器選項。</span><span class="sxs-lookup"><span data-stu-id="3827e-176">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="3827e-177">選項</span><span class="sxs-lookup"><span data-stu-id="3827e-177">Option</span></span>               | <span data-ttu-id="3827e-178">Description</span><span class="sxs-lookup"><span data-stu-id="3827e-178">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3827e-179">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="3827e-179">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3827e-180">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="3827e-180">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3827e-181">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="3827e-181">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3827e-182">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="3827e-182">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3827e-183">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="3827e-183">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3827e-184">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="3827e-184">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3827e-185">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="3827e-185">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

[!INCLUDE[](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3827e-186">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3827e-187">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3827e-187">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="3827e-188">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-188">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="3827e-189">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="3827e-189">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="3827e-190">按一下 [ *頁面/電影* ] 資料夾 > **加入** > **新** 的樣板 ...]</span><span class="sxs-lookup"><span data-stu-id="3827e-190">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![前述指示中的圖片。](model/_static/scaMac.png)

1. <span data-ttu-id="3827e-192">在 [**新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步**] 選取 [頁面]。</span><span class="sxs-lookup"><span data-stu-id="3827e-192">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="3827e-194">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="3827e-194">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="3827e-195">在 **要使用的 DbCoNtext 類別中：** row，將類別命名為 `RazorPagesMovie.Data.RazorPagesMovieContext` 。</span><span class="sxs-lookup"><span data-stu-id="3827e-195">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="3827e-196">選取 [完成]。</span><span class="sxs-lookup"><span data-stu-id="3827e-196">Select **Finish**.</span></span>

   ![前述指示中的圖片。](model/_static/5/arpMac.png)

<span data-ttu-id="3827e-198">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3827e-198">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

[!INCLUDE[](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="3827e-199">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="3827e-199">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-200">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-201">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3827e-201">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3827e-202">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3827e-202">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3827e-203">*Data/ Razor PagesMovieCoNtext .cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-203">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3827e-204">已更新</span><span class="sxs-lookup"><span data-stu-id="3827e-204">Updated</span></span>

* <span data-ttu-id="3827e-205">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-205">*Startup.cs*</span></span>

<span data-ttu-id="3827e-206">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-206">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3827e-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3827e-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3827e-208">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3827e-208">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="3827e-209">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3827e-209">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="3827e-210">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-210">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3827e-211">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3827e-212">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3827e-212">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3827e-213">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3827e-213">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3827e-214">*Data/ Razor PagesMovieCoNtext .cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-214">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3827e-215">已更新</span><span class="sxs-lookup"><span data-stu-id="3827e-215">Updated</span></span>

* <span data-ttu-id="3827e-216">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-216">*Startup.cs*</span></span>

<span data-ttu-id="3827e-217">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-217">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="3827e-218">使用 EF 的遷移功能建立初始資料庫架構</span><span class="sxs-lookup"><span data-stu-id="3827e-218">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="3827e-219">Entity Framework Core 中的「遷移」功能提供了一種方法，可讓您：</span><span class="sxs-lookup"><span data-stu-id="3827e-219">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="3827e-220">建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="3827e-220">Create the initial database schema.</span></span>
* <span data-ttu-id="3827e-221">以累加方式更新資料庫架構，使其與應用程式的資料模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="3827e-221">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="3827e-222">資料庫中的現有資料會保留下來。</span><span class="sxs-lookup"><span data-stu-id="3827e-222">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-223">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-224">在本節中，會使用 **封裝管理員主控台** (PMC) 視窗：</span><span class="sxs-lookup"><span data-stu-id="3827e-224">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="3827e-225">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="3827e-225">Add an initial migration.</span></span>
* <span data-ttu-id="3827e-226">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-226">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="3827e-227">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="3827e-227">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 功能表](model/_static/5/pmc.png)

1. <span data-ttu-id="3827e-229">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-229">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3827e-230">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="3827e-231">執行下列 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-231">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="3827e-232">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="3827e-232">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3827e-233">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="3827e-233">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3827e-234">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="3827e-234">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3827e-235">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="3827e-235">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="3827e-236">`migrations` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="3827e-236">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3827e-237">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="3827e-237">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="3827e-238">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="3827e-238">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="3827e-239">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="3827e-239">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="3827e-240">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-240">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="3827e-241">在此情況下，會 `update` `Up` 在建立資料庫的 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-241">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-242">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3827e-243">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="3827e-243">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3827e-244">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3827e-244">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3827e-245">服務（例如 EF Core 資料庫內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="3827e-245">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3827e-246">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3827e-246">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3827e-247">取得資料庫內容執行個體的建構函式程式碼會顯示在本教學課程稍後部分。</span><span class="sxs-lookup"><span data-stu-id="3827e-247">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3827e-248">「樣板」工具會自動建立資料庫內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="3827e-248">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3827e-249">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-249">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3827e-250">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="3827e-250">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3827e-251">`RazorPagesMovieContext`協調模型 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="3827e-251">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3827e-252">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="3827e-252">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3827e-253">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="3827e-253">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3827e-254">上述程式碼會建立實體集的[DbSet \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601)屬性。</span><span class="sxs-lookup"><span data-stu-id="3827e-254">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="3827e-255">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3827e-255">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3827e-256">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3827e-256">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3827e-257">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3827e-257">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3827e-258">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3827e-258">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3827e-259">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3827e-260">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-260">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="3827e-261">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3827e-261">Test the app</span></span>

1. <span data-ttu-id="3827e-262">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="3827e-262">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="3827e-263">如果您收到下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="3827e-263">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="3827e-264">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="3827e-264">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="3827e-265">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="3827e-265">Test the **Create** link.</span></span>

   ![Create page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="3827e-267">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="3827e-267">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3827e-268">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="3827e-268">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3827e-269">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3827e-269">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="3827e-270">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="3827e-270">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="3827e-271">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-271">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3827e-272">其他資源</span><span class="sxs-lookup"><span data-stu-id="3827e-272">Additional resources</span></span>


> [!div class="step-by-step"]
> <span data-ttu-id="3827e-273">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3827e-273">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="3827e-274">在本節中，會新增類別來管理電影。</span><span class="sxs-lookup"><span data-stu-id="3827e-274">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="3827e-275">應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-275">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="3827e-276">EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="3827e-276">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="3827e-277">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="3827e-277">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3827e-278">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="3827e-278">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3827e-279">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3827e-279">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3827e-280">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="3827e-280">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-282">以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-282">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3827e-283">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="3827e-283">Name the folder *Models*.</span></span>

<span data-ttu-id="3827e-284">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-284">Right-click the *Models* folder.</span></span> <span data-ttu-id="3827e-285">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-285">Select **Add** > **Class**.</span></span> <span data-ttu-id="3827e-286">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="3827e-286">Name the class **Movie**.</span></span>

<span data-ttu-id="3827e-287">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-287">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-288">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-288">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-289">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-289">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-290">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-290">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-291">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-291">With this attribute:</span></span>

  * <span data-ttu-id="3827e-292">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-292">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-293">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-293">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3827e-294">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3827e-294">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3827e-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3827e-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3827e-296">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-296">Add a folder named *Models*.</span></span>
* <span data-ttu-id="3827e-297">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-297">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3827e-298">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-298">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-299">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-299">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-300">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-300">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-301">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-301">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-302">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-302">With this attribute:</span></span>

  * <span data-ttu-id="3827e-303">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-303">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-304">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-304">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3827e-305">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3827e-305">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3827e-306">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="3827e-306">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="3827e-307">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="3827e-307">Add a database context class</span></span>

* <span data-ttu-id="3827e-308">在 *Razor PagesMovie* 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-308">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="3827e-309">將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3827e-309">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3827e-310">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="3827e-310">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="3827e-311">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3827e-311">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="3827e-312">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="3827e-312">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="3827e-313">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="3827e-313">Add a database connection string</span></span>

<span data-ttu-id="3827e-314">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="3827e-314">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="3827e-315">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="3827e-315">Register the database context</span></span>

<span data-ttu-id="3827e-316">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="3827e-316">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="3827e-317">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="3827e-317">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3827e-318">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3827e-319">在 [**方案] 工具視窗** 中，按一下 [ **Razor PagesMovie** ] 專案，然後選取 [**加入** > **新資料夾**...]。命名資料夾 *模型*。</span><span class="sxs-lookup"><span data-stu-id="3827e-319">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="3827e-320">以滑鼠右鍵按一下 [ *模型* ] 資料夾，然後 **選取 [** > **新增檔案 ...**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-320">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="3827e-321">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="3827e-321">In the **New File** dialog:</span></span>

  * <span data-ttu-id="3827e-322">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="3827e-322">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="3827e-323">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="3827e-323">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="3827e-324">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3827e-324">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="3827e-325">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-325">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-326">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-326">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-327">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-327">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-328">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-328">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-329">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-329">With this attribute:</span></span>

  * <span data-ttu-id="3827e-330">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-330">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-331">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-331">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="3827e-332">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3827e-332">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="3827e-333">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="3827e-333">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3827e-334">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="3827e-334">Scaffold the movie model</span></span>

<span data-ttu-id="3827e-335">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="3827e-335">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3827e-336">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="3827e-336">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-337">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-337">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-338">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3827e-338">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3827e-339">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-339">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3827e-340">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="3827e-340">Name the folder *Movies*.</span></span>

<span data-ttu-id="3827e-341">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-341">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="3827e-343">在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3827e-343">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="3827e-345">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="3827e-345">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3827e-346">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="3827e-346">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3827e-347">在 [ **資料內容類別] 資料** 列中，選取 **+** (加號，) 簽署並變更 PagesMovie 所產生的名稱 Razor 。**模型**。 RazorPagesMovieCoNtext 至 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="3827e-347">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="3827e-348">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3827e-348">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="3827e-349">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3827e-349">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="3827e-350">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3827e-350">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="3827e-352">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3827e-352">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3827e-353">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3827e-353">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3827e-354">在專案目錄中開啟命令視窗，其中包含 *程式 .cs*、 *Startup .cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-354">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="3827e-355">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-355">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3827e-356">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-356">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3827e-357">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="3827e-357">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="3827e-358">選項</span><span class="sxs-lookup"><span data-stu-id="3827e-358">Option</span></span>               | <span data-ttu-id="3827e-359">Description</span><span class="sxs-lookup"><span data-stu-id="3827e-359">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3827e-360">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="3827e-360">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3827e-361">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="3827e-361">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3827e-362">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="3827e-362">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3827e-363">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="3827e-363">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3827e-364">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="3827e-364">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3827e-365">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="3827e-365">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3827e-366">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="3827e-366">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3827e-367">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="3827e-367">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3827e-368">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="3827e-368">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3827e-369">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="3827e-369">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="3827e-370">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="3827e-370">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3827e-371">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3827e-372">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3827e-372">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3827e-373">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-373">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3827e-374">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="3827e-374">Name the folder *Movies*.</span></span>

<span data-ttu-id="3827e-375">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新** 的樣板 ...]。</span><span class="sxs-lookup"><span data-stu-id="3827e-375">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="3827e-377">在 [**新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步**] 選取 [頁面]。</span><span class="sxs-lookup"><span data-stu-id="3827e-377">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="3827e-379">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="3827e-379">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3827e-380">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie (Razor PagesMovie) 模型**。</span><span class="sxs-lookup"><span data-stu-id="3827e-380">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3827e-381">在 [ **資料內容類別** ] 列中，輸入新類別的名稱 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="3827e-381">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="3827e-382">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3827e-382">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="3827e-383">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3827e-383">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="3827e-384">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3827e-384">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="3827e-386">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3827e-386">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="3827e-387">新增 EF 工具</span><span class="sxs-lookup"><span data-stu-id="3827e-387">Add EF tools</span></span>

<span data-ttu-id="3827e-388">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-388">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="3827e-389">上述命令會新增 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="3827e-389">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="3827e-390">如需詳細資訊，請參閱 [Entity Framework Core 工具參考-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="3827e-390">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3827e-391">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="3827e-391">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3827e-392">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="3827e-392">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3827e-393">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="3827e-393">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="3827e-394">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="3827e-394">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="3827e-395">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="3827e-395">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-396">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-397">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3827e-397">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3827e-398">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3827e-398">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3827e-399">*Data/ Razor PagesMovieCoNtext .cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-399">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3827e-400">已更新</span><span class="sxs-lookup"><span data-stu-id="3827e-400">Updated</span></span>

* <span data-ttu-id="3827e-401">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-401">*Startup.cs*</span></span>

<span data-ttu-id="3827e-402">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-402">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3827e-403">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3827e-404">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3827e-404">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="3827e-405">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3827e-405">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3827e-406">*Data/ Razor PagesMovieCoNtext .cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-406">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="3827e-407">已更新</span><span class="sxs-lookup"><span data-stu-id="3827e-407">Updated</span></span>

* <span data-ttu-id="3827e-408">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-408">*Startup.cs*</span></span>

<span data-ttu-id="3827e-409">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-409">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3827e-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3827e-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3827e-411">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3827e-411">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="3827e-412">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3827e-412">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="3827e-413">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-413">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3827e-414">初始移轉</span><span class="sxs-lookup"><span data-stu-id="3827e-414">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-416">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="3827e-416">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="3827e-417">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="3827e-417">Add an initial migration.</span></span>
* <span data-ttu-id="3827e-418">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-418">Update the database with the initial migration.</span></span>

<span data-ttu-id="3827e-419">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="3827e-419">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3827e-421">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-421">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3827e-422">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-422">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3827e-423">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-423">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="3827e-424">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="3827e-424">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3827e-425">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="3827e-425">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3827e-426">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="3827e-426">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3827e-427">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="3827e-427">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="3827e-428">遷移命令會產生程式碼來建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="3827e-428">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="3827e-429">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="3827e-429">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="3827e-430">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="3827e-430">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="3827e-431">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="3827e-431">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="3827e-432">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-432">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="3827e-433">在此情況下，會 `update` `Up` 在建立資料庫的  *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-433">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-434">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3827e-435">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="3827e-435">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3827e-436">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3827e-436">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3827e-437">服務（例如 EF Core 資料庫內容內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="3827e-437">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3827e-438">需要這些服務的元件（例如 Razor 頁面）是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3827e-438">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="3827e-439">本教學課程稍後會顯示取得資料庫內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="3827e-439">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3827e-440">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="3827e-440">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3827e-441">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-441">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3827e-442">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="3827e-442">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3827e-443">`RazorPagesMovieContext`協調模型 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="3827e-443">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3827e-444">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="3827e-444">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3827e-445">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="3827e-445">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3827e-446">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="3827e-446">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3827e-447">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3827e-447">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3827e-448">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3827e-448">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3827e-449">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3827e-449">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3827e-450">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3827e-450">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3827e-451">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-451">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3827e-452">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-452">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3827e-453">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3827e-453">Test the app</span></span>

* <span data-ttu-id="3827e-454">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="3827e-454">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3827e-455">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="3827e-455">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3827e-456">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="3827e-456">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3827e-457">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="3827e-457">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="3827e-459">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="3827e-459">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3827e-460">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="3827e-460">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3827e-461">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3827e-461">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3827e-462">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="3827e-462">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="3827e-463">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-463">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3827e-464">其他資源</span><span class="sxs-lookup"><span data-stu-id="3827e-464">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3827e-465">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3827e-465">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3827e-466">在本節中，會新增類別來管理跨平臺 [SQLite 資料庫](https://www.sqlite.org/index.html)中的電影。</span><span class="sxs-lookup"><span data-stu-id="3827e-466">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="3827e-467">從 ASP.NET Core 範本建立的應用程式會使用 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-467">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="3827e-468">應用程式的模型類別可搭配 [Entity Framework Core (EF Core) ](/ef/core) ([SQLite EF Core 資料庫提供者](/ef/core/providers/sqlite)) 使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-468">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="3827e-469">EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="3827e-469">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="3827e-470">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="3827e-470">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3827e-471">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="3827e-471">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="3827e-472">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3827e-472">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="3827e-473">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="3827e-473">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-474">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-474">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-475">以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-475">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3827e-476">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="3827e-476">Name the folder *Models*.</span></span>

<span data-ttu-id="3827e-477">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-477">Right-click the *Models* folder.</span></span> <span data-ttu-id="3827e-478">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-478">Select **Add** > **Class**.</span></span> <span data-ttu-id="3827e-479">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="3827e-479">Name the class **Movie**.</span></span>

<span data-ttu-id="3827e-480">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-480">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-481">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-481">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-482">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-482">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-483">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-483">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-484">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-484">With this attribute:</span></span>

  * <span data-ttu-id="3827e-485">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-485">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-486">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-486">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3827e-487">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3827e-487">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3827e-488">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3827e-488">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3827e-489">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-489">Add a folder named *Models*.</span></span>
* <span data-ttu-id="3827e-490">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-490">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="3827e-491">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-491">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-492">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-492">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-493">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-493">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-494">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-494">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-495">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-495">With this attribute:</span></span>

  * <span data-ttu-id="3827e-496">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-496">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-497">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-497">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3827e-498">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3827e-498">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="3827e-499">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="3827e-499">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="3827e-500">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="3827e-500">Add a database context class</span></span>

<span data-ttu-id="3827e-501">在 Razor PagesMovie 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="3827e-501">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="3827e-502">將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3827e-502">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3827e-503">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="3827e-503">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="3827e-504">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3827e-504">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="3827e-505">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="3827e-505">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="3827e-506">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="3827e-506">Add a database connection string</span></span>

<span data-ttu-id="3827e-507">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="3827e-507">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="3827e-508">新增必要的 NuGet 封裝</span><span class="sxs-lookup"><span data-stu-id="3827e-508">Add required NuGet packages</span></span>

<span data-ttu-id="3827e-509">執行下列 .NET Core CLI 命令，以將 SQLite 和 CodeGeneration.Design 新增至專案：</span><span class="sxs-lookup"><span data-stu-id="3827e-509">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="3827e-510">需要 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 封裝，才能進行 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="3827e-510">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="3827e-511">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="3827e-511">Register the database context</span></span>

<span data-ttu-id="3827e-512">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="3827e-512">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="3827e-513">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="3827e-513">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="3827e-514">建置專案來檢查錯誤。</span><span class="sxs-lookup"><span data-stu-id="3827e-514">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3827e-515">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-515">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3827e-516">在 [**方案工具] 視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後 **選取 [**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-516">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="3827e-517">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="3827e-517">Name the folder *Models*.</span></span>
* <span data-ttu-id="3827e-518">在 [ *模型* ] 資料夾上按一下控制項，然後選取 [ **加入** > **新** 檔案]。</span><span class="sxs-lookup"><span data-stu-id="3827e-518">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="3827e-519">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="3827e-519">In the **New File** dialog:</span></span>

  * <span data-ttu-id="3827e-520">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="3827e-520">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="3827e-521">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="3827e-521">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="3827e-522">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3827e-522">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="3827e-523">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="3827e-523">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="3827e-524">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="3827e-524">The `Movie` class contains:</span></span>

* <span data-ttu-id="3827e-525">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="3827e-525">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="3827e-526">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="3827e-526">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3827e-527">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="3827e-527">With this attribute:</span></span>

  * <span data-ttu-id="3827e-528">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-528">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="3827e-529">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="3827e-529">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3827e-530">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="3827e-530">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="3827e-531">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="3827e-531">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3827e-532">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="3827e-532">Scaffold the movie model</span></span>

<span data-ttu-id="3827e-533">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="3827e-533">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3827e-534">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="3827e-534">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-535">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-535">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-536">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3827e-536">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3827e-537">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-537">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3827e-538">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="3827e-538">Name the folder *Movies*.</span></span>

<span data-ttu-id="3827e-539">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-539">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="3827e-541">在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3827e-541">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="3827e-543">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="3827e-543">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="3827e-544">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="3827e-544">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3827e-545">在 [**資料內容類別] 資料** 列中，選取 **+** (加上) 號，然後接受產生的名稱 **Razor PagesMovie。 RazorPagesMovieCoNtext**。</span><span class="sxs-lookup"><span data-stu-id="3827e-545">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="3827e-546">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3827e-546">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arp.png)

<span data-ttu-id="3827e-548">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3827e-548">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3827e-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3827e-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3827e-550">在專案目錄中開啟命令視窗，其中包含 *程式 .cs*、 *Startup .cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-550">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="3827e-551">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-551">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3827e-552">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-552">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="3827e-553">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="3827e-553">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="3827e-554">選項</span><span class="sxs-lookup"><span data-stu-id="3827e-554">Option</span></span>               | <span data-ttu-id="3827e-555">Description</span><span class="sxs-lookup"><span data-stu-id="3827e-555">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="3827e-556">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="3827e-556">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="3827e-557">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="3827e-557">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="3827e-558">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="3827e-558">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="3827e-559">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="3827e-559">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="3827e-560">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="3827e-560">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="3827e-561">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="3827e-561">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3827e-562">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="3827e-562">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3827e-563">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-563">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3827e-564">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="3827e-564">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3827e-565">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-565">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3827e-566">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="3827e-566">Name the folder *Movies*.</span></span>

<span data-ttu-id="3827e-567">在 [ *頁面/電影* ] 資料夾上按一下控制項，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="3827e-567">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="3827e-569">在 [**加入新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="3827e-569">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="3827e-571">**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：</span><span class="sxs-lookup"><span data-stu-id="3827e-571">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3827e-572">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="3827e-572">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="3827e-573">在 [**資料內容類別] 資料** 列中，輸入 select **Razor PagesMovieCoNtext** ，這會使用正確的命名空間來建立新的資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3827e-573">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="3827e-574">在此情況下，它將會是 **Razor PagesMovie 模型。 RazorPagesMovieCoNtext**。</span><span class="sxs-lookup"><span data-stu-id="3827e-574">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="3827e-575">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3827e-575">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="3827e-577">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="3827e-577">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="3827e-578">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3827e-578">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="3827e-579">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="3827e-579">Files created</span></span>

* <span data-ttu-id="3827e-580">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="3827e-580">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3827e-581">*Data/ Razor PagesMovieCoNtext .cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-581">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="3827e-582">檔案已更新</span><span class="sxs-lookup"><span data-stu-id="3827e-582">File updated</span></span>

* <span data-ttu-id="3827e-583">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3827e-583">*Startup.cs*</span></span>

<span data-ttu-id="3827e-584">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-584">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3827e-585">初始移轉</span><span class="sxs-lookup"><span data-stu-id="3827e-585">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-586">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3827e-587">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="3827e-587">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="3827e-588">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="3827e-588">Add an initial migration.</span></span>
* <span data-ttu-id="3827e-589">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-589">Update the database with the initial migration.</span></span>

<span data-ttu-id="3827e-590">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="3827e-590">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3827e-592">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-592">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="3827e-593">`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="3827e-593">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3827e-594">架構是以 `DbContext` *Razor PagesMovieCoNtext .cs* 檔案中指定的模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="3827e-594">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="3827e-595">`InitialCreate`引數是用來命名遷移。</span><span class="sxs-lookup"><span data-stu-id="3827e-595">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="3827e-596">您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="3827e-596">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="3827e-597">如需詳細資訊，請參閱<xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="3827e-597">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="3827e-598">此 `Update-Database` 命令會 `Up` 在 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-598">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="3827e-599">`Up` 方法會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="3827e-599">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3827e-600">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-600">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3827e-601">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3827e-601">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="3827e-602">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="3827e-602">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3827e-603">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="3827e-603">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3827e-604">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="3827e-604">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3827e-605">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="3827e-605">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3827e-606">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3827e-606">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3827e-607">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="3827e-607">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3827e-608">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3827e-608">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3827e-609">服務 (例如 EF Core 資料庫內容內容) 會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="3827e-609">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3827e-610">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3827e-610">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3827e-611">本教學課程稍後會顯示取得資料庫內容 coNtextB 內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="3827e-611">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3827e-612">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="3827e-612">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3827e-613">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-613">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3827e-614">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="3827e-614">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="3827e-615">`RazorPagesMovieContext`座標 EF Core 的功能，例如建立、讀取、更新和刪除 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="3827e-615">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="3827e-616">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="3827e-616">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="3827e-617">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="3827e-617">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3827e-618">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="3827e-618">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3827e-619">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3827e-619">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3827e-620">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3827e-620">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3827e-621">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3827e-621">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="3827e-622">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3827e-622">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3827e-623">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3827e-623">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3827e-624">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="3827e-624">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3827e-625">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3827e-625">Test the app</span></span>

* <span data-ttu-id="3827e-626">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="3827e-626">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3827e-627">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="3827e-627">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3827e-628">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="3827e-628">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3827e-629">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="3827e-629">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="3827e-631">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="3827e-631">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3827e-632">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="3827e-632">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3827e-633">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="3827e-633">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3827e-634">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="3827e-634">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="3827e-635">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="3827e-635">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3827e-636">其他資源</span><span class="sxs-lookup"><span data-stu-id="3827e-636">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3827e-637">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3827e-637">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
