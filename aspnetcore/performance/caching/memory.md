---
title: ASP.NET Core 中的快取記憶體
author: rick-anderson
description: 了解如何快取 ASP.NET Core 中的資料和記憶體。
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
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
uid: performance/caching/memory
ms.openlocfilehash: 71aeb246a22e236ea134e0427d07837b09988bf9
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105051006"
---
# <a name="cache-in-memory-in-aspnet-core"></a>ASP.NET Core 中的快取記憶體

::: moniker range=">= aspnetcore-3.0"

依 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和 [Steve Smith](https://ardalis.com/)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/memory/3.0sample) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="caching-basics"></a>快取基本概念

快取可以藉由減少產生內容所需的工作，大幅改善應用程式的效能和擴充性。 快取最適合用於不常變更 **且** 產生昂貴的資料。 快取會建立資料的複本，而這些資料的傳回速度會比來源快許多。 應用程式應該撰寫和測試， **永遠不** 依賴快取的資料。

ASP.NET Core 支援數個不同的快取。 最簡單的快取是以 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)為基礎。 `IMemoryCache` 表示儲存在 web 伺服器記憶體中的快取。 在伺服器陣列上執行的應用程式 (多部伺服器) 應可確保在使用記憶體中快取時，會話會保持在使用狀態。 「粘滯話」可確保來自用戶端的後續要求都會移至相同的伺服器。 例如，Azure Web apps 會使用 [應用程式要求路由](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) 將所有後續的要求路由傳送到相同的伺服器。

Web 伺服陣列中的非粘滯話需要 [分散式](distributed.md) 快取，以避免快取一致性問題。 針對某些應用程式，分散式快取可支援比記憶體中快取更高的擴充。 使用分散式快取可將快取記憶體卸載至外部進程。

記憶體中的快取可以儲存任何物件。 分散式快取介面的限制為 `byte[]` 。 記憶體中和分散式快取會將快取專案儲存為索引鍵/值組。

## <a name="systemruntimecachingmemorycache"></a>System. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 套件](https://www.nuget.org/packages/System.Runtime.Caching/)) 可搭配使用：

* .NET Standard 2.0 或更新版本。
* 任何以 .NET Standard 2.0 或更新版本為目標的 [.net 執行](/dotnet/standard/net-standard#net-implementation-support) 。 例如，ASP.NET Core 2.0 或更新版本。
* .NET Framework 4.5 或更新版本。

[](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` 建議您不要使用本文所述的 (， `System.Runtime.Caching` / `MemoryCache` 因為它已更緊密地整合到 ASP.NET Core) 中。 例如，以原生 `IMemoryCache` 方式使用 ASP.NET Core 相依性 [插入](xref:fundamentals/dependency-injection)。

將 `System.Runtime.Caching` / `MemoryCache` ASP.NET 4.x 的程式碼移植到 ASP.NET Core 時，請使用做為相容性橋樑。

## <a name="cache-guidelines"></a>快取指導方針

* 程式碼應該一律具有可提取資料的回溯選項，而 **不** 會相依于可用的快取值。
* 快取會使用稀有資源，也就是記憶體。 限制快取成長：
  * 請勿 **使用外部輸入做為** 快取索引鍵。
  * 使用過期來限制快取成長。
  * [使用 SetSize、Size 和 SizeLimit 來限制](#use-setsize-size-and-sizelimit-to-limit-cache-size)快取大小。 ASP.NET Core 執行時間不會根據記憶體 **壓力限制快** 取大小。 開發人員需要限制快取大小。

## <a name="use-imemorycache"></a>使用 IMemoryCache

> [!WARNING]
> 使用來自相依性 [插入](xref:fundamentals/dependency-injection)的 *共用* 記憶體快取，以及呼叫 `SetSize` 、 `Size` 或 `SizeLimit` 來限制快取大小，可能會導致應用程式失敗。 在快取上設定大小限制時，所有專案都必須在新增時指定大小。 這可能會導致問題，因為開發人員可能無法完全掌控使用共用快取的內容。 例如，Entity Framework Core 會使用共用快取，且不會指定大小。 如果應用程式設定快取大小限制並使用 EF Core，應用程式會擲回 `InvalidOperationException` 。
> 使用 `SetSize` 、或來限制快取時，請建立快取 `Size` `SizeLimit` singleton 以進行快取。 如需詳細資訊和範例，請參閱 [使用 SetSize、大小和 SizeLimit 來限制](#use-setsize-size-and-sizelimit-to-limit-cache-size)快取大小。
> 共用快取是由其他架構或程式庫共用。 例如，EF Core 會使用共用快取，且不會指定大小。 

記憶體內部快取是從使用相依性 [插入](xref:fundamentals/dependency-injection)的應用程式所參考的 *服務*。 在函式 `IMemoryCache` 中要求實例：

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

下列程式碼會使用 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) 來檢查快取中是否有時間。 如果未快取某個時間，則會建立新的專案，並 [將](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)其加入至快取。 `CacheKeys`類別是下載範例的一部分。

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

此時會顯示目前的時間和快取的時間：

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

下列程式碼會使用 [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_System_TimeSpan_) 擴充方法來快取相對時間的資料，而不需要建立 `MemoryCacheEntryOptions` 物件。
[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_set)]

快取的 `DateTime` 值會保留在快取中，而在此時間內有要求。

下列程式碼會使用 [getorcreate 設定](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 和 [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 來快取資料。

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

下列程式碼會呼叫 Get 來提取 [快](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 取的時間：

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

下列程式碼會取得或建立具有絕對到期時間的快取專案：

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

具有滑動期限的快取專案集，只會有過時的風險。 如果比滑動到期間隔更頻繁地存取，專案將永遠不會過期。 將滑動期限與絕對到期合併，以保證專案會在其絕對到期時間過後到期。 絕對到期時間會將專案的上限設定為可快取的時間長度，但如果不是在滑動到期間隔內要求，則仍允許專案提早到期。 當指定絕對和滑動期限時，會以邏輯方式 Or 運算到期時間。 如果滑動到期間隔 *或* 絕對到期時間通過，則會從快取中收回專案。

下列程式碼會取得或建立具有滑動 *和* 絕對到期的快取專案：

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

上述程式碼保證資料的快取時間不會超過絕對時間。

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> 是類別中的擴充方法 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> 。 這些方法會擴充的功能 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 。

## `MemoryCacheEntryOptions`

下列範例：

* 設定滑動到期時間。 存取這個快取專案的要求將會重設滑動的到期時鐘。
* 將快取優先權設定為 [CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove)。
* 設定從快取收回專案之後，將會呼叫的 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。 回呼是在從快取中移除專案的程式碼的不同執行緒上執行。

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>使用 SetSize、Size 和 SizeLimit 來限制快取大小

`MemoryCache`實例可以選擇性地指定並強制執行大小限制。 快取大小限制沒有已定義的測量單位，因為快取沒有任何機制可測量專案的大小。 如果已設定快取大小限制，則所有專案都必須指定大小。 ASP.NET Core 執行時間不會根據記憶體壓力限制快取大小。 開發人員需要限制快取大小。 指定的大小是以開發人員選擇的單位來計算。

例如：

* 如果 web 應用程式主要是快取字串，則每個快取專案大小都可能是字串長度。
* 應用程式可以將所有專案的大小指定為1，且大小限制為專案計數。

如果 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> 未設定，快取就會成長而不受限制。 當系統記憶體不足時，ASP.NET Core 執行時間不會修剪快取。 應用程式必須架構為：

* 限制快取成長。
* 呼叫 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> 可用記憶體的限制：

下列程式碼會建立相依 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> 性 [插入](xref:fundamentals/dependency-injection)所可存取的未使用固定大小：

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` 沒有單位。 如果已設定快取大小限制，則快取的專案必須以其最適合的任何單位來指定大小。 快取實例的所有使用者都應該使用相同的單位系統。 如果快取專案大小的總和超過所指定的值，則不會快取專案 `SizeLimit` 。 如果未設定快取大小限制，則會忽略專案上設定的快取大小。

下列程式碼會 `MyMemoryCache` 向相依性 [插入](xref:fundamentals/dependency-injection) 容器註冊。

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

`MyMemoryCache` 會建立為元件的獨立記憶體快取，此快取會察覺此大小有限的快取，並知道如何適當地設定快取專案大小。

下列程式碼會使用 `MyMemoryCache` ：

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

您可以設定快取專案的大小 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> 擴充方法：

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache Compact

`MemoryCache.Compact` 嘗試以下列順序移除指定百分比的快取：

* 所有過期的專案。
* 依優先順序排序的專案。 系統會先移除最低優先順序專案。
* 最近最少使用的物件。
* 具有最早絕對過期的專案。
* 具有最早滑動期限的專案。

永遠不會移除具有優先權的釘選項目 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 。 下列程式碼會移除快取專案並呼叫 `Compact` ：

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

如需詳細資訊，請參閱 [GitHub 上的 Compact source](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) 。

## <a name="cache-dependencies"></a>快取相依性

下列範例會示範當相依專案過期時，如何將快取專案過期。 <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>會加入至快取的專案。 當在 `Cancel` 上呼叫時 `CancellationTokenSource` ，這兩個快取專案都會被收回。

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

使用 <xref:System.Threading.CancellationTokenSource> 可讓多個快取專案以群組的形式收回。 使用 `using` 上述程式碼中的模式，在區塊內建立的快取專案 `using` 將會繼承觸發程式和到期設定。

## <a name="additional-notes"></a>其他注意事項

* 到期不會在背景中發生。 沒有任何計時器會主動掃描快取是否有過期的專案。 快取 (、) 上的任何活動 `Get` `Set` `Remove` 都可以觸發過期專案的背景掃描。  (上的計時器 `CancellationTokenSource` <xref:System.Threading.CancellationTokenSource.CancelAfter*>) 也會移除專案，並觸發掃描是否有過期的專案。 下列範例會針對已註冊的權杖使用 [CancellationTokenSource (TimeSpan) ](/dotnet/api/system.threading.cancellationtokensource.-ctor) 。 當這個權杖引發時，它會立即移除專案並引發收回回呼：

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* 使用回呼來重新擴展快取專案時：

  * 由於回呼未完成，因此多個要求可以找到空白的快取索引鍵值。
  * 這可能會導致數個執行緒重新填入快取的專案。

* 當某個快取專案用來建立另一個快取專案時，子系會複製父專案的到期權杖和以時間為基礎的到期設定。 子系不會藉由手動移除或更新父專案而過期。

* 用 <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> 來設定從快取收回快取專案之後將引發的回呼。
* 針對大部分的應用程式， `IMemoryCache` 則會啟用。 例如， `AddMvc` 在中呼叫、、 `AddControllersWithViews` `AddRazorPages` 、 `AddMvcCore().AddRazorViewEngine` 及許多其他 `Add{Service}` 方法會 `ConfigureServices` 啟用 `IMemoryCache` 。 針對未呼叫上述其中一個方法的應用程式 `Add{Service}` ，可能需要 <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> 在中呼叫 `ConfigureServices` 。

## <a name="background-cache-update"></a>背景快取更新

使用 [背景服務](xref:fundamentals/host/hosted-services) （例如） <xref:Microsoft.Extensions.Hosting.IHostedService> 來更新快取。 背景服務可以重新計算專案，然後在快取準備就緒時將它們指派給快取。

## <a name="additional-resources"></a>其他資源

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
依 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和 [Steve Smith](https://ardalis.com/)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/memory/sample) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="caching-basics"></a>快取基本概念

快取可以藉由減少產生內容所需的工作，大幅改善應用程式的效能和擴充性。 快取最適用于不常變更的資料。 快取會建立資料的複本，而這些資料的傳回速度會比原始來源快很多。 撰寫和測試程式碼時， **絕對不會** 依賴快取的資料。

ASP.NET Core 支援數個不同的快取。 最簡單的快取是以 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)為基礎，代表儲存在 web 伺服器記憶體中的快取。 在伺服器陣列 (多部伺服器上執行的應用程式) 應該確保在使用記憶體中快取時，會話會保持在使用中。 「粘滯話」可確保來自用戶端的後續要求都會移至相同的伺服器。 例如，Azure Web apps 會使用 [應用程式要求路由](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) 將使用者代理程式的所有要求路由傳送至相同的伺服器。

Web 伺服陣列中的非粘滯話需要 [分散式](distributed.md) 快取，以避免快取一致性問題。 針對某些應用程式，分散式快取可支援比記憶體中快取更高的擴充。 使用分散式快取可將快取記憶體卸載至外部進程。

記憶體中的快取可以儲存任何物件。 分散式快取介面的限制為 `byte[]` 。 記憶體中和分散式快取會將快取專案儲存為索引鍵/值組。

## <a name="systemruntimecachingmemorycache"></a>System. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 套件](https://www.nuget.org/packages/System.Runtime.Caching/)) 可搭配使用：

* .NET Standard 2.0 或更新版本。
* 任何以 .NET Standard 2.0 或更新版本為目標的 [.net 執行](/dotnet/standard/net-standard#net-implementation-support) 。 例如，ASP.NET Core 2.0 或更新版本。
* .NET Framework 4.5 或更新版本。

[](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` 建議您不要使用本文所述的 (， `System.Runtime.Caching` / `MemoryCache` 因為它已更緊密地整合到 ASP.NET Core) 中。 例如，以原生 `IMemoryCache` 方式使用 ASP.NET Core 相依性 [插入](xref:fundamentals/dependency-injection)。

將 `System.Runtime.Caching` / `MemoryCache` ASP.NET 4.x 的程式碼移植到 ASP.NET Core 時，請使用做為相容性橋樑。

## <a name="cache-guidelines"></a>快取指導方針

* 程式碼應該一律具有可提取資料的回溯選項，而 **不** 會相依于可用的快取值。
* 快取會使用稀有資源，也就是記憶體。 限制快取成長：
  * 請勿 **使用外部輸入做為** 快取索引鍵。
  * 使用過期來限制快取成長。
  * [使用 SetSize、Size 和 SizeLimit 來限制](#use-setsize-size-and-sizelimit-to-limit-cache-size)快取大小。 ASP.NET Core 執行時間不會根據記憶體壓力限制快取大小。 開發人員需要限制快取大小。

## <a name="using-imemorycache"></a>使用 IMemoryCache

> [!WARNING]
> 使用來自相依性 [插入](xref:fundamentals/dependency-injection)的 *共用* 記憶體快取，以及呼叫 `SetSize` 、 `Size` 或 `SizeLimit` 來限制快取大小，可能會導致應用程式失敗。 在快取上設定大小限制時，所有專案都必須在新增時指定大小。 這可能會導致問題，因為開發人員可能無法完全掌控使用共用快取的內容。 例如，Entity Framework Core 會使用共用快取，且不會指定大小。 如果應用程式設定快取大小限制並使用 EF Core，應用程式會擲回 `InvalidOperationException` 。
> 使用 `SetSize` 、或來限制快取時，請建立快取 `Size` `SizeLimit` singleton 以進行快取。 如需詳細資訊和範例，請參閱 [使用 SetSize、大小和 SizeLimit 來限制](#use-setsize-size-and-sizelimit-to-limit-cache-size)快取大小。

記憶體內部快取是使用相依性 [插入](../../fundamentals/dependency-injection.md)從您的應用程式參考的 *服務*。 呼叫 `AddMemoryCache` 于 `ConfigureServices` ：

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

在函式 `IMemoryCache` 中要求實例：

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

`IMemoryCache`需要 NuGet 套件[Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)中提供的版本，可供[使用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)

下列程式碼會使用 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) 來檢查快取中是否有時間。 如果未快取某個時間，則會建立新的專案，並 [將](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)其加入至快取。

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

此時會顯示目前的時間和快取的時間：

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

快取的 `DateTime` 值會保留在快取中，而在此時間內有要求。 下圖顯示從快取取出的目前時間和較舊的時間：

![顯示兩個不同時間的索引視圖](memory/_static/time.png)

下列程式碼會使用 [getorcreate 設定](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 和 [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 來快取資料。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

下列程式碼會呼叫 Get 來提取 [快](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 取的時間：

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> 、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 是 [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 類別的擴充方法的一部分，可延伸的功能 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 。 如需其他快取方法的描述，請參閱 [IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) 和 [CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 。

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

下列範例：

* 設定滑動到期時間。 存取這個快取專案的要求將會重設滑動的到期時鐘。
* 將快取優先權設定為 `CacheItemPriority.NeverRemove` 。
* 設定從快取收回專案之後，將會呼叫的 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。 回呼是在從快取中移除專案的程式碼的不同執行緒上執行。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>使用 SetSize、Size 和 SizeLimit 來限制快取大小

`MemoryCache`實例可以選擇性地指定並強制執行大小限制。 快取大小限制沒有已定義的測量單位，因為快取沒有任何機制可測量專案的大小。 如果已設定快取大小限制，則所有專案都必須指定大小。 ASP.NET Core 執行時間不會根據記憶體壓力限制快取大小。 開發人員需要限制快取大小。 指定的大小是以開發人員選擇的單位來計算。

例如：

* 如果 web 應用程式主要是快取字串，則每個快取專案大小都可能是字串長度。
* 應用程式可以將所有專案的大小指定為1，且大小限制為專案計數。

如果 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> 未設定，快取就會成長而不受限制。 當系統記憶體不足時，ASP.NET Core 執行時間不會修剪快取。 應用程式的架構很多：

* 限制快取成長。
* 呼叫 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> 可用記憶體的限制：

下列程式碼會建立相依 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> 性 [插入](xref:fundamentals/dependency-injection)所可存取的未使用固定大小：

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` 沒有單位。 如果已設定快取大小限制，則快取的專案必須以其最適合的任何單位來指定大小。 快取實例的所有使用者都應該使用相同的單位系統。 如果快取專案大小的總和超過所指定的值，則不會快取專案 `SizeLimit` 。 如果未設定快取大小限制，則會忽略專案上設定的快取大小。

下列程式碼會 `MyMemoryCache` 向相依性 [插入](xref:fundamentals/dependency-injection) 容器註冊。

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` 會建立為元件的獨立記憶體快取，此快取會察覺此大小有限的快取，並知道如何適當地設定快取專案大小。

下列程式碼會使用 `MyMemoryCache` ：

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

快取專案的大小可依 [大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) 或 [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) 擴充方法設定：

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache Compact

`MemoryCache.Compact` 嘗試以下列順序移除指定百分比的快取：

* 所有過期的專案。
* 依優先順序排序的專案。 系統會先移除最低優先順序專案。
* 最近最少使用的物件。
* 具有最早絕對過期的專案。
* 具有最早滑動期限的專案。

永遠不會移除具有優先權的釘選項目 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 。

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

如需詳細資訊，請參閱 [GitHub 上的 Compact source](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) 。

## <a name="cache-dependencies"></a>快取相依性

下列範例會示範當相依專案過期時，如何將快取專案過期。 <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>會加入至快取的專案。 當在 `Cancel` 上呼叫時 `CancellationTokenSource` ，這兩個快取專案都會被收回。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

使用 `CancellationTokenSource` 可讓多個快取專案以群組的形式收回。 使用 `using` 上述程式碼中的模式，在區塊內建立的快取專案 `using` 將會繼承觸發程式和到期設定。

## <a name="additional-notes"></a>其他注意事項

* 使用回呼來重新擴展快取專案時：

  * 由於回呼未完成，因此多個要求可以找到空白的快取索引鍵值。
  * 這可能會導致數個執行緒重新填入快取的專案。

* 當某個快取專案用來建立另一個快取專案時，子系會複製父專案的到期權杖和以時間為基礎的到期設定。 子系不會藉由手動移除或更新父專案而過期。

* 使用 [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) 設定將在快取專案從快取中收回之後引發的回呼。

## <a name="background-cache-update"></a>背景快取更新

使用 [背景服務](xref:fundamentals/host/hosted-services) （例如） <xref:Microsoft.Extensions.Hosting.IHostedService> 來更新快取。 背景服務可以重新計算專案，然後在快取準備就緒時將它們指派給快取。

## <a name="additional-resources"></a>其他資源

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
