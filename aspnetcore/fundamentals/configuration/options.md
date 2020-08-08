---
title: ASP.NET Core 中的選項模式
author: rick-anderson
description: 了解如何使用選項模式來代表 ASP.NET Core 應用程式中的一組相關設定。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: dc03e0820bc332f29e48edb73b57faf5cfd83754
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017619"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="227a4-103">ASP.NET Core 中的選項模式</span><span class="sxs-lookup"><span data-stu-id="227a4-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="227a4-104">By [Kirk Larkin](https://twitter.com/serpent5)和[Rick Anderson](https://twitter.com/RickAndMSFT)。</span><span class="sxs-lookup"><span data-stu-id="227a4-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="227a4-105">選項模式會使用類別來提供相關設定群組的強型別存取。</span><span class="sxs-lookup"><span data-stu-id="227a4-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="227a4-106">當[組態設定](xref:fundamentals/configuration/index)依案例隔離到不同的類別時，應用程式會遵守兩個重要的軟體工程準則：</span><span class="sxs-lookup"><span data-stu-id="227a4-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="227a4-107">[ (ISP) 或封裝的介面隔離原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)： () 類別的案例，取決於其所使用的設定。</span><span class="sxs-lookup"><span data-stu-id="227a4-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="227a4-108">[關注點分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (不同考量)：應用程式不同部分的設定不會彼此相依或結合。</span><span class="sxs-lookup"><span data-stu-id="227a4-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="227a4-109">選項也提供驗證設定資料的機制。</span><span class="sxs-lookup"><span data-stu-id="227a4-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="227a4-110">如需詳細資訊，請參閱[選項驗證](#options-validation)一節。</span><span class="sxs-lookup"><span data-stu-id="227a4-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="227a4-111">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="227a4-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="227a4-112">系結階層式設定</span><span class="sxs-lookup"><span data-stu-id="227a4-112">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="227a4-113">選項介面</span><span class="sxs-lookup"><span data-stu-id="227a4-113">Options interfaces</span></span>

<span data-ttu-id="227a4-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="227a4-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="227a4-115">不***支援：***</span><span class="sxs-lookup"><span data-stu-id="227a4-115">Does ***not*** support:</span></span>
  * <span data-ttu-id="227a4-116">在應用程式啟動後讀取設定資料。</span><span class="sxs-lookup"><span data-stu-id="227a4-116">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="227a4-117">具名選項</span><span class="sxs-lookup"><span data-stu-id="227a4-117">Named options</span></span>](#named)
* <span data-ttu-id="227a4-118">會註冊為[Singleton](xref:fundamentals/dependency-injection#singleton) ，並可插入至任何[服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="227a4-118">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="227a4-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="227a4-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="227a4-120">適用于應該在每個要求上重新計算選項的案例。</span><span class="sxs-lookup"><span data-stu-id="227a4-120">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="227a4-121">如需詳細資訊，請參閱[使用 IOptionsSnapshot 來讀取更新的資料](#ios)。</span><span class="sxs-lookup"><span data-stu-id="227a4-121">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="227a4-122">已註冊為已設定[範圍](xref:fundamentals/dependency-injection#scoped)，因此無法插入單一服務中。</span><span class="sxs-lookup"><span data-stu-id="227a4-122">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="227a4-123">支援[命名選項](#named)</span><span class="sxs-lookup"><span data-stu-id="227a4-123">Supports [named options](#named)</span></span>

<span data-ttu-id="227a4-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="227a4-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="227a4-125">用來抓取實例的選項和管理選項通知 `TOptions` 。</span><span class="sxs-lookup"><span data-stu-id="227a4-125">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="227a4-126">會註冊為[Singleton](xref:fundamentals/dependency-injection#singleton) ，並可插入至任何[服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="227a4-126">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="227a4-127">支援：</span><span class="sxs-lookup"><span data-stu-id="227a4-127">Supports:</span></span>
  * <span data-ttu-id="227a4-128">變更通知</span><span class="sxs-lookup"><span data-stu-id="227a4-128">Change notifications</span></span>
  * [<span data-ttu-id="227a4-129">具名選項</span><span class="sxs-lookup"><span data-stu-id="227a4-129">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="227a4-130">可重新載入的設定</span><span class="sxs-lookup"><span data-stu-id="227a4-130">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="227a4-131">選擇性選項無效判定 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="227a4-131">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="227a4-132">[後續](#options-post-configuration)設定案例會在進行所有設定之後，啟用設定或變更選項 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 。</span><span class="sxs-lookup"><span data-stu-id="227a4-132">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="227a4-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 負責建立新的選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="227a4-134">它有單一 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="227a4-134">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="227a4-135">預設實作會接受所有已註冊的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 與 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>，並先執行所有設定，接著執行設定後作業。</span><span class="sxs-lookup"><span data-stu-id="227a4-135">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="227a4-136">它會區別 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>，且只會呼叫適當的介面。</span><span class="sxs-lookup"><span data-stu-id="227a4-136">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="227a4-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用來快取 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="227a4-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會使監視器中的選項執行個體失效，以便重新計算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="227a4-138">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="227a4-139">值可以使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手動導入。</span><span class="sxs-lookup"><span data-stu-id="227a4-139">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="227a4-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法用於應該視需要重新建立所有具名執行個體時。</span><span class="sxs-lookup"><span data-stu-id="227a4-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="227a4-141">使用 IOptionsSnapshot 來讀取更新的資料</span><span class="sxs-lookup"><span data-stu-id="227a4-141">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="227a4-142">使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ，在要求的存留期記憶體取和快取時，會針對每個要求計算一次選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-142">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="227a4-143">當應用程式啟動時，如果使用支援讀取更新設定值的設定提供者，就會讀取設定的變更。</span><span class="sxs-lookup"><span data-stu-id="227a4-143">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="227a4-144">和之間的差異在於 `IOptionsMonitor` `IOptionsSnapshot` ：</span><span class="sxs-lookup"><span data-stu-id="227a4-144">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="227a4-145">`IOptionsMonitor`是一項[單一服務](xref:fundamentals/dependency-injection#singleton)，可隨時抓取目前的選項值，這在單一相依性中特別有用。</span><span class="sxs-lookup"><span data-stu-id="227a4-145">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="227a4-146">`IOptionsSnapshot`是已設定[範圍的服務](xref:fundamentals/dependency-injection#scoped)，會在物件建立時提供選項的快照集 `IOptionsSnapshot<T>` 。</span><span class="sxs-lookup"><span data-stu-id="227a4-146">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="227a4-147">選項快照集的設計目的是要搭配暫時性和範圍相依性使用。</span><span class="sxs-lookup"><span data-stu-id="227a4-147">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="227a4-148">下列程式碼會使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 。</span><span class="sxs-lookup"><span data-stu-id="227a4-148">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="227a4-149">下列程式碼會註冊系結到的設定實例 `MyOptions` ：</span><span class="sxs-lookup"><span data-stu-id="227a4-149">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="227a4-150">在上述程式碼中，會讀取應用程式啟動後對 JSON 設定檔案的變更。</span><span class="sxs-lookup"><span data-stu-id="227a4-150">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="227a4-151">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="227a4-151">IOptionsMonitor</span></span>

<span data-ttu-id="227a4-152">下列程式碼會註冊系結到的設定實例 `MyOptions` 。</span><span class="sxs-lookup"><span data-stu-id="227a4-152">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="227a4-153">下列範例會使用 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>：</span><span class="sxs-lookup"><span data-stu-id="227a4-153">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="227a4-154">在上述程式碼中，根據預設，會讀取應用程式啟動後對 JSON 設定檔進行的變更。</span><span class="sxs-lookup"><span data-stu-id="227a4-154">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="227a4-155">命名選項支援使用 IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="227a4-155">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="227a4-156">已命名的選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-156">Named options:</span></span>

* <span data-ttu-id="227a4-157">當多個設定區段系結至相同的屬性時，會很有用。</span><span class="sxs-lookup"><span data-stu-id="227a4-157">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="227a4-158">區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="227a4-158">Are case sensitive.</span></span>

<span data-ttu-id="227a4-159">請考慮下列*appsettings.js*檔案：</span><span class="sxs-lookup"><span data-stu-id="227a4-159">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="227a4-160">並不是建立兩個類別來系結 `TopItem:Month` 和，而是 `TopItem:Year` 針對每個區段使用下列類別：</span><span class="sxs-lookup"><span data-stu-id="227a4-160">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="227a4-161">下列程式碼會設定已命名的選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-161">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="227a4-162">下列程式碼會顯示已命名的選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-162">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="227a4-163">所有選項都是具名執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-163">All options are named instances.</span></span> <span data-ttu-id="227a4-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601>實例會被視為以實例為目標 `Options.DefaultName` ，也就是 `string.Empty` 。</span><span class="sxs-lookup"><span data-stu-id="227a4-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="227a4-165"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 也會實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="227a4-165"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="227a4-166"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的預設實作有邏輯可以適當地使用每個項目。</span><span class="sxs-lookup"><span data-stu-id="227a4-166">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="227a4-167">已命名的 `null` 選項可用來以所有命名的實例為目標，而不是特定的已命名實例。</span><span class="sxs-lookup"><span data-stu-id="227a4-167">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="227a4-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>並 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此慣例。</span><span class="sxs-lookup"><span data-stu-id="227a4-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="227a4-169">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="227a4-169">OptionsBuilder API</span></span>

<span data-ttu-id="227a4-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 會用於設定 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="227a4-171">因為 `OptionsBuilder` 僅為初始 `AddOptions<TOptions>(string optionsName)` 呼叫的單一參數，而不是出現在所有後續呼叫的參數，所以其可簡化建立具名選項的程序。</span><span class="sxs-lookup"><span data-stu-id="227a4-171">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="227a4-172">選項驗證及接受服務依存性的 `ConfigureOptions` 多載，只可透過 `OptionsBuilder` 使用。</span><span class="sxs-lookup"><span data-stu-id="227a4-172">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="227a4-173">`OptionsBuilder`在 [[選項驗證](#val)] 區段中使用。</span><span class="sxs-lookup"><span data-stu-id="227a4-173">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="227a4-174">使用 DI 服務來設定選項</span><span class="sxs-lookup"><span data-stu-id="227a4-174">Use DI services to configure options</span></span>

<span data-ttu-id="227a4-175">在以兩種方式設定選項時，可以從相依性插入存取服務：</span><span class="sxs-lookup"><span data-stu-id="227a4-175">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="227a4-176">傳遞設定委派以在[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)上[設定](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="227a4-176">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="227a4-177">`OptionsBuilder<TOptions>`提供[設定的多載，以](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)允許使用最多五個服務來設定選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-177">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="227a4-178">建立類型來執行 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> ，並將類型註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="227a4-178">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="227a4-179">我們建議您將設定委派傳遞到 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因為建立服務更複雜。</span><span class="sxs-lookup"><span data-stu-id="227a4-179">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="227a4-180">建立類型相當於呼叫[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)時的架構所執行的工作。</span><span class="sxs-lookup"><span data-stu-id="227a4-180">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="227a4-181">呼叫 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 會註冊暫時性泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，其具有會接受所指定泛型服務類型的建構函式。</span><span class="sxs-lookup"><span data-stu-id="227a4-181">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="227a4-182">選項驗證</span><span class="sxs-lookup"><span data-stu-id="227a4-182">Options validation</span></span>

<span data-ttu-id="227a4-183">選項驗證會啟用要驗證的選項值。</span><span class="sxs-lookup"><span data-stu-id="227a4-183">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="227a4-184">請考慮下列*appsettings.js*檔案：</span><span class="sxs-lookup"><span data-stu-id="227a4-184">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="227a4-185">下列類別會系結至設定 `"MyConfig"` 區段，並套用幾個 `DataAnnotations` 規則：</span><span class="sxs-lookup"><span data-stu-id="227a4-185">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="227a4-186">下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="227a4-186">The following code:</span></span>

* <span data-ttu-id="227a4-187">呼叫 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> 以取得系結至類別的[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) `MyConfigOptions` 。</span><span class="sxs-lookup"><span data-stu-id="227a4-187">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="227a4-188"><xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A>使用來呼叫，以啟用驗證 `DataAnnotations` 。</span><span class="sxs-lookup"><span data-stu-id="227a4-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="227a4-189">`ValidateDataAnnotations`擴充方法定義于[DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet 套件中。</span><span class="sxs-lookup"><span data-stu-id="227a4-189">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="227a4-190">針對使用 SDK 的 web 應用程式 `Microsoft.NET.Sdk.Web` ，會從共用架構隱含參考此套件。</span><span class="sxs-lookup"><span data-stu-id="227a4-190">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="227a4-191">下列程式碼會顯示設定值或驗證錯誤：</span><span class="sxs-lookup"><span data-stu-id="227a4-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="227a4-192">下列程式碼會使用委派來套用更複雜的驗證規則：</span><span class="sxs-lookup"><span data-stu-id="227a4-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="227a4-193">複雜驗證的 IValidateOptions</span><span class="sxs-lookup"><span data-stu-id="227a4-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="227a4-194">下列類別會實行 <xref:Microsoft.Extensions.Options.IValidateOptions`1> ：</span><span class="sxs-lookup"><span data-stu-id="227a4-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="227a4-195">`IValidateOptions`可將驗證程式代碼移出 `StartUp` 和移入類別。</span><span class="sxs-lookup"><span data-stu-id="227a4-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="227a4-196">使用上述程式碼，會在中啟用驗證， `Startup.ConfigureServices` 並使用下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="227a4-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="227a4-197">選項設定後作業</span><span class="sxs-lookup"><span data-stu-id="227a4-197">Options post-configuration</span></span>

<span data-ttu-id="227a4-198">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 來設定設定後作業。</span><span class="sxs-lookup"><span data-stu-id="227a4-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="227a4-199">設定後作業會在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生後執行：</span><span class="sxs-lookup"><span data-stu-id="227a4-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="227a4-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用來後置設定具名選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="227a4-201">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 後置設定所有設定執行個體：</span><span class="sxs-lookup"><span data-stu-id="227a4-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="227a4-202">在啟動期間存取選項</span><span class="sxs-lookup"><span data-stu-id="227a4-202">Accessing options during startup</span></span>

<span data-ttu-id="227a4-203"><xref:Microsoft.Extensions.Options.IOptions%601> 與 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用於 `Startup.Configure`，因為服務是在 `Configure` 方法執行之前建置。</span><span class="sxs-lookup"><span data-stu-id="227a4-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="227a4-204">請勿在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="227a4-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="227a4-205">可能會因為服務註冊的順序而有不一致的選項狀態存在。</span><span class="sxs-lookup"><span data-stu-id="227a4-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="227a4-206">Options.ConfigurationExtensions NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="227a4-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="227a4-207">ASP.NET Core apps 中會隱含地參考[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)套件。</span><span class="sxs-lookup"><span data-stu-id="227a4-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="227a4-208">選項模式使用類別來代表一組相關的設定。</span><span class="sxs-lookup"><span data-stu-id="227a4-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="227a4-209">當[組態設定](xref:fundamentals/configuration/index)依案例隔離到不同的類別時，應用程式會遵守兩個重要的軟體工程準則：</span><span class="sxs-lookup"><span data-stu-id="227a4-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="227a4-210">[ (ISP) 或封裝的介面隔離原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)： () 類別的案例，取決於其所使用的設定。</span><span class="sxs-lookup"><span data-stu-id="227a4-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="227a4-211">[關注點分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (不同考量)：應用程式不同部分的設定不會彼此相依或結合。</span><span class="sxs-lookup"><span data-stu-id="227a4-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="227a4-212">選項也提供驗證設定資料的機制。</span><span class="sxs-lookup"><span data-stu-id="227a4-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="227a4-213">如需詳細資訊，請參閱[選項驗證](#options-validation)一節。</span><span class="sxs-lookup"><span data-stu-id="227a4-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="227a4-214">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="227a4-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="227a4-215">必要條件</span><span class="sxs-lookup"><span data-stu-id="227a4-215">Prerequisites</span></span>

<span data-ttu-id="227a4-216">參考 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)，或新增 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="227a4-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="227a4-217">選項介面</span><span class="sxs-lookup"><span data-stu-id="227a4-217">Options interfaces</span></span>

<span data-ttu-id="227a4-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 是用來擷取選項並管理 `TOptions` 執行個體的選項通知。</span><span class="sxs-lookup"><span data-stu-id="227a4-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="227a4-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支援以下案例：</span><span class="sxs-lookup"><span data-stu-id="227a4-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="227a4-220">變更通知</span><span class="sxs-lookup"><span data-stu-id="227a4-220">Change notifications</span></span>
* [<span data-ttu-id="227a4-221">具名選項</span><span class="sxs-lookup"><span data-stu-id="227a4-221">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="227a4-222">可重新載入的設定</span><span class="sxs-lookup"><span data-stu-id="227a4-222">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="227a4-223">選擇性選項無效判定 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="227a4-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="227a4-224">[設定後](#options-post-configuration)案例可讓您在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生時設定或變更選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="227a4-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 負責建立新的選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="227a4-226">它有單一 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="227a4-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="227a4-227">預設實作會接受所有已註冊的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 與 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>，並先執行所有設定，接著執行設定後作業。</span><span class="sxs-lookup"><span data-stu-id="227a4-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="227a4-228">它會區別 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>，且只會呼叫適當的介面。</span><span class="sxs-lookup"><span data-stu-id="227a4-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="227a4-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用來快取 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="227a4-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會使監視器中的選項執行個體失效，以便重新計算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="227a4-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="227a4-231">值可以使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手動導入。</span><span class="sxs-lookup"><span data-stu-id="227a4-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="227a4-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法用於應該視需要重新建立所有具名執行個體時。</span><span class="sxs-lookup"><span data-stu-id="227a4-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="227a4-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在應該於收到每個要求時重新計算選項的案例中很實用用。</span><span class="sxs-lookup"><span data-stu-id="227a4-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="227a4-234">如需詳細資訊，請參閱[使用 IOptionsSnapshot 重新載入設定資料](#reload-configuration-data-with-ioptionssnapshot)一節。</span><span class="sxs-lookup"><span data-stu-id="227a4-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="227a4-235"><xref:Microsoft.Extensions.Options.IOptions%601> 可用於支援選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="227a4-236">不過，<xref:Microsoft.Extensions.Options.IOptions%601> 不支援前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 案例。</span><span class="sxs-lookup"><span data-stu-id="227a4-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="227a4-237">您可以在現有架構與程式庫中繼續使用 <xref:Microsoft.Extensions.Options.IOptions%601>，此程式庫已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 介面且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的案例。</span><span class="sxs-lookup"><span data-stu-id="227a4-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="227a4-238">一般選項設定</span><span class="sxs-lookup"><span data-stu-id="227a4-238">General options configuration</span></span>

<span data-ttu-id="227a4-239">一般選項設定是以範例應用程式中的範例 1 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="227a4-240">選項類別必須為非抽象，且具有公用的無參數建構函式。</span><span class="sxs-lookup"><span data-stu-id="227a4-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="227a4-241">下列 `MyOptions` 類別有兩個屬性，`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="227a4-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="227a4-242">設定預設值為選擇性，但在下列範例中，類別建構函式會設定 `Option1` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="227a4-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="227a4-243">`Option2` 的預設值直接藉由初始化屬性來設定 (*Models/MyOptions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="227a4-244">`MyOptions` 類別使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 新增到服務容器，並繫結到設定：</span><span class="sxs-lookup"><span data-stu-id="227a4-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="227a4-245">下列頁面模型使用[建構函式相依性插入](xref:mvc/controllers/dependency-injection)搭配 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 來存取設定 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="227a4-246">範例的 *appsettings.json* 檔案指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="227a4-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="227a4-247">當應用程式執行時，頁面模型的 `OnGet` 方法會傳回字串，顯示選項類別值：</span><span class="sxs-lookup"><span data-stu-id="227a4-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="227a4-248">使用自訂 <xref:System.Configuration.ConfigurationBuilder> 從設定檔載入選項設定時，請確認已正確設定基底路徑：</span><span class="sxs-lookup"><span data-stu-id="227a4-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="227a4-249">透過 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 從設定檔載入選項設定時，不需要明確設定基底路徑。</span><span class="sxs-lookup"><span data-stu-id="227a4-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="227a4-250">使用委派設定簡單的選項</span><span class="sxs-lookup"><span data-stu-id="227a4-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="227a4-251">使用委派設定簡單的選項是以範例應用程式中的範例 2 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="227a4-252">使用委派來設定選項值。</span><span class="sxs-lookup"><span data-stu-id="227a4-252">Use a delegate to set options values.</span></span> <span data-ttu-id="227a4-253">範例應用程式使用 `MyOptionsWithDelegateConfig` 類別 (*Models/MyOptionsWithDelegateConfig.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="227a4-254">在下列程式碼中，第二個 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="227a4-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="227a4-255">它使用委派，以 `MyOptionsWithDelegateConfig` 設定繫結：</span><span class="sxs-lookup"><span data-stu-id="227a4-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="227a4-256">*Index.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="227a4-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="227a4-257">您可以新增多個設定提供者。</span><span class="sxs-lookup"><span data-stu-id="227a4-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="227a4-258">設定提供者可在 NuGet 套件中找到，而且會依註冊順序套用。</span><span class="sxs-lookup"><span data-stu-id="227a4-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="227a4-259">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="227a4-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="227a4-260">每次呼叫 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 時都會新增 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務到服務容器。</span><span class="sxs-lookup"><span data-stu-id="227a4-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="227a4-261">在上述範例中，`Option1` 和 `Option2` 的值都指定在 *appsettings.json* 中，但 `Option1` 和 `Option2` 的值會被設定的委派所覆寫。</span><span class="sxs-lookup"><span data-stu-id="227a4-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="227a4-262">啟用多個設定服務時，最後一個指定的設定來源會「勝出」\*\* 並設定此組態值。</span><span class="sxs-lookup"><span data-stu-id="227a4-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="227a4-263">當應用程式執行時，頁面模型的 `OnGet` 方法會傳回字串，顯示選項類別值：</span><span class="sxs-lookup"><span data-stu-id="227a4-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="227a4-264">子選項組態</span><span class="sxs-lookup"><span data-stu-id="227a4-264">Suboptions configuration</span></span>

<span data-ttu-id="227a4-265">子選項組態是以範例應用程式中的範例 3 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="227a4-266">應用程式應該建立屬於應用程式中特定案例群組 (類別) 的選項類別。</span><span class="sxs-lookup"><span data-stu-id="227a4-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="227a4-267">需要組態值的應用程式組件應該只能存取它們使用的設定值。</span><span class="sxs-lookup"><span data-stu-id="227a4-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="227a4-268">將選項繫結至組態時，選項類型中的每一個屬性都會繫結至 `property[:sub-property:]` 格式的組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="227a4-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="227a4-269">例如，`MyOptions.Option1` 屬性繫結至索引鍵 `Option1`，其是從 *appsettings.json* 中的 `option1` 屬性讀取。</span><span class="sxs-lookup"><span data-stu-id="227a4-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="227a4-270">在下列程式碼中，第三個 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="227a4-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="227a4-271">它將 `MySubOptions` 繫結至 *appSettings.json* 檔案的 `subsection` 區段：</span><span class="sxs-lookup"><span data-stu-id="227a4-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="227a4-272">`GetSection`方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空間。</span><span class="sxs-lookup"><span data-stu-id="227a4-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="227a4-273">範例的 *appsettings.json* 檔案會定義 `subsection` 成員，並具有 `suboption1` 和 `suboption2` 的索引鍵：</span><span class="sxs-lookup"><span data-stu-id="227a4-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="227a4-274">`MySubOptions` 類別會定義屬性 `SubOption1` 和 `SubOption2`，來保存選項值 (*Models/MySubOptions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="227a4-275">頁面模型的 `OnGet` 方法會傳回具有選項值 (*Pages/Index.cshtml.cs*) 的字串：</span><span class="sxs-lookup"><span data-stu-id="227a4-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="227a4-276">當應用程式執行時，`OnGet` 方法會傳回字串，顯示子選項類別值：</span><span class="sxs-lookup"><span data-stu-id="227a4-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="227a4-277">選項插入</span><span class="sxs-lookup"><span data-stu-id="227a4-277">Options injection</span></span>

<span data-ttu-id="227a4-278">範例應用程式中的範例4示範了選項插入。</span><span class="sxs-lookup"><span data-stu-id="227a4-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="227a4-279">插入 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ：</span><span class="sxs-lookup"><span data-stu-id="227a4-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="227a4-280">具有指示詞的 Razor 頁面或 MVC 視圖 [`@inject`](xref:mvc/views/razor#inject) Razor 。</span><span class="sxs-lookup"><span data-stu-id="227a4-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="227a4-281">頁面或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="227a4-281">A page or view model.</span></span>

<span data-ttu-id="227a4-282">下列來自範例應用程式的範例會將 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> (*Pages/Index. cshtml .cs*) 中插入頁面模型：</span><span class="sxs-lookup"><span data-stu-id="227a4-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="227a4-283">範例應用程式會顯示如何使用 `@inject` 指示詞來插入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="227a4-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="227a4-284">執行應用程式時，轉譯的頁面中會顯示選項值：</span><span class="sxs-lookup"><span data-stu-id="227a4-284">When the app is run, the options values are shown in the rendered page:</span></span>

![選項值 Option1:：value1_from_json 和 Option2: -1 是從模型藉由插入至檢視來載入。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="227a4-286">使用 IOptionsSnapshot 重新載入設定資料</span><span class="sxs-lookup"><span data-stu-id="227a4-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="227a4-287">使用重載設定資料 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 時，會在範例應用程式的範例5中示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="227a4-288">使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ，在要求的存留期記憶體取和快取時，會針對每個要求計算一次選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="227a4-289">和之間的差異在於 `IOptionsMonitor` `IOptionsSnapshot` ：</span><span class="sxs-lookup"><span data-stu-id="227a4-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="227a4-290">`IOptionsMonitor`是一項[單一服務](xref:fundamentals/dependency-injection#singleton)，可隨時抓取目前的選項值，這在單一相依性中特別有用。</span><span class="sxs-lookup"><span data-stu-id="227a4-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="227a4-291">`IOptionsSnapshot`是已設定[範圍的服務](xref:fundamentals/dependency-injection#scoped)，會在物件建立時提供選項的快照集 `IOptionsSnapshot<T>` 。</span><span class="sxs-lookup"><span data-stu-id="227a4-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="227a4-292">選項快照集的設計目的是要搭配暫時性和範圍相依性使用。</span><span class="sxs-lookup"><span data-stu-id="227a4-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="227a4-293">下列範例示範在 *appsettings.json* 變更之後如何建立新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="227a4-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="227a4-294">對伺服器的多個要求會傳回 *appsettings.json* 檔案所提供的常數值，直到檔案變更並重新載入組態為止。</span><span class="sxs-lookup"><span data-stu-id="227a4-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="227a4-295">下圖顯示從 *appsettings.json* 檔案載入的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="227a4-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="227a4-296">將 *appsettings.json* 檔案中的值變更為 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="227a4-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="227a4-297">將*appsettings.js儲存在檔案上*。</span><span class="sxs-lookup"><span data-stu-id="227a4-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="227a4-298">重新整理瀏覽器，以查看選項值更新：</span><span class="sxs-lookup"><span data-stu-id="227a4-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="227a4-299">IConfigureNamedOptions 的具名選項支援</span><span class="sxs-lookup"><span data-stu-id="227a4-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="227a4-300"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的具名選項支援是以範例應用程式中的範例 6 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="227a4-301">「具名選項」支援可讓應用程式區別具名選項組態。</span><span class="sxs-lookup"><span data-stu-id="227a4-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="227a4-302">在範例應用程式中，名稱為 options 的宣告會使用[OptionsServiceCollectionExtensions.Configu](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)，這會呼叫[configurenamedoptions .configure \<TOptions> 。設定](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)擴充方法。</span><span class="sxs-lookup"><span data-stu-id="227a4-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="227a4-303">已命名的選項會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="227a4-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="227a4-304">範例應用程式會使用　<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*) 來存取具名選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="227a4-305">執行範例應用程式後，會傳回具名選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="227a4-306">`named_options_1` 值會從設定提供，這會從 *appsettings.json* 檔案載入。</span><span class="sxs-lookup"><span data-stu-id="227a4-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="227a4-307">`named_options_2` 值是由以下提供：</span><span class="sxs-lookup"><span data-stu-id="227a4-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="227a4-308">`ConfigureServices` 中 `Option1` 的 `named_options_2` 委派。</span><span class="sxs-lookup"><span data-stu-id="227a4-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="227a4-309">`MyOptions` 類別所提供的 `Option2` 預設值。</span><span class="sxs-lookup"><span data-stu-id="227a4-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="227a4-310">使用 ConfigureAll 方法設定所有選項</span><span class="sxs-lookup"><span data-stu-id="227a4-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="227a4-311">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法設定所有選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="227a4-312">下列程式碼會為具有共通值的所有設定執行個體設定 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="227a4-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="227a4-313">將下列程式碼手動新增至 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="227a4-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="227a4-314">新增程式碼之後執行範例應用程式，就會產生下列結果：</span><span class="sxs-lookup"><span data-stu-id="227a4-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="227a4-315">所有選項都是具名執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-315">All options are named instances.</span></span> <span data-ttu-id="227a4-316">現有的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 執行個體會視為以 `Options.DefaultName` 執行個體為目標，也就是 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="227a4-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="227a4-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 也會實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="227a4-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="227a4-318"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的預設實作有邏輯可以適當地使用每個項目。</span><span class="sxs-lookup"><span data-stu-id="227a4-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="227a4-319">`null` 具名選項用來以所有具名執行個體為目標，而不是特定的具名執行個體 (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 與 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此慣例)。</span><span class="sxs-lookup"><span data-stu-id="227a4-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="227a4-320">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="227a4-320">OptionsBuilder API</span></span>

<span data-ttu-id="227a4-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 會用於設定 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="227a4-322">因為 `OptionsBuilder` 僅為初始 `AddOptions<TOptions>(string optionsName)` 呼叫的單一參數，而不是出現在所有後續呼叫的參數，所以其可簡化建立具名選項的程序。</span><span class="sxs-lookup"><span data-stu-id="227a4-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="227a4-323">選項驗證及接受服務依存性的 `ConfigureOptions` 多載，只可透過 `OptionsBuilder` 使用。</span><span class="sxs-lookup"><span data-stu-id="227a4-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="227a4-324">使用 DI 服務來設定選項</span><span class="sxs-lookup"><span data-stu-id="227a4-324">Use DI services to configure options</span></span>

<span data-ttu-id="227a4-325">在以下列兩種方式設定選項的同時，您可以從相依性插入中存取其他服務：</span><span class="sxs-lookup"><span data-stu-id="227a4-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="227a4-326">傳遞設定委派以在[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)上[設定](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="227a4-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="227a4-327">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)提供[設定的多載，可](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)讓您使用最多五個服務來設定選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="227a4-328">建立您自己的類型來實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，並將該類型註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="227a4-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="227a4-329">我們建議您將設定委派傳遞到 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因為建立服務更複雜。</span><span class="sxs-lookup"><span data-stu-id="227a4-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="227a4-330">建立您自己的類型相當於，當您使用 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 時此架構可為您執行的動作。</span><span class="sxs-lookup"><span data-stu-id="227a4-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="227a4-331">呼叫 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 會註冊暫時性泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，其具有會接受所指定泛型服務類型的建構函式。</span><span class="sxs-lookup"><span data-stu-id="227a4-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="227a4-332">選項驗證</span><span class="sxs-lookup"><span data-stu-id="227a4-332">Options validation</span></span>

<span data-ttu-id="227a4-333">選項驗證可讓您在設定選項之後驗證選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="227a4-334">搭配驗證方法呼叫 `Validate`，若選項有效會傳回 `true`，若為無效則傳回 `false`：</span><span class="sxs-lookup"><span data-stu-id="227a4-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="227a4-335">上述範例會將具名的選項執行個體設定為 `optionalOptionsName`。</span><span class="sxs-lookup"><span data-stu-id="227a4-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="227a4-336">預設選項執行個體為 `Options.DefaultName`。</span><span class="sxs-lookup"><span data-stu-id="227a4-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="227a4-337">當選項執行個體建立之後，便會執行驗證。</span><span class="sxs-lookup"><span data-stu-id="227a4-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="227a4-338">選項實例保證會在第一次存取時通過驗證。</span><span class="sxs-lookup"><span data-stu-id="227a4-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="227a4-339">選項驗證不會在建立 options 實例之後防護選項的修改。</span><span class="sxs-lookup"><span data-stu-id="227a4-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="227a4-340">例如， `IOptionsSnapshot` 當第一次存取選項時，會針對每個要求建立及驗證一個選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="227a4-341">`IOptionsSnapshot`*針對相同要求*的後續存取嘗試時，不會再次驗證這些選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="227a4-342">`Validate` 方法可接受 `Func<TOptions, bool>`。</span><span class="sxs-lookup"><span data-stu-id="227a4-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="227a4-343">若要完整地自訂驗證，請實作 `IValidateOptions<TOptions>`，它允許：</span><span class="sxs-lookup"><span data-stu-id="227a4-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="227a4-344">多種選項類型的驗證：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="227a4-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="227a4-345">取決於另一個選項類型的驗證：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="227a4-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="227a4-346">`IValidateOptions` 可驗證：</span><span class="sxs-lookup"><span data-stu-id="227a4-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="227a4-347">特定的具名選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-347">A specific named options instance.</span></span>
* <span data-ttu-id="227a4-348">所有選項 (當 `name` 為 `null` 時)。</span><span class="sxs-lookup"><span data-stu-id="227a4-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="227a4-349">從以下的介面實作傳回 `ValidateOptionsResult`：</span><span class="sxs-lookup"><span data-stu-id="227a4-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="227a4-350">透過呼叫 `OptionsBuilder<TOptions>` 上的 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 方法，就可從 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 套件使用資料註解型驗證。</span><span class="sxs-lookup"><span data-stu-id="227a4-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="227a4-351">`Microsoft.Extensions.Options.DataAnnotations`包含在[AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="227a4-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="227a4-352">我們考慮在之後的版本中加入積極式驗證 (啟動時快速檢錯)。</span><span class="sxs-lookup"><span data-stu-id="227a4-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="227a4-353">選項設定後作業</span><span class="sxs-lookup"><span data-stu-id="227a4-353">Options post-configuration</span></span>

<span data-ttu-id="227a4-354">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 來設定設定後作業。</span><span class="sxs-lookup"><span data-stu-id="227a4-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="227a4-355">設定後作業會在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生後執行：</span><span class="sxs-lookup"><span data-stu-id="227a4-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="227a4-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用來後置設定具名選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="227a4-357">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 後置設定所有設定執行個體：</span><span class="sxs-lookup"><span data-stu-id="227a4-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="227a4-358">在啟動期間存取選項</span><span class="sxs-lookup"><span data-stu-id="227a4-358">Accessing options during startup</span></span>

<span data-ttu-id="227a4-359"><xref:Microsoft.Extensions.Options.IOptions%601> 與 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用於 `Startup.Configure`，因為服務是在 `Configure` 方法執行之前建置。</span><span class="sxs-lookup"><span data-stu-id="227a4-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="227a4-360">請勿在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="227a4-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="227a4-361">可能會因為服務註冊的順序而有不一致的選項狀態存在。</span><span class="sxs-lookup"><span data-stu-id="227a4-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="227a4-362">選項模式使用類別來代表一組相關的設定。</span><span class="sxs-lookup"><span data-stu-id="227a4-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="227a4-363">當[組態設定](xref:fundamentals/configuration/index)依案例隔離到不同的類別時，應用程式會遵守兩個重要的軟體工程準則：</span><span class="sxs-lookup"><span data-stu-id="227a4-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="227a4-364">[ (ISP) 或封裝的介面隔離原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)： () 類別的案例，取決於其所使用的設定。</span><span class="sxs-lookup"><span data-stu-id="227a4-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="227a4-365">[關注點分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (不同考量)：應用程式不同部分的設定不會彼此相依或結合。</span><span class="sxs-lookup"><span data-stu-id="227a4-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="227a4-366">選項也提供驗證設定資料的機制。</span><span class="sxs-lookup"><span data-stu-id="227a4-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="227a4-367">如需詳細資訊，請參閱[選項驗證](#options-validation)一節。</span><span class="sxs-lookup"><span data-stu-id="227a4-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="227a4-368">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="227a4-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="227a4-369">必要條件</span><span class="sxs-lookup"><span data-stu-id="227a4-369">Prerequisites</span></span>

<span data-ttu-id="227a4-370">參考 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)，或新增 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="227a4-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="227a4-371">選項介面</span><span class="sxs-lookup"><span data-stu-id="227a4-371">Options interfaces</span></span>

<span data-ttu-id="227a4-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 是用來擷取選項並管理 `TOptions` 執行個體的選項通知。</span><span class="sxs-lookup"><span data-stu-id="227a4-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="227a4-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支援以下案例：</span><span class="sxs-lookup"><span data-stu-id="227a4-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="227a4-374">變更通知</span><span class="sxs-lookup"><span data-stu-id="227a4-374">Change notifications</span></span>
* [<span data-ttu-id="227a4-375">具名選項</span><span class="sxs-lookup"><span data-stu-id="227a4-375">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="227a4-376">可重新載入的設定</span><span class="sxs-lookup"><span data-stu-id="227a4-376">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="227a4-377">選擇性選項無效判定 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="227a4-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="227a4-378">[設定後](#options-post-configuration)案例可讓您在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生時設定或變更選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="227a4-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 負責建立新的選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="227a4-380">它有單一 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="227a4-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="227a4-381">預設實作會接受所有已註冊的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 與 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>，並先執行所有設定，接著執行設定後作業。</span><span class="sxs-lookup"><span data-stu-id="227a4-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="227a4-382">它會區別 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>，且只會呼叫適當的介面。</span><span class="sxs-lookup"><span data-stu-id="227a4-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="227a4-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用來快取 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="227a4-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會使監視器中的選項執行個體失效，以便重新計算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="227a4-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="227a4-385">值可以使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手動導入。</span><span class="sxs-lookup"><span data-stu-id="227a4-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="227a4-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法用於應該視需要重新建立所有具名執行個體時。</span><span class="sxs-lookup"><span data-stu-id="227a4-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="227a4-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在應該於收到每個要求時重新計算選項的案例中很實用用。</span><span class="sxs-lookup"><span data-stu-id="227a4-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="227a4-388">如需詳細資訊，請參閱[使用 IOptionsSnapshot 重新載入設定資料](#reload-configuration-data-with-ioptionssnapshot)一節。</span><span class="sxs-lookup"><span data-stu-id="227a4-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="227a4-389"><xref:Microsoft.Extensions.Options.IOptions%601> 可用於支援選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="227a4-390">不過，<xref:Microsoft.Extensions.Options.IOptions%601> 不支援前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 案例。</span><span class="sxs-lookup"><span data-stu-id="227a4-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="227a4-391">您可以在現有架構與程式庫中繼續使用 <xref:Microsoft.Extensions.Options.IOptions%601>，此程式庫已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 介面且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的案例。</span><span class="sxs-lookup"><span data-stu-id="227a4-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="227a4-392">一般選項設定</span><span class="sxs-lookup"><span data-stu-id="227a4-392">General options configuration</span></span>

<span data-ttu-id="227a4-393">一般選項設定是以範例應用程式中的範例 1 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="227a4-394">選項類別必須為非抽象，且具有公用的無參數建構函式。</span><span class="sxs-lookup"><span data-stu-id="227a4-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="227a4-395">下列 `MyOptions` 類別有兩個屬性，`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="227a4-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="227a4-396">設定預設值為選擇性，但在下列範例中，類別建構函式會設定 `Option1` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="227a4-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="227a4-397">`Option2` 的預設值直接藉由初始化屬性來設定 (*Models/MyOptions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="227a4-398">`MyOptions` 類別使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 新增到服務容器，並繫結到設定：</span><span class="sxs-lookup"><span data-stu-id="227a4-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="227a4-399">下列頁面模型使用[建構函式相依性插入](xref:mvc/controllers/dependency-injection)搭配 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 來存取設定 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="227a4-400">範例的 *appsettings.json* 檔案指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="227a4-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="227a4-401">當應用程式執行時，頁面模型的 `OnGet` 方法會傳回字串，顯示選項類別值：</span><span class="sxs-lookup"><span data-stu-id="227a4-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="227a4-402">使用自訂 <xref:System.Configuration.ConfigurationBuilder> 從設定檔載入選項設定時，請確認已正確設定基底路徑：</span><span class="sxs-lookup"><span data-stu-id="227a4-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="227a4-403">透過 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 從設定檔載入選項設定時，不需要明確設定基底路徑。</span><span class="sxs-lookup"><span data-stu-id="227a4-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="227a4-404">使用委派設定簡單的選項</span><span class="sxs-lookup"><span data-stu-id="227a4-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="227a4-405">使用委派設定簡單的選項是以範例應用程式中的範例 2 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="227a4-406">使用委派來設定選項值。</span><span class="sxs-lookup"><span data-stu-id="227a4-406">Use a delegate to set options values.</span></span> <span data-ttu-id="227a4-407">範例應用程式使用 `MyOptionsWithDelegateConfig` 類別 (*Models/MyOptionsWithDelegateConfig.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="227a4-408">在下列程式碼中，第二個 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="227a4-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="227a4-409">它使用委派，以 `MyOptionsWithDelegateConfig` 設定繫結：</span><span class="sxs-lookup"><span data-stu-id="227a4-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="227a4-410">*Index.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="227a4-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="227a4-411">您可以新增多個設定提供者。</span><span class="sxs-lookup"><span data-stu-id="227a4-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="227a4-412">設定提供者可在 NuGet 套件中找到，而且會依註冊順序套用。</span><span class="sxs-lookup"><span data-stu-id="227a4-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="227a4-413">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="227a4-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="227a4-414">每次呼叫 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 時都會新增 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務到服務容器。</span><span class="sxs-lookup"><span data-stu-id="227a4-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="227a4-415">在上述範例中，`Option1` 和 `Option2` 的值都指定在 *appsettings.json* 中，但 `Option1` 和 `Option2` 的值會被設定的委派所覆寫。</span><span class="sxs-lookup"><span data-stu-id="227a4-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="227a4-416">啟用多個設定服務時，最後一個指定的設定來源會「勝出」\*\* 並設定此組態值。</span><span class="sxs-lookup"><span data-stu-id="227a4-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="227a4-417">當應用程式執行時，頁面模型的 `OnGet` 方法會傳回字串，顯示選項類別值：</span><span class="sxs-lookup"><span data-stu-id="227a4-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="227a4-418">子選項組態</span><span class="sxs-lookup"><span data-stu-id="227a4-418">Suboptions configuration</span></span>

<span data-ttu-id="227a4-419">子選項組態是以範例應用程式中的範例 3 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="227a4-420">應用程式應該建立屬於應用程式中特定案例群組 (類別) 的選項類別。</span><span class="sxs-lookup"><span data-stu-id="227a4-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="227a4-421">需要組態值的應用程式組件應該只能存取它們使用的設定值。</span><span class="sxs-lookup"><span data-stu-id="227a4-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="227a4-422">將選項繫結至組態時，選項類型中的每一個屬性都會繫結至 `property[:sub-property:]` 格式的組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="227a4-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="227a4-423">例如，`MyOptions.Option1` 屬性繫結至索引鍵 `Option1`，其是從 *appsettings.json* 中的 `option1` 屬性讀取。</span><span class="sxs-lookup"><span data-stu-id="227a4-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="227a4-424">在下列程式碼中，第三個 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="227a4-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="227a4-425">它將 `MySubOptions` 繫結至 *appSettings.json* 檔案的 `subsection` 區段：</span><span class="sxs-lookup"><span data-stu-id="227a4-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="227a4-426">`GetSection`方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空間。</span><span class="sxs-lookup"><span data-stu-id="227a4-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="227a4-427">範例的 *appsettings.json* 檔案會定義 `subsection` 成員，並具有 `suboption1` 和 `suboption2` 的索引鍵：</span><span class="sxs-lookup"><span data-stu-id="227a4-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="227a4-428">`MySubOptions` 類別會定義屬性 `SubOption1` 和 `SubOption2`，來保存選項值 (*Models/MySubOptions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="227a4-429">頁面模型的 `OnGet` 方法會傳回具有選項值 (*Pages/Index.cshtml.cs*) 的字串：</span><span class="sxs-lookup"><span data-stu-id="227a4-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="227a4-430">當應用程式執行時，`OnGet` 方法會傳回字串，顯示子選項類別值：</span><span class="sxs-lookup"><span data-stu-id="227a4-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="227a4-431">檢視模型提供的選項或使用直接檢視插入提供的選項</span><span class="sxs-lookup"><span data-stu-id="227a4-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="227a4-432">檢視模型提供的選項或使用直接檢視插入提供的選項是以範例應用程式中的範例 4 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="227a4-433">可以在檢視模型中提供選項，或藉由將 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接插入至檢視來提供選項 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="227a4-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="227a4-434">範例應用程式會顯示如何使用 `@inject` 指示詞來插入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="227a4-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="227a4-435">執行應用程式時，轉譯的頁面中會顯示選項值：</span><span class="sxs-lookup"><span data-stu-id="227a4-435">When the app is run, the options values are shown in the rendered page:</span></span>

![選項值 Option1:：value1_from_json 和 Option2: -1 是從模型藉由插入至檢視來載入。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="227a4-437">使用 IOptionsSnapshot 重新載入設定資料</span><span class="sxs-lookup"><span data-stu-id="227a4-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="227a4-438">使用重載設定資料 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 時，會在範例應用程式的範例5中示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="227a4-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支援以最小的處理負擔來重新載入選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="227a4-440">在要求的存留期內存取及快取選項時，會針對每個要求計算一次選項。</span><span class="sxs-lookup"><span data-stu-id="227a4-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="227a4-441">下列範例示範在 *appsettings.json* 變更之後如何建立新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="227a4-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="227a4-442">對伺服器的多個要求會傳回 *appsettings.json* 檔案所提供的常數值，直到檔案變更並重新載入組態為止。</span><span class="sxs-lookup"><span data-stu-id="227a4-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="227a4-443">下圖顯示從 *appsettings.json* 檔案載入的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="227a4-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="227a4-444">將 *appsettings.json* 檔案中的值變更為 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="227a4-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="227a4-445">將*appsettings.js儲存在檔案上*。</span><span class="sxs-lookup"><span data-stu-id="227a4-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="227a4-446">重新整理瀏覽器，以查看選項值更新：</span><span class="sxs-lookup"><span data-stu-id="227a4-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="227a4-447">IConfigureNamedOptions 的具名選項支援</span><span class="sxs-lookup"><span data-stu-id="227a4-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="227a4-448"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的具名選項支援是以範例應用程式中的範例 6 來示範。</span><span class="sxs-lookup"><span data-stu-id="227a4-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="227a4-449">「具名選項」支援可讓應用程式區別具名選項組態。</span><span class="sxs-lookup"><span data-stu-id="227a4-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="227a4-450">在範例應用程式中，名稱為 options 的宣告會使用[OptionsServiceCollectionExtensions.Configu](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)，這會呼叫[configurenamedoptions .configure \<TOptions> 。設定](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)擴充方法。</span><span class="sxs-lookup"><span data-stu-id="227a4-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="227a4-451">已命名的選項會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="227a4-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="227a4-452">範例應用程式會使用　<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*) 來存取具名選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="227a4-453">執行範例應用程式後，會傳回具名選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="227a4-454">`named_options_1` 值會從設定提供，這會從 *appsettings.json* 檔案載入。</span><span class="sxs-lookup"><span data-stu-id="227a4-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="227a4-455">`named_options_2` 值是由以下提供：</span><span class="sxs-lookup"><span data-stu-id="227a4-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="227a4-456">`ConfigureServices` 中 `Option1` 的 `named_options_2` 委派。</span><span class="sxs-lookup"><span data-stu-id="227a4-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="227a4-457">`MyOptions` 類別所提供的 `Option2` 預設值。</span><span class="sxs-lookup"><span data-stu-id="227a4-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="227a4-458">使用 ConfigureAll 方法設定所有選項</span><span class="sxs-lookup"><span data-stu-id="227a4-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="227a4-459">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法設定所有選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="227a4-460">下列程式碼會為具有共通值的所有設定執行個體設定 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="227a4-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="227a4-461">將下列程式碼手動新增至 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="227a4-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="227a4-462">新增程式碼之後執行範例應用程式，就會產生下列結果：</span><span class="sxs-lookup"><span data-stu-id="227a4-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="227a4-463">所有選項都是具名執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-463">All options are named instances.</span></span> <span data-ttu-id="227a4-464">現有的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 執行個體會視為以 `Options.DefaultName` 執行個體為目標，也就是 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="227a4-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="227a4-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 也會實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="227a4-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="227a4-466"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的預設實作有邏輯可以適當地使用每個項目。</span><span class="sxs-lookup"><span data-stu-id="227a4-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="227a4-467">`null` 具名選項用來以所有具名執行個體為目標，而不是特定的具名執行個體 (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 與 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此慣例)。</span><span class="sxs-lookup"><span data-stu-id="227a4-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="227a4-468">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="227a4-468">OptionsBuilder API</span></span>

<span data-ttu-id="227a4-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 會用於設定 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="227a4-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="227a4-470">因為 `OptionsBuilder` 僅為初始 `AddOptions<TOptions>(string optionsName)` 呼叫的單一參數，而不是出現在所有後續呼叫的參數，所以其可簡化建立具名選項的程序。</span><span class="sxs-lookup"><span data-stu-id="227a4-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="227a4-471">選項驗證及接受服務依存性的 `ConfigureOptions` 多載，只可透過 `OptionsBuilder` 使用。</span><span class="sxs-lookup"><span data-stu-id="227a4-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="227a4-472">使用 DI 服務來設定選項</span><span class="sxs-lookup"><span data-stu-id="227a4-472">Use DI services to configure options</span></span>

<span data-ttu-id="227a4-473">在以下列兩種方式設定選項的同時，您可以從相依性插入中存取其他服務：</span><span class="sxs-lookup"><span data-stu-id="227a4-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="227a4-474">傳遞設定委派以在[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)上[設定](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="227a4-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="227a4-475">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)提供[設定的多載，可](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)讓您使用最多五個服務來設定選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="227a4-476">建立您自己的類型來實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，並將該類型註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="227a4-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="227a4-477">我們建議您將設定委派傳遞到 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因為建立服務更複雜。</span><span class="sxs-lookup"><span data-stu-id="227a4-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="227a4-478">建立您自己的類型相當於，當您使用 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 時此架構可為您執行的動作。</span><span class="sxs-lookup"><span data-stu-id="227a4-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="227a4-479">呼叫 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 會註冊暫時性泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，其具有會接受所指定泛型服務類型的建構函式。</span><span class="sxs-lookup"><span data-stu-id="227a4-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="227a4-480">選項設定後作業</span><span class="sxs-lookup"><span data-stu-id="227a4-480">Options post-configuration</span></span>

<span data-ttu-id="227a4-481">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 來設定設定後作業。</span><span class="sxs-lookup"><span data-stu-id="227a4-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="227a4-482">設定後作業會在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生後執行：</span><span class="sxs-lookup"><span data-stu-id="227a4-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="227a4-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用來後置設定具名選項：</span><span class="sxs-lookup"><span data-stu-id="227a4-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="227a4-484">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 後置設定所有設定執行個體：</span><span class="sxs-lookup"><span data-stu-id="227a4-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="227a4-485">在啟動期間存取選項</span><span class="sxs-lookup"><span data-stu-id="227a4-485">Accessing options during startup</span></span>

<span data-ttu-id="227a4-486"><xref:Microsoft.Extensions.Options.IOptions%601> 與 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用於 `Startup.Configure`，因為服務是在 `Configure` 方法執行之前建置。</span><span class="sxs-lookup"><span data-stu-id="227a4-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="227a4-487">請勿在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="227a4-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="227a4-488">可能會因為服務註冊的順序而有不一致的選項狀態存在。</span><span class="sxs-lookup"><span data-stu-id="227a4-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="227a4-489">其他資源</span><span class="sxs-lookup"><span data-stu-id="227a4-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
