---
title: 使用 gRPC 的效能最佳作法
author: jamesnk
description: 瞭解建立高效能 gRPC 服務的最佳做法。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: a0a1a6901e07fb0074ca403870378f267d3d4403
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379441"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="0f6fa-103">使用 gRPC 的效能最佳作法</span><span class="sxs-lookup"><span data-stu-id="0f6fa-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="0f6fa-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0f6fa-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0f6fa-105">gRPC 是專為高效能服務所設計。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="0f6fa-106">本檔說明如何從 gRPC 獲得最佳效能。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="0f6fa-107">重複使用 gRPC 通道</span><span class="sxs-lookup"><span data-stu-id="0f6fa-107">Reuse gRPC channels</span></span>

<span data-ttu-id="0f6fa-108">進行 gRPC 呼叫時，應重複使用 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="0f6fa-109">重複使用通道可讓您透過現有的 HTTP/2 連接來對呼叫進行多工處理。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="0f6fa-110">如果為每個 gRPC 呼叫建立新的通道，則完成所需的時間可能會大幅增加。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="0f6fa-111">每次呼叫都需要用戶端與伺服器之間的多個網路往返，才能建立新的 HTTP/2 連線：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="0f6fa-112">開啟通訊端</span><span class="sxs-lookup"><span data-stu-id="0f6fa-112">Opening a socket</span></span>
2. <span data-ttu-id="0f6fa-113">正在建立 TCP 連接</span><span class="sxs-lookup"><span data-stu-id="0f6fa-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="0f6fa-114">協商 TLS</span><span class="sxs-lookup"><span data-stu-id="0f6fa-114">Negotiating TLS</span></span>
4. <span data-ttu-id="0f6fa-115">正在啟動 HTTP/2 連接</span><span class="sxs-lookup"><span data-stu-id="0f6fa-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="0f6fa-116">進行 gRPC 呼叫</span><span class="sxs-lookup"><span data-stu-id="0f6fa-116">Making the gRPC call</span></span>

<span data-ttu-id="0f6fa-117">您可以安全地在 gRPC 呼叫之間共用和重複使用通道：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="0f6fa-118">gRPC 用戶端會使用通道建立。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="0f6fa-119">gRPC 用戶端是輕量物件，不需要快取或重複使用。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="0f6fa-120">您可以從通道建立多個 gRPC 用戶端，包括不同類型的用戶端。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="0f6fa-121">通道和從通道建立的用戶端可以安全地由多個執行緒使用。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="0f6fa-122">從通道建立的用戶端可能會進行多個同時呼叫。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="0f6fa-123">gRPC 用戶端 factory 提供集中的方式來設定通道。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="0f6fa-124">它會自動重複使用基礎通道。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="0f6fa-125">如需詳細資訊，請參閱<xref:grpc/clientfactory>。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="0f6fa-126">並行連接</span><span class="sxs-lookup"><span data-stu-id="0f6fa-126">Connection concurrency</span></span>

<span data-ttu-id="0f6fa-127">HTTP/2 連線通常會限制連接上 (使用中 [HTTP 要求的最大並行資料流程 ](https://http2.github.io/http2-spec/#rfc.section.5.1.2) 數目，) 一次連線。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="0f6fa-128">根據預設，大部分的伺服器會將此限制設定為100的並行資料流程。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="0f6fa-129">GRPC 通道會使用單一 HTTP/2 連線，而且並行呼叫會在該連接上多工。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="0f6fa-130">當使用中的呼叫數目達到連接資料流程限制時，其他呼叫會在用戶端中排入佇列。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="0f6fa-131">排入佇列的呼叫會等待使用中的呼叫完成，然後才傳送。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="0f6fa-132">負載很高或長時間執行的串流 gRPC 呼叫的應用程式，可能會看到因為這項限制而導致呼叫佇列所造成的效能問題。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0f6fa-133">.NET 5 引進了 `SocketsHttpHandler.EnableMultipleHttp2Connections` 屬性。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="0f6fa-134">當設定為時 `true` ，當達到並行資料流程限制時，通道會建立額外的 HTTP/2 連接。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="0f6fa-135">當建立時，系統 `GrpcChannel` 會自動將其內部 `SocketsHttpHandler` 設定為建立額外的 HTTP/2 連接。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="0f6fa-136">如果應用程式設定自己的處理常式，請考慮將設定 `EnableMultipleHttp2Connections` 為 `true` ：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="0f6fa-137">有幾個適用于 .NET Core 3.1 應用程式的因應措施：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="0f6fa-138">針對負載高的應用程式區域建立個別的 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="0f6fa-139">例如， `Logger` gRPC 服務可能會有較高的負載。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="0f6fa-140">使用不同的通道 `LoggerClient` 在應用程式中建立。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="0f6fa-141">例如，使用 gRPC 通道的集區，建立 gRPC 通道的清單。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="0f6fa-142">`Random` 是在每次需要 gRPC 通道時，用來挑選清單中的通道。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="0f6fa-143">使用 `Random` 隨機分配多個連接的呼叫。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0f6fa-144">增加伺服器上的最大並行串流限制是解決此問題的另一種方式。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="0f6fa-145">在 Kestrel 中，這是使用來設定 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> 。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="0f6fa-146">不建議增加最大並行串流限制。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="0f6fa-147">單一 HTTP/2 連接上的資料流程過多會導致新的效能問題：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="0f6fa-148">嘗試寫入連接的資料流程之間的執行緒爭用。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="0f6fa-149">連接封包遺失會導致 TCP 層封鎖所有呼叫。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="0f6fa-150">負載平衡</span><span class="sxs-lookup"><span data-stu-id="0f6fa-150">Load balancing</span></span>

<span data-ttu-id="0f6fa-151">某些負載平衡器不會有效地與 gRPC 搭配運作。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="0f6fa-152">L4 (傳輸) 負載平衡器會在連接層級運作，方法是將 TCP 連線分散到各個端點。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="0f6fa-153">這種方法很適合用來載入使用 HTTP/1.1 進行的平衡 API 呼叫。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="0f6fa-154">使用 HTTP/1.1 進行的並行呼叫會在不同的連線上傳送，讓呼叫能夠跨端點進行負載平衡。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="0f6fa-155">因為 L4 負載平衡器會在連接層級運作，所以它們無法與 gRPC 搭配運作。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="0f6fa-156">gRPC 使用 HTTP/2，它會在單一 TCP 連接上將分離信號多個呼叫。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="0f6fa-157">所有透過該連接的 gRPC 呼叫都會移至一個端點。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="0f6fa-158">有兩個選項可有效 gRPC 負載平衡：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="0f6fa-159">用戶端負載平衡</span><span class="sxs-lookup"><span data-stu-id="0f6fa-159">Client-side load balancing</span></span>
* <span data-ttu-id="0f6fa-160">L7 (應用程式) proxy 負載平衡</span><span class="sxs-lookup"><span data-stu-id="0f6fa-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="0f6fa-161">只有 gRPC 呼叫可以在端點之間進行負載平衡。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="0f6fa-162">一旦建立串流 gRPC 呼叫，透過資料流程傳送的所有訊息都會移至一個端點。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="0f6fa-163">用戶端負載平衡</span><span class="sxs-lookup"><span data-stu-id="0f6fa-163">Client-side load balancing</span></span>

<span data-ttu-id="0f6fa-164">使用用戶端負載平衡時，用戶端會知道端點。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="0f6fa-165">針對每個 gRPC 呼叫，它會選取不同的端點來傳送呼叫。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="0f6fa-166">當延遲很重要時，用戶端負載平衡是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="0f6fa-167">用戶端與服務之間沒有 proxy，所以會直接將呼叫傳送至服務。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="0f6fa-168">用戶端負載平衡的缺點在於，每個用戶端都必須追蹤應使用的可用端點。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="0f6fa-169">對應用戶端負載平衡是一種技術，可將負載平衡狀態儲存在中央位置。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="0f6fa-170">用戶端會定期查詢中央位置，以取得負載平衡決策時要使用的資訊。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="0f6fa-171">`Grpc.Net.Client` 目前不支援用戶端負載平衡。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="0f6fa-172">如果 .NET 需要用戶端負載平衡，則 Grpc 是不錯的選擇[。](https://www.nuget.org/packages/Grpc.Core)</span><span class="sxs-lookup"><span data-stu-id="0f6fa-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="0f6fa-173">Proxy 負載平衡</span><span class="sxs-lookup"><span data-stu-id="0f6fa-173">Proxy load balancing</span></span>

<span data-ttu-id="0f6fa-174">L7 (應用程式) proxy 的運作層級高於 L4 (傳輸) proxy。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="0f6fa-175">L7 proxy 瞭解 HTTP/2，而且能夠將 gRPC 的呼叫分散至多個端點的一個 HTTP/2 連接上的 proxy。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="0f6fa-176">使用 proxy 比用戶端負載平衡更為簡單，但可能會增加額外的延遲給 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="0f6fa-177">有許多 L7 proxy 可用。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-177">There are many L7 proxies available.</span></span> <span data-ttu-id="0f6fa-178">部分選項如下：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-178">Some options are:</span></span>

* <span data-ttu-id="0f6fa-179">[Envoy](https://www.envoyproxy.io/) -常用的開放原始碼 proxy。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="0f6fa-180">適用于 Kubernetes 的[Linkerd](https://linkerd.io/)服務網格。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="0f6fa-181">[YARP：反向 Proxy](https://microsoft.github.io/reverse-proxy/) -以 .Net 撰寫的預覽開放原始碼 Proxy。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="0f6fa-182">保持作用中的 ping</span><span class="sxs-lookup"><span data-stu-id="0f6fa-182">Keep alive pings</span></span>

<span data-ttu-id="0f6fa-183">保持作用中的 ping 可用來讓 HTTP/2 連接在非使用狀態期間保持運作。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-183">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="0f6fa-184">當應用程式繼續活動時，擁有現有的 HTTP/2 連線可讓您快速進行初始 gRPC 呼叫，而不會因為重新建立連接而造成延遲。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-184">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="0f6fa-185">保持作用中 ping 的設定 <xref:System.Net.Http.SocketsHttpHandler> ：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-185">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="0f6fa-186">上述程式碼會設定通道，在沒有活動的期間，每隔60秒將「保持運作」 ping 傳送至伺服器。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-186">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="0f6fa-187">Ping 可確保伺服器和使用中的任何 proxy 都不會因為無活動而關閉連接。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-187">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="0f6fa-188">資料流</span><span class="sxs-lookup"><span data-stu-id="0f6fa-188">Streaming</span></span>

<span data-ttu-id="0f6fa-189">gRPC 雙向串流可以用來取代高效能案例中的一元 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-189">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="0f6fa-190">一旦啟動雙向串流之後，來回串流訊息的速度會比傳送具有多個一元 gRPC 呼叫的訊息更快。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-190">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="0f6fa-191">經過資料流程處理的訊息會以現有 HTTP/2 要求的資料形式傳送，並消除為每個一元呼叫建立新 HTTP/2 要求的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-191">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="0f6fa-192">範例服務：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-192">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="0f6fa-193">範例用戶端：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-193">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="0f6fa-194">基於效能考慮，使用雙向串流取代一元呼叫是先進的技巧，而且在許多情況下都不適用。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-194">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="0f6fa-195">在下列情況中，使用串流呼叫是不錯的選擇：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-195">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="0f6fa-196">需要高輸送量或低延遲。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-196">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="0f6fa-197">gRPC 和 HTTP/2 會識別為效能瓶頸。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-197">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="0f6fa-198">用戶端中的工作者正在傳送或接收具有 gRPC 服務的一般訊息。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-198">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="0f6fa-199">請留意使用串流呼叫而非一元的額外複雜性和限制：</span><span class="sxs-lookup"><span data-stu-id="0f6fa-199">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="0f6fa-200">資料流程可能會因服務或連接錯誤而中斷。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-200">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="0f6fa-201">如果發生錯誤，則需要邏輯才能重新開機資料流程。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-201">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="0f6fa-202">`RequestStream.WriteAsync` 對多重執行緒而言不安全。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-202">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="0f6fa-203">一次只能將一個訊息寫入資料流程。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-203">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="0f6fa-204">從多個執行緒透過單一資料流程傳送訊息時，需要生產者/取用者佇列 <xref:System.Threading.Channels.Channel%601> ，例如整理訊息。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-204">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="0f6fa-205">GRPC 串流方法僅限於接收一種訊息類型，以及傳送一種訊息類型。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-205">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="0f6fa-206">例如， `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` 接收 `RequestMessage` 和傳送 `ResponseMessage` 。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-206">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="0f6fa-207">Protobuf 支援使用和的未知或條件式訊息 `Any` ， `oneof` 可解決這項限制。</span><span class="sxs-lookup"><span data-stu-id="0f6fa-207">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
