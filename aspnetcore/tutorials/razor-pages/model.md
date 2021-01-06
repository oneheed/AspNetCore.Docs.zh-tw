---
title: 第2部分：加入模型
author: rick-anderson
description: 頁面上教學課程系列的第2部分 Razor 。 在本節中，會加入模型類別。
ms.author: riande
ms.date: 11/11/2020
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
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485988"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>第2部分：在 ASP.NET Core 中將模型新增至 Razor 頁面應用程式

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

在本節中，您可以新增類別來管理資料庫中的電影。 應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。 EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。 您會先撰寫模型類別，EF Core 建立資料庫。

模型類別稱為 POCO 類別 (自 "**P**>lain-**O** ld **C** LR **O** bjects" ) ，因為它們沒有 EF Core 的相依性。 它們會定義資料儲存在資料庫中的屬性。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。

## <a name="add-a-data-model"></a>新增資料模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 在 **方案總管** 中，以滑鼠右鍵按一下 *Razor PagesMovie* 專案 **，> 新增**  >  **資料夾**]。 將資料夾命名為 *Models*。
1. 以滑鼠右鍵按一下 [ *模型* ] 資料夾。 選取 [**新增**  >  **類別**]。 將類別命名為 *Movie*。
1. 將下列屬性新增至 `Movie` 類別：

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 新增名為 *Models* 的資料夾。
1. 將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。

將下列屬性新增至 `Movie` 類別：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>新增 NuGet 套件和 EF 工具

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>新增資料庫內容類別

1. 在 *Razor PagesMovie* 專案中，建立一個名為 *Data* 的資料夾。
1. 在 [*資料*] 資料夾中，使用下列程式碼加入名為 *Razor PagesMovieCoNtext.cs* 的檔案：

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   上述程式碼會建立實體集的 `DbSet` 屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。 在稍後的步驟中新增相依性之前，程式碼不會進行編譯。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>新增資料庫連線字串

將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>登錄資料庫內容

1. 在 *Startup.cs* 最上方新增下列 `using` 陳述式：

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. 使用中的相依性 [插入](xref:fundamentals/dependency-injection) 容器來註冊資料庫內容 `Startup.ConfigureServices` ：

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在 [**方案] 工具視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後選取 [**加入** > **新資料夾**...]。命名資料夾 *模型*。
1. 以控制項按一下 [ *模型* ] 資料夾， **然後選取 [** > **新增檔案 ...**]。
1. 在 [新增檔案] 對話方塊中：
   1. 在左窗格中選取 [一般]。
   1. 在中央窗格中選取 [類別是空的]。
   1. 將類別命名為 **Movie**，然後選取 [新增]。

1. 將下列屬性新增至 `Movie` 類別：

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

---

稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。

建置專案，以確認沒有任何編譯錯誤。

## <a name="scaffold-the-movie-model"></a>Scaffold 影片模型

在本節中會 scaffold 影片模型。 亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 建立 *Pages/Movies* 資料夾：
   1. 在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。
   1. 將資料夾命名為 *電影*。

1. 在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。

   ![前述指示中的圖片。](model/_static/5/sca.png)

1. 在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > ****。

   ![前述指示中的圖片。](model/_static/add_scaffold.png)

1. **Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：
   1. 在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。
   1. 在 [資料內容類別] 資料列中，選取 **+** (加號)。
      1. 在 [**加入資料內容**] 對話方塊中，類別名稱 *Razor PagesMovie。 Razor產生 PagesMovieCoNtext* 。
   1. 選取 [新增]。

   ![前述指示中的圖片。](model/_static/3/arp.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 開啟包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案的命令 shell 至專案目錄。

* 若 **為 Windows**：請執行下列命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **針對 macOS 與 Linux**：執行下列命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> 下表詳細說明 ASP.NET Core 程式碼產生器選項。

| 選項               | 描述|
| ----------------- | ------------ |
| `-m`  | 模型的名稱。 |
| `-dc`  | 要使用的 `DbContext` 類別。 |
| `-udl` | 使用預設的配置。 |
| `-outDir` | 要建立檢視的相對輸出資料夾路徑。 |
| `--referenceScriptLibraries` | 將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面 |

使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。

### <a name="use-sqlite-for-development-sql-server-for-production"></a>使用 SQLite 進行開發，SQL Server 生產環境

選取 SQLite 時，範本產生的程式碼就可以開始開發。 下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。 `IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 建立 *Pages/Movies* 資料夾：
   1. 在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾**]。
   1. 將資料夾命名為 *電影*。

1. 按一下 [ *頁面/電影* ] 資料夾 > **加入** > **新** 的樣板 ...]

   ![前述指示中的圖片。](model/_static/scaMac.png)

1. 在 [**新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步**] 選取 [頁面]。

   ![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

1. **Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：
   1. 在 **要使用的 DbCoNtext 類別中：** row，將類別命名為 *Razor PagesMovie。 RazorPagesMovieCoNtext*。
   1. 選取 [完成]。

   ![前述指示中的圖片。](model/_static/5/arpMac.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

### <a name="use-sqlite-for-development-sql-server-for-production"></a>使用 SQLite 進行開發，SQL Server 生產環境

選取 SQLite 時，範本產生的程式碼就可以開始開發。 下列程式碼將示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 至 `Startup` 。 `IWebHostEnvironment` 會插入，讓應用程式可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>建立的檔案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

隨即建立 Scaffold 處理序並更新下列檔案：

* *Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。
* *Data/ Razor PagesMovieCoNtext.cs*

### <a name="updated"></a>已更新

* *Startup.cs*

下一節將說明所建立和更新的檔案。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Scaffold 處理序會建立下列檔案：

* *Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。

下一節將說明所建立的檔案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

隨即建立 Scaffold 處理序並更新下列檔案：

* *Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。
* *Data/ Razor PagesMovieCoNtext.cs*

### <a name="updated"></a>已更新

* *Startup.cs*

下一節將說明所建立和更新的檔案。

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a>使用 EF 的遷移功能建立初始資料庫架構

Entity Framework Core 中的「遷移」功能提供了一種方法，可讓您：

* 建立初始資料庫架構。
* 以累加方式更新資料庫架構，使其與應用程式的資料模型保持同步。  資料庫中的現有資料會保留下來。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在本節中，會使用 **封裝管理員主控台** (PMC) 視窗：

* 新增初始移轉。
* 以初始移轉更新資料庫。

1. 從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。

   ![PMC 功能表](model/_static/5/pmc.png)

1. 在 PMC 中，輸入下列命令：

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* 執行下列 .NET CLI 命令：

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。 如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。 使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」

略過警告，因為它會在稍後的步驟中解決。

`migrations` 命令會產生程式碼來建立初始資料庫結構描述。 架構是以中指定的模型為基礎 `DbContext` 。 `InitialCreate` 引數用來命名移轉。 您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。

此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。 在此情況下，會 `update` `Up` 在建立資料庫的 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>檢查使用相依性插入所註冊的內容

ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。 服務（例如 EF Core 資料庫內容）會在應用程式啟動期間以相依性插入來註冊。 需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。 取得資料庫內容執行個體的建構函式程式碼會顯示在本教學課程稍後部分。

「樣板」工具會自動建立資料庫內容，並使用相依性插入容器來註冊它。

檢查 `Startup.ConfigureServices` 方法。 強調顯示的行由 Scaffolder 新增：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`協調模型 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。 資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。 資料內容會指定資料模型包含哪些實體。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

上述程式碼會建立實體集的[DbSet \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601)屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。 實體會對應至資料表中的資料列。

連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。 針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

檢查 `Up` 方法。

---

<a name="test"></a>

## <a name="test-the-app"></a>測試應用程式

1. 執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。

   如果您收到下列錯誤：

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   您遺失了[移轉步驟](#pmc)。

1. 測試 **Create** 連結。

   ![Create page](model/_static/conan5.png)

   > [!NOTE]
   > 您可能無法在 `Price` 欄位中輸入小數逗號。 若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。 如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。

1. 測試 [編輯]、[詳細資料] 和 [刪除] 連結。

下一個教學課程說明 Scaffolding 所建立的檔案。

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

在本節中，會新增類別來管理電影。 應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。 EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。

模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。 它們會定義資料儲存在資料庫中的屬性。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。

## <a name="add-a-data-model"></a>新增資料模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾**]。 將資料夾命名為 *Models*。

以滑鼠右鍵按一下 [ *模型* ] 資料夾。 選取 [**新增**  >  **類別**]。 將類別命名為 **Movie**。

將下列屬性新增至 `Movie` 類別：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 新增名為 *Models* 的資料夾。
* 將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。

將下列屬性新增至 `Movie` 類別：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>新增 NuGet 套件和 EF 工具

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>新增資料庫內容類別

* 在 *Razor PagesMovie* 專案中，建立名為 *Data* 的新資料夾。
* 將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

上述程式碼會建立實體集的 `DbSet` 屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。 在稍後的步驟中新增相依性之前，程式碼不會進行編譯。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>新增資料庫連線字串

將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>登錄資料庫內容

在 *Startup.cs* 最上方新增下列 `using` 陳述式：

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在 [**方案] 工具視窗** 中，按一下 [ **Razor PagesMovie** ] 專案，然後選取 [**加入** > **新資料夾**...]。命名資料夾 *模型*。
* 以滑鼠右鍵按一下 [ *模型* ] 資料夾，然後 **選取 [** > **新增檔案 ...**]。
* 在 [新增檔案] 對話方塊中：

  * 在左窗格中選取 [一般]。
  * 在中央窗格中選取 [類別是空的]。
  * 將類別命名為 **Movie**，然後選取 [新增]。

將下列屬性新增至 `Movie` 類別：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

---

稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。

建置專案，以確認沒有任何編譯錯誤。

## <a name="scaffold-the-movie-model"></a>Scaffold 影片模型

在本節中會 scaffold 影片模型。 亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

建立 *Pages/Movies* 資料夾：

* 在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。
* 將資料夾命名為 *電影*。

在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。

![前述指示中的圖片。](model/_static/sca.png)

在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > ****。

![前述指示中的圖片。](model/_static/add_scaffold.png)

**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：

* 在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。
* 在 [ **資料內容類別] 資料** 列中，選取 **+** (加號，) 簽署並變更 PagesMovie 所產生的名稱 Razor 。**模型**。 RazorPagesMovieCoNtext 至 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext. 這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。 它會使用正確的命名空間來建立資料庫內容類別。
* 選取 [新增]。

![前述指示中的圖片。](model/_static/3/arp.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 在專案目錄中開啟命令視窗，其中包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案。

* 若 **為 Windows**：請執行下列命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **針對 macOS 與 Linux**：執行下列命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> 下表詳細說明 ASP.NET Core 程式碼產生器選項：

| 選項               | 描述|
| ----------------- | ------------ |
| `-m`  | 模型的名稱。 |
| `-dc`  | 要使用的 `DbContext` 類別。 |
| `-udl` | 使用預設的配置。 |
| `-outDir` | 要建立檢視的相對輸出資料夾路徑。 |
| `--referenceScriptLibraries` | 將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面 |

使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。

### <a name="use-sqlite-for-development-sql-server-for-production"></a>使用 SQLite 進行開發，SQL Server 生產環境

選取 SQLite 時，範本產生的程式碼就可以開始開發。 下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。 `IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

建立 *Pages/Movies* 資料夾：

* 在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。
* 將資料夾命名為 *電影*。

在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新** 的樣板 ...]。

![前述指示中的圖片。](model/_static/scaMac.png)

在 [**新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步**] 選取 [頁面]。

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：

* 在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie (Razor PagesMovie) 模型**。
* 在 [ **資料內容類別** ] 列中，輸入新類別的名稱 Razor PagesMovie。**資料**。 RazorPagesMovieCoNtext. 這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。 它會使用正確的命名空間來建立資料庫內容類別。
* 選取 [新增]。

![前述指示中的圖片。](model/_static/arpMac.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

### <a name="add-ef-tools"></a>新增 EF 工具

執行下列 .NET Core CLI 命令：

```dotnetcli
dotnet tool install --global dotnet-ef
```

上述命令會新增 .NET Core CLI 的 Entity Framework Core 工具。 如需詳細資訊，請參閱 [Entity Framework Core 工具參考-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。

### <a name="use-sqlite-for-development-sql-server-for-production"></a>使用 SQLite 進行開發，SQL Server 生產環境

選取 SQLite 時，範本產生的程式碼就可以開始開發。 下列程式碼示範如何插入 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 啟動。 `IWebHostEnvironment` 已插入，因此 `ConfigureServices` 可以在開發環境中使用 SQLite，並在生產環境中 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>建立的檔案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

隨即建立 Scaffold 處理序並更新下列檔案：

* *Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。
* *Data/ Razor PagesMovieCoNtext.cs*

### <a name="updated"></a>已更新

* *Startup.cs*

下一節將說明所建立和更新的檔案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

隨即建立 Scaffold 處理序並更新下列檔案：

* *Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。
* *Data/ Razor PagesMovieCoNtext.cs*

### <a name="updated"></a>已更新

* *Startup.cs*

下一節將說明所建立和更新的檔案。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Scaffold 處理序會建立下列檔案：

* *Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。

下一節將說明所建立的檔案。

---

<a name="pmc"></a>

## <a name="initial-migration"></a>初始移轉

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：

* 新增初始移轉。
* 以初始移轉更新資料庫。

從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

在 PMC 中，輸入下列命令：

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

執行下列 .NET Core CLI 命令：

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。 如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。 使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」

略過警告，因為它會在稍後的步驟中解決。

遷移命令會產生程式碼來建立初始資料庫架構。 架構是以中指定的模型為基礎 `DbContext` 。 `InitialCreate` 引數用來命名移轉。 您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。

此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。 在此情況下，會 `update` `Up` 在建立資料庫的  *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>檢查使用相依性插入所註冊的內容

ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。 服務（例如 EF Core 資料庫內容內容）會在應用程式啟動期間以相依性插入來註冊。 需要這些服務的元件（例如 Razor 頁面）是透過函式參數提供這些服務。 本教學課程稍後會顯示取得資料庫內容實例的函式程式碼。

「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。

檢查 `Startup.ConfigureServices` 方法。 強調顯示的行由 Scaffolder 新增：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`協調模型 EF Core 功能，例如建立、讀取、更新和刪除 `Movie` 。 資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。 資料內容會指定資料模型包含哪些實體。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。 實體會對應至資料表中的資料列。

連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。 針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

檢查 `Up` 方法。

---

<a name="test"></a>

### <a name="test-the-app"></a>測試應用程式

* 執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。

如果您收到錯誤：

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

您遺失了[移轉步驟](#pmc)。

* 測試 **Create** 連結。

  ![Create page](model/_static/conan5.png)

  > [!NOTE]
  > 您可能無法在 `Price` 欄位中輸入小數逗號。 若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。 如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。

* 測試 [編輯]、[詳細資料] 和 [刪除] 連結。

下一個教學課程說明 Scaffolding 所建立的檔案。

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

在本節中，會新增類別來管理跨平臺 [SQLite 資料庫](https://www.sqlite.org/index.html)中的電影。 從 ASP.NET Core 範本建立的應用程式會使用 SQLite 資料庫。 應用程式的模型類別可搭配 [Entity Framework Core (EF Core) ](/ef/core) ([SQLite EF Core 資料庫提供者](/ef/core/providers/sqlite)) 使用資料庫。 EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化資料存取。

模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。 它們會定義資料儲存在資料庫中的屬性。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。

## <a name="add-a-data-model"></a>新增資料模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾**]。 將資料夾命名為 *Models*。

以滑鼠右鍵按一下 [ *模型* ] 資料夾。 選取 [**新增**  >  **類別**]。 將類別命名為 **Movie**。

將下列屬性新增至 `Movie` 類別：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 新增名為 *Models* 的資料夾。
* 將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。

將下列屬性新增至 `Movie` 類別：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>新增 NuGet 套件和 EF 工具

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>新增資料庫內容類別

在 Razor PagesMovie 專案中，建立名為 *Data* 的新資料夾。 
將下列 `RazorPagesMovieContext` 類別新增至 *ata* 資料夾：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

上述程式碼會建立實體集的 `DbSet` 屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。 在稍後的步驟中新增相依性之前，程式碼不會進行編譯。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>新增資料庫連線字串

將連接字串新增至檔案，如下列醒目提示的程式 *appsettings.json* 代碼所示：

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>新增必要的 NuGet 封裝

執行下列 .NET Core CLI 命令以將 SQLite 和 >nswag.codegeneration.csharp 新增至專案：

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

需要 `Microsoft.VisualStudio.Web.CodeGeneration.Design` 封裝，才能進行 Scaffolding。

<a name="reg"></a>

### <a name="register-the-database-context"></a>登錄資料庫內容

在 *Startup.cs* 最上方新增下列 `using` 陳述式：

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

使用[相依性插入](xref:fundamentals/dependency-injection)容器，在 `Startup.ConfigureServices` 中註冊資料庫內容。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

建置專案來檢查錯誤。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在 [**方案工具] 視窗** 中，按一下 [ *Razor PagesMovie* ] 專案，然後 **選取 [**  >  **新增資料夾**]。 將資料夾命名為 *Models*。
* 在 [ *模型* ] 資料夾上按一下控制項，然後選取 [ **加入** > **新** 檔案]。
* 在 [新增檔案] 對話方塊中：

  * 在左窗格中選取 [一般]。
  * 在中央窗格中選取 [類別是空的]。
  * 將類別命名為 **Movie**，然後選取 [新增]。

將下列屬性新增至 `Movie` 類別：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 類別包含：

* `ID` 欄位是資料庫對於主索引鍵的必要欄位。
* `[DataType(DataType.Date)]`： [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 屬性指定資料 (的類型 `Date`) 。 使用此屬性：

  * 使用者不需要在日期欄位中輸入時間資訊。
  * 只會顯示日期，不會顯示時間資訊。

稍後的教學課程會涵蓋 [DataAnnotations](xref:System.ComponentModel.DataAnnotations)。

---

建置專案，以確認沒有任何編譯錯誤。

## <a name="scaffold-the-movie-model"></a>Scaffold 影片模型

在本節中會 scaffold 影片模型。 亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

建立 *Pages/Movies* 資料夾：

* 在 [ *頁面* ] 資料夾上按一下滑鼠右鍵 **，>** > **新增資料夾**]。
* 將資料夾命名為 *電影*。

在 [ *頁面/電影* ] 資料夾上按一下滑鼠右鍵，> **加入** > **新的 scaffold 專案**]。

![前述指示中的圖片。](model/_static/sca.png)

在 [**新增 Scaffold** ] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > ****。

![前述指示中的圖片。](model/_static/add_scaffold.png)

**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* 在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])**。
* 在 [**資料內容類別] 資料** 列中，選取 **+** (加上) 號，然後接受產生的名稱 **Razor PagesMovie。 RazorPagesMovieCoNtext**。
* 選取 [新增]。

![前述指示中的圖片。](model/_static/arp.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 在專案目錄中開啟命令視窗，其中包含 *Program.cs*、 *Startup.cs* 和 *.csproj* 檔案。

* 若 **為 Windows**：請執行下列命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **針對 macOS 與 Linux**：執行下列命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> 下表詳細說明 ASP.NET Core 程式碼產生器選項：

| 選項               | 描述|
| ----------------- | ------------ |
| `-m`  | 模型的名稱。 |
| `-dc`  | 要使用的 `DbContext` 類別。 |
| `-udl` | 使用預設的配置。 |
| `-outDir` | 要建立檢視的相對輸出資料夾路徑。 |
| `--referenceScriptLibraries` | 將 `_ValidationScriptsPartial` 新增至 Edit 和 Create 頁面 |

使用 `-h` 選項來取得命令的說明 `aspnet-codegenerator razorpage` ：

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

如需詳細資訊，請參閱 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

建立 *Pages/Movies* 資料夾：

* 在 [ *頁面* ] 資料夾上按一下 Control **>** > **新增資料夾**]。
* 將資料夾命名為 *電影*。

在 [ *頁面/電影* ] 資料夾上按一下控制項，> **加入** > **新的 scaffold 專案**]。

![前述指示中的圖片。](model/_static/scaMac.png)

在 [**加入新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > ****。

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面**]：

* 在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie**。
* 在 [**資料內容類別] 資料** 列中，輸入 select **Razor PagesMovieCoNtext** ，這會使用正確的命名空間來建立新的資料庫內容類別。 在此情況下，它將會是 **Razor PagesMovie 模型。 RazorPagesMovieCoNtext**。
* 選取 [新增]。

![前述指示中的圖片。](model/_static/arpMac.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

---

隨即建立 Scaffold 處理序並更新下列檔案：

### <a name="files-created"></a>建立的檔案

* *Pages/電影*：建立、刪除、詳細資料、編輯和 Index 。
* *Data/ Razor PagesMovieCoNtext.cs*

### <a name="file-updated"></a>檔案已更新

* *Startup.cs*

下一節將說明所建立和更新的檔案。

<a name="pmc"></a>

## <a name="initial-migration"></a>初始移轉

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：

* 新增初始移轉。
* 以初始移轉更新資料庫。

從 [工具] 功能表中，選取 [NuGet 封裝管理員]**[封裝管理員主控台]** > 。

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

在 PMC 中，輸入下列命令：

```powershell
Add-Migration Initial
Update-Database
```

`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。 架構是以 PagesMovieCoNtext.cs 檔案中指定的模型為基礎 `DbContext` 。 *Razor* `InitialCreate`引數是用來命名遷移。 您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。 如需詳細資訊，請參閱 <xref:data/ef-mvc/migrations> 。

此 `Update-Database` 命令會 `Up` 在 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。 `Up` 方法會建立資料庫。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

執行下列 .NET Core CLI 命令：

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。 如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。 使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」

略過警告，因為它會在稍後的步驟中解決。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>檢查使用相依性插入所註冊的內容

ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。 服務 (例如 EF Core 資料庫內容內容) 會在應用程式啟動期間以相依性插入來註冊。 需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。 本教學課程稍後會顯示取得資料庫內容 coNtextB 內容實例的函式程式碼。

「樣板」工具會自動建立資料庫內容內容，並使用相依性插入容器來註冊它。

檢查 `Startup.ConfigureServices` 方法。 強調顯示的行由 Scaffolder 新增：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext`座標 EF Core 的功能，例如建立、讀取、更新和刪除 `Movie` 模型。 資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。 資料內容會指定資料模型包含哪些實體。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。 實體會對應至資料表中的資料列。

連接字串的名稱，會透過對 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 物件呼叫方法來傳遞至內容。 針對本機開發，設定 [系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

檢查 `Up` 方法。

---

<a name="test"></a>

### <a name="test-the-app"></a>測試應用程式

* 執行應用程式，並將 `/Movies` 附加至瀏覽器中的 URL ( `http://localhost:port/movies` )。

如果您收到錯誤：

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

您遺失了[移轉步驟](#pmc)。

* 測試 **Create** 連結。

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > 您可能無法在 `Price` 欄位中輸入小數逗號。 若要對使用逗號 (",") 作為小數點的非英文地區設定和非英文日期格式支援 [jQuery 驗證](https://jqueryvalidation.org/)，則必須將應用程式全球化。 如需全球化指示，請參閱[此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) \(英文\)。

* 測試 [編輯]、[詳細資料] 和 [刪除] 連結。

下一個教學課程說明 Scaffolding 所建立的檔案。

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)

::: moniker-end
