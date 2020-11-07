---
title: 第4部分：使用資料庫
author: rick-anderson
description: '頁面上教學課程系列的第4部分 :::no-loc(Razor)::: 。'
ms.author: riande
ms.date: 09/26/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3e78b5b6dab7145413ae8612bfeb352f328ec86a
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360715"
---
# <a name="part-4-work-with-a-database-and-aspnet-core"></a><span data-ttu-id="98196-103">第4部分：使用資料庫和 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98196-103">Part 4, work with a database and ASP.NET Core</span></span>

<span data-ttu-id="98196-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="98196-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="98196-105">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98196-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="98196-106">`:::no-loc(Razor):::PagesMovieContext` 物件會處理連線到資料庫和將 `Movie` 物件對應至資料庫記錄的工作。</span><span class="sxs-lookup"><span data-stu-id="98196-106">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="98196-107">在 *Startup.cs* 的 `ConfigureServices` 方法中，以 [相依性插入](xref:fundamentals/dependency-injection)容器登錄資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="98196-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-109">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="98196-110">[ASP.NET Core 設定](xref:fundamentals/configuration/index)系統會讀取 `ConnectionString` 金鑰。</span><span class="sxs-lookup"><span data-stu-id="98196-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="98196-111">針對本機開發，設定會從檔案取得連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="98196-111">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98196-113">產生的連接字串將如下所示：</span><span class="sxs-lookup"><span data-stu-id="98196-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-114">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="98196-115">當應用程式部署到測試或實際執行伺服器時，環境變數可以用來將連接字串設定為測試或生產資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="98196-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="98196-116">如需詳細資訊，請參閱 [Configuration](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="98196-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="98196-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="98196-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="98196-119">LocalDB 為輕量版的 SQL Server Express 資料庫引擎，鎖定程式開發為其目標。</span><span class="sxs-lookup"><span data-stu-id="98196-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="98196-120">LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。</span><span class="sxs-lookup"><span data-stu-id="98196-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="98196-121">LocalDB 資料庫預設會在 `C:\Users\<user>\` 目錄中建立 `*.mdf` 檔案。</span><span class="sxs-lookup"><span data-stu-id="98196-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="98196-122">從 [檢視] 功能表中，開啟 [SQL Server 物件總管] (SSOX)。</span><span class="sxs-lookup"><span data-stu-id="98196-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![檢視功能表](sql/_static/5/ssox.png)

1. <span data-ttu-id="98196-124">以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Designer** ]：</span><span class="sxs-lookup"><span data-stu-id="98196-124">Right-click on the `Movie` table and select **View Designer** :</span></span>

   ![在 Movie 資料表上開啟的操作功能表](sql/_static/5/design.png)

   ![在設計工具中開啟的 Movie 資料表](sql/_static/dv.png)

   <span data-ttu-id="98196-127">請注意 `ID` 旁的索引鍵圖示。</span><span class="sxs-lookup"><span data-stu-id="98196-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="98196-128">根據預設，EF 會為主索引鍵建立名為 `ID` 的屬性。</span><span class="sxs-lookup"><span data-stu-id="98196-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="98196-129">以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Data** ：</span><span class="sxs-lookup"><span data-stu-id="98196-129">Right-click on the `Movie` table and select **View Data** :</span></span>

   ![開啟的電影資料表顯示資料表資料](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-131">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="98196-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="98196-132">SQLite</span></span>

<span data-ttu-id="98196-133">[SQLite](https://www.sqlite.org/) 網站指出：</span><span class="sxs-lookup"><span data-stu-id="98196-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="98196-134">SQLite 是獨立、高可靠性、內嵌式、全功能、公用領域的 SQL 資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="98196-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="98196-135">SQLite 是世界上最常使用的資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="98196-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="98196-136">您可以下載許多協力廠商工具來管理和查看 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="98196-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="98196-137">下圖是來自 [SQLite 的資料庫瀏覽器](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="98196-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="98196-138">如果有慣用的 SQLite 工具，請留下您喜歡哪一點的評論。</span><span class="sxs-lookup"><span data-stu-id="98196-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![顯示 movie 資料庫之 SQLite 的資料庫瀏覽器](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="98196-140">在本教學課程中，會盡可能使用 Entity Framework Core 的 *遷移* 功能。</span><span class="sxs-lookup"><span data-stu-id="98196-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="98196-141">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="98196-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="98196-142">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="98196-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="98196-143">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="98196-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="98196-144">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="98196-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="98196-145">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="98196-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="98196-146">相反地，當架構變更時，就會卸載並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="98196-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="98196-147">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="98196-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="98196-148">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="98196-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="98196-149">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="98196-149">Creating a new table.</span></span>
>* <span data-ttu-id="98196-150">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="98196-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="98196-151">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="98196-151">Dropping the old table.</span></span>
>* <span data-ttu-id="98196-152">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="98196-152">Renaming the new table.</span></span>
>
><span data-ttu-id="98196-153">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="98196-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="98196-154">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="98196-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="98196-155">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="98196-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="98196-156">資料植入</span><span class="sxs-lookup"><span data-stu-id="98196-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="98196-157">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="98196-157">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>

---

## <a name="seed-the-database"></a><span data-ttu-id="98196-158">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="98196-158">Seed the database</span></span>

<span data-ttu-id="98196-159">:::no-loc(Create)::: 在 `SeedData` [ *模型* ] 資料夾中，以下列程式碼命名的新類別：</span><span class="sxs-lookup"><span data-stu-id="98196-159">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="98196-160">如果資料庫中有任何電影，則種子初始化運算式會傳回，而且不會新增任何電影。</span><span class="sxs-lookup"><span data-stu-id="98196-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="98196-161">新增種子初始設定式</span><span class="sxs-lookup"><span data-stu-id="98196-161">Add the seed initializer</span></span>

<span data-ttu-id="98196-162">使用下列程式碼取代 *Program.cs* 的內容：</span><span class="sxs-lookup"><span data-stu-id="98196-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Program.cs)]

<span data-ttu-id="98196-163">在先前的程式碼中， `Main` 方法已經過修改，以執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="98196-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="98196-164">從相依性插入容器中取得資料庫內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="98196-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="98196-165">呼叫 `seedData.Initialize` 方法，並將它傳遞至資料庫內容實例。</span><span class="sxs-lookup"><span data-stu-id="98196-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="98196-166">種子方法完成時處理內容。</span><span class="sxs-lookup"><span data-stu-id="98196-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="98196-167">[Using 語句](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)可確保會處置內容。</span><span class="sxs-lookup"><span data-stu-id="98196-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="98196-168">未執行時，會發生下列例外狀況 `Update-Database` ：</span><span class="sxs-lookup"><span data-stu-id="98196-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="98196-169">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="98196-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="98196-171">:::no-loc(Delete)::: 資料庫中的所有記錄。</span><span class="sxs-lookup"><span data-stu-id="98196-171">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="98196-172">使用瀏覽器或[SSOX](xref:tutorials/razor-pages/new-field#ssox)中的刪除連結</span><span class="sxs-lookup"><span data-stu-id="98196-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="98196-173">藉由呼叫類別中的方法來強制讓應用程式初始化 `Startup` ，以執行種子方法。</span><span class="sxs-lookup"><span data-stu-id="98196-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="98196-174">若要強制初始化，IIS Express 必須停止並重新啟動。</span><span class="sxs-lookup"><span data-stu-id="98196-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="98196-175">使用下列任一方法停止並重新啟動 IIS：</span><span class="sxs-lookup"><span data-stu-id="98196-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="98196-176">以滑鼠右鍵按一下通知區域中的 IIS Express 系統匣圖示，然後 **選取** [ **結束或停止網站** ：</span><span class="sxs-lookup"><span data-stu-id="98196-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site** :</span></span>

      ![IIS Express 系統匣圖示](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![操作功能表](sql/_static/stopIIS.png)

   1. <span data-ttu-id="98196-179">如果應用程式是在非偵測模式中執行，請按 <kbd>F5</kbd> 以在「偵測模式」中執行。</span><span class="sxs-lookup"><span data-stu-id="98196-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="98196-180">如果應用程式處於 [偵測] 模式，請停止偵錯工具，然後按下 <kbd>F5</kbd>。</span><span class="sxs-lookup"><span data-stu-id="98196-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-181">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="98196-182">:::no-loc(Delete)::: 資料庫中的所有記錄，因此會執行種子方法。</span><span class="sxs-lookup"><span data-stu-id="98196-182">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="98196-183">停止並啟動應用程式來植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="98196-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="98196-184">應用程式會顯示植入的資料：</span><span class="sxs-lookup"><span data-stu-id="98196-184">The app shows the seeded data:</span></span>

![在瀏覽器中開啟的電影應用程式顯示電影資料](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="98196-186">其他資源</span><span class="sxs-lookup"><span data-stu-id="98196-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="98196-187">[上一個： scaffold :::no-loc(Razor):::頁面](xref:tutorials/razor-pages/page) 
>  [下一步：更新頁面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="98196-187">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="98196-188">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98196-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="98196-189">`:::no-loc(Razor):::PagesMovieContext` 物件會處理連線到資料庫和將 `Movie` 物件對應至資料庫記錄的工作。</span><span class="sxs-lookup"><span data-stu-id="98196-189">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="98196-190">在 *Startup.cs* 的 `ConfigureServices` 方法中，以 [相依性插入](xref:fundamentals/dependency-injection)容器登錄資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="98196-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-192">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="98196-193">[ASP.NET Core 設定](xref:fundamentals/configuration/index)系統會讀取 `ConnectionString` 金鑰。</span><span class="sxs-lookup"><span data-stu-id="98196-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="98196-194">針對本機開發，設定會從檔案取得連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="98196-194">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98196-196">產生的連接字串將如下所示：</span><span class="sxs-lookup"><span data-stu-id="98196-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-197">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="98196-198">當應用程式部署到測試或實際執行伺服器時，環境變數可以用來將連接字串設定為測試或生產資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="98196-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="98196-199">如需詳細資訊，請參閱[組態](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="98196-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="98196-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="98196-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="98196-202">LocalDB 為輕量版的 SQL Server Express 資料庫引擎，鎖定程式開發為其目標。</span><span class="sxs-lookup"><span data-stu-id="98196-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="98196-203">LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。</span><span class="sxs-lookup"><span data-stu-id="98196-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="98196-204">LocalDB 資料庫預設會在 `C:\Users\<user>\` 目錄中建立 `*.mdf` 檔案。</span><span class="sxs-lookup"><span data-stu-id="98196-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="98196-205">從 [檢視] 功能表中，開啟 [SQL Server 物件總管] (SSOX)。</span><span class="sxs-lookup"><span data-stu-id="98196-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![檢視功能表](sql/_static/ssox.png)

* <span data-ttu-id="98196-207">以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Designer** ]：</span><span class="sxs-lookup"><span data-stu-id="98196-207">Right-click on the `Movie` table and select **View Designer** :</span></span>

  ![在 Movie 資料表上開啟的操作功能表](sql/_static/design.png)

  ![在設計工具中開啟的 Movie 資料表](sql/_static/dv.png)

<span data-ttu-id="98196-210">請注意 `ID` 旁的索引鍵圖示。</span><span class="sxs-lookup"><span data-stu-id="98196-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="98196-211">根據預設，EF 會為主索引鍵建立名為 `ID` 的屬性。</span><span class="sxs-lookup"><span data-stu-id="98196-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="98196-212">以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Data** ：</span><span class="sxs-lookup"><span data-stu-id="98196-212">Right-click on the `Movie` table and select **View Data** :</span></span>

  ![開啟的電影資料表顯示資料表資料](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-214">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="98196-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="98196-215">SQLite</span></span>

<span data-ttu-id="98196-216">[SQLite](https://www.sqlite.org/) 網站指出：</span><span class="sxs-lookup"><span data-stu-id="98196-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="98196-217">SQLite 是獨立、高可靠性、內嵌式、全功能、公用領域的 SQL 資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="98196-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="98196-218">SQLite 是世界上最常使用的資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="98196-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="98196-219">您可以下載許多協力廠商工具來管理和查看 SQLite 資料庫。</span><span class="sxs-lookup"><span data-stu-id="98196-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="98196-220">下圖是來自 [SQLite 的資料庫瀏覽器](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="98196-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="98196-221">如果有慣用的 SQLite 工具，請留下您喜歡哪一點的評論。</span><span class="sxs-lookup"><span data-stu-id="98196-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![顯示 movie 資料庫之 SQLite 的資料庫瀏覽器](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="98196-223">在本教學課程中，會盡可能使用 Entity Framework Core 的 *遷移* 功能。</span><span class="sxs-lookup"><span data-stu-id="98196-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="98196-224">移轉可更新資料庫結構描述，以符合資料模型中的變更。</span><span class="sxs-lookup"><span data-stu-id="98196-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="98196-225">不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。</span><span class="sxs-lookup"><span data-stu-id="98196-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="98196-226">例如，其支援新增資料行，但不支援移除或變更資料行。</span><span class="sxs-lookup"><span data-stu-id="98196-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="98196-227">如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。</span><span class="sxs-lookup"><span data-stu-id="98196-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="98196-228">由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。</span><span class="sxs-lookup"><span data-stu-id="98196-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="98196-229">相反地，當架構變更時，就會卸載並重新建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="98196-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="98196-230">SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。</span><span class="sxs-lookup"><span data-stu-id="98196-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="98196-231">重建資料表包含：</span><span class="sxs-lookup"><span data-stu-id="98196-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="98196-232">建立新的資料表。</span><span class="sxs-lookup"><span data-stu-id="98196-232">Creating a new table.</span></span>
>* <span data-ttu-id="98196-233">將資料從舊的資料表複製到新的資料表。</span><span class="sxs-lookup"><span data-stu-id="98196-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="98196-234">卸除舊的資料表。</span><span class="sxs-lookup"><span data-stu-id="98196-234">Dropping the old table.</span></span>
>* <span data-ttu-id="98196-235">重新命名新資料表。</span><span class="sxs-lookup"><span data-stu-id="98196-235">Renaming the new table.</span></span>
>
><span data-ttu-id="98196-236">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="98196-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="98196-237">SQLite EF Core 資料庫提供者限制</span><span class="sxs-lookup"><span data-stu-id="98196-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="98196-238">自訂移轉程式碼</span><span class="sxs-lookup"><span data-stu-id="98196-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="98196-239">資料植入</span><span class="sxs-lookup"><span data-stu-id="98196-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * <span data-ttu-id="98196-240">[SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="98196-240">[SQLite ALTER TABLE statement](https://sqlite.org/lang_altertable.html)</span></span>

---

## <a name="seed-the-database"></a><span data-ttu-id="98196-241">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="98196-241">Seed the database</span></span>

<span data-ttu-id="98196-242">:::no-loc(Create)::: 在 `SeedData` [ *模型* ] 資料夾中，以下列程式碼命名的新類別：</span><span class="sxs-lookup"><span data-stu-id="98196-242">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="98196-243">如果資料庫中有任何電影，則種子初始化運算式會傳回，而且不會新增任何電影。</span><span class="sxs-lookup"><span data-stu-id="98196-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="98196-244">新增種子初始設定式</span><span class="sxs-lookup"><span data-stu-id="98196-244">Add the seed initializer</span></span>

<span data-ttu-id="98196-245">使用下列程式碼取代 *Program.cs* 的內容：</span><span class="sxs-lookup"><span data-stu-id="98196-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Program.cs)]

<span data-ttu-id="98196-246">在先前的程式碼中， `Main` 方法已經過修改，以執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="98196-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="98196-247">從相依性插入容器中取得資料庫內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="98196-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="98196-248">呼叫 `seedData.Initialize` 方法，並將它傳遞至資料庫內容實例。</span><span class="sxs-lookup"><span data-stu-id="98196-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="98196-249">種子方法完成時處理內容。</span><span class="sxs-lookup"><span data-stu-id="98196-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="98196-250">[Using 語句](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)可確保會處置內容。</span><span class="sxs-lookup"><span data-stu-id="98196-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="98196-251">未執行時，會發生下列例外狀況 `Update-Database` ：</span><span class="sxs-lookup"><span data-stu-id="98196-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="98196-252">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="98196-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="98196-254">:::no-loc(Delete)::: 資料庫中的所有記錄。</span><span class="sxs-lookup"><span data-stu-id="98196-254">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="98196-255">使用瀏覽器或 [SSOX](xref:tutorials/razor-pages/new-field#ssox)中的 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="98196-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="98196-256">藉由呼叫類別中的方法來強制讓應用程式初始化 `Startup` ，以執行種子方法。</span><span class="sxs-lookup"><span data-stu-id="98196-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="98196-257">若要強制初始化，IIS Express 必須停止並重新啟動。</span><span class="sxs-lookup"><span data-stu-id="98196-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="98196-258">使用下列任一方法停止並重新啟動 IIS：</span><span class="sxs-lookup"><span data-stu-id="98196-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="98196-259">以滑鼠右鍵按一下通知區域中的 IIS Express 系統匣圖示，然後點選 [結束] 或 [停止站台]：</span><span class="sxs-lookup"><span data-stu-id="98196-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![IIS Express 系統匣圖示](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![操作功能表](sql/_static/stopIIS.png)

    * <span data-ttu-id="98196-262">如果應用程式是在非偵測模式中執行，請按 <kbd>F5</kbd> 以在「偵測模式」中執行。</span><span class="sxs-lookup"><span data-stu-id="98196-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="98196-263">如果應用程式處於 [偵測] 模式，請停止偵錯工具，然後按下 <kbd>F5</kbd>。</span><span class="sxs-lookup"><span data-stu-id="98196-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-264">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="98196-265">:::no-loc(Delete)::: 資料庫中的所有記錄，因此會執行種子方法。</span><span class="sxs-lookup"><span data-stu-id="98196-265">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="98196-266">停止並啟動應用程式來植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="98196-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="98196-267">應用程式會顯示植入的資料：</span><span class="sxs-lookup"><span data-stu-id="98196-267">The app shows the seeded data:</span></span>

![在 Chrome 中開啟的電影應用程式顯示電影資料](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="98196-269">其他資源</span><span class="sxs-lookup"><span data-stu-id="98196-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="98196-270">[上一個： scaffold :::no-loc(Razor):::頁面](xref:tutorials/razor-pages/page) 
>  [下一步：更新頁面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="98196-270">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="98196-271">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98196-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="98196-272">`:::no-loc(Razor):::PagesMovieContext` 物件會處理連線到資料庫和將 `Movie` 物件對應至資料庫記錄的工作。</span><span class="sxs-lookup"><span data-stu-id="98196-272">The `:::no-loc(Razor):::PagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="98196-273">在 *Startup.cs* 的 `ConfigureServices` 方法中，以 [相依性插入](xref:fundamentals/dependency-injection)容器登錄資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="98196-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs* :</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="98196-275">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="98196-276">如需 `ConfigureServices` 中所用之方法的詳細資訊，請參閱：</span><span class="sxs-lookup"><span data-stu-id="98196-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="98196-277">適用於 `:::no-loc(Cookie):::PolicyOptions` 的 [ASP.NET Core 中的 EU 一般資料保護規定 (GDPR) 支援](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="98196-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `:::no-loc(Cookie):::PolicyOptions`.</span></span>
* [<span data-ttu-id="98196-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="98196-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="98196-279">[ASP.NET Core 設定](xref:fundamentals/configuration/index)系統會讀取 `ConnectionString` 金鑰。</span><span class="sxs-lookup"><span data-stu-id="98196-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="98196-280">針對本機開發，設定會從檔案取得連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="98196-280">For local development, configuration gets the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98196-282">產生的連接字串將如下所示：</span><span class="sxs-lookup"><span data-stu-id="98196-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/:::no-loc(appsettings.json):::)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="98196-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98196-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="98196-284">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="98196-285">當應用程式部署到測試或實際執行伺服器時，環境變數可以用來將連接字串設定為測試或生產資料庫伺服器。</span><span class="sxs-lookup"><span data-stu-id="98196-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="98196-286">如需詳細資訊，請參閱[組態](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="98196-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="98196-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="98196-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="98196-289">LocalDB 為輕量版的 SQL Server Express 資料庫引擎，鎖定程式開發為其目標。</span><span class="sxs-lookup"><span data-stu-id="98196-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="98196-290">LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。</span><span class="sxs-lookup"><span data-stu-id="98196-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="98196-291">LocalDB 資料庫預設會在 `C:/Users/<user/>` 目錄中建立 `*.mdf` 檔案。</span><span class="sxs-lookup"><span data-stu-id="98196-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="98196-292">從 [檢視] 功能表中，開啟 [SQL Server 物件總管] (SSOX)。</span><span class="sxs-lookup"><span data-stu-id="98196-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![檢視功能表](sql/_static/ssox.png)

* <span data-ttu-id="98196-294">以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Designer** ]：</span><span class="sxs-lookup"><span data-stu-id="98196-294">Right-click on the `Movie` table and select **View Designer** :</span></span>

  ![在電影資料表上開啟操作功能表](sql/_static/design.png)

  ![在設計工具中開啟電影資料表](sql/_static/dv.png)

<span data-ttu-id="98196-297">請注意 `ID` 旁的索引鍵圖示。</span><span class="sxs-lookup"><span data-stu-id="98196-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="98196-298">根據預設，EF 會為主索引鍵建立名為 `ID` 的屬性。</span><span class="sxs-lookup"><span data-stu-id="98196-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="98196-299">以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Data** ：</span><span class="sxs-lookup"><span data-stu-id="98196-299">Right-click on the `Movie` table and select **View Data** :</span></span>

  ![開啟的電影資料表顯示資料表資料](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="98196-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98196-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="98196-302">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="98196-303">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="98196-303">Seed the database</span></span>

<span data-ttu-id="98196-304">:::no-loc(Create)::: 在 `SeedData` [ *模型* ] 資料夾中，以下列程式碼命名的新類別：</span><span class="sxs-lookup"><span data-stu-id="98196-304">:::no-loc(Create)::: a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="98196-305">如果資料庫中有任何電影，則種子初始化運算式會傳回，而且不會新增任何電影。</span><span class="sxs-lookup"><span data-stu-id="98196-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="98196-306">新增種子初始設定式</span><span class="sxs-lookup"><span data-stu-id="98196-306">Add the seed initializer</span></span>

<span data-ttu-id="98196-307">使用下列程式碼取代 *Program.cs* 的內容：</span><span class="sxs-lookup"><span data-stu-id="98196-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Program.cs)]

<span data-ttu-id="98196-308">在先前的程式碼中， `Main` 方法已經過修改，以執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="98196-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="98196-309">從相依性插入容器中取得資料庫內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="98196-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="98196-310">呼叫 `seedData.Initialize` 方法，並將它傳遞至資料庫內容實例。</span><span class="sxs-lookup"><span data-stu-id="98196-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="98196-311">種子方法完成時處理內容。</span><span class="sxs-lookup"><span data-stu-id="98196-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="98196-312">[Using 語句](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement)可確保會處置內容。</span><span class="sxs-lookup"><span data-stu-id="98196-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="98196-313">生產環境應用程式不會呼叫 `Database.Migrate`。</span><span class="sxs-lookup"><span data-stu-id="98196-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="98196-314">它會新增至前面的程式碼以免在尚未執行 `Update-Database` 時發生下列例外狀況：</span><span class="sxs-lookup"><span data-stu-id="98196-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="98196-315">SqlException：無法開啟 :::no-loc(Razor)::: 登入所要求的資料庫 "PagesMovieCoNtext-21"。</span><span class="sxs-lookup"><span data-stu-id="98196-315">SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="98196-316">登入失敗。</span><span class="sxs-lookup"><span data-stu-id="98196-316">The login failed.</span></span>
<span data-ttu-id="98196-317">使用者 'user name' 登入失敗。</span><span class="sxs-lookup"><span data-stu-id="98196-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="98196-318">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="98196-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98196-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98196-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="98196-320">:::no-loc(Delete)::: 資料庫中的所有記錄。</span><span class="sxs-lookup"><span data-stu-id="98196-320">:::no-loc(Delete)::: all the records in the database.</span></span> <span data-ttu-id="98196-321">您可以使用瀏覽器或 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 的刪除連結來執行這項操作</span><span class="sxs-lookup"><span data-stu-id="98196-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="98196-322">藉由呼叫類別中的方法來強制讓應用程式初始化 `Startup` ，以執行種子方法。</span><span class="sxs-lookup"><span data-stu-id="98196-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="98196-323">若要強制初始化，IIS Express 必須停止並重新啟動。</span><span class="sxs-lookup"><span data-stu-id="98196-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="98196-324">您可以使用下列其中一個方法來執行此工作：</span><span class="sxs-lookup"><span data-stu-id="98196-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="98196-325">以滑鼠右鍵按一下通知區域中的 IIS Express 系統匣圖示，然後點選 [結束] 或 [停止站台]：</span><span class="sxs-lookup"><span data-stu-id="98196-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site** :</span></span>

    ![IIS Express 系統匣圖示](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![操作功能表](sql/_static/stopIIS.png)

    * <span data-ttu-id="98196-328">如果應用程式是在非偵測模式中執行，請按 <kbd>F5</kbd> 以在「偵測模式」中執行。</span><span class="sxs-lookup"><span data-stu-id="98196-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="98196-329">如果應用程式處於 [偵測] 模式，請停止偵錯工具，然後按下 <kbd>F5</kbd>。</span><span class="sxs-lookup"><span data-stu-id="98196-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="98196-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98196-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="98196-331">:::no-loc(Delete)::: 資料庫中的所有記錄，因此會執行種子方法。</span><span class="sxs-lookup"><span data-stu-id="98196-331">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="98196-332">停止並啟動應用程式來植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="98196-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="98196-333">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98196-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="98196-334">:::no-loc(Delete)::: 資料庫中的所有記錄，因此會執行種子方法。</span><span class="sxs-lookup"><span data-stu-id="98196-334">:::no-loc(Delete)::: all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="98196-335">停止並啟動應用程式來植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="98196-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="98196-336">應用程式會顯示植入的資料：</span><span class="sxs-lookup"><span data-stu-id="98196-336">The app shows the seeded data:</span></span>

![在 Chrome 中開啟的電影應用程式顯示電影資料](sql/_static/m55https.png)

<span data-ttu-id="98196-338">接下來的教學課程將會清除資料的呈現。</span><span class="sxs-lookup"><span data-stu-id="98196-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98196-339">其他資源</span><span class="sxs-lookup"><span data-stu-id="98196-339">Additional resources</span></span>

* [<span data-ttu-id="98196-340">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="98196-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="98196-341">[上一個： scaffold :::no-loc(Razor):::頁面](xref:tutorials/razor-pages/page) 
>  [下一步：更新頁面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="98196-341">[Previous: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
