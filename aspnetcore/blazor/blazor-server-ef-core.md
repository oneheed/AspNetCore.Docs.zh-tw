---
title: 'Blazor Server具有 Entity Framework Core (EFCore 的 ASP.NET Core) '
author: JeremyLikness
description: 在應用程式中使用 EF Core 的指導方針 Blazor Server 。
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 2ea702aa73a2981afc223e5c1700d6ec2dc62df4
ms.sourcegitcommit: 503b348e9046fcd969de85898394a1ea8274ec38
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227748"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>Blazor Server具有 Entity Framework Core (EFCore 的 ASP.NET Core) 

依據： [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server 是可設定狀態的應用程式架構。 應用程式會維持與伺服器的持續連線，而使用者的狀態會保留在*伺服器的記憶體中。* 「使用者狀態」的其中一個範例是將資料保存在相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為該線路。 提供的唯一應用程式模型 Blazor Server 需要特殊的方法，才能使用 Entity Framework Core。 

> [!NOTE]
> 本文說明應用程式中的 EF Core Blazor Server 。 Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。 在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。

## <a name="sample-app"></a>範例應用程式

範例應用程式已建立為 Blazor Server 使用 EF Core 之應用程式的參考。 範例應用程式包含具有排序和篩選、刪除、加入和更新作業的方格。 此範例示範如何使用 EF Core 來處理開放式平行存取。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) 

此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，讓它可以在任何平臺上使用。 此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。 這是在中設定 `appsettings.Development.json` ：

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

方格、加入和視圖元件會使用「每一作業內容」模式，其中會為每個作業建立內容。 編輯元件會使用「每個元件的內容」模式，其中會為每個元件建立內容。

## <a name="database-access"></a>資料庫存取

EF Core 依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取權](/ef/core/miscellaneous/configuring-dbcontext) ，並作為 [_工作單位的_](https://martinfowler.com/eaaCatalog/unitOfWork.html)方式。 EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的延伸模組，其會在預設情況下，將內容註冊為 _範圍_ 服務。 在 Blazor Server 應用程式中，有範圍的服務註冊可能會有問題，因為實例會在使用者線路內的各個元件之間共用。 <xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了同時使用而設計。 基於下列原因，現有的存留期不適當：

* **Singleton** 會在應用程式的所有使用者之間共用狀態，並導致不適當的並行使用。
* 限**域** (預設) 會在相同使用者的元件之間產生類似的問題。
* **暫時性** 會產生每個要求的新實例;但因為元件可能會很長，所以這會產生比預期更長的內容。

下列建議的設計，是為了提供一致的方法，讓您在應用程式中使用 EF Core Blazor Server 。 

* 根據預設，請考慮針對每個作業使用一個內容。 內容是針對快速、低負荷的具現化所設計：

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* 使用旗標來防止多個並行作業：

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* 對於利用 EF Core 的 [變更追蹤](/ef/core/querying/tracking) 或 [並行存取控制](/ef/core/saving/concurrency)的較長時間作業，請 [將內容約制限定在元件的存留期](#scope-to-the-component-lifetime)。

### <a name="new-dbcontext-instances"></a>新的 DbCoNtext 實例

建立新實例的最快方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 來建立新的實例。 不過，有幾個案例可能需要解析其他相依性。 例如，您可能會想要使用 [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 來設定內容。 

建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。 EF Core 5.0 或更新版本會提供內建的 factory 來建立新的內容。

下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。 程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

Factory 會插入元件中，並用來建立新的實例。 例如，在 `Pages/Index.razor` 中：

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a>元件存留期的範圍

您可能想要建立一個 <xref:Microsoft.EntityFrameworkCore.DbContext> 存在於元件存留期的。 這可讓您使用它做為 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) ，並利用內建功能，例如變更追蹤和並行解析。
您可以使用 factory 來建立內容，並在元件的存留期間追蹤它。 首先，執行 <xref:System.IDisposable> 並插入 factory，如下所示 `Pages/EditContact.razor` ：

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

範例應用程式可確保在處置元件時，會處置連絡人：

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

最後， `OnInitializedAsync` 會覆寫以建立新的內容。 在範例應用程式中，會 `OnInitializedAsync` 以相同的方法載入連絡人：

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server 是可設定狀態的應用程式架構。 應用程式會維持與伺服器的持續連線，而使用者的狀態會保留在*伺服器的記憶體中。* 「使用者狀態」的其中一個範例是將資料保存在相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為該線路。 提供的唯一應用程式模型 Blazor Server 需要特殊的方法，才能使用 Entity Framework Core。 

> [!NOTE]
> 本文說明應用程式中的 EF Core Blazor Server 。 Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。 在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。

## <a name="sample-app"></a>範例應用程式

範例應用程式已建立為 Blazor Server 使用 EF Core 之應用程式的參考。 範例應用程式包含具有排序和篩選、刪除、加入和更新作業的方格。 此範例示範如何使用 EF Core 來處理開放式平行存取。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) 

此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，讓它可以在任何平臺上使用。 此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。 這是在中設定 `appsettings.Development.json` ：

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

方格、加入和視圖元件會使用「每一作業內容」模式，其中會為每個作業建立內容。 編輯元件會使用「每個元件的內容」模式，其中會為每個元件建立內容。

## <a name="database-access"></a>資料庫存取

EF Core 依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取權](/ef/core/miscellaneous/configuring-dbcontext) ，並作為 [_工作單位的_](https://martinfowler.com/eaaCatalog/unitOfWork.html)方式。 EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的延伸模組，其會在預設情況下，將內容註冊為 _範圍_ 服務。 在 Blazor Server 應用程式中，這可能會造成問題，因為實例會在使用者線路內的各個元件之間共用。 <xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了同時使用而設計。 基於下列原因，現有的存留期不適當：

* **Singleton** 會在應用程式的所有使用者之間共用狀態，並導致不適當的並行使用。
* 限**域** (預設) 會在相同使用者的元件之間產生類似的問題。
* **暫時性** 會產生每個要求的新實例;但因為元件可能會很長，所以這會產生比預期更長的內容。

## <a name="database-access"></a>資料庫存取

下列建議的設計，是為了提供一致的方法，讓您在應用程式中使用 EF Core Blazor Server 。 

* 根據預設，請考慮針對每個作業使用一個內容。 內容是針對快速、低負荷的具現化所設計：

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* 使用旗標來防止多個並行作業：

  ```csharp
  if (Loading)
  {
      return;
  }
  try 
  {
      Loading = true;
      // operations go here
  }
  finally 
  {
      Loading = false;
  }
  ```

* 對於利用 EF Core 的 [變更追蹤](/ef/core/querying/tracking) 或 [並行存取控制](/ef/core/saving/concurrency)的較長時間作業，請 [將內容約制限定在元件的存留期](#scope-to-the-component-lifetime)。

### <a name="create-new-dbcontext-instances"></a>建立新的 DbCoNtext 實例

建立新實例的最快方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 來建立新的實例。 不過，有幾個案例可能需要解析其他相依性。 例如，您可能會想要使用 [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 來設定內容。 

建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。 範例應用程式會在中執行自己的 factory `Data/DbContextFactory.cs` 。 

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。 程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

Factory 會插入元件中，並用來建立新的實例。 例如，在 `Pages/Index.razor` 中：

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a>元件存留期的範圍

您可能想要建立一個 <xref:Microsoft.EntityFrameworkCore.DbContext> 存在於元件存留期的。 這可讓您使用它做為 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) ，並利用內建功能，例如變更追蹤和並行解析。
您可以使用 factory 來建立內容，並在元件的存留期間追蹤它。 首先，執行 <xref:System.IDisposable> 並插入 factory，如下所示 `Pages/EditContact.razor` ：

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

範例應用程式可確保在處置元件時，會處置連絡人：

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

最後， `OnInitializedAsync` 會覆寫以建立新的內容。 在範例應用程式中，會 `OnInitializedAsync` 以相同的方法載入連絡人：

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

## <a name="additional-resources"></a>其他資源

> [EF Core 檔](/ef/)
