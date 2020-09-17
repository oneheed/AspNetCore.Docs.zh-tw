---
title: ASP.NET Core 中的記憶體管理和模式
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 中管理記憶體，以及垃圾收集行程 (GC) 如何運作。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: 7f1d20687f6dd588e125acf3815815c2bcf0cd04
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722679"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>ASP.NET Core 中的記憶體管理和垃圾收集 (GC) 

[Sébastien Ros](https://github.com/sebastienros)與[Rick Anderson](https://twitter.com/RickAndMSFT)

記憶體管理很複雜，即使在 .NET 之類的 managed 架構中也是如此。 分析和瞭解記憶體問題可能很困難。 這篇文章：

* 有許多 *記憶體* 流失和 *GC 無法正常運作* 的問題。 這些問題大多是因為不了解記憶體耗用量在 .NET Core 中的運作方式所造成，或是不了解它的測量方式。
* 示範有問題的記憶體使用量，並建議替代方法。

## <a name="how-garbage-collection-gc-works-in-net-core"></a>垃圾收集 (GC) 如何在 .NET Core 中運作

GC 會配置堆積區段，其中每個區段都是連續的記憶體範圍。 放在堆積中的物件會分類成3個層代的其中一個：0、1或2。 世代會決定 GC 嘗試釋放應用程式已不再參考的受控物件記憶體的頻率。 較低編號的層代會更頻繁地成為 GC。

物件會根據其存留期從一個世代移至另一個。 當物件存留較久時，它們就會移至較高的層代。 如先前所述，較高的層代較不常進行 GC。 短期存留期物件永遠會保留在層代0中。 例如，在 web 要求存留期間所參考的物件會存留期很短。 應用層級 [singleton](xref:fundamentals/dependency-injection#service-lifetimes) 通常會遷移到第2代。

當 ASP.NET Core 應用程式啟動時，GC：

* 保留一些記憶體給初始堆積區段。
* 在載入執行時間時認可小部分的記憶體。

先前的記憶體配置是基於效能因素而完成。 效能優點來自于連續記憶體中的堆積區段。

### <a name="call-gccollect"></a>呼叫 GC。收集

呼叫 [GC。明確收集](xref:System.GC.Collect*) ：

* **不**應由生產環境 ASP.NET Core 應用程式完成。
* 在調查記憶體流失時很實用。
* 進行調查時，會驗證 GC 是否已從記憶體中移除所有的無關聯物件，以便測量記憶體。

## <a name="analyzing-the-memory-usage-of-an-app"></a>分析應用程式的記憶體使用量

專用工具可協助分析記憶體使用量：

- 計算物件參考
- 測量 GC 對 CPU 使用量的影響程度
- 測量每個世代所使用的記憶體空間

使用下列工具來分析記憶體使用量：

* [dotnet-追蹤](/dotnet/core/diagnostics/dotnet-trace)：可用於生產機器。
* [在沒有 Visual Studio 偵錯工具的情況下分析記憶體使用量](/visualstudio/profiling/memory-usage-without-debugging2)
* [分析 Visual Studio 中的記憶體使用量](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>偵測記憶體問題

工作管理員可以用來瞭解 ASP.NET 應用程式所使用的記憶體數量。 工作管理員的記憶體值：

* 代表 ASP.NET 進程使用的記憶體數量。
* 包含應用程式的生活物件和其他記憶體取用者（例如原生記憶體使用量）。

如果工作管理員的記憶體值無限期地增加，且不會壓平合併，則應用程式會發生記憶體流失。 下列各節將示範和說明數種記憶體使用模式。

## <a name="sample-display-memory-usage-app"></a>範例顯示記憶體使用量應用程式

[MemoryLeak 範例應用程式](https://github.com/sebastienros/memoryleak)可在 GitHub 上取得。 MemoryLeak 應用程式：

* 包含診斷控制器，可收集應用程式的即時記憶體和 GC 資料。
* 具有顯示記憶體和 GC 資料的索引頁面。 索引頁面會每秒重新整理一次。
* 包含提供各種記憶體負載模式的 API 控制器。
* 不是支援的工具，但可以用來顯示 ASP.NET Core 應用程式的記憶體使用模式。

執行 MemoryLeak。 配置的記憶體會在 GC 發生之前變慢。 因為此工具會配置自訂物件來捕捉資料，所以記憶體會增加。 下圖顯示 Gen 0 GC 發生時的 MemoryLeak 索引頁面。 此圖表顯示0個 RPS (每秒要求數) ，因為未呼叫 API 控制器中的 API 端點。

![上圖](memory/_static/0RPS.png)

此圖表會顯示記憶體使用量的兩個值：

- 配置： managed 物件所佔用的記憶體數量
- [工作集](/windows/win32/memory/working-set)：進程的虛擬位址空間中的一組頁面，目前仍在實體記憶體中。 顯示的工作集與工作管理員顯示的值相同。

### <a name="transient-objects"></a>暫時性物件

下列 API 會建立 10 KB 的字串實例，並將它傳回給用戶端。 在每個要求中，會在記憶體中配置新的物件，並將其寫入至回應。 字串在 .NET 中會儲存為 UTF-16 字元，因此每個字元在記憶體中會佔用2個位元組。

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

下圖產生的負載相對較小，以顯示 GC 對記憶體配置有何影響。

![上圖](memory/_static/bigstring.png)

上圖顯示：

* 每秒 4K RPS (要求) 。
* 第0代 GC 回收大約每兩秒發生一次。
* 工作集的常數大約為 500 MB。
* CPU 是12%。
* 透過 GC) 的記憶體耗用量和發行 (是穩定的。

下圖是以機器可處理的最大輸送量為單位。

![上圖](memory/_static/bigstring2.png)

上圖顯示：

* 22K RPS
* 層代 0 GC 回收會每秒發生數次。
* 系統會觸發第1代回收，因為應用程式每秒配置的記憶體會大幅增加。
* 工作集的常數大約為 500 MB。
* CPU 為33%。
* 透過 GC) 的記憶體耗用量和發行 (是穩定的。
* CPU (33% ) 未過度使用，因此垃圾收集可能會跟上大量的配置。

### <a name="workstation-gc-vs-server-gc"></a>工作站 GC 與伺服器 GC 的比較

.NET 垃圾收集行程有兩種不同的模式：

* **工作站 GC**：已針對桌面優化。
* **伺服器 GC**。 ASP.NET Core 應用程式的預設 GC。 已針對伺服器優化。

GC 模式可以在專案檔或已發佈應用程式的 *runtimeconfig.js* 檔案中明確設定。 下列標記會顯示專案檔中的設定 `ServerGarbageCollection` ：

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

變更 `ServerGarbageCollection` 專案檔需要重建應用程式。

**注意：** 在具有單一核心的機器上 **無法** 使用伺服器垃圾收集。 如需詳細資訊，請參閱<xref:System.Runtime.GCSettings.IsServerGC>。

下圖顯示使用工作站 GC 的充分測 RPS 下的記憶體設定檔。

![上圖](memory/_static/workstation.png)

此圖表與伺服器版本之間的差異很重要：

- 工作集從 500 MB 降至 70 MB。
- GC 每秒會執行一次層代0回收，而不是每隔兩秒。
- GC 從 300 MB 降至 10 MB。

在一般的 web 伺服器環境中，CPU 使用量比記憶體更重要，因此伺服器 GC 更好。 如果記憶體使用率很高，且 CPU 使用率相對較低，則工作站 GC 可能會更有效率。 例如，在記憶體不足的情況下裝載數個 web 應用程式的高密度。

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>使用 Docker 和小型容器的 GC

當有多個容器化應用程式在一部電腦上執行時，工作站 GC 可能比伺服器 GC 更 preformant。 如需詳細資訊，請參閱在小型容器中以 [伺服器 gc](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) 執行，並 [在小型容器案例中以伺服器 Gc 執行，第1部分-GC 堆積的固定限制](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)。

### <a name="persistent-object-references"></a>持續性物件參考

GC 無法釋放參考的物件。 參考但不再需要的物件會導致記憶體流失。 如果應用程式經常設定物件，並且在不再需要物件之後無法釋放它們，記憶體使用量將會隨時間增加。

下列 API 會建立 10 KB 的字串實例，並將它傳回給用戶端。 與上一個範例的差異在於，這個實例是由靜態成員所參考，這表示它永遠不能用於集合。

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

上述程式碼：

* 這是典型記憶體流失的範例。
* 使用頻繁的呼叫，會導致應用程式記憶體增加，直到進程損毀併發生 `OutOfMemory` 例外狀況。

![上圖](memory/_static/eternal.png)

在上圖中：

* 負載測試 `/api/staticstring` 端點會導致記憶體中的線性增加。
* GC 會藉由呼叫層代2回收，嘗試在記憶體壓力增加時釋出記憶體。
* GC 無法釋放流失的記憶體。 配置的工作集與時間的增加。

某些案例（例如快取）需要保留物件參考，直到記憶體壓力強制釋放它們為止。 <xref:System.WeakReference>類別可用於這種類型的快取程式碼。 `WeakReference`在記憶體壓力下收集物件。 的預設實作為 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 使用 `WeakReference` 。

### <a name="native-memory"></a>原生記憶體

某些 .NET Core 物件依賴原生記憶體。 GC **無法** 收集原生記憶體。 使用原生記憶體的 .NET 物件必須使用機器碼來釋放它。

.NET 提供 <xref:System.IDisposable> 可讓開發人員釋放原生記憶體的介面。 即使未 <xref:System.IDisposable.Dispose*> 呼叫，也會 `Dispose` 在完成項執行時， [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors)正確實作為類別的呼叫。

請考慮下列程式碼：

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) 是 managed 類別，因此會在要求結束時收集任何實例。

下圖顯示連續叫用 API 時的記憶體設定檔 `fileprovider` 。

![上圖](memory/_static/fileprovider.png)

上圖顯示此類別的執行明顯問題，因為它會持續增加記憶體使用量。 這是在 [這個問題](https://github.com/dotnet/aspnetcore/issues/3110)中所追蹤的已知問題。

在使用者程式碼中，可能會有下列其中一種情況發生相同的洩漏：

* 未正確釋放類別。
* 忘記叫 `Dispose` 用應該處置之相依物件的方法。

### <a name="large-objects-heap"></a>大型物件堆積

頻繁的記憶體配置/可用迴圈可將記憶體片段化，尤其是在配置大型記憶體區塊時。 物件會配置在連續的記憶體區塊中。 若要減少片段，當 GC 釋出記憶體時，它會嘗試將它重組。 此進程稱為「 **壓縮**」。 壓縮牽涉到移動物件。 移動大型物件會對效能造成負面影響。 基於這個理由，GC 會為 _大型_ 物件建立特殊的記憶體區域，稱為 [大型物件堆積](/dotnet/standard/garbage-collection/large-object-heap) (LOH) 。 大於85000個位元組的物件 (大約 83 KB) 為：

* 放在 LOH 上。
* 未壓縮。
* 在層代 2 Gc 期間收集。

當 LOH 已滿時，GC 將會觸發層代2回收。 第2代集合：

* 本質上很慢。
* 此外，也會產生在所有其他層代上觸發集合的成本。

下列程式碼會立即壓縮 LOH：

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>如需壓縮 LOH 的詳細資訊，請參閱。

在使用 .NET Core 3.0 和更新版本的容器中，LOH 會自動壓縮。

下列 API 會說明此行為：

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

下圖顯示 `/api/loh/84975` 在最大負載下呼叫端點的記憶體設定檔：

![上圖](memory/_static/loh1.png)

下圖顯示呼叫端點的記憶體設定檔 `/api/loh/84976` ， *只配置一個位元組*：

![上圖](memory/_static/loh2.png)

注意：此 `byte[]` 結構有額外負荷的位元組。 這就是為什麼84976位元組會觸發85000限制的原因。

比較上述兩個圖表：

* 這兩種案例的工作集很類似，大約是 450 MB。
* LOH 要求 (84975 個位元組) 會顯示大部分的層代0回收。
* Over LOH 要求會產生常數層代2回收。 層代2回收的成本很高。 需要更多 CPU，且輸送量會下降將近50%。

暫存大型物件特別有問題，因為它們會造成 gen2 Gc。

為了達到最大效能，應該將大型物件使用降至最低。 可能的話，請分割大型物件。 例如，ASP.NET Core 中的 [回應](xref:performance/caching/response) 快取中介軟體會將快取專案分割成小於85000個位元組的區塊。

下列連結顯示在 LOH 限制下保留物件的 ASP.NET Core 方法：

* [ResponseCaching/串流/StreamUtilities .cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache .cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

如需詳細資訊，請參閱

* [發現大型物件堆積](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [大型物件堆積](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

不當使用 <xref:System.Net.Http.HttpClient> 可能會導致資源流失。 系統資源，例如資料庫連接、通訊端、檔案控制代碼等：

* 比記憶體更少。
* 當流失的記憶體較大時，會造成更大的問題。

有經驗的 .NET 開發人員知道要在實的 <xref:System.IDisposable.Dispose*> 物件上呼叫 <xref:System.IDisposable> 。 未處置執行的物件 `IDisposable` 通常會導致記憶體流失或洩漏的系統資源。

`HttpClient``IDisposable`會執行，但**不**應該在每次調用時處置。 相反地， `HttpClient` 應該重複使用。

下列端點會  `HttpClient` 在每個要求上建立並處置新的實例：

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

載入時，會記錄下列錯誤訊息：

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

即使 `HttpClient` 已處置實例，實際的網路連接也需要一些時間才能由作業系統釋放。 藉由持續建立新的連線，就會發生 _埠耗盡_ 。 每個用戶端連接都需要自己的用戶端埠。

防止埠耗盡的其中一種方式是重複使用相同的 `HttpClient` 實例：

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

`HttpClient`當應用程式停止時，就會釋放實例。 此範例顯示每次使用後，不應該處置每個可處置的資源。

請參閱下列各項，以取得更好的方式來處理實例的存留期 `HttpClient` ：

* [HttpClient 和存留期管理](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [HTTPClient factory 的 blog](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>物件集區

上一個範例示範了如何將 `HttpClient` 實例設為靜態，並由所有要求重複使用。 重複使用會導致資源不足。

物件共用：

* 使用重複使用模式。
* 是針對建立成本昂貴的物件所設計。

集區是預先初始化的物件集合，可以線上程中保留和釋放。 集區可以定義配置規則，例如限制、預先定義的大小或成長率。

NuGet 套件 [ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) 包含可協助管理這類集區的類別。

下列 API 端點 `byte` 會具現化在每個要求上填入亂數字的緩衝區：

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

下列圖表顯示使用中等負載呼叫上述 API：

![上圖](memory/_static/array.png)

在上圖中，第0代回收大約會每秒執行一次。

上述程式碼可以藉由 `byte` 使用[ArrayPool \<T> ](xref:System.Buffers.ArrayPool`1)來共用緩衝區來優化。 靜態實例會跨要求重複使用。

這種方法有何不同之處，就是從 API 傳回集區物件。 這表示：

* 當您從方法返回時，物件就會移出您的控制項。
* 您無法釋放物件。

若要設定物件的處置：

* 將集區陣列封裝在可處置的物件中。
* 使用 [HttpCoNtext RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)註冊集區物件。

`RegisterForDispose` 會處理 `Dispose` 目標物件上的呼叫，使其只在 HTTP 要求完成時才會釋放。

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

將相同的負載套用為非集區版本會產生下圖：

![上圖](memory/_static/pooledarray.png)

主要差異在於配置的位元組數，因此產生的0代集合會減少許多。

## <a name="additional-resources"></a>其他資源

* [記憶體回收](/dotnet/standard/garbage-collection/)
* [瞭解並行視覺化的不同 GC 模式](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [發現大型物件堆積](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [大型物件堆積](/dotnet/standard/garbage-collection/large-object-heap)