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
ms.openlocfilehash: defbc73d0c1d6aac30360cd7b83cc518a407bf98
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413440"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="f368d-104">第2部分：將模型新增至 Razor ASP.NET Core 中的頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="f368d-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="f368d-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f368d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="f368d-106">在本節中，您可以新增類別來管理資料庫中的電影。</span><span class="sxs-lookup"><span data-stu-id="f368d-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="f368d-107">應用程式的模型類別使用 [Entity Framework core (EF core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="f368d-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="f368d-108">EF Core 是一種物件關聯式對應程式， (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="f368d-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="f368d-109">您會先撰寫模型類別，而 EF Core 會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="f368d-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="f368d-110">模型類別稱為 POCO 類別 (自 "**P**>lain-**O** ld **C** LR **O** Bjects" ) ，因為它們沒有 EF Core 的相依性。</span><span class="sxs-lookup"><span data-stu-id="f368d-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="f368d-111">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="f368d-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="f368d-112">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f368d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="f368d-113">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="f368d-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f368d-115">在 [**方案 Explorer**] 中，以滑鼠右鍵按一下 *Razor PagesMovie* 專案，>**加入**  >  **新資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="f368d-116">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="f368d-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="f368d-117">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="f368d-118">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="f368d-119">將類別命名為 *Movie*。</span><span class="sxs-lookup"><span data-stu-id="f368d-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="f368d-120">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-121">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-122">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-123">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-124">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-124">With this attribute:</span></span>

  * <span data-ttu-id="f368d-125">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-126">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f368d-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f368d-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f368d-128">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="f368d-129">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="f368d-130">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-131">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-132">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-133">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-134">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-134">With this attribute:</span></span>

  * <span data-ttu-id="f368d-135">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-136">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="f368d-137">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="f368d-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f368d-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f368d-139">在 [**方案] 工具視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後選取 [**加入** > **新資料夾**...]。命名資料夾 *模型*。</span><span class="sxs-lookup"><span data-stu-id="f368d-139">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="f368d-140">以控制項按一下 [ *模型* ] 資料夾， **然後選取 [** > **新增檔案 ...**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-140">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="f368d-141">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="f368d-141">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="f368d-142">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="f368d-142">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="f368d-143">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="f368d-143">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="f368d-144">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="f368d-144">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="f368d-145">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-145">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-146">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-146">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-147">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-147">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-148">`[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-148">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-149">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-149">With this attribute:</span></span>

  * <span data-ttu-id="f368d-150">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-150">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-151">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-151">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="f368d-152">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="f368d-152">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="f368d-153">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="f368d-153">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f368d-154">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="f368d-154">Scaffold the movie model</span></span>

<span data-ttu-id="f368d-155">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="f368d-155">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f368d-156">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="f368d-156">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-157">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f368d-158">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="f368d-158">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="f368d-159">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-159">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="f368d-160">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="f368d-160">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="f368d-161">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-161">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![前述指示中的圖片。](model/_static/5/sca.png)

1. <span data-ttu-id="f368d-163">在 [**新增 Scaffold** ] 對話方塊中，選取 [ **Razor 使用 Entity Framework (CRUD)** 加入的頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="f368d-163">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffold.png)

1. <span data-ttu-id="f368d-165">**Razor 使用 Entity FRAMEWORK (CRUD) 對話方塊來完成加入頁面**：</span><span class="sxs-lookup"><span data-stu-id="f368d-165">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="f368d-166">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="f368d-166">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="f368d-167">在 [資料內容類別] 資料列中，選取 **+** (加號)。</span><span class="sxs-lookup"><span data-stu-id="f368d-167">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="f368d-168">在 [ **加入資料內容** ] 對話方塊中， `RazorPagesMovie.Data.RazorPagesMovieContext` 會產生類別名稱。</span><span class="sxs-lookup"><span data-stu-id="f368d-168">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="f368d-169">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="f368d-169">Select **Add**.</span></span>

   ![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="f368d-171">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="f368d-171">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f368d-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f368d-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="f368d-173">開啟包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案的命令 shell 至專案目錄。</span><span class="sxs-lookup"><span data-stu-id="f368d-173">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="f368d-174">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-174">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="f368d-175">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-175">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="f368d-176">下表詳細說明 ASP.NET Core 程式碼產生器選項。</span><span class="sxs-lookup"><span data-stu-id="f368d-176">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="f368d-177">選項</span><span class="sxs-lookup"><span data-stu-id="f368d-177">Option</span></span>               | <span data-ttu-id="f368d-178">Description</span><span class="sxs-lookup"><span data-stu-id="f368d-178">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="f368d-179">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="f368d-179">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="f368d-180">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="f368d-180">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="f368d-181">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="f368d-181">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="f368d-182">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="f368d-182">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="f368d-183">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="f368d-183">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="f368d-184">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="f368d-184">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="f368d-185">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="f368d-185">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f368d-186">使用 SQLite 進行開發，SQL Server 用於生產環境</span><span class="sxs-lookup"><span data-stu-id="f368d-186">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f368d-187">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="f368d-187">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f368d-188">下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="f368d-188">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="f368d-189">`IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="f368d-189">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f368d-190">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f368d-191">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="f368d-191">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="f368d-192">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-192">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="f368d-193">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="f368d-193">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="f368d-194">按一下 [ *頁面/電影* ] 資料夾 > **加入** > **新** 的樣板 ...]</span><span class="sxs-lookup"><span data-stu-id="f368d-194">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![前述指示中的圖片。](model/_static/scaMac.png)

1. <span data-ttu-id="f368d-196">在 [**新** 的樣板] 對話方塊中，選取 [ **Razor 使用 Entity Framework (CRUD 的頁面)** > **下一步**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-196">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="f368d-198">**Razor 使用 Entity FRAMEWORK (CRUD) 對話方塊來完成加入頁面**：</span><span class="sxs-lookup"><span data-stu-id="f368d-198">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="f368d-199">在 **要使用的 DbCoNtext 類別中：** row，將類別命名為 `RazorPagesMovie.Data.RazorPagesMovieContext` 。</span><span class="sxs-lookup"><span data-stu-id="f368d-199">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="f368d-200">選取 [完成]。</span><span class="sxs-lookup"><span data-stu-id="f368d-200">Select **Finish**.</span></span>

   ![前述指示中的圖片。](model/_static/5/arpMac.png)

<span data-ttu-id="f368d-202">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="f368d-202">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f368d-203">使用 SQLite 進行開發，SQL Server 用於生產環境</span><span class="sxs-lookup"><span data-stu-id="f368d-203">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f368d-204">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="f368d-204">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f368d-205">下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="f368d-205">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="f368d-206">`IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="f368d-206">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="f368d-207">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="f368d-207">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-208">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-209">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="f368d-209">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="f368d-210">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="f368d-210">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f368d-211">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-211">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="f368d-212">已更新</span><span class="sxs-lookup"><span data-stu-id="f368d-212">Updated</span></span>

* <span data-ttu-id="f368d-213">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-213">*Startup.cs*</span></span>

<span data-ttu-id="f368d-214">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-214">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f368d-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f368d-215">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f368d-216">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="f368d-216">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="f368d-217">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="f368d-217">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="f368d-218">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-218">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f368d-219">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f368d-220">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="f368d-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="f368d-221">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="f368d-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f368d-222">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="f368d-223">已更新</span><span class="sxs-lookup"><span data-stu-id="f368d-223">Updated</span></span>

* <span data-ttu-id="f368d-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-224">*Startup.cs*</span></span>

<span data-ttu-id="f368d-225">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-225">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="f368d-226">使用 EF 的遷移功能建立初始資料庫架構</span><span class="sxs-lookup"><span data-stu-id="f368d-226">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="f368d-227">Entity Framework Core 中的「遷移」功能提供了一種方法，可讓您：</span><span class="sxs-lookup"><span data-stu-id="f368d-227">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="f368d-228">建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="f368d-228">Create the initial database schema.</span></span>
* <span data-ttu-id="f368d-229">以累加方式更新資料庫架構，使其與應用程式的資料模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="f368d-229">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="f368d-230">資料庫中的現有資料會保留下來。</span><span class="sxs-lookup"><span data-stu-id="f368d-230">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-232">在本節中，會使用 **套件管理員主控台** (PMC) 視窗：</span><span class="sxs-lookup"><span data-stu-id="f368d-232">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="f368d-233">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="f368d-233">Add an initial migration.</span></span>
* <span data-ttu-id="f368d-234">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="f368d-234">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="f368d-235">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="f368d-235">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 功能表](model/_static/5/pmc.png)

1. <span data-ttu-id="f368d-237">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-237">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f368d-238">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="f368d-239">執行下列 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-239">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="f368d-240">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="f368d-240">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="f368d-241">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="f368d-241">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="f368d-242">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="f368d-242">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="f368d-243">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="f368d-243">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="f368d-244">`migrations` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="f368d-244">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="f368d-245">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="f368d-245">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="f368d-246">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="f368d-246">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="f368d-247">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="f368d-247">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="f368d-248">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-248">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="f368d-249">在此情況下，會 `update` `Up` 在建立資料庫的 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-249">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-250">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f368d-251">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="f368d-251">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f368d-252">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f368d-252">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f368d-253">服務（例如 EF Core 資料庫內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="f368d-253">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f368d-254">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="f368d-254">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f368d-255">取得資料庫內容執行個體的建構函式程式碼會顯示在本教學課程稍後部分。</span><span class="sxs-lookup"><span data-stu-id="f368d-255">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f368d-256">「樣板」工具會自動建立資料庫內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="f368d-256">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f368d-257">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-257">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f368d-258">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="f368d-258">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="f368d-259">會 `RazorPagesMovieContext` 協調模型的 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="f368d-259">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="f368d-260">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="f368d-260">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="f368d-261">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="f368d-261">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f368d-262">上述程式碼會建立實體集的[DbSet \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601)屬性。</span><span class="sxs-lookup"><span data-stu-id="f368d-262">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="f368d-263">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="f368d-263">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f368d-264">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="f368d-264">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f368d-265">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="f368d-265">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="f368d-266">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="f368d-266">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f368d-267">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-267">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f368d-268">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-268">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="f368d-269">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="f368d-269">Test the app</span></span>

1. <span data-ttu-id="f368d-270">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="f368d-270">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="f368d-271">如果您收到下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="f368d-271">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="f368d-272">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="f368d-272">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="f368d-273">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="f368d-273">Test the **Create** link.</span></span>

   ![Create page](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="f368d-275">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="f368d-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f368d-276">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="f368d-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f368d-277">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="f368d-277">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="f368d-278">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="f368d-278">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f368d-279">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-279">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f368d-280">其他資源</span><span class="sxs-lookup"><span data-stu-id="f368d-280">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f368d-281">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
>  使用[下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="f368d-281">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="f368d-282">在本節中，會新增類別來管理電影。</span><span class="sxs-lookup"><span data-stu-id="f368d-282">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="f368d-283">應用程式的模型類別使用 [Entity Framework core (EF core) ](/ef/core) 來處理資料庫。</span><span class="sxs-lookup"><span data-stu-id="f368d-283">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="f368d-284">EF Core 是一種物件關聯式對應程式， (O/RM) 可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="f368d-284">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="f368d-285">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="f368d-285">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="f368d-286">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="f368d-286">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="f368d-287">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f368d-287">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="f368d-288">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="f368d-288">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-289">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-289">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-290">以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-290">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="f368d-291">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="f368d-291">Name the folder *Models*.</span></span>

<span data-ttu-id="f368d-292">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-292">Right-click the *Models* folder.</span></span> <span data-ttu-id="f368d-293">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-293">Select **Add** > **Class**.</span></span> <span data-ttu-id="f368d-294">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="f368d-294">Name the class **Movie**.</span></span>

<span data-ttu-id="f368d-295">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-295">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-296">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-296">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-297">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-297">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-298">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-298">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-299">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-299">With this attribute:</span></span>

  * <span data-ttu-id="f368d-300">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-300">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-301">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-301">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f368d-302">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="f368d-302">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f368d-303">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f368d-303">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f368d-304">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-304">Add a folder named *Models*.</span></span>
* <span data-ttu-id="f368d-305">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-305">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="f368d-306">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-307">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-308">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-309">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-310">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-310">With this attribute:</span></span>

  * <span data-ttu-id="f368d-311">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-312">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f368d-313">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="f368d-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="f368d-314">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="f368d-314">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="f368d-315">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="f368d-315">Add a database context class</span></span>

* <span data-ttu-id="f368d-316">在 *Razor PagesMovie* 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-316">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="f368d-317">將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="f368d-317">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f368d-318">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="f368d-318">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="f368d-319">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="f368d-319">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="f368d-320">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="f368d-320">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="f368d-321">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="f368d-321">Add a database connection string</span></span>

<span data-ttu-id="f368d-322">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="f368d-322">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f368d-323">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="f368d-323">Register the database context</span></span>

<span data-ttu-id="f368d-324">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="f368d-324">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f368d-325">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="f368d-325">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f368d-326">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f368d-327">在 [**方案] 工具視窗** 中，按一下 [ **Razor PagesMovie** ] 專案，然後選取 [**加入** > **新資料夾**...]。命名資料夾 *模型*。</span><span class="sxs-lookup"><span data-stu-id="f368d-327">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="f368d-328">以滑鼠右鍵按一下 [ *模型* ] 資料夾，然後 **選取 [** > **新增檔案 ...**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-328">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="f368d-329">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="f368d-329">In the **New File** dialog:</span></span>

  * <span data-ttu-id="f368d-330">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="f368d-330">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="f368d-331">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="f368d-331">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="f368d-332">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="f368d-332">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="f368d-333">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-333">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-334">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-334">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-335">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-335">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-336">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-336">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-337">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-337">With this attribute:</span></span>

  * <span data-ttu-id="f368d-338">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-338">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-339">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-339">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="f368d-340">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="f368d-340">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="f368d-341">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="f368d-341">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f368d-342">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="f368d-342">Scaffold the movie model</span></span>

<span data-ttu-id="f368d-343">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="f368d-343">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f368d-344">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="f368d-344">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-345">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-345">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-346">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="f368d-346">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f368d-347">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-347">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f368d-348">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="f368d-348">Name the folder *Movies*.</span></span>

<span data-ttu-id="f368d-349">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-349">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="f368d-351">在 [**新增 Scaffold** ] 對話方塊中，選取 [ **Razor 使用 Entity Framework (CRUD)** 加入的頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="f368d-351">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="f368d-353">**Razor 使用 Entity FRAMEWORK (CRUD) 對話方塊來完成加入頁面**：</span><span class="sxs-lookup"><span data-stu-id="f368d-353">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f368d-354">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="f368d-354">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="f368d-355">在 [ **資料內容類別] 資料** 列中，選取 **+** (加號，) 簽署並變更 PagesMovie 所產生的名稱 Razor 。**模型**。 RazorPagesMovieCoNtext 至 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="f368d-355">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="f368d-356">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="f368d-356">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="f368d-357">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="f368d-357">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="f368d-358">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="f368d-358">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/3/arp.png)

<span data-ttu-id="f368d-360">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="f368d-360">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f368d-361">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f368d-361">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="f368d-362">在專案目錄中開啟命令視窗，其中包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-362">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="f368d-363">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-363">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="f368d-364">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-364">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="f368d-365">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="f368d-365">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="f368d-366">選項</span><span class="sxs-lookup"><span data-stu-id="f368d-366">Option</span></span>               | <span data-ttu-id="f368d-367">Description</span><span class="sxs-lookup"><span data-stu-id="f368d-367">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="f368d-368">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="f368d-368">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="f368d-369">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="f368d-369">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="f368d-370">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="f368d-370">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="f368d-371">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="f368d-371">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="f368d-372">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="f368d-372">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="f368d-373">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="f368d-373">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="f368d-374">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="f368d-374">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f368d-375">使用 SQLite 進行開發，SQL Server 用於生產環境</span><span class="sxs-lookup"><span data-stu-id="f368d-375">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f368d-376">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="f368d-376">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f368d-377">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="f368d-377">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="f368d-378">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="f368d-378">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f368d-379">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-379">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f368d-380">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="f368d-380">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f368d-381">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-381">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f368d-382">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="f368d-382">Name the folder *Movies*.</span></span>

<span data-ttu-id="f368d-383">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新** 的樣板 ...]。</span><span class="sxs-lookup"><span data-stu-id="f368d-383">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="f368d-385">在 [**新** 的樣板] 對話方塊中，選取 [ **Razor 使用 Entity Framework (CRUD 的頁面)** > **下一步**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-385">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="f368d-387">**Razor 使用 Entity FRAMEWORK (CRUD) 對話方塊來完成加入頁面**：</span><span class="sxs-lookup"><span data-stu-id="f368d-387">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f368d-388">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie (Razor PagesMovie) 模型**。</span><span class="sxs-lookup"><span data-stu-id="f368d-388">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="f368d-389">在 [ **資料內容類別** ] 列中，輸入新類別的名稱 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext.</span><span class="sxs-lookup"><span data-stu-id="f368d-389">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="f368d-390">這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="f368d-390">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="f368d-391">它會使用正確的命名空間來建立資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="f368d-391">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="f368d-392">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="f368d-392">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="f368d-394">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="f368d-394">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="f368d-395">新增 EF 工具</span><span class="sxs-lookup"><span data-stu-id="f368d-395">Add EF tools</span></span>

<span data-ttu-id="f368d-396">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-396">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="f368d-397">上述命令會新增適用于 .NET Core CLI 的 Entity Framework Core Tools。</span><span class="sxs-lookup"><span data-stu-id="f368d-397">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="f368d-398">如需詳細資訊，請參閱 [Entity Framework Core 工具參考-.Net CORE CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="f368d-398">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f368d-399">使用 SQLite 進行開發，SQL Server 用於生產環境</span><span class="sxs-lookup"><span data-stu-id="f368d-399">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f368d-400">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="f368d-400">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f368d-401">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="f368d-401">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="f368d-402">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="f368d-402">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="f368d-403">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="f368d-403">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-405">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="f368d-405">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="f368d-406">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="f368d-406">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f368d-407">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-407">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="f368d-408">已更新</span><span class="sxs-lookup"><span data-stu-id="f368d-408">Updated</span></span>

* <span data-ttu-id="f368d-409">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-409">*Startup.cs*</span></span>

<span data-ttu-id="f368d-410">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-410">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f368d-411">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-411">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f368d-412">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="f368d-412">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="f368d-413">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="f368d-413">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f368d-414">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-414">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="f368d-415">已更新</span><span class="sxs-lookup"><span data-stu-id="f368d-415">Updated</span></span>

* <span data-ttu-id="f368d-416">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-416">*Startup.cs*</span></span>

<span data-ttu-id="f368d-417">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-417">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f368d-418">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f368d-418">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f368d-419">Scaffold 處理序會建立下列檔案：</span><span class="sxs-lookup"><span data-stu-id="f368d-419">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="f368d-420">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="f368d-420">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="f368d-421">下一節將說明所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-421">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="f368d-422">初始移轉</span><span class="sxs-lookup"><span data-stu-id="f368d-422">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-424">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="f368d-424">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="f368d-425">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="f368d-425">Add an initial migration.</span></span>
* <span data-ttu-id="f368d-426">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="f368d-426">Update the database with the initial migration.</span></span>

<span data-ttu-id="f368d-427">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="f368d-427">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="f368d-429">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-429">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f368d-430">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-430">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="f368d-431">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-431">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="f368d-432">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="f368d-432">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="f368d-433">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="f368d-433">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="f368d-434">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="f368d-434">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="f368d-435">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="f368d-435">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="f368d-436">遷移命令會產生程式碼來建立初始資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="f368d-436">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="f368d-437">架構是以中指定的模型為基礎 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="f368d-437">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="f368d-438">`InitialCreate` 引數用來命名移轉。</span><span class="sxs-lookup"><span data-stu-id="f368d-438">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="f368d-439">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="f368d-439">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="f368d-440">此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-440">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="f368d-441">在此情況下，會 `update` `Up` 在建立資料庫的  *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-441">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-442">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f368d-443">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="f368d-443">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f368d-444">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f368d-444">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f368d-445">服務（例如 EF Core 資料庫內容內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="f368d-445">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f368d-446">需要這些服務的元件（例如 Razor 頁面）是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="f368d-446">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="f368d-447">本教學課程稍後會顯示取得資料庫內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="f368d-447">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f368d-448">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="f368d-448">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f368d-449">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-449">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f368d-450">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="f368d-450">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="f368d-451">會 `RazorPagesMovieContext` 協調模型的 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="f368d-451">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="f368d-452">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="f368d-452">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="f368d-453">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="f368d-453">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f368d-454">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="f368d-454">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f368d-455">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="f368d-455">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f368d-456">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="f368d-456">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f368d-457">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="f368d-457">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="f368d-458">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="f368d-458">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f368d-459">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-459">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f368d-460">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-460">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="f368d-461">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="f368d-461">Test the app</span></span>

* <span data-ttu-id="f368d-462">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="f368d-462">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="f368d-463">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="f368d-463">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="f368d-464">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="f368d-464">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="f368d-465">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="f368d-465">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="f368d-467">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="f368d-467">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f368d-468">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="f368d-468">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f368d-469">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="f368d-469">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="f368d-470">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="f368d-470">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f368d-471">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-471">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f368d-472">其他資源</span><span class="sxs-lookup"><span data-stu-id="f368d-472">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f368d-473">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
>  使用[下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="f368d-473">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f368d-474">在本節中，會新增類別來管理跨平臺 [SQLite 資料庫](https://www.sqlite.org/index.html)中的電影。</span><span class="sxs-lookup"><span data-stu-id="f368d-474">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="f368d-475">從 ASP.NET Core 範本建立的應用程式會使用 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="f368d-475">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="f368d-476">應用程式的模型類別與 [Entity Framework Core (EF core) ](/ef/core) ([SQLite EF Core 資料庫提供者](/ef/core/providers/sqlite)) 與資料庫搭配使用。</span><span class="sxs-lookup"><span data-stu-id="f368d-476">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="f368d-477">EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化資料存取。</span><span class="sxs-lookup"><span data-stu-id="f368d-477">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="f368d-478">模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="f368d-478">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="f368d-479">它們會定義資料儲存在資料庫中的屬性。</span><span class="sxs-lookup"><span data-stu-id="f368d-479">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="f368d-480">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f368d-480">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="f368d-481">新增資料模型</span><span class="sxs-lookup"><span data-stu-id="f368d-481">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-482">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-482">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-483">以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-483">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="f368d-484">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="f368d-484">Name the folder *Models*.</span></span>

<span data-ttu-id="f368d-485">以滑鼠右鍵按一下 [ *模型* ] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-485">Right-click the *Models* folder.</span></span> <span data-ttu-id="f368d-486">選取 [**新增**  >  **類別**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-486">Select **Add** > **Class**.</span></span> <span data-ttu-id="f368d-487">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="f368d-487">Name the class **Movie**.</span></span>

<span data-ttu-id="f368d-488">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-488">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-489">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-489">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-490">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-490">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-491">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-491">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-492">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-492">With this attribute:</span></span>

  * <span data-ttu-id="f368d-493">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-493">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-494">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-494">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f368d-495">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="f368d-495">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f368d-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f368d-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f368d-497">新增名為 *Models* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-497">Add a folder named *Models*.</span></span>
* <span data-ttu-id="f368d-498">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-498">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="f368d-499">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-500">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-501">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-502">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-503">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-503">With this attribute:</span></span>

  * <span data-ttu-id="f368d-504">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-505">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f368d-506">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="f368d-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="f368d-507">新增 NuGet 套件和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="f368d-507">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="f368d-508">新增資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="f368d-508">Add a database context class</span></span>

<span data-ttu-id="f368d-509">在 Razor PagesMovie 專案中，建立名為 *Data* 的新資料夾。</span><span class="sxs-lookup"><span data-stu-id="f368d-509">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="f368d-510">將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="f368d-510">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f368d-511">上述程式碼會建立實體集的 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="f368d-511">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="f368d-512">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="f368d-512">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="f368d-513">在稍後的步驟中新增相依性之前，程式碼不會進行編譯。</span><span class="sxs-lookup"><span data-stu-id="f368d-513">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="f368d-514">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="f368d-514">Add a database connection string</span></span>

<span data-ttu-id="f368d-515">將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：</span><span class="sxs-lookup"><span data-stu-id="f368d-515">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="f368d-516">新增必要的 NuGet 封裝</span><span class="sxs-lookup"><span data-stu-id="f368d-516">Add required NuGet packages</span></span>

<span data-ttu-id="f368d-517">執行下列 .NET Core CLI 命令，以將 SQLite 和 >nswag.codegeneration.csharp 新增至專案：</span><span class="sxs-lookup"><span data-stu-id="f368d-517">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="f368d-518">需要 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 封裝，才能進行 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="f368d-518">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f368d-519">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="f368d-519">Register the database context</span></span>

<span data-ttu-id="f368d-520">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="f368d-520">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f368d-521">使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="f368d-521">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="f368d-522">建置專案來檢查錯誤。</span><span class="sxs-lookup"><span data-stu-id="f368d-522">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f368d-523">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-523">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f368d-524">在 [**方案工具] 視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後 **選取 [**  >  **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-524">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="f368d-525">將資料夾命名為 *Models*。</span><span class="sxs-lookup"><span data-stu-id="f368d-525">Name the folder *Models*.</span></span>
* <span data-ttu-id="f368d-526">在 [ *模型* ] 資料夾上按一下控制項，然後選取 [ **加入** > **新** 檔案]。</span><span class="sxs-lookup"><span data-stu-id="f368d-526">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="f368d-527">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="f368d-527">In the **New File** dialog:</span></span>

  * <span data-ttu-id="f368d-528">在左窗格中選取 [一般]。</span><span class="sxs-lookup"><span data-stu-id="f368d-528">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="f368d-529">在中央窗格中選取 [類別是空的]。</span><span class="sxs-lookup"><span data-stu-id="f368d-529">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="f368d-530">將類別命名為 **Movie**，然後選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="f368d-530">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="f368d-531">將下列屬性新增至 `Movie` 類別：</span><span class="sxs-lookup"><span data-stu-id="f368d-531">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="f368d-532">`Movie` 類別包含：</span><span class="sxs-lookup"><span data-stu-id="f368d-532">The `Movie` class contains:</span></span>

* <span data-ttu-id="f368d-533">`ID` 欄位是資料庫對於主索引鍵的必要欄位。</span><span class="sxs-lookup"><span data-stu-id="f368d-533">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="f368d-534">`[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="f368d-534">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f368d-535">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="f368d-535">With this attribute:</span></span>

  * <span data-ttu-id="f368d-536">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-536">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="f368d-537">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="f368d-537">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f368d-538">稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="f368d-538">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="f368d-539">建置專案，以確認沒有任何編譯錯誤。</span><span class="sxs-lookup"><span data-stu-id="f368d-539">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f368d-540">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="f368d-540">Scaffold the movie model</span></span>

<span data-ttu-id="f368d-541">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="f368d-541">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f368d-542">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="f368d-542">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-543">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-544">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="f368d-544">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f368d-545">在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-545">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f368d-546">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="f368d-546">Name the folder *Movies*.</span></span>

<span data-ttu-id="f368d-547">在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-547">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/sca.png)

<span data-ttu-id="f368d-549">在 [**新增 Scaffold** ] 對話方塊中，選取 [ **Razor 使用 Entity Framework (CRUD)** 加入的頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="f368d-549">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffold.png)

<span data-ttu-id="f368d-551">**Razor 使用 Entity FRAMEWORK (CRUD) 對話方塊來完成加入頁面**：</span><span class="sxs-lookup"><span data-stu-id="f368d-551">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="f368d-552">在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。</span><span class="sxs-lookup"><span data-stu-id="f368d-552">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="f368d-553">在 [**資料內容類別] 資料** 列中，選取 **+** (加上) 號，然後接受產生的名稱 **Razor PagesMovie。 RazorPagesMovieCoNtext**。</span><span class="sxs-lookup"><span data-stu-id="f368d-553">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="f368d-554">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="f368d-554">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arp.png)

<span data-ttu-id="f368d-556">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="f368d-556">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f368d-557">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f368d-557">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="f368d-558">在專案目錄中開啟命令視窗，其中包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-558">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="f368d-559">若 **為 Windows**：請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-559">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="f368d-560">**針對 macOS 與 Linux**：執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-560">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="f368d-561">下表詳細說明 ASP.NET Core 程式碼產生器選項：</span><span class="sxs-lookup"><span data-stu-id="f368d-561">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="f368d-562">選項</span><span class="sxs-lookup"><span data-stu-id="f368d-562">Option</span></span>               | <span data-ttu-id="f368d-563">Description</span><span class="sxs-lookup"><span data-stu-id="f368d-563">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="f368d-564">模型的名稱。</span><span class="sxs-lookup"><span data-stu-id="f368d-564">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="f368d-565">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="f368d-565">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="f368d-566">使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="f368d-566">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="f368d-567">要建立檢視的相對輸出資料夾路徑。</span><span class="sxs-lookup"><span data-stu-id="f368d-567">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="f368d-568">將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面</span><span class="sxs-lookup"><span data-stu-id="f368d-568">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="f368d-569">使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：</span><span class="sxs-lookup"><span data-stu-id="f368d-569">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="f368d-570">如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="f368d-570">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f368d-571">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-571">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f368d-572">建立 *Pages/Movies* 資料夾：</span><span class="sxs-lookup"><span data-stu-id="f368d-572">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f368d-573">在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-573">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f368d-574">將資料夾命名為 *電影*。</span><span class="sxs-lookup"><span data-stu-id="f368d-574">Name the folder *Movies*.</span></span>

<span data-ttu-id="f368d-575">在 [ *頁面/電影* ] 資料夾上按一下控制項，> **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="f368d-575">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前述指示中的圖片。](model/_static/scaMac.png)

<span data-ttu-id="f368d-577">在 [**加入新** 的樣板] 對話方塊中，選取 [ **Razor 使用 Entity Framework (CRUD)** 加入的頁面] > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="f368d-577">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="f368d-579">**Razor 使用 Entity FRAMEWORK (CRUD) 對話方塊來完成加入頁面**：</span><span class="sxs-lookup"><span data-stu-id="f368d-579">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f368d-580">在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="f368d-580">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="f368d-581">在 [**資料內容類別] 資料** 列中，輸入 select **Razor PagesMovieCoNtext** ，這會使用正確的命名空間來建立新的資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="f368d-581">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="f368d-582">在此情況下，它將會是 **Razor PagesMovie 模型。 RazorPagesMovieCoNtext**。</span><span class="sxs-lookup"><span data-stu-id="f368d-582">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="f368d-583">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="f368d-583">Select **Add**.</span></span>

![前述指示中的圖片。](model/_static/arpMac.png)

<span data-ttu-id="f368d-585">檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。</span><span class="sxs-lookup"><span data-stu-id="f368d-585">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="f368d-586">隨即建立 Scaffold 處理序並更新下列檔案：</span><span class="sxs-lookup"><span data-stu-id="f368d-586">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="f368d-587">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="f368d-587">Files created</span></span>

* <span data-ttu-id="f368d-588">*Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。</span><span class="sxs-lookup"><span data-stu-id="f368d-588">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="f368d-589">*Data/ Razor PagesMovieCoNtext.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-589">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="f368d-590">檔案已更新</span><span class="sxs-lookup"><span data-stu-id="f368d-590">File updated</span></span>

* <span data-ttu-id="f368d-591">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f368d-591">*Startup.cs*</span></span>

<span data-ttu-id="f368d-592">下一節將說明所建立和更新的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-592">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="f368d-593">初始移轉</span><span class="sxs-lookup"><span data-stu-id="f368d-593">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-594">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-594">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f368d-595">在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：</span><span class="sxs-lookup"><span data-stu-id="f368d-595">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="f368d-596">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="f368d-596">Add an initial migration.</span></span>
* <span data-ttu-id="f368d-597">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="f368d-597">Update the database with the initial migration.</span></span>

<span data-ttu-id="f368d-598">從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。</span><span class="sxs-lookup"><span data-stu-id="f368d-598">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="f368d-600">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-600">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="f368d-601">`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="f368d-601">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="f368d-602">架構是以 PagesMovieCoNtext.cs 檔案中指定的模型為基礎 `DbContext` 。 *Razor*</span><span class="sxs-lookup"><span data-stu-id="f368d-602">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="f368d-603">`InitialCreate`引數是用來命名遷移。</span><span class="sxs-lookup"><span data-stu-id="f368d-603">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="f368d-604">您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="f368d-604">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="f368d-605">如需詳細資訊，請參閱<xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="f368d-605">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="f368d-606">此 `Update-Database` 命令會 `Up` 在 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-606">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="f368d-607">`Up` 方法會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="f368d-607">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f368d-608">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-608">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="f368d-609">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="f368d-609">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="f368d-610">上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。</span><span class="sxs-lookup"><span data-stu-id="f368d-610">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="f368d-611">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="f368d-611">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="f368d-612">使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」</span><span class="sxs-lookup"><span data-stu-id="f368d-612">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="f368d-613">略過警告，因為它會在稍後的步驟中解決。</span><span class="sxs-lookup"><span data-stu-id="f368d-613">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f368d-614">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f368d-614">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f368d-615">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="f368d-615">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f368d-616">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f368d-616">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f368d-617">服務 (例如 EF Core 資料庫內容內容) 會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="f368d-617">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f368d-618">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="f368d-618">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f368d-619">本教學課程稍後會顯示取得資料庫內容 coNtextB 內容實例的函式程式碼。</span><span class="sxs-lookup"><span data-stu-id="f368d-619">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f368d-620">「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。</span><span class="sxs-lookup"><span data-stu-id="f368d-620">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f368d-621">檢查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-621">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f368d-622">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="f368d-622">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="f368d-623">會 `RazorPagesMovieContext` 協調模型的 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="f368d-623">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="f368d-624">資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="f368d-624">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="f368d-625">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="f368d-625">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f368d-626">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="f368d-626">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f368d-627">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="f368d-627">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f368d-628">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="f368d-628">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f368d-629">連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="f368d-629">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="f368d-630">針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="f368d-630">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f368d-631">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f368d-631">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f368d-632">檢查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="f368d-632">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="f368d-633">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="f368d-633">Test the app</span></span>

* <span data-ttu-id="f368d-634">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="f368d-634">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="f368d-635">如果您收到錯誤：</span><span class="sxs-lookup"><span data-stu-id="f368d-635">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="f368d-636">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="f368d-636">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="f368d-637">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="f368d-637">Test the **Create** link.</span></span>

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="f368d-639">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="f368d-639">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f368d-640">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="f368d-640">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f368d-641">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="f368d-641">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="f368d-642">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="f368d-642">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f368d-643">下一個教學課程說明 Scaffolding 所建立的檔案。</span><span class="sxs-lookup"><span data-stu-id="f368d-643">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f368d-644">其他資源</span><span class="sxs-lookup"><span data-stu-id="f368d-644">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f368d-645">[上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
>  使用[下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="f368d-645">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
