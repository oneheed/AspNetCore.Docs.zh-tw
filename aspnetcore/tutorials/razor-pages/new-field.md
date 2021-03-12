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
ms.openlocfilehash: cd637507e19735f020b4c28e6f22de0e7e772040
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588317"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a>第7部分：將新欄位新增至 Razor ASP.NET Core 中的頁面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([如何下載](xref:index#how-to-download-a-sample))。

在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：

* 在模型中新增一個欄位。
* 將新的欄位結構描述變更移轉至資料庫。

使用 EF Code First 自動建立資料庫時，Code First 會：

* 將 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。
* 如果模型類別未與資料庫同步，EF 會擲回例外狀況。

架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。

## <a name="adding-a-rating-property-to-the-movie-model"></a>將 Rating 屬性新增至電影模型

1. 開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. 建置應用程式。

1. 編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. 更新下列頁面：
   1. 將 `Rating` 欄位新增至 Delete 和 Details 頁面。
   1. 使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml)。
   1. 將 `Rating` 欄位新增至 Edit 頁面。

在資料庫更新為包含新欄位之前，應用程式將無法運作。 在沒有資料庫更新的情況下執行應用程式，會擲回 `SqlException` ：

`SqlException: Invalid column name 'Rating'.`

`SqlException`例外狀況是因為更新的電影模型類別與資料庫之電影資料表的架構不同而造成。 `Rating`資料庫資料表中沒有資料行。

有幾個方法可以解決這個錯誤：

1. 讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。 這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。 缺點是會遺失在資料庫中的現有資料。 請勿在生產環境資料庫上使用此方法！ 在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。

2. 您可明確修改現有資料庫的結構描述，使其符合模型類別。 這種方法的優點是保留資料。 請手動或藉由建立資料庫變更腳本來進行這項變更。

3. 使用 Code First 移轉來更新資料庫結構描述。

在本教學課程中，請使用 Code First 移轉。

更新 `SeedData` 類別，使其提供新資料行的值。 範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs) (英文)。

建置方案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>新增評等欄位的移轉

1.  從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。
2. 在 PMC 中，輸入下列命令：

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

`Add-Migration` 命令會告知架構，以便：

* 比較 `Movie` 模型與 `Movie` 資料庫架構。
* 建立程式碼，將資料庫架構遷移至新的模型。

"Rating" 是用來命名移轉檔案的任意名稱。 建議您針對移轉檔案使用有意義的名稱，這更加實用。

此 `Update-Database` 命令會指示架構將架構變更套用至資料庫，並保留現有的資料。

<a name="ssox"></a>

如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。 您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。

另一個選擇是刪除資料庫並使用移轉重新建立資料庫。 若要在 SSOX 中刪除資料庫：

1. 在 SSOX 中選取資料庫。
1. 以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。
1. 勾選 [ **關閉現有的連接**]。
1. 選取 [確定]。
1. 在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>卸除並重新建立資料庫

> [!NOTE]
> 在本教學課程中，您會盡可能使用 Entity Framework Core *遷移* 功能。 移轉可更新資料庫結構描述，以符合資料模型中的變更。 不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。 例如，其支援新增資料行，但不支援移除或變更資料行。 如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。 由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。 反之，當結構描述變更時，請您卸除並重新建立資料庫。
>
>SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。 重建資料表包含：
>
>* 建立新的資料表。
>* 將資料從舊的資料表複製到新的資料表。
>* 卸除舊的資料表。
>* 重新命名新資料表。
>
>如需詳細資訊，請參閱下列資源：
>
> * [SQLite EF Core 資料庫提供者限制](/ef/core/providers/sqlite/limitations)
> * [自訂移轉程式碼](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [資料植入](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)

1. 刪除移轉資料夾。  

1. 使用下列命令重新建立資料庫。

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。 若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([如何下載](xref:index#how-to-download-a-sample))。

在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：

* 在模型中新增一個欄位。
* 將新的欄位結構描述變更移轉至資料庫。

使用 EF Code First 自動建立資料庫時，Code First 會：

* 將 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。
* 如果模型類別未與資料庫同步，EF 會擲回例外狀況。

架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。

## <a name="adding-a-rating-property-to-the-movie-model"></a>將 Rating 屬性新增至電影模型

1. 開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. 建置應用程式。

1. 編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. 更新下列頁面：
   1. 將 `Rating` 欄位新增至 Delete 和 Details 頁面。
   1. 使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)。
   1. 將 `Rating` 欄位新增至 Edit 頁面。

在資料庫更新為包含新欄位之前，應用程式將無法運作。 在沒有資料庫更新的情況下執行應用程式，會擲回 `SqlException` ：

`SqlException: Invalid column name 'Rating'.`

`SqlException`例外狀況是因為更新的電影模型類別與資料庫之電影資料表的架構不同而造成。 `Rating`資料庫資料表中沒有資料行。

有幾個方法可以解決這個錯誤：

1. 讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。 這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。 缺點是會遺失在資料庫中的現有資料。 請勿在生產環境資料庫上使用此方法！ 在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。

2. 您可明確修改現有資料庫的結構描述，使其符合模型類別。 這種方法的優點是保留資料。 請手動或藉由建立資料庫變更腳本來進行這項變更。

3. 使用 Code First 移轉來更新資料庫結構描述。

在本教學課程中，請使用 Code First 移轉。

更新 `SeedData` 類別，使其提供新資料行的值。 範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs) (英文)。

建置方案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>新增評等欄位的移轉

1.  從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。
2. 在 PMC 中，輸入下列命令：

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

`Add-Migration` 命令會告知架構，以便：

* 比較 `Movie` 模型與 `Movie` 資料庫架構。
* 建立程式碼，將資料庫架構遷移至新的模型。

"Rating" 是用來命名移轉檔案的任意名稱。 建議您針對移轉檔案使用有意義的名稱，這更加實用。

此 `Update-Database` 命令會指示架構將架構變更套用至資料庫，並保留現有的資料。

<a name="ssox"></a>

如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。 您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。

另一個選擇是刪除資料庫並使用移轉重新建立資料庫。 若要在 SSOX 中刪除資料庫：

* 在 SSOX 中選取資料庫。
* 以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。
* 勾選 [ **關閉現有的連接**]。
* 選取 [確定]。
* 在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>卸除並重新建立資料庫

> [!NOTE]
> 在本教學課程中，您可以盡可能使用 Entity Framework Core *遷移* 功能。 移轉可更新資料庫結構描述，以符合資料模型中的變更。 不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。 例如，其支援新增資料行，但不支援移除或變更資料行。 如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。 由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。 反之，當結構描述變更時，請您卸除並重新建立資料庫。
>
>SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。 重建資料表包含：
>
>* 建立新的資料表。
>* 將資料從舊的資料表複製到新的資料表。
>* 卸除舊的資料表。
>* 重新命名新資料表。
>
>如需詳細資訊，請參閱下列資源：
>
> * [SQLite EF Core 資料庫提供者限制](/ef/core/providers/sqlite/limitations)
> * [自訂移轉程式碼](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [資料植入](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)

1. 刪除移轉資料夾。  

1. 使用下列命令重新建立資料庫。

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。 若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。

## <a name="additional-resources"></a>其他資源

> [!div class="step-by-step"]
> [上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start) ([如何下載](xref:index#how-to-download-a-sample))。

在本節中，您會使用 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 移轉：

* 在模型中新增一個欄位。
* 將新的欄位結構描述變更移轉至資料庫。

使用 EF Code First 自動建立資料庫時，Code First 會：

* 將 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 資料表加入至資料庫，以追蹤資料庫的架構是否與其產生的模型類別同步。
* 如果模型類別未與資料庫同步，EF 會擲回例外狀況。

架構和模型同步的自動驗證，可讓您更輕鬆地找到不一致的資料庫程式碼問題。

## <a name="adding-a-rating-property-to-the-movie-model"></a>將 Rating 屬性新增至電影模型

開啟 *Models/Movie.cs* 檔案，然後新增 `Rating` 屬性：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

建置應用程式。

編輯 *Pages/電影/ Index cshtml*，然後新增 `Rating` 欄位：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

更新下列頁面：

* 將 `Rating` 欄位新增至 Delete 和 Details 頁面。
* 使用 `Rating` 欄位更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)。
* 將 `Rating` 欄位新增至 Edit 頁面。

在資料庫更新為包含新欄位之前，應用程式將無法運作。 如果立即執行應用程式，應用程式會擲回 `SqlException` ：

`SqlException: Invalid column name 'Rating'.`

此錯誤是因為更新的電影模型類別，不同於資料庫之電影資料表的結構描述 `Rating`資料庫資料表中沒有資料行。

有幾個方法可以解決這個錯誤：

1. 讓 Entity Framework 自動卸除資料庫，並使用新的模型類別結構描述來重新建立資料庫。 這種方法在開發週期初期很方便，可讓您快速地將模型和資料庫架構一起演進。 缺點是會遺失在資料庫中的現有資料。 請勿在生產環境資料庫上使用此方法！ 在架構變更上卸載資料庫，並使用初始化運算式以使用測試資料自動植入資料庫，通常是開發應用程式的有效方式。

2. 您可明確修改現有資料庫的結構描述，使其符合模型類別。 這種方法的優點是保留資料。 請手動或藉由建立資料庫變更腳本來進行這項變更。

3. 使用 Code First 移轉來更新資料庫結構描述。

在本教學課程中，請使用 Code First 移轉。

更新 `SeedData` 類別，使其提供新資料行的值。 範例變更如下所示，但對每個區塊進行這項變更 `new Movie` 。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

請參閱[完整的 SeedData.cs 檔案](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs) (英文)。

建置方案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>新增評等欄位的移轉

 從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。
在 PMC 中，輸入下列命令：

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` 命令會告知架構，以便：

* 比較 `Movie` 模型與 `Movie` 資料庫架構。
* 建立程式碼，將資料庫架構遷移至新的模型。

"Rating" 是用來命名移轉檔案的任意名稱。 建議您針對移轉檔案使用有意義的名稱，這更加實用。

`Update-Database` 命令會指示架構將結構描述變更套用至資料庫。

<a name="ssox"></a>

如果您刪除資料庫中的所有記錄，初始化運算式將會植入資料庫，並包含 `Rating` 欄位。 您可以使用瀏覽器或 [Sql Server 物件總管](xref:tutorials/razor-pages/sql#ssox) (SSOX) 的刪除連結來執行這項操作。

另一個選擇是刪除資料庫並使用移轉重新建立資料庫。 若要在 SSOX 中刪除資料庫：

* 在 SSOX 中選取資料庫。
* 以滑鼠右鍵按一下資料庫，然後選取 [ **刪除**]。
* 勾選 [ **關閉現有的連接**]。
* 選取 [確定]。
* 在 [PMC](xref:tutorials/razor-pages/new-field#pmc)中，更新資料庫：

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>卸除並重新建立資料庫

> [!NOTE]
> 在本教學課程中，您可以盡可能使用 Entity Framework Core *遷移* 功能。 移轉可更新資料庫結構描述，以符合資料模型中的變更。 不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。 例如，其支援新增資料行，但不支援移除或變更資料行。 如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。 由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。 反之，當結構描述變更時，請您卸除並重新建立資料庫。
>
>SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。 重建資料表包含：
>
>* 建立新的資料表。
>* 將資料從舊的資料表複製到新的資料表。
>* 卸除舊的資料表。
>* 重新命名新資料表。
>
>如需詳細資訊，請參閱下列資源：
>
> * [SQLite EF Core 資料庫提供者限制](/ef/core/providers/sqlite/limitations)
> * [自訂移轉程式碼](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [資料植入](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)

刪除資料庫並使用移轉重新建立資料庫。 若要刪除資料庫，請刪除資料庫檔案 (*MvcMovie.db*)。 然後執行 `ef database update` 命令：

```dotnetcli
dotnet ef database update
```

---

執行應用程式，並確認您可以使用 `Rating` 欄位建立/編輯/顯示電影。 若未植入資料庫，請在 `SeedData.Initialize` 方法中設定中斷點。

## <a name="additional-resources"></a>其他資源

* [本教學課程的 YouTube 版本](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [上一步：新增搜尋](xref:tutorials/razor-pages/search) 
> [下一步：新增驗證](xref:tutorials/razor-pages/validation)

::: moniker-end