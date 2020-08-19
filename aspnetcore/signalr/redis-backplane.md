---
title: ASP.NET Core 相應放大的 Redis 背板 SignalR
author: bradygaster
description: 瞭解如何設定 Redis 背板，以針對 ASP.NET Core 應用程式啟用相應放大 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/redis-backplane
ms.openlocfilehash: bc28eb3096e88455347f68ca381c9a280d5a153e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633652"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a><span data-ttu-id="e784a-103">設定 Redis 背板來 ASP.NET Core SignalR 向外延展</span><span class="sxs-lookup"><span data-stu-id="e784a-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="e784a-104">[Andrew Stanton-護士](https://twitter.com/anurse)、 [Brady Gaster](https://twitter.com/bradygaster)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="e784a-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="e784a-105">本文說明 SignalR 設定 [Redis](https://redis.io/) 伺服器以用於相應放大 ASP.NET Core 應用程式的特定層面 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="e784a-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="e784a-106">設定 Redis 背板</span><span class="sxs-lookup"><span data-stu-id="e784a-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="e784a-107">部署 Redis 伺服器。</span><span class="sxs-lookup"><span data-stu-id="e784a-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="e784a-108">在生產環境中，建議只有在與應用程式在相同的資料中心執行時，才建議使用 Redis 背板 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="e784a-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="e784a-109">否則，網路延遲會降低效能。</span><span class="sxs-lookup"><span data-stu-id="e784a-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="e784a-110">如果您 SignalR 的應用程式是在 azure 雲端中執行，我們建議 SignalR 使用 azure 服務，而不是 Redis 背板。</span><span class="sxs-lookup"><span data-stu-id="e784a-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="e784a-111">您可以針對開發和測試環境使用 Azure Redis 快取服務。</span><span class="sxs-lookup"><span data-stu-id="e784a-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="e784a-112">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="e784a-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="e784a-113">Redis 文件</span><span class="sxs-lookup"><span data-stu-id="e784a-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="e784a-114">Azure Redis 快取文件</span><span class="sxs-lookup"><span data-stu-id="e784a-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="e784a-115">在 SignalR 應用程式中，安裝 `Microsoft.AspNetCore.SignalR.Redis` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="e784a-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="e784a-116">在 `Startup.ConfigureServices` 方法中，在下列情況下呼叫 `AddRedis` `AddSignalR` ：</span><span class="sxs-lookup"><span data-stu-id="e784a-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="e784a-117">視需要設定選項：</span><span class="sxs-lookup"><span data-stu-id="e784a-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="e784a-118">大部分的選項都可以在連接字串或 [>configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 物件中設定。</span><span class="sxs-lookup"><span data-stu-id="e784a-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="e784a-119">中指定的選項會覆 `ConfigurationOptions` 寫連接字串中所設定的選項。</span><span class="sxs-lookup"><span data-stu-id="e784a-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="e784a-120">下列範例顯示如何設定物件中的選項 `ConfigurationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="e784a-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="e784a-121">此範例會新增通道前置詞，讓多個應用程式可以共用相同的 Redis 實例，如下列步驟所述。</span><span class="sxs-lookup"><span data-stu-id="e784a-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="e784a-122">在上述程式碼中， `options.Configuration` 會使用連接字串中所指定的內容來初始化。</span><span class="sxs-lookup"><span data-stu-id="e784a-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="e784a-123">在 SignalR 應用程式中，安裝下列其中一個 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="e784a-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="e784a-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` -相依于 >stackexchange.redis. Redis 2. X.X。</span><span class="sxs-lookup"><span data-stu-id="e784a-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="e784a-125">這是 ASP.NET Core 2.2 和更新版本的建議套件。</span><span class="sxs-lookup"><span data-stu-id="e784a-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="e784a-126">`Microsoft.AspNetCore.SignalR.Redis` -相依于 >stackexchange.redis. Redis 1. X.X。</span><span class="sxs-lookup"><span data-stu-id="e784a-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="e784a-127">此套件不包含在 ASP.NET Core 3.0 和更新版本中。</span><span class="sxs-lookup"><span data-stu-id="e784a-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="e784a-128">在 `Startup.ConfigureServices` 方法中，呼叫 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> ：</span><span class="sxs-lookup"><span data-stu-id="e784a-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="e784a-129">使用時 `Microsoft.AspNetCore.SignalR.Redis` ，請呼叫 <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> 。</span><span class="sxs-lookup"><span data-stu-id="e784a-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="e784a-130">視需要設定選項：</span><span class="sxs-lookup"><span data-stu-id="e784a-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="e784a-131">大部分的選項都可以在連接字串或 [>configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 物件中設定。</span><span class="sxs-lookup"><span data-stu-id="e784a-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="e784a-132">中指定的選項會覆 `ConfigurationOptions` 寫連接字串中所設定的選項。</span><span class="sxs-lookup"><span data-stu-id="e784a-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="e784a-133">下列範例顯示如何設定物件中的選項 `ConfigurationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="e784a-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="e784a-134">此範例會新增通道前置詞，讓多個應用程式可以共用相同的 Redis 實例，如下列步驟所述。</span><span class="sxs-lookup"><span data-stu-id="e784a-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="e784a-135">使用時 `Microsoft.AspNetCore.SignalR.Redis` ，請呼叫 <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> 。</span><span class="sxs-lookup"><span data-stu-id="e784a-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="e784a-136">在上述程式碼中， `options.Configuration` 會使用連接字串中所指定的內容來初始化。</span><span class="sxs-lookup"><span data-stu-id="e784a-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="e784a-137">如需 Redis 選項的詳細資訊，請參閱 [>stackexchange.redis Redis 檔](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)。</span><span class="sxs-lookup"><span data-stu-id="e784a-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="e784a-138">在 SignalR 應用程式中，安裝下列 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="e784a-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="e784a-139">在 `Startup.ConfigureServices` 方法中，呼叫 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> ：</span><span class="sxs-lookup"><span data-stu-id="e784a-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="e784a-140">視需要設定選項：</span><span class="sxs-lookup"><span data-stu-id="e784a-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="e784a-141">大部分的選項都可以在連接字串或 [>configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 物件中設定。</span><span class="sxs-lookup"><span data-stu-id="e784a-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="e784a-142">中指定的選項會覆 `ConfigurationOptions` 寫連接字串中所設定的選項。</span><span class="sxs-lookup"><span data-stu-id="e784a-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="e784a-143">下列範例顯示如何設定物件中的選項 `ConfigurationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="e784a-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="e784a-144">此範例會新增通道前置詞，讓多個應用程式可以共用相同的 Redis 實例，如下列步驟所述。</span><span class="sxs-lookup"><span data-stu-id="e784a-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="e784a-145">在上述程式碼中， `options.Configuration` 會使用連接字串中所指定的內容來初始化。</span><span class="sxs-lookup"><span data-stu-id="e784a-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="e784a-146">如需 Redis 選項的詳細資訊，請參閱 [>stackexchange.redis Redis 檔](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)。</span><span class="sxs-lookup"><span data-stu-id="e784a-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="e784a-147">如果您針對多個應用程式使用一個 Redis 伺服器 SignalR ，請針對每個應用程式使用不同的通道前置詞 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="e784a-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="e784a-148">設定通道首碼會隔離一個 SignalR 應用程式與其他使用不同通道首碼的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e784a-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="e784a-149">如果您未指派不同的前置詞，則從某個應用程式傳送到其所有用戶端的訊息將會移至所有使用 Redis 伺服器做為背板的應用程式的所有用戶端。</span><span class="sxs-lookup"><span data-stu-id="e784a-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="e784a-150">設定您的伺服器陣列負載平衡軟體以進行粘滯話。</span><span class="sxs-lookup"><span data-stu-id="e784a-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="e784a-151">以下是一些有關如何執行此動作的檔範例：</span><span class="sxs-lookup"><span data-stu-id="e784a-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="e784a-152">IIS</span><span class="sxs-lookup"><span data-stu-id="e784a-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="e784a-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="e784a-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="e784a-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="e784a-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="e784a-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="e784a-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="e784a-156">Redis 伺服器錯誤</span><span class="sxs-lookup"><span data-stu-id="e784a-156">Redis server errors</span></span>

<span data-ttu-id="e784a-157">當 Redis 伺服器關閉時，會擲回 SignalR 指出不會傳遞訊息的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e784a-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="e784a-158">一些一般例外狀況訊息：</span><span class="sxs-lookup"><span data-stu-id="e784a-158">Some typical exception messages:</span></span>

* <span data-ttu-id="e784a-159">*無法寫入訊息*</span><span class="sxs-lookup"><span data-stu-id="e784a-159">*Failed writing message*</span></span>
* <span data-ttu-id="e784a-160">*無法叫用中樞方法 ' 方法 '*</span><span class="sxs-lookup"><span data-stu-id="e784a-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="e784a-161">*連接至 Redis 失敗*</span><span class="sxs-lookup"><span data-stu-id="e784a-161">*Connection to Redis failed*</span></span>

<span data-ttu-id="e784a-162">SignalR 當伺服器恢復運作時，不會緩衝處理訊息來傳送訊息。</span><span class="sxs-lookup"><span data-stu-id="e784a-162">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="e784a-163">Redis 伺服器關閉時傳送的任何訊息都會遺失。</span><span class="sxs-lookup"><span data-stu-id="e784a-163">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="e784a-164">SignalR 當 Redis 伺服器再次可用時，會自動重新連接。</span><span class="sxs-lookup"><span data-stu-id="e784a-164">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="e784a-165">連接失敗的自訂行為</span><span class="sxs-lookup"><span data-stu-id="e784a-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="e784a-166">以下範例顯示如何處理 Redis 連接失敗事件。</span><span class="sxs-lookup"><span data-stu-id="e784a-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="e784a-167">Redis 群集</span><span class="sxs-lookup"><span data-stu-id="e784a-167">Redis Clustering</span></span>

<span data-ttu-id="e784a-168">[Redis](https://redis.io/topics/cluster-spec) 叢集是使用多部 Redis 伺服器達到高可用性的方法。</span><span class="sxs-lookup"><span data-stu-id="e784a-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="e784a-169">叢集未正式支援，但可能會有作用。</span><span class="sxs-lookup"><span data-stu-id="e784a-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e784a-170">後續步驟</span><span class="sxs-lookup"><span data-stu-id="e784a-170">Next steps</span></span>

<span data-ttu-id="e784a-171">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="e784a-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="e784a-172">Redis 文件</span><span class="sxs-lookup"><span data-stu-id="e784a-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="e784a-173">>stackexchange.redis Redis 檔</span><span class="sxs-lookup"><span data-stu-id="e784a-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="e784a-174">Azure Redis 快取文件</span><span class="sxs-lookup"><span data-stu-id="e784a-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
