---
title: 教學課程第5部分：將遷移套用至 Contoso 大學範例
description: Contoso 大學教學課程系列的第5部分。 使用 EF Core 的遷移功能來管理 ASP.NET Core MVC 應用程式中的資料模型變更。
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
ms.openlocfilehash: 237192b93c3cf21657c6053454952c2677111cd1
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105051071"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a>教學課程：第5部分：將遷移套用至 Contoso 大學範例

在本教學課程中，您會先使用 EF Core 移轉功能來管理資料模型變更。 在稍後的教學課程中，您將在變更資料模型時新增更多移轉作業。

在本教學課程中，您：

> [!div class="checklist"]
> * 了解移轉
> * 建立初始移轉
> * 檢查 Up 和 Down 方法
> * 了解資料模型快照集
> * 套用移轉

## <a name="prerequisites"></a>必要條件

* [排序、篩選和分頁](sort-filter-page.md)

## <a name="about-migrations"></a>關於移轉

在開發新的應用程式時，您的資料模型會頻繁變更，而每次模型變更時，模型會與資料庫不同步。 首先，您會設定 Entity Framework 以建立資料庫 (如果尚未存在的話) 以進行本教學課程。 然後在每次變更資料模型 (新增、移除或變更實體類別或變更您的 DbContext 類別) 時，您可以刪除資料庫，EF 即會建立一個相符的新模型，並植入測試資料。

在您將應用程式部署到生產環境之前，都可以使用上述方法讓資料庫與資料模型保持同步。 但當應用程式在生產環境中執行時，通常會儲存您想要保留的資料，而您也不想在每次資料變更 (例如新增資料行) 時遺失任何項目。 為了解決上述問題，EF Core 移轉功能可讓 EF 更新資料庫結構描述，而不是建立新的資料庫。

若要使用遷移，您可以使用 **封裝管理員主控台** (PMC) 或 CLI。  這些教學課程會示範如何使用 CLI 命令。 PMC 的資訊則位於[本教學課程結尾](#pmc)。

## <a name="drop-the-database"></a>卸除資料庫

以 [通用工具](/ef/core/miscellaneous/cli/dotnet) 的形式安裝 EF Core 工具，然後刪除資料庫：

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

下節說明如何執行 CLI 命令。

## <a name="create-an-initial-migration"></a>建立初始移轉

儲存您的變更，並建置專案。 接著，開啟命令視窗並巡覽至專案資料夾。 以下是執行這個動作的快速方法：

* 在 [方案總管] 中，於專案上按一下滑鼠右鍵，然後從操作功能表中選擇 [在檔案總管中開啟資料夾]。

  ![[在檔案總管中開啟] 功能表項目](migrations/_static/open-in-file-explorer.png)

* 在位址列中輸入 "cmd"，然後按 Enter。

  ![開啟命令視窗](migrations/_static/open-command-window.png)

在命令視窗中輸入下列命令：

```dotnetcli
dotnet ef migrations add InitialCreate
```

在上述命令中，會顯示類似下列的輸出：

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

如果您看到錯誤訊息「*無法存取檔案 ... ContosoUniversity.dll，因為另一個進程正在使用它*。」，請在 Windows 系統匣中尋找 IIS Express 圖示，然後以滑鼠右鍵按一下它，再按一下 [ **ContosoUniversity > 停止網站**]。

## <a name="examine-up-and-down-methods"></a>檢查 Up 和 Down 方法

當您執行 `migrations add` 命令時，EF 產生的程式碼會從頭開始建立資料庫。 這段程式碼位於名為 *\<timestamp> _InitialCreate .cs* 的檔案中的 [*遷移*] 資料夾中。 `InitialCreate` 類別的 `Up` 方法會建立對應至資料模型實體集的資料庫資料表，而 `Down` 方法則會刪除它們，如下列範例所示。

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Migrations 會呼叫 `Up` 方法，以實作移轉所需的資料模型變更。 當您輸入命令以復原更新時，Migrations 會呼叫 `Down` 方法。

這個程式碼適用於您之前輸入 `migrations add InitialCreate` 命令時所建立的初始移轉。 移轉名稱參數 (在此範例中為 "InitialCreate") 可作為檔案名稱，您也可以任意命名。 建議您選擇某個單字或片語，以摘要說明移轉中所要完成的作業。 例如，您可以將稍後的移轉命名為 "AddDepartmentTable"。

如果在您建立初始移轉時資料庫已經存在，系統會產生資料庫建立程式碼，但不需要執行，因為資料庫已經符合資料模型。 當您將應用程式部署到資料庫尚未存在的其他環境中時，即會執行這個程式碼以建立您的資料庫；建議您先進行測試。 這就是您稍早卸載資料庫的原因，讓遷移可以從頭建立一個新的。

## <a name="the-data-model-snapshot"></a>資料模型快照集

移轉會在 *Migrations/SchoolContextModelSnapshot.cs* 中建立目前資料庫結構描述的「快照集」。 當您新增移轉時，EF 會比較資料模型與快照集檔案，以判斷變更的內容。

使用 [dotnet ef 遷移移除](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 命令以移除遷移。 `dotnet ef migrations remove` 會刪除移轉，並確保正確地重設快照集。 如果 `dotnet ef migrations remove` 失敗，請使用 `dotnet ef migrations remove -v` 以取得失敗的詳細資訊。

如需如何使用快照集檔案的詳細資訊，請參閱[小組環境中的 EF Core 移轉](/ef/core/managing-schemas/migrations/teams)。

## <a name="apply-the-migration"></a>套用移轉

在命令視窗中輸入下列命令，以建立資料庫和其中的資料表。

```dotnetcli
dotnet ef database update
```

此命令的輸出類似於 `migrations add` 命令，不同之處在於其會顯示設定資料庫之 SQL 命令的記錄。 下列範例輸出中省略了大部分的記錄。 如果您不想看到這麼詳細的記錄訊息，可以變更 *appsettings.Development.json* 檔案中的記錄層級。 如需詳細資訊，請參閱<xref:fundamentals/logging/index>。

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

按照第一個教學課程的做法，使用 [SQL Server 物件總管] 檢查資料庫。  您會發現新增 \_\_EFMigrationsHistory 資料表，其會持續追蹤哪些移轉已套用至資料庫。 檢視該資料表中的資料，您會看到其中一個資料列代表第一個移轉。 (上述 CLI 輸出範例中的最後一則記錄會顯示建立此資料列的 INSERT 陳述式。)

執行應用程式，以驗證一切如往常般運作。

![Students [索引] 頁面](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>比較 CLI 與 PMC

您可以透過 .NET Core CLI 命令或 Visual Studio [套件管理員主控台] (PMC) 視窗中的 PowerShell Cmdlet，取得適用於管理移轉的 EF 工具。 本教學課程示範如何使用 CLI，但是您也可以視習慣使用 PMC。

適用於 PMC 命令的 EF 命令位於 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 套件中。 此套件包含在 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中；因此，如果您的應用程式具有 `Microsoft.AspNetCore.App` 的套件參考，則您不需要新增套件參考。

**重要事項：** 此套件與您為 CLI 安裝的套件不同 (透過編輯 *.csproj* 檔案來進行)。 這個套件的名稱以 `Tools` 結尾，不同於以 `Tools.DotNet` 結尾的 CLI 套件名稱。

如需 CLI 命令的詳細資訊，請參閱 [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。

如需 PMC 命令的詳細資訊，請參閱[套件管理員主控台 (Visual Studio)](/ef/core/miscellaneous/cli/powershell)。

## <a name="get-the-code"></a>取得程式碼

[下載或檢視已完成的應用程式。](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a>後續步驟

若要開始查看更進階的主題，了解資料模型的擴展，請前往下一個教學課程。 在過程中，您會建立並套用其他移轉。

> [!div class="nextstepaction"]
> [建立並套用額外的移轉](complex-data-model.md)
