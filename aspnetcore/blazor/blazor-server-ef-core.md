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
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="f49bb-103">Blazor Server具有 Entity Framework Core (EFCore 的 ASP.NET Core) </span><span class="sxs-lookup"><span data-stu-id="f49bb-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="f49bb-104">依據： [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="f49bb-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f49bb-105">Blazor Server 是可設定狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="f49bb-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="f49bb-106">應用程式會維持與伺服器的持續連線，而使用者的狀態會保留在*伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="f49bb-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="f49bb-107">「使用者狀態」的其中一個範例是將資料保存在相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為該線路。</span><span class="sxs-lookup"><span data-stu-id="f49bb-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="f49bb-108">提供的唯一應用程式模型 Blazor Server 需要特殊的方法，才能使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="f49bb-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span> 

> [!NOTE]
> <span data-ttu-id="f49bb-109">本文說明應用程式中的 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="f49bb-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="f49bb-110">Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="f49bb-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="f49bb-111">在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="f49bb-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="f49bb-112">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="f49bb-112">Sample app</span></span>

<span data-ttu-id="f49bb-113">範例應用程式已建立為 Blazor Server 使用 EF Core 之應用程式的參考。</span><span class="sxs-lookup"><span data-stu-id="f49bb-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="f49bb-114">範例應用程式包含具有排序和篩選、刪除、加入和更新作業的方格。</span><span class="sxs-lookup"><span data-stu-id="f49bb-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="f49bb-115">此範例示範如何使用 EF Core 來處理開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="f49bb-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="f49bb-116">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="f49bb-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f49bb-117">此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，讓它可以在任何平臺上使用。</span><span class="sxs-lookup"><span data-stu-id="f49bb-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="f49bb-118">此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="f49bb-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="f49bb-119">這是在中設定 `appsettings.Development.json` ：</span><span class="sxs-lookup"><span data-stu-id="f49bb-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

<span data-ttu-id="f49bb-120">方格、加入和視圖元件會使用「每一作業內容」模式，其中會為每個作業建立內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="f49bb-121">編輯元件會使用「每個元件的內容」模式，其中會為每個元件建立內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

## <a name="database-access"></a><span data-ttu-id="f49bb-122">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="f49bb-122">Database access</span></span>

<span data-ttu-id="f49bb-123">EF Core 依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取權](/ef/core/miscellaneous/configuring-dbcontext) ，並作為 [_工作單位的_](https://martinfowler.com/eaaCatalog/unitOfWork.html)方式。</span><span class="sxs-lookup"><span data-stu-id="f49bb-123">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [_unit of work_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="f49bb-124">EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的延伸模組，其會在預設情況下，將內容註冊為 _範圍_ 服務。</span><span class="sxs-lookup"><span data-stu-id="f49bb-124">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a _scoped_ service by default.</span></span> <span data-ttu-id="f49bb-125">在 Blazor Server 應用程式中，有範圍的服務註冊可能會有問題，因為實例會在使用者線路內的各個元件之間共用。</span><span class="sxs-lookup"><span data-stu-id="f49bb-125">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="f49bb-126"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了同時使用而設計。</span><span class="sxs-lookup"><span data-stu-id="f49bb-126"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="f49bb-127">基於下列原因，現有的存留期不適當：</span><span class="sxs-lookup"><span data-stu-id="f49bb-127">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="f49bb-128">**Singleton** 會在應用程式的所有使用者之間共用狀態，並導致不適當的並行使用。</span><span class="sxs-lookup"><span data-stu-id="f49bb-128">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="f49bb-129">限**域** (預設) 會在相同使用者的元件之間產生類似的問題。</span><span class="sxs-lookup"><span data-stu-id="f49bb-129">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="f49bb-130">**暫時性** 會產生每個要求的新實例;但因為元件可能會很長，所以這會產生比預期更長的內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-130">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="f49bb-131">下列建議的設計，是為了提供一致的方法，讓您在應用程式中使用 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="f49bb-131">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span> 

* <span data-ttu-id="f49bb-132">根據預設，請考慮針對每個作業使用一個內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-132">By default, consider using one context per operation.</span></span> <span data-ttu-id="f49bb-133">內容是針對快速、低負荷的具現化所設計：</span><span class="sxs-lookup"><span data-stu-id="f49bb-133">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="f49bb-134">使用旗標來防止多個並行作業：</span><span class="sxs-lookup"><span data-stu-id="f49bb-134">Use a flag to prevent multiple concurrent operations:</span></span>

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

* <span data-ttu-id="f49bb-135">對於利用 EF Core 的 [變更追蹤](/ef/core/querying/tracking) 或 [並行存取控制](/ef/core/saving/concurrency)的較長時間作業，請 [將內容約制限定在元件的存留期](#scope-to-the-component-lifetime)。</span><span class="sxs-lookup"><span data-stu-id="f49bb-135">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="f49bb-136">新的 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="f49bb-136">New DbContext instances</span></span>

<span data-ttu-id="f49bb-137">建立新實例的最快方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="f49bb-137">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="f49bb-138">不過，有幾個案例可能需要解析其他相依性。</span><span class="sxs-lookup"><span data-stu-id="f49bb-138">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="f49bb-139">例如，您可能會想要使用 [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 來設定內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-139">For example, you may wish to use [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span> 

<span data-ttu-id="f49bb-140">建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。</span><span class="sxs-lookup"><span data-stu-id="f49bb-140">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="f49bb-141">EF Core 5.0 或更新版本會提供內建的 factory 來建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-141">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="f49bb-142">下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。</span><span class="sxs-lookup"><span data-stu-id="f49bb-142">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="f49bb-143">程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：</span><span class="sxs-lookup"><span data-stu-id="f49bb-143">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

<span data-ttu-id="f49bb-144">Factory 會插入元件中，並用來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="f49bb-144">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="f49bb-145">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="f49bb-145">For example, in `Pages/Index.razor`:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="f49bb-146">元件存留期的範圍</span><span class="sxs-lookup"><span data-stu-id="f49bb-146">Scope to the component lifetime</span></span>

<span data-ttu-id="f49bb-147">您可能想要建立一個 <xref:Microsoft.EntityFrameworkCore.DbContext> 存在於元件存留期的。</span><span class="sxs-lookup"><span data-stu-id="f49bb-147">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="f49bb-148">這可讓您使用它做為 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) ，並利用內建功能，例如變更追蹤和並行解析。</span><span class="sxs-lookup"><span data-stu-id="f49bb-148">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="f49bb-149">您可以使用 factory 來建立內容，並在元件的存留期間追蹤它。</span><span class="sxs-lookup"><span data-stu-id="f49bb-149">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="f49bb-150">首先，執行 <xref:System.IDisposable> 並插入 factory，如下所示 `Pages/EditContact.razor` ：</span><span class="sxs-lookup"><span data-stu-id="f49bb-150">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

<span data-ttu-id="f49bb-151">範例應用程式可確保在處置元件時，會處置連絡人：</span><span class="sxs-lookup"><span data-stu-id="f49bb-151">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

<span data-ttu-id="f49bb-152">最後， `OnInitializedAsync` 會覆寫以建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-152">Finally, `OnInitializedAsync` is overridden to create a new context.</span></span> <span data-ttu-id="f49bb-153">在範例應用程式中，會 `OnInitializedAsync` 以相同的方法載入連絡人：</span><span class="sxs-lookup"><span data-stu-id="f49bb-153">In the sample app, `OnInitializedAsync` loads the contact in the same method:</span></span>

[!code-razor[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="f49bb-154">Blazor Server 是可設定狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="f49bb-154">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="f49bb-155">應用程式會維持與伺服器的持續連線，而使用者的狀態會保留在*伺服器的記憶體中。*</span><span class="sxs-lookup"><span data-stu-id="f49bb-155">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="f49bb-156">「使用者狀態」的其中一個範例是將資料保存在相依性 [插入 (DI) ](xref:fundamentals/dependency-injection) 服務實例的範圍設定為該線路。</span><span class="sxs-lookup"><span data-stu-id="f49bb-156">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="f49bb-157">提供的唯一應用程式模型 Blazor Server 需要特殊的方法，才能使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="f49bb-157">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span> 

> [!NOTE]
> <span data-ttu-id="f49bb-158">本文說明應用程式中的 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="f49bb-158">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="f49bb-159">Blazor WebAssembly 應用程式會在 WebAssembly 沙箱中執行，以防止大部分的直接資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="f49bb-159">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="f49bb-160">在中執行 EF Core Blazor WebAssembly 已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="f49bb-160">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="f49bb-161">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="f49bb-161">Sample app</span></span>

<span data-ttu-id="f49bb-162">範例應用程式已建立為 Blazor Server 使用 EF Core 之應用程式的參考。</span><span class="sxs-lookup"><span data-stu-id="f49bb-162">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="f49bb-163">範例應用程式包含具有排序和篩選、刪除、加入和更新作業的方格。</span><span class="sxs-lookup"><span data-stu-id="f49bb-163">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="f49bb-164">此範例示範如何使用 EF Core 來處理開放式平行存取。</span><span class="sxs-lookup"><span data-stu-id="f49bb-164">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="f49bb-165">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="f49bb-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f49bb-166">此範例會使用本機 [SQLite](https://www.sqlite.org/index.html) 資料庫，讓它可以在任何平臺上使用。</span><span class="sxs-lookup"><span data-stu-id="f49bb-166">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="f49bb-167">此範例也會設定資料庫記錄，以顯示所產生的 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="f49bb-167">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="f49bb-168">這是在中設定 `appsettings.Development.json` ：</span><span class="sxs-lookup"><span data-stu-id="f49bb-168">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)] 

<span data-ttu-id="f49bb-169">方格、加入和視圖元件會使用「每一作業內容」模式，其中會為每個作業建立內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-169">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="f49bb-170">編輯元件會使用「每個元件的內容」模式，其中會為每個元件建立內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-170">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

## <a name="database-access"></a><span data-ttu-id="f49bb-171">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="f49bb-171">Database access</span></span>

<span data-ttu-id="f49bb-172">EF Core 依賴 <xref:Microsoft.EntityFrameworkCore.DbContext> 作為 [設定資料庫存取權](/ef/core/miscellaneous/configuring-dbcontext) ，並作為 [_工作單位的_](https://martinfowler.com/eaaCatalog/unitOfWork.html)方式。</span><span class="sxs-lookup"><span data-stu-id="f49bb-172">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [_unit of work_](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="f49bb-173">EF Core 提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> ASP.NET Core 應用程式的延伸模組，其會在預設情況下，將內容註冊為 _範圍_ 服務。</span><span class="sxs-lookup"><span data-stu-id="f49bb-173">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a _scoped_ service by default.</span></span> <span data-ttu-id="f49bb-174">在 Blazor Server 應用程式中，這可能會造成問題，因為實例會在使用者線路內的各個元件之間共用。</span><span class="sxs-lookup"><span data-stu-id="f49bb-174">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="f49bb-175"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是安全線程，而且不是為了同時使用而設計。</span><span class="sxs-lookup"><span data-stu-id="f49bb-175"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="f49bb-176">基於下列原因，現有的存留期不適當：</span><span class="sxs-lookup"><span data-stu-id="f49bb-176">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="f49bb-177">**Singleton** 會在應用程式的所有使用者之間共用狀態，並導致不適當的並行使用。</span><span class="sxs-lookup"><span data-stu-id="f49bb-177">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="f49bb-178">限**域** (預設) 會在相同使用者的元件之間產生類似的問題。</span><span class="sxs-lookup"><span data-stu-id="f49bb-178">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="f49bb-179">**暫時性** 會產生每個要求的新實例;但因為元件可能會很長，所以這會產生比預期更長的內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-179">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="f49bb-180">資料庫存取</span><span class="sxs-lookup"><span data-stu-id="f49bb-180">Database access</span></span>

<span data-ttu-id="f49bb-181">下列建議的設計，是為了提供一致的方法，讓您在應用程式中使用 EF Core Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="f49bb-181">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span> 

* <span data-ttu-id="f49bb-182">根據預設，請考慮針對每個作業使用一個內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-182">By default, consider using one context per operation.</span></span> <span data-ttu-id="f49bb-183">內容是針對快速、低負荷的具現化所設計：</span><span class="sxs-lookup"><span data-stu-id="f49bb-183">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="f49bb-184">使用旗標來防止多個並行作業：</span><span class="sxs-lookup"><span data-stu-id="f49bb-184">Use a flag to prevent multiple concurrent operations:</span></span>

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

* <span data-ttu-id="f49bb-185">對於利用 EF Core 的 [變更追蹤](/ef/core/querying/tracking) 或 [並行存取控制](/ef/core/saving/concurrency)的較長時間作業，請 [將內容約制限定在元件的存留期](#scope-to-the-component-lifetime)。</span><span class="sxs-lookup"><span data-stu-id="f49bb-185">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="f49bb-186">建立新的 DbCoNtext 實例</span><span class="sxs-lookup"><span data-stu-id="f49bb-186">Create new DbContext instances</span></span>

<span data-ttu-id="f49bb-187">建立新實例的最快方式 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 `new` 來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="f49bb-187">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="f49bb-188">不過，有幾個案例可能需要解析其他相依性。</span><span class="sxs-lookup"><span data-stu-id="f49bb-188">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="f49bb-189">例如，您可能會想要使用 [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 來設定內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-189">For example, you may wish to use [`DbContextOptions`](https://docs.microsoft.com/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span> 

<span data-ttu-id="f49bb-190">建立具有相依性之新的建議解決方案 <xref:Microsoft.EntityFrameworkCore.DbContext> 是使用 factory。</span><span class="sxs-lookup"><span data-stu-id="f49bb-190">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="f49bb-191">範例應用程式會在中執行自己的 factory `Data/DbContextFactory.cs` 。</span><span class="sxs-lookup"><span data-stu-id="f49bb-191">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span> 

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="f49bb-192">下列範例會設定 [SQLite](https://www.sqlite.org/index.html) 並啟用資料記錄。</span><span class="sxs-lookup"><span data-stu-id="f49bb-192">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="f49bb-193">程式碼會使用擴充方法來設定 DI 的資料庫 factory，並提供預設選項：</span><span class="sxs-lookup"><span data-stu-id="f49bb-193">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?range=29-31)]

<span data-ttu-id="f49bb-194">Factory 會插入元件中，並用來建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="f49bb-194">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="f49bb-195">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="f49bb-195">For example, in `Pages/Index.razor`:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?range=199-212)]

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="f49bb-196">元件存留期的範圍</span><span class="sxs-lookup"><span data-stu-id="f49bb-196">Scope to the component lifetime</span></span>

<span data-ttu-id="f49bb-197">您可能想要建立一個 <xref:Microsoft.EntityFrameworkCore.DbContext> 存在於元件存留期的。</span><span class="sxs-lookup"><span data-stu-id="f49bb-197">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="f49bb-198">這可讓您使用它做為 [工作單位](https://martinfowler.com/eaaCatalog/unitOfWork.html) ，並利用內建功能，例如變更追蹤和並行解析。</span><span class="sxs-lookup"><span data-stu-id="f49bb-198">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="f49bb-199">您可以使用 factory 來建立內容，並在元件的存留期間追蹤它。</span><span class="sxs-lookup"><span data-stu-id="f49bb-199">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="f49bb-200">首先，執行 <xref:System.IDisposable> 並插入 factory，如下所示 `Pages/EditContact.razor` ：</span><span class="sxs-lookup"><span data-stu-id="f49bb-200">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=5-7)]

<span data-ttu-id="f49bb-201">範例應用程式可確保在處置元件時，會處置連絡人：</span><span class="sxs-lookup"><span data-stu-id="f49bb-201">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=181-184)]

<span data-ttu-id="f49bb-202">最後， `OnInitializedAsync` 會覆寫以建立新的內容。</span><span class="sxs-lookup"><span data-stu-id="f49bb-202">Finally, `OnInitializedAsync` is overridden to create a new context.</span></span> <span data-ttu-id="f49bb-203">在範例應用程式中，會 `OnInitializedAsync` 以相同的方法載入連絡人：</span><span class="sxs-lookup"><span data-stu-id="f49bb-203">In the sample app, `OnInitializedAsync` loads the contact in the same method:</span></span>

[!code-razor[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?range=89-104)]

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f49bb-204">其他資源</span><span class="sxs-lookup"><span data-stu-id="f49bb-204">Additional resources</span></span>

> [<span data-ttu-id="f49bb-205">EF Core 檔</span><span class="sxs-lookup"><span data-stu-id="f49bb-205">EF Core documentation</span></span>](/ef/)
