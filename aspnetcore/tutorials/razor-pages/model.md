---
title: 第2部分：在 ASP.NET Core 中將模型新增至 Razor 頁面應用程式
author: rick-anderson
description: 頁面上教學課程系列的第2部分 Razor 。
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050923"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>第2部分：在 ASP.NET Core 中將模型新增至 Razor 頁面應用程式

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

在本節中，會新增類別來管理電影。 應用程式的模型類別會使用 [Entity Framework Core (EF Core) ](/ef/core) 來處理資料庫。 EF Core 是物件關聯式對應程式 (O/RM) 可簡化資料存取。

模型類別稱為 POCO 類別 (來自「簡單的 CLR 物件」)，因為它們對 EF Core 沒有任何相依性。 它們會定義資料儲存在資料庫中的屬性。

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>新增資料模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾** ]。 將資料夾命名為 *Models* 。

以滑鼠右鍵按一下 *Models* 資料夾。 選取 [ **新增**  >  **類別** ]。 將類別命名為 **Movie** 。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 新增名為 *Models* 的資料夾。
* 將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在 Solution Pad 中，以滑鼠右鍵按一下 **Razor PagesMovie** 專案，然後選取 [ **加入** > **新資料夾** ...]。命名資料夾 *模型* 。
* 以滑鼠右鍵按一下 [ *模型* ] 資料夾，然後 **選取 [** > **新增檔案 ...** ]。
* 在 [新增檔案]  對話方塊中：

  * 在左窗格中選取 [一般]  。
  * 在中央窗格中選取 [類別是空的]  。
  * 將類別命名為  。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

建置專案，以確認沒有任何編譯錯誤。

## <a name="scaffold-the-movie-model"></a>Scaffold 影片模型

在本節中會 scaffold 影片模型。 亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

建立 *Pages/Movies* 資料夾：

* 以滑鼠右鍵按一下 [Pages]  資料夾 > [新增]  。
* 將資料夾命名為 *Movies*

以滑鼠右鍵按一下 [Pages/Movies]  資料夾 > [新增]  。

![前述指示中的圖片。](model/_static/sca.png)

在 [  。

![前述指示中的圖片。](model/_static/add_scaffold.png)

**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：

* 在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])** 。
* 在 [ **資料內容類別] 資料** 列中，選取 **+** (加號，) 簽署並變更 PagesMovie 所產生的名稱 Razor 。 **模型** 。 RazorPagesMovieCoNtext 至 Razor PagesMovie。 **資料** 。 RazorPagesMovieCoNtext. 這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。 它會使用正確的命名空間來建立資料庫內容類別。
* 選取 [新增]。

![前述指示中的圖片。](model/_static/3/arp.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 在專案目錄 (包含 *Program.cs* 、 *Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。
* 安裝 Scaffolding 工具：

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* 若 **為 Windows** ：請執行下列命令：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **針對 macOS 與 Linux** ：執行下列命令：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

建立 *Pages/Movies* 資料夾：

* 以滑鼠右鍵按一下 [Pages]  資料夾 > [新增]  。
* 將資料夾命名為 *Movies*

以滑鼠右鍵按一下 [ *Pages/電影* ] 資料夾，> **加入** > **新** 的樣板 ...]。

![前述指示中的圖片。](model/_static/scaMac.png)

在 [ **新** 的樣板] 對話方塊中， **Razor 使用 Entity Framework (CRUD)** > **下一步** ] 選取 [頁面]。

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：

* 在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie (Razor PagesMovie) 模型** 。
* 在 [ **資料內容類別** ] 列中，輸入新類別的名稱 Razor PagesMovie。 **資料** 。 RazorPagesMovieCoNtext. 這不是必要的[變更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) \(英文\)。 它會使用正確的命名空間來建立資料庫內容類別。
* 選取 [新增]。

![前述指示中的圖片。](model/_static/arpMac.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

### <a name="add-ef-tools"></a>新增 EF 工具

執行下列 .NET Core CLI 命令：

```dotnetcli
dotnet tool install --global dotnet-ef
```

上述命令會新增 .NET Core CLI 的 Entity Framework Core 工具。

---

### <a name="files-created"></a>建立的檔案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

隨即建立 Scaffold 處理序並更新下列檔案：

* *Pages/Movies* ：建立、刪除、詳細資料、編輯和索引。
* *Data/ Razor PagesMovieCoNtext.cs*

### <a name="updated"></a>已更新

* *Startup.cs*

下一節將說明所建立和更新的檔案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

隨即建立 Scaffold 處理序並更新下列檔案：

* *Pages/Movies* ：建立、刪除、詳細資料、編輯和索引。
* *Data/ Razor PagesMovieCoNtext.cs*

### <a name="updated"></a>已更新

* *Startup.cs*

下一節將說明所建立和更新的檔案。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Scaffold 處理序會建立下列檔案：

* *Pages/Movies* ：建立、刪除、詳細資料、編輯和索引。

下一節將說明所建立的檔案。

---

<a name="pmc"></a>

## <a name="initial-migration"></a>初始移轉

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在本節中，您可以使用套件管理員主控台 (PMC) 進行下列作業：

* 新增初始移轉。
* 以初始移轉更新資料庫。

從 [工具]  功能表中，選取 [NuGet 封裝管理員]  。

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

在 PMC 中，輸入下列命令：

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

上述命令會產生下列警告：「未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。 如果它們不符合預設的有效位數和小數位數，會導致以無訊息模式截斷這些值。 使用 'HasColumnType()' 明確指定可容納所有值的 SQL Server 資料行類型。」

您可以忽略該警告，稍後的教學課程中將修正此問題。

遷移命令會產生程式碼來建立初始資料庫架構。 架構是以中指定的模型為基礎 `DbContext` 。 `InitialCreate` 引數用來命名移轉。 您可以使用任何名稱，但依照慣例，會選取描述移轉的名稱。

此 `update` 命令會在尚未套用 `Up` 的遷移中執行方法。 在此情況下，會 `update` `Up` 在建立資料庫的  *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>檢查使用相依性插入所註冊的內容

ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。 服務 (例如 EF Core DB 內容) 是在應用程式啟動期間使用相依性插入來註冊。 需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。 取得資料庫內容執行個體的建構函式程式碼，本教學課程中稍後會示範。

Scaffolding 工具會自動建立資料庫內容，並向相依性插入容器註冊。

檢查 `Startup.ConfigureServices` 方法。 強調顯示的行由 Scaffolder 新增：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` 會協調 `Movie` 模型的 EF Core 功能 (建立、更新、刪除等)。 資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。 資料內容會指定資料模型包含哪些實體。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。 實體會對應至資料表中的資料列。

連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。 針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

檢查 `Up` 方法。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

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

* 測試 [編輯]  、[詳細資料]  和 [刪除]  連結。

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

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>新增資料模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **>**  >  **新增資料夾** ]。 將資料夾命名為 *Models* 。

以滑鼠右鍵按一下 *Models* 資料夾。 選取 [ **新增**  >  **類別** ]。 將類別命名為 **Movie** 。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 新增名為 *Models* 的資料夾。
* 將類別新增至名為 *Movie.cs* 的 *Models* 資料夾。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在方案總管中，以滑鼠右鍵按一下 **Razor PagesMovie** 專案， **然後選取 [**  >  **新增資料夾** ]。 將資料夾命名為 *Models* 。
* 以滑鼠右鍵按一下 [Models]  資料夾，然後選取 [新增]  。
* 在 [新增檔案]  對話方塊中：

  * 在左窗格中選取 [一般]  。
  * 在中央窗格中選取 [類別是空的]  。
  * 將類別命名為  。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

建置專案，以確認沒有任何編譯錯誤。

## <a name="scaffold-the-movie-model"></a>Scaffold 影片模型

在本節中會 scaffold 影片模型。 亦即 Scaffolding 工具會產生影片模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

建立 *Pages/Movies* 資料夾：

* 以滑鼠右鍵按一下 [Pages]  資料夾 > [新增]  。
* 將資料夾命名為 *Movies*

以滑鼠右鍵按一下 [Pages/Movies]  資料夾 > [新增]  。

![前述指示中的圖片。](model/_static/sca.png)

在 [  。

![前述指示中的圖片。](model/_static/add_scaffold.png)

**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* 在 [ **模型類別** ] 下拉式清單中，選取 [ **Movie (Razor PagesMovie])** 。
* 在 [ **資料內容類別] 資料** 列中，選取 **+** (加上) 號，然後接受產生的名稱 **Razor PagesMovie。 RazorPagesMovieCoNtext** 。
* 選取 [新增]。

![前述指示中的圖片。](model/_static/arp.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 在專案目錄 (包含 *Program.cs* 、 *Startup.cs* 和 *.csproj* 檔案的目錄) 中開啟一個命令視窗。

* 若 **為 Windows** ：請執行下列命令：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **針對 macOS 與 Linux** ：執行下列命令：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

建立 *Pages/Movies* 資料夾：

* 以滑鼠右鍵按一下 [Pages]  資料夾 > [新增]  。
* 將資料夾命名為 *Movies*

以滑鼠右鍵按一下 [Pages/Movies]  資料夾 > [新增]  。

![前述指示中的圖片。](model/_static/scaMac.png)

在 [  。

![前述指示中的圖片。](model/_static/add_scaffoldMac.png)

**Razor 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：

* 在 [ **模型類別** ] 下拉式清單中，選取或輸入 **Movie** 。
* 在 [ **資料內容類別** ] 列中，輸入 select **Razor PagesMovieCoNtext** ，這會使用正確的命名空間來建立新的資料庫內容類別。 在此情況下，它將會是 **Razor PagesMovie 模型。 RazorPagesMovieCoNtext** 。
* 選取 [新增]。

![前述指示中的圖片。](model/_static/arpMac.png)

檔案 *appsettings.json* 會以用來連接到本機資料庫的連接字串進行更新。

---

隨即建立 Scaffold 處理序並更新下列檔案：

### <a name="files-created"></a>建立的檔案

* *Pages/Movies* ：建立、刪除、詳細資料、編輯和索引。
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

從 [工具]  功能表中，選取 [NuGet 封裝管理員]  。

  ![PMC 功能表](../first-mvc-app/adding-model/_static/pmc.png)

在 PMC 中，輸入下列命令：

```powershell
Add-Migration Initial
Update-Database
```

`Add-Migration` 命令會產生程式碼來建立初始資料庫結構描述。 架構是以 `DbContext` *Razor PagesMovieCoNtext.cs* 檔) 的 (中指定的模型為基礎。 `InitialCreate`引數是用來命名遷移。 您可以使用任何名稱，但依照慣例，會使用描述移轉的名稱。 如需詳細資訊，請參閱<xref:data/ef-mvc/migrations>。

此 `Update-Database` 命令會 `Up` 在 *遷移/ \<time-stamp> _InitialCreate .cs* 檔案中執行方法。 `Up` 方法會建立資料庫。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> 上述命令會產生下列警告：「 *未在實體類型 ' Movie ' 上的十進位資料行 ' Price ' 指定類型。如果值不符合預設的有效位數和小數位數，則會導致以無訊息模式截斷值。使用 ' HasColumnType ( # A1 ' 來明確指定可容納所有值的 SQL server 資料行類型。* 您可以忽略該警告，在稍後的教學課程中將會加以修正。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>檢查使用相依性插入所註冊的內容

ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。 服務 (例如 EF Core DB 內容) 是在應用程式啟動期間使用相依性插入來註冊。 需要這些服務的元件 (例如 Razor 頁面) 是透過函式參數提供這些服務。 取得資料庫內容執行個體的建構函式程式碼，本教學課程中稍後會示範。

Scaffolding 工具會自動建立資料庫內容，並向相依性插入容器註冊。

檢查 `Startup.ConfigureServices` 方法。 強調顯示的行由 Scaffolder 新增：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` 會協調 `Movie` 模型的 EF Core 功能 (建立、更新、刪除等)。 資料內容 (`RazorPagesMovieContext`) 衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。 資料內容會指定資料模型包含哪些實體。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

上述程式碼會建立實體集的[DbSet \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表。 實體會對應至資料表中的資料列。

連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。 針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

檢查 `Up` 方法。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

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

* 測試 [編輯]  、[詳細資料]  和 [刪除]  連結。

下一個教學課程說明 Scaffolding 所建立的檔案。

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一步：開始](xref:tutorials/razor-pages/razor-pages-start) 
> [下一步： scaffold Razor頁面](xref:tutorials/razor-pages/page)

::: moniker-end
