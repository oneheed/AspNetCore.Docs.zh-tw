---
title: '第7部分： :::no-loc(Razor)::: 在頁面中新增欄位，ASP.NET Core'
author: rick-anderson
description: '頁面上教學課程系列的第7部分 :::no-loc(Razor)::: 。'
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 951a8ada57ae523f362313426c0279556eb8339b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050611"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="83f83-103">第7部分： :::no-loc(Razor)::: 在頁面中新增欄位，ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83f83-103">Part 7, add a new field to a :::no-loc(Razor)::: Page in ASP.NET Core</span></span>

<span data-ttu-id="83f83-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83f83-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="83f83-105">在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：</span><span class="sxs-lookup"><span data-stu-id="83f83-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="83f83-106">在模型中新增一個欄位。</span><span class="sxs-lookup"><span data-stu-id="83f83-106">Add a new field to the model.</span></span>
* <span data-ttu-id="83f83-107">將新的欄位結構描述變更移轉至資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="83f83-108">使用 EF Code First 自動建立資料庫時，Code First 會：</span><span class="sxs-lookup"><span data-stu-id="83f83-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="83f83-109">將 `__EFMigrationsHistory` 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。</span><span class="sxs-lookup"><span data-stu-id="83f83-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="83f83-110">如果模型類別與資料庫不同步，EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="83f83-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="83f83-111">自動驗證結構描述/模型是否同步，讓您更容易發現不一致的資料庫/程式碼問題。</span><span class="sxs-lookup"><span data-stu-id="83f83-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="83f83-112">將 Rating 屬性新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="83f83-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="83f83-113">開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：</span><span class="sxs-lookup"><span data-stu-id="83f83-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="83f83-114">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="83f83-114">Build the app.</span></span>

<span data-ttu-id="83f83-115">編輯 *Pages/Movies/Index.cshtml* ，然後新增 `Rating` 欄位：</span><span class="sxs-lookup"><span data-stu-id="83f83-115">Edit *Pages/Movies/Index.cshtml* , and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="83f83-116">更新下列頁面：</span><span class="sxs-lookup"><span data-stu-id="83f83-116">Update the following pages:</span></span>

* <span data-ttu-id="83f83-117">將 `Rating` 欄位新增至 Delete 和 Details 頁面。</span><span class="sxs-lookup"><span data-stu-id="83f83-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="83f83-118">使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="83f83-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="83f83-119">將 `Rating` 欄位新增至 Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="83f83-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="83f83-120">在更新資料庫以包含新欄位之前，應用程式無法運作。</span><span class="sxs-lookup"><span data-stu-id="83f83-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="83f83-121">在不更新資料庫的情況下執行應用程式，會擲回 `SqlException` ：</span><span class="sxs-lookup"><span data-stu-id="83f83-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="83f83-122">`SqlException`例外狀況是因為更新的電影模型類別與資料庫之電影資料表的架構不同而造成。</span><span class="sxs-lookup"><span data-stu-id="83f83-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="83f83-123">(資料庫資料表中沒有任何 `Rating` 資料行)。</span><span class="sxs-lookup"><span data-stu-id="83f83-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="83f83-124">有幾個方法可以解決這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="83f83-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="83f83-125">讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="83f83-126">在開發週期早期，這個方法會很方便；其可讓您一併調整模型和資料庫結構描述，更加快速。</span><span class="sxs-lookup"><span data-stu-id="83f83-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="83f83-127">缺點是會遺失在資料庫中的現有資料。</span><span class="sxs-lookup"><span data-stu-id="83f83-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="83f83-128">請勿在生產環境資料庫上使用此方法！</span><span class="sxs-lookup"><span data-stu-id="83f83-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="83f83-129">在結構描述變更時卸除資料庫以及使用初始設定式將測試資料自動植入資料庫，通常是開發應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="83f83-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="83f83-130">您可明確修改現有資料庫的結構描述，使其符合模型類別。</span><span class="sxs-lookup"><span data-stu-id="83f83-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="83f83-131">這種方法的優點是可以保留您的資料。</span><span class="sxs-lookup"><span data-stu-id="83f83-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="83f83-132">您可以手動方式或藉由建立資料庫變更指令碼來進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="83f83-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="83f83-133">使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="83f83-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="83f83-134">在本教學課程中，請使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="83f83-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="83f83-135">更新 `SeedData` 類別，使其提供新資料行的值。</span><span class="sxs-lookup"><span data-stu-id="83f83-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="83f83-136">範例變更如下所示，但您會想要為每個 `new Movie` 區塊進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="83f83-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="83f83-137">請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedDataRating.cs) (英文)。</span><span class="sxs-lookup"><span data-stu-id="83f83-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="83f83-138">建置方案。</span><span class="sxs-lookup"><span data-stu-id="83f83-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83f83-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83f83-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="83f83-140">新增評等欄位的移轉</span><span class="sxs-lookup"><span data-stu-id="83f83-140">Add a migration for the rating field</span></span>

<span data-ttu-id="83f83-141"> 從 [工具]  功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]  。</span><span class="sxs-lookup"><span data-stu-id="83f83-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console** .</span></span>
<span data-ttu-id="83f83-142">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="83f83-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="83f83-143">`Add-Migration` 命令會告知架構，以便：</span><span class="sxs-lookup"><span data-stu-id="83f83-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="83f83-144">比較 `Movie` 模型與 `Movie` 資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="83f83-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="83f83-145">建立程式碼，將資料庫結構描述移轉至新模型。</span><span class="sxs-lookup"><span data-stu-id="83f83-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="83f83-146">"Rating" 是用來命名移轉檔案的任意名稱。</span><span class="sxs-lookup"><span data-stu-id="83f83-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="83f83-147">建議您針對移轉檔案使用有意義的名稱，這更加實用。</span><span class="sxs-lookup"><span data-stu-id="83f83-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="83f83-148">此 `Update-Database` 命令會指示架構將架構變更套用至資料庫，並保留現有的資料。</span><span class="sxs-lookup"><span data-stu-id="83f83-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="83f83-149">如果您刪除資料庫中的所有記錄，初始設定式會將內容植入資料庫，並包含 `Rating` 欄位。</span><span class="sxs-lookup"><span data-stu-id="83f83-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="83f83-150">您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="83f83-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="83f83-151">另一個選擇是刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="83f83-152">若要在 SSOX 中刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="83f83-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="83f83-153">在 SSOX 中選取資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="83f83-154">以滑鼠右鍵按一下資料庫，然後選取 [刪除]  。</span><span class="sxs-lookup"><span data-stu-id="83f83-154">Right click on the database, and select *Delete* .</span></span>
* <span data-ttu-id="83f83-155">勾選 [ **關閉現有的連接** ]。</span><span class="sxs-lookup"><span data-stu-id="83f83-155">Check **Close existing connections** .</span></span>
* <span data-ttu-id="83f83-156">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="83f83-156">Select **OK** .</span></span>
* <span data-ttu-id="83f83-157">在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="83f83-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="83f83-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83f83-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="83f83-159">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="83f83-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="83f83-160">刪除移轉資料夾。</span><span class="sxs-lookup"><span data-stu-id="83f83-160">Delete the migration folder.</span></span>  <span data-ttu-id="83f83-161">使用下列命令重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="83f83-162">執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。</span><span class="sxs-lookup"><span data-stu-id="83f83-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="83f83-163">若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="83f83-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83f83-164">其他資源</span><span class="sxs-lookup"><span data-stu-id="83f83-164">Additional resources</span></span>

* [<span data-ttu-id="83f83-165">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="83f83-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="83f83-166">[上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="83f83-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="83f83-167">在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：</span><span class="sxs-lookup"><span data-stu-id="83f83-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="83f83-168">在模型中新增一個欄位。</span><span class="sxs-lookup"><span data-stu-id="83f83-168">Add a new field to the model.</span></span>
* <span data-ttu-id="83f83-169">將新的欄位結構描述變更移轉至資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="83f83-170">使用 EF Code First 自動建立資料庫時，Code First 會：</span><span class="sxs-lookup"><span data-stu-id="83f83-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="83f83-171">將資料表新增至資料庫，以追蹤資料庫的結構描述是否與其產生的來源模型類別同步。</span><span class="sxs-lookup"><span data-stu-id="83f83-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="83f83-172">如果模型類別與資料庫不同步，EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="83f83-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="83f83-173">自動驗證結構描述/模型是否同步，讓您更容易發現不一致的資料庫/程式碼問題。</span><span class="sxs-lookup"><span data-stu-id="83f83-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="83f83-174">將 Rating 屬性新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="83f83-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="83f83-175">開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：</span><span class="sxs-lookup"><span data-stu-id="83f83-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="83f83-176">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="83f83-176">Build the app.</span></span>

<span data-ttu-id="83f83-177">編輯 *Pages/Movies/Index.cshtml* ，然後新增 `Rating` 欄位：</span><span class="sxs-lookup"><span data-stu-id="83f83-177">Edit *Pages/Movies/Index.cshtml* , and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="83f83-178">更新下列頁面：</span><span class="sxs-lookup"><span data-stu-id="83f83-178">Update the following pages:</span></span>

* <span data-ttu-id="83f83-179">將 `Rating` 欄位新增至 Delete 和 Details 頁面。</span><span class="sxs-lookup"><span data-stu-id="83f83-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="83f83-180">使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="83f83-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="83f83-181">將 `Rating` 欄位新增至 Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="83f83-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="83f83-182">在更新資料庫以包含新欄位之前，應用程式無法運作。</span><span class="sxs-lookup"><span data-stu-id="83f83-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="83f83-183">如果立即執行，應用程式會擲回 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="83f83-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="83f83-184">此錯誤是因為更新的電影模型類別，不同於資料庫之電影資料表的結構描述</span><span class="sxs-lookup"><span data-stu-id="83f83-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="83f83-185">(資料庫資料表中沒有任何 `Rating` 資料行)。</span><span class="sxs-lookup"><span data-stu-id="83f83-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="83f83-186">有幾個方法可以解決這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="83f83-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="83f83-187">讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="83f83-188">在開發週期早期，這個方法會很方便；其可讓您一併調整模型和資料庫結構描述，更加快速。</span><span class="sxs-lookup"><span data-stu-id="83f83-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="83f83-189">缺點是會遺失在資料庫中的現有資料。</span><span class="sxs-lookup"><span data-stu-id="83f83-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="83f83-190">請勿在生產環境資料庫上使用此方法！</span><span class="sxs-lookup"><span data-stu-id="83f83-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="83f83-191">在結構描述變更時卸除資料庫以及使用初始設定式將測試資料自動植入資料庫，通常是開發應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="83f83-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="83f83-192">您可明確修改現有資料庫的結構描述，使其符合模型類別。</span><span class="sxs-lookup"><span data-stu-id="83f83-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="83f83-193">這種方法的優點是可以保留您的資料。</span><span class="sxs-lookup"><span data-stu-id="83f83-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="83f83-194">您可以手動方式或藉由建立資料庫變更指令碼來進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="83f83-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="83f83-195">使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="83f83-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="83f83-196">在本教學課程中，請使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="83f83-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="83f83-197">更新 `SeedData` 類別，使其提供新資料行的值。</span><span class="sxs-lookup"><span data-stu-id="83f83-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="83f83-198">範例變更如下所示，但您會想要為每個 `new Movie` 區塊進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="83f83-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="83f83-199">請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs) (英文)。</span><span class="sxs-lookup"><span data-stu-id="83f83-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="83f83-200">建置方案。</span><span class="sxs-lookup"><span data-stu-id="83f83-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83f83-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83f83-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="83f83-202">新增評等欄位的移轉</span><span class="sxs-lookup"><span data-stu-id="83f83-202">Add a migration for the rating field</span></span>

<span data-ttu-id="83f83-203"> 從 [工具]  功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]  。</span><span class="sxs-lookup"><span data-stu-id="83f83-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console** .</span></span>
<span data-ttu-id="83f83-204">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="83f83-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="83f83-205">`Add-Migration` 命令會告知架構，以便：</span><span class="sxs-lookup"><span data-stu-id="83f83-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="83f83-206">比較 `Movie` 模型與 `Movie` 資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="83f83-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="83f83-207">建立程式碼，將資料庫結構描述移轉至新模型。</span><span class="sxs-lookup"><span data-stu-id="83f83-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="83f83-208">"Rating" 是用來命名移轉檔案的任意名稱。</span><span class="sxs-lookup"><span data-stu-id="83f83-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="83f83-209">建議您針對移轉檔案使用有意義的名稱，這更加實用。</span><span class="sxs-lookup"><span data-stu-id="83f83-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="83f83-210">`Update-Database` 命令會指示架構將結構描述變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="83f83-211">如果您刪除資料庫中的所有記錄，初始設定式會將內容植入資料庫，並包含 `Rating` 欄位。</span><span class="sxs-lookup"><span data-stu-id="83f83-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="83f83-212">您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="83f83-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="83f83-213">另一個選擇是刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="83f83-214">若要在 SSOX 中刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="83f83-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="83f83-215">在 SSOX 中選取資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="83f83-216">以滑鼠右鍵按一下資料庫，然後選取 [刪除]  。</span><span class="sxs-lookup"><span data-stu-id="83f83-216">Right click on the database, and select *Delete* .</span></span>
* <span data-ttu-id="83f83-217">勾選 [ **關閉現有的連接** ]。</span><span class="sxs-lookup"><span data-stu-id="83f83-217">Check **Close existing connections** .</span></span>
* <span data-ttu-id="83f83-218">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="83f83-218">Select **OK** .</span></span>
* <span data-ttu-id="83f83-219">在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="83f83-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="83f83-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="83f83-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="83f83-221">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="83f83-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="83f83-222">刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="83f83-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="83f83-223">若要刪除資料庫，請刪除資料庫檔案 ( *MvcMovie.db* )。</span><span class="sxs-lookup"><span data-stu-id="83f83-223">To delete the database, delete the database file ( *MvcMovie.db* ).</span></span> <span data-ttu-id="83f83-224">然後執行 `ef database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="83f83-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="83f83-225">執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。</span><span class="sxs-lookup"><span data-stu-id="83f83-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="83f83-226">若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="83f83-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83f83-227">其他資源</span><span class="sxs-lookup"><span data-stu-id="83f83-227">Additional resources</span></span>

* [<span data-ttu-id="83f83-228">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="83f83-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="83f83-229">[上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="83f83-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
