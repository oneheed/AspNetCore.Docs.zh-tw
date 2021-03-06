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
ms.openlocfilehash: db70bafc85311f8ccbf87e123739ea99c8d8f3c5
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394573"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="98821-103">ASP.NET Core Blazor Server 與 Entity Framework core (EFCore) </span><span class="sxs-lookup"><span data-stu-id="98821-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="98821-104">Blazor Server 是具狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="98821-104">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="98821-105">應用程式會維持與伺服器的持續連線，而且使用者的狀態會保留在 *伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="98821-105">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="98821-106">使用者狀態的其中一個範例是在相依性插入中保存的資料 [ (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為線路。</span><span class="sxs-lookup"><span data-stu-id="98821-106">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="98821-107">提供的唯一應用程式模型 Blazor Server 需要使用 Entity Framework Core 的特殊方法。</span><span class="sxs-lookup"><span data-stu-id="98821-107">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="98821-108">本文探討應用程式中的 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="98821-108">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="98821-109">Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="98821-109">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="98821-110">在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="98821-110">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="98821-111">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="98821-111">Sample app</span></span></h2>

<span data-ttu-id="98821-112">範例應用程式已建立為 Blazor Server 使用 EF Core 的應用程式參考。</span><span class="sxs-lookup"><span data-stu-id="98821-112">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="98821-113">範例應用程式包含具有排序和篩選、刪除、新增和更新作業的方格。</span><span class="sxs-lookup"><span data-stu-id="98821-113">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="98821-114">此範例示範如何使用 EF Core 來處理開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="98821-114">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="98821-115">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="98821-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="98821-116">此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，以便在任何平臺上使用。</span><span class="sxs-lookup"><span data-stu-id="98821-116">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="98821-117">此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="98821-117">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="98821-118">這是在中設定 `appsettings.Development.json` ：</span><span class="sxs-lookup"><span data-stu-id="98821-118">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="98821-119">Grid、add 和 view 元件會使用「每一作業的內容」模式，其中會為每個作業建立內容。</span><span class="sxs-lookup"><span data-stu-id="98821-119">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="98821-120">編輯元件會使用「每一元件的內容」模式，其中會為每個元件建立內容。</span><span class="sxs-lookup"><span data-stu-id="98821-120">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="98821-121">本主題中的部分程式碼範例需要未顯示的命名空間和服務。</span><span class="sxs-lookup"><span data-stu-id="98821-121">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="98821-122">若要檢查完整運作的程式碼，包括範例的必要和指示詞 [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor ，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="98821-122">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="98821-123">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="98821-123">Database access</span></span></h2>

<span data-ttu-id="98821-124">EF Core 會依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取](/ef/core/miscellaneous/configuring-dbcontext) 的方法，並作為 [*工作單位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="98821-124">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="98821-125">EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的擴充功能，這些應用程式預設會將內容註冊為 *範圍* 服務。</span><span class="sxs-lookup"><span data-stu-id="98821-125">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="98821-126">在 Blazor Server 應用程式中，已設定範圍的服務註冊可能會有問題，因為該實例會在使用者的線路內跨元件共用。</span><span class="sxs-lookup"><span data-stu-id="98821-126">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="98821-127"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了並行使用而設計。</span><span class="sxs-lookup"><span data-stu-id="98821-127"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="98821-128">現有的存留期不適當，原因如下：</span><span class="sxs-lookup"><span data-stu-id="98821-128">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="98821-129">在應用程式的所有使用者之間 **單獨** 共用狀態，並導致不當的並行使用。</span><span class="sxs-lookup"><span data-stu-id="98821-129">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="98821-130">限 **域** (預設) 在相同使用者的元件之間提出類似的問題。</span><span class="sxs-lookup"><span data-stu-id="98821-130">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="98821-131">**暫時性** 會在每個要求中產生新的實例;但由於元件的存留期可能很長，因此可能會產生比預期更長的內容。</span><span class="sxs-lookup"><span data-stu-id="98821-131">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="98821-132">下列建議旨在提供在應用程式中使用 EF Core 的一致方法 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="98821-132">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="98821-133">根據預設，每個作業都應考慮使用一個內容。</span><span class="sxs-lookup"><span data-stu-id="98821-133">By default, consider using one context per operation.</span></span> <span data-ttu-id="98821-134">內容是針對快速、低負荷的具現化所設計：</span><span class="sxs-lookup"><span data-stu-id="98821-134">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="98821-135">使用旗標來防止多個平行作業：</span><span class="sxs-lookup"><span data-stu-id="98821-135">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="98821-136">將作業放 `Loading = true;` 在區塊中的行後面 `try` 。</span><span class="sxs-lookup"><span data-stu-id="98821-136">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="98821-137">針對使用 EF Core 的 [變更追蹤](/ef/core/querying/tracking) 或 [並行控制](/ef/core/saving/concurrency)的較長存留作業，請將 [內容約制設為元件的存留期](#scope-to-the-component-lifetime-5x)。</span><span class="sxs-lookup"><span data-stu-id="98821-137">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="98821-138">新的 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="98821-138">New DbContext instances</span></span></h3>

<span data-ttu-id="98821-139">建立新實例的最快速方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="98821-139">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="98821-140">不過，有幾個案例可能需要解析其他相依性。</span><span class="sxs-lookup"><span data-stu-id="98821-140">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="98821-141">例如，您可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 設定內容。</span><span class="sxs-lookup"><span data-stu-id="98821-141">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="98821-142">建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。</span><span class="sxs-lookup"><span data-stu-id="98821-142">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="98821-143">EF Core 5.0 或更新版本提供內建的 factory 來建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="98821-143">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="98821-144">下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。</span><span class="sxs-lookup"><span data-stu-id="98821-144">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="98821-145">程式碼會使用 [擴充方法 (`AddDbContextFactory`) ](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) 來設定 DI 的資料庫 factory，並提供預設選項：</span><span class="sxs-lookup"><span data-stu-id="98821-145">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="98821-146">Factory 會插入元件，並用來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="98821-146">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="98821-147">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="98821-147">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="98821-148">`Wrapper` 是元件的 [元件參考](xref:blazor/components/index#capture-references-to-components) `GridWrapper` 。</span><span class="sxs-lookup"><span data-stu-id="98821-148">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="98821-149">請參閱 `Index` `Pages/Index.razor` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 () 元件。</span><span class="sxs-lookup"><span data-stu-id="98821-149">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="98821-150"><xref:Microsoft.EntityFrameworkCore.DbContext>您可以使用處理站建立新的實例，讓您設定每個的連接字串 `DbContext` ，例如當您使用[ASP.NET Core 的 Identity 模型](xref:security/authentication/customize_identity_model)時：</span><span class="sxs-lookup"><span data-stu-id="98821-150">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="98821-151">將範圍設為元件存留期</span><span class="sxs-lookup"><span data-stu-id="98821-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="98821-152">您可能會想要建立在 <xref:Microsoft.EntityFrameworkCore.DbContext> 元件存留期存在的。</span><span class="sxs-lookup"><span data-stu-id="98821-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="98821-153">這可讓您將它當作 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) 使用，並利用內建的功能，例如變更追蹤和並行解析。</span><span class="sxs-lookup"><span data-stu-id="98821-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="98821-154">您可以使用 factory 來建立內容，並在元件的存留期追蹤它。</span><span class="sxs-lookup"><span data-stu-id="98821-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="98821-155">首先，請依照 <xref:System.IDisposable> 下列方式，執行並插入 `Pages/EditContact.razor` factory：</span><span class="sxs-lookup"><span data-stu-id="98821-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="98821-156">範例應用程式可確保在元件處置時處置內容：</span><span class="sxs-lookup"><span data-stu-id="98821-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="98821-157">最後， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 會覆寫以建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="98821-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="98821-158">在範例應用程式中， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 以相同的方法載入連絡人：</span><span class="sxs-lookup"><span data-stu-id="98821-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](~/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="98821-159">啟用敏感資料記錄</span><span class="sxs-lookup"><span data-stu-id="98821-159">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="98821-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包含例外狀況訊息和架構記錄中的應用程式資料。</span><span class="sxs-lookup"><span data-stu-id="98821-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="98821-161">記錄的資料可以包含指派給實體實例屬性的值，以及傳送至資料庫之命令的參數值。</span><span class="sxs-lookup"><span data-stu-id="98821-161">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="98821-162">記錄資料的 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> **安全性風險**，因為它可能會在記錄針對資料庫執行的 SQL 語句時， (PII) 公開密碼和其他個人識別資訊。</span><span class="sxs-lookup"><span data-stu-id="98821-162">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="98821-163">建議您只 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 針對開發和測試啟用：</span><span class="sxs-lookup"><span data-stu-id="98821-163">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

<span data-ttu-id="98821-164">Blazor Server 是具狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="98821-164">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="98821-165">應用程式會維持與伺服器的持續連線，而且使用者的狀態會保留在 *伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="98821-165">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="98821-166">使用者狀態的其中一個範例是在相依性插入中保存的資料 [ (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為線路。</span><span class="sxs-lookup"><span data-stu-id="98821-166">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="98821-167">提供的唯一應用程式模型 Blazor Server 需要使用 Entity Framework Core 的特殊方法。</span><span class="sxs-lookup"><span data-stu-id="98821-167">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="98821-168">本文探討應用程式中的 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="98821-168">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="98821-169">Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="98821-169">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="98821-170">在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="98821-170">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="98821-171">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="98821-171">Sample app</span></span></h2>

<span data-ttu-id="98821-172">範例應用程式已建立為 Blazor Server 使用 EF Core 的應用程式參考。</span><span class="sxs-lookup"><span data-stu-id="98821-172">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="98821-173">範例應用程式包含具有排序和篩選、刪除、新增和更新作業的方格。</span><span class="sxs-lookup"><span data-stu-id="98821-173">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="98821-174">此範例示範如何使用 EF Core 來處理開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="98821-174">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="98821-175">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="98821-175">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="98821-176">此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，以便在任何平臺上使用。</span><span class="sxs-lookup"><span data-stu-id="98821-176">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="98821-177">此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="98821-177">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="98821-178">這是在中設定 `appsettings.Development.json` ：</span><span class="sxs-lookup"><span data-stu-id="98821-178">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="98821-179">Grid、add 和 view 元件會使用「每一作業的內容」模式，其中會為每個作業建立內容。</span><span class="sxs-lookup"><span data-stu-id="98821-179">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="98821-180">編輯元件會使用「每一元件的內容」模式，其中會為每個元件建立內容。</span><span class="sxs-lookup"><span data-stu-id="98821-180">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="98821-181">本主題中的部分程式碼範例需要未顯示的命名空間和服務。</span><span class="sxs-lookup"><span data-stu-id="98821-181">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="98821-182">若要檢查完整運作的程式碼，包括範例的必要和指示詞 [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor ，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="98821-182">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="98821-183">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="98821-183">Database access</span></span></h2>

<span data-ttu-id="98821-184">EF Core 會依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取](/ef/core/miscellaneous/configuring-dbcontext) 的方法，並作為 [*工作單位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="98821-184">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="98821-185">EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的擴充功能，這些應用程式預設會將內容註冊為 *範圍* 服務。</span><span class="sxs-lookup"><span data-stu-id="98821-185">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="98821-186">在 Blazor Server 應用程式中，這可能會造成問題，因為該實例會在使用者的線路內跨元件共用。</span><span class="sxs-lookup"><span data-stu-id="98821-186">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="98821-187"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了並行使用而設計。</span><span class="sxs-lookup"><span data-stu-id="98821-187"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="98821-188">現有的存留期不適當，原因如下：</span><span class="sxs-lookup"><span data-stu-id="98821-188">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="98821-189">在應用程式的所有使用者之間 **單獨** 共用狀態，並導致不當的並行使用。</span><span class="sxs-lookup"><span data-stu-id="98821-189">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="98821-190">限 **域** (預設) 在相同使用者的元件之間提出類似的問題。</span><span class="sxs-lookup"><span data-stu-id="98821-190">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="98821-191">**暫時性** 會在每個要求中產生新的實例;但由於元件的存留期可能很長，因此可能會產生比預期更長的內容。</span><span class="sxs-lookup"><span data-stu-id="98821-191">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="98821-192">下列建議旨在提供在應用程式中使用 EF Core 的一致方法 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="98821-192">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="98821-193">根據預設，每個作業都應考慮使用一個內容。</span><span class="sxs-lookup"><span data-stu-id="98821-193">By default, consider using one context per operation.</span></span> <span data-ttu-id="98821-194">內容是針對快速、低負荷的具現化所設計：</span><span class="sxs-lookup"><span data-stu-id="98821-194">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="98821-195">使用旗標來防止多個平行作業：</span><span class="sxs-lookup"><span data-stu-id="98821-195">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="98821-196">將作業放 `Loading = true;` 在區塊中的行後面 `try` 。</span><span class="sxs-lookup"><span data-stu-id="98821-196">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="98821-197">針對使用 EF Core 的 [變更追蹤](/ef/core/querying/tracking) 或 [並行控制](/ef/core/saving/concurrency)的較長存留作業，請將 [內容約制設為元件的存留期](#scope-to-the-component-lifetime-3x)。</span><span class="sxs-lookup"><span data-stu-id="98821-197">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="98821-198">新的 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="98821-198">New DbContext instances</span></span></h3>

<span data-ttu-id="98821-199">建立新實例的最快速方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="98821-199">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="98821-200">不過，有幾個案例可能需要解析其他相依性。</span><span class="sxs-lookup"><span data-stu-id="98821-200">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="98821-201">例如，您可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 設定內容。</span><span class="sxs-lookup"><span data-stu-id="98821-201">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="98821-202">建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。</span><span class="sxs-lookup"><span data-stu-id="98821-202">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="98821-203">範例應用程式會在中執行自己的 factory `Data/DbContextFactory.cs` 。</span><span class="sxs-lookup"><span data-stu-id="98821-203">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="98821-204">在先前的 factory 中：</span><span class="sxs-lookup"><span data-stu-id="98821-204">In the preceding factory:</span></span>

* <span data-ttu-id="98821-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 透過服務提供者滿足任何相依性。</span><span class="sxs-lookup"><span data-stu-id="98821-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="98821-206">`IDbContextFactory` 適用于 EF Core ASP.NET Core 5.0 或更新版本，因此介面會 [在適用于 ASP.NET Core 3.x 的範例應用程式中執行](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)。</span><span class="sxs-lookup"><span data-stu-id="98821-206">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="98821-207">下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。</span><span class="sxs-lookup"><span data-stu-id="98821-207">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="98821-208">程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：</span><span class="sxs-lookup"><span data-stu-id="98821-208">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="98821-209">Factory 會插入元件，並用來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="98821-209">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="98821-210">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="98821-210">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="98821-211">`Wrapper` 是元件的 [元件參考](xref:blazor/components/index#capture-references-to-components) `GridWrapper` 。</span><span class="sxs-lookup"><span data-stu-id="98821-211">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="98821-212">請參閱 `Index` `Pages/Index.razor` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 () 元件。</span><span class="sxs-lookup"><span data-stu-id="98821-212">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="98821-213">您 <xref:Microsoft.EntityFrameworkCore.DbContext> 可以使用處理站來建立新的實例，以讓您設定每個連接字串 `DbContext` ，例如當您使用 [ASP.NET Core 的 Identity 模型] )  (x： security/authentication/customize_identity_model) 時：</span><span class="sxs-lookup"><span data-stu-id="98821-213">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="98821-214">將範圍設為元件存留期</span><span class="sxs-lookup"><span data-stu-id="98821-214">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="98821-215">您可能會想要建立在 <xref:Microsoft.EntityFrameworkCore.DbContext> 元件存留期存在的。</span><span class="sxs-lookup"><span data-stu-id="98821-215">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="98821-216">這可讓您將它當作 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) 使用，並利用內建的功能，例如變更追蹤和並行解析。</span><span class="sxs-lookup"><span data-stu-id="98821-216">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="98821-217">您可以使用 factory 來建立內容，並在元件的存留期追蹤它。</span><span class="sxs-lookup"><span data-stu-id="98821-217">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="98821-218">首先，請依照 <xref:System.IDisposable> 下列方式，執行並插入 `Pages/EditContact.razor` factory：</span><span class="sxs-lookup"><span data-stu-id="98821-218">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="98821-219">範例應用程式可確保在元件處置時處置內容：</span><span class="sxs-lookup"><span data-stu-id="98821-219">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="98821-220">最後， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 會覆寫以建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="98821-220">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="98821-221">在範例應用程式中， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 以相同的方法載入連絡人：</span><span class="sxs-lookup"><span data-stu-id="98821-221">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](~/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="98821-222">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="98821-222">In the preceding example:</span></span>

* <span data-ttu-id="98821-223">當 `Busy` 設定為時 `true` ，非同步作業可能會開始。</span><span class="sxs-lookup"><span data-stu-id="98821-223">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="98821-224">當 `Busy` 設回時 `false` ，非同步作業應完成。</span><span class="sxs-lookup"><span data-stu-id="98821-224">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="98821-225">將其他錯誤處理邏輯放在 `catch` 區塊中。</span><span class="sxs-lookup"><span data-stu-id="98821-225">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="98821-226">啟用敏感資料記錄</span><span class="sxs-lookup"><span data-stu-id="98821-226">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="98821-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包含例外狀況訊息和架構記錄中的應用程式資料。</span><span class="sxs-lookup"><span data-stu-id="98821-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="98821-228">記錄的資料可以包含指派給實體實例屬性的值，以及傳送至資料庫之命令的參數值。</span><span class="sxs-lookup"><span data-stu-id="98821-228">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="98821-229">記錄資料的 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> **安全性風險**，因為它可能會在記錄針對資料庫執行的 SQL 語句時， (PII) 公開密碼和其他個人識別資訊。</span><span class="sxs-lookup"><span data-stu-id="98821-229">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="98821-230">建議您只 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 針對開發和測試啟用：</span><span class="sxs-lookup"><span data-stu-id="98821-230">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="98821-231">其他資源</span><span class="sxs-lookup"><span data-stu-id="98821-231">Additional resources</span></span>

* [<span data-ttu-id="98821-232">EF Core 檔</span><span class="sxs-lookup"><span data-stu-id="98821-232">EF Core documentation</span></span>](/ef/)
