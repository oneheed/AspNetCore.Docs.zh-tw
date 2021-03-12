---
title: ASP.NET Core 中的分散式快取
author: rick-anderson
description: 瞭解如何使用 ASP.NET 核心分散式快取來改善應用程式效能和擴充性，尤其是在雲端或伺服器陣列環境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: a4fd179772a26ffd20fa79cef4720cd5a4746ab3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588610"
---
# <a name="distributed-caching-in-aspnet-core"></a>ASP.NET Core 中的分散式快取

[Mohsin Nasir](https://github.com/mohsinnasir)和[Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

分散式快取是由多個應用程式伺服器所共用的快取，通常會以外部服務的形式維護給存取它的應用程式伺服器。 分散式快取可以改善 ASP.NET Core 應用程式的效能和擴充性，特別是當應用程式是由雲端服務或伺服器陣列所裝載時。

分散式快取的優點比其他快取案例更多，其中快取的資料會儲存在個別的應用程式伺服器上。

當快取的資料散發時，資料會：

*  (一致) 跨多部 *伺服器的要求* 。
* 繼續生存伺服器重新開機和應用程式部署。
* 不使用本機記憶體。

分散式快取設定是特定的執行。 本文說明如何設定 SQL Server 和 Redis 分散式快取。 此外也提供協力廠商的執行，例如[GitHub) 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache。 無論選取哪一個執行，應用程式都會使用介面與快取互動 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/distributed/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

若要使用 SQL Server 分散式快取，請將封裝參考加入至[node.js 套件。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分散式快取，請將套件參考新增至 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 套件。

若要使用 NCache 分散式快取，請將套件參考新增至 [NCache 開放原始碼](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 套件。

## <a name="idistributedcache-interface"></a>>idistributedcache 介面

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>介面提供下列方法來操作分散式快取執行中的專案：

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字串索引鍵，並以陣列形式抓取快取的專案（ `byte[]` 如果在快取中找到的話）。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字串索引鍵，將 (為 `byte[]` 陣列) 的專案加入至快取。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>：根據快取的索引鍵重新整理快取 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> 中的專案，重設其滑動到期時間 (如果有任何) 。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：會根據其字串索引鍵來移除快取專案。

## <a name="establish-distributed-caching-services"></a>建立分散式快取服務

在中註冊的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 執行 `Startup.ConfigureServices` 。 本主題中所述的架構提供的實施包括：

* [分散式記憶體快取](#distributed-memory-cache)
* [分散式 SQL Server 快取](#distributed-sql-server-cache)
* [分散式 Redis 快取](#distributed-redis-cache)
* [分散式 NCache 快取](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>分散式記憶體快取

分散式記憶體快取 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 可將專案儲存在記憶體中的架構所提供的實作為。 分散式記憶體快取不是實際的分散式快取。 快取的專案是由應用程式實例儲存在執行應用程式的伺服器上。

分散式記憶體快取是一個實用的實作為：

* 在開發和測試案例中。
* 當單一伺服器用於生產和記憶體耗用量時，不會發生問題。 執行分散式記憶體快取會抽象化快取的資料儲存區。 如果需要多個節點或容錯功能，它可讓您在未來執行真正的分散式快取解決方案。

當應用程式在的開發環境中執行時，範例應用程式會使用分散式記憶體快取 `Startup.ConfigureServices` ：

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>分散式 SQL Server 快取

分散式 SQL Server 快取執行 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 可讓分散式快取使用 SQL Server 資料庫做為其備份存放區。 若要在 SQL Server 實例中建立 SQL Server 快取專案資料表，您可以使用此 `sql-cache` 工具。 此工具會使用您指定的名稱和架構來建立資料表。

藉由執行命令，在 SQL Server 中建立資料表 `sql-cache create` 。 提供 SQL Server 實例 (`Data Source`) 、資料庫 (`Initial Catalog`) 、架構 (例如) `dbo` 和資料表名稱 (例如， `TestCache`) ：

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

記錄一則訊息，表示工具已成功：

```console
Table and index were created successfully.
```

此工具所建立的資料表 `sql-cache` 具有下列架構：

![SqlServer 快取資料表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 應用程式應該使用的實例來操作快取值 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。

範例應用程式會 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 在的非開發環境中 `Startup.ConfigureServices` 執行：

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (以及（選擇性） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常會儲存在原始檔控制之外 (例如，由 [秘密管理員](xref:security/app-secrets)或 *appsettings.json* / *appsettings 儲存。環境}. json* 檔案) 。 連接字串可能包含應從原始檔控制系統中保留的認證。

### <a name="distributed-redis-cache"></a>分散式 Redis 快取

[Redis](https://redis.io/) 是一種開放原始碼的記憶體內部資料存放區，通常用來做為分散式快取。  您可以為 Azure 託管的 ASP.NET Core 應用程式設定 [Azure Redis](https://azure.microsoft.com/services/cache/) 快取，並使用 azure Redis 快取來進行本機開發。

應用程式會使用實例 () 來設定快取執行 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 。

如需詳細資訊，請參閱 [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)。

如需本機 Redis 快取替代方法的討論，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/19542) 。

### <a name="distributed-ncache-cache"></a>分散式 NCache 快取

[NCache](https://github.com/Alachisoft/NCache) 是一種開放原始碼的記憶體內部分散式快取，以原生方式在 .NET 和 .net Core 中開發。 NCache 可在本機運作，並設定為在 Azure 或其他裝載平臺上執行的 ASP.NET Core 應用程式的分散式快取叢集。

若要在本機電腦上安裝和設定 NCache，請參閱 [Windows ( .net 和 .Net Core) 的 ](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)使用者入門指南。

若要設定 NCache：

1. 安裝 [NCache 開放原始碼 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。
1. 在 [ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)中設定快取叢集。
1. 將下列程式碼新增至 `Startup.ConfigureServices`：

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>使用分散式快取

若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 介面，請 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 從應用程式中的任何函式要求的實例。 實例是由相依性 [插入 (DI) ](xref:fundamentals/dependency-injection)所提供。

當範例應用程式啟動時， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 會插入至 `Startup.Configure` 。 目前的時間會使用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (來快取。如需詳細資訊，請參閱 [泛型主機： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)) ：

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

範例應用程式會插入到中供 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 索引頁面使用。

每次載入 [索引] 頁面時，都會檢查快取中的快取時間 `OnGetAsync` 。 如果快取的時間尚未過期，則會顯示時間。 如果自上一次存取快取時間以來已經過了20秒 (上次載入此頁面的時間) ，則頁面會顯示快取的 *時間已過期*。

選取 [ **重設** 快取時間] 按鈕，立即將快取的時間更新為目前時間。 按鈕會觸發 `OnPostResetCachedTime` 處理常式方法。

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
>  (至少針對內建的) ，則不需要使用實例的單一或範圍存留期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。
>
> 您也可以在 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 任何需要的地方建立實例，而不是使用 DI，但是在程式碼中建立實例可能會讓您的程式碼更難測試，並違反 [明確](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)的相依性原則。

## <a name="recommendations"></a>建議

當您決定哪一個 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最適合您的應用程式時，請考慮下列事項：

* 現有的基礎結構
* 效能需求
* 成本
* 小組經驗

快取解決方案通常依賴記憶體內部儲存體，以快速抓取快取的資料，但記憶體是有限的資源，擴充的成本很高。 只將常用的資料儲存在快取中。

一般而言，Redis 快取提供比 SQL Server 快取更高的輸送量和較低的延遲。 不過，通常需要進行基準測試，以判斷快取策略的效能特性。

當 SQL Server 做為分散式快取備份存放區時，使用相同的資料庫進行快取，而應用程式的一般資料儲存和抓取可能會對這兩者的效能產生負面影響。 建議您針對分散式快取備份存放區使用專用的 SQL Server 實例。

## <a name="additional-resources"></a>其他資源

* [Azure 上的 Redis 快取](/azure/azure-cache-for-redis/)
* [Azure 上的 SQL Database](/azure/sql-database/)
* [ASP.NET Core >idistributedcache Provider For NCache In Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) Farm ([NCache on GitHub](https://github.com/Alachisoft/NCache)) 
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

分散式快取是由多個應用程式伺服器所共用的快取，通常會以外部服務的形式維護給存取它的應用程式伺服器。 分散式快取可以改善 ASP.NET Core 應用程式的效能和擴充性，特別是當應用程式是由雲端服務或伺服器陣列所裝載時。

分散式快取的優點比其他快取案例更多，其中快取的資料會儲存在個別的應用程式伺服器上。

當快取的資料散發時，資料會：

*  (一致) 跨多部 *伺服器的要求* 。
* 繼續生存伺服器重新開機和應用程式部署。
* 不使用本機記憶體。

分散式快取設定是特定的執行。 本文說明如何設定 SQL Server 和 Redis 分散式快取。 此外也提供協力廠商的執行，例如[GitHub) 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache。 無論選取哪一個執行，應用程式都會使用介面與快取互動 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/distributed/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

若要使用 SQL Server 分散式快取，請參考[AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，或將封裝參考加入至[。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分散式快取，請參考 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ，並將套件參考新增至 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 套件。 Redis 套件不包含在套件中 `Microsoft.AspNetCore.App` ，因此您必須在專案檔中個別參考 Redis 套件。

若要使用 NCache 分散式快取，請參考 [AspNetCore 的中繼套件](xref:fundamentals/metapackage-app) ，並將套件參考新增至 [NCache. 開放原始碼](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 封裝。 NCache 套件不包含在套件中 `Microsoft.AspNetCore.App` ，因此您必須在專案檔中個別參考 NCache 套件。

## <a name="idistributedcache-interface"></a>>idistributedcache 介面

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>介面提供下列方法來操作分散式快取執行中的專案：

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字串索引鍵，並以陣列形式抓取快取的專案（ `byte[]` 如果在快取中找到的話）。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字串索引鍵，將 (為 `byte[]` 陣列) 的專案加入至快取。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>：根據快取的索引鍵重新整理快取 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> 中的專案，重設其滑動到期時間 (如果有任何) 。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：會根據其字串索引鍵來移除快取專案。

## <a name="establish-distributed-caching-services"></a>建立分散式快取服務

在中註冊的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 執行 `Startup.ConfigureServices` 。 本主題中所述的架構提供的實施包括：

* [分散式記憶體快取](#distributed-memory-cache)
* [分散式 SQL Server 快取](#distributed-sql-server-cache)
* [分散式 Redis 快取](#distributed-redis-cache)
* [分散式 NCache 快取](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>分散式記憶體快取

分散式記憶體快取 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 可將專案儲存在記憶體中的架構所提供的實作為。 分散式記憶體快取不是實際的分散式快取。 快取的專案是由應用程式實例儲存在執行應用程式的伺服器上。

分散式記憶體快取是一個實用的實作為：

* 在開發和測試案例中。
* 當單一伺服器用於生產和記憶體耗用量時，不會發生問題。 執行分散式記憶體快取會抽象化快取的資料儲存區。 如果需要多個節點或容錯功能，它可讓您在未來執行真正的分散式快取解決方案。

當應用程式在的開發環境中執行時，範例應用程式會使用分散式記憶體快取 `Startup.ConfigureServices` ：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>分散式 SQL Server 快取

分散式 SQL Server 快取執行 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 可讓分散式快取使用 SQL Server 資料庫做為其備份存放區。 若要在 SQL Server 實例中建立 SQL Server 快取專案資料表，您可以使用此 `sql-cache` 工具。 此工具會使用您指定的名稱和架構來建立資料表。

藉由執行命令，在 SQL Server 中建立資料表 `sql-cache create` 。 提供 SQL Server 實例 (`Data Source`) 、資料庫 (`Initial Catalog`) 、架構 (例如) `dbo` 和資料表名稱 (例如， `TestCache`) ：

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

記錄一則訊息，表示工具已成功：

```console
Table and index were created successfully.
```

此工具所建立的資料表 `sql-cache` 具有下列架構：

![SqlServer 快取資料表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 應用程式應該使用的實例來操作快取值 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。

範例應用程式會 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 在的非開發環境中 `Startup.ConfigureServices` 執行：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (以及（選擇性） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常會儲存在原始檔控制之外 (例如，由 [秘密管理員](xref:security/app-secrets)或 *appsettings.json* / *appsettings 儲存。環境}. json* 檔案) 。 連接字串可能包含應從原始檔控制系統中保留的認證。

### <a name="distributed-redis-cache"></a>分散式 Redis 快取

[Redis](https://redis.io/) 是一種開放原始碼的記憶體內部資料存放區，通常用來做為分散式快取。 您可以在本機使用 Redis，也可以為 Azure 託管的 ASP.NET 核心應用程式設定 [Azure Redis](https://azure.microsoft.com/services/cache/) 快取。

應用程式會在 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 下列情況中，使用 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 非開發環境中的實例 () 來設定快取執行 `Startup.ConfigureServices` ：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

若要在本機電腦上安裝 Redis：

1. 安裝 [Chocolatey Redis 套件](https://chocolatey.org/packages/redis-64/)。
1. `redis-server`從命令提示字元執行。

### <a name="distributed-ncache-cache"></a>分散式 NCache 快取

[NCache](https://github.com/Alachisoft/NCache) 是一種開放原始碼的記憶體內部分散式快取，以原生方式在 .NET 和 .net Core 中開發。 NCache 可在本機運作，並設定為在 Azure 或其他裝載平臺上執行的 ASP.NET Core 應用程式的分散式快取叢集。

若要在本機電腦上安裝和設定 NCache，請參閱 [Windows ( .net 和 .Net Core) 的 ](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)使用者入門指南。

若要設定 NCache：

1. 安裝 [NCache 開放原始碼 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。
1. 在 [ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)中設定快取叢集。
1. 將下列程式碼新增至 `Startup.ConfigureServices`：

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>使用分散式快取

若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 介面，請 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 從應用程式中的任何函式要求的實例。 實例是由相依性 [插入 (DI) ](xref:fundamentals/dependency-injection)所提供。

當範例應用程式啟動時， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 會插入至 `Startup.Configure` 。 目前的時間會使用 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (來快取。如需詳細資訊，請參閱 [Web 主機： IApplicationLifetime 介面](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) ：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

範例應用程式會插入到中供 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 索引頁面使用。

每次載入 [索引] 頁面時，都會檢查快取中的快取時間 `OnGetAsync` 。 如果快取的時間尚未過期，則會顯示時間。 如果自上一次存取快取時間以來已經過了20秒 (上次載入此頁面的時間) ，則頁面會顯示快取的 *時間已過期*。

選取 [ **重設** 快取時間] 按鈕，立即將快取的時間更新為目前時間。 按鈕會觸發 `OnPostResetCachedTime` 處理常式方法。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
>  (至少針對內建的) ，則不需要使用實例的單一或範圍存留期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。
>
> 您也可以在 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 任何需要的地方建立實例，而不是使用 DI，但是在程式碼中建立實例可能會讓您的程式碼更難測試，並違反 [明確](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)的相依性原則。

## <a name="recommendations"></a>建議

當您決定哪一個 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最適合您的應用程式時，請考慮下列事項：

* 現有的基礎結構
* 效能需求
* 成本
* 小組經驗

快取解決方案通常依賴記憶體內部儲存體，以快速抓取快取的資料，但記憶體是有限的資源，擴充的成本很高。 只將常用的資料儲存在快取中。

一般而言，Redis 快取提供比 SQL Server 快取更高的輸送量和較低的延遲。 不過，通常需要進行基準測試，以判斷快取策略的效能特性。

當 SQL Server 做為分散式快取備份存放區時，使用相同的資料庫進行快取，而應用程式的一般資料儲存和抓取可能會對這兩者的效能產生負面影響。 建議您針對分散式快取備份存放區使用專用的 SQL Server 實例。

## <a name="additional-resources"></a>其他資源

* [Azure 上的 Redis 快取](/azure/azure-cache-for-redis/)
* [Azure 上的 SQL Database](/azure/sql-database/)
* [ASP.NET Core >idistributedcache Provider For NCache In Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) Farm ([NCache on GitHub](https://github.com/Alachisoft/NCache)) 
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

分散式快取是由多個應用程式伺服器所共用的快取，通常會以外部服務的形式維護給存取它的應用程式伺服器。 分散式快取可以改善 ASP.NET Core 應用程式的效能和擴充性，特別是當應用程式是由雲端服務或伺服器陣列所裝載時。

分散式快取的優點比其他快取案例更多，其中快取的資料會儲存在個別的應用程式伺服器上。

當快取的資料散發時，資料會：

*  (一致) 跨多部 *伺服器的要求* 。
* 繼續生存伺服器重新開機和應用程式部署。
* 不使用本機記憶體。

分散式快取設定是特定的執行。 本文說明如何設定 SQL Server 和 Redis 分散式快取。 此外也提供協力廠商的執行，例如[GitHub) 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache。 無論選取哪一個執行，應用程式都會使用介面與快取互動 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/distributed/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

若要使用 SQL Server 分散式快取，請參考[AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，或將封裝參考加入至[。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分散式快取，請參考 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ，並將套件參考新增至 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 套件。 Redis 套件不包含在套件中 `Microsoft.AspNetCore.App` ，因此您必須在專案檔中個別參考 Redis 套件。

若要使用 NCache 分散式快取，請參考 [AspNetCore 的中繼套件](xref:fundamentals/metapackage-app) ，並將套件參考新增至 [NCache. 開放原始碼](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 封裝。 NCache 套件不包含在套件中 `Microsoft.AspNetCore.App` ，因此您必須在專案檔中個別參考 NCache 套件。

## <a name="idistributedcache-interface"></a>>idistributedcache 介面

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>介面提供下列方法來操作分散式快取執行中的專案：

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字串索引鍵，並以陣列形式抓取快取的專案（ `byte[]` 如果在快取中找到的話）。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字串索引鍵，將 (為 `byte[]` 陣列) 的專案加入至快取。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>：根據快取的索引鍵重新整理快取 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> 中的專案，重設其滑動到期時間 (如果有任何) 。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：會根據其字串索引鍵來移除快取專案。

## <a name="establish-distributed-caching-services"></a>建立分散式快取服務

在中註冊的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 執行 `Startup.ConfigureServices` 。 本主題中所述的架構提供的實施包括：

* [分散式記憶體快取](#distributed-memory-cache)
* [分散式 SQL Server 快取](#distributed-sql-server-cache)
* [分散式 Redis 快取](#distributed-redis-cache)
* [分散式 NCache 快取](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>分散式記憶體快取

分散式記憶體快取 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 可將專案儲存在記憶體中的架構所提供的實作為。 分散式記憶體快取不是實際的分散式快取。 快取的專案是由應用程式實例儲存在執行應用程式的伺服器上。

分散式記憶體快取是一個實用的實作為：

* 在開發和測試案例中。
* 當單一伺服器用於生產和記憶體耗用量時，不會發生問題。 執行分散式記憶體快取會抽象化快取的資料儲存區。 如果需要多個節點或容錯功能，它可讓您在未來執行真正的分散式快取解決方案。

當應用程式在的開發環境中執行時，範例應用程式會使用分散式記憶體快取 `Startup.ConfigureServices` ：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>分散式 SQL Server 快取

分散式 SQL Server 快取執行 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 可讓分散式快取使用 SQL Server 資料庫做為其備份存放區。 若要在 SQL Server 實例中建立 SQL Server 快取專案資料表，您可以使用此 `sql-cache` 工具。 此工具會使用您指定的名稱和架構來建立資料表。

藉由執行命令，在 SQL Server 中建立資料表 `sql-cache create` 。 提供 SQL Server 實例 (`Data Source`) 、資料庫 (`Initial Catalog`) 、架構 (例如) `dbo` 和資料表名稱 (例如， `TestCache`) ：

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

記錄一則訊息，表示工具已成功：

```console
Table and index were created successfully.
```

此工具所建立的資料表 `sql-cache` 具有下列架構：

![SqlServer 快取資料表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 應用程式應該使用的實例來操作快取值 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。

範例應用程式會 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 在的非開發環境中 `Startup.ConfigureServices` 執行：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (以及（選擇性） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常會儲存在原始檔控制之外 (例如，由 [秘密管理員](xref:security/app-secrets)或 *appsettings.json* / *appsettings 儲存。環境}. json* 檔案) 。 連接字串可能包含應從原始檔控制系統中保留的認證。

### <a name="distributed-redis-cache"></a>分散式 Redis 快取

[Redis](https://redis.io/) 是一種開放原始碼的記憶體內部資料存放區，通常用來做為分散式快取。 您可以在本機使用 Redis，也可以為 Azure 託管的 ASP.NET 核心應用程式設定 [Azure Redis](https://azure.microsoft.com/services/cache/) 快取。

應用程式會使用實例 () 來設定快取執行 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ：

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

若要在本機電腦上安裝 Redis：

1. 安裝 [Chocolatey Redis 套件](https://chocolatey.org/packages/redis-64/)。
1. `redis-server`從命令提示字元執行。

### <a name="distributed-ncache-cache"></a>分散式 NCache 快取

[NCache](https://github.com/Alachisoft/NCache) 是一種開放原始碼的記憶體內部分散式快取，以原生方式在 .NET 和 .net Core 中開發。 NCache 可在本機運作，並設定為在 Azure 或其他裝載平臺上執行的 ASP.NET Core 應用程式的分散式快取叢集。

若要在本機電腦上安裝和設定 NCache，請參閱 [Windows ( .net 和 .Net Core) 的 ](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)使用者入門指南。

若要設定 NCache：

1. 安裝 [NCache 開放原始碼 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。
1. 在 [ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)中設定快取叢集。
1. 將下列程式碼新增至 `Startup.ConfigureServices`：

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>使用分散式快取

若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 介面，請 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 從應用程式中的任何函式要求的實例。 實例是由相依性 [插入 (DI) ](xref:fundamentals/dependency-injection)所提供。

當範例應用程式啟動時， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 會插入至 `Startup.Configure` 。 目前的時間會使用 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (來快取。如需詳細資訊，請參閱 [Web 主機： IApplicationLifetime 介面](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) ：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

範例應用程式會插入到中供 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 索引頁面使用。

每次載入 [索引] 頁面時，都會檢查快取中的快取時間 `OnGetAsync` 。 如果快取的時間尚未過期，則會顯示時間。 如果自上一次存取快取時間以來已經過了20秒 (上次載入此頁面的時間) ，則頁面會顯示快取的 *時間已過期*。

選取 [ **重設** 快取時間] 按鈕，立即將快取的時間更新為目前時間。 按鈕會觸發 `OnPostResetCachedTime` 處理常式方法。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
>  (至少針對內建的) ，則不需要使用實例的單一或範圍存留期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。
>
> 您也可以在 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 任何需要的地方建立實例，而不是使用 DI，但是在程式碼中建立實例可能會讓您的程式碼更難測試，並違反 [明確](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)的相依性原則。

## <a name="recommendations"></a>建議

當您決定哪一個 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最適合您的應用程式時，請考慮下列事項：

* 現有的基礎結構
* 效能需求
* 成本
* 小組經驗

快取解決方案通常依賴記憶體內部儲存體，以快速抓取快取的資料，但記憶體是有限的資源，擴充的成本很高。 只將常用的資料儲存在快取中。

一般而言，Redis 快取提供比 SQL Server 快取更高的輸送量和較低的延遲。 不過，通常需要進行基準測試，以判斷快取策略的效能特性。

當 SQL Server 做為分散式快取備份存放區時，使用相同的資料庫進行快取，而應用程式的一般資料儲存和抓取可能會對這兩者的效能產生負面影響。 建議您針對分散式快取備份存放區使用專用的 SQL Server 實例。

## <a name="additional-resources"></a>其他資源

* [Azure 上的 Redis 快取](/azure/azure-cache-for-redis/)
* [Azure 上的 SQL Database](/azure/sql-database/)
* [ASP.NET Core >idistributedcache Provider For NCache In Web](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) Farm ([NCache on GitHub](https://github.com/Alachisoft/NCache)) 
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
