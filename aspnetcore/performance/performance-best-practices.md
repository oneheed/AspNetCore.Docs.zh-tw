---
title: ASP.NET Core 效能最佳做法
author: mjrousos
description: 提升 ASP.NET Core 應用程式效能，並避免常見效能問題的秘訣。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: a3fc398569fafefc0b4634e80433a5d4e0e1b4ff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060998"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core 效能最佳做法

由 [Mike Rousos](https://github.com/mjrousos)

本文提供 ASP.NET Core 的效能最佳做法指導方針。

## <a name="cache-aggressively"></a>積極快取

本檔的幾個部分會討論快取。 如需詳細資訊，請參閱<xref:performance/caching/response>。

## <a name="understand-hot-code-paths"></a>瞭解熱程式碼路徑

在本檔中，最常使用的程式 *代碼路徑* 定義為經常呼叫的程式碼路徑，以及大部分的執行時間。 經常性程式碼路徑通常會限制應用程式相應放大和效能，並在本檔的幾個部分中討論。

## <a name="avoid-blocking-calls"></a>避免封鎖呼叫

ASP.NET Core 應用程式應設計為同時處理許多要求。 非同步 Api 可讓小型的執行緒集區，藉由不等待封鎖呼叫來處理上千個並行要求。 執行緒可能會在另一個要求上運作，而不是等待長時間執行的同步工作完成。

ASP.NET Core 應用程式中常見的效能問題是封鎖可能為非同步呼叫。 許多同步封鎖呼叫會導致 [執行緒集](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) 區耗盡和降級的回應時間。

**請勿** ：

* 藉由呼叫 [task. Wait](/dotnet/api/system.threading.tasks.task.wait) 或 task 來封鎖非同步執行。 [結果](/dotnet/api/system.threading.tasks.task-1.result)。
* 取得通用程式碼路徑中的鎖定。 當架構為平行執行程式碼時，ASP.NET Core 應用程式的效能最高。
* 呼叫工作 [。執行](/dotnet/api/system.threading.tasks.task.run) 並立即等待它。 ASP.NET Core 已在一般執行緒集區執行緒上執行應用程式程式碼，因此呼叫工作。執行只會導致額外的不必要執行緒集區排程。 即使已排程的程式碼會封鎖執行緒，但工作卻無法防止這種情況。

**執行** ：

* 使 [熱程式碼路徑](#understand-hot-code-paths) 成為非同步。
* 如果有可用的非同步 API，則以非同步方式呼叫資料存取、i/o 和長時間執行的作業 Api。 請勿 **使用** 工作 [。執行](/dotnet/api/system.threading.tasks.task.run) 以將同步 API 設為非同步。
* 將控制器/ Razor 頁面動作設為非同步。 整個呼叫堆疊都是非同步，以便從 [async/await](/dotnet/csharp/programming-guide/concepts/async/) 模式中獲益。

您可以流量分析工具（例如 [PerfView](https://github.com/Microsoft/perfview)）來尋找經常新增到 [執行緒集](/windows/desktop/procthread/thread-pools)區的執行緒。 此 `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 事件表示新增至執行緒集區的執行緒。 <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a>傳回 IEnumerable \<T> 或 IAsyncEnumerable\<T>

`IEnumerable<T>`從動作傳回會導致序列化程式進行同步的集合反復專案。 結果是封鎖呼叫，以及執行緒集區耗盡的可能性。 若要避免同步列舉，請在傳回可列舉 `ToListAsync` 的之前使用。

從 ASP.NET Core 3.0 開始， `IAsyncEnumerable<T>` 可以用來做為 `IEnumerable<T>` 非同步列舉的替代方案。 如需詳細資訊，請參閱 [控制器動作傳回類型](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet)。

## <a name="minimize-large-object-allocations"></a>最小化大型物件分配

[.Net Core 垃圾收集](/dotnet/standard/garbage-collection/)行程會在 ASP.NET Core 應用程式中自動管理記憶體的配置和釋放。 自動垃圾收集通常表示開發人員不需要擔心釋放記憶體的方式或時間。 不過，清除未參考的物件會花費 CPU 時間，因此開發人員應該盡可能減少在經常性程式 [代碼路徑](#understand-hot-code-paths)中設定物件。 垃圾收集在 ( # A0 85 K 位元組) 的大型物件上特別耗費資源。 大型物件儲存在 [大型物件堆積](/dotnet/standard/garbage-collection/large-object-heap) 上，需要完整的 (層代 2) 垃圾收集來進行清除。 與層代0和層代1回收不同的是，層代2回收需要暫時暫停應用程式執行。 頻繁配置和取消配置大型物件可能會造成不一致的效能。

建議：

* **請考慮快** 取經常使用的大型物件。 快取大型物件可防止昂貴的配置。
* 使用 [ArrayPool \<T>](/dotnet/api/system.buffers.arraypool-1)來儲存大型陣列，以 **進行** 集區緩衝區。
* **請勿** 在經常性程式 [代碼路徑](#understand-hot-code-paths)上配置許多存留期較短的大型物件。

記憶體問題（例如上述）可以藉由在 [PerfView](https://github.com/Microsoft/perfview) 中檢查垃圾收集 (GC) 統計資料來診斷，並檢查：

* 垃圾收集暫停時間。
* 花費在垃圾收集的處理器時間百分比。
* 層代0、1和2的垃圾收集數目。

如需詳細資訊，請參閱 [垃圾收集和效能](/dotnet/standard/garbage-collection/performance)。

## <a name="optimize-data-access-and-io"></a>優化資料存取與 i/o

與資料存放區和其他遠端服務的互動通常是 ASP.NET Core 應用程式的最慢部分。 有效率地讀取和寫入資料是很重要的效能。

建議：

* **請** 以非同步方式呼叫所有資料存取 api。
* **請勿** 取出超過所需的資料。 撰寫查詢，只傳回目前 HTTP 要求所需的資料。
* 如果可接受稍微過期的資料， **請考慮快** 取從資料庫或遠端服務取出的經常存取資料。 視案例而定，使用 [MemoryCache](xref:performance/caching/memory) 或 [microsoft.web.distributedcache](xref:performance/caching/distributed)。 如需詳細資訊，請參閱<xref:performance/caching/response>。
* **儘量減少** 網路往返。 目標是在單一呼叫中取出所需的資料，而不是使用數個呼叫。
* 針對唯讀用途存取資料時， **請** 使用 Entity Framework Core 中的 [無追蹤查詢](/ef/core/querying/tracking#no-tracking-queries)。 EF Core 可以更有效率地傳回無追蹤查詢的結果。
* 使用、或語句來 **篩選和** 匯總 LINQ 查詢 (`.Where` `.Select` `.Sum` ，例如) ，以便讓資料庫進行篩選。
* 請 **注意，** EF Core 會在用戶端上解析某些查詢運算子，而這可能會導致執行效率不佳的查詢。 如需詳細資訊，請參閱 [用戶端評估效能問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)。
* **請勿** 在集合上使用投影查詢，這可能會導致執行「N + 1」 SQL 查詢。 如需詳細資訊，請參閱相互 [關聯子查詢的優化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。

查看 [EF High 效能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) ，以取得可改善高規模應用程式效能的方法：

* [DbContext 共用](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [明確地編譯查詢](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

建議您在認可程式碼基底之前，先測量先前高效能方法的影響。 已編譯查詢的額外複雜性可能不會證明效能改進。

您可以藉由使用 [Application Insights](/azure/application-insights/app-insights-overview) 或使用程式碼剖析工具來檢查存取資料所花費的時間，來偵測查詢問題。 大部分的資料庫也會讓統計資料可用於經常執行的查詢。

## <a name="pool-http-connections-with-httpclientfactory"></a>使用 HttpClientFactory 集區 HTTP 連接

雖然 [HttpClient](/dotnet/api/system.net.http.httpclient) 會實作為 `IDisposable` 介面，但它是針對重複使用而設計的。 封閉式 `HttpClient` 實例會讓通訊端在 `TIME_WAIT` 一小段時間內保持開啟狀態。 如果經常使用建立和處置物件的程式碼路徑 `HttpClient` ，應用程式可能會耗盡可用的通訊端。 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 是在 ASP.NET Core 2.1 中引進，做為這個問題的解決方案。 它會處理共用的 HTTP 連線，以將效能和可靠性優化。

建議：

* **請勿** 直接建立和處置 `HttpClient` 實例。
* **請** 務必使用 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 來取得 `HttpClient` 實例。 如需詳細資訊，請參閱 [使用 HttpClientFactory 來執行復原的 HTTP 要求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。

## <a name="keep-common-code-paths-fast"></a>快速保持通用程式碼路徑

您希望所有的程式碼都能快速進行。 經常呼叫的程式碼路徑是最重要的，可進行優化。 它們包括：

* 應用程式要求處理管線中的中介軟體元件，特別是在管線早期執行的中介軟體。 這些元件對效能會有很大的影響。
* 針對每個要求執行的程式碼，或針對每個要求多次執行的程式碼。 例如，自訂記錄、授權處理常式或暫時性服務的初始化。

建議：

* **請勿** 使用具有長時間執行之工作的自訂中介軟體元件。
* **請使用效能** 分析工具（例如 [Visual Studio 診斷工具](/visualstudio/profiling/profiling-feature-tour) 或 [PerfView](https://github.com/Microsoft/perfview)) ）來識別 [熱程式碼路徑](#understand-hot-code-paths)。

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>在 HTTP 要求以外完成長時間執行的工作

對 ASP.NET Core 應用程式的大部分要求都可由呼叫所需服務的控制器或頁面模型來處理，並傳回 HTTP 回應。 對於需要長時間執行之工作的某些要求，最好是將整個要求-回應程式設為非同步。

建議：

* **請勿** 等待長時間執行的工作完成，作為一般 HTTP 要求處理的一部分。
* **請考慮使用** [Azure](/azure/azure-functions/)函式，以 [背景服務](xref:fundamentals/host/hosted-services)或跨進程處理長時間執行的要求。 完成跨進程工作對於需要大量 CPU 的工作特別有用。
* **請使用即時** 通訊選項（例如 [SignalR](xref:signalr/introduction) ），以非同步方式與用戶端通訊。

## <a name="minify-client-assets"></a>縮短用戶端資產

具有複雜前端的 ASP.NET Core 應用程式通常會提供許多 JavaScript、CSS 或影像檔案。 初始載入要求的效能可透過下列方式改善：

* 將多個檔案結合成一個的組合。
* 縮小，藉由移除空白和批註來減少檔案大小。

建議：

* **請** 使用 ASP.NET Core 的 [內建支援](xref:client-side/bundling-and-minification) 來組合和縮小用戶端資產。
* **請考慮其他** 協力廠商工具（例如 [Webpack](https://webpack.js.org/)），以進行複雜的用戶端資產管理。

## <a name="compress-responses"></a>壓縮回應

 減少回應的大小通常會增加應用程式的回應能力，通常會大幅增加。 減少承載大小的其中一種方式是壓縮應用程式的回應。 如需詳細資訊，請參閱 [回應壓縮](xref:performance/response-compression)。

## <a name="use-the-latest-aspnet-core-release"></a>使用最新的 ASP.NET Core 版本

ASP.NET Core 的每個新版本都包含效能改進。 .NET Core 和 ASP.NET Core 中的優化表示較新的版本通常會優於較舊的版本。 例如，.NET Core 2.1 新增了從[Span \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay)對編譯的正則運算式和受惠的支援。 ASP.NET Core 2.2 已新增 HTTP/2 的支援。 [ASP.NET Core 3.0 增加了許多改進](xref:aspnetcore-3.0) ，可減少記憶體使用量並提升輸送量。 如果效能是優先考慮，請考慮升級至目前版本的 ASP.NET Core。

## <a name="minimize-exceptions"></a>最小化例外狀況

例外狀況應該很少見。 擲回和攔截例外狀況的速度相對於其他程式碼流程模式。 因此，不應該使用例外狀況來控制一般程式流程。

建議：

* **請勿** 使用擲回或攔截例外狀況作為一般程式流程的方法，尤其是在經常性程式 [代碼路徑](#understand-hot-code-paths)中。
* **請** 在應用程式中包含邏輯，以偵測並處理會造成例外狀況的條件。
* 針對不尋常或非預期的狀況， **請** 擲回或攔截例外狀況。

應用程式診斷工具（例如 Application Insights）有助於識別應用程式中可能會影響效能的常見例外狀況。

## <a name="performance-and-reliability"></a>效能和可靠性

下列各節提供效能秘訣以及已知的可靠性問題和解決方案。

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>避免在 HttpRequest/HttpResponse 主體上進行同步讀取或寫入

ASP.NET Core 中的所有 i/o 都是非同步。 伺服器會執行 `Stream` 介面，同時具有同步和非同步多載。 最好的作法是避免封鎖執行緒集區執行緒。 封鎖執行緒可能會導致執行緒集區耗盡。

請勿這樣 **做：** 下列範例會使用 <xref:System.IO.StreamReader.ReadToEnd*> 。 它會封鎖目前的執行緒以等待結果。 這是透過 [非同步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)的範例。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

在上述程式碼中，會以 `Get` 同步方式將整個 HTTP 要求主體讀入記憶體中。 如果用戶端緩慢上傳，則應用程式會透過非同步進行同步處理。 應用程式會透過非同步進行同步，因為 Kestrel **不支援同步** 讀取。

**請執行下列動作：** 下列範例會使用 <xref:System.IO.StreamReader.ReadToEndAsync*> ，並且不會在讀取時封鎖執行緒。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

上述程式碼會以非同步方式將整個 HTTP 要求主體讀取到記憶體中。

> [!WARNING]
> 如果要求很大，將整個 HTTP 要求本文讀入記憶體中可能會導致記憶體不足 (OOM) 狀況。 OOM 可能會導致拒絕服務。  如需詳細資訊，請參閱這份檔中的 [避免將大型要求本文或回應內文讀入記憶體](#arlb) 中。

**請執行下列動作：** 下列範例是使用非緩衝要求主體的完整非同步：

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

上述程式碼會以非同步方式將要求主體還原序列化為 c # 物件。

## <a name="prefer-readformasync-over-requestform"></a>偏好 ReadFormAsync over Request. Form

使用 `HttpContext.Request.ReadFormAsync`，而不是 `HttpContext.Request.Form`。
`HttpContext.Request.Form` 只有在下列情況下才可以安全地讀取：

* 已透過呼叫來讀取表單 `ReadFormAsync` ，以及
* 正在使用讀取快取的表單值 `HttpContext.Request.Form`

請勿這樣 **做：** 下列範例會使用 `HttpContext.Request.Form` 。  `HttpContext.Request.Form` 使用 [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) ，可能導致執行緒集區耗盡。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**請執行下列動作：** 下列範例會使用 `HttpContext.Request.ReadFormAsync` 來以非同步方式讀取表單主體。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>避免將大型要求主體或回應內文讀入記憶體中

在 .NET 中，大於 85 KB 的每個物件配置最終都會在大型物件堆積中 ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) 。 大型物件的成本有兩種：

* 配置成本很高，因為必須清除新配置之大型物件的記憶體。 CLR 保證會清除所有新設定物件的記憶體。
* LOH 會與堆積的其餘部分一起收集。 LOH 需要完整的 [垃圾收集](/dotnet/standard/garbage-collection/fundamentals) 或 [Gen2 集合](/dotnet/standard/garbage-collection/fundamentals#generations)。

此 [blog 文章](https://adamsitnik.com/Array-Pool/#the-problem) 簡潔地描述問題：

> 配置大型物件時，會將它標示為 Gen 2 物件。 不適用於小型物件的 Gen 0。 結果是，如果您在 LOH 中耗盡記憶體，GC 就會清除整個受控堆積，而不只是 LOH。 因此，它會清除 Gen 0、Gen 1 和 Gen 2 （包括 LOH）。 這稱為完整垃圾收集，而且是最耗時的垃圾收集。 對於許多應用程式來說，它可以是可接受的。 但絕對不能使用高效能的 web 伺服器，因為需要海量儲存體緩衝區來處理平均 web 要求 (從通訊端讀取、解壓縮、解碼 JSON & 更多) 。

輕鬆自在管理將大型要求或回應主體儲存成單一 `byte[]` 或 `string` ：

* 可能會導致 LOH 中的空間快速用盡。
* 可能會導致應用程式的效能問題，因為執行的是完整 Gc。

## <a name="working-with-a-synchronous-data-processing-api"></a>使用同步資料處理 API

使用僅支援同步讀取和寫入的序列化程式/還原序列化程式時 (例如，  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)) ：

* 在將資料傳入序列化程式/還原序列化程式之前，以非同步方式將資料緩衝到記憶體中。

> [!WARNING]
> 如果要求很大，可能會導致記憶體不足 (OOM) 狀況。 OOM 可能會導致拒絕服務。  如需詳細資訊，請參閱這份檔中的 [避免將大型要求本文或回應內文讀入記憶體](#arlb) 中。

ASP.NET Core 3.0 預設會使用 <xref:System.Text.Json> JSON 序列化。 <xref:System.Text.Json>:

* 以非同步方式讀取和寫入 JSON。
* 已針對 UTF-8 文字進行優化。
* 通常比更高 `Newtonsoft.Json` 的效能。

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>請勿將 >iHTTPcoNtextaccessor 儲存在欄位中

[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) `HttpContext` 從要求執行緒存取時，>iHTTPcoNtextaccessor 會傳回使用中要求的。 `IHttpContextAccessor.HttpContext`**不** 應儲存在欄位或變數中。

請勿這樣 **做：** 下列範例會將儲存 `HttpContext` 在欄位中，然後稍後再嘗試使用它。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

上述程式碼通常會在函式中捕捉 null 或不正確 `HttpContext` 。

**請執行下列動作：** 下列範例：

* 將儲存 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 在欄位中。
* `HttpContext`在正確的時間使用欄位，並檢查 `null` 。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>不要從多個執行緒存取 HttpCoNtext

`HttpContext` 不 *是安全* 執行緒。 `HttpContext`以平行方式從多個執行緒存取可能會導致未定義的行為，例如當機、損毀和資料損毀。

請勿這樣 **做：** 下列範例會產生三個平行要求，並在傳出 HTTP 要求之前和之後記錄傳入的要求路徑。 要求路徑可從多個執行緒存取，可能會以平行方式進行存取。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**請執行下列動作：** 下列範例會先複製傳入要求中的所有資料，再進行三個平行要求。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>要求完成之後，請勿使用 HttpCoNtext

`HttpContext` 只有在 ASP.NET Core 管線中有使用中的 HTTP 要求時，才有效。 整個 ASP.NET Core 管線是執行每個要求的非同步委派鏈。 當 `Task` 此鏈傳回的完成時， `HttpContext` 會回收。

請勿這樣 **做：** 下列範例會使用， `async void` 以在達到第一個要求時完成 HTTP 要求 `await` ：

* 在 ASP.NET Core 應用程式中，這 **一律** 是不良的做法。
* 在 `HttpResponse` HTTP 要求完成後存取。
* 損毀進程。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**請執行下列動作：** 下列範例會將傳回 `Task` 至架構，所以在動作完成之前，HTTP 要求都不會完成。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>請勿在背景執行緒中捕捉 HttpCoNtext

請勿這樣 **做：** 下列範例顯示的是 `HttpContext` 從屬性中捕捉的關閉 `Controller` 。 這是不正確的作法，因為工作專案可以：

* 在要求範圍之外執行。
* 嘗試讀取錯誤 `HttpContext` 。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**請執行下列動作：** 下列範例：

* 在要求期間複製背景工作所需的資料。
* 不會參考控制器中的任何事物。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

背景工作應該實作為託管服務。 如需詳細資訊，請參閱[搭配託管服務的背景工作](xref:fundamentals/host/hosted-services)。

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>不要在背景執行緒上捕捉插入控制器的服務

請勿這樣 **做：** 下列範例顯示的是 `DbContext` 從 action 參數中捕捉的關閉 `Controller` 。 這是不正確的作法。  工作專案可以在要求範圍之外執行。 的 `ContosoDbContext` 範圍是要求，會產生 `ObjectDisposedException` 。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**請執行下列動作：** 下列範例：

* 插入，以便在 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 背景工作專案中建立範圍。 `IServiceScopeFactory` 是 singleton。
* 在背景執行緒中建立新的相依性插入範圍。
* 不會參考控制器中的任何事物。
* 不會 `ContosoDbContext` 從傳入要求中捕捉。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

以下反白顯示的程式碼：

* 在背景作業的存留期內建立範圍，並從中解析服務。
* `ContosoDbContext`從正確的範圍使用。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>在回應主體開始之後，請勿修改狀態碼或標頭

ASP.NET Core 不會緩衝 HTTP 回應主體。 第一次寫入回應時：

* 標頭會連同主體的區區塊轉送給用戶端。
* 您不再可以變更回應標頭。

請勿這樣 **做：** 下列程式碼會嘗試在回應已開始之後新增回應標頭：

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

在上述程式碼中， `context.Response.Headers["test"] = "test value";` 如果 `next()` 已寫入回應，將會擲回例外狀況。

**請執行下列動作：** 下列範例會在修改標頭之前，檢查 HTTP 回應是否已啟動。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**請執行下列動作：** 下列範例會在 `HttpResponse.OnStarting` 回應標頭排清至用戶端之前，使用來設定標頭。

檢查回應是否未啟動，允許註冊將在寫入回應標頭之前叫用的回呼。 檢查回應是否未啟動：

* 提供即時附加或覆寫標頭的功能。
* 不需要知道管線中的下一個中介軟體。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>如果您已開始寫入至回應主體，請不要呼叫下一個 ( # A1

只有當元件可以處理和操作回應時，才會被呼叫。

## <a name="use-in-process-hosting-with-iis"></a>使用同進程裝載與 IIS

使用同處理序裝載，ASP.NET Core 應用程式會在與其 IIS 工作者處理序相同的處理序中執行。 內含式裝載可提供跨進程裝載的改善效能，因為要求不會透過回送介面卡進行 proxy 處理。 回送介面卡是一種網路介面，可將連出的網路流量傳回相同的電腦。 IIS 透過 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 來執行處理程序管理。

專案預設為 ASP.NET Core 3.0 和更新版本中的同進程裝載模型。

如需詳細資訊，請參閱 [使用 IIS 在 Windows 上裝載 ASP.NET Core](xref:host-and-deploy/iis/index)