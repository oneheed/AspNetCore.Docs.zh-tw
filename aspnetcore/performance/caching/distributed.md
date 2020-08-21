---
title: ASP.NET Core 中的分散式快取
author: rick-anderson
description: 瞭解如何使用 ASP.NET Core 分散式快取來改善應用程式效能和擴充性，尤其是在雲端或伺服器陣列環境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 0d27206412a098f4ea749ec10189bf24d2322de1
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712476"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="70457-103">ASP.NET Core 中的分散式快取</span><span class="sxs-lookup"><span data-stu-id="70457-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="70457-104">[Mohsin Nasir](https://github.com/mohsinnasir)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="70457-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70457-105">分散式快取是由多個應用程式伺服器所共用的快取，通常會以外部服務的形式維護給存取它的應用程式伺服器。</span><span class="sxs-lookup"><span data-stu-id="70457-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="70457-106">分散式快取可以改善 ASP.NET Core 應用程式的效能和擴充性，特別是當應用程式是由雲端服務或伺服器陣列所裝載時。</span><span class="sxs-lookup"><span data-stu-id="70457-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="70457-107">分散式快取的優點比其他快取案例更多，其中快取的資料會儲存在個別的應用程式伺服器上。</span><span class="sxs-lookup"><span data-stu-id="70457-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="70457-108">當快取的資料散發時，資料會：</span><span class="sxs-lookup"><span data-stu-id="70457-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="70457-109"> (一致) 跨多部 *伺服器的要求* 。</span><span class="sxs-lookup"><span data-stu-id="70457-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="70457-110">繼續生存伺服器重新開機和應用程式部署。</span><span class="sxs-lookup"><span data-stu-id="70457-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="70457-111">不使用本機記憶體。</span><span class="sxs-lookup"><span data-stu-id="70457-111">Doesn't use local memory.</span></span>

<span data-ttu-id="70457-112">分散式快取設定是特定的執行。</span><span class="sxs-lookup"><span data-stu-id="70457-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="70457-113">本文說明如何設定 SQL Server 和 Redis 分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="70457-114">此外也提供協力廠商的執行，例如[GitHub) 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache。</span><span class="sxs-lookup"><span data-stu-id="70457-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="70457-115">無論選取哪一個執行，應用程式都會使用介面與快取互動 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="70457-116">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="70457-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70457-117">先決條件</span><span class="sxs-lookup"><span data-stu-id="70457-117">Prerequisites</span></span>

<span data-ttu-id="70457-118">若要使用 SQL Server 的分散式快取，請將封裝參考新增至[node.js 套件。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="70457-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="70457-119">若要使用 Redis 分散式快取，請將套件參考新增至 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 套件。</span><span class="sxs-lookup"><span data-stu-id="70457-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="70457-120">若要使用 NCache 分散式快取，請將套件參考新增至 [NCache 開放原始碼](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 套件。</span><span class="sxs-lookup"><span data-stu-id="70457-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="70457-121">>idistributedcache 介面</span><span class="sxs-lookup"><span data-stu-id="70457-121">IDistributedCache interface</span></span>

<span data-ttu-id="70457-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>介面提供下列方法來操作分散式快取執行中的專案：</span><span class="sxs-lookup"><span data-stu-id="70457-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="70457-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字串索引鍵，並以陣列形式抓取快取的專案（ `byte[]` 如果在快取中找到的話）。</span><span class="sxs-lookup"><span data-stu-id="70457-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="70457-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字串索引鍵，將 (為 `byte[]` 陣列) 的專案加入至快取。</span><span class="sxs-lookup"><span data-stu-id="70457-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="70457-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>：根據快取的索引鍵重新整理快取 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> 中的專案，重設其滑動到期時間 (如果有任何) 。</span><span class="sxs-lookup"><span data-stu-id="70457-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="70457-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：會根據其字串索引鍵來移除快取專案。</span><span class="sxs-lookup"><span data-stu-id="70457-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="70457-127">建立分散式快取服務</span><span class="sxs-lookup"><span data-stu-id="70457-127">Establish distributed caching services</span></span>

<span data-ttu-id="70457-128">在中註冊的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 執行 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="70457-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="70457-129">本主題中所述的架構提供的實施包括：</span><span class="sxs-lookup"><span data-stu-id="70457-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="70457-130">分散式記憶體快取</span><span class="sxs-lookup"><span data-stu-id="70457-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="70457-131">分散式 SQL Server 快取</span><span class="sxs-lookup"><span data-stu-id="70457-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="70457-132">分散式 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="70457-133">分散式 NCache 快取</span><span class="sxs-lookup"><span data-stu-id="70457-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="70457-134">分散式記憶體快取</span><span class="sxs-lookup"><span data-stu-id="70457-134">Distributed Memory Cache</span></span>

<span data-ttu-id="70457-135">分散式記憶體快取 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 可將專案儲存在記憶體中的架構所提供的實作為。</span><span class="sxs-lookup"><span data-stu-id="70457-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="70457-136">分散式記憶體快取不是實際的分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="70457-137">快取的專案是由應用程式實例儲存在執行應用程式的伺服器上。</span><span class="sxs-lookup"><span data-stu-id="70457-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="70457-138">分散式記憶體快取是一個實用的實作為：</span><span class="sxs-lookup"><span data-stu-id="70457-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="70457-139">在開發和測試案例中。</span><span class="sxs-lookup"><span data-stu-id="70457-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="70457-140">當單一伺服器用於生產和記憶體耗用量時，不會發生問題。</span><span class="sxs-lookup"><span data-stu-id="70457-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="70457-141">執行分散式記憶體快取會抽象化快取的資料儲存區。</span><span class="sxs-lookup"><span data-stu-id="70457-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="70457-142">如果需要多個節點或容錯功能，它可讓您在未來執行真正的分散式快取解決方案。</span><span class="sxs-lookup"><span data-stu-id="70457-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="70457-143">當應用程式在的開發環境中執行時，範例應用程式會使用分散式記憶體快取 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="70457-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="70457-144">分散式 SQL Server 快取</span><span class="sxs-lookup"><span data-stu-id="70457-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="70457-145">分散式 SQL Server 快取的 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 可讓分散式快取使用 SQL Server 資料庫做為其備份存放區。</span><span class="sxs-lookup"><span data-stu-id="70457-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="70457-146">若要在 SQL Server 實例中建立 SQL Server 快取的專案資料表，您可以使用此 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="70457-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="70457-147">此工具會使用您指定的名稱和架構來建立資料表。</span><span class="sxs-lookup"><span data-stu-id="70457-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="70457-148">藉由執行命令，在 SQL Server 中建立資料表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="70457-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="70457-149">提供 SQL Server 實例 (`Data Source`) 、資料庫 (`Initial Catalog`) 、架構 (例如) `dbo` 和資料表名稱 (例如， `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="70457-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="70457-150">記錄一則訊息，表示工具已成功：</span><span class="sxs-lookup"><span data-stu-id="70457-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="70457-151">此工具所建立的資料表 `sql-cache` 具有下列架構：</span><span class="sxs-lookup"><span data-stu-id="70457-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 快取資料表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="70457-153">應用程式應該使用的實例來操作快取值 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="70457-154">範例應用程式會 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 在的非開發環境中 `Startup.ConfigureServices` 執行：</span><span class="sxs-lookup"><span data-stu-id="70457-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="70457-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (以及（選擇性） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常會儲存在原始檔控制之外 (例如，由[秘密管理員](xref:security/app-secrets)儲存或在 appsettings 的*appsettings.js*儲存 / *。環境}. json*檔案) 。</span><span class="sxs-lookup"><span data-stu-id="70457-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="70457-156">連接字串可能包含應從原始檔控制系統中保留的認證。</span><span class="sxs-lookup"><span data-stu-id="70457-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="70457-157">分散式 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-157">Distributed Redis Cache</span></span>

<span data-ttu-id="70457-158">[Redis](https://redis.io/) 是一種開放原始碼的記憶體內部資料存放區，通常用來做為分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span>  <span data-ttu-id="70457-159">您可以為 Azure 託管的 ASP.NET Core 應用程式設定 [Azure Redis](https://azure.microsoft.com/services/cache/) 快取，並使用 azure Redis 快取來進行本機開發。</span><span class="sxs-lookup"><span data-stu-id="70457-159">You can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app, and use an Azure Redis Cache for local development.</span></span>

<span data-ttu-id="70457-160">應用程式會使用實例 () 來設定快取執行 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 。</span><span class="sxs-lookup"><span data-stu-id="70457-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>).</span></span>

<span data-ttu-id="70457-161">如需詳細資訊，請參閱 [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)。</span><span class="sxs-lookup"><span data-stu-id="70457-161">For more information, see [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span></span>

<span data-ttu-id="70457-162">如需本機 Redis 快取替代方法的討論，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/19542) 。</span><span class="sxs-lookup"><span data-stu-id="70457-162">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/19542) for a discussion on alternative approaches to a local Redis cache.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="70457-163">分散式 NCache 快取</span><span class="sxs-lookup"><span data-stu-id="70457-163">Distributed NCache Cache</span></span>

<span data-ttu-id="70457-164">[NCache](https://github.com/Alachisoft/NCache) 是一種開放原始碼的記憶體內部分散式快取，以原生方式在 .NET 和 .net Core 中開發。</span><span class="sxs-lookup"><span data-stu-id="70457-164">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="70457-165">NCache 可在本機運作，並設定為分散式快取叢集，適用于在 Azure 或其他裝載平臺上執行的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="70457-165">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="70457-166">若要在本機電腦上安裝和設定 NCache，請參閱 [適用于 Windows 的 NCache 消費者入門指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="70457-166">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="70457-167">若要設定 NCache：</span><span class="sxs-lookup"><span data-stu-id="70457-167">To configure NCache:</span></span>

1. <span data-ttu-id="70457-168">安裝 [NCache 開放原始碼 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="70457-168">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="70457-169">在 [ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中設定快取叢集。</span><span class="sxs-lookup"><span data-stu-id="70457-169">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="70457-170">將下列程式碼新增至 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="70457-170">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="70457-171">使用分散式快取</span><span class="sxs-lookup"><span data-stu-id="70457-171">Use the distributed cache</span></span>

<span data-ttu-id="70457-172">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 介面，請 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 從應用程式中的任何函式要求的實例。</span><span class="sxs-lookup"><span data-stu-id="70457-172">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="70457-173">實例是由相依性 [插入 (DI) ](xref:fundamentals/dependency-injection)所提供。</span><span class="sxs-lookup"><span data-stu-id="70457-173">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="70457-174">當範例應用程式啟動時， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 會插入至 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="70457-174">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="70457-175">目前的時間會使用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (來快取。如需詳細資訊，請參閱 [泛型主機： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)) ：</span><span class="sxs-lookup"><span data-stu-id="70457-175">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="70457-176">範例應用程式會插入到中供 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 索引頁面使用。</span><span class="sxs-lookup"><span data-stu-id="70457-176">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="70457-177">每次載入 [索引] 頁面時，都會檢查快取中的快取時間 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="70457-177">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="70457-178">如果快取的時間尚未過期，則會顯示時間。</span><span class="sxs-lookup"><span data-stu-id="70457-178">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="70457-179">如果自上一次存取快取時間以來已經過了20秒 (上次載入此頁面的時間) ，則頁面會顯示快取的 *時間已過期*。</span><span class="sxs-lookup"><span data-stu-id="70457-179">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="70457-180">選取 [ **重設** 快取時間] 按鈕，立即將快取的時間更新為目前時間。</span><span class="sxs-lookup"><span data-stu-id="70457-180">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="70457-181">按鈕會觸發 `OnPostResetCachedTime` 處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="70457-181">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="70457-182"> (至少針對內建的) ，則不需要使用實例的單一或範圍存留期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-182">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="70457-183">您也可以在 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 任何需要的地方建立實例，而不是使用 DI，但是在程式碼中建立實例可能會讓您的程式碼更難測試，並違反 [明確](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)的相依性原則。</span><span class="sxs-lookup"><span data-stu-id="70457-183">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="70457-184">建議</span><span class="sxs-lookup"><span data-stu-id="70457-184">Recommendations</span></span>

<span data-ttu-id="70457-185">當您決定哪一個 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最適合您的應用程式時，請考慮下列事項：</span><span class="sxs-lookup"><span data-stu-id="70457-185">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="70457-186">現有的基礎結構</span><span class="sxs-lookup"><span data-stu-id="70457-186">Existing infrastructure</span></span>
* <span data-ttu-id="70457-187">效能需求</span><span class="sxs-lookup"><span data-stu-id="70457-187">Performance requirements</span></span>
* <span data-ttu-id="70457-188">成本</span><span class="sxs-lookup"><span data-stu-id="70457-188">Cost</span></span>
* <span data-ttu-id="70457-189">小組經驗</span><span class="sxs-lookup"><span data-stu-id="70457-189">Team experience</span></span>

<span data-ttu-id="70457-190">快取解決方案通常依賴記憶體內部儲存體，以快速抓取快取的資料，但記憶體是有限的資源，擴充的成本很高。</span><span class="sxs-lookup"><span data-stu-id="70457-190">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="70457-191">只將常用的資料儲存在快取中。</span><span class="sxs-lookup"><span data-stu-id="70457-191">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="70457-192">一般而言，Redis 快取提供比 SQL Server 快取更高的輸送量和較低的延遲。</span><span class="sxs-lookup"><span data-stu-id="70457-192">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="70457-193">不過，通常需要進行基準測試，以判斷快取策略的效能特性。</span><span class="sxs-lookup"><span data-stu-id="70457-193">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="70457-194">當 SQL Server 當做分散式快取備份存放區使用時，使用相同的資料庫進行快取，而應用程式的一般資料儲存和抓取可能會對這兩者的效能產生負面影響。</span><span class="sxs-lookup"><span data-stu-id="70457-194">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="70457-195">我們建議針對分散式快取備份存放區使用專用的 SQL Server 實例。</span><span class="sxs-lookup"><span data-stu-id="70457-195">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70457-196">其他資源</span><span class="sxs-lookup"><span data-stu-id="70457-196">Additional resources</span></span>

* [<span data-ttu-id="70457-197">Azure 上的 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-197">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="70457-198">Azure 上的 SQL Database</span><span class="sxs-lookup"><span data-stu-id="70457-198">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="70457-199">在[GitHub 上](https://github.com/Alachisoft/NCache)[的 Web 伺服陣列中的 NCache ASP.NET Core >idistributedcache 提供者](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) </span><span class="sxs-lookup"><span data-stu-id="70457-199">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="70457-200">分散式快取是由多個應用程式伺服器所共用的快取，通常會以外部服務的形式維護給存取它的應用程式伺服器。</span><span class="sxs-lookup"><span data-stu-id="70457-200">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="70457-201">分散式快取可以改善 ASP.NET Core 應用程式的效能和擴充性，特別是當應用程式是由雲端服務或伺服器陣列所裝載時。</span><span class="sxs-lookup"><span data-stu-id="70457-201">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="70457-202">分散式快取的優點比其他快取案例更多，其中快取的資料會儲存在個別的應用程式伺服器上。</span><span class="sxs-lookup"><span data-stu-id="70457-202">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="70457-203">當快取的資料散發時，資料會：</span><span class="sxs-lookup"><span data-stu-id="70457-203">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="70457-204"> (一致) 跨多部 *伺服器的要求* 。</span><span class="sxs-lookup"><span data-stu-id="70457-204">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="70457-205">繼續生存伺服器重新開機和應用程式部署。</span><span class="sxs-lookup"><span data-stu-id="70457-205">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="70457-206">不使用本機記憶體。</span><span class="sxs-lookup"><span data-stu-id="70457-206">Doesn't use local memory.</span></span>

<span data-ttu-id="70457-207">分散式快取設定是特定的執行。</span><span class="sxs-lookup"><span data-stu-id="70457-207">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="70457-208">本文說明如何設定 SQL Server 和 Redis 分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-208">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="70457-209">此外也提供協力廠商的執行，例如[GitHub) 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache。</span><span class="sxs-lookup"><span data-stu-id="70457-209">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="70457-210">無論選取哪一個執行，應用程式都會使用介面與快取互動 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-210">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="70457-211">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="70457-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70457-212">先決條件</span><span class="sxs-lookup"><span data-stu-id="70457-212">Prerequisites</span></span>

<span data-ttu-id="70457-213">若要使用 SQL Server 分散式快取，請參考[AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，或將封裝參考加入至[。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="70457-213">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="70457-214">若要使用 Redis 分散式快取，請參考 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ，並將套件參考新增至 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 套件。</span><span class="sxs-lookup"><span data-stu-id="70457-214">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="70457-215">Redis 套件不包含在套件中 `Microsoft.AspNetCore.App` ，因此您必須在專案檔中個別參考 Redis 套件。</span><span class="sxs-lookup"><span data-stu-id="70457-215">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="70457-216">若要使用 NCache 分散式快取，請參考 [AspNetCore 的中繼套件](xref:fundamentals/metapackage-app) ，並將套件參考新增至 [NCache. 開放原始碼](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 封裝。</span><span class="sxs-lookup"><span data-stu-id="70457-216">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="70457-217">NCache 套件不包含在套件中 `Microsoft.AspNetCore.App` ，因此您必須在專案檔中個別參考 NCache 套件。</span><span class="sxs-lookup"><span data-stu-id="70457-217">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="70457-218">>idistributedcache 介面</span><span class="sxs-lookup"><span data-stu-id="70457-218">IDistributedCache interface</span></span>

<span data-ttu-id="70457-219"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>介面提供下列方法來操作分散式快取執行中的專案：</span><span class="sxs-lookup"><span data-stu-id="70457-219">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="70457-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字串索引鍵，並以陣列形式抓取快取的專案（ `byte[]` 如果在快取中找到的話）。</span><span class="sxs-lookup"><span data-stu-id="70457-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="70457-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字串索引鍵，將 (為 `byte[]` 陣列) 的專案加入至快取。</span><span class="sxs-lookup"><span data-stu-id="70457-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="70457-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>：根據快取的索引鍵重新整理快取 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> 中的專案，重設其滑動到期時間 (如果有任何) 。</span><span class="sxs-lookup"><span data-stu-id="70457-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="70457-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：會根據其字串索引鍵來移除快取專案。</span><span class="sxs-lookup"><span data-stu-id="70457-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="70457-224">建立分散式快取服務</span><span class="sxs-lookup"><span data-stu-id="70457-224">Establish distributed caching services</span></span>

<span data-ttu-id="70457-225">在中註冊的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 執行 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="70457-225">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="70457-226">本主題中所述的架構提供的實施包括：</span><span class="sxs-lookup"><span data-stu-id="70457-226">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="70457-227">分散式記憶體快取</span><span class="sxs-lookup"><span data-stu-id="70457-227">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="70457-228">分散式 SQL Server 快取</span><span class="sxs-lookup"><span data-stu-id="70457-228">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="70457-229">分散式 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-229">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="70457-230">分散式 NCache 快取</span><span class="sxs-lookup"><span data-stu-id="70457-230">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="70457-231">分散式記憶體快取</span><span class="sxs-lookup"><span data-stu-id="70457-231">Distributed Memory Cache</span></span>

<span data-ttu-id="70457-232">分散式記憶體快取 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 可將專案儲存在記憶體中的架構所提供的實作為。</span><span class="sxs-lookup"><span data-stu-id="70457-232">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="70457-233">分散式記憶體快取不是實際的分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-233">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="70457-234">快取的專案是由應用程式實例儲存在執行應用程式的伺服器上。</span><span class="sxs-lookup"><span data-stu-id="70457-234">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="70457-235">分散式記憶體快取是一個實用的實作為：</span><span class="sxs-lookup"><span data-stu-id="70457-235">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="70457-236">在開發和測試案例中。</span><span class="sxs-lookup"><span data-stu-id="70457-236">In development and testing scenarios.</span></span>
* <span data-ttu-id="70457-237">當單一伺服器用於生產和記憶體耗用量時，不會發生問題。</span><span class="sxs-lookup"><span data-stu-id="70457-237">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="70457-238">執行分散式記憶體快取會抽象化快取的資料儲存區。</span><span class="sxs-lookup"><span data-stu-id="70457-238">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="70457-239">如果需要多個節點或容錯功能，它可讓您在未來執行真正的分散式快取解決方案。</span><span class="sxs-lookup"><span data-stu-id="70457-239">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="70457-240">當應用程式在的開發環境中執行時，範例應用程式會使用分散式記憶體快取 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="70457-240">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="70457-241">分散式 SQL Server 快取</span><span class="sxs-lookup"><span data-stu-id="70457-241">Distributed SQL Server Cache</span></span>

<span data-ttu-id="70457-242">分散式 SQL Server 快取的 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 可讓分散式快取使用 SQL Server 資料庫做為其備份存放區。</span><span class="sxs-lookup"><span data-stu-id="70457-242">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="70457-243">若要在 SQL Server 實例中建立 SQL Server 快取的專案資料表，您可以使用此 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="70457-243">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="70457-244">此工具會使用您指定的名稱和架構來建立資料表。</span><span class="sxs-lookup"><span data-stu-id="70457-244">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="70457-245">藉由執行命令，在 SQL Server 中建立資料表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="70457-245">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="70457-246">提供 SQL Server 實例 (`Data Source`) 、資料庫 (`Initial Catalog`) 、架構 (例如) `dbo` 和資料表名稱 (例如， `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="70457-246">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="70457-247">記錄一則訊息，表示工具已成功：</span><span class="sxs-lookup"><span data-stu-id="70457-247">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="70457-248">此工具所建立的資料表 `sql-cache` 具有下列架構：</span><span class="sxs-lookup"><span data-stu-id="70457-248">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 快取資料表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="70457-250">應用程式應該使用的實例來操作快取值 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-250">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="70457-251">範例應用程式會 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 在的非開發環境中 `Startup.ConfigureServices` 執行：</span><span class="sxs-lookup"><span data-stu-id="70457-251">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="70457-252"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (以及（選擇性） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常會儲存在原始檔控制之外 (例如，由[秘密管理員](xref:security/app-secrets)儲存或在 appsettings 的*appsettings.js*儲存 / *。環境}. json*檔案) 。</span><span class="sxs-lookup"><span data-stu-id="70457-252">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="70457-253">連接字串可能包含應從原始檔控制系統中保留的認證。</span><span class="sxs-lookup"><span data-stu-id="70457-253">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="70457-254">分散式 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-254">Distributed Redis Cache</span></span>

<span data-ttu-id="70457-255">[Redis](https://redis.io/) 是一種開放原始碼的記憶體內部資料存放區，通常用來做為分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-255">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="70457-256">您可以在本機使用 Redis，也可以為 Azure 託管的 ASP.NET Core 應用程式設定 [Azure Redis](https://azure.microsoft.com/services/cache/) 快取。</span><span class="sxs-lookup"><span data-stu-id="70457-256">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="70457-257">應用程式會在 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 下列情況中，使用 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 非開發環境中的實例 () 來設定快取執行 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="70457-257">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="70457-258">若要在本機電腦上安裝 Redis：</span><span class="sxs-lookup"><span data-stu-id="70457-258">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="70457-259">安裝 [Chocolatey Redis 套件](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="70457-259">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="70457-260">`redis-server`從命令提示字元執行。</span><span class="sxs-lookup"><span data-stu-id="70457-260">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="70457-261">分散式 NCache 快取</span><span class="sxs-lookup"><span data-stu-id="70457-261">Distributed NCache Cache</span></span>

<span data-ttu-id="70457-262">[NCache](https://github.com/Alachisoft/NCache) 是一種開放原始碼的記憶體內部分散式快取，以原生方式在 .NET 和 .net Core 中開發。</span><span class="sxs-lookup"><span data-stu-id="70457-262">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="70457-263">NCache 可在本機運作，並設定為分散式快取叢集，適用于在 Azure 或其他裝載平臺上執行的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="70457-263">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="70457-264">若要在本機電腦上安裝和設定 NCache，請參閱 [適用于 Windows 的 NCache 消費者入門指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="70457-264">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="70457-265">若要設定 NCache：</span><span class="sxs-lookup"><span data-stu-id="70457-265">To configure NCache:</span></span>

1. <span data-ttu-id="70457-266">安裝 [NCache 開放原始碼 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="70457-266">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="70457-267">在 [ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中設定快取叢集。</span><span class="sxs-lookup"><span data-stu-id="70457-267">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="70457-268">將下列程式碼新增至 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="70457-268">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="70457-269">使用分散式快取</span><span class="sxs-lookup"><span data-stu-id="70457-269">Use the distributed cache</span></span>

<span data-ttu-id="70457-270">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 介面，請 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 從應用程式中的任何函式要求的實例。</span><span class="sxs-lookup"><span data-stu-id="70457-270">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="70457-271">實例是由相依性 [插入 (DI) ](xref:fundamentals/dependency-injection)所提供。</span><span class="sxs-lookup"><span data-stu-id="70457-271">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="70457-272">當範例應用程式啟動時， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 會插入至 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="70457-272">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="70457-273">目前的時間會使用 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (來快取。如需詳細資訊，請參閱 [Web 主機： IApplicationLifetime 介面](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) ：</span><span class="sxs-lookup"><span data-stu-id="70457-273">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="70457-274">範例應用程式會插入到中供 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 索引頁面使用。</span><span class="sxs-lookup"><span data-stu-id="70457-274">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="70457-275">每次載入 [索引] 頁面時，都會檢查快取中的快取時間 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="70457-275">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="70457-276">如果快取的時間尚未過期，則會顯示時間。</span><span class="sxs-lookup"><span data-stu-id="70457-276">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="70457-277">如果自上一次存取快取時間以來已經過了20秒 (上次載入此頁面的時間) ，則頁面會顯示快取的 *時間已過期*。</span><span class="sxs-lookup"><span data-stu-id="70457-277">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="70457-278">選取 [ **重設** 快取時間] 按鈕，立即將快取的時間更新為目前時間。</span><span class="sxs-lookup"><span data-stu-id="70457-278">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="70457-279">按鈕會觸發 `OnPostResetCachedTime` 處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="70457-279">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="70457-280"> (至少針對內建的) ，則不需要使用實例的單一或範圍存留期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-280">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="70457-281">您也可以在 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 任何需要的地方建立實例，而不是使用 DI，但是在程式碼中建立實例可能會讓您的程式碼更難測試，並違反 [明確](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)的相依性原則。</span><span class="sxs-lookup"><span data-stu-id="70457-281">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="70457-282">建議</span><span class="sxs-lookup"><span data-stu-id="70457-282">Recommendations</span></span>

<span data-ttu-id="70457-283">當您決定哪一個 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最適合您的應用程式時，請考慮下列事項：</span><span class="sxs-lookup"><span data-stu-id="70457-283">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="70457-284">現有的基礎結構</span><span class="sxs-lookup"><span data-stu-id="70457-284">Existing infrastructure</span></span>
* <span data-ttu-id="70457-285">效能需求</span><span class="sxs-lookup"><span data-stu-id="70457-285">Performance requirements</span></span>
* <span data-ttu-id="70457-286">成本</span><span class="sxs-lookup"><span data-stu-id="70457-286">Cost</span></span>
* <span data-ttu-id="70457-287">小組經驗</span><span class="sxs-lookup"><span data-stu-id="70457-287">Team experience</span></span>

<span data-ttu-id="70457-288">快取解決方案通常依賴記憶體內部儲存體，以快速抓取快取的資料，但記憶體是有限的資源，擴充的成本很高。</span><span class="sxs-lookup"><span data-stu-id="70457-288">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="70457-289">只將常用的資料儲存在快取中。</span><span class="sxs-lookup"><span data-stu-id="70457-289">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="70457-290">一般而言，Redis 快取提供比 SQL Server 快取更高的輸送量和較低的延遲。</span><span class="sxs-lookup"><span data-stu-id="70457-290">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="70457-291">不過，通常需要進行基準測試，以判斷快取策略的效能特性。</span><span class="sxs-lookup"><span data-stu-id="70457-291">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="70457-292">當 SQL Server 當做分散式快取備份存放區使用時，使用相同的資料庫進行快取，而應用程式的一般資料儲存和抓取可能會對這兩者的效能產生負面影響。</span><span class="sxs-lookup"><span data-stu-id="70457-292">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="70457-293">我們建議針對分散式快取備份存放區使用專用的 SQL Server 實例。</span><span class="sxs-lookup"><span data-stu-id="70457-293">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70457-294">其他資源</span><span class="sxs-lookup"><span data-stu-id="70457-294">Additional resources</span></span>

* [<span data-ttu-id="70457-295">Azure 上的 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-295">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="70457-296">Azure 上的 SQL Database</span><span class="sxs-lookup"><span data-stu-id="70457-296">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="70457-297">在[GitHub 上](https://github.com/Alachisoft/NCache)[的 Web 伺服陣列中的 NCache ASP.NET Core >idistributedcache 提供者](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) </span><span class="sxs-lookup"><span data-stu-id="70457-297">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="70457-298">分散式快取是由多個應用程式伺服器所共用的快取，通常會以外部服務的形式維護給存取它的應用程式伺服器。</span><span class="sxs-lookup"><span data-stu-id="70457-298">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="70457-299">分散式快取可以改善 ASP.NET Core 應用程式的效能和擴充性，特別是當應用程式是由雲端服務或伺服器陣列所裝載時。</span><span class="sxs-lookup"><span data-stu-id="70457-299">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="70457-300">分散式快取的優點比其他快取案例更多，其中快取的資料會儲存在個別的應用程式伺服器上。</span><span class="sxs-lookup"><span data-stu-id="70457-300">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="70457-301">當快取的資料散發時，資料會：</span><span class="sxs-lookup"><span data-stu-id="70457-301">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="70457-302"> (一致) 跨多部 *伺服器的要求* 。</span><span class="sxs-lookup"><span data-stu-id="70457-302">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="70457-303">繼續生存伺服器重新開機和應用程式部署。</span><span class="sxs-lookup"><span data-stu-id="70457-303">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="70457-304">不使用本機記憶體。</span><span class="sxs-lookup"><span data-stu-id="70457-304">Doesn't use local memory.</span></span>

<span data-ttu-id="70457-305">分散式快取設定是特定的執行。</span><span class="sxs-lookup"><span data-stu-id="70457-305">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="70457-306">本文說明如何設定 SQL Server 和 Redis 分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-306">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="70457-307">此外也提供協力廠商的執行，例如[GitHub) 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache。</span><span class="sxs-lookup"><span data-stu-id="70457-307">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="70457-308">無論選取哪一個執行，應用程式都會使用介面與快取互動 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-308">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="70457-309">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="70457-309">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70457-310">先決條件</span><span class="sxs-lookup"><span data-stu-id="70457-310">Prerequisites</span></span>

<span data-ttu-id="70457-311">若要使用 SQL Server 分散式快取，請參考[AspNetCore 中繼套件](xref:fundamentals/metapackage-app)，或將封裝參考加入至[。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="70457-311">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="70457-312">若要使用 Redis 分散式快取，請參考 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ，並將套件參考新增至 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 套件。</span><span class="sxs-lookup"><span data-stu-id="70457-312">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="70457-313">Redis 套件不包含在套件中 `Microsoft.AspNetCore.App` ，因此您必須在專案檔中個別參考 Redis 套件。</span><span class="sxs-lookup"><span data-stu-id="70457-313">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="70457-314">若要使用 NCache 分散式快取，請參考 [AspNetCore 的中繼套件](xref:fundamentals/metapackage-app) ，並將套件參考新增至 [NCache. 開放原始碼](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 封裝。</span><span class="sxs-lookup"><span data-stu-id="70457-314">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="70457-315">NCache 套件不包含在套件中 `Microsoft.AspNetCore.App` ，因此您必須在專案檔中個別參考 NCache 套件。</span><span class="sxs-lookup"><span data-stu-id="70457-315">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="70457-316">>idistributedcache 介面</span><span class="sxs-lookup"><span data-stu-id="70457-316">IDistributedCache interface</span></span>

<span data-ttu-id="70457-317"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>介面提供下列方法來操作分散式快取執行中的專案：</span><span class="sxs-lookup"><span data-stu-id="70457-317">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="70457-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字串索引鍵，並以陣列形式抓取快取的專案（ `byte[]` 如果在快取中找到的話）。</span><span class="sxs-lookup"><span data-stu-id="70457-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="70457-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字串索引鍵，將 (為 `byte[]` 陣列) 的專案加入至快取。</span><span class="sxs-lookup"><span data-stu-id="70457-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="70457-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>：根據快取的索引鍵重新整理快取 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> 中的專案，重設其滑動到期時間 (如果有任何) 。</span><span class="sxs-lookup"><span data-stu-id="70457-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="70457-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：會根據其字串索引鍵來移除快取專案。</span><span class="sxs-lookup"><span data-stu-id="70457-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="70457-322">建立分散式快取服務</span><span class="sxs-lookup"><span data-stu-id="70457-322">Establish distributed caching services</span></span>

<span data-ttu-id="70457-323">在中註冊的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 執行 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="70457-323">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="70457-324">本主題中所述的架構提供的實施包括：</span><span class="sxs-lookup"><span data-stu-id="70457-324">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="70457-325">分散式記憶體快取</span><span class="sxs-lookup"><span data-stu-id="70457-325">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="70457-326">分散式 SQL Server 快取</span><span class="sxs-lookup"><span data-stu-id="70457-326">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="70457-327">分散式 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-327">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="70457-328">分散式 NCache 快取</span><span class="sxs-lookup"><span data-stu-id="70457-328">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="70457-329">分散式記憶體快取</span><span class="sxs-lookup"><span data-stu-id="70457-329">Distributed Memory Cache</span></span>

<span data-ttu-id="70457-330">分散式記憶體快取 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 可將專案儲存在記憶體中的架構所提供的實作為。</span><span class="sxs-lookup"><span data-stu-id="70457-330">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="70457-331">分散式記憶體快取不是實際的分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-331">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="70457-332">快取的專案是由應用程式實例儲存在執行應用程式的伺服器上。</span><span class="sxs-lookup"><span data-stu-id="70457-332">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="70457-333">分散式記憶體快取是一個實用的實作為：</span><span class="sxs-lookup"><span data-stu-id="70457-333">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="70457-334">在開發和測試案例中。</span><span class="sxs-lookup"><span data-stu-id="70457-334">In development and testing scenarios.</span></span>
* <span data-ttu-id="70457-335">當單一伺服器用於生產和記憶體耗用量時，不會發生問題。</span><span class="sxs-lookup"><span data-stu-id="70457-335">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="70457-336">執行分散式記憶體快取會抽象化快取的資料儲存區。</span><span class="sxs-lookup"><span data-stu-id="70457-336">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="70457-337">如果需要多個節點或容錯功能，它可讓您在未來執行真正的分散式快取解決方案。</span><span class="sxs-lookup"><span data-stu-id="70457-337">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="70457-338">當應用程式在的開發環境中執行時，範例應用程式會使用分散式記憶體快取 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="70457-338">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="70457-339">分散式 SQL Server 快取</span><span class="sxs-lookup"><span data-stu-id="70457-339">Distributed SQL Server Cache</span></span>

<span data-ttu-id="70457-340">分散式 SQL Server 快取的 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 可讓分散式快取使用 SQL Server 資料庫做為其備份存放區。</span><span class="sxs-lookup"><span data-stu-id="70457-340">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="70457-341">若要在 SQL Server 實例中建立 SQL Server 快取的專案資料表，您可以使用此 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="70457-341">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="70457-342">此工具會使用您指定的名稱和架構來建立資料表。</span><span class="sxs-lookup"><span data-stu-id="70457-342">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="70457-343">藉由執行命令，在 SQL Server 中建立資料表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="70457-343">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="70457-344">提供 SQL Server 實例 (`Data Source`) 、資料庫 (`Initial Catalog`) 、架構 (例如) `dbo` 和資料表名稱 (例如， `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="70457-344">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="70457-345">記錄一則訊息，表示工具已成功：</span><span class="sxs-lookup"><span data-stu-id="70457-345">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="70457-346">此工具所建立的資料表 `sql-cache` 具有下列架構：</span><span class="sxs-lookup"><span data-stu-id="70457-346">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 快取資料表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="70457-348">應用程式應該使用的實例來操作快取值 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-348">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="70457-349">範例應用程式會 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 在的非開發環境中 `Startup.ConfigureServices` 執行：</span><span class="sxs-lookup"><span data-stu-id="70457-349">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="70457-350"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (以及（選擇性） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常會儲存在原始檔控制之外 (例如，由[秘密管理員](xref:security/app-secrets)儲存或在 appsettings 的*appsettings.js*儲存 / *。環境}. json*檔案) 。</span><span class="sxs-lookup"><span data-stu-id="70457-350">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="70457-351">連接字串可能包含應從原始檔控制系統中保留的認證。</span><span class="sxs-lookup"><span data-stu-id="70457-351">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="70457-352">分散式 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-352">Distributed Redis Cache</span></span>

<span data-ttu-id="70457-353">[Redis](https://redis.io/) 是一種開放原始碼的記憶體內部資料存放區，通常用來做為分散式快取。</span><span class="sxs-lookup"><span data-stu-id="70457-353">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="70457-354">您可以在本機使用 Redis，也可以為 Azure 託管的 ASP.NET Core 應用程式設定 [Azure Redis](https://azure.microsoft.com/services/cache/) 快取。</span><span class="sxs-lookup"><span data-stu-id="70457-354">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="70457-355">應用程式會使用實例 () 來設定快取執行 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ：</span><span class="sxs-lookup"><span data-stu-id="70457-355">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="70457-356">若要在本機電腦上安裝 Redis：</span><span class="sxs-lookup"><span data-stu-id="70457-356">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="70457-357">安裝 [Chocolatey Redis 套件](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="70457-357">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="70457-358">`redis-server`從命令提示字元執行。</span><span class="sxs-lookup"><span data-stu-id="70457-358">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="70457-359">分散式 NCache 快取</span><span class="sxs-lookup"><span data-stu-id="70457-359">Distributed NCache Cache</span></span>

<span data-ttu-id="70457-360">[NCache](https://github.com/Alachisoft/NCache) 是一種開放原始碼的記憶體內部分散式快取，以原生方式在 .NET 和 .net Core 中開發。</span><span class="sxs-lookup"><span data-stu-id="70457-360">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="70457-361">NCache 可在本機運作，並設定為分散式快取叢集，適用于在 Azure 或其他裝載平臺上執行的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="70457-361">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="70457-362">若要在本機電腦上安裝和設定 NCache，請參閱 [適用于 Windows 的 NCache 消費者入門指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="70457-362">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="70457-363">若要設定 NCache：</span><span class="sxs-lookup"><span data-stu-id="70457-363">To configure NCache:</span></span>

1. <span data-ttu-id="70457-364">安裝 [NCache 開放原始碼 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="70457-364">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="70457-365">在 [ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中設定快取叢集。</span><span class="sxs-lookup"><span data-stu-id="70457-365">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="70457-366">將下列程式碼新增至 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="70457-366">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="70457-367">使用分散式快取</span><span class="sxs-lookup"><span data-stu-id="70457-367">Use the distributed cache</span></span>

<span data-ttu-id="70457-368">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 介面，請 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 從應用程式中的任何函式要求的實例。</span><span class="sxs-lookup"><span data-stu-id="70457-368">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="70457-369">實例是由相依性 [插入 (DI) ](xref:fundamentals/dependency-injection)所提供。</span><span class="sxs-lookup"><span data-stu-id="70457-369">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="70457-370">當範例應用程式啟動時， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 會插入至 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="70457-370">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="70457-371">目前的時間會使用 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (來快取。如需詳細資訊，請參閱 [Web 主機： IApplicationLifetime 介面](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) ：</span><span class="sxs-lookup"><span data-stu-id="70457-371">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="70457-372">範例應用程式會插入到中供 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 索引頁面使用。</span><span class="sxs-lookup"><span data-stu-id="70457-372">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="70457-373">每次載入 [索引] 頁面時，都會檢查快取中的快取時間 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="70457-373">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="70457-374">如果快取的時間尚未過期，則會顯示時間。</span><span class="sxs-lookup"><span data-stu-id="70457-374">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="70457-375">如果自上一次存取快取時間以來已經過了20秒 (上次載入此頁面的時間) ，則頁面會顯示快取的 *時間已過期*。</span><span class="sxs-lookup"><span data-stu-id="70457-375">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="70457-376">選取 [ **重設** 快取時間] 按鈕，立即將快取的時間更新為目前時間。</span><span class="sxs-lookup"><span data-stu-id="70457-376">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="70457-377">按鈕會觸發 `OnPostResetCachedTime` 處理常式方法。</span><span class="sxs-lookup"><span data-stu-id="70457-377">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="70457-378"> (至少針對內建的) ，則不需要使用實例的單一或範圍存留期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="70457-378">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="70457-379">您也可以在 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 任何需要的地方建立實例，而不是使用 DI，但是在程式碼中建立實例可能會讓您的程式碼更難測試，並違反 [明確](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)的相依性原則。</span><span class="sxs-lookup"><span data-stu-id="70457-379">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="70457-380">建議</span><span class="sxs-lookup"><span data-stu-id="70457-380">Recommendations</span></span>

<span data-ttu-id="70457-381">當您決定哪一個 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最適合您的應用程式時，請考慮下列事項：</span><span class="sxs-lookup"><span data-stu-id="70457-381">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="70457-382">現有的基礎結構</span><span class="sxs-lookup"><span data-stu-id="70457-382">Existing infrastructure</span></span>
* <span data-ttu-id="70457-383">效能需求</span><span class="sxs-lookup"><span data-stu-id="70457-383">Performance requirements</span></span>
* <span data-ttu-id="70457-384">成本</span><span class="sxs-lookup"><span data-stu-id="70457-384">Cost</span></span>
* <span data-ttu-id="70457-385">小組經驗</span><span class="sxs-lookup"><span data-stu-id="70457-385">Team experience</span></span>

<span data-ttu-id="70457-386">快取解決方案通常依賴記憶體內部儲存體，以快速抓取快取的資料，但記憶體是有限的資源，擴充的成本很高。</span><span class="sxs-lookup"><span data-stu-id="70457-386">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="70457-387">只將常用的資料儲存在快取中。</span><span class="sxs-lookup"><span data-stu-id="70457-387">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="70457-388">一般而言，Redis 快取提供比 SQL Server 快取更高的輸送量和較低的延遲。</span><span class="sxs-lookup"><span data-stu-id="70457-388">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="70457-389">不過，通常需要進行基準測試，以判斷快取策略的效能特性。</span><span class="sxs-lookup"><span data-stu-id="70457-389">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="70457-390">當 SQL Server 當做分散式快取備份存放區使用時，使用相同的資料庫進行快取，而應用程式的一般資料儲存和抓取可能會對這兩者的效能產生負面影響。</span><span class="sxs-lookup"><span data-stu-id="70457-390">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="70457-391">我們建議針對分散式快取備份存放區使用專用的 SQL Server 實例。</span><span class="sxs-lookup"><span data-stu-id="70457-391">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70457-392">其他資源</span><span class="sxs-lookup"><span data-stu-id="70457-392">Additional resources</span></span>

* [<span data-ttu-id="70457-393">Azure 上的 Redis 快取</span><span class="sxs-lookup"><span data-stu-id="70457-393">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="70457-394">Azure 上的 SQL Database</span><span class="sxs-lookup"><span data-stu-id="70457-394">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="70457-395">在[GitHub 上](https://github.com/Alachisoft/NCache)[的 Web 伺服陣列中的 NCache ASP.NET Core >idistributedcache 提供者](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) </span><span class="sxs-lookup"><span data-stu-id="70457-395">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
