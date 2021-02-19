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
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101142"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="027e1-103">第7部分： Razor 在頁面中新增欄位，ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="027e1-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="027e1-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="027e1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="027e1-105">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="027e1-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="027e1-106">在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：</span><span class="sxs-lookup"><span data-stu-id="027e1-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="027e1-107">在模型中新增一個欄位。</span><span class="sxs-lookup"><span data-stu-id="027e1-107">Add a new field to the model.</span></span>
* <span data-ttu-id="027e1-108">將新的欄位結構描述變更移轉至資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="027e1-109">使用 EF Code First 自動建立資料庫時，Code First 會：</span><span class="sxs-lookup"><span data-stu-id="027e1-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="027e1-110">將 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。</span><span class="sxs-lookup"><span data-stu-id="027e1-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="027e1-111">如果模型類別未與資料庫同步，EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="027e1-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="027e1-112">架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。</span><span class="sxs-lookup"><span data-stu-id="027e1-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="027e1-113">將 Rating 屬性新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="027e1-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="027e1-114">開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：</span><span class="sxs-lookup"><span data-stu-id="027e1-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="027e1-115">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="027e1-115">Build the app.</span></span>

1. <span data-ttu-id="027e1-116">編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：</span><span class="sxs-lookup"><span data-stu-id="027e1-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="027e1-117">更新下列頁面：</span><span class="sxs-lookup"><span data-stu-id="027e1-117">Update the following pages:</span></span>
   1. <span data-ttu-id="027e1-118">將 `Rating` 欄位新增至 Delete 和 Details 頁面。</span><span class="sxs-lookup"><span data-stu-id="027e1-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="027e1-119">使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="027e1-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="027e1-120">將 `Rating` 欄位新增至 Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="027e1-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="027e1-121">在資料庫更新為包含新欄位之前，應用程式將無法運作。</span><span class="sxs-lookup"><span data-stu-id="027e1-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="027e1-122">在沒有資料庫更新的情況下執行應用程式，會擲回 `SqlException` ：</span><span class="sxs-lookup"><span data-stu-id="027e1-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="027e1-123">`SqlException`例外狀況是因為更新的電影模型類別與資料庫之電影資料表的架構不同而造成。</span><span class="sxs-lookup"><span data-stu-id="027e1-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="027e1-124">`Rating`資料庫資料表中沒有資料行。</span><span class="sxs-lookup"><span data-stu-id="027e1-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="027e1-125">有幾個方法可以解決這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="027e1-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="027e1-126">讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="027e1-127">這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。</span><span class="sxs-lookup"><span data-stu-id="027e1-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="027e1-128">缺點是會遺失在資料庫中的現有資料。</span><span class="sxs-lookup"><span data-stu-id="027e1-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="027e1-129">請勿在生產環境資料庫上使用此方法！</span><span class="sxs-lookup"><span data-stu-id="027e1-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="027e1-130">在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="027e1-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="027e1-131">您可明確修改現有資料庫的結構描述，使其符合模型類別。</span><span class="sxs-lookup"><span data-stu-id="027e1-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="027e1-132">這種方法的優點是保留資料。</span><span class="sxs-lookup"><span data-stu-id="027e1-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="027e1-133">請手動或藉由建立資料庫變更腳本來進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="027e1-134">使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="027e1-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="027e1-135">在本教學課程中，請使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="027e1-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="027e1-136">更新 `SeedData` 類別，使其提供新資料行的值。</span><span class="sxs-lookup"><span data-stu-id="027e1-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="027e1-137">範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。</span><span class="sxs-lookup"><span data-stu-id="027e1-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="027e1-138">請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs) (英文)。</span><span class="sxs-lookup"><span data-stu-id="027e1-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="027e1-139">建置方案。</span><span class="sxs-lookup"><span data-stu-id="027e1-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="027e1-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="027e1-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="027e1-141">新增評等欄位的移轉</span><span class="sxs-lookup"><span data-stu-id="027e1-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="027e1-142"> 從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。</span><span class="sxs-lookup"><span data-stu-id="027e1-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="027e1-143">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="027e1-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="027e1-144">`Add-Migration` 命令會告知架構，以便：</span><span class="sxs-lookup"><span data-stu-id="027e1-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="027e1-145">比較 `Movie` 模型與 `Movie` 資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="027e1-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="027e1-146">建立程式碼，將資料庫架構遷移至新的模型。</span><span class="sxs-lookup"><span data-stu-id="027e1-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="027e1-147">"Rating" 是用來命名移轉檔案的任意名稱。</span><span class="sxs-lookup"><span data-stu-id="027e1-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="027e1-148">建議您針對移轉檔案使用有意義的名稱，這更加實用。</span><span class="sxs-lookup"><span data-stu-id="027e1-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="027e1-149">此 `Update-Database` 命令會指示架構將架構變更套用至資料庫，並保留現有的資料。</span><span class="sxs-lookup"><span data-stu-id="027e1-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="027e1-150">如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。</span><span class="sxs-lookup"><span data-stu-id="027e1-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="027e1-151">您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="027e1-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="027e1-152">另一個選擇是刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="027e1-153">若要在 SSOX 中刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="027e1-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="027e1-154">在 SSOX 中選取資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="027e1-155">以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="027e1-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="027e1-156">勾選 [ **關閉現有的連接**]。</span><span class="sxs-lookup"><span data-stu-id="027e1-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="027e1-157">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="027e1-157">Select **OK**.</span></span>
1. <span data-ttu-id="027e1-158">在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="027e1-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="027e1-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="027e1-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="027e1-160">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="027e1-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="027e1-161">在本教學課程中，您會盡可能使用 Entity Framework Core 的 *遷移* 功能。</span><span class="sxs-lookup"><span data-stu-id="027e1-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="027e1-162">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="027e1-163">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="027e1-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="027e1-164">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="027e1-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="027e1-165">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="027e1-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="027e1-166">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="027e1-167">反之，當結構描述變更時，請您卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="027e1-168">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="027e1-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="027e1-169">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="027e1-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="027e1-170">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-170">Creating a new table.</span></span>
>* <span data-ttu-id="027e1-171">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="027e1-172">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-172">Dropping the old table.</span></span>
>* <span data-ttu-id="027e1-173">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-173">Renaming the new table.</span></span>
>
><span data-ttu-id="027e1-174">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="027e1-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="027e1-175">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="027e1-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="027e1-176">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="027e1-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="027e1-177">資料植入</span><span class="sxs-lookup"><span data-stu-id="027e1-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="027e1-178">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="027e1-178">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>

1. <span data-ttu-id="027e1-179">刪除移轉資料夾。</span><span class="sxs-lookup"><span data-stu-id="027e1-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="027e1-180">使用下列命令重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="027e1-181">執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。</span><span class="sxs-lookup"><span data-stu-id="027e1-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="027e1-182">若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="027e1-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="027e1-183">其他資源</span><span class="sxs-lookup"><span data-stu-id="027e1-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="027e1-184">[上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="027e1-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="027e1-185">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="027e1-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="027e1-186">在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：</span><span class="sxs-lookup"><span data-stu-id="027e1-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="027e1-187">在模型中新增一個欄位。</span><span class="sxs-lookup"><span data-stu-id="027e1-187">Add a new field to the model.</span></span>
* <span data-ttu-id="027e1-188">將新的欄位結構描述變更移轉至資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="027e1-189">使用 EF Code First 自動建立資料庫時，Code First 會：</span><span class="sxs-lookup"><span data-stu-id="027e1-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="027e1-190">將 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。</span><span class="sxs-lookup"><span data-stu-id="027e1-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="027e1-191">如果模型類別未與資料庫同步，EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="027e1-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="027e1-192">架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。</span><span class="sxs-lookup"><span data-stu-id="027e1-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="027e1-193">將 Rating 屬性新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="027e1-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="027e1-194">開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：</span><span class="sxs-lookup"><span data-stu-id="027e1-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="027e1-195">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="027e1-195">Build the app.</span></span>

1. <span data-ttu-id="027e1-196">編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：</span><span class="sxs-lookup"><span data-stu-id="027e1-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="027e1-197">更新下列頁面：</span><span class="sxs-lookup"><span data-stu-id="027e1-197">Update the following pages:</span></span>
   1. <span data-ttu-id="027e1-198">將 `Rating` 欄位新增至 Delete 和 Details 頁面。</span><span class="sxs-lookup"><span data-stu-id="027e1-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="027e1-199">使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="027e1-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="027e1-200">將 `Rating` 欄位新增至 Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="027e1-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="027e1-201">在資料庫更新為包含新欄位之前，應用程式將無法運作。</span><span class="sxs-lookup"><span data-stu-id="027e1-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="027e1-202">在沒有資料庫更新的情況下執行應用程式，會擲回 `SqlException` ：</span><span class="sxs-lookup"><span data-stu-id="027e1-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="027e1-203">`SqlException`例外狀況是因為更新的電影模型類別與資料庫之電影資料表的架構不同而造成。</span><span class="sxs-lookup"><span data-stu-id="027e1-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="027e1-204">`Rating`資料庫資料表中沒有資料行。</span><span class="sxs-lookup"><span data-stu-id="027e1-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="027e1-205">有幾個方法可以解決這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="027e1-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="027e1-206">讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="027e1-207">這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。</span><span class="sxs-lookup"><span data-stu-id="027e1-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="027e1-208">缺點是會遺失在資料庫中的現有資料。</span><span class="sxs-lookup"><span data-stu-id="027e1-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="027e1-209">請勿在生產環境資料庫上使用此方法！</span><span class="sxs-lookup"><span data-stu-id="027e1-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="027e1-210">在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="027e1-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="027e1-211">您可明確修改現有資料庫的結構描述，使其符合模型類別。</span><span class="sxs-lookup"><span data-stu-id="027e1-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="027e1-212">這種方法的優點是保留資料。</span><span class="sxs-lookup"><span data-stu-id="027e1-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="027e1-213">請手動或藉由建立資料庫變更腳本來進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="027e1-214">使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="027e1-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="027e1-215">在本教學課程中，請使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="027e1-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="027e1-216">更新 `SeedData` 類別，使其提供新資料行的值。</span><span class="sxs-lookup"><span data-stu-id="027e1-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="027e1-217">範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。</span><span class="sxs-lookup"><span data-stu-id="027e1-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="027e1-218">請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs) (英文)。</span><span class="sxs-lookup"><span data-stu-id="027e1-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="027e1-219">建置方案。</span><span class="sxs-lookup"><span data-stu-id="027e1-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="027e1-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="027e1-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="027e1-221">新增評等欄位的移轉</span><span class="sxs-lookup"><span data-stu-id="027e1-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="027e1-222"> 從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。</span><span class="sxs-lookup"><span data-stu-id="027e1-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="027e1-223">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="027e1-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="027e1-224">`Add-Migration` 命令會告知架構，以便：</span><span class="sxs-lookup"><span data-stu-id="027e1-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="027e1-225">比較 `Movie` 模型與 `Movie` 資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="027e1-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="027e1-226">建立程式碼，將資料庫架構遷移至新的模型。</span><span class="sxs-lookup"><span data-stu-id="027e1-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="027e1-227">"Rating" 是用來命名移轉檔案的任意名稱。</span><span class="sxs-lookup"><span data-stu-id="027e1-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="027e1-228">建議您針對移轉檔案使用有意義的名稱，這更加實用。</span><span class="sxs-lookup"><span data-stu-id="027e1-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="027e1-229">此 `Update-Database` 命令會指示架構將架構變更套用至資料庫，並保留現有的資料。</span><span class="sxs-lookup"><span data-stu-id="027e1-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="027e1-230">如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。</span><span class="sxs-lookup"><span data-stu-id="027e1-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="027e1-231">您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="027e1-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="027e1-232">另一個選擇是刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="027e1-233">若要在 SSOX 中刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="027e1-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="027e1-234">在 SSOX 中選取資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="027e1-235">以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="027e1-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="027e1-236">勾選 [ **關閉現有的連接**]。</span><span class="sxs-lookup"><span data-stu-id="027e1-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="027e1-237">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="027e1-237">Select **OK**.</span></span>
* <span data-ttu-id="027e1-238">在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="027e1-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="027e1-239">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="027e1-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="027e1-240">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="027e1-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="027e1-241">在本教學課程中，您可以盡可能使用 Entity Framework Core 的 *遷移* 功能。</span><span class="sxs-lookup"><span data-stu-id="027e1-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="027e1-242">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="027e1-243">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="027e1-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="027e1-244">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="027e1-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="027e1-245">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="027e1-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="027e1-246">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="027e1-247">反之，當結構描述變更時，請您卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="027e1-248">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="027e1-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="027e1-249">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="027e1-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="027e1-250">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-250">Creating a new table.</span></span>
>* <span data-ttu-id="027e1-251">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="027e1-252">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-252">Dropping the old table.</span></span>
>* <span data-ttu-id="027e1-253">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-253">Renaming the new table.</span></span>
>
><span data-ttu-id="027e1-254">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="027e1-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="027e1-255">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="027e1-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="027e1-256">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="027e1-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="027e1-257">資料植入</span><span class="sxs-lookup"><span data-stu-id="027e1-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="027e1-258">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="027e1-258">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>

1. <span data-ttu-id="027e1-259">刪除移轉資料夾。</span><span class="sxs-lookup"><span data-stu-id="027e1-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="027e1-260">使用下列命令重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="027e1-261">執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。</span><span class="sxs-lookup"><span data-stu-id="027e1-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="027e1-262">若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="027e1-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="027e1-263">其他資源</span><span class="sxs-lookup"><span data-stu-id="027e1-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="027e1-264">[上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="027e1-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="027e1-265">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="027e1-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="027e1-266">在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：</span><span class="sxs-lookup"><span data-stu-id="027e1-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="027e1-267">在模型中新增一個欄位。</span><span class="sxs-lookup"><span data-stu-id="027e1-267">Add a new field to the model.</span></span>
* <span data-ttu-id="027e1-268">將新的欄位結構描述變更移轉至資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="027e1-269">使用 EF Code First 自動建立資料庫時，Code First 會：</span><span class="sxs-lookup"><span data-stu-id="027e1-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="027e1-270">將 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。</span><span class="sxs-lookup"><span data-stu-id="027e1-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="027e1-271">如果模型類別未與資料庫同步，EF 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="027e1-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="027e1-272">架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。</span><span class="sxs-lookup"><span data-stu-id="027e1-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="027e1-273">將 Rating 屬性新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="027e1-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="027e1-274">開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：</span><span class="sxs-lookup"><span data-stu-id="027e1-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="027e1-275">建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="027e1-275">Build the app.</span></span>

<span data-ttu-id="027e1-276">編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：</span><span class="sxs-lookup"><span data-stu-id="027e1-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="027e1-277">更新下列頁面：</span><span class="sxs-lookup"><span data-stu-id="027e1-277">Update the following pages:</span></span>

* <span data-ttu-id="027e1-278">將 `Rating` 欄位新增至 Delete 和 Details 頁面。</span><span class="sxs-lookup"><span data-stu-id="027e1-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="027e1-279">使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="027e1-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="027e1-280">將 `Rating` 欄位新增至 Edit 頁面。</span><span class="sxs-lookup"><span data-stu-id="027e1-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="027e1-281">在資料庫更新為包含新欄位之前，應用程式將無法運作。</span><span class="sxs-lookup"><span data-stu-id="027e1-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="027e1-282">如果立即執行應用程式，應用程式會擲回 `SqlException` ：</span><span class="sxs-lookup"><span data-stu-id="027e1-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="027e1-283">此錯誤是因為更新的電影模型類別，不同於資料庫之電影資料表的結構描述</span><span class="sxs-lookup"><span data-stu-id="027e1-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="027e1-284">`Rating`資料庫資料表中沒有資料行。</span><span class="sxs-lookup"><span data-stu-id="027e1-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="027e1-285">有幾個方法可以解決這個錯誤：</span><span class="sxs-lookup"><span data-stu-id="027e1-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="027e1-286">讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="027e1-287">這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。</span><span class="sxs-lookup"><span data-stu-id="027e1-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="027e1-288">缺點是會遺失在資料庫中的現有資料。</span><span class="sxs-lookup"><span data-stu-id="027e1-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="027e1-289">請勿在生產環境資料庫上使用此方法！</span><span class="sxs-lookup"><span data-stu-id="027e1-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="027e1-290">在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。</span><span class="sxs-lookup"><span data-stu-id="027e1-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="027e1-291">您可明確修改現有資料庫的結構描述，使其符合模型類別。</span><span class="sxs-lookup"><span data-stu-id="027e1-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="027e1-292">這種方法的優點是保留資料。</span><span class="sxs-lookup"><span data-stu-id="027e1-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="027e1-293">請手動或藉由建立資料庫變更腳本來進行這項變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="027e1-294">使用 Code First 移轉來更新資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="027e1-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="027e1-295">在本教學課程中，請使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="027e1-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="027e1-296">更新 `SeedData` 類別，使其提供新資料行的值。</span><span class="sxs-lookup"><span data-stu-id="027e1-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="027e1-297">範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。</span><span class="sxs-lookup"><span data-stu-id="027e1-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="027e1-298">請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs) (英文)。</span><span class="sxs-lookup"><span data-stu-id="027e1-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="027e1-299">建置方案。</span><span class="sxs-lookup"><span data-stu-id="027e1-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="027e1-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="027e1-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="027e1-301">新增評等欄位的移轉</span><span class="sxs-lookup"><span data-stu-id="027e1-301">Add a migration for the rating field</span></span>

<span data-ttu-id="027e1-302"> 從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。</span><span class="sxs-lookup"><span data-stu-id="027e1-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="027e1-303">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="027e1-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="027e1-304">`Add-Migration` 命令會告知架構，以便：</span><span class="sxs-lookup"><span data-stu-id="027e1-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="027e1-305">比較 `Movie` 模型與 `Movie` 資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="027e1-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="027e1-306">建立程式碼，將資料庫架構遷移至新的模型。</span><span class="sxs-lookup"><span data-stu-id="027e1-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="027e1-307">"Rating" 是用來命名移轉檔案的任意名稱。</span><span class="sxs-lookup"><span data-stu-id="027e1-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="027e1-308">建議您針對移轉檔案使用有意義的名稱，這更加實用。</span><span class="sxs-lookup"><span data-stu-id="027e1-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="027e1-309">`Update-Database` 命令會指示架構將結構描述變更套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="027e1-310">如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。</span><span class="sxs-lookup"><span data-stu-id="027e1-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="027e1-311">您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="027e1-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="027e1-312">另一個選擇是刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="027e1-313">若要在 SSOX 中刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="027e1-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="027e1-314">在 SSOX 中選取資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="027e1-315">以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。</span><span class="sxs-lookup"><span data-stu-id="027e1-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="027e1-316">勾選 [ **關閉現有的連接**]。</span><span class="sxs-lookup"><span data-stu-id="027e1-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="027e1-317">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="027e1-317">Select **OK**.</span></span>
* <span data-ttu-id="027e1-318">在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：</span><span class="sxs-lookup"><span data-stu-id="027e1-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="027e1-319">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="027e1-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="027e1-320">卸除並重新建立資料庫</span><span class="sxs-lookup"><span data-stu-id="027e1-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="027e1-321">在本教學課程中，您可以盡可能使用 Entity Framework Core 的 *遷移* 功能。</span><span class="sxs-lookup"><span data-stu-id="027e1-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="027e1-322">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="027e1-323">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="027e1-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="027e1-324">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="027e1-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="027e1-325">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="027e1-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="027e1-326">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="027e1-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="027e1-327">反之，當結構描述變更時，請您卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="027e1-328">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="027e1-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="027e1-329">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="027e1-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="027e1-330">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-330">Creating a new table.</span></span>
>* <span data-ttu-id="027e1-331">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="027e1-332">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-332">Dropping the old table.</span></span>
>* <span data-ttu-id="027e1-333">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="027e1-333">Renaming the new table.</span></span>
>
><span data-ttu-id="027e1-334">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="027e1-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="027e1-335">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="027e1-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="027e1-336">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="027e1-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="027e1-337">資料植入</span><span class="sxs-lookup"><span data-stu-id="027e1-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="027e1-338">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="027e1-338">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>

<span data-ttu-id="027e1-339">刪除資料庫並使用移轉重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="027e1-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="027e1-340">若要刪除資料庫，請刪除資料庫檔案 (*MvcMovie.db*)。</span><span class="sxs-lookup"><span data-stu-id="027e1-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="027e1-341">然後執行 `ef database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="027e1-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="027e1-342">執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。</span><span class="sxs-lookup"><span data-stu-id="027e1-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="027e1-343">若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="027e1-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="027e1-344">其他資源</span><span class="sxs-lookup"><span data-stu-id="027e1-344">Additional resources</span></span>

* [<span data-ttu-id="027e1-345">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="027e1-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="027e1-346">[上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="027e1-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end