---
title: ASP.NET Core 中的選項模式
author: rick-anderson
description: 了解如何使用選項模式來代表 ASP.NET Core 應用程式中的一組相關設定。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061089"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="d4ae9-103">ASP.NET Core 中的選項模式</span><span class="sxs-lookup"><span data-stu-id="d4ae9-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4ae9-104">[Kirk Larkin](https://twitter.com/serpent5)與[Rick Anderson](https://twitter.com/RickAndMSFT)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="d4ae9-105">選項模式使用類別來提供相關設定群組的強型別存取。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="d4ae9-106">當[組態設定](xref:fundamentals/configuration/index)依案例隔離到不同的類別時，應用程式會遵守兩個重要的軟體工程準則：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="d4ae9-107">[介面隔離原則 (ISP) 或封裝](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：相依于設定的案例 (類別) 取決於所使用的設定。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="d4ae9-108">[關注點分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (不同考量)：應用程式不同部分的設定不會彼此相依或結合。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="d4ae9-109">選項也提供驗證設定資料的機制。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="d4ae9-110">如需詳細資訊，請參閱[選項驗證](#options-validation)一節。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="d4ae9-111">本主題提供 ASP.NET Core 中選項模式的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="d4ae9-112">如需在主控台應用程式中使用選項模式的詳細資訊，請參閱 [.net 中的選項模式](/dotnet/core/extensions/options)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="d4ae9-113">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4ae9-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="d4ae9-114">系結階層式設定</span><span class="sxs-lookup"><span data-stu-id="d4ae9-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="d4ae9-115">選項介面</span><span class="sxs-lookup"><span data-stu-id="d4ae9-115">Options interfaces</span></span>

<span data-ttu-id="d4ae9-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="d4ae9-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="d4ae9-117">\***Not** _ 支援： _ 在應用程式啟動後讀取設定資料。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-117">Does \***not** _ support: _ Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="d4ae9-118">具名選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-118">Named options</span></span>](#named)
* <span data-ttu-id="d4ae9-119">註冊為 [Singleton](xref:fundamentals/dependency-injection#singleton) ，並且可以插入至任何 [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-119">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="d4ae9-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="d4ae9-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="d4ae9-121">適用于每個要求都應該重新計算選項的情況。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-121">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="d4ae9-122">如需詳細資訊，請參閱 [使用 IOptionsSnapshot 讀取更新的資料](#ios)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-122">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="d4ae9-123">註冊為 [範圍](xref:fundamentals/dependency-injection#scoped) ，因此無法插入至單一服務。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-123">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="d4ae9-124">支援 [已命名的選項](#named)</span><span class="sxs-lookup"><span data-stu-id="d4ae9-124">Supports [named options](#named)</span></span>

<span data-ttu-id="d4ae9-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="d4ae9-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="d4ae9-126">用來取得實例的選項和管理選項通知 `TOptions` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-126">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="d4ae9-127">註冊為 [Singleton](xref:fundamentals/dependency-injection#singleton) ，並且可以插入至任何 [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-127">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="d4ae9-128">支援：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-128">Supports:</span></span>
  * <span data-ttu-id="d4ae9-129">變更通知</span><span class="sxs-lookup"><span data-stu-id="d4ae9-129">Change notifications</span></span>
  * [<span data-ttu-id="d4ae9-130">具名選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-130">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="d4ae9-131">可重新載入的設定</span><span class="sxs-lookup"><span data-stu-id="d4ae9-131">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="d4ae9-132">選擇性選項無效判定 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="d4ae9-132">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="d4ae9-133">設定[後](#options-post-configuration)案例會在進行所有設定之後，啟用設定或變更選項 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-133">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="d4ae9-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 負責建立新的選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="d4ae9-135">它有單一 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-135">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="d4ae9-136">預設實作會接受所有已註冊的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 與 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>，並先執行所有設定，接著執行設定後作業。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-136">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="d4ae9-137">它會區別 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>，且只會呼叫適當的介面。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-137">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="d4ae9-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用來快取 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="d4ae9-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會使監視器中的選項執行個體失效，以便重新計算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-139">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="d4ae9-140">值可以使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手動導入。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-140">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="d4ae9-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法用於應該視需要重新建立所有具名執行個體時。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-141">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="d4ae9-142">使用 IOptionsSnapshot 讀取更新的資料</span><span class="sxs-lookup"><span data-stu-id="d4ae9-142">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="d4ae9-143">使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ，在要求的存留期記憶體取和快取選項時，會針對每個要求計算一次。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-143">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="d4ae9-144">使用支援讀取已更新設定值的設定提供者時，會在應用程式啟動後讀取設定的變更。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-144">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="d4ae9-145">和之間的差異在於 `IOptionsMonitor` `IOptionsSnapshot` ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-145">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="d4ae9-146">`IOptionsMonitor` 是 [單一服務](xref:fundamentals/dependency-injection#singleton) ，可隨時抓取目前的選項值，這在單一相依性中特別有用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-146">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="d4ae9-147">`IOptionsSnapshot` 是限 [域的服務](xref:fundamentals/dependency-injection#scoped) ，會在建立物件時提供選項的快照 `IOptionsSnapshot<T>` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-147">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="d4ae9-148">選項快照集的設計目的是要搭配使用暫時性和限定範圍的相依性。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-148">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="d4ae9-149">下列程式碼使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-149">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="d4ae9-150">下列程式碼會註冊系結的設定實例 `MyOptions` ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-150">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="d4ae9-151">在上述程式碼中，會讀取應用程式啟動後的 JSON 設定檔變更。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-151">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="d4ae9-152">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="d4ae9-152">IOptionsMonitor</span></span>

<span data-ttu-id="d4ae9-153">下列程式碼會註冊針對系結的設定實例 `MyOptions` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-153">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="d4ae9-154">下列範例會使用 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-154">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="d4ae9-155">在上述程式碼中，預設會讀取應用程式啟動後的 JSON 設定檔變更。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-155">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="d4ae9-156">使用 IConfigureNamedOptions 的命名選項支援</span><span class="sxs-lookup"><span data-stu-id="d4ae9-156">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="d4ae9-157">命名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-157">Named options:</span></span>

* <span data-ttu-id="d4ae9-158">當多個設定區段系結至相同的屬性時，會很有用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-158">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="d4ae9-159">會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-159">Are case sensitive.</span></span>

<span data-ttu-id="d4ae9-160">請考慮下列檔案 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-160">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="d4ae9-161">`TopItem:Month` `TopItem:Year` 下列類別可用於每個區段，而不是建立兩個類別來系結和：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-161">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="d4ae9-162">下列程式碼會設定命名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-162">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="d4ae9-163">下列程式碼會顯示已命名的選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-163">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="d4ae9-164">所有選項都是具名執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-164">All options are named instances.</span></span> <span data-ttu-id="d4ae9-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 系統會將實例視為目標 `Options.DefaultName` 實例，也就是 `string.Empty` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="d4ae9-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 也會實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="d4ae9-167"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的預設實作有邏輯可以適當地使用每個項目。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-167">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="d4ae9-168">此 `null` 命名選項用來以所有已命名的實例為目標，而不是特定的已命名實例。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-168">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="d4ae9-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 並 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此慣例。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="d4ae9-170">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="d4ae9-170">OptionsBuilder API</span></span>

<span data-ttu-id="d4ae9-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 會用於設定 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="d4ae9-172">因為 `OptionsBuilder` 僅為初始 `AddOptions<TOptions>(string optionsName)` 呼叫的單一參數，而不是出現在所有後續呼叫的參數，所以其可簡化建立具名選項的程序。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-172">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="d4ae9-173">選項驗證及接受服務依存性的 `ConfigureOptions` 多載，只可透過 `OptionsBuilder` 使用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-173">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="d4ae9-174">`OptionsBuilder` 在 [ [選項驗證](#val) ] 區段中使用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-174">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="d4ae9-175">使用 DI 服務來設定選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-175">Use DI services to configure options</span></span>

<span data-ttu-id="d4ae9-176">您可以透過下列兩種方式，從相依性插入來存取服務：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-176">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="d4ae9-177">傳遞設定委派以[設定](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-177">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="d4ae9-178">`OptionsBuilder<TOptions>` 提供 [設定的多載，可](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 讓您使用最多五個服務來設定選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-178">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="d4ae9-179">建立實作為型別 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 並將其註冊為服務的型別。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-179">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="d4ae9-180">我們建議您將設定委派傳遞到 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因為建立服務更複雜。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-180">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="d4ae9-181">建立類型相當於呼叫 [設定](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)時的架構。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-181">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="d4ae9-182">呼叫 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 會註冊暫時性泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，其具有會接受所指定泛型服務類型的建構函式。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-182">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="d4ae9-183">選項驗證</span><span class="sxs-lookup"><span data-stu-id="d4ae9-183">Options validation</span></span>

<span data-ttu-id="d4ae9-184">選項驗證可讓您驗證選項值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-184">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="d4ae9-185">請考慮下列檔案 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-185">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="d4ae9-186">下列類別會系結至設定 `"MyConfig"` 區段，並套用一些 `DataAnnotations` 規則：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-186">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="d4ae9-187">下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-187">The following code:</span></span>

* <span data-ttu-id="d4ae9-188">呼叫 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> 以取得系結至類別的[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) `MyConfigOptions` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="d4ae9-189"><xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A>使用呼叫來啟用驗證 `DataAnnotations` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="d4ae9-190">`ValidateDataAnnotations`擴充方法定義于[DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet 套件中。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-190">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="d4ae9-191">針對使用 SDK 的 web 應用程式 `Microsoft.NET.Sdk.Web` ，會從共用架構隱含參考此套件。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-191">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="d4ae9-192">下列程式碼顯示設定值或驗證錯誤：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-192">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="d4ae9-193">下列程式碼會使用委派來套用更複雜的驗證規則：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-193">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="d4ae9-194">複雜驗證的 IValidateOptions</span><span class="sxs-lookup"><span data-stu-id="d4ae9-194">IValidateOptions for complex validation</span></span>

<span data-ttu-id="d4ae9-195">下列類別會實行 <xref:Microsoft.Extensions.Options.IValidateOptions`1> ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-195">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="d4ae9-196">`IValidateOptions` 可將驗證程式代碼從和移出 `StartUp` 至類別。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-196">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="d4ae9-197">使用上述程式碼，即可在中使用下列程式碼來啟用驗證 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-197">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

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

## <a name="options-post-configuration"></a><span data-ttu-id="d4ae9-198">選項設定後作業</span><span class="sxs-lookup"><span data-stu-id="d4ae9-198">Options post-configuration</span></span>

<span data-ttu-id="d4ae9-199">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 來設定設定後作業。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-199">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="d4ae9-200">設定後作業會在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生後執行：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-200">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d4ae9-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用來後置設定具名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d4ae9-202">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 後置設定所有設定執行個體：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-202">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="d4ae9-203">在啟動期間存取選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-203">Accessing options during startup</span></span>

<span data-ttu-id="d4ae9-204"><xref:Microsoft.Extensions.Options.IOptions%601> 與 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用於 `Startup.Configure`，因為服務是在 `Configure` 方法執行之前建置。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-204"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="d4ae9-205">請勿在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-205">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4ae9-206">可能會因為服務註冊的順序而有不一致的選項狀態存在。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-206">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="d4ae9-207">Options.ConfigurationExtensions NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="d4ae9-207">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="d4ae9-208">ASP.NET Core 應用程式中會隱含參考 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 套件。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-208">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d4ae9-209">選項模式使用類別來代表一組相關的設定。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-209">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="d4ae9-210">當[組態設定](xref:fundamentals/configuration/index)依案例隔離到不同的類別時，應用程式會遵守兩個重要的軟體工程準則：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-210">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="d4ae9-211">[介面隔離原則 (ISP) 或封裝](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：相依于設定的案例 (類別) 取決於所使用的設定。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-211">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="d4ae9-212">[關注點分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (不同考量)：應用程式不同部分的設定不會彼此相依或結合。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-212">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="d4ae9-213">選項也提供驗證設定資料的機制。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-213">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="d4ae9-214">如需詳細資訊，請參閱[選項驗證](#options-validation)一節。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-214">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="d4ae9-215">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4ae9-215">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4ae9-216">先決條件</span><span class="sxs-lookup"><span data-stu-id="d4ae9-216">Prerequisites</span></span>

<span data-ttu-id="d4ae9-217">參考 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)，或新增 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-217">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="d4ae9-218">選項介面</span><span class="sxs-lookup"><span data-stu-id="d4ae9-218">Options interfaces</span></span>

<span data-ttu-id="d4ae9-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 是用來擷取選項並管理 `TOptions` 執行個體的選項通知。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="d4ae9-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支援以下案例：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="d4ae9-221">變更通知</span><span class="sxs-lookup"><span data-stu-id="d4ae9-221">Change notifications</span></span>
* [<span data-ttu-id="d4ae9-222">具名選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-222">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="d4ae9-223">可重新載入的設定</span><span class="sxs-lookup"><span data-stu-id="d4ae9-223">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="d4ae9-224">選擇性選項無效判定 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="d4ae9-224">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="d4ae9-225">[設定後](#options-post-configuration)案例可讓您在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生時設定或變更選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-225">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="d4ae9-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 負責建立新的選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="d4ae9-227">它有單一 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-227">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="d4ae9-228">預設實作會接受所有已註冊的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 與 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>，並先執行所有設定，接著執行設定後作業。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-228">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="d4ae9-229">它會區別 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>，且只會呼叫適當的介面。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-229">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="d4ae9-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用來快取 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="d4ae9-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會使監視器中的選項執行個體失效，以便重新計算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-231">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="d4ae9-232">值可以使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手動導入。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-232">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="d4ae9-233"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法用於應該視需要重新建立所有具名執行個體時。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-233">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="d4ae9-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在應該於收到每個要求時重新計算選項的案例中很實用用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="d4ae9-235">如需詳細資訊，請參閱[使用 IOptionsSnapshot 重新載入設定資料](#reload-configuration-data-with-ioptionssnapshot)一節。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-235">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="d4ae9-236"><xref:Microsoft.Extensions.Options.IOptions%601> 可用於支援選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-236"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="d4ae9-237">不過，<xref:Microsoft.Extensions.Options.IOptions%601> 不支援前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 案例。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-237">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="d4ae9-238">您可以在現有架構與程式庫中繼續使用 <xref:Microsoft.Extensions.Options.IOptions%601>，此程式庫已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 介面且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的案例。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-238">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="d4ae9-239">一般選項設定</span><span class="sxs-lookup"><span data-stu-id="d4ae9-239">General options configuration</span></span>

<span data-ttu-id="d4ae9-240">一般選項設定是以範例應用程式中的範例 1 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-240">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="d4ae9-241">選項類別必須為非抽象，且具有公用的無參數建構函式。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-241">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="d4ae9-242">下列 `MyOptions` 類別有兩個屬性，`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-242">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="d4ae9-243">設定預設值為選擇性，但在下列範例中，類別建構函式會設定 `Option1` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-243">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="d4ae9-244">`Option2` 的預設值直接藉由初始化屬性來設定 (*Models/MyOptions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-244">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="d4ae9-245">`MyOptions` 類別使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 新增到服務容器，並繫結到設定：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-245">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="d4ae9-246">下列頁面模型使用 [建構函式相依性插入](xref:mvc/controllers/dependency-injection)搭配 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 來存取設定 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-246">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="d4ae9-247">範例的檔案會 *appsettings.json* 指定和的 `option1` 值 `option2` ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-247">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="d4ae9-248">當應用程式執行時，頁面模型的 `OnGet` 方法會傳回字串，顯示選項類別值：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-248">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="d4ae9-249">使用自訂 <xref:System.Configuration.ConfigurationBuilder> 從設定檔載入選項設定時，請確認已正確設定基底路徑：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-249">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="d4ae9-250">透過 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 從設定檔載入選項設定時，不需要明確設定基底路徑。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-250">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="d4ae9-251">使用委派設定簡單的選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-251">Configure simple options with a delegate</span></span>

<span data-ttu-id="d4ae9-252">使用委派設定簡單的選項是以範例應用程式中的範例 2 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-252">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="d4ae9-253">使用委派來設定選項值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-253">Use a delegate to set options values.</span></span> <span data-ttu-id="d4ae9-254">範例應用程式使用 `MyOptionsWithDelegateConfig` 類別 (*Models/MyOptionsWithDelegateConfig.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-254">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="d4ae9-255">在下列程式碼中，第二個 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-255">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d4ae9-256">它使用委派，以 `MyOptionsWithDelegateConfig` 設定繫結：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-256">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="d4ae9-257">*Index.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-257">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="d4ae9-258">您可以新增多個設定提供者。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-258">You can add multiple configuration providers.</span></span> <span data-ttu-id="d4ae9-259">設定提供者可在 NuGet 套件中找到，而且會依註冊順序套用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-259">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="d4ae9-260">如需詳細資訊，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-260">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="d4ae9-261">每次呼叫 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 時都會新增 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務到服務容器。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-261">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="d4ae9-262">在上述範例中，和的值 `Option1` 都 `Option2` 是指定于中 *appsettings.json* ，但是和的值 `Option1` `Option2` 會由設定的委派覆寫。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-262">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="d4ae9-263">啟用多個設定服務時，最後一個指定的設定來源會「勝出」並設定此組態值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-263">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="d4ae9-264">當應用程式執行時，頁面模型的 `OnGet` 方法會傳回字串，顯示選項類別值：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-264">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="d4ae9-265">子選項組態</span><span class="sxs-lookup"><span data-stu-id="d4ae9-265">Suboptions configuration</span></span>

<span data-ttu-id="d4ae9-266">子選項組態是以範例應用程式中的範例 3 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-266">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="d4ae9-267">應用程式應該建立屬於應用程式中特定案例群組 (類別) 的選項類別。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-267">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="d4ae9-268">需要組態值的應用程式組件應該只能存取它們使用的設定值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-268">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="d4ae9-269">將選項繫結至組態時，選項類型中的每一個屬性都會繫結至 `property[:sub-property:]` 格式的組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-269">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="d4ae9-270">例如， `MyOptions.Option1` 屬性會系結至索引鍵，這個索引鍵 `Option1` 是從的 `option1` 屬性中讀取的 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-270">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="d4ae9-271">在下列程式碼中，第三個 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-271">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d4ae9-272">它會系結至檔案的 `MySubOptions` 區段 `subsection` *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-272">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="d4ae9-273">`GetSection`方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空間。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-273">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="d4ae9-274">範例的檔案會 *appsettings.json* 定義 `subsection` 具有和之索引鍵的成員 `suboption1` `suboption2` ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-274">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="d4ae9-275">`MySubOptions` 類別會定義屬性 `SubOption1` 和 `SubOption2`，來保存選項值 (*Models/MySubOptions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-275">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="d4ae9-276">頁面模型的 `OnGet` 方法會傳回具有選項值 (*Pages/Index.cshtml.cs*) 的字串：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-276">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="d4ae9-277">當應用程式執行時，`OnGet` 方法會傳回字串，顯示子選項類別值：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-277">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="d4ae9-278">選項插入</span><span class="sxs-lookup"><span data-stu-id="d4ae9-278">Options injection</span></span>

<span data-ttu-id="d4ae9-279">選項插入在範例應用程式中會示範為範例4。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-279">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="d4ae9-280">插入 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-280">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="d4ae9-281">具有指示詞的 Razor 頁面或 MVC 視圖 [`@inject`](xref:mvc/views/razor#inject) Razor 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-281">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="d4ae9-282">頁面或視圖模型。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-282">A page or view model.</span></span>

<span data-ttu-id="d4ae9-283">範例應用程式中的下列範例會插入 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 頁面模型中， (*Pages/Index. cshtml*) ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-283">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="d4ae9-284">範例應用程式會顯示如何使用 `@inject` 指示詞來插入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-284">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="d4ae9-285">執行應用程式時，轉譯的頁面中會顯示選項值：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-285">When the app is run, the options values are shown in the rendered page:</span></span>

![選項值 Option1:：value1_from_json 和 Option2: -1 是從模型藉由插入至檢視來載入。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="d4ae9-287">使用 IOptionsSnapshot 重新載入設定資料</span><span class="sxs-lookup"><span data-stu-id="d4ae9-287">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="d4ae9-288">使用範例 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 應用程式中的範例5示範重載設定資料。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-288">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="d4ae9-289">使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ，在要求的存留期記憶體取和快取選項時，會針對每個要求計算一次。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-289">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="d4ae9-290">和之間的差異在於 `IOptionsMonitor` `IOptionsSnapshot` ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-290">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="d4ae9-291">`IOptionsMonitor` 是 [單一服務](xref:fundamentals/dependency-injection#singleton) ，可隨時抓取目前的選項值，這在單一相依性中特別有用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-291">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="d4ae9-292">`IOptionsSnapshot` 是限 [域的服務](xref:fundamentals/dependency-injection#scoped) ，會在建立物件時提供選項的快照 `IOptionsSnapshot<T>` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-292">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="d4ae9-293">選項快照集的設計目的是要搭配使用暫時性和限定範圍的相依性。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-293">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="d4ae9-294">下列範例示範如何在 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appsettings.json* 變更 (*頁面/索引*) 之後建立新的。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-294">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="d4ae9-295">伺服器的多個要求會傳回檔案所提供的常數值 *appsettings.json* ，直到檔案變更和設定重載為止。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-295">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="d4ae9-296">下圖顯示從檔案載入的初始 `option1` 和 `option2` 值 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-296">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="d4ae9-297">將檔案中的值變更 *appsettings.json* 為 `value1_from_json UPDATED` 和 `200` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-297">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="d4ae9-298">儲存 *appsettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-298">Save the *appsettings.json* file.</span></span> <span data-ttu-id="d4ae9-299">重新整理瀏覽器，以查看選項值更新：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-299">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="d4ae9-300">IConfigureNamedOptions 的具名選項支援</span><span class="sxs-lookup"><span data-stu-id="d4ae9-300">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="d4ae9-301"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的具名選項支援是以範例應用程式中的範例 6 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-301">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="d4ae9-302">「具名選項」支援可讓應用程式區別具名選項組態。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-302">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="d4ae9-303">在範例應用程式中，命名選項是使用 [OptionsServiceCollectionExtensions.Configu) ](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)來宣告，而這會呼叫 [>configurenamedoptions \<TOptions> 。設定](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-303">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="d4ae9-304">命名選項會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-304">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="d4ae9-305">範例應用程式會使用　<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*) 來存取具名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-305">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="d4ae9-306">執行範例應用程式後，會傳回具名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-306">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="d4ae9-307">`named_options_1` 系統會從設定中提供值，這些值是從檔案載入的 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-307">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="d4ae9-308">`named_options_2` 值是由以下提供：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-308">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="d4ae9-309">`ConfigureServices` 中 `Option1` 的 `named_options_2` 委派。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-309">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="d4ae9-310">`MyOptions` 類別所提供的 `Option2` 預設值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-310">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="d4ae9-311">使用 ConfigureAll 方法設定所有選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-311">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="d4ae9-312">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法設定所有選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-312">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="d4ae9-313">下列程式碼會為具有共通值的所有設定執行個體設定 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-313">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="d4ae9-314">將下列程式碼手動新增至 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-314">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="d4ae9-315">新增程式碼之後執行範例應用程式，就會產生下列結果：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-315">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="d4ae9-316">所有選項都是具名執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-316">All options are named instances.</span></span> <span data-ttu-id="d4ae9-317">現有的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 執行個體會視為以 `Options.DefaultName` 執行個體為目標，也就是 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-317">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="d4ae9-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 也會實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="d4ae9-319"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的預設實作有邏輯可以適當地使用每個項目。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-319">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="d4ae9-320">`null` 具名選項用來以所有具名執行個體為目標，而不是特定的具名執行個體 (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 與 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此慣例)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-320">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="d4ae9-321">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="d4ae9-321">OptionsBuilder API</span></span>

<span data-ttu-id="d4ae9-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 會用於設定 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="d4ae9-323">因為 `OptionsBuilder` 僅為初始 `AddOptions<TOptions>(string optionsName)` 呼叫的單一參數，而不是出現在所有後續呼叫的參數，所以其可簡化建立具名選項的程序。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-323">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="d4ae9-324">選項驗證及接受服務依存性的 `ConfigureOptions` 多載，只可透過 `OptionsBuilder` 使用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-324">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="d4ae9-325">使用 DI 服務來設定選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-325">Use DI services to configure options</span></span>

<span data-ttu-id="d4ae9-326">在以下列兩種方式設定選項的同時，您可以從相依性插入中存取其他服務：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-326">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="d4ae9-327">傳遞設定委派以[設定](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-327">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="d4ae9-328">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)提供[設定的多載，可](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)讓您使用最多五個服務來設定選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="d4ae9-329">建立您自己的類型來實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，並將該類型註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-329">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="d4ae9-330">我們建議您將設定委派傳遞到 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因為建立服務更複雜。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-330">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="d4ae9-331">建立您自己的類型相當於，當您使用 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 時此架構可為您執行的動作。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-331">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="d4ae9-332">呼叫 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 會註冊暫時性泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，其具有會接受所指定泛型服務類型的建構函式。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-332">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="d4ae9-333">選項驗證</span><span class="sxs-lookup"><span data-stu-id="d4ae9-333">Options validation</span></span>

<span data-ttu-id="d4ae9-334">選項驗證可讓您在設定選項之後驗證選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-334">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="d4ae9-335">搭配驗證方法呼叫 `Validate`，若選項有效會傳回 `true`，若為無效則傳回 `false`：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-335">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="d4ae9-336">上述範例會將具名的選項執行個體設定為 `optionalOptionsName`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-336">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="d4ae9-337">預設選項執行個體為 `Options.DefaultName`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-337">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="d4ae9-338">當選項執行個體建立之後，便會執行驗證。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-338">Validation runs when the options instance is created.</span></span> <span data-ttu-id="d4ae9-339">選項實例保證會在第一次存取時通過驗證。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-339">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d4ae9-340">選項驗證不會在建立選項實例之後防止修改選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-340">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="d4ae9-341">例如，在 `IOptionsSnapshot` 第一次存取選項時，會針對每個要求建立和驗證選項一次。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-341">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="d4ae9-342">在對 `IOptionsSnapshot` *相同要求進行* 後續的存取嘗試時，不會再次驗證這些選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-342">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="d4ae9-343">`Validate` 方法可接受 `Func<TOptions, bool>`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-343">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="d4ae9-344">若要完整地自訂驗證，請實作 `IValidateOptions<TOptions>`，它允許：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-344">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="d4ae9-345">多種選項類型的驗證：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="d4ae9-345">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="d4ae9-346">取決於另一個選項類型的驗證：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="d4ae9-346">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="d4ae9-347">`IValidateOptions` 可驗證：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-347">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="d4ae9-348">特定的具名選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-348">A specific named options instance.</span></span>
* <span data-ttu-id="d4ae9-349">所有選項 (當 `name` 為 `null` 時)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-349">All options when `name` is `null`.</span></span>

<span data-ttu-id="d4ae9-350">從以下的介面實作傳回 `ValidateOptionsResult`：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-350">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="d4ae9-351">透過呼叫 `OptionsBuilder<TOptions>` 上的 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 方法，就可從 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 套件使用資料註解型驗證。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-351">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="d4ae9-352">`Microsoft.Extensions.Options.DataAnnotations` 包含在 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-352">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="d4ae9-353">我們考慮在之後的版本中加入積極式驗證 (啟動時快速檢錯)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-353">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="d4ae9-354">選項設定後作業</span><span class="sxs-lookup"><span data-stu-id="d4ae9-354">Options post-configuration</span></span>

<span data-ttu-id="d4ae9-355">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 來設定設定後作業。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-355">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="d4ae9-356">設定後作業會在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生後執行：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-356">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d4ae9-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用來後置設定具名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d4ae9-358">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 後置設定所有設定執行個體：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-358">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="d4ae9-359">在啟動期間存取選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-359">Accessing options during startup</span></span>

<span data-ttu-id="d4ae9-360"><xref:Microsoft.Extensions.Options.IOptions%601> 與 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用於 `Startup.Configure`，因為服務是在 `Configure` 方法執行之前建置。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-360"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="d4ae9-361">請勿在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-361">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4ae9-362">可能會因為服務註冊的順序而有不一致的選項狀態存在。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-362">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d4ae9-363">選項模式使用類別來代表一組相關的設定。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-363">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="d4ae9-364">當[組態設定](xref:fundamentals/configuration/index)依案例隔離到不同的類別時，應用程式會遵守兩個重要的軟體工程準則：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-364">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="d4ae9-365">[介面隔離原則 (ISP) 或封裝](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：相依于設定的案例 (類別) 取決於所使用的設定。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-365">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="d4ae9-366">[關注點分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (不同考量)：應用程式不同部分的設定不會彼此相依或結合。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-366">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="d4ae9-367">選項也提供驗證設定資料的機制。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-367">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="d4ae9-368">如需詳細資訊，請參閱[選項驗證](#options-validation)一節。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-368">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="d4ae9-369">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d4ae9-369">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4ae9-370">先決條件</span><span class="sxs-lookup"><span data-stu-id="d4ae9-370">Prerequisites</span></span>

<span data-ttu-id="d4ae9-371">參考 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)，或新增 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="d4ae9-372">選項介面</span><span class="sxs-lookup"><span data-stu-id="d4ae9-372">Options interfaces</span></span>

<span data-ttu-id="d4ae9-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 是用來擷取選項並管理 `TOptions` 執行個體的選項通知。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="d4ae9-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支援以下案例：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="d4ae9-375">變更通知</span><span class="sxs-lookup"><span data-stu-id="d4ae9-375">Change notifications</span></span>
* [<span data-ttu-id="d4ae9-376">具名選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-376">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="d4ae9-377">可重新載入的設定</span><span class="sxs-lookup"><span data-stu-id="d4ae9-377">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="d4ae9-378">選擇性選項無效判定 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="d4ae9-378">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="d4ae9-379">[設定後](#options-post-configuration)案例可讓您在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生時設定或變更選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-379">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="d4ae9-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 負責建立新的選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="d4ae9-381">它有單一 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-381">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="d4ae9-382">預設實作會接受所有已註冊的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 與 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>，並先執行所有設定，接著執行設定後作業。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-382">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="d4ae9-383">它會區別 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>，且只會呼叫適當的介面。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-383">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="d4ae9-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用來快取 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="d4ae9-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 會使監視器中的選項執行個體失效，以便重新計算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-385">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="d4ae9-386">值可以使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手動導入。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-386">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="d4ae9-387"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法用於應該視需要重新建立所有具名執行個體時。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-387">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="d4ae9-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在應該於收到每個要求時重新計算選項的案例中很實用用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="d4ae9-389">如需詳細資訊，請參閱[使用 IOptionsSnapshot 重新載入設定資料](#reload-configuration-data-with-ioptionssnapshot)一節。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-389">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="d4ae9-390"><xref:Microsoft.Extensions.Options.IOptions%601> 可用於支援選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-390"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="d4ae9-391">不過，<xref:Microsoft.Extensions.Options.IOptions%601> 不支援前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 案例。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-391">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="d4ae9-392">您可以在現有架構與程式庫中繼續使用 <xref:Microsoft.Extensions.Options.IOptions%601>，此程式庫已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 介面且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的案例。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-392">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="d4ae9-393">一般選項設定</span><span class="sxs-lookup"><span data-stu-id="d4ae9-393">General options configuration</span></span>

<span data-ttu-id="d4ae9-394">一般選項設定是以範例應用程式中的範例 1 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-394">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="d4ae9-395">選項類別必須為非抽象，且具有公用的無參數建構函式。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-395">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="d4ae9-396">下列 `MyOptions` 類別有兩個屬性，`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-396">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="d4ae9-397">設定預設值為選擇性，但在下列範例中，類別建構函式會設定 `Option1` 的預設值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-397">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="d4ae9-398">`Option2` 的預設值直接藉由初始化屬性來設定 (*Models/MyOptions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-398">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="d4ae9-399">`MyOptions` 類別使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 新增到服務容器，並繫結到設定：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-399">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="d4ae9-400">下列頁面模型使用 [建構函式相依性插入](xref:mvc/controllers/dependency-injection)搭配 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 來存取設定 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-400">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="d4ae9-401">範例的檔案會 *appsettings.json* 指定和的 `option1` 值 `option2` ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-401">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="d4ae9-402">當應用程式執行時，頁面模型的 `OnGet` 方法會傳回字串，顯示選項類別值：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-402">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="d4ae9-403">使用自訂 <xref:System.Configuration.ConfigurationBuilder> 從設定檔載入選項設定時，請確認已正確設定基底路徑：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-403">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="d4ae9-404">透過 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 從設定檔載入選項設定時，不需要明確設定基底路徑。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-404">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="d4ae9-405">使用委派設定簡單的選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-405">Configure simple options with a delegate</span></span>

<span data-ttu-id="d4ae9-406">使用委派設定簡單的選項是以範例應用程式中的範例 2 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-406">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="d4ae9-407">使用委派來設定選項值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-407">Use a delegate to set options values.</span></span> <span data-ttu-id="d4ae9-408">範例應用程式使用 `MyOptionsWithDelegateConfig` 類別 (*Models/MyOptionsWithDelegateConfig.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-408">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="d4ae9-409">在下列程式碼中，第二個 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-409">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d4ae9-410">它使用委派，以 `MyOptionsWithDelegateConfig` 設定繫結：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-410">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="d4ae9-411">*Index.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-411">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="d4ae9-412">您可以新增多個設定提供者。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-412">You can add multiple configuration providers.</span></span> <span data-ttu-id="d4ae9-413">設定提供者可在 NuGet 套件中找到，而且會依註冊順序套用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-413">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="d4ae9-414">如需詳細資訊，請參閱 <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-414">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="d4ae9-415">每次呼叫 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 時都會新增 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務到服務容器。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-415">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="d4ae9-416">在上述範例中，和的值 `Option1` 都 `Option2` 是指定于中 *appsettings.json* ，但是和的值 `Option1` `Option2` 會由設定的委派覆寫。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-416">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="d4ae9-417">啟用多個設定服務時，最後一個指定的設定來源會「勝出」並設定此組態值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-417">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="d4ae9-418">當應用程式執行時，頁面模型的 `OnGet` 方法會傳回字串，顯示選項類別值：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-418">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="d4ae9-419">子選項組態</span><span class="sxs-lookup"><span data-stu-id="d4ae9-419">Suboptions configuration</span></span>

<span data-ttu-id="d4ae9-420">子選項組態是以範例應用程式中的範例 3 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-420">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="d4ae9-421">應用程式應該建立屬於應用程式中特定案例群組 (類別) 的選項類別。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-421">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="d4ae9-422">需要組態值的應用程式組件應該只能存取它們使用的設定值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-422">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="d4ae9-423">將選項繫結至組態時，選項類型中的每一個屬性都會繫結至 `property[:sub-property:]` 格式的組態索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-423">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="d4ae9-424">例如， `MyOptions.Option1` 屬性會系結至索引鍵，這個索引鍵 `Option1` 是從的 `option1` 屬性中讀取的 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-424">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="d4ae9-425">在下列程式碼中，第三個 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-425">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d4ae9-426">它會系結至檔案的 `MySubOptions` 區段 `subsection` *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-426">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="d4ae9-427">`GetSection`方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空間。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-427">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="d4ae9-428">範例的檔案會 *appsettings.json* 定義 `subsection` 具有和之索引鍵的成員 `suboption1` `suboption2` ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-428">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="d4ae9-429">`MySubOptions` 類別會定義屬性 `SubOption1` 和 `SubOption2`，來保存選項值 (*Models/MySubOptions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-429">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="d4ae9-430">頁面模型的 `OnGet` 方法會傳回具有選項值 (*Pages/Index.cshtml.cs*) 的字串：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-430">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="d4ae9-431">當應用程式執行時，`OnGet` 方法會傳回字串，顯示子選項類別值：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-431">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="d4ae9-432">檢視模型提供的選項或使用直接檢視插入提供的選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-432">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="d4ae9-433">檢視模型提供的選項或使用直接檢視插入提供的選項是以範例應用程式中的範例 4 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-433">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="d4ae9-434">可以在檢視模型中提供選項，或藉由將 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接插入至檢視來提供選項 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-434">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="d4ae9-435">範例應用程式會顯示如何使用 `@inject` 指示詞來插入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-435">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="d4ae9-436">執行應用程式時，轉譯的頁面中會顯示選項值：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-436">When the app is run, the options values are shown in the rendered page:</span></span>

![選項值 Option1:：value1_from_json 和 Option2: -1 是從模型藉由插入至檢視來載入。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="d4ae9-438">使用 IOptionsSnapshot 重新載入設定資料</span><span class="sxs-lookup"><span data-stu-id="d4ae9-438">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="d4ae9-439">使用範例 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 應用程式中的範例5示範重載設定資料。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-439">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="d4ae9-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支援以最小的處理負擔來重新載入選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="d4ae9-441">在要求的存留期內存取及快取選項時，會針對每個要求計算一次選項。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-441">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="d4ae9-442">下列範例示範如何在 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> *appsettings.json* 變更 (*頁面/索引*) 之後建立新的。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-442">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="d4ae9-443">伺服器的多個要求會傳回檔案所提供的常數值 *appsettings.json* ，直到檔案變更和設定重載為止。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-443">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="d4ae9-444">下圖顯示從檔案載入的初始 `option1` 和 `option2` 值 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-444">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="d4ae9-445">將檔案中的值變更 *appsettings.json* 為 `value1_from_json UPDATED` 和 `200` 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-445">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="d4ae9-446">儲存 *appsettings.json* 檔案。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-446">Save the *appsettings.json* file.</span></span> <span data-ttu-id="d4ae9-447">重新整理瀏覽器，以查看選項值更新：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-447">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="d4ae9-448">IConfigureNamedOptions 的具名選項支援</span><span class="sxs-lookup"><span data-stu-id="d4ae9-448">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="d4ae9-449"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的具名選項支援是以範例應用程式中的範例 6 來示範。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-449">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="d4ae9-450">「具名選項」支援可讓應用程式區別具名選項組態。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-450">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="d4ae9-451">在範例應用程式中，命名選項是使用 [OptionsServiceCollectionExtensions.Configu) ](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)來宣告，而這會呼叫 [>configurenamedoptions \<TOptions> 。設定](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-451">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="d4ae9-452">命名選項會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-452">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="d4ae9-453">範例應用程式會使用　<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*) 來存取具名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-453">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="d4ae9-454">執行範例應用程式後，會傳回具名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-454">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="d4ae9-455">`named_options_1` 系統會從設定中提供值，這些值是從檔案載入的 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-455">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="d4ae9-456">`named_options_2` 值是由以下提供：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-456">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="d4ae9-457">`ConfigureServices` 中 `Option1` 的 `named_options_2` 委派。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-457">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="d4ae9-458">`MyOptions` 類別所提供的 `Option2` 預設值。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-458">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="d4ae9-459">使用 ConfigureAll 方法設定所有選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-459">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="d4ae9-460">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法設定所有選項執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-460">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="d4ae9-461">下列程式碼會為具有共通值的所有設定執行個體設定 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-461">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="d4ae9-462">將下列程式碼手動新增至 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-462">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="d4ae9-463">新增程式碼之後執行範例應用程式，就會產生下列結果：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-463">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="d4ae9-464">所有選項都是具名執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-464">All options are named instances.</span></span> <span data-ttu-id="d4ae9-465">現有的 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 執行個體會視為以 `Options.DefaultName` 執行個體為目標，也就是 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-465">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="d4ae9-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 也會實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="d4ae9-467"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的預設實作有邏輯可以適當地使用每個項目。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-467">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="d4ae9-468">`null` 具名選項用來以所有具名執行個體為目標，而不是特定的具名執行個體 (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 與 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此慣例)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-468">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="d4ae9-469">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="d4ae9-469">OptionsBuilder API</span></span>

<span data-ttu-id="d4ae9-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 會用於設定 `TOptions` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="d4ae9-471">因為 `OptionsBuilder` 僅為初始 `AddOptions<TOptions>(string optionsName)` 呼叫的單一參數，而不是出現在所有後續呼叫的參數，所以其可簡化建立具名選項的程序。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-471">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="d4ae9-472">選項驗證及接受服務依存性的 `ConfigureOptions` 多載，只可透過 `OptionsBuilder` 使用。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-472">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="d4ae9-473">使用 DI 服務來設定選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-473">Use DI services to configure options</span></span>

<span data-ttu-id="d4ae9-474">在以下列兩種方式設定選項的同時，您可以從相依性插入中存取其他服務：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-474">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="d4ae9-475">傳遞設定委派以[設定](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-475">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="d4ae9-476">[OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1)提供[設定的多載，可](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)讓您使用最多五個服務來設定選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="d4ae9-477">建立您自己的類型來實作 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，並將該類型註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-477">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="d4ae9-478">我們建議您將設定委派傳遞到 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因為建立服務更複雜。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-478">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="d4ae9-479">建立您自己的類型相當於，當您使用 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 時此架構可為您執行的動作。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-479">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="d4ae9-480">呼叫 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 會註冊暫時性泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，其具有會接受所指定泛型服務類型的建構函式。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-480">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="d4ae9-481">選項設定後作業</span><span class="sxs-lookup"><span data-stu-id="d4ae9-481">Options post-configuration</span></span>

<span data-ttu-id="d4ae9-482">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 來設定設定後作業。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-482">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="d4ae9-483">設定後作業會在所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 設定發生後執行：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-483">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d4ae9-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用來後置設定具名選項：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d4ae9-485">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 後置設定所有設定執行個體：</span><span class="sxs-lookup"><span data-stu-id="d4ae9-485">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="d4ae9-486">在啟動期間存取選項</span><span class="sxs-lookup"><span data-stu-id="d4ae9-486">Accessing options during startup</span></span>

<span data-ttu-id="d4ae9-487"><xref:Microsoft.Extensions.Options.IOptions%601> 與 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用於 `Startup.Configure`，因為服務是在 `Configure` 方法執行之前建置。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-487"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="d4ae9-488">請勿在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-488">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4ae9-489">可能會因為服務註冊的順序而有不一致的選項狀態存在。</span><span class="sxs-lookup"><span data-stu-id="d4ae9-489">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d4ae9-490">其他資源</span><span class="sxs-lookup"><span data-stu-id="d4ae9-490">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
