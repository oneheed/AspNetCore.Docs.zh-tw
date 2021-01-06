---
title: 第4部分：將模型新增至 ASP.NET Core MVC 應用程式
author: rick-anderson
description: ASP.NET Core MVC 之教學課程系列的第4部分。
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: bfda45afeea67a11ad775996d94a06125df08bc6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854583"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="219d0-103">第4部分：將模型新增至 ASP.NET Core MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="219d0-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="219d0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Ryan Nowak](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="219d0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="219d0-105">在本節中，您可以新增類別來管理資料庫中的電影。</span><span class="sxs-lookup"><span data-stu-id="219d0-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="219d0-106">這些類別是 **MVC** 應用程式的「模型」部分。</span><span class="sxs-lookup"><span data-stu-id="219d0-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="219d0-107">搭配 [Entity Framework Core](/ef/core) (EF Core) 使用這些類別，即可使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="219d0-108">EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化您必須撰寫的資料存取程式碼。</span><span class="sxs-lookup"><span data-stu-id="219d0-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="219d0-109">您所建立的模型類別稱為 POCO 類別 (來自「純舊 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。</span><span class="sxs-lookup"><span data-stu-id="219d0-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="219d0-110">它們只會定義資料庫將儲存之資料的屬性。</span><span class="sxs-lookup"><span data-stu-id="219d0-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="219d0-111">在本教學課程中，您首先要撰寫模型類別，而 EF Core 會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="219d0-112">新增資料模型類別</span><span class="sxs-lookup"><span data-stu-id="219d0-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-114">以滑鼠右鍵按一下 *Models* 資料夾 > [新增] > [類別]。</span><span class="sxs-lookup"><span data-stu-id="219d0-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="219d0-115">將檔案命名為 *Movie.cs*。</span><span class="sxs-lookup"><span data-stu-id="219d0-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="219d0-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="219d0-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="219d0-117">將名為 *Movie.cs* 的檔案新增到 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="219d0-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="219d0-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="219d0-119">以滑鼠右鍵按一下 [*模型*] 資料夾 **，>**  >  **新增類別**  >  **空白類別**]。</span><span class="sxs-lookup"><span data-stu-id="219d0-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="219d0-120">將檔案命名為 *Movie.cs*。</span><span class="sxs-lookup"><span data-stu-id="219d0-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="219d0-121">使用下列程式碼更新 *Movie.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="219d0-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="219d0-122">`Movie` 類別包含 `Id` 欄位，該欄位是資料庫的必要欄位，將作為主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="219d0-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="219d0-123"><xref:System.ComponentModel.DataAnnotations.DataType>上的屬性會 `ReleaseDate` 指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="219d0-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="219d0-124">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="219d0-124">With this attribute:</span></span>

* <span data-ttu-id="219d0-125">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="219d0-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="219d0-126">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="219d0-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="219d0-127">稍後的教學課程會涵蓋 [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)。</span><span class="sxs-lookup"><span data-stu-id="219d0-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="219d0-128">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="219d0-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-130">從 [工具] 功能表中，選取 [NuGet 套件管理員]**[套件管理器主控台]** >  (PMC)。</span><span class="sxs-lookup"><span data-stu-id="219d0-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 功能表](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="219d0-132">在 PMC 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="219d0-133">上述命令會新增 EF Core SQL Server 提供者。</span><span class="sxs-lookup"><span data-stu-id="219d0-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="219d0-134">提供者套件會將 EF Core 套件作為相依性安裝。</span><span class="sxs-lookup"><span data-stu-id="219d0-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="219d0-135">其他套件會在本教學課程中稍後的 scaffolding 步驟內自動安裝。</span><span class="sxs-lookup"><span data-stu-id="219d0-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="219d0-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="219d0-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="219d0-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="219d0-138">從 [ **專案** ] 功能表選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="219d0-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="219d0-139">在右上方的 **搜尋** 欄位中，輸入， `Microsoft.EntityFrameworkCore.SQLite` 然後按下 **Return** 鍵進行搜尋。</span><span class="sxs-lookup"><span data-stu-id="219d0-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="219d0-140">選取相符的 NuGet 套件，然後按 [ **新增套件** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="219d0-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![新增 Entity Framework Core NuGet 套件](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="219d0-142">將會顯示 [ **選取專案** ] 對話方塊，並 `MvcMovie` 選取專案。</span><span class="sxs-lookup"><span data-stu-id="219d0-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="219d0-143">按下 [ **確定]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="219d0-143">Press the **Ok** button.</span></span>

<span data-ttu-id="219d0-144">[ **接受授權** ] 對話方塊隨即顯示。</span><span class="sxs-lookup"><span data-stu-id="219d0-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="219d0-145">視需要檢查授權，然後按一下 [ **接受** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="219d0-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="219d0-146">重複上述步驟，以安裝下列 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="219d0-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="219d0-147">執行下列 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="219d0-148">上述命令會新增 [aspnet codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)的元件工具。</span><span class="sxs-lookup"><span data-stu-id="219d0-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="219d0-149">建立資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="219d0-149">Create a database context class</span></span>

<span data-ttu-id="219d0-150">資料庫內容類別是協調 `Movie` 模型 EF Core 功能 (建立、讀取、更新、刪除) 的必要項目。</span><span class="sxs-lookup"><span data-stu-id="219d0-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="219d0-151">資料庫內容衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)，並指定要包含在資料模型中的實體。</span><span class="sxs-lookup"><span data-stu-id="219d0-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="219d0-152">建立 *Data* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="219d0-152">Create a *Data* folder.</span></span>

<span data-ttu-id="219d0-153">使用下列程式碼新增 *Data/MvcMovieContext.cs* 內容：</span><span class="sxs-lookup"><span data-stu-id="219d0-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="219d0-154">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="219d0-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="219d0-155">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="219d0-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="219d0-156">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="219d0-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="219d0-157">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="219d0-157">Register the database context</span></span>

<span data-ttu-id="219d0-158">ASP.NET Core 內建[相依性插入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="219d0-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="219d0-159">服務 (例如 EF Core 資料庫內容) 必須在應用程式啟動期間向 DI 進行註冊。</span><span class="sxs-lookup"><span data-stu-id="219d0-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="219d0-160">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="219d0-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="219d0-161">取得資料庫內容執行個體的建構函式程式碼，本教學課程中稍後會示範。</span><span class="sxs-lookup"><span data-stu-id="219d0-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="219d0-162">在本節中，您會向 DI 容器註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="219d0-163">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="219d0-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="219d0-164">在 `Startup.ConfigureServices` 中新增下列醒目提示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="219d0-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="219d0-167">連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="219d0-168">針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="219d0-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="219d0-169">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="219d0-169">Add a database connection string</span></span>

<span data-ttu-id="219d0-170">將連接字串新增至檔案 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="219d0-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-172">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="219d0-173">建置專案以檢查編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="219d0-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="219d0-174">Scaffold 影片頁面</span><span class="sxs-lookup"><span data-stu-id="219d0-174">Scaffold movie pages</span></span>

<span data-ttu-id="219d0-175">使用 scaffolding 工具來為影片模型產生建立、讀取、更新和刪除 (CRUD) 頁面。</span><span class="sxs-lookup"><span data-stu-id="219d0-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-177">在 [方案總管] 中以滑鼠右鍵按一下 *Controllers* 資料夾 > [新增] > [新增 Scaffold 項目]。</span><span class="sxs-lookup"><span data-stu-id="219d0-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上方步驟的檢視](adding-model/_static/add_controller21.png)

<span data-ttu-id="219d0-179">在 [新增 Scaffold] 對話方塊中，選取 [使用 Entity Framework 執行檢視的 MVC 控制器] > [新增]。</span><span class="sxs-lookup"><span data-stu-id="219d0-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[新增 Scaffold] 對話方塊](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="219d0-181">完成 [新增控制器] 對話方塊：</span><span class="sxs-lookup"><span data-stu-id="219d0-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="219d0-182">**模型類別:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="219d0-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="219d0-183">**資料內容類別：** *mvcmoviecoNtext.cs (MvcMovie 的資料)*</span><span class="sxs-lookup"><span data-stu-id="219d0-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![新增資料內容](adding-model/_static/dc5.png)

* <span data-ttu-id="219d0-185">**檢視：** 保持核取預設的每一個選項</span><span class="sxs-lookup"><span data-stu-id="219d0-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="219d0-186">**控制器名稱：** 保留預設值 *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="219d0-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="219d0-187">選取 [新增]</span><span class="sxs-lookup"><span data-stu-id="219d0-187">Select **Add**</span></span>

<span data-ttu-id="219d0-188">Visual Studio 會建立：</span><span class="sxs-lookup"><span data-stu-id="219d0-188">Visual Studio creates:</span></span>

* <span data-ttu-id="219d0-189">電影控制器 (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="219d0-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="219d0-190">Razor 查看建立、刪除、詳細資料、編輯和索引頁面的檔案 (*Views/電影/ \* cshtml*) </span><span class="sxs-lookup"><span data-stu-id="219d0-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="219d0-191">自動建立這些檔案的流程稱為 *scaffolding*。</span><span class="sxs-lookup"><span data-stu-id="219d0-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="219d0-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="219d0-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="219d0-193">在專案目錄 (包含 *Program.cs*、*Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。</span><span class="sxs-lookup"><span data-stu-id="219d0-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="219d0-194">在 Linux 上，匯出 scaffold 工具路徑：</span><span class="sxs-lookup"><span data-stu-id="219d0-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="219d0-195">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="219d0-196">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="219d0-197">在專案目錄 (包含 *Program.cs*、*Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。</span><span class="sxs-lookup"><span data-stu-id="219d0-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="219d0-198">匯出 scaffold 工具路徑：</span><span class="sxs-lookup"><span data-stu-id="219d0-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="219d0-199">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="219d0-200">您目前尚無法使用 scaffold 頁面，因為資料庫不存在。</span><span class="sxs-lookup"><span data-stu-id="219d0-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="219d0-201">如果您執行應用程式，並按一下 [ **電影應用程式** ] 連結，則會出現 [ *無法開啟資料庫* ] 或 [ *沒有這類資料表：電影* 錯誤訊息]。</span><span class="sxs-lookup"><span data-stu-id="219d0-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="219d0-202">初始移轉</span><span class="sxs-lookup"><span data-stu-id="219d0-202">Initial migration</span></span>

<span data-ttu-id="219d0-203">使用 EF Core 的 [移轉](xref:data/ef-mvc/migrations) 功能來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="219d0-204">移轉是一組工具，可讓您建立和更新資料庫，使其與您的資料模型相符。</span><span class="sxs-lookup"><span data-stu-id="219d0-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-206">從 [工具] 功能表中，選取 [NuGet 套件管理員]**[套件管理器主控台]** >  (PMC)。</span><span class="sxs-lookup"><span data-stu-id="219d0-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="219d0-207">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="219d0-208">`Add-Migration InitialCreate`：產生 *遷移/{timestamp} _InitialCreate .cs* 遷移檔。</span><span class="sxs-lookup"><span data-stu-id="219d0-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="219d0-209">`InitialCreate` 引數是移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="219d0-210">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="219d0-211">因為這是第一次移轉，所產生類別會包含建立資料庫結構描述的程式碼。</span><span class="sxs-lookup"><span data-stu-id="219d0-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="219d0-212">資料庫結構描述是以 `MvcMovieContext` 類別為基礎。</span><span class="sxs-lookup"><span data-stu-id="219d0-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="219d0-213">`Update-Database`：將資料庫更新為先前命令所建立的最新遷移。</span><span class="sxs-lookup"><span data-stu-id="219d0-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="219d0-214">此命令會執行 *Migrations/{time-stamp}_InitialCreate.cs* 檔案中的 `Up` 方法，其會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="219d0-215">資料庫更新命令會產生下列警告：</span><span class="sxs-lookup"><span data-stu-id="219d0-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="219d0-216">沒有為實體型別 'Movie' 上的十進位資料行 'Price' 指定型別。</span><span class="sxs-lookup"><span data-stu-id="219d0-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="219d0-217">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="219d0-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="219d0-218">使用 'HasColumnType()' 明確指定可容納所有值的 SQL 伺服器資料行型別。</span><span class="sxs-lookup"><span data-stu-id="219d0-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="219d0-219">您可以忽略該警告，稍後的教學課程中將修正此問題。</span><span class="sxs-lookup"><span data-stu-id="219d0-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="219d0-221">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="219d0-222">`ef migrations add InitialCreate`：產生 *遷移/{timestamp} _InitialCreate .cs* 遷移檔。</span><span class="sxs-lookup"><span data-stu-id="219d0-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="219d0-223">`InitialCreate` 引數是移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="219d0-224">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="219d0-225">因為這是第一次移轉，所產生類別會包含建立資料庫結構描述的程式碼。</span><span class="sxs-lookup"><span data-stu-id="219d0-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="219d0-226">資料庫結構描述會以 `MvcMovieContext` 類別 (在 *Data/MvcMovieContext.cs* 檔案中) 中指定的模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="219d0-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="219d0-227">`ef database update`：將資料庫更新為先前命令所建立的最新遷移。</span><span class="sxs-lookup"><span data-stu-id="219d0-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="219d0-228">此命令會執行 *Migrations/{time-stamp}_InitialCreate.cs* 檔案中的 `Up` 方法，其會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="219d0-229">InitialCreate 類別</span><span class="sxs-lookup"><span data-stu-id="219d0-229">The InitialCreate class</span></span>

<span data-ttu-id="219d0-230">檢查 *Migrations/{timestamp}_InitialCreate.cs* 移轉檔案：</span><span class="sxs-lookup"><span data-stu-id="219d0-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="219d0-231">`Up` 方法會建立 Movie 資料表，並將 `Id` 設為主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="219d0-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="219d0-232">`Down` 方法會還原 `Up` 移轉所做的結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="219d0-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="219d0-233">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="219d0-233">Test the app</span></span>

* <span data-ttu-id="219d0-234">執行應用程式，然後按一下 [影片應用程式] 連結。</span><span class="sxs-lookup"><span data-stu-id="219d0-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="219d0-235">若您收到與下列內容相似的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="219d0-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-237">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="219d0-238">您可能遺漏了[移轉步驟](#migration)。</span><span class="sxs-lookup"><span data-stu-id="219d0-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="219d0-239">測試 [ **建立** ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="219d0-239">Test the **Create** page.</span></span> <span data-ttu-id="219d0-240">輸入並提交資料。</span><span class="sxs-lookup"><span data-stu-id="219d0-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="219d0-241">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="219d0-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="219d0-242">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="219d0-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="219d0-243">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="219d0-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="219d0-244">測試 **Edit**、**Details** 和 **Delete** 頁面。</span><span class="sxs-lookup"><span data-stu-id="219d0-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="219d0-245">控制器中的相依性插入</span><span class="sxs-lookup"><span data-stu-id="219d0-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-247">開啟 *Controllers/MoviesController.cs* 檔案，並檢查建構函式：</span><span class="sxs-lookup"><span data-stu-id="219d0-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="219d0-248">建構函式會使用[相依性插入](xref:fundamentals/dependency-injection)將資料庫內容 (`MvcMovieContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="219d0-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="219d0-249">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-250">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="219d0-251">建構函式會使用[相依性插入](xref:fundamentals/dependency-injection)將資料庫內容 (`MvcMovieContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="219d0-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="219d0-252">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="219d0-253">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="219d0-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="219d0-254">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="219d0-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="219d0-255">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="219d0-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="219d0-256">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="219d0-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="219d0-257">強型別模型和 @model 關鍵字</span><span class="sxs-lookup"><span data-stu-id="219d0-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="219d0-258">稍早在本教學課程中，您已看到控制器如何使用 `ViewData` 字典傳遞資料或物件給檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="219d0-259">`ViewData` 字典是一種動態物件，提供便利的晚期繫結方式，將資訊傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="219d0-260">MVC 也提供將強型別模型物件傳遞至檢視的能力。</span><span class="sxs-lookup"><span data-stu-id="219d0-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="219d0-261">這種強型別的方法可在編譯時檢查程式碼。</span><span class="sxs-lookup"><span data-stu-id="219d0-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="219d0-262">此方法所使用的 scaffolding 機制 (即傳遞強型別模型)，包括 `MoviesController` 類別和檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="219d0-263">檢查 *Controllers/MoviesController.cs* 檔案中產生的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="219d0-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="219d0-264">`id` 參數通常會傳遞為路由資料。</span><span class="sxs-lookup"><span data-stu-id="219d0-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="219d0-265">例如，`https://localhost:5001/movies/details/1` 設定：</span><span class="sxs-lookup"><span data-stu-id="219d0-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="219d0-266">控制器為 `movies` 控制器 (第一個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="219d0-267">動作為 `details` (第二個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="219d0-268">識別碼為 1 (最後一個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="219d0-269">您也可以在 `id` 中使用查詢字串傳遞，如下所示：</span><span class="sxs-lookup"><span data-stu-id="219d0-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="219d0-270">`id`如果未提供識別碼值，參數會定義為[可為 null 的型](/dotnet/csharp/programming-guide/nullable-types/index)別 (`int?`) 。</span><span class="sxs-lookup"><span data-stu-id="219d0-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="219d0-271">[Lambda 運算式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)會傳遞至 `FirstOrDefaultAsync`，以選取符合路由資料或查詢字串值的電影實體。</span><span class="sxs-lookup"><span data-stu-id="219d0-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="219d0-272">如果找到電影，則 `Movie` 模型的執行個體會傳遞至 `Details` 檢視：</span><span class="sxs-lookup"><span data-stu-id="219d0-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="219d0-273">檢查 *Views/Movies/Details.cshtml* 檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="219d0-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="219d0-274">檢視檔案頂端的 `@model` 陳述式會指定檢視所預期物件型別。</span><span class="sxs-lookup"><span data-stu-id="219d0-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="219d0-275">建立影片控制器時，會包含下列 `@model` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="219d0-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="219d0-276">此 `@model` 指示詞會允許存取控制器傳遞給檢視的影片。</span><span class="sxs-lookup"><span data-stu-id="219d0-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="219d0-277">`Model` 物件為強型別物件。</span><span class="sxs-lookup"><span data-stu-id="219d0-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="219d0-278">例如，在 *Details.cshtml* 檢視中，程式碼會使用強型別的 `Model` 物件，將每個電影欄位傳遞至 `DisplayNameFor` 和 `DisplayFor` HTML 協助程式。</span><span class="sxs-lookup"><span data-stu-id="219d0-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="219d0-279">`Create` 與 `Edit` 方法和檢視也會傳遞 `Movie` 模型物件。</span><span class="sxs-lookup"><span data-stu-id="219d0-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="219d0-280">檢查電影控制器中的 *Index.cshtml* 檢視和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="219d0-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="219d0-281">請注意程式碼如何在呼叫 `View` 方法時建立 `List` 物件。</span><span class="sxs-lookup"><span data-stu-id="219d0-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="219d0-282">此程式碼會從 `Index` 動作方法將 `Movies` 清單傳遞至檢視：</span><span class="sxs-lookup"><span data-stu-id="219d0-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="219d0-283">建立影片控制器時，scaffolding 會在 *Index.cshtml* 檔案的頂端包含下列 `@model` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="219d0-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="219d0-284">`@model` 指示詞可讓您使用強型別的 `Model` 物件，存取控制器傳遞至檢視的電影清單。</span><span class="sxs-lookup"><span data-stu-id="219d0-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="219d0-285">例如，在 *Index.cshtml* 檢視中，程式碼會透過強型別 `Model` 物件的 `foreach` 陳述式循環存取電影：</span><span class="sxs-lookup"><span data-stu-id="219d0-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="219d0-286">因為 `Model` 物件是強型別 (作為 `IEnumerable<Movie>` 物件)，所以迴圈中每個項目的類型為 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="219d0-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="219d0-287">撇開其他優點，這表示您會進行程式碼編譯時期檢查。</span><span class="sxs-lookup"><span data-stu-id="219d0-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="219d0-288">其他資源</span><span class="sxs-lookup"><span data-stu-id="219d0-288">Additional resources</span></span>

* [<span data-ttu-id="219d0-289">標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="219d0-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="219d0-290">全球化和當地語系化</span><span class="sxs-lookup"><span data-stu-id="219d0-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="219d0-291">[先前新增視圖](adding-view.md) 
> [下一步使用 SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="219d0-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="219d0-292">新增資料模型類別</span><span class="sxs-lookup"><span data-stu-id="219d0-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-294">以滑鼠右鍵按一下 *Models* 資料夾 > [新增] > [類別]。</span><span class="sxs-lookup"><span data-stu-id="219d0-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="219d0-295">將檔案命名為 *Movie.cs*。</span><span class="sxs-lookup"><span data-stu-id="219d0-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="219d0-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="219d0-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="219d0-297">將名為 *Movie.cs* 的檔案新增到 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="219d0-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="219d0-298">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="219d0-299">以滑鼠右鍵按一下 [*模型*] 資料夾 **，>**  >  **新增類別**  >  **空白類別**]。</span><span class="sxs-lookup"><span data-stu-id="219d0-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="219d0-300">將檔案命名為 *Movie.cs*。</span><span class="sxs-lookup"><span data-stu-id="219d0-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="219d0-301">使用下列程式碼更新 *Movie.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="219d0-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="219d0-302">`Movie` 類別包含 `Id` 欄位，該欄位是資料庫的必要欄位，將作為主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="219d0-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="219d0-303"><xref:System.ComponentModel.DataAnnotations.DataType>上的屬性會 `ReleaseDate` 指定資料 (的類型 `Date`) 。</span><span class="sxs-lookup"><span data-stu-id="219d0-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="219d0-304">使用此屬性：</span><span class="sxs-lookup"><span data-stu-id="219d0-304">With this attribute:</span></span>

* <span data-ttu-id="219d0-305">使用者不需要在日期欄位中輸入時間資訊。</span><span class="sxs-lookup"><span data-stu-id="219d0-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="219d0-306">只會顯示日期，不會顯示時間資訊。</span><span class="sxs-lookup"><span data-stu-id="219d0-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="219d0-307">稍後的教學課程會涵蓋 [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations)。</span><span class="sxs-lookup"><span data-stu-id="219d0-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="219d0-308">新增 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="219d0-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-310">從 [工具] 功能表中，選取 [NuGet 套件管理員]**[套件管理器主控台]** >  (PMC)。</span><span class="sxs-lookup"><span data-stu-id="219d0-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 功能表](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="219d0-312">在 PMC 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="219d0-313">上述命令會新增 EF Core SQL Server 提供者。</span><span class="sxs-lookup"><span data-stu-id="219d0-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="219d0-314">提供者套件會將 EF Core 套件作為相依性安裝。</span><span class="sxs-lookup"><span data-stu-id="219d0-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="219d0-315">其他套件會在本教學課程中稍後的 scaffolding 步驟內自動安裝。</span><span class="sxs-lookup"><span data-stu-id="219d0-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="219d0-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="219d0-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="219d0-317">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="219d0-318">從 [ **專案** ] 功能表選取 [ **管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="219d0-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="219d0-319">在右上方的 **搜尋** 欄位中，輸入， `Microsoft.EntityFrameworkCore.SQLite` 然後按下 **Return** 鍵進行搜尋。</span><span class="sxs-lookup"><span data-stu-id="219d0-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="219d0-320">選取相符的 NuGet 套件，然後按 [ **新增套件** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="219d0-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![新增 Entity Framework Core NuGet 套件](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="219d0-322">將會顯示 [ **選取專案** ] 對話方塊，並 `MvcMovie` 選取專案。</span><span class="sxs-lookup"><span data-stu-id="219d0-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="219d0-323">按下 [ **確定]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="219d0-323">Press the **Ok** button.</span></span>

<span data-ttu-id="219d0-324">[ **接受授權** ] 對話方塊隨即顯示。</span><span class="sxs-lookup"><span data-stu-id="219d0-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="219d0-325">視需要檢查授權，然後按一下 [ **接受** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="219d0-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="219d0-326">重複上述步驟，以安裝下列 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="219d0-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="219d0-327">建立資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="219d0-327">Create a database context class</span></span>

<span data-ttu-id="219d0-328">資料庫內容類別是協調 `Movie` 模型 EF Core 功能 (建立、讀取、更新、刪除) 的必要項目。</span><span class="sxs-lookup"><span data-stu-id="219d0-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="219d0-329">資料庫內容衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)，並指定要包含在資料模型中的實體。</span><span class="sxs-lookup"><span data-stu-id="219d0-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="219d0-330">建立 *Data* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="219d0-330">Create a *Data* folder.</span></span>

<span data-ttu-id="219d0-331">使用下列程式碼新增 *Data/MvcMovieContext.cs* 內容：</span><span class="sxs-lookup"><span data-stu-id="219d0-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="219d0-332">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="219d0-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="219d0-333">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="219d0-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="219d0-334">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="219d0-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="219d0-335">登錄資料庫內容</span><span class="sxs-lookup"><span data-stu-id="219d0-335">Register the database context</span></span>

<span data-ttu-id="219d0-336">ASP.NET Core 內建[相依性插入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="219d0-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="219d0-337">服務 (例如 EF Core 資料庫內容) 必須在應用程式啟動期間向 DI 進行註冊。</span><span class="sxs-lookup"><span data-stu-id="219d0-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="219d0-338">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="219d0-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="219d0-339">取得資料庫內容執行個體的建構函式程式碼，本教學課程中稍後會示範。</span><span class="sxs-lookup"><span data-stu-id="219d0-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="219d0-340">在本節中，您會向 DI 容器註冊資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="219d0-341">在 *Startup.cs* 最上方新增下列 `using` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="219d0-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="219d0-342">在 `Startup.ConfigureServices` 中新增下列醒目提示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="219d0-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-344">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="219d0-345">連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="219d0-346">針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="219d0-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="219d0-347">新增資料庫連線字串</span><span class="sxs-lookup"><span data-stu-id="219d0-347">Add a database connection string</span></span>

<span data-ttu-id="219d0-348">將連接字串新增至檔案 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="219d0-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-350">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="219d0-351">建置專案以檢查編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="219d0-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="219d0-352">Scaffold 影片頁面</span><span class="sxs-lookup"><span data-stu-id="219d0-352">Scaffold movie pages</span></span>

<span data-ttu-id="219d0-353">使用 scaffolding 工具來為影片模型產生建立、讀取、更新和刪除 (CRUD) 頁面。</span><span class="sxs-lookup"><span data-stu-id="219d0-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-355">在 [方案總管] 中以滑鼠右鍵按一下 *Controllers* 資料夾 > [新增] > [新增 Scaffold 項目]。</span><span class="sxs-lookup"><span data-stu-id="219d0-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上方步驟的檢視](adding-model/_static/add_controller21.png)

<span data-ttu-id="219d0-357">在 [新增 Scaffold] 對話方塊中，選取 [使用 Entity Framework 執行檢視的 MVC 控制器] > [新增]。</span><span class="sxs-lookup"><span data-stu-id="219d0-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[新增 Scaffold] 對話方塊](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="219d0-359">完成 [新增控制器] 對話方塊：</span><span class="sxs-lookup"><span data-stu-id="219d0-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="219d0-360">**模型類別:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="219d0-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="219d0-361">**資料內容類別：** *mvcmoviecoNtext.cs (MvcMovie 的資料)*</span><span class="sxs-lookup"><span data-stu-id="219d0-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![新增資料內容](adding-model/_static/dc3.png)

* <span data-ttu-id="219d0-363">**檢視：** 保持核取預設的每一個選項</span><span class="sxs-lookup"><span data-stu-id="219d0-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="219d0-364">**控制器名稱：** 保留預設值 *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="219d0-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="219d0-365">選取 [新增]</span><span class="sxs-lookup"><span data-stu-id="219d0-365">Select **Add**</span></span>

<span data-ttu-id="219d0-366">Visual Studio 會建立：</span><span class="sxs-lookup"><span data-stu-id="219d0-366">Visual Studio creates:</span></span>

* <span data-ttu-id="219d0-367">電影控制器 (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="219d0-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="219d0-368">Razor 查看建立、刪除、詳細資料、編輯和索引頁面的檔案 (*Views/電影/ \* cshtml*) </span><span class="sxs-lookup"><span data-stu-id="219d0-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="219d0-369">自動建立這些檔案的流程稱為 *scaffolding*。</span><span class="sxs-lookup"><span data-stu-id="219d0-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="219d0-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="219d0-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="219d0-371">在專案目錄 (包含 *Program.cs*、*Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。</span><span class="sxs-lookup"><span data-stu-id="219d0-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="219d0-372">在 Linux 上，匯出 scaffold 工具路徑：</span><span class="sxs-lookup"><span data-stu-id="219d0-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="219d0-373">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="219d0-374">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="219d0-375">在專案目錄 (包含 *Program.cs*、*Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。</span><span class="sxs-lookup"><span data-stu-id="219d0-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="219d0-376">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="219d0-377">您目前尚無法使用 scaffold 頁面，因為資料庫不存在。</span><span class="sxs-lookup"><span data-stu-id="219d0-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="219d0-378">如果您執行應用程式，並按一下 [ **電影應用程式** ] 連結，則會出現 [ *無法開啟資料庫* ] 或 [ *沒有這類資料表：電影* 錯誤訊息]。</span><span class="sxs-lookup"><span data-stu-id="219d0-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="219d0-379">初始移轉</span><span class="sxs-lookup"><span data-stu-id="219d0-379">Initial migration</span></span>

<span data-ttu-id="219d0-380">使用 EF Core 的 [移轉](xref:data/ef-mvc/migrations) 功能來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="219d0-381">移轉是一組工具，可讓您建立和更新資料庫，使其與您的資料模型相符。</span><span class="sxs-lookup"><span data-stu-id="219d0-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-383">從 [工具] 功能表中，選取 [NuGet 套件管理員]**[套件管理器主控台]** >  (PMC)。</span><span class="sxs-lookup"><span data-stu-id="219d0-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="219d0-384">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="219d0-385">`Add-Migration InitialCreate`：產生 *遷移/{timestamp} _InitialCreate .cs* 遷移檔。</span><span class="sxs-lookup"><span data-stu-id="219d0-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="219d0-386">`InitialCreate` 引數是移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="219d0-387">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="219d0-388">因為這是第一次移轉，所產生類別會包含建立資料庫結構描述的程式碼。</span><span class="sxs-lookup"><span data-stu-id="219d0-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="219d0-389">資料庫結構描述是以 `MvcMovieContext` 類別為基礎。</span><span class="sxs-lookup"><span data-stu-id="219d0-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="219d0-390">`Update-Database`：將資料庫更新為先前命令所建立的最新遷移。</span><span class="sxs-lookup"><span data-stu-id="219d0-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="219d0-391">此命令會執行 *Migrations/{time-stamp}_InitialCreate.cs* 檔案中的 `Up` 方法，其會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="219d0-392">資料庫更新命令會產生下列警告：</span><span class="sxs-lookup"><span data-stu-id="219d0-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="219d0-393">沒有為實體型別 'Movie' 上的十進位資料行 'Price' 指定型別。</span><span class="sxs-lookup"><span data-stu-id="219d0-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="219d0-394">如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。</span><span class="sxs-lookup"><span data-stu-id="219d0-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="219d0-395">使用 'HasColumnType()' 明確指定可容納所有值的 SQL 伺服器資料行型別。</span><span class="sxs-lookup"><span data-stu-id="219d0-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="219d0-396">您可以忽略該警告，稍後的教學課程中將修正此問題。</span><span class="sxs-lookup"><span data-stu-id="219d0-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-397">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="219d0-398">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="219d0-399">`ef migrations add InitialCreate`：產生 *遷移/{timestamp} _InitialCreate .cs* 遷移檔。</span><span class="sxs-lookup"><span data-stu-id="219d0-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="219d0-400">`InitialCreate` 引數是移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="219d0-401">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="219d0-402">因為這是第一次移轉，所產生類別會包含建立資料庫結構描述的程式碼。</span><span class="sxs-lookup"><span data-stu-id="219d0-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="219d0-403">資料庫結構描述會以 `MvcMovieContext` 類別 (在 *Data/MvcMovieContext.cs* 檔案中) 中指定的模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="219d0-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="219d0-404">`ef database update`：將資料庫更新為先前命令所建立的最新遷移。</span><span class="sxs-lookup"><span data-stu-id="219d0-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="219d0-405">此命令會執行 *Migrations/{time-stamp}_InitialCreate.cs* 檔案中的 `Up` 方法，其會建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="219d0-406">InitialCreate 類別</span><span class="sxs-lookup"><span data-stu-id="219d0-406">The InitialCreate class</span></span>

<span data-ttu-id="219d0-407">檢查 *Migrations/{timestamp}_InitialCreate.cs* 移轉檔案：</span><span class="sxs-lookup"><span data-stu-id="219d0-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="219d0-408">`Up` 方法會建立 Movie 資料表，並將 `Id` 設為主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="219d0-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="219d0-409">`Down` 方法會還原 `Up` 移轉所做的結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="219d0-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="219d0-410">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="219d0-410">Test the app</span></span>

* <span data-ttu-id="219d0-411">執行應用程式，然後按一下 [影片應用程式] 連結。</span><span class="sxs-lookup"><span data-stu-id="219d0-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="219d0-412">若您收到與下列內容相似的例外狀況：</span><span class="sxs-lookup"><span data-stu-id="219d0-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-414">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="219d0-415">您可能遺漏了[移轉步驟](#migration)。</span><span class="sxs-lookup"><span data-stu-id="219d0-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="219d0-416">測試 [ **建立** ] 頁面。</span><span class="sxs-lookup"><span data-stu-id="219d0-416">Test the **Create** page.</span></span> <span data-ttu-id="219d0-417">輸入並提交資料。</span><span class="sxs-lookup"><span data-stu-id="219d0-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="219d0-418">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="219d0-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="219d0-419">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="219d0-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="219d0-420">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="219d0-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="219d0-421">測試 **Edit**、**Details** 和 **Delete** 頁面。</span><span class="sxs-lookup"><span data-stu-id="219d0-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="219d0-422">控制器中的相依性插入</span><span class="sxs-lookup"><span data-stu-id="219d0-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-424">開啟 *Controllers/MoviesController.cs* 檔案，並檢查建構函式：</span><span class="sxs-lookup"><span data-stu-id="219d0-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="219d0-425">建構函式會使用[相依性插入](xref:fundamentals/dependency-injection)將資料庫內容 (`MvcMovieContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="219d0-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="219d0-426">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-427">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="219d0-428">建構函式會使用[相依性插入](xref:fundamentals/dependency-injection)將資料庫內容 (`MvcMovieContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="219d0-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="219d0-429">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="219d0-430">使用 SQLite 進行開發，SQL Server 生產環境</span><span class="sxs-lookup"><span data-stu-id="219d0-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="219d0-431">選取 SQLite 時，範本產生的程式碼就可以開始開發。</span><span class="sxs-lookup"><span data-stu-id="219d0-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="219d0-432">下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。</span><span class="sxs-lookup"><span data-stu-id="219d0-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="219d0-433">`IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="219d0-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="219d0-434">強型別模型和 @model 關鍵字</span><span class="sxs-lookup"><span data-stu-id="219d0-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="219d0-435">稍早在本教學課程中，您已看到控制器如何使用 `ViewData` 字典傳遞資料或物件給檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="219d0-436">`ViewData` 字典是一種動態物件，提供便利的晚期繫結方式，將資訊傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="219d0-437">MVC 也提供將強型別模型物件傳遞至檢視的能力。</span><span class="sxs-lookup"><span data-stu-id="219d0-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="219d0-438">這種強型別的方法可在編譯時檢查程式碼。</span><span class="sxs-lookup"><span data-stu-id="219d0-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="219d0-439">此方法所使用的 scaffolding 機制 (即傳遞強型別模型)，包括 `MoviesController` 類別和檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="219d0-440">檢查 *Controllers/MoviesController.cs* 檔案中產生的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="219d0-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="219d0-441">`id` 參數通常會傳遞為路由資料。</span><span class="sxs-lookup"><span data-stu-id="219d0-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="219d0-442">例如，`https://localhost:5001/movies/details/1` 設定：</span><span class="sxs-lookup"><span data-stu-id="219d0-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="219d0-443">控制器為 `movies` 控制器 (第一個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="219d0-444">動作為 `details` (第二個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="219d0-445">識別碼為 1 (最後一個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="219d0-446">您也可以在 `id` 中使用查詢字串傳遞，如下所示：</span><span class="sxs-lookup"><span data-stu-id="219d0-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="219d0-447">`id`如果未提供識別碼值，參數會定義為[可為 null 的型](/dotnet/csharp/programming-guide/nullable-types/index)別 (`int?`) 。</span><span class="sxs-lookup"><span data-stu-id="219d0-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="219d0-448">[Lambda 運算式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)會傳遞至 `FirstOrDefaultAsync`，以選取符合路由資料或查詢字串值的電影實體。</span><span class="sxs-lookup"><span data-stu-id="219d0-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="219d0-449">如果找到電影，則 `Movie` 模型的執行個體會傳遞至 `Details` 檢視：</span><span class="sxs-lookup"><span data-stu-id="219d0-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="219d0-450">檢查 *Views/Movies/Details.cshtml* 檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="219d0-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="219d0-451">檢視檔案頂端的 `@model` 陳述式會指定檢視所預期物件型別。</span><span class="sxs-lookup"><span data-stu-id="219d0-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="219d0-452">建立影片控制器時，會包含下列 `@model` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="219d0-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="219d0-453">此 `@model` 指示詞會允許存取控制器傳遞給檢視的影片。</span><span class="sxs-lookup"><span data-stu-id="219d0-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="219d0-454">`Model` 物件為強型別物件。</span><span class="sxs-lookup"><span data-stu-id="219d0-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="219d0-455">例如，在 *Details.cshtml* 檢視中，程式碼會使用強型別的 `Model` 物件，將每個電影欄位傳遞至 `DisplayNameFor` 和 `DisplayFor` HTML 協助程式。</span><span class="sxs-lookup"><span data-stu-id="219d0-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="219d0-456">`Create` 與 `Edit` 方法和檢視也會傳遞 `Movie` 模型物件。</span><span class="sxs-lookup"><span data-stu-id="219d0-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="219d0-457">檢查電影控制器中的 *Index.cshtml* 檢視和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="219d0-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="219d0-458">請注意程式碼如何在呼叫 `View` 方法時建立 `List` 物件。</span><span class="sxs-lookup"><span data-stu-id="219d0-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="219d0-459">此程式碼會從 `Index` 動作方法將 `Movies` 清單傳遞至檢視：</span><span class="sxs-lookup"><span data-stu-id="219d0-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="219d0-460">建立影片控制器時，scaffolding 會在 *Index.cshtml* 檔案的頂端包含下列 `@model` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="219d0-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="219d0-461">`@model` 指示詞可讓您使用強型別的 `Model` 物件，存取控制器傳遞至檢視的電影清單。</span><span class="sxs-lookup"><span data-stu-id="219d0-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="219d0-462">例如，在 *Index.cshtml* 檢視中，程式碼會透過強型別 `Model` 物件的 `foreach` 陳述式循環存取電影：</span><span class="sxs-lookup"><span data-stu-id="219d0-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="219d0-463">因為 `Model` 物件是強型別 (作為 `IEnumerable<Movie>` 物件)，所以迴圈中每個項目的類型為 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="219d0-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="219d0-464">撇開其他優點，這表示您會進行程式碼編譯時期檢查。</span><span class="sxs-lookup"><span data-stu-id="219d0-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="219d0-465">其他資源</span><span class="sxs-lookup"><span data-stu-id="219d0-465">Additional resources</span></span>

* [<span data-ttu-id="219d0-466">標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="219d0-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="219d0-467">全球化和當地語系化</span><span class="sxs-lookup"><span data-stu-id="219d0-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="219d0-468">[先前新增視圖](adding-view.md) 
> [下一步使用 SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="219d0-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="219d0-469">新增資料模型類別</span><span class="sxs-lookup"><span data-stu-id="219d0-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-471">以滑鼠右鍵按一下 *Models* 資料夾 > [新增] > [類別]。</span><span class="sxs-lookup"><span data-stu-id="219d0-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="219d0-472">將類別命名為 **Movie**。</span><span class="sxs-lookup"><span data-stu-id="219d0-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-473">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="219d0-474">將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="219d0-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="219d0-475">Scaffold 影片模型</span><span class="sxs-lookup"><span data-stu-id="219d0-475">Scaffold the movie model</span></span>

<span data-ttu-id="219d0-476">在本節中會 scaffold 影片模型。</span><span class="sxs-lookup"><span data-stu-id="219d0-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="219d0-477">亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="219d0-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-479">在 [方案總管] 中以滑鼠右鍵按一下 *Controllers* 資料夾 > [新增] > [新增 Scaffold 項目]。</span><span class="sxs-lookup"><span data-stu-id="219d0-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上方步驟的檢視](adding-model/_static/add_controller21.png)

<span data-ttu-id="219d0-481">在 [新增 Scaffold] 對話方塊中，選取 [使用 Entity Framework 執行檢視的 MVC 控制器] > [新增]。</span><span class="sxs-lookup"><span data-stu-id="219d0-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[新增 Scaffold] 對話方塊](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="219d0-483">完成 [新增控制器] 對話方塊：</span><span class="sxs-lookup"><span data-stu-id="219d0-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="219d0-484">**模型類別:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="219d0-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="219d0-485">**資料內容類別：** 選取 **+** 圖示並新增預設的 **MvcMovie.Models.MvcMovieContext**</span><span class="sxs-lookup"><span data-stu-id="219d0-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![新增資料內容](adding-model/_static/dc.png)

* <span data-ttu-id="219d0-487">**檢視：** 保持核取預設的每一個選項</span><span class="sxs-lookup"><span data-stu-id="219d0-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="219d0-488">**控制器名稱：** 保留預設值 *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="219d0-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="219d0-489">選取 [新增]</span><span class="sxs-lookup"><span data-stu-id="219d0-489">Select **Add**</span></span>

![[新增控制器] 對話方塊](adding-model/_static/add_controller2.png)

<span data-ttu-id="219d0-491">Visual Studio 會建立：</span><span class="sxs-lookup"><span data-stu-id="219d0-491">Visual Studio creates:</span></span>

* <span data-ttu-id="219d0-492">Entity Framework Core [資料庫內容類別](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="219d0-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="219d0-493">電影控制器 (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="219d0-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="219d0-494">Razor 查看建立、刪除、詳細資料、編輯和索引頁面的檔案 (*Views/電影/ \* cshtml*) </span><span class="sxs-lookup"><span data-stu-id="219d0-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="219d0-495">自動建立資料庫內容與 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (建立、讀取、更新和刪除) 動作方法和檢視稱為 *Scaffolding*。</span><span class="sxs-lookup"><span data-stu-id="219d0-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="219d0-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="219d0-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="219d0-497">在專案目錄 (包含 *Program.cs*、*Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。</span><span class="sxs-lookup"><span data-stu-id="219d0-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="219d0-498">安裝 Scaffolding 工具：</span><span class="sxs-lookup"><span data-stu-id="219d0-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="219d0-499">在 Linux 上，匯出 scaffold 工具路徑：</span><span class="sxs-lookup"><span data-stu-id="219d0-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="219d0-500">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="219d0-501">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="219d0-502">在專案目錄 (包含 *Program.cs*、*Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。</span><span class="sxs-lookup"><span data-stu-id="219d0-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="219d0-503">安裝 Scaffolding 工具：</span><span class="sxs-lookup"><span data-stu-id="219d0-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="219d0-504">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="219d0-505">如果執行應用程式，然後按一下 **Mvc Movie** 連結，則會收到如下錯誤：</span><span class="sxs-lookup"><span data-stu-id="219d0-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-507">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="219d0-508">您需要建立資料庫，而且您會使用 EF Core [移轉](xref:data/ef-mvc/migrations)功能來執行此作業。</span><span class="sxs-lookup"><span data-stu-id="219d0-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="219d0-509">移轉可讓您建立符合資料模型的資料庫，並在資料模型變更時，更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="219d0-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="219d0-510">初始移轉</span><span class="sxs-lookup"><span data-stu-id="219d0-510">Initial migration</span></span>

<span data-ttu-id="219d0-511">在本節中，將會完成下列作業：</span><span class="sxs-lookup"><span data-stu-id="219d0-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="219d0-512">新增初始移轉。</span><span class="sxs-lookup"><span data-stu-id="219d0-512">Add an initial migration.</span></span>
* <span data-ttu-id="219d0-513">以初始移轉更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="219d0-515">從 [工具] 功能表中，選取 [NuGet 套件管理員]**[套件管理器主控台]** >  (PMC)。</span><span class="sxs-lookup"><span data-stu-id="219d0-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC 功能表](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="219d0-517">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="219d0-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="219d0-518">`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="219d0-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="219d0-519">資料庫結構描述是以 `MvcMovieContext` 類別為基礎。</span><span class="sxs-lookup"><span data-stu-id="219d0-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="219d0-520">`Initial` 引數是移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="219d0-521">您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="219d0-522">如需詳細資訊，請參閱 <xref:data/ef-mvc/migrations> 。</span><span class="sxs-lookup"><span data-stu-id="219d0-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="219d0-523">`Update-Database` 命令會執行 *Migrations/{時間戳記}_InitialCreate.cs* 檔案中的 `Up` 方法，以建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="219d0-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-524">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="219d0-525">`ef migrations add InitialCreate` 命令會產生程式碼來建立初始資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="219d0-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="219d0-526">資料庫結構描述會以 `MvcMovieContext` 類別 (在 *Data/MvcMovieContext.cs* 檔案中) 中指定的模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="219d0-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="219d0-527">`InitialCreate` 引數是移轉名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="219d0-528">您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。</span><span class="sxs-lookup"><span data-stu-id="219d0-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="219d0-529">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="219d0-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="219d0-530">ASP.NET Core 內建[相依性插入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="219d0-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="219d0-531">服務 (例如 EF Core DB 內容) 會在應用程式啟動期間使用 DI 來註冊。</span><span class="sxs-lookup"><span data-stu-id="219d0-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="219d0-532">需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="219d0-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="219d0-533">取得資料庫內容執行個體的建構函式程式碼，本教學課程中稍後會示範。</span><span class="sxs-lookup"><span data-stu-id="219d0-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="219d0-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="219d0-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="219d0-535">Scaffolding 工具會自動建立 DB 內容，並向 DI 容器註冊該內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="219d0-536">請檢查下列 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="219d0-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="219d0-537">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="219d0-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="219d0-538">`MvcMovieContext` 會協調 `Movie` 模型的 EF Core 功能 (建立、更新、刪除等)。</span><span class="sxs-lookup"><span data-stu-id="219d0-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="219d0-539">資料內容 (`MvcMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="219d0-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="219d0-540">資料內容會指定資料模型包含哪些實體：</span><span class="sxs-lookup"><span data-stu-id="219d0-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="219d0-541">上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="219d0-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="219d0-542">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="219d0-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="219d0-543">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="219d0-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="219d0-544">連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="219d0-545">針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="219d0-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="219d0-546">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="219d0-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="219d0-547">您已建立 DB 內容，並向 DI 容器註冊該內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="219d0-548">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="219d0-548">Test the app</span></span>

* <span data-ttu-id="219d0-549">執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="219d0-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="219d0-550">如果您收到類似如下的資料庫例外狀況：</span><span class="sxs-lookup"><span data-stu-id="219d0-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="219d0-551">您遺失了[移轉步驟](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="219d0-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="219d0-552">測試 **Create** 連結。</span><span class="sxs-lookup"><span data-stu-id="219d0-552">Test the **Create** link.</span></span> <span data-ttu-id="219d0-553">輸入並提交資料。</span><span class="sxs-lookup"><span data-stu-id="219d0-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="219d0-554">您可能無法在 `Price` 欄位中輸入小數逗號。</span><span class="sxs-lookup"><span data-stu-id="219d0-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="219d0-555">若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。</span><span class="sxs-lookup"><span data-stu-id="219d0-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="219d0-556">如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="219d0-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="219d0-557">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="219d0-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="219d0-558">檢查 `Startup` 類別：</span><span class="sxs-lookup"><span data-stu-id="219d0-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="219d0-559">上述醒目提示的程式碼會顯示要新增至[相依性插入](xref:fundamentals/dependency-injection)容器的電影資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="219d0-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="219d0-560">`services.AddDbContext<MvcMovieContext>(options =>` 指定要使用的資料庫和連線字串。</span><span class="sxs-lookup"><span data-stu-id="219d0-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="219d0-561">`=>` 是 [Lambda 運算子](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="219d0-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="219d0-562">開啟 *Controllers/MoviesController.cs* 檔案，並檢查建構函式：</span><span class="sxs-lookup"><span data-stu-id="219d0-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="219d0-563">建構函式會使用[相依性插入](xref:fundamentals/dependency-injection)將資料庫內容 (`MvcMovieContext`) 插入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="219d0-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="219d0-564">控制器中的每一個 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法都會使用資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="219d0-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="219d0-565">強型別模型和 @model 關鍵字</span><span class="sxs-lookup"><span data-stu-id="219d0-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="219d0-566">稍早在本教學課程中，您已看到控制器如何使用 `ViewData` 字典傳遞資料或物件給檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="219d0-567">`ViewData` 字典是一種動態物件，提供便利的晚期繫結方式，將資訊傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="219d0-568">MVC 也提供將強型別模型物件傳遞至檢視的能力。</span><span class="sxs-lookup"><span data-stu-id="219d0-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="219d0-569">強型別方法可讓程式碼的編譯時期檢查變得更佳。</span><span class="sxs-lookup"><span data-stu-id="219d0-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="219d0-570">Scaffolding 機制在建立方法和檢視時，已使用此方法 (也就是傳遞強型別模型) 來搭配 `MoviesController` 類別和檢視。</span><span class="sxs-lookup"><span data-stu-id="219d0-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="219d0-571">檢查 *Controllers/MoviesController.cs* 檔案中產生的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="219d0-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="219d0-572">`id` 參數通常會傳遞為路由資料。</span><span class="sxs-lookup"><span data-stu-id="219d0-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="219d0-573">例如，`https://localhost:5001/movies/details/1` 設定：</span><span class="sxs-lookup"><span data-stu-id="219d0-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="219d0-574">控制器為 `movies` 控制器 (第一個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="219d0-575">動作為 `details` (第二個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="219d0-576">識別碼為 1 (最後一個 URL 區段)。</span><span class="sxs-lookup"><span data-stu-id="219d0-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="219d0-577">您也可以在 `id` 中使用查詢字串傳遞，如下所示：</span><span class="sxs-lookup"><span data-stu-id="219d0-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="219d0-578">`id`如果未提供識別碼值，參數會定義為[可為 null 的型](/dotnet/csharp/programming-guide/nullable-types/index)別 (`int?`) 。</span><span class="sxs-lookup"><span data-stu-id="219d0-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="219d0-579">[Lambda 運算式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)會傳遞至 `FirstOrDefaultAsync`，以選取符合路由資料或查詢字串值的電影實體。</span><span class="sxs-lookup"><span data-stu-id="219d0-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="219d0-580">如果找到電影，則 `Movie` 模型的執行個體會傳遞至 `Details` 檢視：</span><span class="sxs-lookup"><span data-stu-id="219d0-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="219d0-581">檢查 *Views/Movies/Details.cshtml* 檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="219d0-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="219d0-582">藉由在檢視檔案的最上方包含 `@model` 陳述式，您可以指定檢視預期要有的物件類型。</span><span class="sxs-lookup"><span data-stu-id="219d0-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="219d0-583">當您建立電影控制器時，*Details.cshtml* 檔案的最上方會自動包含下列 `@model` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="219d0-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="219d0-584">這個 `@model` 指示詞可讓您使用強型別的 `Model` 物件，存取控制器傳遞至檢視的電影。</span><span class="sxs-lookup"><span data-stu-id="219d0-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="219d0-585">例如，在 *Details.cshtml* 檢視中，程式碼會使用強型別的 `Model` 物件，將每個電影欄位傳遞至 `DisplayNameFor` 和 `DisplayFor` HTML 協助程式。</span><span class="sxs-lookup"><span data-stu-id="219d0-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="219d0-586">`Create` 與 `Edit` 方法和檢視也會傳遞 `Movie` 模型物件。</span><span class="sxs-lookup"><span data-stu-id="219d0-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="219d0-587">檢查電影控制器中的 *Index.cshtml* 檢視和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="219d0-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="219d0-588">請注意程式碼如何在呼叫 `View` 方法時建立 `List` 物件。</span><span class="sxs-lookup"><span data-stu-id="219d0-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="219d0-589">此程式碼會從 `Index` 動作方法將 `Movies` 清單傳遞至檢視：</span><span class="sxs-lookup"><span data-stu-id="219d0-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="219d0-590">當您建立電影控制器時，Scaffolding 會在 *Index.cshtml* 檔案的最上方自動包含下列 `@model` 陳述式：</span><span class="sxs-lookup"><span data-stu-id="219d0-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="219d0-591">`@model` 指示詞可讓您使用強型別的 `Model` 物件，存取控制器傳遞至檢視的電影清單。</span><span class="sxs-lookup"><span data-stu-id="219d0-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="219d0-592">例如，在 *Index.cshtml* 檢視中，程式碼會透過強型別 `Model` 物件的 `foreach` 陳述式循環存取電影：</span><span class="sxs-lookup"><span data-stu-id="219d0-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="219d0-593">因為 `Model` 物件是強型別 (作為 `IEnumerable<Movie>` 物件)，所以迴圈中每個項目的類型為 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="219d0-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="219d0-594">撇開其他優點，這表示您會進行程式碼編譯時期檢查：</span><span class="sxs-lookup"><span data-stu-id="219d0-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="219d0-595">其他資源</span><span class="sxs-lookup"><span data-stu-id="219d0-595">Additional resources</span></span>

* [<span data-ttu-id="219d0-596">標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="219d0-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="219d0-597">全球化和當地語系化</span><span class="sxs-lookup"><span data-stu-id="219d0-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="219d0-598">[先前新增視圖](adding-view.md) 
> [下一步使用資料庫](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="219d0-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
