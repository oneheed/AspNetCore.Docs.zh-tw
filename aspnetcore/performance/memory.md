---
title: ASP.NET Core 中的記憶體管理和模式
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 中管理記憶體，以及垃圾收集行程 (GC) 如何運作。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: performance/memory
ms.openlocfilehash: 6d2a89ec7c64728bc585ad235293f2277f9a66f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061479"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="774a9-103">ASP.NET Core 中的記憶體管理和垃圾收集 (GC) </span><span class="sxs-lookup"><span data-stu-id="774a9-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="774a9-104">[Sébastien Ros](https://github.com/sebastienros)與[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="774a9-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="774a9-105">記憶體管理很複雜，即使在 .NET 之類的 managed 架構中也是如此。</span><span class="sxs-lookup"><span data-stu-id="774a9-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="774a9-106">分析和瞭解記憶體問題可能很困難。</span><span class="sxs-lookup"><span data-stu-id="774a9-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="774a9-107">這篇文章：</span><span class="sxs-lookup"><span data-stu-id="774a9-107">This article:</span></span>

* <span data-ttu-id="774a9-108">有許多 *記憶體* 流失和 *GC 無法正常運作* 的問題。</span><span class="sxs-lookup"><span data-stu-id="774a9-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="774a9-109">這些問題大多是因為不了解記憶體耗用量在 .NET Core 中的運作方式所造成，或是不了解它的測量方式。</span><span class="sxs-lookup"><span data-stu-id="774a9-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="774a9-110">示範有問題的記憶體使用量，並建議替代方法。</span><span class="sxs-lookup"><span data-stu-id="774a9-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="774a9-111">垃圾收集 (GC) 如何在 .NET Core 中運作</span><span class="sxs-lookup"><span data-stu-id="774a9-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="774a9-112">GC 會配置堆積區段，其中每個區段都是連續的記憶體範圍。</span><span class="sxs-lookup"><span data-stu-id="774a9-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="774a9-113">放在堆積中的物件會分類成3個層代的其中一個：0、1或2。</span><span class="sxs-lookup"><span data-stu-id="774a9-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="774a9-114">世代會決定 GC 嘗試釋放應用程式已不再參考的受控物件記憶體的頻率。</span><span class="sxs-lookup"><span data-stu-id="774a9-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="774a9-115">較低編號的層代會更頻繁地成為 GC。</span><span class="sxs-lookup"><span data-stu-id="774a9-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="774a9-116">物件會根據其存留期從一個世代移至另一個。</span><span class="sxs-lookup"><span data-stu-id="774a9-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="774a9-117">當物件存留較久時，它們就會移至較高的層代。</span><span class="sxs-lookup"><span data-stu-id="774a9-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="774a9-118">如先前所述，較高的層代較不常進行 GC。</span><span class="sxs-lookup"><span data-stu-id="774a9-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="774a9-119">短期存留期物件永遠會保留在層代0中。</span><span class="sxs-lookup"><span data-stu-id="774a9-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="774a9-120">例如，在 web 要求存留期間所參考的物件會存留期很短。</span><span class="sxs-lookup"><span data-stu-id="774a9-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="774a9-121">應用層級 [singleton](xref:fundamentals/dependency-injection#service-lifetimes) 通常會遷移到第2代。</span><span class="sxs-lookup"><span data-stu-id="774a9-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="774a9-122">當 ASP.NET Core 應用程式啟動時，GC：</span><span class="sxs-lookup"><span data-stu-id="774a9-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="774a9-123">保留一些記憶體給初始堆積區段。</span><span class="sxs-lookup"><span data-stu-id="774a9-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="774a9-124">在載入執行時間時認可小部分的記憶體。</span><span class="sxs-lookup"><span data-stu-id="774a9-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="774a9-125">先前的記憶體配置是基於效能因素而完成。</span><span class="sxs-lookup"><span data-stu-id="774a9-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="774a9-126">效能優點來自于連續記憶體中的堆積區段。</span><span class="sxs-lookup"><span data-stu-id="774a9-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="774a9-127">呼叫 GC。收集</span><span class="sxs-lookup"><span data-stu-id="774a9-127">Call GC.Collect</span></span>

<span data-ttu-id="774a9-128">呼叫 [GC。明確收集](xref:System.GC.Collect*) ：</span><span class="sxs-lookup"><span data-stu-id="774a9-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="774a9-129">**不** 應由生產環境 ASP.NET Core 應用程式完成。</span><span class="sxs-lookup"><span data-stu-id="774a9-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="774a9-130">在調查記憶體流失時很實用。</span><span class="sxs-lookup"><span data-stu-id="774a9-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="774a9-131">進行調查時，會驗證 GC 是否已從記憶體中移除所有的無關聯物件，以便測量記憶體。</span><span class="sxs-lookup"><span data-stu-id="774a9-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="774a9-132">分析應用程式的記憶體使用量</span><span class="sxs-lookup"><span data-stu-id="774a9-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="774a9-133">專用工具可協助分析記憶體使用量：</span><span class="sxs-lookup"><span data-stu-id="774a9-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="774a9-134">計算物件參考</span><span class="sxs-lookup"><span data-stu-id="774a9-134">Counting object references</span></span>
- <span data-ttu-id="774a9-135">測量 GC 對 CPU 使用量的影響程度</span><span class="sxs-lookup"><span data-stu-id="774a9-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="774a9-136">測量每個世代所使用的記憶體空間</span><span class="sxs-lookup"><span data-stu-id="774a9-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="774a9-137">使用下列工具來分析記憶體使用量：</span><span class="sxs-lookup"><span data-stu-id="774a9-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="774a9-138">[dotnet-追蹤](/dotnet/core/diagnostics/dotnet-trace)：可用於生產機器。</span><span class="sxs-lookup"><span data-stu-id="774a9-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="774a9-139">在沒有 Visual Studio 偵錯工具的情況下分析記憶體使用量</span><span class="sxs-lookup"><span data-stu-id="774a9-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="774a9-140">分析 Visual Studio 中的記憶體使用量</span><span class="sxs-lookup"><span data-stu-id="774a9-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="774a9-141">偵測記憶體問題</span><span class="sxs-lookup"><span data-stu-id="774a9-141">Detecting memory issues</span></span>

<span data-ttu-id="774a9-142">工作管理員可以用來瞭解 ASP.NET 應用程式所使用的記憶體數量。</span><span class="sxs-lookup"><span data-stu-id="774a9-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="774a9-143">工作管理員的記憶體值：</span><span class="sxs-lookup"><span data-stu-id="774a9-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="774a9-144">代表 ASP.NET 進程使用的記憶體數量。</span><span class="sxs-lookup"><span data-stu-id="774a9-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="774a9-145">包含應用程式的生活物件和其他記憶體取用者（例如原生記憶體使用量）。</span><span class="sxs-lookup"><span data-stu-id="774a9-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="774a9-146">如果工作管理員的記憶體值無限期地增加，且不會壓平合併，則應用程式會發生記憶體流失。</span><span class="sxs-lookup"><span data-stu-id="774a9-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="774a9-147">下列各節將示範和說明數種記憶體使用模式。</span><span class="sxs-lookup"><span data-stu-id="774a9-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="774a9-148">範例顯示記憶體使用量應用程式</span><span class="sxs-lookup"><span data-stu-id="774a9-148">Sample display memory usage app</span></span>

<span data-ttu-id="774a9-149">[MemoryLeak 範例應用程式](https://github.com/sebastienros/memoryleak)可在 GitHub 上取得。</span><span class="sxs-lookup"><span data-stu-id="774a9-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="774a9-150">MemoryLeak 應用程式：</span><span class="sxs-lookup"><span data-stu-id="774a9-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="774a9-151">包含診斷控制器，可收集應用程式的即時記憶體和 GC 資料。</span><span class="sxs-lookup"><span data-stu-id="774a9-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="774a9-152">具有顯示記憶體和 GC 資料的索引頁面。</span><span class="sxs-lookup"><span data-stu-id="774a9-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="774a9-153">索引頁面會每秒重新整理一次。</span><span class="sxs-lookup"><span data-stu-id="774a9-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="774a9-154">包含提供各種記憶體負載模式的 API 控制器。</span><span class="sxs-lookup"><span data-stu-id="774a9-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="774a9-155">不是支援的工具，但可以用來顯示 ASP.NET Core 應用程式的記憶體使用模式。</span><span class="sxs-lookup"><span data-stu-id="774a9-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="774a9-156">執行 MemoryLeak。</span><span class="sxs-lookup"><span data-stu-id="774a9-156">Run MemoryLeak.</span></span> <span data-ttu-id="774a9-157">配置的記憶體會在 GC 發生之前變慢。</span><span class="sxs-lookup"><span data-stu-id="774a9-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="774a9-158">因為此工具會配置自訂物件來捕捉資料，所以記憶體會增加。</span><span class="sxs-lookup"><span data-stu-id="774a9-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="774a9-159">下圖顯示 Gen 0 GC 發生時的 MemoryLeak 索引頁面。</span><span class="sxs-lookup"><span data-stu-id="774a9-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="774a9-160">此圖表顯示0個 RPS (每秒要求數) ，因為未呼叫 API 控制器中的 API 端點。</span><span class="sxs-lookup"><span data-stu-id="774a9-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![上圖](memory/_static/0RPS.png)

<span data-ttu-id="774a9-162">此圖表會顯示記憶體使用量的兩個值：</span><span class="sxs-lookup"><span data-stu-id="774a9-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="774a9-163">配置： managed 物件所佔用的記憶體數量</span><span class="sxs-lookup"><span data-stu-id="774a9-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="774a9-164">[工作集](/windows/win32/memory/working-set)：進程的虛擬位址空間中的一組頁面，目前仍在實體記憶體中。</span><span class="sxs-lookup"><span data-stu-id="774a9-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="774a9-165">顯示的工作集與工作管理員顯示的值相同。</span><span class="sxs-lookup"><span data-stu-id="774a9-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="774a9-166">暫時性物件</span><span class="sxs-lookup"><span data-stu-id="774a9-166">Transient objects</span></span>

<span data-ttu-id="774a9-167">下列 API 會建立 10 KB 的字串實例，並將它傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="774a9-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="774a9-168">在每個要求中，會在記憶體中配置新的物件，並將其寫入至回應。</span><span class="sxs-lookup"><span data-stu-id="774a9-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="774a9-169">字串在 .NET 中會儲存為 UTF-16 字元，因此每個字元在記憶體中會佔用2個位元組。</span><span class="sxs-lookup"><span data-stu-id="774a9-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="774a9-170">下圖產生的負載相對較小，以顯示 GC 對記憶體配置有何影響。</span><span class="sxs-lookup"><span data-stu-id="774a9-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![上圖](memory/_static/bigstring.png)

<span data-ttu-id="774a9-172">上圖顯示：</span><span class="sxs-lookup"><span data-stu-id="774a9-172">The preceding chart shows:</span></span>

* <span data-ttu-id="774a9-173">每秒 4K RPS (要求) 。</span><span class="sxs-lookup"><span data-stu-id="774a9-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="774a9-174">第0代 GC 回收大約每兩秒發生一次。</span><span class="sxs-lookup"><span data-stu-id="774a9-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="774a9-175">工作集的常數大約為 500 MB。</span><span class="sxs-lookup"><span data-stu-id="774a9-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="774a9-176">CPU 是12%。</span><span class="sxs-lookup"><span data-stu-id="774a9-176">CPU is 12%.</span></span>
* <span data-ttu-id="774a9-177">透過 GC) 的記憶體耗用量和發行 (是穩定的。</span><span class="sxs-lookup"><span data-stu-id="774a9-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="774a9-178">下圖是以機器可處理的最大輸送量為單位。</span><span class="sxs-lookup"><span data-stu-id="774a9-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![上圖](memory/_static/bigstring2.png)

<span data-ttu-id="774a9-180">上圖顯示：</span><span class="sxs-lookup"><span data-stu-id="774a9-180">The preceding chart shows:</span></span>

* <span data-ttu-id="774a9-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="774a9-181">22K RPS</span></span>
* <span data-ttu-id="774a9-182">層代 0 GC 回收會每秒發生數次。</span><span class="sxs-lookup"><span data-stu-id="774a9-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="774a9-183">系統會觸發第1代回收，因為應用程式每秒配置的記憶體會大幅增加。</span><span class="sxs-lookup"><span data-stu-id="774a9-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="774a9-184">工作集的常數大約為 500 MB。</span><span class="sxs-lookup"><span data-stu-id="774a9-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="774a9-185">CPU 為33%。</span><span class="sxs-lookup"><span data-stu-id="774a9-185">CPU is 33%.</span></span>
* <span data-ttu-id="774a9-186">透過 GC) 的記憶體耗用量和發行 (是穩定的。</span><span class="sxs-lookup"><span data-stu-id="774a9-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="774a9-187">CPU (33% ) 未過度使用，因此垃圾收集可能會跟上大量的配置。</span><span class="sxs-lookup"><span data-stu-id="774a9-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="774a9-188">工作站 GC 與伺服器 GC 的比較</span><span class="sxs-lookup"><span data-stu-id="774a9-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="774a9-189">.NET 垃圾收集行程有兩種不同的模式：</span><span class="sxs-lookup"><span data-stu-id="774a9-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="774a9-190">**工作站 GC** ：已針對桌面優化。</span><span class="sxs-lookup"><span data-stu-id="774a9-190">**Workstation GC** : Optimized for the desktop.</span></span>
* <span data-ttu-id="774a9-191">**伺服器 GC** 。</span><span class="sxs-lookup"><span data-stu-id="774a9-191">**Server GC** .</span></span> <span data-ttu-id="774a9-192">ASP.NET Core 應用程式的預設 GC。</span><span class="sxs-lookup"><span data-stu-id="774a9-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="774a9-193">已針對伺服器優化。</span><span class="sxs-lookup"><span data-stu-id="774a9-193">Optimized for the server.</span></span>

<span data-ttu-id="774a9-194">GC 模式可以在專案檔或已發佈應用程式的 *runtimeconfig.js* 檔案中明確設定。</span><span class="sxs-lookup"><span data-stu-id="774a9-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="774a9-195">下列標記會顯示專案檔中的設定 `ServerGarbageCollection` ：</span><span class="sxs-lookup"><span data-stu-id="774a9-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="774a9-196">變更 `ServerGarbageCollection` 專案檔需要重建應用程式。</span><span class="sxs-lookup"><span data-stu-id="774a9-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="774a9-197">**注意：** 在具有單一核心的機器上 **無法** 使用伺服器垃圾收集。</span><span class="sxs-lookup"><span data-stu-id="774a9-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="774a9-198">如需詳細資訊，請參閱<xref:System.Runtime.GCSettings.IsServerGC>。</span><span class="sxs-lookup"><span data-stu-id="774a9-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="774a9-199">下圖顯示使用工作站 GC 的充分測 RPS 下的記憶體設定檔。</span><span class="sxs-lookup"><span data-stu-id="774a9-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![上圖](memory/_static/workstation.png)

<span data-ttu-id="774a9-201">此圖表與伺服器版本之間的差異很重要：</span><span class="sxs-lookup"><span data-stu-id="774a9-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="774a9-202">工作集從 500 MB 降至 70 MB。</span><span class="sxs-lookup"><span data-stu-id="774a9-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="774a9-203">GC 每秒會執行一次層代0回收，而不是每隔兩秒。</span><span class="sxs-lookup"><span data-stu-id="774a9-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="774a9-204">GC 從 300 MB 降至 10 MB。</span><span class="sxs-lookup"><span data-stu-id="774a9-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="774a9-205">在一般的 web 伺服器環境中，CPU 使用量比記憶體更重要，因此伺服器 GC 更好。</span><span class="sxs-lookup"><span data-stu-id="774a9-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="774a9-206">如果記憶體使用率很高，且 CPU 使用率相對較低，則工作站 GC 可能會更有效率。</span><span class="sxs-lookup"><span data-stu-id="774a9-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="774a9-207">例如，在記憶體不足的情況下裝載數個 web 應用程式的高密度。</span><span class="sxs-lookup"><span data-stu-id="774a9-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="774a9-208">使用 Docker 和小型容器的 GC</span><span class="sxs-lookup"><span data-stu-id="774a9-208">GC using Docker and small containers</span></span>

<span data-ttu-id="774a9-209">當有多個容器化應用程式在一部電腦上執行時，工作站 GC 可能比伺服器 GC 更 preformant。</span><span class="sxs-lookup"><span data-stu-id="774a9-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="774a9-210">如需詳細資訊，請參閱在小型容器中以 [伺服器 gc](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) 執行，並 [在小型容器案例中以伺服器 Gc 執行，第1部分-GC 堆積的固定限制](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)。</span><span class="sxs-lookup"><span data-stu-id="774a9-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="774a9-211">持續性物件參考</span><span class="sxs-lookup"><span data-stu-id="774a9-211">Persistent object references</span></span>

<span data-ttu-id="774a9-212">GC 無法釋放參考的物件。</span><span class="sxs-lookup"><span data-stu-id="774a9-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="774a9-213">參考但不再需要的物件會導致記憶體流失。</span><span class="sxs-lookup"><span data-stu-id="774a9-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="774a9-214">如果應用程式經常設定物件，並且在不再需要物件之後無法釋放它們，記憶體使用量將會隨時間增加。</span><span class="sxs-lookup"><span data-stu-id="774a9-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="774a9-215">下列 API 會建立 10 KB 的字串實例，並將它傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="774a9-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="774a9-216">與上一個範例的差異在於，這個實例是由靜態成員所參考，這表示它永遠不能用於集合。</span><span class="sxs-lookup"><span data-stu-id="774a9-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="774a9-217">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="774a9-217">The preceding code:</span></span>

* <span data-ttu-id="774a9-218">這是典型記憶體流失的範例。</span><span class="sxs-lookup"><span data-stu-id="774a9-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="774a9-219">使用頻繁的呼叫，會導致應用程式記憶體增加，直到進程損毀併發生 `OutOfMemory` 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="774a9-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![上圖](memory/_static/eternal.png)

<span data-ttu-id="774a9-221">在上圖中：</span><span class="sxs-lookup"><span data-stu-id="774a9-221">In the preceding image:</span></span>

* <span data-ttu-id="774a9-222">負載測試 `/api/staticstring` 端點會導致記憶體中的線性增加。</span><span class="sxs-lookup"><span data-stu-id="774a9-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="774a9-223">GC 會藉由呼叫層代2回收，嘗試在記憶體壓力增加時釋出記憶體。</span><span class="sxs-lookup"><span data-stu-id="774a9-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="774a9-224">GC 無法釋放流失的記憶體。</span><span class="sxs-lookup"><span data-stu-id="774a9-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="774a9-225">配置的工作集與時間的增加。</span><span class="sxs-lookup"><span data-stu-id="774a9-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="774a9-226">某些案例（例如快取）需要保留物件參考，直到記憶體壓力強制釋放它們為止。</span><span class="sxs-lookup"><span data-stu-id="774a9-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="774a9-227"><xref:System.WeakReference>類別可用於這種類型的快取程式碼。</span><span class="sxs-lookup"><span data-stu-id="774a9-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="774a9-228">`WeakReference`在記憶體壓力下收集物件。</span><span class="sxs-lookup"><span data-stu-id="774a9-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="774a9-229">的預設實作為 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 使用 `WeakReference` 。</span><span class="sxs-lookup"><span data-stu-id="774a9-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="774a9-230">原生記憶體</span><span class="sxs-lookup"><span data-stu-id="774a9-230">Native memory</span></span>

<span data-ttu-id="774a9-231">某些 .NET Core 物件依賴原生記憶體。</span><span class="sxs-lookup"><span data-stu-id="774a9-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="774a9-232">GC **無法** 收集原生記憶體。</span><span class="sxs-lookup"><span data-stu-id="774a9-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="774a9-233">使用原生記憶體的 .NET 物件必須使用機器碼來釋放它。</span><span class="sxs-lookup"><span data-stu-id="774a9-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="774a9-234">.NET 提供 <xref:System.IDisposable> 可讓開發人員釋放原生記憶體的介面。</span><span class="sxs-lookup"><span data-stu-id="774a9-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="774a9-235">即使未 <xref:System.IDisposable.Dispose*> 呼叫，也會 `Dispose` 在完成項執行時， [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors)正確實作為類別的呼叫。</span><span class="sxs-lookup"><span data-stu-id="774a9-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="774a9-236">請考慮下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="774a9-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="774a9-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) 是 managed 類別，因此會在要求結束時收集任何實例。</span><span class="sxs-lookup"><span data-stu-id="774a9-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="774a9-238">下圖顯示連續叫用 API 時的記憶體設定檔 `fileprovider` 。</span><span class="sxs-lookup"><span data-stu-id="774a9-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![上圖](memory/_static/fileprovider.png)

<span data-ttu-id="774a9-240">上圖顯示此類別的執行明顯問題，因為它會持續增加記憶體使用量。</span><span class="sxs-lookup"><span data-stu-id="774a9-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="774a9-241">這是在 [這個問題](https://github.com/dotnet/aspnetcore/issues/3110)中所追蹤的已知問題。</span><span class="sxs-lookup"><span data-stu-id="774a9-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="774a9-242">在使用者程式碼中，可能會有下列其中一種情況發生相同的洩漏：</span><span class="sxs-lookup"><span data-stu-id="774a9-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="774a9-243">未正確釋放類別。</span><span class="sxs-lookup"><span data-stu-id="774a9-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="774a9-244">忘記叫 `Dispose` 用應該處置之相依物件的方法。</span><span class="sxs-lookup"><span data-stu-id="774a9-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="774a9-245">大型物件堆積</span><span class="sxs-lookup"><span data-stu-id="774a9-245">Large objects heap</span></span>

<span data-ttu-id="774a9-246">頻繁的記憶體配置/可用迴圈可將記憶體片段化，尤其是在配置大型記憶體區塊時。</span><span class="sxs-lookup"><span data-stu-id="774a9-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="774a9-247">物件會配置在連續的記憶體區塊中。</span><span class="sxs-lookup"><span data-stu-id="774a9-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="774a9-248">若要減少片段，當 GC 釋出記憶體時，它會嘗試將它重組。</span><span class="sxs-lookup"><span data-stu-id="774a9-248">To mitigate fragmentation, when the GC frees memory, it tries to defragment it.</span></span> <span data-ttu-id="774a9-249">此進程稱為「 **壓縮** 」。</span><span class="sxs-lookup"><span data-stu-id="774a9-249">This process is called **compaction** .</span></span> <span data-ttu-id="774a9-250">壓縮牽涉到移動物件。</span><span class="sxs-lookup"><span data-stu-id="774a9-250">Compaction involves moving objects.</span></span> <span data-ttu-id="774a9-251">移動大型物件會對效能造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="774a9-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="774a9-252">基於這個理由，GC 會為 _大型_ 物件建立特殊的記憶體區域，稱為 [大型物件堆積](/dotnet/standard/garbage-collection/large-object-heap) (LOH) 。</span><span class="sxs-lookup"><span data-stu-id="774a9-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="774a9-253">大於85000個位元組的物件 (大約 83 KB) 為：</span><span class="sxs-lookup"><span data-stu-id="774a9-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="774a9-254">放在 LOH 上。</span><span class="sxs-lookup"><span data-stu-id="774a9-254">Placed on the LOH.</span></span>
* <span data-ttu-id="774a9-255">未壓縮。</span><span class="sxs-lookup"><span data-stu-id="774a9-255">Not compacted.</span></span>
* <span data-ttu-id="774a9-256">在層代 2 Gc 期間收集。</span><span class="sxs-lookup"><span data-stu-id="774a9-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="774a9-257">當 LOH 已滿時，GC 將會觸發層代2回收。</span><span class="sxs-lookup"><span data-stu-id="774a9-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="774a9-258">第2代集合：</span><span class="sxs-lookup"><span data-stu-id="774a9-258">Generation 2 collections:</span></span>

* <span data-ttu-id="774a9-259">本質上很慢。</span><span class="sxs-lookup"><span data-stu-id="774a9-259">Are inherently slow.</span></span>
* <span data-ttu-id="774a9-260">此外，也會產生在所有其他層代上觸發集合的成本。</span><span class="sxs-lookup"><span data-stu-id="774a9-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="774a9-261">下列程式碼會立即壓縮 LOH：</span><span class="sxs-lookup"><span data-stu-id="774a9-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="774a9-262"><xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>如需壓縮 LOH 的詳細資訊，請參閱。</span><span class="sxs-lookup"><span data-stu-id="774a9-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="774a9-263">在使用 .NET Core 3.0 和更新版本的容器中，LOH 會自動壓縮。</span><span class="sxs-lookup"><span data-stu-id="774a9-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="774a9-264">下列 API 會說明此行為：</span><span class="sxs-lookup"><span data-stu-id="774a9-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="774a9-265">下圖顯示 `/api/loh/84975` 在最大負載下呼叫端點的記憶體設定檔：</span><span class="sxs-lookup"><span data-stu-id="774a9-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![上圖](memory/_static/loh1.png)

<span data-ttu-id="774a9-267">下圖顯示呼叫端點的記憶體設定檔 `/api/loh/84976` ， *只配置一個位元組* ：</span><span class="sxs-lookup"><span data-stu-id="774a9-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte* :</span></span>

![上圖](memory/_static/loh2.png)

<span data-ttu-id="774a9-269">注意：此 `byte[]` 結構有額外負荷的位元組。</span><span class="sxs-lookup"><span data-stu-id="774a9-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="774a9-270">這就是為什麼84976位元組會觸發85000限制的原因。</span><span class="sxs-lookup"><span data-stu-id="774a9-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="774a9-271">比較上述兩個圖表：</span><span class="sxs-lookup"><span data-stu-id="774a9-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="774a9-272">這兩種案例的工作集很類似，大約是 450 MB。</span><span class="sxs-lookup"><span data-stu-id="774a9-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="774a9-273">LOH 要求 (84975 個位元組) 會顯示大部分的層代0回收。</span><span class="sxs-lookup"><span data-stu-id="774a9-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="774a9-274">Over LOH 要求會產生常數層代2回收。</span><span class="sxs-lookup"><span data-stu-id="774a9-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="774a9-275">層代2回收的成本很高。</span><span class="sxs-lookup"><span data-stu-id="774a9-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="774a9-276">需要更多 CPU，且輸送量會下降將近50%。</span><span class="sxs-lookup"><span data-stu-id="774a9-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="774a9-277">暫存大型物件特別有問題，因為它們會造成 gen2 Gc。</span><span class="sxs-lookup"><span data-stu-id="774a9-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="774a9-278">為了達到最大效能，應該將大型物件使用降至最低。</span><span class="sxs-lookup"><span data-stu-id="774a9-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="774a9-279">可能的話，請分割大型物件。</span><span class="sxs-lookup"><span data-stu-id="774a9-279">If possible, split up large objects.</span></span> <span data-ttu-id="774a9-280">例如，ASP.NET Core 中的 [回應](xref:performance/caching/response) 快取中介軟體會將快取專案分割成小於85000個位元組的區塊。</span><span class="sxs-lookup"><span data-stu-id="774a9-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="774a9-281">下列連結顯示在 LOH 限制下保留物件的 ASP.NET Core 方法：</span><span class="sxs-lookup"><span data-stu-id="774a9-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="774a9-282">ResponseCaching/串流/StreamUtilities .cs</span><span class="sxs-lookup"><span data-stu-id="774a9-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="774a9-283">ResponseCaching/MemoryResponseCache .cs</span><span class="sxs-lookup"><span data-stu-id="774a9-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="774a9-284">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="774a9-284">For more information, see:</span></span>

* [<span data-ttu-id="774a9-285">發現大型物件堆積</span><span class="sxs-lookup"><span data-stu-id="774a9-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="774a9-286">大型物件堆積</span><span class="sxs-lookup"><span data-stu-id="774a9-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="774a9-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="774a9-287">HttpClient</span></span>

<span data-ttu-id="774a9-288">不當使用 <xref:System.Net.Http.HttpClient> 可能會導致資源流失。</span><span class="sxs-lookup"><span data-stu-id="774a9-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="774a9-289">系統資源，例如資料庫連接、通訊端、檔案控制代碼等：</span><span class="sxs-lookup"><span data-stu-id="774a9-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="774a9-290">比記憶體更少。</span><span class="sxs-lookup"><span data-stu-id="774a9-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="774a9-291">當流失的記憶體較大時，會造成更大的問題。</span><span class="sxs-lookup"><span data-stu-id="774a9-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="774a9-292">有經驗的 .NET 開發人員知道要在實的 <xref:System.IDisposable.Dispose*> 物件上呼叫 <xref:System.IDisposable> 。</span><span class="sxs-lookup"><span data-stu-id="774a9-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="774a9-293">未處置執行的物件 `IDisposable` 通常會導致記憶體流失或洩漏的系統資源。</span><span class="sxs-lookup"><span data-stu-id="774a9-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="774a9-294">`HttpClient``IDisposable`會執行，但 **不** 應該在每次調用時處置。</span><span class="sxs-lookup"><span data-stu-id="774a9-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="774a9-295">相反地， `HttpClient` 應該重複使用。</span><span class="sxs-lookup"><span data-stu-id="774a9-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="774a9-296">下列端點會  `HttpClient` 在每個要求上建立並處置新的實例：</span><span class="sxs-lookup"><span data-stu-id="774a9-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="774a9-297">載入時，會記錄下列錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="774a9-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="774a9-298">即使 `HttpClient` 已處置實例，實際的網路連接也需要一些時間才能由作業系統釋放。</span><span class="sxs-lookup"><span data-stu-id="774a9-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="774a9-299">藉由持續建立新的連線，就會發生 _埠耗盡_ 。</span><span class="sxs-lookup"><span data-stu-id="774a9-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="774a9-300">每個用戶端連接都需要自己的用戶端埠。</span><span class="sxs-lookup"><span data-stu-id="774a9-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="774a9-301">防止埠耗盡的其中一種方式是重複使用相同的 `HttpClient` 實例：</span><span class="sxs-lookup"><span data-stu-id="774a9-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="774a9-302">`HttpClient`當應用程式停止時，就會釋放實例。</span><span class="sxs-lookup"><span data-stu-id="774a9-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="774a9-303">此範例顯示每次使用後，不應該處置每個可處置的資源。</span><span class="sxs-lookup"><span data-stu-id="774a9-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="774a9-304">請參閱下列各項，以取得更好的方式來處理實例的存留期 `HttpClient` ：</span><span class="sxs-lookup"><span data-stu-id="774a9-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="774a9-305">HttpClient 和存留期管理</span><span class="sxs-lookup"><span data-stu-id="774a9-305">HttpClient and lifetime management</span></span>](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [<span data-ttu-id="774a9-306">HTTPClient factory 的 blog</span><span class="sxs-lookup"><span data-stu-id="774a9-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="774a9-307">物件集區</span><span class="sxs-lookup"><span data-stu-id="774a9-307">Object pooling</span></span>

<span data-ttu-id="774a9-308">上一個範例示範了如何將 `HttpClient` 實例設為靜態，並由所有要求重複使用。</span><span class="sxs-lookup"><span data-stu-id="774a9-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="774a9-309">重複使用會導致資源不足。</span><span class="sxs-lookup"><span data-stu-id="774a9-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="774a9-310">物件共用：</span><span class="sxs-lookup"><span data-stu-id="774a9-310">Object pooling:</span></span>

* <span data-ttu-id="774a9-311">使用重複使用模式。</span><span class="sxs-lookup"><span data-stu-id="774a9-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="774a9-312">是針對建立成本昂貴的物件所設計。</span><span class="sxs-lookup"><span data-stu-id="774a9-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="774a9-313">集區是預先初始化的物件集合，可以線上程中保留和釋放。</span><span class="sxs-lookup"><span data-stu-id="774a9-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="774a9-314">集區可以定義配置規則，例如限制、預先定義的大小或成長率。</span><span class="sxs-lookup"><span data-stu-id="774a9-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="774a9-315">NuGet 套件 [ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) 包含可協助管理這類集區的類別。</span><span class="sxs-lookup"><span data-stu-id="774a9-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="774a9-316">下列 API 端點 `byte` 會具現化在每個要求上填入亂數字的緩衝區：</span><span class="sxs-lookup"><span data-stu-id="774a9-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="774a9-317">下列圖表顯示使用中等負載呼叫上述 API：</span><span class="sxs-lookup"><span data-stu-id="774a9-317">The following chart display calling the preceding API with moderate load:</span></span>

![上圖](memory/_static/array.png)

<span data-ttu-id="774a9-319">在上圖中，第0代回收大約會每秒執行一次。</span><span class="sxs-lookup"><span data-stu-id="774a9-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="774a9-320">上述程式碼可以藉由 `byte` 使用[ArrayPool \<T> ](xref:System.Buffers.ArrayPool`1)來共用緩衝區來優化。</span><span class="sxs-lookup"><span data-stu-id="774a9-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="774a9-321">靜態實例會跨要求重複使用。</span><span class="sxs-lookup"><span data-stu-id="774a9-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="774a9-322">這種方法有何不同之處，就是從 API 傳回集區物件。</span><span class="sxs-lookup"><span data-stu-id="774a9-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="774a9-323">這表示：</span><span class="sxs-lookup"><span data-stu-id="774a9-323">That means:</span></span>

* <span data-ttu-id="774a9-324">當您從方法返回時，物件就會移出您的控制項。</span><span class="sxs-lookup"><span data-stu-id="774a9-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="774a9-325">您無法釋放物件。</span><span class="sxs-lookup"><span data-stu-id="774a9-325">You can't release the object.</span></span>

<span data-ttu-id="774a9-326">若要設定物件的處置：</span><span class="sxs-lookup"><span data-stu-id="774a9-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="774a9-327">將集區陣列封裝在可處置的物件中。</span><span class="sxs-lookup"><span data-stu-id="774a9-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="774a9-328">使用 [HttpCoNtext RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)註冊集區物件。</span><span class="sxs-lookup"><span data-stu-id="774a9-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="774a9-329">`RegisterForDispose` 會處理 `Dispose` 目標物件上的呼叫，使其只在 HTTP 要求完成時才會釋放。</span><span class="sxs-lookup"><span data-stu-id="774a9-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="774a9-330">將相同的負載套用為非集區版本會產生下圖：</span><span class="sxs-lookup"><span data-stu-id="774a9-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![上圖](memory/_static/pooledarray.png)

<span data-ttu-id="774a9-332">主要差異在於配置的位元組數，因此產生的0代集合會減少許多。</span><span class="sxs-lookup"><span data-stu-id="774a9-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="774a9-333">其他資源</span><span class="sxs-lookup"><span data-stu-id="774a9-333">Additional resources</span></span>

* [<span data-ttu-id="774a9-334">記憶體回收</span><span class="sxs-lookup"><span data-stu-id="774a9-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="774a9-335">瞭解並行視覺化的不同 GC 模式</span><span class="sxs-lookup"><span data-stu-id="774a9-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="774a9-336">發現大型物件堆積</span><span class="sxs-lookup"><span data-stu-id="774a9-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="774a9-337">大型物件堆積</span><span class="sxs-lookup"><span data-stu-id="774a9-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)