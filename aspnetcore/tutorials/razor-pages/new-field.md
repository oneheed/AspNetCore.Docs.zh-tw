---
title: 第7部分：新增欄位
author: rick-anderson
description: 頁面上教學課程系列的第7部分 Razor 。
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2961fcaa8bbb93527eb80f5aa7b18ef04761fb50
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486157"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="20bc3-103">第7部分： Razor 在頁面中新增欄位，ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20bc3-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="20bc3-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="20bc3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="20bc3-105">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="20bc3-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="20bc3-106">在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：</span><span class="sxs-lookup"><span data-stu-id="20bc3-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="20bc3-107">在模型中新增一個欄位。</span><span class="sxs-lookup"><span data-stu-id="20bc3-107">Add a new field to the model.</span></span>
* <span data-ttu-id="20bc3-108">將新的欄位結構描述變更移轉至資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="20bc3-109">使用 EF Code First 自動建立資料庫時，Code First 會：</span><span class="sxs-lookup"><span data-stu-id="20bc3-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="20bc3-110">將 [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。</span><span class="sxs-lookup"><span data-stu-id="20bc3-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="20bc3-111">如果模型類別未與資料庫同步，EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20bc3-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="20bc3-112">架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。</span><span class="sxs-lookup"><span data-stu-id="20bc3-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="20bc3-113">將 Rating 屬性新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="20bc3-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="20bc3-114">開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：</span><span class="sxs-lookup"><span data-stu-id="20bc3-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="20bc3-115">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="20bc3-115">Build the app.</span></span>

1. <span data-ttu-id="20bc3-116">編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：</span><span class="sxs-lookup"><span data-stu-id="20bc3-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="20bc3-117">更新下列頁面：</span><span class="sxs-lookup"><span data-stu-id="20bc3-117">Update the following pages:</span></span>
   1. <span data-ttu-id="20bc3-118">將 `Rating` 欄位新增至 Delete 和 Details 頁面。</span><span class="sxs-lookup"><span data-stu-id="20bc3-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="20bc3-119">使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="20bc3-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="20bc3-120">將 `Rating` 欄位新增至 Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="20bc3-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="20bc3-121">在資料庫更新為包含新欄位之前，應用程式將無法運作。</span><span class="sxs-lookup"><span data-stu-id="20bc3-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="20bc3-122">在沒有資料庫更新的情況下執行應用程式，會擲回 `SqlException` ：</span><span class="sxs-lookup"><span data-stu-id="20bc3-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="20bc3-123">`SqlException`例外狀況是因為更新的電影模型類別與資料庫之電影資料表的架構不同而造成。</span><span class="sxs-lookup"><span data-stu-id="20bc3-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="20bc3-124">`Rating`資料庫資料表中沒有資料行。</span><span class="sxs-lookup"><span data-stu-id="20bc3-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="20bc3-125">有幾個方法可以解決這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="20bc3-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="20bc3-126">讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="20bc3-127">這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。</span><span class="sxs-lookup"><span data-stu-id="20bc3-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="20bc3-128">缺點是會遺失在資料庫中的現有資料。</span><span class="sxs-lookup"><span data-stu-id="20bc3-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="20bc3-129">請勿在生產環境資料庫上使用此方法！</span><span class="sxs-lookup"><span data-stu-id="20bc3-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="20bc3-130">在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="20bc3-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="20bc3-131">您可明確修改現有資料庫的結構描述，使其符合模型類別。</span><span class="sxs-lookup"><span data-stu-id="20bc3-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="20bc3-132">這種方法的優點是保留資料。</span><span class="sxs-lookup"><span data-stu-id="20bc3-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="20bc3-133">請手動或藉由建立資料庫變更腳本來進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="20bc3-134">使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="20bc3-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="20bc3-135">在本教學課程中，請使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="20bc3-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="20bc3-136">更新 `SeedData` 類別，使其提供新資料行的值。</span><span class="sxs-lookup"><span data-stu-id="20bc3-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="20bc3-137">範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。</span><span class="sxs-lookup"><span data-stu-id="20bc3-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="20bc3-138">請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs) (英文)。</span><span class="sxs-lookup"><span data-stu-id="20bc3-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="20bc3-139">建置方案。</span><span class="sxs-lookup"><span data-stu-id="20bc3-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20bc3-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20bc3-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="20bc3-141">新增評等欄位的移轉</span><span class="sxs-lookup"><span data-stu-id="20bc3-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="20bc3-142"> 從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="20bc3-143">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="20bc3-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="20bc3-144">`Add-Migration` 命令會告知架構，以便：</span><span class="sxs-lookup"><span data-stu-id="20bc3-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="20bc3-145">比較 `Movie` 模型與 `Movie` 資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="20bc3-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="20bc3-146">建立程式碼，將資料庫架構遷移至新的模型。</span><span class="sxs-lookup"><span data-stu-id="20bc3-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="20bc3-147">"Rating" 是用來命名移轉檔案的任意名稱。</span><span class="sxs-lookup"><span data-stu-id="20bc3-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="20bc3-148">建議您針對移轉檔案使用有意義的名稱，這更加實用。</span><span class="sxs-lookup"><span data-stu-id="20bc3-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="20bc3-149">此 `Update-Database` 命令會指示架構將架構變更套用至資料庫，並保留現有的資料。</span><span class="sxs-lookup"><span data-stu-id="20bc3-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="20bc3-150">如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。</span><span class="sxs-lookup"><span data-stu-id="20bc3-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="20bc3-151">您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="20bc3-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="20bc3-152">另一個選擇是刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="20bc3-153">若要在 SSOX 中刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="20bc3-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="20bc3-154">在 SSOX 中選取資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="20bc3-155">以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="20bc3-156">勾選 [ **關閉現有的連接**]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="20bc3-157">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-157">Select **OK**.</span></span>
1. <span data-ttu-id="20bc3-158">在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="20bc3-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20bc3-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20bc3-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="20bc3-160">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="20bc3-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="20bc3-161">在本教學課程中，您會盡可能使用 Entity Framework Core 的 *遷移* 功能。</span><span class="sxs-lookup"><span data-stu-id="20bc3-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="20bc3-162">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="20bc3-163">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="20bc3-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="20bc3-164">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="20bc3-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="20bc3-165">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="20bc3-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="20bc3-166">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="20bc3-167">反之，當結構描述變更時，請您卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="20bc3-168">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="20bc3-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="20bc3-169">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="20bc3-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="20bc3-170">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-170">Creating a new table.</span></span>
>* <span data-ttu-id="20bc3-171">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="20bc3-172">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-172">Dropping the old table.</span></span>
>* <span data-ttu-id="20bc3-173">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-173">Renaming the new table.</span></span>
>
><span data-ttu-id="20bc3-174">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="20bc3-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="20bc3-175">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="20bc3-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="20bc3-176">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="20bc3-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="20bc3-177">資料植入</span><span class="sxs-lookup"><span data-stu-id="20bc3-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="20bc3-178">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="20bc3-178">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>

1. <span data-ttu-id="20bc3-179">刪除移轉資料夾。</span><span class="sxs-lookup"><span data-stu-id="20bc3-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="20bc3-180">使用下列命令重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="20bc3-181">執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。</span><span class="sxs-lookup"><span data-stu-id="20bc3-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="20bc3-182">若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="20bc3-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20bc3-183">其他資源</span><span class="sxs-lookup"><span data-stu-id="20bc3-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="20bc3-184">[上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="20bc3-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="20bc3-185">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="20bc3-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="20bc3-186">在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：</span><span class="sxs-lookup"><span data-stu-id="20bc3-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="20bc3-187">在模型中新增一個欄位。</span><span class="sxs-lookup"><span data-stu-id="20bc3-187">Add a new field to the model.</span></span>
* <span data-ttu-id="20bc3-188">將新的欄位結構描述變更移轉至資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="20bc3-189">使用 EF Code First 自動建立資料庫時，Code First 會：</span><span class="sxs-lookup"><span data-stu-id="20bc3-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="20bc3-190">將 [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。</span><span class="sxs-lookup"><span data-stu-id="20bc3-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="20bc3-191">如果模型類別未與資料庫同步，EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20bc3-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="20bc3-192">架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。</span><span class="sxs-lookup"><span data-stu-id="20bc3-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="20bc3-193">將 Rating 屬性新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="20bc3-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="20bc3-194">開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：</span><span class="sxs-lookup"><span data-stu-id="20bc3-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="20bc3-195">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="20bc3-195">Build the app.</span></span>

1. <span data-ttu-id="20bc3-196">編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：</span><span class="sxs-lookup"><span data-stu-id="20bc3-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="20bc3-197">更新下列頁面：</span><span class="sxs-lookup"><span data-stu-id="20bc3-197">Update the following pages:</span></span>
   1. <span data-ttu-id="20bc3-198">將 `Rating` 欄位新增至 Delete 和 Details 頁面。</span><span class="sxs-lookup"><span data-stu-id="20bc3-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="20bc3-199">使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="20bc3-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="20bc3-200">將 `Rating` 欄位新增至 Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="20bc3-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="20bc3-201">在資料庫更新為包含新欄位之前，應用程式將無法運作。</span><span class="sxs-lookup"><span data-stu-id="20bc3-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="20bc3-202">在沒有資料庫更新的情況下執行應用程式，會擲回 `SqlException` ：</span><span class="sxs-lookup"><span data-stu-id="20bc3-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="20bc3-203">`SqlException`例外狀況是因為更新的電影模型類別與資料庫之電影資料表的架構不同而造成。</span><span class="sxs-lookup"><span data-stu-id="20bc3-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="20bc3-204">`Rating`資料庫資料表中沒有資料行。</span><span class="sxs-lookup"><span data-stu-id="20bc3-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="20bc3-205">有幾個方法可以解決這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="20bc3-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="20bc3-206">讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="20bc3-207">這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。</span><span class="sxs-lookup"><span data-stu-id="20bc3-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="20bc3-208">缺點是會遺失在資料庫中的現有資料。</span><span class="sxs-lookup"><span data-stu-id="20bc3-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="20bc3-209">請勿在生產環境資料庫上使用此方法！</span><span class="sxs-lookup"><span data-stu-id="20bc3-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="20bc3-210">在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="20bc3-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="20bc3-211">您可明確修改現有資料庫的結構描述，使其符合模型類別。</span><span class="sxs-lookup"><span data-stu-id="20bc3-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="20bc3-212">這種方法的優點是保留資料。</span><span class="sxs-lookup"><span data-stu-id="20bc3-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="20bc3-213">請手動或藉由建立資料庫變更腳本來進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="20bc3-214">使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="20bc3-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="20bc3-215">在本教學課程中，請使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="20bc3-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="20bc3-216">更新 `SeedData` 類別，使其提供新資料行的值。</span><span class="sxs-lookup"><span data-stu-id="20bc3-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="20bc3-217">範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。</span><span class="sxs-lookup"><span data-stu-id="20bc3-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="20bc3-218">請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs) (英文)。</span><span class="sxs-lookup"><span data-stu-id="20bc3-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="20bc3-219">建置方案。</span><span class="sxs-lookup"><span data-stu-id="20bc3-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20bc3-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20bc3-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="20bc3-221">新增評等欄位的移轉</span><span class="sxs-lookup"><span data-stu-id="20bc3-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="20bc3-222"> 從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="20bc3-223">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="20bc3-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="20bc3-224">`Add-Migration` 命令會告知架構，以便：</span><span class="sxs-lookup"><span data-stu-id="20bc3-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="20bc3-225">比較 `Movie` 模型與 `Movie` 資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="20bc3-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="20bc3-226">建立程式碼，將資料庫架構遷移至新的模型。</span><span class="sxs-lookup"><span data-stu-id="20bc3-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="20bc3-227">"Rating" 是用來命名移轉檔案的任意名稱。</span><span class="sxs-lookup"><span data-stu-id="20bc3-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="20bc3-228">建議您針對移轉檔案使用有意義的名稱，這更加實用。</span><span class="sxs-lookup"><span data-stu-id="20bc3-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="20bc3-229">此 `Update-Database` 命令會指示架構將架構變更套用至資料庫，並保留現有的資料。</span><span class="sxs-lookup"><span data-stu-id="20bc3-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="20bc3-230">如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。</span><span class="sxs-lookup"><span data-stu-id="20bc3-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="20bc3-231">您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="20bc3-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="20bc3-232">另一個選擇是刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="20bc3-233">若要在 SSOX 中刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="20bc3-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="20bc3-234">在 SSOX 中選取資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="20bc3-235">以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="20bc3-236">勾選 [ **關閉現有的連接**]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="20bc3-237">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-237">Select **OK**.</span></span>
* <span data-ttu-id="20bc3-238">在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="20bc3-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20bc3-239">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20bc3-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="20bc3-240">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="20bc3-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="20bc3-241">在本教學課程中，您可以盡可能使用 Entity Framework Core 的 *遷移* 功能。</span><span class="sxs-lookup"><span data-stu-id="20bc3-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="20bc3-242">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="20bc3-243">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="20bc3-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="20bc3-244">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="20bc3-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="20bc3-245">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="20bc3-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="20bc3-246">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="20bc3-247">反之，當結構描述變更時，請您卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="20bc3-248">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="20bc3-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="20bc3-249">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="20bc3-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="20bc3-250">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-250">Creating a new table.</span></span>
>* <span data-ttu-id="20bc3-251">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="20bc3-252">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-252">Dropping the old table.</span></span>
>* <span data-ttu-id="20bc3-253">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-253">Renaming the new table.</span></span>
>
><span data-ttu-id="20bc3-254">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="20bc3-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="20bc3-255">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="20bc3-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="20bc3-256">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="20bc3-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="20bc3-257">資料植入</span><span class="sxs-lookup"><span data-stu-id="20bc3-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="20bc3-258">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="20bc3-258">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>

1. <span data-ttu-id="20bc3-259">刪除移轉資料夾。</span><span class="sxs-lookup"><span data-stu-id="20bc3-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="20bc3-260">使用下列命令重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="20bc3-261">執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。</span><span class="sxs-lookup"><span data-stu-id="20bc3-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="20bc3-262">若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="20bc3-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20bc3-263">其他資源</span><span class="sxs-lookup"><span data-stu-id="20bc3-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="20bc3-264">[上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="20bc3-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="20bc3-265">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="20bc3-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="20bc3-266">在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：</span><span class="sxs-lookup"><span data-stu-id="20bc3-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="20bc3-267">在模型中新增一個欄位。</span><span class="sxs-lookup"><span data-stu-id="20bc3-267">Add a new field to the model.</span></span>
* <span data-ttu-id="20bc3-268">將新的欄位結構描述變更移轉至資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="20bc3-269">使用 EF Code First 自動建立資料庫時，Code First 會：</span><span class="sxs-lookup"><span data-stu-id="20bc3-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="20bc3-270">將 [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。</span><span class="sxs-lookup"><span data-stu-id="20bc3-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="20bc3-271">如果模型類別未與資料庫同步，EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="20bc3-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="20bc3-272">架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。</span><span class="sxs-lookup"><span data-stu-id="20bc3-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="20bc3-273">將 Rating 屬性新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="20bc3-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="20bc3-274">開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：</span><span class="sxs-lookup"><span data-stu-id="20bc3-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="20bc3-275">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="20bc3-275">Build the app.</span></span>

<span data-ttu-id="20bc3-276">編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：</span><span class="sxs-lookup"><span data-stu-id="20bc3-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="20bc3-277">更新下列頁面：</span><span class="sxs-lookup"><span data-stu-id="20bc3-277">Update the following pages:</span></span>

* <span data-ttu-id="20bc3-278">將 `Rating` 欄位新增至 Delete 和 Details 頁面。</span><span class="sxs-lookup"><span data-stu-id="20bc3-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="20bc3-279">使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="20bc3-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="20bc3-280">將 `Rating` 欄位新增至 Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="20bc3-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="20bc3-281">在資料庫更新為包含新欄位之前，應用程式將無法運作。</span><span class="sxs-lookup"><span data-stu-id="20bc3-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="20bc3-282">如果立即執行應用程式，應用程式會擲回 `SqlException` ：</span><span class="sxs-lookup"><span data-stu-id="20bc3-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="20bc3-283">此錯誤是因為更新的電影模型類別，不同於資料庫之電影資料表的結構描述</span><span class="sxs-lookup"><span data-stu-id="20bc3-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="20bc3-284">`Rating`資料庫資料表中沒有資料行。</span><span class="sxs-lookup"><span data-stu-id="20bc3-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="20bc3-285">有幾個方法可以解決這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="20bc3-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="20bc3-286">讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="20bc3-287">這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。</span><span class="sxs-lookup"><span data-stu-id="20bc3-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="20bc3-288">缺點是會遺失在資料庫中的現有資料。</span><span class="sxs-lookup"><span data-stu-id="20bc3-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="20bc3-289">請勿在生產環境資料庫上使用此方法！</span><span class="sxs-lookup"><span data-stu-id="20bc3-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="20bc3-290">在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="20bc3-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="20bc3-291">您可明確修改現有資料庫的結構描述，使其符合模型類別。</span><span class="sxs-lookup"><span data-stu-id="20bc3-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="20bc3-292">這種方法的優點是保留資料。</span><span class="sxs-lookup"><span data-stu-id="20bc3-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="20bc3-293">請手動或藉由建立資料庫變更腳本來進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="20bc3-294">使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="20bc3-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="20bc3-295">在本教學課程中，請使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="20bc3-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="20bc3-296">更新 `SeedData` 類別，使其提供新資料行的值。</span><span class="sxs-lookup"><span data-stu-id="20bc3-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="20bc3-297">範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。</span><span class="sxs-lookup"><span data-stu-id="20bc3-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="20bc3-298">請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs) (英文)。</span><span class="sxs-lookup"><span data-stu-id="20bc3-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="20bc3-299">建置方案。</span><span class="sxs-lookup"><span data-stu-id="20bc3-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20bc3-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20bc3-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="20bc3-301">新增評等欄位的移轉</span><span class="sxs-lookup"><span data-stu-id="20bc3-301">Add a migration for the rating field</span></span>

<span data-ttu-id="20bc3-302"> 從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="20bc3-303">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="20bc3-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="20bc3-304">`Add-Migration` 命令會告知架構，以便：</span><span class="sxs-lookup"><span data-stu-id="20bc3-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="20bc3-305">比較 `Movie` 模型與 `Movie` 資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="20bc3-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="20bc3-306">建立程式碼，將資料庫架構遷移至新的模型。</span><span class="sxs-lookup"><span data-stu-id="20bc3-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="20bc3-307">"Rating" 是用來命名移轉檔案的任意名稱。</span><span class="sxs-lookup"><span data-stu-id="20bc3-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="20bc3-308">建議您針對移轉檔案使用有意義的名稱，這更加實用。</span><span class="sxs-lookup"><span data-stu-id="20bc3-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="20bc3-309">`Update-Database` 命令會指示架構將結構描述變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="20bc3-310">如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。</span><span class="sxs-lookup"><span data-stu-id="20bc3-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="20bc3-311">您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="20bc3-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="20bc3-312">另一個選擇是刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="20bc3-313">若要在 SSOX 中刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="20bc3-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="20bc3-314">在 SSOX 中選取資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="20bc3-315">以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="20bc3-316">勾選 [ **關閉現有的連接**]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="20bc3-317">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="20bc3-317">Select **OK**.</span></span>
* <span data-ttu-id="20bc3-318">在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="20bc3-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20bc3-319">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20bc3-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="20bc3-320">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="20bc3-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="20bc3-321">在本教學課程中，您可以盡可能使用 Entity Framework Core 的 *遷移* 功能。</span><span class="sxs-lookup"><span data-stu-id="20bc3-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="20bc3-322">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="20bc3-323">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="20bc3-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="20bc3-324">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="20bc3-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="20bc3-325">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="20bc3-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="20bc3-326">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="20bc3-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="20bc3-327">反之，當結構描述變更時，請您卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="20bc3-328">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="20bc3-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="20bc3-329">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="20bc3-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="20bc3-330">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-330">Creating a new table.</span></span>
>* <span data-ttu-id="20bc3-331">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="20bc3-332">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-332">Dropping the old table.</span></span>
>* <span data-ttu-id="20bc3-333">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="20bc3-333">Renaming the new table.</span></span>
>
><span data-ttu-id="20bc3-334">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="20bc3-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="20bc3-335">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="20bc3-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="20bc3-336">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="20bc3-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="20bc3-337">資料植入</span><span class="sxs-lookup"><span data-stu-id="20bc3-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="20bc3-338">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="20bc3-338">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>

<span data-ttu-id="20bc3-339">刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="20bc3-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="20bc3-340">若要刪除資料庫，請刪除資料庫檔案 (*MvcMovie.db*)。</span><span class="sxs-lookup"><span data-stu-id="20bc3-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="20bc3-341">然後執行 `ef database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="20bc3-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="20bc3-342">執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。</span><span class="sxs-lookup"><span data-stu-id="20bc3-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="20bc3-343">若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="20bc3-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20bc3-344">其他資源</span><span class="sxs-lookup"><span data-stu-id="20bc3-344">Additional resources</span></span>

* [<span data-ttu-id="20bc3-345">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="20bc3-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="20bc3-346">[上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="20bc3-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
