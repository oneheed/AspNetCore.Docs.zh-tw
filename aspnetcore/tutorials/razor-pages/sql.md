---
title: 第4部分：使用資料庫
author: rick-anderson
description: 頁面上教學課程系列的第4部分 Razor 。
ms.author: riande
ms.date: 01/05/2021
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 3133ff7a192f5e3bdec4f6f1e6e6d55f966d7e64
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589760"
---
# <a name="part-4-of-tutorial-series-on-razor-pages"></a>頁面上教學課程系列的第4部分 Razor

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。

`RazorPagesMovieContext` 物件會處理連線到資料庫和將 `Movie` 物件對應至資料庫記錄的工作。 在 *Startup.cs* 的 `ConfigureServices` 方法中，以 [相依性插入](xref:fundamentals/dependency-injection)容器登錄資料庫內容：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

ASP.NET [核心設定](xref:fundamentals/configuration/index) 系統會讀取 `ConnectionString` 金鑰。 針對本機開發，設定會從檔案取得連接字串 *appsettings.json* 。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

產生的連接字串類似下列 JSON：

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

當應用程式部署到測試或實際執行伺服器時，環境變數可以用來將連接字串設定為測試或生產資料庫伺服器。 如需詳細資訊，請參閱 [Configuration](xref:fundamentals/configuration/index)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB 為輕量版的 SQL Server Express 資料庫引擎，鎖定程式開發為其目標。 LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。 LocalDB 資料庫預設會在 `C:\Users\<user>\` 目錄中建立 `*.mdf` 檔案。

<a name="ssox"></a>
1. 從 [檢視] 功能表中，開啟 [SQL Server 物件總管] (SSOX)。

   ![檢視功能表](sql/_static/5/ssox.png)

1. 以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Designer**]：

   ![在 Movie 資料表上開啟的操作功能表](sql/_static/5/design.png)

   ![在設計工具中開啟的 Movie 資料表](sql/_static/dv.png)

   請注意 `ID` 旁的索引鍵圖示。 根據預設，EF 會為主索引鍵建立名為 `ID` 的屬性。

1. 以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Data**：

   ![開啟的電影資料表顯示資料表資料](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) 網站指出：

> SQLite 是獨立、高可靠性、內嵌式、全功能、公用領域的 SQL 資料庫引擎。 SQLite 是世界上最常使用的資料庫引擎。

您可以下載許多協力廠商工具來管理和查看 SQLite 資料庫。 下圖是來自 [SQLite 的資料庫瀏覽器](https://sqlitebrowser.org/)。 如果有慣用的 SQLite 工具，請留下您喜歡哪一點的評論。

![顯示 movie 資料庫之 SQLite 的資料庫瀏覽器](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> 在此教學課程中，會盡可能使用 Entity Framework Core *遷移* 功能。 移轉可更新資料庫結構描述，以符合資料模型中的變更。 不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。 例如，其支援新增資料行，但不支援移除或變更資料行。 如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。 由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。 相反地，當架構變更時，就會卸載並重新建立資料庫。
>
>SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。 重建資料表包含：
>
>* 建立新的資料表。
>* 將資料從舊的資料表複製到新的資料表。
>* 卸除舊的資料表。
>* 重新命名新資料表。
>
>如需詳細資訊，請參閱下列資源：
> * [SQLite EF Core 資料庫提供者限制](/ef/core/providers/sqlite/limitations)
> * [自訂移轉程式碼](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [資料植入](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)

---

## <a name="seed-the-database"></a>植入資料庫

使用下列程式碼，在 *Models* 資料夾中建立名為 `SeedData` 的新類別：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

如果資料庫中有任何電影，則種子初始化運算式會傳回，而且不會新增任何電影。

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>新增種子初始設定式

使用下列程式碼取代 *Program.cs* 的內容：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

在先前的程式碼中， `Main` 方法已經過修改，以執行下列動作：

* 從相依性插入容器中取得資料庫內容執行個體。
* 呼叫 `seedData.Initialize` 方法，並將它傳遞至資料庫內容實例。
* 種子方法完成時處理內容。 [Using 語句](/dotnet/csharp/language-reference/keywords/using-statement)可確保會處置內容。

未執行時，會發生下列例外狀況 `Update-Database` ：

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>測試應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 刪除資料庫中的所有記錄。 使用瀏覽器或[SSOX](xref:tutorials/razor-pages/new-field#ssox)中的刪除連結

1. 藉由呼叫類別中的方法來強制讓應用程式初始化 `Startup` ，以執行種子方法。 若要強制初始化，IIS Express 必須停止並重新啟動。 使用下列任一方法停止並重新啟動 IIS：

   1. 以滑鼠右鍵按一下通知區域中的 IIS Express 系統匣圖示，然後 **選取** [ **結束或停止網站**：

      ![IIS Express 系統匣圖示](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![操作功能表](sql/_static/stopIIS.png)

   1. 如果應用程式是在非偵測模式中執行，請按 <kbd>F5</kbd> 以在「偵測模式」中執行。
   1. 如果應用程式處於 [偵測] 模式，請停止偵錯工具，然後按下 <kbd>F5</kbd>。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

刪除資料庫中的所有記錄，因此會執行種子方法。 停止並啟動應用程式來植入資料庫。

---

應用程式會顯示植入的資料：

![在瀏覽器中開啟的電影應用程式顯示電影資料](sql/_static/5/m55.png)

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一個： scaffold Razor頁面](xref:tutorials/razor-pages/page) 
>  [下一步：更新頁面](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。

`RazorPagesMovieContext` 物件會處理連線到資料庫和將 `Movie` 物件對應至資料庫記錄的工作。 在 *Startup.cs* 的 `ConfigureServices` 方法中，以 [相依性插入](xref:fundamentals/dependency-injection)容器登錄資料庫內容：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

ASP.NET [核心設定](xref:fundamentals/configuration/index) 系統會讀取 `ConnectionString` 金鑰。 針對本機開發，設定會從檔案取得連接字串 *appsettings.json* 。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

產生的連接字串將如下所示：

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

當應用程式部署到測試或實際執行伺服器時，環境變數可以用來將連接字串設定為測試或生產資料庫伺服器。 如需詳細資訊，請參閱[組態](xref:fundamentals/configuration/index)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB 為輕量版的 SQL Server Express 資料庫引擎，鎖定程式開發為其目標。 LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。 LocalDB 資料庫預設會在 `C:\Users\<user>\` 目錄中建立 `*.mdf` 檔案。

<a name="ssox"></a>
* 從 [檢視] 功能表中，開啟 [SQL Server 物件總管] (SSOX)。

  ![檢視功能表](sql/_static/ssox.png)

* 以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Designer**]：

  ![在 Movie 資料表上開啟的操作功能表](sql/_static/design.png)

  ![在設計工具中開啟的 Movie 資料表](sql/_static/dv.png)

請注意 `ID` 旁的索引鍵圖示。 根據預設，EF 會為主索引鍵建立名為 `ID` 的屬性。

* 以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Data**：

  ![開啟的電影資料表顯示資料表資料](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) 網站指出：

> SQLite 是獨立、高可靠性、內嵌式、全功能、公用領域的 SQL 資料庫引擎。 SQLite 是世界上最常使用的資料庫引擎。

您可以下載許多協力廠商工具來管理和查看 SQLite 資料庫。 下圖是來自 [SQLite 的資料庫瀏覽器](https://sqlitebrowser.org/)。 如果有慣用的 SQLite 工具，請留下您喜歡哪一點的評論。

![顯示 movie 資料庫之 SQLite 的資料庫瀏覽器](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> 在此教學課程中，會盡可能使用 Entity Framework Core *遷移* 功能。 移轉可更新資料庫結構描述，以符合資料模型中的變更。 不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。 例如，其支援新增資料行，但不支援移除或變更資料行。 如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。 由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。 相反地，當架構變更時，就會卸載並重新建立資料庫。
>
>SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。 重建資料表包含：
>
>* 建立新的資料表。
>* 將資料從舊的資料表複製到新的資料表。
>* 卸除舊的資料表。
>* 重新命名新資料表。
>
>如需詳細資訊，請參閱下列資源：
> * [SQLite EF Core 資料庫提供者限制](/ef/core/providers/sqlite/limitations)
> * [自訂移轉程式碼](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [資料植入](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)

---

## <a name="seed-the-database"></a>植入資料庫

使用下列程式碼，在 *Models* 資料夾中建立名為 `SeedData` 的新類別：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

如果資料庫中有任何電影，則種子初始化運算式會傳回，而且不會新增任何電影。

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>新增種子初始設定式

使用下列程式碼取代 *Program.cs* 的內容：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

在先前的程式碼中， `Main` 方法已經過修改，以執行下列動作：

* 從相依性插入容器中取得資料庫內容執行個體。
* 呼叫 `seedData.Initialize` 方法，並將它傳遞至資料庫內容實例。
* 種子方法完成時處理內容。 [Using 語句](/dotnet/csharp/language-reference/keywords/using-statement)可確保會處置內容。

未執行時，會發生下列例外狀況 `Update-Database` ：

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>測試應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 刪除資料庫中的所有記錄。 使用瀏覽器或 [SSOX](xref:tutorials/razor-pages/new-field#ssox)中的 [刪除] 連結。
* 藉由呼叫類別中的方法來強制讓應用程式初始化 `Startup` ，以執行種子方法。 若要強制初始化，IIS Express 必須停止並重新啟動。 使用下列任一方法停止並重新啟動 IIS：

  * 以滑鼠右鍵按一下通知區域中的 IIS Express 系統匣圖示，然後點選 [結束] 或 [停止站台]：

    ![IIS Express 系統匣圖示](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![操作功能表](sql/_static/stopIIS.png)

    * 如果應用程式是在非偵測模式中執行，請按 <kbd>F5</kbd> 以在「偵測模式」中執行。
    * 如果應用程式處於 [偵測] 模式，請停止偵錯工具，然後按下 <kbd>F5</kbd>。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

刪除資料庫中的所有記錄，因此會執行種子方法。 停止並啟動應用程式來植入資料庫。

---

應用程式會顯示植入的資料：

![在 Chrome 中開啟的電影應用程式顯示電影資料](sql/_static/m55https.png)

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一個： scaffold Razor頁面](xref:tutorials/razor-pages/page) 
>  [下一步：更新頁面](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。

`RazorPagesMovieContext` 物件會處理連線到資料庫和將 `Movie` 物件對應至資料庫記錄的工作。 在 *Startup.cs* 的 `ConfigureServices` 方法中，以 [相依性插入](xref:fundamentals/dependency-injection)容器登錄資料庫內容：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

如需 `ConfigureServices` 中所用之方法的詳細資訊，請參閱：

* 適用於 `CookiePolicyOptions` 的 [ASP.NET Core 中的 EU 一般資料保護規定 (GDPR) 支援](xref:security/gdpr)。
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET [核心設定](xref:fundamentals/configuration/index) 系統會讀取 `ConnectionString` 金鑰。 針對本機開發，設定會從檔案取得連接字串 *appsettings.json* 。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

產生的連接字串將如下所示：

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

當應用程式部署到測試或實際執行伺服器時，環境變數可以用來將連接字串設定為測試或生產資料庫伺服器。 如需詳細資訊，請參閱[組態](xref:fundamentals/configuration/index)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB 為輕量版的 SQL Server Express 資料庫引擎，鎖定程式開發為其目標。 LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。 LocalDB 資料庫預設會在 `C:/Users/<user/>` 目錄中建立 `*.mdf` 檔案。

<a name="ssox"></a>
* 從 [檢視] 功能表中，開啟 [SQL Server 物件總管] (SSOX)。

  ![檢視功能表](sql/_static/ssox.png)

* 以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Designer**]：

  ![在電影資料表上開啟操作功能表](sql/_static/design.png)

  ![在設計工具中開啟電影資料表](sql/_static/dv.png)

請注意 `ID` 旁的索引鍵圖示。 根據預設，EF 會為主索引鍵建立名為 `ID` 的屬性。

* 以滑鼠右鍵按一下 `Movie` 資料表，然後選取 [ **View Data**：

  ![開啟的電影資料表顯示資料表資料](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>植入資料庫

使用下列程式碼，在 *Models* 資料夾中建立名為 `SeedData` 的新類別：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

如果資料庫中有任何電影，則種子初始化運算式會傳回，而且不會新增任何電影。

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>新增種子初始設定式

使用下列程式碼取代 *Program.cs* 的內容：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

在先前的程式碼中， `Main` 方法已經過修改，以執行下列動作：

* 從相依性插入容器中取得資料庫內容執行個體。
* 呼叫 `seedData.Initialize` 方法，並將它傳遞至資料庫內容實例。
* 種子方法完成時處理內容。 [Using 語句](/dotnet/csharp/language-reference/keywords/using-statement)可確保會處置內容。

生產環境應用程式不會呼叫 `Database.Migrate`。 它會新增至前面的程式碼以免在尚未執行 `Update-Database` 時發生下列例外狀況：

SqlException：無法開啟 Razor 登入所要求的資料庫 "PagesMovieCoNtext-21"。 登入失敗。
使用者 'user name' 登入失敗。

### <a name="test-the-app"></a>測試應用程式

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 刪除資料庫中的所有記錄。 您可以使用瀏覽器或 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 的刪除連結來執行這項操作
* 藉由呼叫類別中的方法來強制讓應用程式初始化 `Startup` ，以執行種子方法。 若要強制初始化，IIS Express 必須停止並重新啟動。 您可以使用下列其中一個方法來執行此工作：

  * 以滑鼠右鍵按一下通知區域中的 IIS Express 系統匣圖示，然後點選 [結束] 或 [停止站台]：

    ![IIS Express 系統匣圖示](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![操作功能表](sql/_static/stopIIS.png)

    * 如果應用程式是在非偵測模式中執行，請按 <kbd>F5</kbd> 以在「偵測模式」中執行。
    * 如果應用程式處於 [偵測] 模式，請停止偵錯工具，然後按下 <kbd>F5</kbd>。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

刪除資料庫中的所有記錄，因此會執行種子方法。 停止並啟動應用程式來植入資料庫。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

刪除資料庫中的所有記錄，因此會執行種子方法。 停止並啟動應用程式來植入資料庫。

---

應用程式會顯示植入的資料：

![在 Chrome 中開啟的電影應用程式顯示電影資料](sql/_static/m55https.png)

接下來的教學課程將會清除資料的呈現。

## <a name="additional-resources"></a>其他資源

* [本教學課程的 YouTube 版本](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [上一個： scaffold Razor頁面](xref:tutorials/razor-pages/page) 
>  [下一步：更新頁面](xref:tutorials/razor-pages/da1)

::: moniker-end