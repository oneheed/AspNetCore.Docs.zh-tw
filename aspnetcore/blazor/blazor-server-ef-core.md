---
title: 'ASP.NET Core Blazor Server 與 Entity Framework Core (EFCore) '
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
ms.openlocfilehash: 10fc0afe84065f2c226d1e9c2f4314142369613a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97011880"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="8a45b-103">ASP.NET Core Blazor Server 與 Entity Framework Core (EFCore) </span><span class="sxs-lookup"><span data-stu-id="8a45b-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="8a45b-104">依據： [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="8a45b-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8a45b-105">Blazor Server 是具狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="8a45b-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="8a45b-106">應用程式會維持與伺服器的持續連線，而且使用者的狀態會保留在 *伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="8a45b-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="8a45b-107">使用者狀態的其中一個範例是在相依性插入中保存的資料 [ (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為線路。</span><span class="sxs-lookup"><span data-stu-id="8a45b-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="8a45b-108">提供的唯一應用程式模型 Blazor Server 需要使用 Entity Framework Core 的特殊方法。</span><span class="sxs-lookup"><span data-stu-id="8a45b-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="8a45b-109">本文說明應用程式中的 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="8a45b-110">Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="8a45b-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="8a45b-111">在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="8a45b-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="8a45b-112">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="8a45b-112">Sample app</span></span></h2>

<span data-ttu-id="8a45b-113">範例應用程式已建立為 Blazor Server 使用 EF Core 之應用程式的參考。</span><span class="sxs-lookup"><span data-stu-id="8a45b-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="8a45b-114">範例應用程式包含具有排序和篩選、刪除、新增和更新作業的方格。</span><span class="sxs-lookup"><span data-stu-id="8a45b-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="8a45b-115">此範例示範如何使用 EF Core 來處理開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="8a45b-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="8a45b-116">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="8a45b-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a45b-117">此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，以便在任何平臺上使用。</span><span class="sxs-lookup"><span data-stu-id="8a45b-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="8a45b-118">此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="8a45b-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="8a45b-119">這是在中設定 `appsettings.Development.json` ：</span><span class="sxs-lookup"><span data-stu-id="8a45b-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="8a45b-120">Grid、add 和 view 元件會使用「每一作業的內容」模式，其中會為每個作業建立內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="8a45b-121">編輯元件會使用「每一元件的內容」模式，其中會為每個元件建立內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="8a45b-122">本主題中的部分程式碼範例需要未顯示的命名空間和服務。</span><span class="sxs-lookup"><span data-stu-id="8a45b-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="8a45b-123">若要檢查完整運作的程式碼，包括範例的必要和指示詞 [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor ，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="8a45b-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="8a45b-124">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="8a45b-124">Database access</span></span></h2>

<span data-ttu-id="8a45b-125">EF Core 依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取](/ef/core/miscellaneous/configuring-dbcontext) 的方法，並作為 [*工作單位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="8a45b-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="8a45b-126">EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的擴充功能，這些應用程式預設會將內容註冊為 *範圍* 服務。</span><span class="sxs-lookup"><span data-stu-id="8a45b-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="8a45b-127">在 Blazor Server 應用程式中，已設定範圍的服務註冊可能會有問題，因為該實例會在使用者的線路內跨元件共用。</span><span class="sxs-lookup"><span data-stu-id="8a45b-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="8a45b-128"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了並行使用而設計。</span><span class="sxs-lookup"><span data-stu-id="8a45b-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="8a45b-129">現有的存留期不適當，原因如下：</span><span class="sxs-lookup"><span data-stu-id="8a45b-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="8a45b-130">在應用程式的所有使用者之間 **單獨** 共用狀態，並導致不當的並行使用。</span><span class="sxs-lookup"><span data-stu-id="8a45b-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="8a45b-131">限 **域** (預設) 在相同使用者的元件之間提出類似的問題。</span><span class="sxs-lookup"><span data-stu-id="8a45b-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="8a45b-132">**暫時性** 會在每個要求中產生新的實例;但由於元件的存留期可能很長，因此可能會產生比預期更長的內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="8a45b-133">下列建議旨在提供在應用程式中使用 EF Core 的一致方法 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="8a45b-134">根據預設，每個作業都應考慮使用一個內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="8a45b-135">內容是針對快速、低負荷的具現化所設計：</span><span class="sxs-lookup"><span data-stu-id="8a45b-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="8a45b-136">使用旗標來防止多個平行作業：</span><span class="sxs-lookup"><span data-stu-id="8a45b-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="8a45b-137">將作業放 `Loading = true;` 在區塊中的行後面 `try` 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="8a45b-138">對於利用 EF Core [變更追蹤](/ef/core/querying/tracking) 或 [並行控制](/ef/core/saving/concurrency)的較長時間的作業，請將 [內容約制設為元件的存留期](#scope-to-the-component-lifetime-5x)。</span><span class="sxs-lookup"><span data-stu-id="8a45b-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="8a45b-139">新的 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="8a45b-139">New DbContext instances</span></span></h3>

<span data-ttu-id="8a45b-140">建立新實例的最快速方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="8a45b-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="8a45b-141">不過，有幾個案例可能需要解析其他相依性。</span><span class="sxs-lookup"><span data-stu-id="8a45b-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="8a45b-142">例如，您可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 設定內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="8a45b-143">建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。</span><span class="sxs-lookup"><span data-stu-id="8a45b-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="8a45b-144">EF Core 5.0 或更新版本會提供內建的 factory 來建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="8a45b-145">下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。</span><span class="sxs-lookup"><span data-stu-id="8a45b-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="8a45b-146">程式碼會使用 [擴充方法 (`AddDbContextFactory`) ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) 來設定 DI 的資料庫 factory，並提供預設選項：</span><span class="sxs-lookup"><span data-stu-id="8a45b-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="8a45b-147">Factory 會插入元件，並用來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="8a45b-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="8a45b-148">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="8a45b-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="8a45b-149">`Wrapper` 是元件的 [元件參考](xref:blazor/components/index#capture-references-to-components) `GridWrapper` 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="8a45b-150">請參閱 `Index` `Pages/Index.razor` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 () 元件。</span><span class="sxs-lookup"><span data-stu-id="8a45b-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="8a45b-151"><xref:Microsoft.EntityFrameworkCore.DbContext>您可以使用 factory 來建立新的實例，讓您設定每個的連接字串 `DbContext` ，例如當您使用[ASP.NET Core 的 Identity 模型](xref:security/authentication/customize_identity_model)時：</span><span class="sxs-lookup"><span data-stu-id="8a45b-151">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="8a45b-152">將範圍設為元件存留期</span><span class="sxs-lookup"><span data-stu-id="8a45b-152">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="8a45b-153">您可能會想要建立在 <xref:Microsoft.EntityFrameworkCore.DbContext> 元件存留期存在的。</span><span class="sxs-lookup"><span data-stu-id="8a45b-153">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="8a45b-154">這可讓您將它當作 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) 使用，並利用內建的功能，例如變更追蹤和並行解析。</span><span class="sxs-lookup"><span data-stu-id="8a45b-154">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="8a45b-155">您可以使用 factory 來建立內容，並在元件的存留期追蹤它。</span><span class="sxs-lookup"><span data-stu-id="8a45b-155">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="8a45b-156">首先，請依照 <xref:System.IDisposable> 下列方式，執行並插入 `Pages/EditContact.razor` factory：</span><span class="sxs-lookup"><span data-stu-id="8a45b-156">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="8a45b-157">範例應用程式可確保在元件處置時處置內容：</span><span class="sxs-lookup"><span data-stu-id="8a45b-157">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="8a45b-158">最後， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 會覆寫以建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-158">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="8a45b-159">在範例應用程式中， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 以相同的方法載入連絡人：</span><span class="sxs-lookup"><span data-stu-id="8a45b-159">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="8a45b-160">啟用敏感資料記錄</span><span class="sxs-lookup"><span data-stu-id="8a45b-160">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="8a45b-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包含例外狀況訊息和架構記錄中的應用程式資料。</span><span class="sxs-lookup"><span data-stu-id="8a45b-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="8a45b-162">記錄的資料可以包含指派給實體實例屬性的值，以及傳送至資料庫之命令的參數值。</span><span class="sxs-lookup"><span data-stu-id="8a45b-162">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="8a45b-163">記錄資料的 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> **安全性風險**，因為它可能會在記錄針對資料庫執行的 SQL 語句時， (PII) 公開密碼和其他個人識別資訊。</span><span class="sxs-lookup"><span data-stu-id="8a45b-163">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="8a45b-164">建議您只 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 針對開發和測試啟用：</span><span class="sxs-lookup"><span data-stu-id="8a45b-164">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

<span data-ttu-id="8a45b-165">Blazor Server 是具狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="8a45b-165">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="8a45b-166">應用程式會維持與伺服器的持續連線，而且使用者的狀態會保留在 *伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="8a45b-166">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="8a45b-167">使用者狀態的其中一個範例是在相依性插入中保存的資料 [ (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為線路。</span><span class="sxs-lookup"><span data-stu-id="8a45b-167">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="8a45b-168">提供的唯一應用程式模型 Blazor Server 需要使用 Entity Framework Core 的特殊方法。</span><span class="sxs-lookup"><span data-stu-id="8a45b-168">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="8a45b-169">本文說明應用程式中的 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-169">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="8a45b-170">Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="8a45b-170">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="8a45b-171">在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="8a45b-171">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="8a45b-172">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="8a45b-172">Sample app</span></span></h2>

<span data-ttu-id="8a45b-173">範例應用程式已建立為 Blazor Server 使用 EF Core 之應用程式的參考。</span><span class="sxs-lookup"><span data-stu-id="8a45b-173">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="8a45b-174">範例應用程式包含具有排序和篩選、刪除、新增和更新作業的方格。</span><span class="sxs-lookup"><span data-stu-id="8a45b-174">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="8a45b-175">此範例示範如何使用 EF Core 來處理開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="8a45b-175">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="8a45b-176">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="8a45b-176">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a45b-177">此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，以便在任何平臺上使用。</span><span class="sxs-lookup"><span data-stu-id="8a45b-177">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="8a45b-178">此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="8a45b-178">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="8a45b-179">這是在中設定 `appsettings.Development.json` ：</span><span class="sxs-lookup"><span data-stu-id="8a45b-179">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="8a45b-180">Grid、add 和 view 元件會使用「每一作業的內容」模式，其中會為每個作業建立內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-180">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="8a45b-181">編輯元件會使用「每一元件的內容」模式，其中會為每個元件建立內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-181">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="8a45b-182">本主題中的部分程式碼範例需要未顯示的命名空間和服務。</span><span class="sxs-lookup"><span data-stu-id="8a45b-182">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="8a45b-183">若要檢查完整運作的程式碼，包括範例的必要和指示詞 [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor ，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="8a45b-183">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="8a45b-184">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="8a45b-184">Database access</span></span></h2>

<span data-ttu-id="8a45b-185">EF Core 依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取](/ef/core/miscellaneous/configuring-dbcontext) 的方法，並作為 [*工作單位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="8a45b-185">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="8a45b-186">EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的擴充功能，這些應用程式預設會將內容註冊為 *範圍* 服務。</span><span class="sxs-lookup"><span data-stu-id="8a45b-186">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="8a45b-187">在 Blazor Server 應用程式中，這可能會造成問題，因為該實例會在使用者的線路內跨元件共用。</span><span class="sxs-lookup"><span data-stu-id="8a45b-187">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="8a45b-188"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了並行使用而設計。</span><span class="sxs-lookup"><span data-stu-id="8a45b-188"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="8a45b-189">現有的存留期不適當，原因如下：</span><span class="sxs-lookup"><span data-stu-id="8a45b-189">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="8a45b-190">在應用程式的所有使用者之間 **單獨** 共用狀態，並導致不當的並行使用。</span><span class="sxs-lookup"><span data-stu-id="8a45b-190">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="8a45b-191">限 **域** (預設) 在相同使用者的元件之間提出類似的問題。</span><span class="sxs-lookup"><span data-stu-id="8a45b-191">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="8a45b-192">**暫時性** 會在每個要求中產生新的實例;但由於元件的存留期可能很長，因此可能會產生比預期更長的內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-192">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="8a45b-193">下列建議旨在提供在應用程式中使用 EF Core 的一致方法 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-193">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="8a45b-194">根據預設，每個作業都應考慮使用一個內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-194">By default, consider using one context per operation.</span></span> <span data-ttu-id="8a45b-195">內容是針對快速、低負荷的具現化所設計：</span><span class="sxs-lookup"><span data-stu-id="8a45b-195">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="8a45b-196">使用旗標來防止多個平行作業：</span><span class="sxs-lookup"><span data-stu-id="8a45b-196">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="8a45b-197">將作業放 `Loading = true;` 在區塊中的行後面 `try` 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-197">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="8a45b-198">對於利用 EF Core [變更追蹤](/ef/core/querying/tracking) 或 [並行控制](/ef/core/saving/concurrency)的較長時間的作業，請將 [內容約制設為元件的存留期](#scope-to-the-component-lifetime-3x)。</span><span class="sxs-lookup"><span data-stu-id="8a45b-198">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="8a45b-199">新的 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="8a45b-199">New DbContext instances</span></span></h3>

<span data-ttu-id="8a45b-200">建立新實例的最快速方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="8a45b-200">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="8a45b-201">不過，有幾個案例可能需要解析其他相依性。</span><span class="sxs-lookup"><span data-stu-id="8a45b-201">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="8a45b-202">例如，您可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 設定內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-202">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="8a45b-203">建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。</span><span class="sxs-lookup"><span data-stu-id="8a45b-203">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="8a45b-204">範例應用程式會在中執行自己的 factory `Data/DbContextFactory.cs` 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-204">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="8a45b-205">在先前的 factory 中：</span><span class="sxs-lookup"><span data-stu-id="8a45b-205">In the preceding factory:</span></span>

* <span data-ttu-id="8a45b-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 透過服務提供者滿足任何相依性。</span><span class="sxs-lookup"><span data-stu-id="8a45b-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="8a45b-207">`IDbContextFactory` 可在 EF Core ASP.NET Core 5.0 或更新版本中使用，因此介面會 [在 ASP.NET Core 3.x 的範例應用程式中執行](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)。</span><span class="sxs-lookup"><span data-stu-id="8a45b-207">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="8a45b-208">下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。</span><span class="sxs-lookup"><span data-stu-id="8a45b-208">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="8a45b-209">程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：</span><span class="sxs-lookup"><span data-stu-id="8a45b-209">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="8a45b-210">Factory 會插入元件，並用來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="8a45b-210">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="8a45b-211">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="8a45b-211">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="8a45b-212">`Wrapper` 是元件的 [元件參考](xref:blazor/components/index#capture-references-to-components) `GridWrapper` 。</span><span class="sxs-lookup"><span data-stu-id="8a45b-212">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="8a45b-213">請參閱 `Index` `Pages/Index.razor` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 () 元件。</span><span class="sxs-lookup"><span data-stu-id="8a45b-213">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="8a45b-214">您 <xref:Microsoft.EntityFrameworkCore.DbContext> 可以使用處理站建立新的實例，以讓您設定每個連接字串 `DbContext` ，例如當您使用 [ASP.NET Core 的 Identity 模型] 時 ) # B1 x：安全性/驗證/customize_identity_model) ：</span><span class="sxs-lookup"><span data-stu-id="8a45b-214">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="8a45b-215">將範圍設為元件存留期</span><span class="sxs-lookup"><span data-stu-id="8a45b-215">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="8a45b-216">您可能會想要建立在 <xref:Microsoft.EntityFrameworkCore.DbContext> 元件存留期存在的。</span><span class="sxs-lookup"><span data-stu-id="8a45b-216">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="8a45b-217">這可讓您將它當作 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) 使用，並利用內建的功能，例如變更追蹤和並行解析。</span><span class="sxs-lookup"><span data-stu-id="8a45b-217">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="8a45b-218">您可以使用 factory 來建立內容，並在元件的存留期追蹤它。</span><span class="sxs-lookup"><span data-stu-id="8a45b-218">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="8a45b-219">首先，請依照 <xref:System.IDisposable> 下列方式，執行並插入 `Pages/EditContact.razor` factory：</span><span class="sxs-lookup"><span data-stu-id="8a45b-219">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="8a45b-220">範例應用程式可確保在元件處置時處置內容：</span><span class="sxs-lookup"><span data-stu-id="8a45b-220">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="8a45b-221">最後， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 會覆寫以建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="8a45b-221">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="8a45b-222">在範例應用程式中， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 以相同的方法載入連絡人：</span><span class="sxs-lookup"><span data-stu-id="8a45b-222">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="8a45b-223">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="8a45b-223">In the preceding example:</span></span>

* <span data-ttu-id="8a45b-224">當 `Busy` 設定為時 `true` ，非同步作業可能會開始。</span><span class="sxs-lookup"><span data-stu-id="8a45b-224">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="8a45b-225">當 `Busy` 設回時 `false` ，非同步作業應完成。</span><span class="sxs-lookup"><span data-stu-id="8a45b-225">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="8a45b-226">將其他錯誤處理邏輯放在 `catch` 區塊中。</span><span class="sxs-lookup"><span data-stu-id="8a45b-226">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="8a45b-227">啟用敏感資料記錄</span><span class="sxs-lookup"><span data-stu-id="8a45b-227">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="8a45b-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包含例外狀況訊息和架構記錄中的應用程式資料。</span><span class="sxs-lookup"><span data-stu-id="8a45b-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="8a45b-229">記錄的資料可以包含指派給實體實例屬性的值，以及傳送至資料庫之命令的參數值。</span><span class="sxs-lookup"><span data-stu-id="8a45b-229">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="8a45b-230">記錄資料的 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> **安全性風險**，因為它可能會在記錄針對資料庫執行的 SQL 語句時， (PII) 公開密碼和其他個人識別資訊。</span><span class="sxs-lookup"><span data-stu-id="8a45b-230">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="8a45b-231">建議您只 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 針對開發和測試啟用：</span><span class="sxs-lookup"><span data-stu-id="8a45b-231">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="8a45b-232">其他資源</span><span class="sxs-lookup"><span data-stu-id="8a45b-232">Additional resources</span></span>

* [<span data-ttu-id="8a45b-233">EF Core 檔</span><span class="sxs-lookup"><span data-stu-id="8a45b-233">EF Core documentation</span></span>](/ef/)
