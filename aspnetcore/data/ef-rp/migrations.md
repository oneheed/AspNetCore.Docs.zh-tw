---
title: 第4部分： Razor ASP.NET Core-遷移中有 EF Core 的頁面
author: rick-anderson
description: 頁面的第4部分 Razor 和 Entity Framework 的教學課程系列。
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/migrations
ms.openlocfilehash: 7f1741e06a6e43fd80176113fbe4f249f6eeaa2b
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957495"
---
# <a name="part-4-razor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="44857-103">第4部分： Razor ASP.NET Core 中有 EF Core 遷移的頁面</span><span class="sxs-lookup"><span data-stu-id="44857-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="44857-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="44857-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44857-105">本教學課程介紹用於管理資料模型變更的 EF Core 移轉功能。</span><span class="sxs-lookup"><span data-stu-id="44857-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="44857-106">開發新的應用程式時，資料模型經常變更。</span><span class="sxs-lookup"><span data-stu-id="44857-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="44857-107">每次模型變更時，模型就與資料庫不同步。</span><span class="sxs-lookup"><span data-stu-id="44857-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="44857-108">本教學課程系列從設定 Entity Framework 來建立不存在的資料庫開始。</span><span class="sxs-lookup"><span data-stu-id="44857-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="44857-109">每次資料模型變更時，就必須卸載資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-109">Each time the data model changes, the database needs to be dropped.</span></span> <span data-ttu-id="44857-110">下次執行應用程式時，對 `EnsureCreated` 的呼叫會重新建立資料庫，以符合新的資料模型。</span><span class="sxs-lookup"><span data-stu-id="44857-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="44857-111">然後，`DbInitializer` 類別會執行以植入新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="44857-112">這種讓資料庫與資料模型保持同步的方法，在應用程式需要部署到生產環境之前，都能順利運作。</span><span class="sxs-lookup"><span data-stu-id="44857-112">This approach to keeping the DB in sync with the data model works well until the app needs to be deployed to production.</span></span> <span data-ttu-id="44857-113">當應用程式在生產環境中執行時，它通常會儲存需要維護的資料。</span><span class="sxs-lookup"><span data-stu-id="44857-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="44857-114">應用程式不能每次進行變更 (例如新增資料行) 時，都從測試資料庫開始。</span><span class="sxs-lookup"><span data-stu-id="44857-114">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="44857-115">EF Core 的遷移功能可解決此問題，方法是讓 EF Core 更新資料庫架構，而不是建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new database.</span></span>

<span data-ttu-id="44857-116">移轉會更新結構描述並保留現有的資料，而不是在資料模型變更時卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="44857-117">卸除資料庫</span><span class="sxs-lookup"><span data-stu-id="44857-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44857-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44857-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44857-119">使用 **SQL Server 物件總管** (SSOX) 刪除資料庫，或在 **套件管理員主控台** (PMC) 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="44857-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="44857-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44857-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="44857-121">在命令提示字元中執行下列命令，以安裝 EF CLI：</span><span class="sxs-lookup"><span data-stu-id="44857-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="44857-122">在命令提示字元中，巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="44857-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="44857-123">專案資料夾包含 *ContosoUniversity.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="44857-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="44857-124">刪除 *CU.db* 檔案，或執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="44857-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="44857-125">建立初始移轉</span><span class="sxs-lookup"><span data-stu-id="44857-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44857-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44857-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44857-127">在 PMC 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="44857-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
 
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="44857-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44857-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="44857-129">請確認命令提示字元位於專案資料夾中，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="44857-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
 
```

---

### <a name="remove-ensurecreated"></a><span data-ttu-id="44857-130">移除 EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="44857-130">Remove EnsureCreated</span></span>

<span data-ttu-id="44857-131">本教學課程系列使用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 來啟動。</span><span class="sxs-lookup"><span data-stu-id="44857-131">This tutorial series started by using [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) .</span></span> <span data-ttu-id="44857-132">`EnsureCreated` 不會建立移轉記錄資料表，因此無法用於移轉。</span><span class="sxs-lookup"><span data-stu-id="44857-132">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="44857-133">其設計目的是用來測試或快速原型化經常卸除並重新建立資料庫的位置。</span><span class="sxs-lookup"><span data-stu-id="44857-133">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="44857-134">從這裡開始，教學課程會使用移轉。</span><span class="sxs-lookup"><span data-stu-id="44857-134">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="44857-135">在 *Program .cs* 中，刪除下列這一行：</span><span class="sxs-lookup"><span data-stu-id="44857-135">In *Program.cs*, delete the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="44857-136">執行應用程式，並確認資料庫已植入。</span><span class="sxs-lookup"><span data-stu-id="44857-136">Run the app and verify that the database is seeded.</span></span>

## <a name="up-and-down-methods"></a><span data-ttu-id="44857-137">Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="44857-137">Up and Down methods</span></span>

<span data-ttu-id="44857-138">EF Core `migrations add` 命令已產生用來建立資料庫的程式碼。</span><span class="sxs-lookup"><span data-stu-id="44857-138">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="44857-139">此遷移程式碼位於 *遷移 \<timestamp> _InitialCreate .cs* 檔案中。</span><span class="sxs-lookup"><span data-stu-id="44857-139">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="44857-140">`InitialCreate` 類別的 `Up` 方法會建立對應至資料模型實體集的資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="44857-140">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="44857-141">`Down` 方法則會刪除它們，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="44857-141">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="44857-142">上述程式碼適用於初始移轉。</span><span class="sxs-lookup"><span data-stu-id="44857-142">The preceding code is for the initial migration.</span></span> <span data-ttu-id="44857-143">程式碼：</span><span class="sxs-lookup"><span data-stu-id="44857-143">The code:</span></span>

* <span data-ttu-id="44857-144">由 `migrations add InitialCreate` 命令所產生。</span><span class="sxs-lookup"><span data-stu-id="44857-144">Was generated by the `migrations add InitialCreate` command.</span></span>
* <span data-ttu-id="44857-145">由 `database update` 命令執行。</span><span class="sxs-lookup"><span data-stu-id="44857-145">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="44857-146">會為資料庫內容類別所指定的資料模型建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-146">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="44857-147">範例) 中 (的遷移名稱參數 `InitialCreate` 會用於檔案名。</span><span class="sxs-lookup"><span data-stu-id="44857-147">The migration name parameter (`InitialCreate` in the example) is used for the file name.</span></span> <span data-ttu-id="44857-148">移轉名稱可以是任何有效的檔案名稱。</span><span class="sxs-lookup"><span data-stu-id="44857-148">The migration name can be any valid file name.</span></span> <span data-ttu-id="44857-149">建議您選擇某個單字或片語，以摘要說明移轉中所要完成的作業。</span><span class="sxs-lookup"><span data-stu-id="44857-149">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="44857-150">例如，新增了部門資料表的移轉可能稱為 "AddDepartmentTable"。</span><span class="sxs-lookup"><span data-stu-id="44857-150">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="44857-151">移轉記錄資料表</span><span class="sxs-lookup"><span data-stu-id="44857-151">The migrations history table</span></span>

* <span data-ttu-id="44857-152">使用 SSOX 或 SQLite 工具來檢查資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-152">Use SSOX or SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="44857-153">請注意已新增 `__EFMigrationsHistory` 資料表。</span><span class="sxs-lookup"><span data-stu-id="44857-153">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="44857-154">`__EFMigrationsHistory` 資料表會追蹤哪些移轉已套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-154">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="44857-155">檢視 `__EFMigrationsHistory` 資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="44857-155">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="44857-156">第一次移轉時會顯示一個資料列。</span><span class="sxs-lookup"><span data-stu-id="44857-156">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="44857-157">資料模型快照集</span><span class="sxs-lookup"><span data-stu-id="44857-157">The data model snapshot</span></span>

<span data-ttu-id="44857-158">移轉會在 *Migrations/SchoolContextModelSnapshot.cs* 中建立目前資料模型的「快照集」。</span><span class="sxs-lookup"><span data-stu-id="44857-158">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="44857-159">加入 [新增] 時，EF 會比較目前的資料模型與快照集檔案，以判斷變更了哪些內容。</span><span class="sxs-lookup"><span data-stu-id="44857-159">When add a migration is added, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="44857-160">因為快照集檔案會追蹤資料模型的狀態，所以無法藉由刪除檔案來刪除遷移 `<timestamp>_<migrationname>.cs` 。</span><span class="sxs-lookup"><span data-stu-id="44857-160">Because the snapshot file tracks the state of the data model, a migration cannot be deleted by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="44857-161">若要備份最近的遷移，請使用 [`migrations remove`](/ef/core/managing-schemas/migrations/managing#remove-a-migration) 命令。</span><span class="sxs-lookup"><span data-stu-id="44857-161">To back out the most recent migration, use the [`migrations remove`](/ef/core/managing-schemas/migrations/managing#remove-a-migration) command.</span></span> <span data-ttu-id="44857-162">`migrations remove` 會刪除移轉，並確保正確地重設快照集。</span><span class="sxs-lookup"><span data-stu-id="44857-162">`migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="44857-163">如需詳細資訊，請參閱 [dotnet ef 遷移移除](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)。</span><span class="sxs-lookup"><span data-stu-id="44857-163">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

<span data-ttu-id="44857-164">請參閱 [重設所有遷移 ](/ef/core/miscellaneous/cli/dotnet#resetting-all-migrations) 以移除所有遷移</span><span class="sxs-lookup"><span data-stu-id="44857-164">See [Resetting all migrations ](/ef/core/miscellaneous/cli/dotnet#resetting-all-migrations) to remove all migrations</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="44857-165">在生產環境中套用移轉</span><span class="sxs-lookup"><span data-stu-id="44857-165">Applying migrations in production</span></span>

<span data-ttu-id="44857-166">建議在應用程式啟動時，生產環境應用程式 **不應該** 呼叫 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)。</span><span class="sxs-lookup"><span data-stu-id="44857-166">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="44857-167">不應該從部署至伺服器陣列的應用程式呼叫 `Migrate`。</span><span class="sxs-lookup"><span data-stu-id="44857-167">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="44857-168">如果應用程式相應放大至多個伺服器執行個體，則很難確保不從多部伺服器進行資料庫結構描述更新，或與讀取/寫入存取發生衝突。</span><span class="sxs-lookup"><span data-stu-id="44857-168">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="44857-169">資料庫移轉應該在部署中以受控制的方式完成。</span><span class="sxs-lookup"><span data-stu-id="44857-169">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="44857-170">生產環境資料庫移轉方法包括：</span><span class="sxs-lookup"><span data-stu-id="44857-170">Production database migration approaches include:</span></span>

* <span data-ttu-id="44857-171">使用移轉來建立 SQL 指令碼，並在部署中使用 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="44857-171">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="44857-172">從受控制的環境中執行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="44857-172">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="44857-173">疑難排解</span><span class="sxs-lookup"><span data-stu-id="44857-173">Troubleshooting</span></span>

<span data-ttu-id="44857-174">如果應用程式使用 SQL Server LocalDB 並顯示下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="44857-174">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="44857-175">可能的解決方案是在命令提示字元下執行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="44857-175">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="44857-176">其他資源</span><span class="sxs-lookup"><span data-stu-id="44857-176">Additional resources</span></span>

* <span data-ttu-id="44857-177">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="44857-177">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="44857-178">dotnet ef 遷移 CLI 命令</span><span class="sxs-lookup"><span data-stu-id="44857-178">dotnet ef migrations CLI commands</span></span>](/ef/core/miscellaneous/cli/dotnet)
* [<span data-ttu-id="44857-179">套件管理員主控台 (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="44857-179">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="44857-180">下一步</span><span class="sxs-lookup"><span data-stu-id="44857-180">Next steps</span></span>

<span data-ttu-id="44857-181">下一個教學課程會建立資料模型，並新增實體屬性和新實體。</span><span class="sxs-lookup"><span data-stu-id="44857-181">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="44857-182">[上一個教學](xref:data/ef-rp/sort-filter-page) 
>  課程[下一個教學](xref:data/ef-rp/complex-data-model)課程</span><span class="sxs-lookup"><span data-stu-id="44857-182">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="44857-183">在本教學課程中，會使用 EF Core 移轉功能來管理資料模型變更。</span><span class="sxs-lookup"><span data-stu-id="44857-183">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="44857-184">若您遇到無法解決的問題，請下載[完整應用程式](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="44857-184">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="44857-185">開發新的應用程式時，資料模型經常變更。</span><span class="sxs-lookup"><span data-stu-id="44857-185">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="44857-186">每次模型變更時，模型就與資料庫不同步。</span><span class="sxs-lookup"><span data-stu-id="44857-186">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="44857-187">本教學課程從設定 Entity Framework 來建立不存在的資料庫開始。</span><span class="sxs-lookup"><span data-stu-id="44857-187">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="44857-188">每次資料模型變更時：</span><span class="sxs-lookup"><span data-stu-id="44857-188">Each time the data model changes:</span></span>

* <span data-ttu-id="44857-189">會卸除資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-189">The DB is dropped.</span></span>
* <span data-ttu-id="44857-190">EF 會建立一個符合模型的新資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-190">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="44857-191">應用程式會將測試資料植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-191">The app seeds the DB with test data.</span></span>

<span data-ttu-id="44857-192">這種讓資料庫與資料模型保持同步的方法，在應用程式需要部署到生產環境之前，都能順利運作。</span><span class="sxs-lookup"><span data-stu-id="44857-192">This approach to keeping the DB in sync with the data model works well until the app needs to be deployed to production.</span></span> <span data-ttu-id="44857-193">當應用程式在生產環境中執行時，它通常會儲存需要維護的資料。</span><span class="sxs-lookup"><span data-stu-id="44857-193">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="44857-194">應用程式不能每次進行變更 (例如新增資料行) 時，都從測試資料庫開始。</span><span class="sxs-lookup"><span data-stu-id="44857-194">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="44857-195">EF Core 移轉功能可解決這個問題，方法是啟用 EF Core 以更新資料庫結構描述，來代替建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-195">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="44857-196">移轉會更新結構描述，並保留現有的資料，而不是在資料模型變更時，卸除並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-196">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="44857-197">卸除資料庫</span><span class="sxs-lookup"><span data-stu-id="44857-197">Drop the database</span></span>

<span data-ttu-id="44857-198">使用 [SQL Server 物件總管] (SSOX) 或 `database drop` 命令：</span><span class="sxs-lookup"><span data-stu-id="44857-198">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44857-199">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44857-199">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44857-200">在 [套件管理員主控台] (PMC) 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="44857-200">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="44857-201">從 PMC 執行 `Get-Help about_EntityFrameworkCore` 以取得說明資訊。</span><span class="sxs-lookup"><span data-stu-id="44857-201">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="44857-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44857-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="44857-203">開啟命令視窗並巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="44857-203">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="44857-204">專案資料夾中包含 *Startup.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="44857-204">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="44857-205">在命令視窗中輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="44857-205">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="44857-206">建立初始移轉並更新資料庫</span><span class="sxs-lookup"><span data-stu-id="44857-206">Create an initial migration and update the DB</span></span>

<span data-ttu-id="44857-207">建置專案並建立第一個移轉。</span><span class="sxs-lookup"><span data-stu-id="44857-207">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44857-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44857-208">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="44857-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44857-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="44857-210">檢查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="44857-210">Examine the Up and Down methods</span></span>

<span data-ttu-id="44857-211">EF Core 命令 `migrations add` 已產生用來建立資料庫的程式碼。</span><span class="sxs-lookup"><span data-stu-id="44857-211">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="44857-212">此遷移程式碼位於 *遷移 \<timestamp> _InitialCreate .cs* 檔案中。</span><span class="sxs-lookup"><span data-stu-id="44857-212">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="44857-213">`InitialCreate` 類別的 `Up` 方法會建立對應至資料模型實體集的資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="44857-213">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="44857-214">`Down` 方法則會刪除它們，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="44857-214">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="44857-215">Migrations 會呼叫 `Up` 方法，以實作移轉所需的資料模型變更。</span><span class="sxs-lookup"><span data-stu-id="44857-215">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="44857-216">當您輸入命令以復原更新時，遷移會呼叫 `Down` 方法。</span><span class="sxs-lookup"><span data-stu-id="44857-216">When a command is entered to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="44857-217">上述程式碼適用於初始移轉。</span><span class="sxs-lookup"><span data-stu-id="44857-217">The preceding code is for the initial migration.</span></span> <span data-ttu-id="44857-218">該程式碼是在執行 `migrations add InitialCreate` 命令時建立。</span><span class="sxs-lookup"><span data-stu-id="44857-218">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="44857-219">移轉名稱參數 (在範例中為 "InitialCreate") 用於檔案名稱。</span><span class="sxs-lookup"><span data-stu-id="44857-219">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="44857-220">移轉名稱可以是任何有效的檔案名稱。</span><span class="sxs-lookup"><span data-stu-id="44857-220">The migration name can be any valid file name.</span></span> <span data-ttu-id="44857-221">建議您選擇某個單字或片語，以摘要說明移轉中所要完成的作業。</span><span class="sxs-lookup"><span data-stu-id="44857-221">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="44857-222">例如，新增了部門資料表的移轉可能稱為 "AddDepartmentTable"。</span><span class="sxs-lookup"><span data-stu-id="44857-222">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="44857-223">如果已建立初始移轉並結束資料庫，則：</span><span class="sxs-lookup"><span data-stu-id="44857-223">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="44857-224">會產生資料庫建立程式碼。</span><span class="sxs-lookup"><span data-stu-id="44857-224">The DB creation code is generated.</span></span>
* <span data-ttu-id="44857-225">不需要執行資料庫建立程式碼，因為資料庫已符合資料模型。</span><span class="sxs-lookup"><span data-stu-id="44857-225">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="44857-226">如果執行了資料庫建立程式碼，它不會進行任何變更，因為資料庫已符合資料模型。</span><span class="sxs-lookup"><span data-stu-id="44857-226">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="44857-227">當應用程式部署到新的環境時，必須執行資料庫建立程式碼來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-227">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="44857-228">先前資料庫已卸除，並不存在，所以移轉會建立新資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-228">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="44857-229">資料模型快照集</span><span class="sxs-lookup"><span data-stu-id="44857-229">The data model snapshot</span></span>

<span data-ttu-id="44857-230">移轉會在 *Migrations/SchoolContextModelSnapshot.cs* 中建立目前資料庫結構描述的「快照」。</span><span class="sxs-lookup"><span data-stu-id="44857-230">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="44857-231">當您新增移轉時，EF 會比較資料模型與快照集檔案，以判斷變更的內容。</span><span class="sxs-lookup"><span data-stu-id="44857-231">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="44857-232">若要刪除移轉，請使用下列命令：</span><span class="sxs-lookup"><span data-stu-id="44857-232">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="44857-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44857-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="44857-234">移除移轉</span><span class="sxs-lookup"><span data-stu-id="44857-234">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="44857-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44857-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="44857-236">如需詳細資訊，請參閱 [dotnet ef 遷移移除](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)。</span><span class="sxs-lookup"><span data-stu-id="44857-236">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="44857-237">remove migrations 命令會刪除移轉，並確保正確地重設快照集。</span><span class="sxs-lookup"><span data-stu-id="44857-237">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="44857-238">移除 EnsureCreated 並測試應用程式</span><span class="sxs-lookup"><span data-stu-id="44857-238">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="44857-239">早期開發會使用 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="44857-239">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="44857-240">在本教學課程中，則使用移轉。</span><span class="sxs-lookup"><span data-stu-id="44857-240">In this tutorial, migrations are used.</span></span> <span data-ttu-id="44857-241">`EnsureCreated` 具有下列限制：</span><span class="sxs-lookup"><span data-stu-id="44857-241">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="44857-242">略過移轉，並建立資料庫和結構描述。</span><span class="sxs-lookup"><span data-stu-id="44857-242">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="44857-243">不會建立移轉資料表。</span><span class="sxs-lookup"><span data-stu-id="44857-243">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="44857-244">「無法」與移轉搭配使用。</span><span class="sxs-lookup"><span data-stu-id="44857-244">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="44857-245">設計用來測試或快速原型化經常卸除並重新建立資料庫的位置。</span><span class="sxs-lookup"><span data-stu-id="44857-245">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="44857-246">移除 `EnsureCreated`：</span><span class="sxs-lookup"><span data-stu-id="44857-246">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="44857-247">執行應用程式，並確認植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-247">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="44857-248">檢查資料庫</span><span class="sxs-lookup"><span data-stu-id="44857-248">Inspect the database</span></span>

<span data-ttu-id="44857-249">使用 **SQL Server 物件總管** 來檢查資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-249">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="44857-250">請注意已新增 `__EFMigrationsHistory` 資料表。</span><span class="sxs-lookup"><span data-stu-id="44857-250">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="44857-251">`__EFMigrationsHistory` 資料表會追蹤哪些移轉經套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="44857-251">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="44857-252">檢視 `__EFMigrationsHistory` 資料表中的資料，它會顯示第一個移轉的某個資料列。</span><span class="sxs-lookup"><span data-stu-id="44857-252">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="44857-253">上述 CLI 輸出範例中的最後一則記錄會顯示建立此資料列的 INSERT 陳述式。</span><span class="sxs-lookup"><span data-stu-id="44857-253">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="44857-254">執行應用程式，並確認一切運作正常。</span><span class="sxs-lookup"><span data-stu-id="44857-254">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="44857-255">在生產環境中套用移轉</span><span class="sxs-lookup"><span data-stu-id="44857-255">Applying migrations in production</span></span>

<span data-ttu-id="44857-256">建議在應用程式啟動時，生產環境應用程式 **不** 應該呼叫 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)。</span><span class="sxs-lookup"><span data-stu-id="44857-256">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="44857-257">`Migrate` 不應該從伺服器陣列中的應用程式進行呼叫。</span><span class="sxs-lookup"><span data-stu-id="44857-257">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="44857-258">例如，如果應用程式已使用向外延展 (執行應用程式的多個執行個體) 進行雲端部署。</span><span class="sxs-lookup"><span data-stu-id="44857-258">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="44857-259">資料庫移轉應該在部署中以受控制的方式完成。</span><span class="sxs-lookup"><span data-stu-id="44857-259">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="44857-260">生產環境資料庫移轉方法包括：</span><span class="sxs-lookup"><span data-stu-id="44857-260">Production database migration approaches include:</span></span>

* <span data-ttu-id="44857-261">使用移轉來建立 SQL 指令碼，並在部署中使用 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="44857-261">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="44857-262">從受控制的環境中執行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="44857-262">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="44857-263">EF Core 使用 `__MigrationsHistory` 資料表來查看是否有任何需要執行的移轉。</span><span class="sxs-lookup"><span data-stu-id="44857-263">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="44857-264">如果資料庫為最新狀態，則不會執行移轉。</span><span class="sxs-lookup"><span data-stu-id="44857-264">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="44857-265">疑難排解</span><span class="sxs-lookup"><span data-stu-id="44857-265">Troubleshooting</span></span>

<span data-ttu-id="44857-266">下載[完整應用程式](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)。</span><span class="sxs-lookup"><span data-stu-id="44857-266">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="44857-267">應用程式會產生下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="44857-267">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="44857-268">解決方案：執行 `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="44857-268">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="44857-269">其他資源</span><span class="sxs-lookup"><span data-stu-id="44857-269">Additional resources</span></span>

* [<span data-ttu-id="44857-270">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="44857-270">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="44857-271">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="44857-271">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="44857-272">套件管理員主控台 (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="44857-272">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="44857-273">[上一個](xref:data/ef-rp/sort-filter-page) 
> [下一步](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="44857-273">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end
