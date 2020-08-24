---
title: ASP.NET Core 效能最佳做法
author: mjrousos
description: 提升 ASP.NET Core 應用程式效能，並避免常見效能問題的秘訣。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: 587872b269d897d7c86eb77c110a4b6432218ed3
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746555"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="ccace-103">ASP.NET Core 效能最佳做法</span><span class="sxs-lookup"><span data-stu-id="ccace-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="ccace-104">由 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="ccace-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="ccace-105">本文提供 ASP.NET Core 的效能最佳做法指導方針。</span><span class="sxs-lookup"><span data-stu-id="ccace-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="ccace-106">積極快取</span><span class="sxs-lookup"><span data-stu-id="ccace-106">Cache aggressively</span></span>

<span data-ttu-id="ccace-107">本檔的幾個部分會討論快取。</span><span class="sxs-lookup"><span data-stu-id="ccace-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="ccace-108">如需詳細資訊，請參閱<xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="ccace-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="ccace-109">瞭解熱程式碼路徑</span><span class="sxs-lookup"><span data-stu-id="ccace-109">Understand hot code paths</span></span>

<span data-ttu-id="ccace-110">在本檔中，最常使用的程式 *代碼路徑* 定義為經常呼叫的程式碼路徑，以及大部分的執行時間。</span><span class="sxs-lookup"><span data-stu-id="ccace-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="ccace-111">經常性程式碼路徑通常會限制應用程式相應放大和效能，並在本檔的幾個部分中討論。</span><span class="sxs-lookup"><span data-stu-id="ccace-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="ccace-112">避免封鎖呼叫</span><span class="sxs-lookup"><span data-stu-id="ccace-112">Avoid blocking calls</span></span>

<span data-ttu-id="ccace-113">ASP.NET Core 應用程式應設計為同時處理許多要求。</span><span class="sxs-lookup"><span data-stu-id="ccace-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="ccace-114">非同步 Api 可讓小型的執行緒集區，藉由不等待封鎖呼叫來處理上千個並行要求。</span><span class="sxs-lookup"><span data-stu-id="ccace-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="ccace-115">執行緒可能會在另一個要求上運作，而不是等待長時間執行的同步工作完成。</span><span class="sxs-lookup"><span data-stu-id="ccace-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="ccace-116">ASP.NET Core 應用程式中常見的效能問題是封鎖可能為非同步呼叫。</span><span class="sxs-lookup"><span data-stu-id="ccace-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="ccace-117">許多同步封鎖呼叫會導致 [執行緒集](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) 區耗盡和降級的回應時間。</span><span class="sxs-lookup"><span data-stu-id="ccace-117">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="ccace-118">**請勿**：</span><span class="sxs-lookup"><span data-stu-id="ccace-118">**Do not**:</span></span>

* <span data-ttu-id="ccace-119">藉由呼叫 [task. Wait](/dotnet/api/system.threading.tasks.task.wait) 或 task 來封鎖非同步執行。 [結果](/dotnet/api/system.threading.tasks.task-1.result)。</span><span class="sxs-lookup"><span data-stu-id="ccace-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="ccace-120">取得通用程式碼路徑中的鎖定。</span><span class="sxs-lookup"><span data-stu-id="ccace-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="ccace-121">當架構為平行執行程式碼時，ASP.NET Core 應用程式的效能最高。</span><span class="sxs-lookup"><span data-stu-id="ccace-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>
* <span data-ttu-id="ccace-122">呼叫工作 [。執行](/dotnet/api/system.threading.tasks.task.run) 並立即等待它。</span><span class="sxs-lookup"><span data-stu-id="ccace-122">Call [Task.Run](/dotnet/api/system.threading.tasks.task.run) and immediately await it.</span></span> <span data-ttu-id="ccace-123">ASP.NET Core 已在一般執行緒集區執行緒上執行應用程式程式碼，因此呼叫工作。執行只會導致額外的不必要執行緒集區排程。</span><span class="sxs-lookup"><span data-stu-id="ccace-123">ASP.NET Core already runs app code on normal Thread Pool threads, so calling Task.Run only results in extra unnecessary Thread Pool scheduling.</span></span> <span data-ttu-id="ccace-124">即使已排程的程式碼會封鎖執行緒，但工作卻無法防止這種情況。</span><span class="sxs-lookup"><span data-stu-id="ccace-124">Even if the scheduled code would block a thread, Task.Run does not prevent that.</span></span>

<span data-ttu-id="ccace-125">**執行**：</span><span class="sxs-lookup"><span data-stu-id="ccace-125">**Do**:</span></span>

* <span data-ttu-id="ccace-126">使 [熱程式碼路徑](#understand-hot-code-paths) 成為非同步。</span><span class="sxs-lookup"><span data-stu-id="ccace-126">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="ccace-127">如果有可用的非同步 API，則以非同步方式呼叫資料存取、i/o 和長時間執行的作業 Api。</span><span class="sxs-lookup"><span data-stu-id="ccace-127">Call data access, I/O, and long-running operations APIs asynchronously if an asynchronous API is available.</span></span> <span data-ttu-id="ccace-128">請勿 **使用** 工作 [。執行](/dotnet/api/system.threading.tasks.task.run) 以將同步 API 設為非同步。</span><span class="sxs-lookup"><span data-stu-id="ccace-128">Do **not** use [Task.Run](/dotnet/api/system.threading.tasks.task.run) to make a synchronous API asynchronous.</span></span>
* <span data-ttu-id="ccace-129">將控制器/ Razor 頁面動作設為非同步。</span><span class="sxs-lookup"><span data-stu-id="ccace-129">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="ccace-130">整個呼叫堆疊都是非同步，以便從 [async/await](/dotnet/csharp/programming-guide/concepts/async/) 模式中獲益。</span><span class="sxs-lookup"><span data-stu-id="ccace-130">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="ccace-131">您可以流量分析工具（例如 [PerfView](https://github.com/Microsoft/perfview)）來尋找經常新增到 [執行緒集](/windows/desktop/procthread/thread-pools)區的執行緒。</span><span class="sxs-lookup"><span data-stu-id="ccace-131">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="ccace-132">此 `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 事件表示新增至執行緒集區的執行緒。</span><span class="sxs-lookup"><span data-stu-id="ccace-132">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="ccace-133">傳回 IEnumerable \<T> 或 IAsyncEnumerable\<T></span><span class="sxs-lookup"><span data-stu-id="ccace-133">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="ccace-134">`IEnumerable<T>`從動作傳回會導致序列化程式進行同步的集合反復專案。</span><span class="sxs-lookup"><span data-stu-id="ccace-134">Returning `IEnumerable<T>` from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="ccace-135">結果是封鎖呼叫，以及執行緒集區耗盡的可能性。</span><span class="sxs-lookup"><span data-stu-id="ccace-135">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="ccace-136">若要避免同步列舉，請在傳回可列舉 `ToListAsync` 的之前使用。</span><span class="sxs-lookup"><span data-stu-id="ccace-136">To avoid synchronous enumeration, use `ToListAsync` before returning the enumerable.</span></span>

<span data-ttu-id="ccace-137">從 ASP.NET Core 3.0 開始， `IAsyncEnumerable<T>` 可以用來做為 `IEnumerable<T>` 非同步列舉的替代方案。</span><span class="sxs-lookup"><span data-stu-id="ccace-137">Beginning with ASP.NET Core 3.0, `IAsyncEnumerable<T>` can be used as an alternative to `IEnumerable<T>` that enumerates asynchronously.</span></span> <span data-ttu-id="ccace-138">如需詳細資訊，請參閱 [控制器動作傳回類型](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet)。</span><span class="sxs-lookup"><span data-stu-id="ccace-138">For more information, see [Controller action return types](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).</span></span>

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="ccace-139">最小化大型物件分配</span><span class="sxs-lookup"><span data-stu-id="ccace-139">Minimize large object allocations</span></span>

<span data-ttu-id="ccace-140">[.Net Core 垃圾收集](/dotnet/standard/garbage-collection/)行程會在 ASP.NET Core 應用程式中自動管理記憶體的配置和釋放。</span><span class="sxs-lookup"><span data-stu-id="ccace-140">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="ccace-141">自動垃圾收集通常表示開發人員不需要擔心釋放記憶體的方式或時間。</span><span class="sxs-lookup"><span data-stu-id="ccace-141">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="ccace-142">不過，清除未參考的物件會花費 CPU 時間，因此開發人員應該盡可能減少在經常性程式 [代碼路徑](#understand-hot-code-paths)中設定物件。</span><span class="sxs-lookup"><span data-stu-id="ccace-142">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="ccace-143">垃圾收集在 ( # A0 85 K 位元組) 的大型物件上特別耗費資源。</span><span class="sxs-lookup"><span data-stu-id="ccace-143">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="ccace-144">大型物件儲存在 [大型物件堆積](/dotnet/standard/garbage-collection/large-object-heap) 上，需要完整的 (層代 2) 垃圾收集來進行清除。</span><span class="sxs-lookup"><span data-stu-id="ccace-144">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="ccace-145">與層代0和層代1回收不同的是，層代2回收需要暫時暫停應用程式執行。</span><span class="sxs-lookup"><span data-stu-id="ccace-145">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="ccace-146">頻繁配置和取消配置大型物件可能會造成不一致的效能。</span><span class="sxs-lookup"><span data-stu-id="ccace-146">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="ccace-147">建議：</span><span class="sxs-lookup"><span data-stu-id="ccace-147">Recommendations:</span></span>

* <span data-ttu-id="ccace-148">**請考慮快** 取經常使用的大型物件。</span><span class="sxs-lookup"><span data-stu-id="ccace-148">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="ccace-149">快取大型物件可防止昂貴的配置。</span><span class="sxs-lookup"><span data-stu-id="ccace-149">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="ccace-150">使用[ArrayPool \<T> ](/dotnet/api/system.buffers.arraypool-1)來儲存大型陣列，以**進行**集區緩衝區。</span><span class="sxs-lookup"><span data-stu-id="ccace-150">**Do** pool buffers by using an [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="ccace-151">**請勿** 在經常性程式 [代碼路徑](#understand-hot-code-paths)上配置許多存留期較短的大型物件。</span><span class="sxs-lookup"><span data-stu-id="ccace-151">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="ccace-152">記憶體問題（例如上述）可以藉由在 [PerfView](https://github.com/Microsoft/perfview) 中檢查垃圾收集 (GC) 統計資料來診斷，並檢查：</span><span class="sxs-lookup"><span data-stu-id="ccace-152">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="ccace-153">垃圾收集暫停時間。</span><span class="sxs-lookup"><span data-stu-id="ccace-153">Garbage collection pause time.</span></span>
* <span data-ttu-id="ccace-154">花費在垃圾收集的處理器時間百分比。</span><span class="sxs-lookup"><span data-stu-id="ccace-154">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="ccace-155">層代0、1和2的垃圾收集數目。</span><span class="sxs-lookup"><span data-stu-id="ccace-155">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="ccace-156">如需詳細資訊，請參閱 [垃圾收集和效能](/dotnet/standard/garbage-collection/performance)。</span><span class="sxs-lookup"><span data-stu-id="ccace-156">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access-and-io"></a><span data-ttu-id="ccace-157">優化資料存取與 i/o</span><span class="sxs-lookup"><span data-stu-id="ccace-157">Optimize data access and I/O</span></span>

<span data-ttu-id="ccace-158">與資料存放區和其他遠端服務的互動通常是 ASP.NET Core 應用程式的最慢部分。</span><span class="sxs-lookup"><span data-stu-id="ccace-158">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="ccace-159">有效率地讀取和寫入資料是很重要的效能。</span><span class="sxs-lookup"><span data-stu-id="ccace-159">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="ccace-160">建議：</span><span class="sxs-lookup"><span data-stu-id="ccace-160">Recommendations:</span></span>

* <span data-ttu-id="ccace-161">**請** 以非同步方式呼叫所有資料存取 api。</span><span class="sxs-lookup"><span data-stu-id="ccace-161">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="ccace-162">**請勿** 取出超過所需的資料。</span><span class="sxs-lookup"><span data-stu-id="ccace-162">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="ccace-163">撰寫查詢，只傳回目前 HTTP 要求所需的資料。</span><span class="sxs-lookup"><span data-stu-id="ccace-163">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="ccace-164">如果可接受稍微過期的資料，**請考慮快**取從資料庫或遠端服務取出的經常存取資料。</span><span class="sxs-lookup"><span data-stu-id="ccace-164">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="ccace-165">視案例而定，使用 [MemoryCache](xref:performance/caching/memory) 或 [microsoft.web.distributedcache](xref:performance/caching/distributed)。</span><span class="sxs-lookup"><span data-stu-id="ccace-165">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="ccace-166">如需詳細資訊，請參閱<xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="ccace-166">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="ccace-167">**儘量減少** 網路往返。</span><span class="sxs-lookup"><span data-stu-id="ccace-167">**Do** minimize network round trips.</span></span> <span data-ttu-id="ccace-168">目標是在單一呼叫中取出所需的資料，而不是使用數個呼叫。</span><span class="sxs-lookup"><span data-stu-id="ccace-168">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="ccace-169">針對唯讀用途存取資料時，**請**使用 Entity Framework Core 中的[無追蹤查詢](/ef/core/querying/tracking#no-tracking-queries)。</span><span class="sxs-lookup"><span data-stu-id="ccace-169">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="ccace-170">EF Core 可以更有效率地傳回無追蹤查詢的結果。</span><span class="sxs-lookup"><span data-stu-id="ccace-170">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="ccace-171">使用、或語句來**篩選和**匯總 LINQ 查詢 (`.Where` `.Select` `.Sum` ，例如) ，以便讓資料庫進行篩選。</span><span class="sxs-lookup"><span data-stu-id="ccace-171">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="ccace-172">請**注意，** EF Core 會在用戶端上解析某些查詢運算子，而這可能會導致執行效率不佳的查詢。</span><span class="sxs-lookup"><span data-stu-id="ccace-172">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="ccace-173">如需詳細資訊，請參閱 [用戶端評估效能問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)。</span><span class="sxs-lookup"><span data-stu-id="ccace-173">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="ccace-174">**請勿** 在集合上使用投影查詢，這可能會導致執行「N + 1」 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="ccace-174">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="ccace-175">如需詳細資訊，請參閱相互 [關聯子查詢的優化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。</span><span class="sxs-lookup"><span data-stu-id="ccace-175">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="ccace-176">查看 [EF High 效能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) ，以取得可改善高規模應用程式效能的方法：</span><span class="sxs-lookup"><span data-stu-id="ccace-176">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="ccace-177">DbContext 共用</span><span class="sxs-lookup"><span data-stu-id="ccace-177">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="ccace-178">明確地編譯查詢</span><span class="sxs-lookup"><span data-stu-id="ccace-178">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="ccace-179">建議您在認可程式碼基底之前，先測量先前高效能方法的影響。</span><span class="sxs-lookup"><span data-stu-id="ccace-179">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="ccace-180">已編譯查詢的額外複雜性可能不會證明效能改進。</span><span class="sxs-lookup"><span data-stu-id="ccace-180">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="ccace-181">您可以藉由使用 [Application Insights](/azure/application-insights/app-insights-overview) 或使用程式碼剖析工具來檢查存取資料所花費的時間，來偵測查詢問題。</span><span class="sxs-lookup"><span data-stu-id="ccace-181">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="ccace-182">大部分的資料庫也會讓統計資料可用於經常執行的查詢。</span><span class="sxs-lookup"><span data-stu-id="ccace-182">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="ccace-183">使用 HttpClientFactory 集區 HTTP 連接</span><span class="sxs-lookup"><span data-stu-id="ccace-183">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="ccace-184">雖然 [HttpClient](/dotnet/api/system.net.http.httpclient) 會實作為 `IDisposable` 介面，但它是針對重複使用而設計的。</span><span class="sxs-lookup"><span data-stu-id="ccace-184">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="ccace-185">封閉式 `HttpClient` 實例會讓通訊端在 `TIME_WAIT` 一小段時間內保持開啟狀態。</span><span class="sxs-lookup"><span data-stu-id="ccace-185">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="ccace-186">如果經常使用建立和處置物件的程式碼路徑 `HttpClient` ，應用程式可能會耗盡可用的通訊端。</span><span class="sxs-lookup"><span data-stu-id="ccace-186">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="ccace-187">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 是在 ASP.NET Core 2.1 中引進，做為這個問題的解決方案。</span><span class="sxs-lookup"><span data-stu-id="ccace-187">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="ccace-188">它會處理共用的 HTTP 連線，以將效能和可靠性優化。</span><span class="sxs-lookup"><span data-stu-id="ccace-188">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="ccace-189">建議：</span><span class="sxs-lookup"><span data-stu-id="ccace-189">Recommendations:</span></span>

* <span data-ttu-id="ccace-190">**請勿** 直接建立和處置 `HttpClient` 實例。</span><span class="sxs-lookup"><span data-stu-id="ccace-190">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="ccace-191">**請** 務必使用 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 來取得 `HttpClient` 實例。</span><span class="sxs-lookup"><span data-stu-id="ccace-191">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="ccace-192">如需詳細資訊，請參閱 [使用 HttpClientFactory 來執行復原的 HTTP 要求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。</span><span class="sxs-lookup"><span data-stu-id="ccace-192">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="ccace-193">快速保持通用程式碼路徑</span><span class="sxs-lookup"><span data-stu-id="ccace-193">Keep common code paths fast</span></span>

<span data-ttu-id="ccace-194">您希望所有的程式碼都能快速進行。</span><span class="sxs-lookup"><span data-stu-id="ccace-194">You want all of your code to be fast.</span></span> <span data-ttu-id="ccace-195">經常呼叫的程式碼路徑是最重要的，可進行優化。</span><span class="sxs-lookup"><span data-stu-id="ccace-195">Frequently-called code paths are the most critical to optimize.</span></span> <span data-ttu-id="ccace-196">它們包括：</span><span class="sxs-lookup"><span data-stu-id="ccace-196">These include:</span></span>

* <span data-ttu-id="ccace-197">應用程式要求處理管線中的中介軟體元件，特別是在管線早期執行的中介軟體。</span><span class="sxs-lookup"><span data-stu-id="ccace-197">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="ccace-198">這些元件對效能會有很大的影響。</span><span class="sxs-lookup"><span data-stu-id="ccace-198">These components have a large impact on performance.</span></span>
* <span data-ttu-id="ccace-199">針對每個要求執行的程式碼，或針對每個要求多次執行的程式碼。</span><span class="sxs-lookup"><span data-stu-id="ccace-199">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="ccace-200">例如，自訂記錄、授權處理常式或暫時性服務的初始化。</span><span class="sxs-lookup"><span data-stu-id="ccace-200">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="ccace-201">建議：</span><span class="sxs-lookup"><span data-stu-id="ccace-201">Recommendations:</span></span>

* <span data-ttu-id="ccace-202">**請勿** 使用具有長時間執行之工作的自訂中介軟體元件。</span><span class="sxs-lookup"><span data-stu-id="ccace-202">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="ccace-203">**請使用效能** 分析工具（例如 [Visual Studio 診斷工具](/visualstudio/profiling/profiling-feature-tour) 或 [PerfView](https://github.com/Microsoft/perfview)) ）來識別 [熱程式碼路徑](#understand-hot-code-paths)。</span><span class="sxs-lookup"><span data-stu-id="ccace-203">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="ccace-204">在 HTTP 要求以外完成長時間執行的工作</span><span class="sxs-lookup"><span data-stu-id="ccace-204">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="ccace-205">對 ASP.NET Core 應用程式的大部分要求都可由呼叫所需服務的控制器或頁面模型來處理，並傳回 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="ccace-205">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="ccace-206">對於需要長時間執行之工作的某些要求，最好是將整個要求-回應程式設為非同步。</span><span class="sxs-lookup"><span data-stu-id="ccace-206">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="ccace-207">建議：</span><span class="sxs-lookup"><span data-stu-id="ccace-207">Recommendations:</span></span>

* <span data-ttu-id="ccace-208">**請勿** 等待長時間執行的工作完成，作為一般 HTTP 要求處理的一部分。</span><span class="sxs-lookup"><span data-stu-id="ccace-208">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="ccace-209">**請考慮使用** [Azure](/azure/azure-functions/)函式，以[背景服務](xref:fundamentals/host/hosted-services)或跨進程處理長時間執行的要求。</span><span class="sxs-lookup"><span data-stu-id="ccace-209">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="ccace-210">完成跨進程工作對於需要大量 CPU 的工作特別有用。</span><span class="sxs-lookup"><span data-stu-id="ccace-210">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="ccace-211">**請使用即時** 通訊選項（例如 [SignalR](xref:signalr/introduction) ），以非同步方式與用戶端通訊。</span><span class="sxs-lookup"><span data-stu-id="ccace-211">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="ccace-212">縮短用戶端資產</span><span class="sxs-lookup"><span data-stu-id="ccace-212">Minify client assets</span></span>

<span data-ttu-id="ccace-213">具有複雜前端的 ASP.NET Core 應用程式通常會提供許多 JavaScript、CSS 或影像檔案。</span><span class="sxs-lookup"><span data-stu-id="ccace-213">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="ccace-214">初始載入要求的效能可透過下列方式改善：</span><span class="sxs-lookup"><span data-stu-id="ccace-214">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="ccace-215">將多個檔案結合成一個的組合。</span><span class="sxs-lookup"><span data-stu-id="ccace-215">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="ccace-216">縮小，藉由移除空白和批註來減少檔案大小。</span><span class="sxs-lookup"><span data-stu-id="ccace-216">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="ccace-217">建議：</span><span class="sxs-lookup"><span data-stu-id="ccace-217">Recommendations:</span></span>

* <span data-ttu-id="ccace-218">**請** 使用 ASP.NET Core 的 [內建支援](xref:client-side/bundling-and-minification) 來組合和縮小用戶端資產。</span><span class="sxs-lookup"><span data-stu-id="ccace-218">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="ccace-219">**請考慮其他** 協力廠商工具（例如 [Webpack](https://webpack.js.org/)），以進行複雜的用戶端資產管理。</span><span class="sxs-lookup"><span data-stu-id="ccace-219">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="ccace-220">壓縮回應</span><span class="sxs-lookup"><span data-stu-id="ccace-220">Compress responses</span></span>

 <span data-ttu-id="ccace-221">減少回應的大小通常會增加應用程式的回應能力，通常會大幅增加。</span><span class="sxs-lookup"><span data-stu-id="ccace-221">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="ccace-222">減少承載大小的其中一種方式是壓縮應用程式的回應。</span><span class="sxs-lookup"><span data-stu-id="ccace-222">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="ccace-223">如需詳細資訊，請參閱 [回應壓縮](xref:performance/response-compression)。</span><span class="sxs-lookup"><span data-stu-id="ccace-223">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="ccace-224">使用最新的 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="ccace-224">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="ccace-225">ASP.NET Core 的每個新版本都包含效能改進。</span><span class="sxs-lookup"><span data-stu-id="ccace-225">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="ccace-226">.NET Core 和 ASP.NET Core 中的優化表示較新的版本通常會優於較舊的版本。</span><span class="sxs-lookup"><span data-stu-id="ccace-226">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="ccace-227">例如，.NET Core 2.1 新增了從[Span \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay)對編譯的正則運算式和受惠的支援。</span><span class="sxs-lookup"><span data-stu-id="ccace-227">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [Span\<T>](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay).</span></span> <span data-ttu-id="ccace-228">ASP.NET Core 2.2 已新增 HTTP/2 的支援。</span><span class="sxs-lookup"><span data-stu-id="ccace-228">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="ccace-229">[ASP.NET Core 3.0 增加了許多改進](xref:aspnetcore-3.0) ，可減少記憶體使用量並提升輸送量。</span><span class="sxs-lookup"><span data-stu-id="ccace-229">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="ccace-230">如果效能是優先考慮，請考慮升級至目前版本的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="ccace-230">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="ccace-231">最小化例外狀況</span><span class="sxs-lookup"><span data-stu-id="ccace-231">Minimize exceptions</span></span>

<span data-ttu-id="ccace-232">例外狀況應該很少見。</span><span class="sxs-lookup"><span data-stu-id="ccace-232">Exceptions should be rare.</span></span> <span data-ttu-id="ccace-233">擲回和攔截例外狀況的速度相對於其他程式碼流程模式。</span><span class="sxs-lookup"><span data-stu-id="ccace-233">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="ccace-234">因此，不應該使用例外狀況來控制一般程式流程。</span><span class="sxs-lookup"><span data-stu-id="ccace-234">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="ccace-235">建議：</span><span class="sxs-lookup"><span data-stu-id="ccace-235">Recommendations:</span></span>

* <span data-ttu-id="ccace-236">**請勿** 使用擲回或攔截例外狀況作為一般程式流程的方法，尤其是在經常性程式 [代碼路徑](#understand-hot-code-paths)中。</span><span class="sxs-lookup"><span data-stu-id="ccace-236">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="ccace-237">**請** 在應用程式中包含邏輯，以偵測並處理會造成例外狀況的條件。</span><span class="sxs-lookup"><span data-stu-id="ccace-237">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="ccace-238">針對不尋常或非預期的狀況，**請**擲回或攔截例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ccace-238">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="ccace-239">應用程式診斷工具（例如 Application Insights）有助於識別應用程式中可能會影響效能的常見例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ccace-239">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="ccace-240">效能和可靠性</span><span class="sxs-lookup"><span data-stu-id="ccace-240">Performance and reliability</span></span>

<span data-ttu-id="ccace-241">下列各節提供效能秘訣以及已知的可靠性問題和解決方案。</span><span class="sxs-lookup"><span data-stu-id="ccace-241">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="ccace-242">避免在 HttpRequest/HttpResponse 主體上進行同步讀取或寫入</span><span class="sxs-lookup"><span data-stu-id="ccace-242">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="ccace-243">ASP.NET Core 中的所有 i/o 都是非同步。</span><span class="sxs-lookup"><span data-stu-id="ccace-243">All I/O in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="ccace-244">伺服器會執行 `Stream` 介面，同時具有同步和非同步多載。</span><span class="sxs-lookup"><span data-stu-id="ccace-244">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="ccace-245">最好的作法是避免封鎖執行緒集區執行緒。</span><span class="sxs-lookup"><span data-stu-id="ccace-245">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="ccace-246">封鎖執行緒可能會導致執行緒集區耗盡。</span><span class="sxs-lookup"><span data-stu-id="ccace-246">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="ccace-247">請勿這樣**做：** 下列範例會使用 <xref:System.IO.StreamReader.ReadToEnd*> 。</span><span class="sxs-lookup"><span data-stu-id="ccace-247">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="ccace-248">它會封鎖目前的執行緒以等待結果。</span><span class="sxs-lookup"><span data-stu-id="ccace-248">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="ccace-249">這是透過 [非同步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)的範例。</span><span class="sxs-lookup"><span data-stu-id="ccace-249">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="ccace-250">在上述程式碼中，會以 `Get` 同步方式將整個 HTTP 要求主體讀入記憶體中。</span><span class="sxs-lookup"><span data-stu-id="ccace-250">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="ccace-251">如果用戶端緩慢上傳，則應用程式會透過非同步進行同步處理。</span><span class="sxs-lookup"><span data-stu-id="ccace-251">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="ccace-252">應用程式會透過非同步進行同步，因為 Kestrel **不支援同步** 讀取。</span><span class="sxs-lookup"><span data-stu-id="ccace-252">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="ccace-253">**請執行下列動作：** 下列範例會使用 <xref:System.IO.StreamReader.ReadToEndAsync*> ，並且不會在讀取時封鎖執行緒。</span><span class="sxs-lookup"><span data-stu-id="ccace-253">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="ccace-254">上述程式碼會以非同步方式將整個 HTTP 要求主體讀取到記憶體中。</span><span class="sxs-lookup"><span data-stu-id="ccace-254">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="ccace-255">如果要求很大，將整個 HTTP 要求本文讀入記憶體中可能會導致記憶體不足 (OOM) 狀況。</span><span class="sxs-lookup"><span data-stu-id="ccace-255">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="ccace-256">OOM 可能會導致拒絕服務。</span><span class="sxs-lookup"><span data-stu-id="ccace-256">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="ccace-257">如需詳細資訊，請參閱這份檔中的 [避免將大型要求本文或回應內文讀入記憶體](#arlb) 中。</span><span class="sxs-lookup"><span data-stu-id="ccace-257">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="ccace-258">**請執行下列動作：** 下列範例是使用非緩衝要求主體的完整非同步：</span><span class="sxs-lookup"><span data-stu-id="ccace-258">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="ccace-259">上述程式碼會以非同步方式將要求主體還原序列化為 c # 物件。</span><span class="sxs-lookup"><span data-stu-id="ccace-259">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="ccace-260">偏好 ReadFormAsync over Request. Form</span><span class="sxs-lookup"><span data-stu-id="ccace-260">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="ccace-261">使用 `HttpContext.Request.ReadFormAsync`，而不是 `HttpContext.Request.Form`。</span><span class="sxs-lookup"><span data-stu-id="ccace-261">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="ccace-262">`HttpContext.Request.Form` 只有在下列情況下才可以安全地讀取：</span><span class="sxs-lookup"><span data-stu-id="ccace-262">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="ccace-263">已透過呼叫來讀取表單 `ReadFormAsync` ，以及</span><span class="sxs-lookup"><span data-stu-id="ccace-263">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="ccace-264">正在使用讀取快取的表單值 `HttpContext.Request.Form`</span><span class="sxs-lookup"><span data-stu-id="ccace-264">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="ccace-265">請勿這樣**做：** 下列範例會使用 `HttpContext.Request.Form` 。</span><span class="sxs-lookup"><span data-stu-id="ccace-265">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="ccace-266">`HttpContext.Request.Form` 使用 [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) ，可能導致執行緒集區耗盡。</span><span class="sxs-lookup"><span data-stu-id="ccace-266">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="ccace-267">**請執行下列動作：** 下列範例會使用 `HttpContext.Request.ReadFormAsync` 來以非同步方式讀取表單主體。</span><span class="sxs-lookup"><span data-stu-id="ccace-267">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="ccace-268">避免將大型要求主體或回應內文讀入記憶體中</span><span class="sxs-lookup"><span data-stu-id="ccace-268">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="ccace-269">在 .NET 中，大於 85 KB 的每個物件配置最終都會在大型物件堆積中 ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) 。</span><span class="sxs-lookup"><span data-stu-id="ccace-269">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="ccace-270">大型物件的成本有兩種：</span><span class="sxs-lookup"><span data-stu-id="ccace-270">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="ccace-271">配置成本很高，因為必須清除新配置之大型物件的記憶體。</span><span class="sxs-lookup"><span data-stu-id="ccace-271">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="ccace-272">CLR 保證會清除所有新設定物件的記憶體。</span><span class="sxs-lookup"><span data-stu-id="ccace-272">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="ccace-273">LOH 會與堆積的其餘部分一起收集。</span><span class="sxs-lookup"><span data-stu-id="ccace-273">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="ccace-274">LOH 需要完整的 [垃圾收集](/dotnet/standard/garbage-collection/fundamentals) 或 [Gen2 集合](/dotnet/standard/garbage-collection/fundamentals#generations)。</span><span class="sxs-lookup"><span data-stu-id="ccace-274">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="ccace-275">此 [blog 文章](https://adamsitnik.com/Array-Pool/#the-problem) 簡潔地描述問題：</span><span class="sxs-lookup"><span data-stu-id="ccace-275">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="ccace-276">配置大型物件時，會將它標示為 Gen 2 物件。</span><span class="sxs-lookup"><span data-stu-id="ccace-276">When a large object is allocated, it's marked as Gen 2 object.</span></span> <span data-ttu-id="ccace-277">不適用於小型物件的 Gen 0。</span><span class="sxs-lookup"><span data-stu-id="ccace-277">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="ccace-278">結果是，如果您在 LOH 中耗盡記憶體，GC 就會清除整個受控堆積，而不只是 LOH。</span><span class="sxs-lookup"><span data-stu-id="ccace-278">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="ccace-279">因此，它會清除 Gen 0、Gen 1 和 Gen 2 （包括 LOH）。</span><span class="sxs-lookup"><span data-stu-id="ccace-279">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="ccace-280">這稱為完整垃圾收集，而且是最耗時的垃圾收集。</span><span class="sxs-lookup"><span data-stu-id="ccace-280">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="ccace-281">對於許多應用程式來說，它可以是可接受的。</span><span class="sxs-lookup"><span data-stu-id="ccace-281">For many applications, it can be acceptable.</span></span> <span data-ttu-id="ccace-282">但絕對不能使用高效能的 web 伺服器，因為需要海量儲存體緩衝區來處理平均 web 要求 (從通訊端讀取、解壓縮、解碼 JSON & 更多) 。</span><span class="sxs-lookup"><span data-stu-id="ccace-282">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="ccace-283">輕鬆自在管理將大型要求或回應主體儲存成單一 `byte[]` 或 `string` ：</span><span class="sxs-lookup"><span data-stu-id="ccace-283">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="ccace-284">可能會導致 LOH 中的空間快速用盡。</span><span class="sxs-lookup"><span data-stu-id="ccace-284">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="ccace-285">可能會導致應用程式的效能問題，因為執行的是完整 Gc。</span><span class="sxs-lookup"><span data-stu-id="ccace-285">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="ccace-286">使用同步資料處理 API</span><span class="sxs-lookup"><span data-stu-id="ccace-286">Working with a synchronous data processing API</span></span>

<span data-ttu-id="ccace-287">使用僅支援同步讀取和寫入的序列化程式/還原序列化程式時 (例如，  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)) ：</span><span class="sxs-lookup"><span data-stu-id="ccace-287">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="ccace-288">在將資料傳入序列化程式/還原序列化程式之前，以非同步方式將資料緩衝到記憶體中。</span><span class="sxs-lookup"><span data-stu-id="ccace-288">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="ccace-289">如果要求很大，可能會導致記憶體不足 (OOM) 狀況。</span><span class="sxs-lookup"><span data-stu-id="ccace-289">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="ccace-290">OOM 可能會導致拒絕服務。</span><span class="sxs-lookup"><span data-stu-id="ccace-290">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="ccace-291">如需詳細資訊，請參閱這份檔中的 [避免將大型要求本文或回應內文讀入記憶體](#arlb) 中。</span><span class="sxs-lookup"><span data-stu-id="ccace-291">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="ccace-292">ASP.NET Core 3.0 預設會使用 <xref:System.Text.Json> JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="ccace-292">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="ccace-293"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="ccace-293"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="ccace-294">以非同步方式讀取和寫入 JSON。</span><span class="sxs-lookup"><span data-stu-id="ccace-294">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="ccace-295">已針對 UTF-8 文字進行優化。</span><span class="sxs-lookup"><span data-stu-id="ccace-295">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="ccace-296">通常比更高 `Newtonsoft.Json` 的效能。</span><span class="sxs-lookup"><span data-stu-id="ccace-296">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="ccace-297">請勿將 >iHTTPcoNtextaccessor 儲存在欄位中</span><span class="sxs-lookup"><span data-stu-id="ccace-297">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="ccace-298">[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) `HttpContext` 從要求執行緒存取時，>iHTTPcoNtextaccessor 會傳回使用中要求的。</span><span class="sxs-lookup"><span data-stu-id="ccace-298">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="ccace-299">`IHttpContextAccessor.HttpContext`**不**應儲存在欄位或變數中。</span><span class="sxs-lookup"><span data-stu-id="ccace-299">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="ccace-300">請勿這樣**做：** 下列範例會將儲存 `HttpContext` 在欄位中，然後稍後再嘗試使用它。</span><span class="sxs-lookup"><span data-stu-id="ccace-300">**Do not do this:** The following example stores the `HttpContext` in a field and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="ccace-301">上述程式碼通常會在函式中捕捉 null 或不正確 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="ccace-301">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="ccace-302">**請執行下列動作：** 下列範例：</span><span class="sxs-lookup"><span data-stu-id="ccace-302">**Do this:** The following example:</span></span>

* <span data-ttu-id="ccace-303">將儲存 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 在欄位中。</span><span class="sxs-lookup"><span data-stu-id="ccace-303">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="ccace-304">`HttpContext`在正確的時間使用欄位，並檢查 `null` 。</span><span class="sxs-lookup"><span data-stu-id="ccace-304">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="ccace-305">不要從多個執行緒存取 HttpCoNtext</span><span class="sxs-lookup"><span data-stu-id="ccace-305">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="ccace-306">`HttpContext` 不 *是安全* 執行緒。</span><span class="sxs-lookup"><span data-stu-id="ccace-306">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="ccace-307">`HttpContext`以平行方式從多個執行緒存取可能會導致未定義的行為，例如當機、損毀和資料損毀。</span><span class="sxs-lookup"><span data-stu-id="ccace-307">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="ccace-308">請勿這樣**做：** 下列範例會產生三個平行要求，並在傳出 HTTP 要求之前和之後記錄傳入的要求路徑。</span><span class="sxs-lookup"><span data-stu-id="ccace-308">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="ccace-309">要求路徑可從多個執行緒存取，可能會以平行方式進行存取。</span><span class="sxs-lookup"><span data-stu-id="ccace-309">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="ccace-310">**請執行下列動作：** 下列範例會先複製傳入要求中的所有資料，再進行三個平行要求。</span><span class="sxs-lookup"><span data-stu-id="ccace-310">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="ccace-311">要求完成之後，請勿使用 HttpCoNtext</span><span class="sxs-lookup"><span data-stu-id="ccace-311">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="ccace-312">`HttpContext` 只有在 ASP.NET Core 管線中有使用中的 HTTP 要求時，才有效。</span><span class="sxs-lookup"><span data-stu-id="ccace-312">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="ccace-313">整個 ASP.NET Core 管線是執行每個要求的非同步委派鏈。</span><span class="sxs-lookup"><span data-stu-id="ccace-313">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="ccace-314">當 `Task` 此鏈傳回的完成時， `HttpContext` 會回收。</span><span class="sxs-lookup"><span data-stu-id="ccace-314">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="ccace-315">請勿這樣**做：** 下列範例會使用， `async void` 以在達到第一個要求時完成 HTTP 要求 `await` ：</span><span class="sxs-lookup"><span data-stu-id="ccace-315">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="ccace-316">在 ASP.NET Core 應用程式中，這 **一律** 是不良的做法。</span><span class="sxs-lookup"><span data-stu-id="ccace-316">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="ccace-317">在 `HttpResponse` HTTP 要求完成後存取。</span><span class="sxs-lookup"><span data-stu-id="ccace-317">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="ccace-318">損毀進程。</span><span class="sxs-lookup"><span data-stu-id="ccace-318">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="ccace-319">**請執行下列動作：** 下列範例會將傳回 `Task` 至架構，所以在動作完成之前，HTTP 要求都不會完成。</span><span class="sxs-lookup"><span data-stu-id="ccace-319">**Do this:** The following example returns a `Task` to the framework, so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="ccace-320">請勿在背景執行緒中捕捉 HttpCoNtext</span><span class="sxs-lookup"><span data-stu-id="ccace-320">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="ccace-321">請勿這樣**做：** 下列範例顯示的是 `HttpContext` 從屬性中捕捉的關閉 `Controller` 。</span><span class="sxs-lookup"><span data-stu-id="ccace-321">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="ccace-322">這是不正確的作法，因為工作專案可以：</span><span class="sxs-lookup"><span data-stu-id="ccace-322">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="ccace-323">在要求範圍之外執行。</span><span class="sxs-lookup"><span data-stu-id="ccace-323">Run outside of the request scope.</span></span>
* <span data-ttu-id="ccace-324">嘗試讀取錯誤 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="ccace-324">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="ccace-325">**請執行下列動作：** 下列範例：</span><span class="sxs-lookup"><span data-stu-id="ccace-325">**Do this:** The following example:</span></span>

* <span data-ttu-id="ccace-326">在要求期間複製背景工作所需的資料。</span><span class="sxs-lookup"><span data-stu-id="ccace-326">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="ccace-327">不會參考控制器中的任何事物。</span><span class="sxs-lookup"><span data-stu-id="ccace-327">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

<span data-ttu-id="ccace-328">背景工作應該實作為託管服務。</span><span class="sxs-lookup"><span data-stu-id="ccace-328">Background tasks should be implemented as hosted services.</span></span> <span data-ttu-id="ccace-329">如需詳細資訊，請參閱[搭配託管服務的背景工作](xref:fundamentals/host/hosted-services)。</span><span class="sxs-lookup"><span data-stu-id="ccace-329">For more information, see [Background tasks with hosted services](xref:fundamentals/host/hosted-services).</span></span>

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="ccace-330">不要在背景執行緒上捕捉插入控制器的服務</span><span class="sxs-lookup"><span data-stu-id="ccace-330">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="ccace-331">請勿這樣**做：** 下列範例顯示的是 `DbContext` 從 action 參數中捕捉的關閉 `Controller` 。</span><span class="sxs-lookup"><span data-stu-id="ccace-331">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="ccace-332">這是不正確的作法。</span><span class="sxs-lookup"><span data-stu-id="ccace-332">This is a bad practice.</span></span>  <span data-ttu-id="ccace-333">工作專案可以在要求範圍之外執行。</span><span class="sxs-lookup"><span data-stu-id="ccace-333">The work item could run outside of the request scope.</span></span> <span data-ttu-id="ccace-334">的 `ContosoDbContext` 範圍是要求，會產生 `ObjectDisposedException` 。</span><span class="sxs-lookup"><span data-stu-id="ccace-334">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="ccace-335">**請執行下列動作：** 下列範例：</span><span class="sxs-lookup"><span data-stu-id="ccace-335">**Do this:** The following example:</span></span>

* <span data-ttu-id="ccace-336">插入，以便在 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 背景工作專案中建立範圍。</span><span class="sxs-lookup"><span data-stu-id="ccace-336">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="ccace-337">`IServiceScopeFactory` 是 singleton。</span><span class="sxs-lookup"><span data-stu-id="ccace-337">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="ccace-338">在背景執行緒中建立新的相依性插入範圍。</span><span class="sxs-lookup"><span data-stu-id="ccace-338">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="ccace-339">不會參考控制器中的任何事物。</span><span class="sxs-lookup"><span data-stu-id="ccace-339">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="ccace-340">不會 `ContosoDbContext` 從傳入要求中捕捉。</span><span class="sxs-lookup"><span data-stu-id="ccace-340">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="ccace-341">以下反白顯示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="ccace-341">The following highlighted code:</span></span>

* <span data-ttu-id="ccace-342">在背景作業的存留期內建立範圍，並從中解析服務。</span><span class="sxs-lookup"><span data-stu-id="ccace-342">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="ccace-343">`ContosoDbContext`從正確的範圍使用。</span><span class="sxs-lookup"><span data-stu-id="ccace-343">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="ccace-344">在回應主體開始之後，請勿修改狀態碼或標頭</span><span class="sxs-lookup"><span data-stu-id="ccace-344">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="ccace-345">ASP.NET Core 不會緩衝 HTTP 回應主體。</span><span class="sxs-lookup"><span data-stu-id="ccace-345">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="ccace-346">第一次寫入回應時：</span><span class="sxs-lookup"><span data-stu-id="ccace-346">The first time the response is written:</span></span>

* <span data-ttu-id="ccace-347">標頭會連同主體的區區塊轉送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="ccace-347">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="ccace-348">您不再可以變更回應標頭。</span><span class="sxs-lookup"><span data-stu-id="ccace-348">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="ccace-349">請勿這樣**做：** 下列程式碼會嘗試在回應已開始之後新增回應標頭：</span><span class="sxs-lookup"><span data-stu-id="ccace-349">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="ccace-350">在上述程式碼中， `context.Response.Headers["test"] = "test value";` 如果 `next()` 已寫入回應，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ccace-350">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="ccace-351">**請執行下列動作：** 下列範例會在修改標頭之前，檢查 HTTP 回應是否已啟動。</span><span class="sxs-lookup"><span data-stu-id="ccace-351">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="ccace-352">**請執行下列動作：** 下列範例會在 `HttpResponse.OnStarting` 回應標頭排清至用戶端之前，使用來設定標頭。</span><span class="sxs-lookup"><span data-stu-id="ccace-352">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="ccace-353">檢查回應是否未啟動，允許註冊將在寫入回應標頭之前叫用的回呼。</span><span class="sxs-lookup"><span data-stu-id="ccace-353">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="ccace-354">檢查回應是否未啟動：</span><span class="sxs-lookup"><span data-stu-id="ccace-354">Checking if the response has not started:</span></span>

* <span data-ttu-id="ccace-355">提供即時附加或覆寫標頭的功能。</span><span class="sxs-lookup"><span data-stu-id="ccace-355">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="ccace-356">不需要知道管線中的下一個中介軟體。</span><span class="sxs-lookup"><span data-stu-id="ccace-356">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="ccace-357">如果您已開始寫入至回應主體，請不要呼叫下一個 ( # A1</span><span class="sxs-lookup"><span data-stu-id="ccace-357">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="ccace-358">只有當元件可以處理和操作回應時，才會被呼叫。</span><span class="sxs-lookup"><span data-stu-id="ccace-358">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>

## <a name="use-in-process-hosting-with-iis"></a><span data-ttu-id="ccace-359">使用同進程裝載與 IIS</span><span class="sxs-lookup"><span data-stu-id="ccace-359">Use In-process hosting with IIS</span></span>

<span data-ttu-id="ccace-360">使用同處理序裝載，ASP.NET Core 應用程式會在與其 IIS 工作者處理序相同的處理序中執行。</span><span class="sxs-lookup"><span data-stu-id="ccace-360">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="ccace-361">內含式裝載可提供跨進程裝載的改善效能，因為要求不會透過回送介面卡進行 proxy 處理。</span><span class="sxs-lookup"><span data-stu-id="ccace-361">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="ccace-362">回送介面卡是一種網路介面，可將連出的網路流量傳回相同的電腦。</span><span class="sxs-lookup"><span data-stu-id="ccace-362">The loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="ccace-363">IIS 透過 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 來執行處理程序管理。</span><span class="sxs-lookup"><span data-stu-id="ccace-363">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ccace-364">專案預設為 ASP.NET Core 3.0 和更新版本中的同進程裝載模型。</span><span class="sxs-lookup"><span data-stu-id="ccace-364">Projects default to the in-process hosting model in ASP.NET Core 3.0 and later.</span></span>

<span data-ttu-id="ccace-365">如需詳細資訊，請參閱 [使用 IIS 在 Windows 上裝載 ASP.NET Core](xref:host-and-deploy/iis/index)</span><span class="sxs-lookup"><span data-stu-id="ccace-365">For more information, see [Host ASP.NET Core on Windows with IIS](xref:host-and-deploy/iis/index)</span></span>
