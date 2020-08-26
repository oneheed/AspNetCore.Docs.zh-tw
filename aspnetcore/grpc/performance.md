---
title: 效能最佳做法
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
ms.openlocfilehash: c6f6a9e5c9aa2f01209c8457a848dc6ec1f5ed88
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88866184"
---
# <a name="performance-best-practices"></a><span data-ttu-id="c6214-103">效能最佳做法</span><span class="sxs-lookup"><span data-stu-id="c6214-103">Performance best practices</span></span>

<span data-ttu-id="c6214-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c6214-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c6214-105">gRPC 是專為高效能服務所設計。</span><span class="sxs-lookup"><span data-stu-id="c6214-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="c6214-106">本檔說明如何從 gRPC 獲得最佳效能。</span><span class="sxs-lookup"><span data-stu-id="c6214-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="c6214-107">重複使用頻道</span><span class="sxs-lookup"><span data-stu-id="c6214-107">Reuse channel</span></span>

<span data-ttu-id="c6214-108">進行 gRPC 呼叫時，應重複使用 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="c6214-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="c6214-109">重複使用通道可讓您透過現有的 HTTP/2 連接來對呼叫進行多工處理。</span><span class="sxs-lookup"><span data-stu-id="c6214-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="c6214-110">如果為每個 gRPC 呼叫建立新的通道，則完成所需的時間可能會大幅增加。</span><span class="sxs-lookup"><span data-stu-id="c6214-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="c6214-111">每次呼叫都需要用戶端與伺服器之間的多個網路來回行程，才能建立 HTTP/2 連線：</span><span class="sxs-lookup"><span data-stu-id="c6214-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="c6214-112">開啟通訊端</span><span class="sxs-lookup"><span data-stu-id="c6214-112">Opening a socket</span></span>
2. <span data-ttu-id="c6214-113">正在建立 TCP 連接</span><span class="sxs-lookup"><span data-stu-id="c6214-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="c6214-114">協商 TLS</span><span class="sxs-lookup"><span data-stu-id="c6214-114">Negotiating TLS</span></span>
4. <span data-ttu-id="c6214-115">正在啟動 HTTP/2 連接</span><span class="sxs-lookup"><span data-stu-id="c6214-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="c6214-116">進行 gRPC 呼叫</span><span class="sxs-lookup"><span data-stu-id="c6214-116">Making the gRPC call</span></span>

<span data-ttu-id="c6214-117">您可以安全地在 gRPC 呼叫之間共用和重複使用通道：</span><span class="sxs-lookup"><span data-stu-id="c6214-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="c6214-118">gRPC 用戶端會使用通道建立。</span><span class="sxs-lookup"><span data-stu-id="c6214-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="c6214-119">gRPC 用戶端是輕量物件，不需要快取或重複使用。</span><span class="sxs-lookup"><span data-stu-id="c6214-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="c6214-120">您可以從通道建立多個 gRPC 用戶端，包括不同類型的用戶端。</span><span class="sxs-lookup"><span data-stu-id="c6214-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="c6214-121">通道和從通道建立的用戶端可以安全地由多個執行緒使用。</span><span class="sxs-lookup"><span data-stu-id="c6214-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="c6214-122">從通道建立的用戶端可能會進行多個同時呼叫。</span><span class="sxs-lookup"><span data-stu-id="c6214-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="c6214-123">並行連接</span><span class="sxs-lookup"><span data-stu-id="c6214-123">Connection concurrency</span></span>

<span data-ttu-id="c6214-124">HTTP/2 連線通常會限制連接上 (使用中 [HTTP 要求的最大並行資料流程 ](https://http2.github.io/http2-spec/#rfc.section.5.1.2) 數目，) 一次連線。</span><span class="sxs-lookup"><span data-stu-id="c6214-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="c6214-125">根據預設，大部分的伺服器會將此限制設定為100的並行資料流程。</span><span class="sxs-lookup"><span data-stu-id="c6214-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="c6214-126">GRPC 通道會使用單一 HTTP/2 連線，而且並行呼叫會在該連接上多工。</span><span class="sxs-lookup"><span data-stu-id="c6214-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="c6214-127">當使用中的呼叫數目達到連接資料流程限制時，其他呼叫會在用戶端中排入佇列。</span><span class="sxs-lookup"><span data-stu-id="c6214-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="c6214-128">排入佇列的呼叫會等待使用中的呼叫完成，然後才傳送。</span><span class="sxs-lookup"><span data-stu-id="c6214-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="c6214-129">負載很高或長時間執行的串流 gRPC 呼叫的應用程式，可能會看到因為這項限制而導致呼叫佇列所造成的效能問題。</span><span class="sxs-lookup"><span data-stu-id="c6214-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c6214-130">.NET 5 引進了 `SocketsHttpHandler.EnableMultipleHttp2Connections` 屬性。</span><span class="sxs-lookup"><span data-stu-id="c6214-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="c6214-131">當設定為時 `true` ，當達到並行資料流程限制時，通道會建立額外的 HTTP/2 連接。</span><span class="sxs-lookup"><span data-stu-id="c6214-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="c6214-132">當建立時，系統 `GrpcChannel` 會自動將其內部 `SocketsHttpHandler` 設定為建立額外的 HTTP/2 連接。</span><span class="sxs-lookup"><span data-stu-id="c6214-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="c6214-133">如果應用程式設定自己的處理常式，請考慮將設定 `EnableMultipleHttp2Connections` 為 `true` ：</span><span class="sxs-lookup"><span data-stu-id="c6214-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="c6214-134">有幾個適用于 .NET Core 3.1 應用程式的因應措施：</span><span class="sxs-lookup"><span data-stu-id="c6214-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="c6214-135">針對負載高的應用程式區域建立個別的 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="c6214-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="c6214-136">例如， `Logger` gRPC 服務可能會有較高的負載。</span><span class="sxs-lookup"><span data-stu-id="c6214-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="c6214-137">使用不同的通道 `LoggerClient` 在應用程式中建立。</span><span class="sxs-lookup"><span data-stu-id="c6214-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="c6214-138">例如，使用 gRPC 通道的集區，建立 gRPC 通道的清單。</span><span class="sxs-lookup"><span data-stu-id="c6214-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="c6214-139">`Random` 是在每次需要 gRPC 通道時，用來挑選清單中的通道。</span><span class="sxs-lookup"><span data-stu-id="c6214-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="c6214-140">使用 `Random` 隨機分配多個連接的呼叫。</span><span class="sxs-lookup"><span data-stu-id="c6214-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c6214-141">增加伺服器上的最大並行串流限制是解決此問題的另一種方式。</span><span class="sxs-lookup"><span data-stu-id="c6214-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="c6214-142">在 Kestrel 中，這是使用來設定 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> 。</span><span class="sxs-lookup"><span data-stu-id="c6214-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="c6214-143">不建議增加最大並行串流限制。</span><span class="sxs-lookup"><span data-stu-id="c6214-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="c6214-144">單一 HTTP/2 連接上的資料流程過多會導致新的效能問題：</span><span class="sxs-lookup"><span data-stu-id="c6214-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="c6214-145">嘗試寫入連接的資料流程之間的執行緒爭用。</span><span class="sxs-lookup"><span data-stu-id="c6214-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="c6214-146">連接封包遺失會導致 TCP 層封鎖所有呼叫。</span><span class="sxs-lookup"><span data-stu-id="c6214-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="c6214-147">保持作用中的 ping</span><span class="sxs-lookup"><span data-stu-id="c6214-147">Keep alive pings</span></span>

<span data-ttu-id="c6214-148">保持作用中的 ping 可用來讓 HTTP/2 連接在非使用狀態期間保持運作。</span><span class="sxs-lookup"><span data-stu-id="c6214-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="c6214-149">當應用程式繼續活動時，擁有現有的 HTTP/2 連線可讓您快速進行初始 gRPC 呼叫，而不會因為重新建立連接而造成延遲。</span><span class="sxs-lookup"><span data-stu-id="c6214-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="c6214-150">保持作用中 ping 的設定 <xref:System.Net.Http.SocketsHttpHandler> ：</span><span class="sxs-lookup"><span data-stu-id="c6214-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="c6214-151">上述程式碼會設定通道，在沒有活動的期間，每隔60秒將「保持運作」 ping 傳送至伺服器。</span><span class="sxs-lookup"><span data-stu-id="c6214-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="c6214-152">Ping 可確保伺服器和使用中的任何 proxy 都不會因為無活動而關閉連接。</span><span class="sxs-lookup"><span data-stu-id="c6214-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="c6214-153">串流</span><span class="sxs-lookup"><span data-stu-id="c6214-153">Streaming</span></span>

<span data-ttu-id="c6214-154">gRPC 雙向串流可以用來取代高效能案例中的一元 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="c6214-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="c6214-155">一旦啟動雙向串流之後，來回串流訊息的速度會比傳送具有多個一元 gRPC 呼叫的訊息更快。</span><span class="sxs-lookup"><span data-stu-id="c6214-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="c6214-156">經過資料流程處理的訊息會以現有 HTTP/2 要求的資料形式傳送，並消除為每個一元呼叫建立新 HTTP/2 要求的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="c6214-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="c6214-157">範例服務：</span><span class="sxs-lookup"><span data-stu-id="c6214-157">Example service:</span></span>

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

<span data-ttu-id="c6214-158">範例用戶端：</span><span class="sxs-lookup"><span data-stu-id="c6214-158">Example client:</span></span>

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

<span data-ttu-id="c6214-159">基於效能考慮，使用雙向串流取代一元呼叫是先進的技巧，而且在許多情況下都不適用。</span><span class="sxs-lookup"><span data-stu-id="c6214-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="c6214-160">在下列情況中，使用串流呼叫是不錯的選擇：</span><span class="sxs-lookup"><span data-stu-id="c6214-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="c6214-161">需要高輸送量或低延遲。</span><span class="sxs-lookup"><span data-stu-id="c6214-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="c6214-162">gRPC 和 HTTP/2 會識別為效能瓶頸。</span><span class="sxs-lookup"><span data-stu-id="c6214-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="c6214-163">用戶端中的工作者正在傳送或接收具有 gRPC 服務的一般訊息。</span><span class="sxs-lookup"><span data-stu-id="c6214-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="c6214-164">請留意使用串流呼叫而非一元的額外複雜性和限制：</span><span class="sxs-lookup"><span data-stu-id="c6214-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="c6214-165">資料流程可能會因服務或連接錯誤而中斷。</span><span class="sxs-lookup"><span data-stu-id="c6214-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="c6214-166">如果發生錯誤，則需要邏輯才能重新開機資料流程。</span><span class="sxs-lookup"><span data-stu-id="c6214-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="c6214-167">`RequestStream.WriteAsync` 對多重執行緒而言不安全。</span><span class="sxs-lookup"><span data-stu-id="c6214-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="c6214-168">一次只能將一個訊息寫入資料流程。</span><span class="sxs-lookup"><span data-stu-id="c6214-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="c6214-169">從多個執行緒透過單一資料流程傳送訊息時，需要生產者/取用者佇列 <xref:System.Threading.Channels.Channel%601> ，例如整理訊息。</span><span class="sxs-lookup"><span data-stu-id="c6214-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="c6214-170">GRPC 串流方法僅限於接收一種訊息類型，以及傳送一種訊息類型。</span><span class="sxs-lookup"><span data-stu-id="c6214-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="c6214-171">例如， `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` 接收 `RequestMessage` 和傳送 `ResponseMessage` 。</span><span class="sxs-lookup"><span data-stu-id="c6214-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="c6214-172">Protobuf 支援使用和的未知或條件式訊息 `Any` ， `oneof` 可解決這項限制。</span><span class="sxs-lookup"><span data-stu-id="c6214-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
