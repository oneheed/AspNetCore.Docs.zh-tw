---
title: 教學課程第5部分：將遷移套用至 Contoso 大學範例
description: Contoso 大學教學課程系列的第5部分。 使用 EF Core 遷移功能來管理 ASP.NET Core MVC 應用程式中的資料模型變更。
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
ms.topic: tutorial
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: aebbc3f29b0356c7993abd83869ab21d3613bf61
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589344"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="aa52c-104">教學課程：第5部分：將遷移套用至 Contoso 大學範例</span><span class="sxs-lookup"><span data-stu-id="aa52c-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="aa52c-105">在本教學課程中，您會先使用 EF Core 移轉功能來管理資料模型變更。</span><span class="sxs-lookup"><span data-stu-id="aa52c-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="aa52c-106">在稍後的教學課程中，您將在變更資料模型時新增更多移轉作業。</span><span class="sxs-lookup"><span data-stu-id="aa52c-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="aa52c-107">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="aa52c-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aa52c-108">了解移轉</span><span class="sxs-lookup"><span data-stu-id="aa52c-108">Learn about migrations</span></span>
> * <span data-ttu-id="aa52c-109">建立初始移轉</span><span class="sxs-lookup"><span data-stu-id="aa52c-109">Create an initial migration</span></span>
> * <span data-ttu-id="aa52c-110">檢查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="aa52c-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="aa52c-111">了解資料模型快照集</span><span class="sxs-lookup"><span data-stu-id="aa52c-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="aa52c-112">套用移轉</span><span class="sxs-lookup"><span data-stu-id="aa52c-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aa52c-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="aa52c-113">Prerequisites</span></span>

* [<span data-ttu-id="aa52c-114">排序、篩選和分頁</span><span class="sxs-lookup"><span data-stu-id="aa52c-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="aa52c-115">關於移轉</span><span class="sxs-lookup"><span data-stu-id="aa52c-115">About migrations</span></span>

<span data-ttu-id="aa52c-116">在開發新的應用程式時，您的資料模型會頻繁變更，而每次模型變更時，模型會與資料庫不同步。</span><span class="sxs-lookup"><span data-stu-id="aa52c-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="aa52c-117">首先，您會設定 Entity Framework 以建立資料庫 (如果尚未存在的話) 以進行本教學課程。</span><span class="sxs-lookup"><span data-stu-id="aa52c-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="aa52c-118">然後在每次變更資料模型 (新增、移除或變更實體類別或變更您的 DbContext 類別) 時，您可以刪除資料庫，EF 即會建立一個相符的新模型，並植入測試資料。</span><span class="sxs-lookup"><span data-stu-id="aa52c-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="aa52c-119">在您將應用程式部署到生產環境之前，都可以使用上述方法讓資料庫與資料模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="aa52c-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="aa52c-120">但當應用程式在生產環境中執行時，通常會儲存您想要保留的資料，而您也不想在每次資料變更 (例如新增資料行) 時遺失任何項目。</span><span class="sxs-lookup"><span data-stu-id="aa52c-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="aa52c-121">為了解決上述問題，EF Core 移轉功能可讓 EF 更新資料庫結構描述，而不是建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="aa52c-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="aa52c-122">若要使用遷移，您可以使用 **套件管理員主控台** (PMC) 或 CLI。</span><span class="sxs-lookup"><span data-stu-id="aa52c-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="aa52c-123">這些教學課程會示範如何使用 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="aa52c-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="aa52c-124">PMC 的資訊則位於[本教學課程結尾](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="aa52c-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="aa52c-125">卸除資料庫</span><span class="sxs-lookup"><span data-stu-id="aa52c-125">Drop the database</span></span>

<span data-ttu-id="aa52c-126">將 EF Core tools 安裝為 [全域工具](/ef/core/miscellaneous/cli/dotnet) ，然後刪除資料庫：</span><span class="sxs-lookup"><span data-stu-id="aa52c-126">Install EF Core tools as a [global tool](/ef/core/miscellaneous/cli/dotnet) and delete the database:</span></span>

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

<span data-ttu-id="aa52c-127">下節說明如何執行 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="aa52c-127">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="aa52c-128">建立初始移轉</span><span class="sxs-lookup"><span data-stu-id="aa52c-128">Create an initial migration</span></span>

<span data-ttu-id="aa52c-129">儲存您的變更，並建置專案。</span><span class="sxs-lookup"><span data-stu-id="aa52c-129">Save your changes and build the project.</span></span> <span data-ttu-id="aa52c-130">接著，開啟命令視窗並巡覽至專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="aa52c-130">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="aa52c-131">以下是執行這個動作的快速方法：</span><span class="sxs-lookup"><span data-stu-id="aa52c-131">Here's a quick way to do that:</span></span>

* <span data-ttu-id="aa52c-132">在 [方案總管] 中，於專案上按一下滑鼠右鍵，然後從操作功能表中選擇 [在檔案總管中開啟資料夾]。</span><span class="sxs-lookup"><span data-stu-id="aa52c-132">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![[在檔案總管中開啟] 功能表項目](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="aa52c-134">在位址列中輸入 "cmd"，然後按 Enter。</span><span class="sxs-lookup"><span data-stu-id="aa52c-134">Enter "cmd" in the address bar and press Enter.</span></span>

  ![開啟命令視窗](migrations/_static/open-command-window.png)

<span data-ttu-id="aa52c-136">在命令視窗中輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="aa52c-136">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="aa52c-137">在上述命令中，會顯示類似下列的輸出：</span><span class="sxs-lookup"><span data-stu-id="aa52c-137">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="aa52c-138">如果您看到錯誤訊息「*無法存取檔案 ... ContosoUniversity.dll，因為另一個進程正在使用* 該檔案。」，請在 Windows 系統匣中尋找 IIS Express 圖示，然後以滑鼠右鍵按一下它，再按一下 [ **ContosoUniversity > 停止網站**]。</span><span class="sxs-lookup"><span data-stu-id="aa52c-138">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="aa52c-139">檢查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="aa52c-139">Examine Up and Down methods</span></span>

<span data-ttu-id="aa52c-140">當您執行 `migrations add` 命令時，EF 產生的程式碼會從頭開始建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="aa52c-140">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="aa52c-141">這段程式碼位於名為 *\<timestamp> _InitialCreate .cs* 的檔案中的 [*遷移*] 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="aa52c-141">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="aa52c-142">`InitialCreate` 類別的 `Up` 方法會建立對應至資料模型實體集的資料庫資料表，而 `Down` 方法則會刪除它們，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="aa52c-142">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="aa52c-143">Migrations 會呼叫 `Up` 方法，以實作移轉所需的資料模型變更。</span><span class="sxs-lookup"><span data-stu-id="aa52c-143">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="aa52c-144">當您輸入命令以復原更新時，Migrations 會呼叫 `Down` 方法。</span><span class="sxs-lookup"><span data-stu-id="aa52c-144">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="aa52c-145">這個程式碼適用於您之前輸入 `migrations add InitialCreate` 命令時所建立的初始移轉。</span><span class="sxs-lookup"><span data-stu-id="aa52c-145">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="aa52c-146">移轉名稱參數 (在此範例中為 "InitialCreate") 可作為檔案名稱，您也可以任意命名。</span><span class="sxs-lookup"><span data-stu-id="aa52c-146">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="aa52c-147">建議您選擇某個單字或片語，以摘要說明移轉中所要完成的作業。</span><span class="sxs-lookup"><span data-stu-id="aa52c-147">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="aa52c-148">例如，您可以將稍後的移轉命名為 "AddDepartmentTable"。</span><span class="sxs-lookup"><span data-stu-id="aa52c-148">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="aa52c-149">如果在您建立初始移轉時資料庫已經存在，系統會產生資料庫建立程式碼，但不需要執行，因為資料庫已經符合資料模型。</span><span class="sxs-lookup"><span data-stu-id="aa52c-149">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="aa52c-150">當您將應用程式部署到資料庫尚未存在的其他環境中時，即會執行這個程式碼以建立您的資料庫；建議您先進行測試。</span><span class="sxs-lookup"><span data-stu-id="aa52c-150">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="aa52c-151">這就是為什麼稍早要您變更連接字串中資料庫名稱的原因，這樣一來，移轉即可從頭建立一個資料庫。</span><span class="sxs-lookup"><span data-stu-id="aa52c-151">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="aa52c-152">資料模型快照集</span><span class="sxs-lookup"><span data-stu-id="aa52c-152">The data model snapshot</span></span>

<span data-ttu-id="aa52c-153">移轉會在 *Migrations/SchoolContextModelSnapshot.cs* 中建立目前資料庫結構描述的「快照集」。</span><span class="sxs-lookup"><span data-stu-id="aa52c-153">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="aa52c-154">當您新增移轉時，EF 會比較資料模型與快照集檔案，以判斷變更的內容。</span><span class="sxs-lookup"><span data-stu-id="aa52c-154">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="aa52c-155">使用 [dotnet ef 遷移移除](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 命令以移除遷移。</span><span class="sxs-lookup"><span data-stu-id="aa52c-155">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="aa52c-156">`dotnet ef migrations remove` 會刪除移轉，並確保正確地重設快照集。</span><span class="sxs-lookup"><span data-stu-id="aa52c-156">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="aa52c-157">如果 `dotnet ef migrations remove` 失敗，請使用 `dotnet ef migrations remove -v` 以取得失敗的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="aa52c-157">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="aa52c-158">如需如何使用快照集檔案的詳細資訊，請參閱[小組環境中的 EF Core 移轉](/ef/core/managing-schemas/migrations/teams)。</span><span class="sxs-lookup"><span data-stu-id="aa52c-158">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="aa52c-159">套用移轉</span><span class="sxs-lookup"><span data-stu-id="aa52c-159">Apply the migration</span></span>

<span data-ttu-id="aa52c-160">在命令視窗中輸入下列命令，以建立資料庫和其中的資料表。</span><span class="sxs-lookup"><span data-stu-id="aa52c-160">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="aa52c-161">此命令的輸出類似於 `migrations add` 命令，不同之處在於其會顯示設定資料庫之 SQL 命令的記錄。</span><span class="sxs-lookup"><span data-stu-id="aa52c-161">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="aa52c-162">下列範例輸出中省略了大部分的記錄。</span><span class="sxs-lookup"><span data-stu-id="aa52c-162">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="aa52c-163">如果您不想看到這麼詳細的記錄訊息，可以變更 *appsettings.Development.json* 檔案中的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="aa52c-163">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="aa52c-164">如需詳細資訊，請參閱<xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="aa52c-164">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

<span data-ttu-id="aa52c-165">按照第一個教學課程的做法，使用 [SQL Server 物件總管] 檢查資料庫。</span><span class="sxs-lookup"><span data-stu-id="aa52c-165">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="aa52c-166">您會發現新增 \_\_EFMigrationsHistory 資料表，其會持續追蹤哪些移轉已套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="aa52c-166">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="aa52c-167">檢視該資料表中的資料，您會看到其中一個資料列代表第一個移轉。</span><span class="sxs-lookup"><span data-stu-id="aa52c-167">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="aa52c-168">(上述 CLI 輸出範例中的最後一則記錄會顯示建立此資料列的 INSERT 陳述式。)</span><span class="sxs-lookup"><span data-stu-id="aa52c-168">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="aa52c-169">執行應用程式，以驗證一切如往常般運作。</span><span class="sxs-lookup"><span data-stu-id="aa52c-169">Run the application to verify that everything still works the same as before.</span></span>

![Students [索引] 頁面](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="aa52c-171">比較 CLI 與 PMC</span><span class="sxs-lookup"><span data-stu-id="aa52c-171">Compare CLI and PMC</span></span>

<span data-ttu-id="aa52c-172">您可以透過 .NET Core CLI 命令或 Visual Studio [套件管理員主控台] (PMC) 視窗中的 PowerShell Cmdlet，取得適用於管理移轉的 EF 工具。</span><span class="sxs-lookup"><span data-stu-id="aa52c-172">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="aa52c-173">本教學課程示範如何使用 CLI，但是您也可以視習慣使用 PMC。</span><span class="sxs-lookup"><span data-stu-id="aa52c-173">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="aa52c-174">適用於 PMC 命令的 EF 命令位於 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 套件中。</span><span class="sxs-lookup"><span data-stu-id="aa52c-174">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="aa52c-175">此套件包含在 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中；因此，如果您的應用程式具有 `Microsoft.AspNetCore.App` 的套件參考，則您不需要新增套件參考。</span><span class="sxs-lookup"><span data-stu-id="aa52c-175">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="aa52c-176">**重要事項：** 此套件與您為 CLI 安裝的套件不同 (透過編輯 *.csproj* 檔案來進行)。</span><span class="sxs-lookup"><span data-stu-id="aa52c-176">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="aa52c-177">這個套件的名稱以 `Tools` 結尾，不同於以 `Tools.DotNet` 結尾的 CLI 套件名稱。</span><span class="sxs-lookup"><span data-stu-id="aa52c-177">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="aa52c-178">如需 CLI 命令的詳細資訊，請參閱 [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="aa52c-178">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="aa52c-179">如需 PMC 命令的詳細資訊，請參閱[套件管理員主控台 (Visual Studio)](/ef/core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="aa52c-179">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="aa52c-180">取得程式碼</span><span class="sxs-lookup"><span data-stu-id="aa52c-180">Get the code</span></span>

[<span data-ttu-id="aa52c-181">下載或檢視已完成的應用程式。</span><span class="sxs-lookup"><span data-stu-id="aa52c-181">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="aa52c-182">後續步驟</span><span class="sxs-lookup"><span data-stu-id="aa52c-182">Next step</span></span>

<span data-ttu-id="aa52c-183">若要開始查看更進階的主題，了解資料模型的擴展，請前往下一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="aa52c-183">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="aa52c-184">在過程中，您會建立並套用其他移轉。</span><span class="sxs-lookup"><span data-stu-id="aa52c-184">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="aa52c-185">建立並套用額外的移轉</span><span class="sxs-lookup"><span data-stu-id="aa52c-185">Create and apply additional migrations</span></span>](complex-data-model.md)
