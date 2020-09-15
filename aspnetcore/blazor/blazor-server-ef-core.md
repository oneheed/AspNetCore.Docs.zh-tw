---
title: 'ASP.NET Core Blazor Server 與 Entity Framework Core (EFCore) '
author: JeremyLikness
description: 在應用程式中使用 EF Core 的指引 Blazor Server 。
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
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
ms.openlocfilehash: e548465b3d79279802fbfacd66c69724d864d14d
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080325"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="af837-103">ASP.NET Core Blazor Server 與 Entity Framework Core (EFCore) </span><span class="sxs-lookup"><span data-stu-id="af837-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="af837-104">依據： [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="af837-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="af837-105">Blazor Server 是具狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="af837-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="af837-106">應用程式會維持與伺服器的持續連線，而且使用者的狀態會保留在*伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="af837-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="af837-107">使用者狀態的其中一個範例是在相依性插入中保存的資料 [ (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為線路。</span><span class="sxs-lookup"><span data-stu-id="af837-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="af837-108">提供的唯一應用程式模型 Blazor Server 需要使用 Entity Framework Core 的特殊方法。</span><span class="sxs-lookup"><span data-stu-id="af837-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="af837-109">本文說明應用程式中的 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="af837-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="af837-110">Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="af837-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="af837-111">在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="af837-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="af837-112">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="af837-112">Sample app</span></span></h2>

<span data-ttu-id="af837-113">範例應用程式已建立為 Blazor Server 使用 EF Core 之應用程式的參考。</span><span class="sxs-lookup"><span data-stu-id="af837-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="af837-114">範例應用程式包含具有排序和篩選、刪除、新增和更新作業的方格。</span><span class="sxs-lookup"><span data-stu-id="af837-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="af837-115">此範例示範如何使用 EF Core 來處理開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="af837-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="af837-116">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="af837-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="af837-117">此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，以便在任何平臺上使用。</span><span class="sxs-lookup"><span data-stu-id="af837-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="af837-118">此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="af837-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="af837-119">這是在中設定 `appsettings.Development.json` ：</span><span class="sxs-lookup"><span data-stu-id="af837-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="af837-120">Grid、add 和 view 元件會使用「每一作業的內容」模式，其中會為每個作業建立內容。</span><span class="sxs-lookup"><span data-stu-id="af837-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="af837-121">編輯元件會使用「每一元件的內容」模式，其中會為每個元件建立內容。</span><span class="sxs-lookup"><span data-stu-id="af837-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="af837-122">本主題中的部分程式碼範例需要未顯示的命名空間和服務。</span><span class="sxs-lookup"><span data-stu-id="af837-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="af837-123">若要檢查完整運作的程式碼，包括範例的必要和指示詞 [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor ，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="af837-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="af837-124">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="af837-124">Database access</span></span></h2>

<span data-ttu-id="af837-125">EF Core 依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取](/ef/core/miscellaneous/configuring-dbcontext) 的方法，並作為 [*工作單位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="af837-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="af837-126">EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的擴充功能，這些應用程式預設會將內容註冊為 *範圍* 服務。</span><span class="sxs-lookup"><span data-stu-id="af837-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="af837-127">在 Blazor Server 應用程式中，已設定範圍的服務註冊可能會有問題，因為該實例會在使用者的線路內跨元件共用。</span><span class="sxs-lookup"><span data-stu-id="af837-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="af837-128"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了並行使用而設計。</span><span class="sxs-lookup"><span data-stu-id="af837-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="af837-129">現有的存留期不適當，原因如下：</span><span class="sxs-lookup"><span data-stu-id="af837-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="af837-130">在應用程式的所有使用者之間**單獨**共用狀態，並導致不當的並行使用。</span><span class="sxs-lookup"><span data-stu-id="af837-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="af837-131">限**域** (預設) 在相同使用者的元件之間提出類似的問題。</span><span class="sxs-lookup"><span data-stu-id="af837-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="af837-132">**暫時性** 會在每個要求中產生新的實例;但由於元件的存留期可能很長，因此可能會產生比預期更長的內容。</span><span class="sxs-lookup"><span data-stu-id="af837-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="af837-133">下列建議旨在提供在應用程式中使用 EF Core 的一致方法 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="af837-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="af837-134">根據預設，每個作業都應考慮使用一個內容。</span><span class="sxs-lookup"><span data-stu-id="af837-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="af837-135">內容是針對快速、低負荷的具現化所設計：</span><span class="sxs-lookup"><span data-stu-id="af837-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="af837-136">使用旗標來防止多個平行作業：</span><span class="sxs-lookup"><span data-stu-id="af837-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="af837-137">將作業放 `Loading = true;` 在區塊中的行後面 `try` 。</span><span class="sxs-lookup"><span data-stu-id="af837-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="af837-138">對於利用 EF Core [變更追蹤](/ef/core/querying/tracking) 或 [並行控制](/ef/core/saving/concurrency)的較長時間的作業，請將 [內容約制設為元件的存留期](#scope-to-the-component-lifetime-5x)。</span><span class="sxs-lookup"><span data-stu-id="af837-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="af837-139">新的 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="af837-139">New DbContext instances</span></span></h3>

<span data-ttu-id="af837-140">建立新實例的最快速方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="af837-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="af837-141">不過，有幾個案例可能需要解析其他相依性。</span><span class="sxs-lookup"><span data-stu-id="af837-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="af837-142">例如，您可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 設定內容。</span><span class="sxs-lookup"><span data-stu-id="af837-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="af837-143">建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。</span><span class="sxs-lookup"><span data-stu-id="af837-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="af837-144">EF Core 5.0 或更新版本會提供內建的 factory 來建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="af837-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="af837-145">下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。</span><span class="sxs-lookup"><span data-stu-id="af837-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="af837-146">程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：</span><span class="sxs-lookup"><span data-stu-id="af837-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="af837-147">Factory 會插入元件，並用來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="af837-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="af837-148">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="af837-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="af837-149">`Wrapper` 是元件的 [元件參考](xref:blazor/components/index#capture-references-to-components) `GridWrapper` 。</span><span class="sxs-lookup"><span data-stu-id="af837-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="af837-150">請參閱 `Index` `Pages/Index.razor` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 () 元件。</span><span class="sxs-lookup"><span data-stu-id="af837-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="af837-151">將範圍設為元件存留期</span><span class="sxs-lookup"><span data-stu-id="af837-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="af837-152">您可能會想要建立在 <xref:Microsoft.EntityFrameworkCore.DbContext> 元件存留期存在的。</span><span class="sxs-lookup"><span data-stu-id="af837-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="af837-153">這可讓您將它當作 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) 使用，並利用內建的功能，例如變更追蹤和並行解析。</span><span class="sxs-lookup"><span data-stu-id="af837-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="af837-154">您可以使用 factory 來建立內容，並在元件的存留期追蹤它。</span><span class="sxs-lookup"><span data-stu-id="af837-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="af837-155">首先，請依照 <xref:System.IDisposable> 下列方式，執行並插入 `Pages/EditContact.razor` factory：</span><span class="sxs-lookup"><span data-stu-id="af837-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="af837-156">範例應用程式可確保在元件處置時處置內容：</span><span class="sxs-lookup"><span data-stu-id="af837-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="af837-157">最後， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 會覆寫以建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="af837-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="af837-158">在範例應用程式中， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 以相同的方法載入連絡人：</span><span class="sxs-lookup"><span data-stu-id="af837-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="af837-159">Blazor Server 是具狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="af837-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="af837-160">應用程式會維持與伺服器的持續連線，而且使用者的狀態會保留在*伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="af837-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="af837-161">使用者狀態的其中一個範例是在相依性插入中保存的資料 [ (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為線路。</span><span class="sxs-lookup"><span data-stu-id="af837-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="af837-162">提供的唯一應用程式模型 Blazor Server 需要使用 Entity Framework Core 的特殊方法。</span><span class="sxs-lookup"><span data-stu-id="af837-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="af837-163">本文說明應用程式中的 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="af837-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="af837-164">Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="af837-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="af837-165">在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="af837-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="af837-166">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="af837-166">Sample app</span></span></h2>

<span data-ttu-id="af837-167">範例應用程式已建立為 Blazor Server 使用 EF Core 之應用程式的參考。</span><span class="sxs-lookup"><span data-stu-id="af837-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="af837-168">範例應用程式包含具有排序和篩選、刪除、新增和更新作業的方格。</span><span class="sxs-lookup"><span data-stu-id="af837-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="af837-169">此範例示範如何使用 EF Core 來處理開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="af837-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="af837-170">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="af837-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="af837-171">此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，以便在任何平臺上使用。</span><span class="sxs-lookup"><span data-stu-id="af837-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="af837-172">此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="af837-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="af837-173">這是在中設定 `appsettings.Development.json` ：</span><span class="sxs-lookup"><span data-stu-id="af837-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="af837-174">Grid、add 和 view 元件會使用「每一作業的內容」模式，其中會為每個作業建立內容。</span><span class="sxs-lookup"><span data-stu-id="af837-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="af837-175">編輯元件會使用「每一元件的內容」模式，其中會為每個元件建立內容。</span><span class="sxs-lookup"><span data-stu-id="af837-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="af837-176">本主題中的部分程式碼範例需要未顯示的命名空間和服務。</span><span class="sxs-lookup"><span data-stu-id="af837-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="af837-177">若要檢查完整運作的程式碼，包括範例的必要和指示詞 [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor ，請參閱 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="af837-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="af837-178">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="af837-178">Database access</span></span></h2>

<span data-ttu-id="af837-179">EF Core 依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取](/ef/core/miscellaneous/configuring-dbcontext) 的方法，並作為 [*工作單位*](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="af837-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="af837-180">EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的擴充功能，這些應用程式預設會將內容註冊為 *範圍* 服務。</span><span class="sxs-lookup"><span data-stu-id="af837-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="af837-181">在 Blazor Server 應用程式中，這可能會造成問題，因為該實例會在使用者的線路內跨元件共用。</span><span class="sxs-lookup"><span data-stu-id="af837-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="af837-182"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了並行使用而設計。</span><span class="sxs-lookup"><span data-stu-id="af837-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="af837-183">現有的存留期不適當，原因如下：</span><span class="sxs-lookup"><span data-stu-id="af837-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="af837-184">在應用程式的所有使用者之間**單獨**共用狀態，並導致不當的並行使用。</span><span class="sxs-lookup"><span data-stu-id="af837-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="af837-185">限**域** (預設) 在相同使用者的元件之間提出類似的問題。</span><span class="sxs-lookup"><span data-stu-id="af837-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="af837-186">**暫時性** 會在每個要求中產生新的實例;但由於元件的存留期可能很長，因此可能會產生比預期更長的內容。</span><span class="sxs-lookup"><span data-stu-id="af837-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="af837-187">下列建議旨在提供在應用程式中使用 EF Core 的一致方法 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="af837-187">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="af837-188">根據預設，每個作業都應考慮使用一個內容。</span><span class="sxs-lookup"><span data-stu-id="af837-188">By default, consider using one context per operation.</span></span> <span data-ttu-id="af837-189">內容是針對快速、低負荷的具現化所設計：</span><span class="sxs-lookup"><span data-stu-id="af837-189">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="af837-190">使用旗標來防止多個平行作業：</span><span class="sxs-lookup"><span data-stu-id="af837-190">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="af837-191">將作業放 `Loading = true;` 在區塊中的行後面 `try` 。</span><span class="sxs-lookup"><span data-stu-id="af837-191">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="af837-192">對於利用 EF Core [變更追蹤](/ef/core/querying/tracking) 或 [並行控制](/ef/core/saving/concurrency)的較長時間的作業，請將 [內容約制設為元件的存留期](#scope-to-the-component-lifetime-3x)。</span><span class="sxs-lookup"><span data-stu-id="af837-192">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="af837-193">新的 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="af837-193">New DbContext instances</span></span></h3>

<span data-ttu-id="af837-194">建立新實例的最快速方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="af837-194">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="af837-195">不過，有幾個案例可能需要解析其他相依性。</span><span class="sxs-lookup"><span data-stu-id="af837-195">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="af837-196">例如，您可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 設定內容。</span><span class="sxs-lookup"><span data-stu-id="af837-196">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="af837-197">建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。</span><span class="sxs-lookup"><span data-stu-id="af837-197">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="af837-198">範例應用程式會在中執行自己的 factory `Data/DbContextFactory.cs` 。</span><span class="sxs-lookup"><span data-stu-id="af837-198">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="af837-199">在先前的 factory 中，透過 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 服務提供者滿足任何相依性。</span><span class="sxs-lookup"><span data-stu-id="af837-199">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="af837-200">下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。</span><span class="sxs-lookup"><span data-stu-id="af837-200">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="af837-201">程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：</span><span class="sxs-lookup"><span data-stu-id="af837-201">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="af837-202">Factory 會插入元件，並用來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="af837-202">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="af837-203">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="af837-203">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="af837-204">`Wrapper` 是元件的 [元件參考](xref:blazor/components/index#capture-references-to-components) `GridWrapper` 。</span><span class="sxs-lookup"><span data-stu-id="af837-204">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="af837-205">請參閱 `Index` `Pages/Index.razor` [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 () 元件。</span><span class="sxs-lookup"><span data-stu-id="af837-205">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="af837-206">將範圍設為元件存留期</span><span class="sxs-lookup"><span data-stu-id="af837-206">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="af837-207">您可能會想要建立在 <xref:Microsoft.EntityFrameworkCore.DbContext> 元件存留期存在的。</span><span class="sxs-lookup"><span data-stu-id="af837-207">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="af837-208">這可讓您將它當作 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) 使用，並利用內建的功能，例如變更追蹤和並行解析。</span><span class="sxs-lookup"><span data-stu-id="af837-208">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="af837-209">您可以使用 factory 來建立內容，並在元件的存留期追蹤它。</span><span class="sxs-lookup"><span data-stu-id="af837-209">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="af837-210">首先，請依照 <xref:System.IDisposable> 下列方式，執行並插入 `Pages/EditContact.razor` factory：</span><span class="sxs-lookup"><span data-stu-id="af837-210">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="af837-211">範例應用程式可確保在元件處置時處置內容：</span><span class="sxs-lookup"><span data-stu-id="af837-211">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="af837-212">最後， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 會覆寫以建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="af837-212">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="af837-213">在範例應用程式中， [`OnInitializedAsync`](xref:blazor/components/lifecycle) 以相同的方法載入連絡人：</span><span class="sxs-lookup"><span data-stu-id="af837-213">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="af837-214">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="af837-214">In the preceding example:</span></span>

* <span data-ttu-id="af837-215">當 `Busy` 設定為時 `true` ，非同步作業可能會開始。</span><span class="sxs-lookup"><span data-stu-id="af837-215">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="af837-216">當 `Busy` 設回時 `false` ，非同步作業應完成。</span><span class="sxs-lookup"><span data-stu-id="af837-216">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="af837-217">將其他錯誤處理邏輯放在 `catch` 區塊中。</span><span class="sxs-lookup"><span data-stu-id="af837-217">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="af837-218">其他資源</span><span class="sxs-lookup"><span data-stu-id="af837-218">Additional resources</span></span>

* [<span data-ttu-id="af837-219">EF Core 檔</span><span class="sxs-lookup"><span data-stu-id="af837-219">EF Core documentation</span></span>](/ef/)
