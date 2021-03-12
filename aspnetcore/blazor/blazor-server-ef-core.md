---
title: 'ASP.NET Core Blazor Server 與 Entity Framework core (EFCore) '
author: JeremyLikness
description: 在應用程式中使用 EF Core 的指引 Blazor Server 。
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 53d276db996304852d69566584e43d47aa73f921
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586549"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server 與 Entity Framework core (EFCore) 

:::moniker range=">= aspnetcore-5.0"

Blazor Server 是具狀態的應用程式架構。 應用程式會維持與伺服器的持續連線，而且使用者的狀態會保留在 *伺服器的記憶體中。* 使用者狀態的其中一個範例是在相依性插入中保存的資料 [ (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為線路。 提供的唯一應用程式模型 Blazor Server 需要使用 Entity Framework Core 的特殊方法。

> [!NOTE]
> 本文探討應用程式中的 EF Core Blazor Server 。 Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。 在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。

<h2 id="sample-app-5x">範例應用程式</h2>

範例應用程式已建立為 Blazor Server 使用 EF Core 的應用程式參考。 範例應用程式包含具有排序和篩選、刪除、新增和更新作業的方格。 此範例示範如何使用 EF Core 來處理開放式平行存取。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) 

此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，以便在任何平臺上使用。 此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。 這是在中設定 `appsettings.Development.json` ：

[!code-json[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Grid、add 和 view 元件會使用「每一作業的內容」模式，其中會為每個作業建立內容。 編輯元件會使用「每一元件的內容」模式，其中會為每個元件建立內容。

> [!NOTE]
> 本主題中的部分程式碼範例需要未顯示的命名空間和服務。 若要檢查完整運作的程式碼，包括範例的必要和指示詞 [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor ，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)。

<h2 id="database-access-5x">資料庫存取</h2>

EF Core 會依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取](/ef/core/miscellaneous/configuring-dbcontext) 的方法，並作為 [*工作單位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)。 EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的擴充功能，這些應用程式預設會將內容註冊為 *範圍* 服務。 在 Blazor Server 應用程式中，已設定範圍的服務註冊可能會有問題，因為該實例會在使用者的線路內跨元件共用。 <xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了並行使用而設計。 現有的存留期不適當，原因如下：

* 在應用程式的所有使用者之間 **單獨** 共用狀態，並導致不當的並行使用。
* 限 **域** (預設) 在相同使用者的元件之間提出類似的問題。
* **暫時性** 會在每個要求中產生新的實例;但由於元件的存留期可能很長，因此可能會產生比預期更長的內容。

下列建議旨在提供在應用程式中使用 EF Core 的一致方法 Blazor Server 。

* 根據預設，每個作業都應考慮使用一個內容。 內容是針對快速、低負荷的具現化所設計：

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* 使用旗標來防止多個平行作業：

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  將作業放 `Loading = true;` 在區塊中的行後面 `try` 。

* 針對使用 EF Core 的 [變更追蹤](/ef/core/querying/tracking) 或 [並行控制](/ef/core/saving/concurrency)的較長存留作業，請將 [內容約制設為元件的存留期](#scope-to-the-component-lifetime-5x)。

<h3 id="new-dbcontext-instances-5x">新的 DbCoNtext 實例</h3>

建立新實例的最快速方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 建立新的實例。 不過，有幾個案例可能需要解析其他相依性。 例如，您可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 設定內容。

建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。 EF Core 5.0 或更新版本提供內建的 factory 來建立新的內容。

下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。 程式碼會使用 [擴充方法 (`AddDbContextFactory`) ](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) 來設定 DI 的資料庫 factory，並提供預設選項：

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Factory 會插入元件，並用來建立新的實例。 例如，在 `Pages/Index.razor` 中：

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` 是元件的 [元件參考](xref:blazor/components/index#capture-references-to-components) `GridWrapper` 。 請參閱 `Index` `Pages/Index.razor` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 () 元件。

<xref:Microsoft.EntityFrameworkCore.DbContext>您可以使用處理站建立新的實例，讓您設定每個的連接字串 `DbContext` ，例如當您使用[ASP.NET Core 的 Identity 模型](xref:security/authentication/customize_identity_model)時：

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x">將範圍設為元件存留期</h3>

您可能會想要建立在 <xref:Microsoft.EntityFrameworkCore.DbContext> 元件存留期存在的。 這可讓您將它當作 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) 使用，並利用內建的功能，例如變更追蹤和並行解析。
您可以使用 factory 來建立內容，並在元件的存留期追蹤它。 首先，請依照 <xref:System.IDisposable> 下列方式，執行並插入 `Pages/EditContact.razor` factory：

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

範例應用程式可確保在元件處置時處置內容：

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

最後， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 會覆寫以建立新的內容。 在範例應用程式中， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 以相同的方法載入連絡人：

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging">啟用敏感資料記錄</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包含例外狀況訊息和架構記錄中的應用程式資料。 記錄的資料可以包含指派給實體實例屬性的值，以及傳送至資料庫之命令的參數值。 記錄資料的 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> **安全性風險**，因為它可能會在記錄針對資料庫執行的 SQL 語句時， (PII) 公開密碼和其他個人識別資訊。

建議您只 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 針對開發和測試啟用：

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server 是具狀態的應用程式架構。 應用程式會維持與伺服器的持續連線，而且使用者的狀態會保留在 *伺服器的記憶體中。* 使用者狀態的其中一個範例是在相依性插入中保存的資料 [ (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為線路。 提供的唯一應用程式模型 Blazor Server 需要使用 Entity Framework Core 的特殊方法。

> [!NOTE]
> 本文探討應用程式中的 EF Core Blazor Server 。 Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。 在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。

<h2 id="sample-app-3x">範例應用程式</h2>

範例應用程式已建立為 Blazor Server 使用 EF Core 的應用程式參考。 範例應用程式包含具有排序和篩選、刪除、新增和更新作業的方格。 此範例示範如何使用 EF Core 來處理開放式平行存取。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) 

此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，以便在任何平臺上使用。 此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。 這是在中設定 `appsettings.Development.json` ：

[!code-json[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Grid、add 和 view 元件會使用「每一作業的內容」模式，其中會為每個作業建立內容。 編輯元件會使用「每一元件的內容」模式，其中會為每個元件建立內容。

> [!NOTE]
> 本主題中的部分程式碼範例需要未顯示的命名空間和服務。 若要檢查完整運作的程式碼，包括範例的必要和指示詞 [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor ，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)。

<h2 id="database-access-3x">資料庫存取</h2>

EF Core 會依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取](/ef/core/miscellaneous/configuring-dbcontext) 的方法，並作為 [*工作單位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)。 EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的擴充功能，這些應用程式預設會將內容註冊為 *範圍* 服務。 在 Blazor Server 應用程式中，這可能會造成問題，因為該實例會在使用者的線路內跨元件共用。 <xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了並行使用而設計。 現有的存留期不適當，原因如下：

* 在應用程式的所有使用者之間 **單獨** 共用狀態，並導致不當的並行使用。
* 限 **域** (預設) 在相同使用者的元件之間提出類似的問題。
* **暫時性** 會在每個要求中產生新的實例;但由於元件的存留期可能很長，因此可能會產生比預期更長的內容。

下列建議旨在提供在應用程式中使用 EF Core 的一致方法 Blazor Server 。

* 根據預設，每個作業都應考慮使用一個內容。 內容是針對快速、低負荷的具現化所設計：

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* 使用旗標來防止多個平行作業：

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  將作業放 `Loading = true;` 在區塊中的行後面 `try` 。

* 針對使用 EF Core 的 [變更追蹤](/ef/core/querying/tracking) 或 [並行控制](/ef/core/saving/concurrency)的較長存留作業，請將 [內容約制設為元件的存留期](#scope-to-the-component-lifetime-3x)。

<h3 id="new-dbcontext-instances-3x">新的 DbCoNtext 實例</h3>

建立新實例的最快速方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 建立新的實例。 不過，有幾個案例可能需要解析其他相依性。 例如，您可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 設定內容。

建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。 範例應用程式會在中執行自己的 factory `Data/DbContextFactory.cs` 。

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

在先前的 factory 中：

* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 透過服務提供者滿足任何相依性。
* `IDbContextFactory` 適用于 EF Core ASP.NET Core 5.0 或更新版本，因此介面會 [在適用于 ASP.NET Core 3.x 的範例應用程式中執行](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)。

下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。 程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Factory 會插入元件，並用來建立新的實例。 例如，在 `Pages/Index.razor` 中：

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` 是元件的 [元件參考](xref:blazor/components/index#capture-references-to-components) `GridWrapper` 。 請參閱 `Index` `Pages/Index.razor` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 () 元件。

您 <xref:Microsoft.EntityFrameworkCore.DbContext> 可以使用處理站來建立新的實例，以讓您設定每個連接字串 `DbContext` ，例如當您使用 [ASP.NET Core 的 Identity 模型] )  (x： security/authentication/customize_identity_model) 時：

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x">將範圍設為元件存留期</h3>

您可能會想要建立在 <xref:Microsoft.EntityFrameworkCore.DbContext> 元件存留期存在的。 這可讓您將它當作 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) 使用，並利用內建的功能，例如變更追蹤和並行解析。
您可以使用 factory 來建立內容，並在元件的存留期追蹤它。 首先，請依照 <xref:System.IDisposable> 下列方式，執行並插入 `Pages/EditContact.razor` factory：

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

範例應用程式可確保在元件處置時處置內容：

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

最後， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 會覆寫以建立新的內容。 在範例應用程式中， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 以相同的方法載入連絡人：

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

在上述範例中：

* 當 `Busy` 設定為時 `true` ，非同步作業可能會開始。 當 `Busy` 設回時 `false` ，非同步作業應完成。
* 將其他錯誤處理邏輯放在 `catch` 區塊中。

<h3 id="enable-sensitive-data-logging">啟用敏感資料記錄</h3>

<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包含例外狀況訊息和架構記錄中的應用程式資料。 記錄的資料可以包含指派給實體實例屬性的值，以及傳送至資料庫之命令的參數值。 記錄資料的 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> **安全性風險**，因為它可能會在記錄針對資料庫執行的 SQL 語句時， (PII) 公開密碼和其他個人識別資訊。

建議您只 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 針對開發和測試啟用：

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a>其他資源

* [EF Core 檔](/ef/)
