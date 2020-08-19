---
title: 利用 .NET 用戶端呼叫 gRPC 服務
author: jamesnk
description: 瞭解如何使用 .NET gRPC 用戶端呼叫 gRPC 服務。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 28e4f372e301a673644bfa97763ebc930f2d0ad5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634328"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="1267e-103">利用 .NET 用戶端呼叫 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="1267e-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="1267e-104">.NET gRPC 用戶端程式庫可在 [gRPC .net. 用戶端](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 套件中取得。</span><span class="sxs-lookup"><span data-stu-id="1267e-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="1267e-105">本檔說明如何：</span><span class="sxs-lookup"><span data-stu-id="1267e-105">This document explains how to:</span></span>

* <span data-ttu-id="1267e-106">設定 gRPC 用戶端以呼叫 gRPC services。</span><span class="sxs-lookup"><span data-stu-id="1267e-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="1267e-107">對一元、伺服器串流、用戶端串流和雙向串流方法進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="1267e-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="1267e-108">設定 gRPC 用戶端</span><span class="sxs-lookup"><span data-stu-id="1267e-108">Configure gRPC client</span></span>

<span data-ttu-id="1267e-109">gRPC 用戶端是[從\* \* proto\*檔案產生](xref:grpc/basics#generated-c-assets)的具體用戶端類型。</span><span class="sxs-lookup"><span data-stu-id="1267e-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="1267e-110">具體 gRPC 用戶端的方法會轉譯為\* \* proto\*檔案中的 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="1267e-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="1267e-111">GRPC 用戶端是從通道建立。</span><span class="sxs-lookup"><span data-stu-id="1267e-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="1267e-112">首先，使用 `GrpcChannel.ForAddress` 建立通道，然後使用通道來建立 gRPC 用戶端：</span><span class="sxs-lookup"><span data-stu-id="1267e-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="1267e-113">通道代表 gRPC 服務的長時間連接。</span><span class="sxs-lookup"><span data-stu-id="1267e-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="1267e-114">建立通道時，會使用與呼叫服務相關的選項進行設定。</span><span class="sxs-lookup"><span data-stu-id="1267e-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="1267e-115">例如， `HttpClient` 用來進行呼叫、最大傳送和接收訊息大小，以及記錄可以在上指定 `GrpcChannelOptions` 和使用 `GrpcChannel.ForAddress` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="1267e-116">如需完整的選項清單，請參閱 [用戶端設定選項](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="1267e-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="1267e-117">設定 TLS</span><span class="sxs-lookup"><span data-stu-id="1267e-117">Configure TLS</span></span>

<span data-ttu-id="1267e-118">GRPC 用戶端必須使用與所呼叫服務相同的連接層級安全性。</span><span class="sxs-lookup"><span data-stu-id="1267e-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="1267e-119">建立 gRPC 通道時，會設定 (TLS) 的 gRPC 用戶端傳輸層安全性。</span><span class="sxs-lookup"><span data-stu-id="1267e-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="1267e-120">GRPC 用戶端在呼叫服務且通道與服務的連接層級安全性不相符時，會擲回錯誤。</span><span class="sxs-lookup"><span data-stu-id="1267e-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="1267e-121">若要將 gRPC 通道設定為使用 TLS，請確定伺服器位址的開頭為 `https` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="1267e-122">例如，會 `GrpcChannel.ForAddress("https://localhost:5001")` 使用 HTTPS 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="1267e-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="1267e-123">GRPC 通道會自動 negotates 由 TLS 保護的連線，並使用安全連線來進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="1267e-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="1267e-124">gRPC 支援透過 TLS 的用戶端憑證驗證。</span><span class="sxs-lookup"><span data-stu-id="1267e-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="1267e-125">如需使用 gRPC 通道設定用戶端憑證的詳細資訊，請參閱 <xref:grpc/authn-and-authz#client-certificate-authentication> 。</span><span class="sxs-lookup"><span data-stu-id="1267e-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="1267e-126">若要呼叫不安全的 gRPC 服務，請確定伺服器位址的開頭為 `http` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="1267e-127">例如，會 `GrpcChannel.ForAddress("http://localhost:5000")` 使用 HTTP 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="1267e-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="1267e-128">在 .NET Core 3.1 或更新版本中，需要額外的設定，才能 [使用 .net 用戶端呼叫不安全的 gRPC 服務](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)。</span><span class="sxs-lookup"><span data-stu-id="1267e-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="1267e-129">用戶端效能</span><span class="sxs-lookup"><span data-stu-id="1267e-129">Client performance</span></span>

<span data-ttu-id="1267e-130">通道和用戶端效能和使用方式：</span><span class="sxs-lookup"><span data-stu-id="1267e-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="1267e-131">建立通道可能是昂貴的作業。</span><span class="sxs-lookup"><span data-stu-id="1267e-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="1267e-132">重複使用 gRPC 呼叫的通道可提供效能優勢。</span><span class="sxs-lookup"><span data-stu-id="1267e-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="1267e-133">gRPC 用戶端會使用通道建立。</span><span class="sxs-lookup"><span data-stu-id="1267e-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="1267e-134">gRPC 用戶端是輕量物件，不需要快取或重複使用。</span><span class="sxs-lookup"><span data-stu-id="1267e-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="1267e-135">您可以從通道建立多個 gRPC 用戶端，包括不同類型的用戶端。</span><span class="sxs-lookup"><span data-stu-id="1267e-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="1267e-136">通道和從通道建立的用戶端可以安全地由多個執行緒使用。</span><span class="sxs-lookup"><span data-stu-id="1267e-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="1267e-137">從通道建立的用戶端可能會進行多個同時呼叫。</span><span class="sxs-lookup"><span data-stu-id="1267e-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="1267e-138">`GrpcChannel.ForAddress` 不是建立 gRPC 用戶端的唯一選項。</span><span class="sxs-lookup"><span data-stu-id="1267e-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="1267e-139">如果從 ASP.NET Core 應用程式呼叫 gRPC 服務，請考慮 [gRPC 用戶端工廠整合](xref:grpc/clientfactory)。</span><span class="sxs-lookup"><span data-stu-id="1267e-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="1267e-140">gRPC 整合 `HttpClientFactory` 提供了建立 gRPC 用戶端的集中式替代方案。</span><span class="sxs-lookup"><span data-stu-id="1267e-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="1267e-141">`Grpc.Net.Client`在 Xamarin 上，目前不支援透過 HTTP/2 呼叫 gRPC。</span><span class="sxs-lookup"><span data-stu-id="1267e-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="1267e-142">我們正在努力改善未來 Xamarin 版本中的 HTTP/2 支援。</span><span class="sxs-lookup"><span data-stu-id="1267e-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="1267e-143">[Grpc Core](https://www.nuget.org/packages/Grpc.Core) 和 [Grpc-Web 是一](xref:grpc/browser) 種可行的替代方案，可立即運作。</span><span class="sxs-lookup"><span data-stu-id="1267e-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="1267e-144">進行 gRPC 呼叫</span><span class="sxs-lookup"><span data-stu-id="1267e-144">Make gRPC calls</span></span>

<span data-ttu-id="1267e-145">藉由呼叫用戶端上的方法來起始 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="1267e-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="1267e-146">GRPC 用戶端會處理訊息序列化，並將 gRPC 呼叫定址至正確的服務。</span><span class="sxs-lookup"><span data-stu-id="1267e-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="1267e-147">gRPC 具有不同類型的方法。</span><span class="sxs-lookup"><span data-stu-id="1267e-147">gRPC has different types of methods.</span></span> <span data-ttu-id="1267e-148">用戶端如何用來進行 gRPC 呼叫，取決於呼叫的方法類型。</span><span class="sxs-lookup"><span data-stu-id="1267e-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="1267e-149">GRPC 方法類型為：</span><span class="sxs-lookup"><span data-stu-id="1267e-149">The gRPC method types are:</span></span>

* <span data-ttu-id="1267e-150">一元 (Unary)</span><span class="sxs-lookup"><span data-stu-id="1267e-150">Unary</span></span>
* <span data-ttu-id="1267e-151">伺服器串流</span><span class="sxs-lookup"><span data-stu-id="1267e-151">Server streaming</span></span>
* <span data-ttu-id="1267e-152">用戶端串流</span><span class="sxs-lookup"><span data-stu-id="1267e-152">Client streaming</span></span>
* <span data-ttu-id="1267e-153">雙向串流</span><span class="sxs-lookup"><span data-stu-id="1267e-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="1267e-154">一元呼叫</span><span class="sxs-lookup"><span data-stu-id="1267e-154">Unary call</span></span>

<span data-ttu-id="1267e-155">一元呼叫會從傳送要求訊息的用戶端開始。</span><span class="sxs-lookup"><span data-stu-id="1267e-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="1267e-156">當服務完成時，會傳迴響應消息。</span><span class="sxs-lookup"><span data-stu-id="1267e-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="1267e-157">在該\* \* proto\*檔中，每個一元服務方法都會在實體 gRPC 用戶端類型上產生兩個 .net 方法，以呼叫方法：非同步方法和封鎖方法。</span><span class="sxs-lookup"><span data-stu-id="1267e-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="1267e-158">例如， `GreeterClient` 有兩種方式可呼叫 `SayHello` ：</span><span class="sxs-lookup"><span data-stu-id="1267e-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="1267e-159">`GreeterClient.SayHelloAsync` - `Greeter.SayHello` 以非同步方式呼叫服務。</span><span class="sxs-lookup"><span data-stu-id="1267e-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="1267e-160">可以等候。</span><span class="sxs-lookup"><span data-stu-id="1267e-160">Can be awaited.</span></span>
* <span data-ttu-id="1267e-161">`GreeterClient.SayHello` -呼叫 `Greeter.SayHello` 服務並封鎖直到完成為止。</span><span class="sxs-lookup"><span data-stu-id="1267e-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="1267e-162">請勿在非同步程式碼中使用。</span><span class="sxs-lookup"><span data-stu-id="1267e-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="1267e-163">伺服器串流呼叫</span><span class="sxs-lookup"><span data-stu-id="1267e-163">Server streaming call</span></span>

<span data-ttu-id="1267e-164">伺服器串流呼叫會從傳送要求訊息的用戶端開始。</span><span class="sxs-lookup"><span data-stu-id="1267e-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="1267e-165">`ResponseStream.MoveNext()` 讀取從服務資料流程處理的訊息。</span><span class="sxs-lookup"><span data-stu-id="1267e-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="1267e-166">傳回時，伺服器串流呼叫已 `ResponseStream.MoveNext()` 完成 `false` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="1267e-167">使用 c # 8 或更新版本時， `await foreach` 可以使用此語法來讀取訊息。</span><span class="sxs-lookup"><span data-stu-id="1267e-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="1267e-168">`IAsyncStreamReader<T>.ReadAllAsync()`擴充方法會讀取來自回應資料流程的所有訊息：</span><span class="sxs-lookup"><span data-stu-id="1267e-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="1267e-169">用戶端串流呼叫</span><span class="sxs-lookup"><span data-stu-id="1267e-169">Client streaming call</span></span>

<span data-ttu-id="1267e-170">用戶端串流呼叫會在沒有用戶端傳送訊息的 *情況下* 啟動。</span><span class="sxs-lookup"><span data-stu-id="1267e-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="1267e-171">用戶端可以選擇傳送訊息給 `RequestStream.WriteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="1267e-172">當用戶端完成傳送訊息時， `RequestStream.CompleteAsync()` 應呼叫以通知服務。</span><span class="sxs-lookup"><span data-stu-id="1267e-172">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="1267e-173">當服務傳迴響應消息時，就會完成呼叫。</span><span class="sxs-lookup"><span data-stu-id="1267e-173">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="1267e-174">雙向串流呼叫</span><span class="sxs-lookup"><span data-stu-id="1267e-174">Bi-directional streaming call</span></span>

<span data-ttu-id="1267e-175">雙向串流呼叫會在沒有用戶端傳送訊息的 *情況下* 啟動。</span><span class="sxs-lookup"><span data-stu-id="1267e-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="1267e-176">用戶端可以選擇傳送訊息給 `RequestStream.WriteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="1267e-177">從服務串流處理的訊息可使用 `ResponseStream.MoveNext()` 或存取 `ResponseStream.ReadAllAsync()` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="1267e-178">當沒有其他訊息時，雙向串流呼叫就會完成 `ResponseStream` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="1267e-179">為了獲得最佳效能，並避免用戶端與服務發生不必要的錯誤，請嘗試正常完成雙向串流呼叫。</span><span class="sxs-lookup"><span data-stu-id="1267e-179">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="1267e-180">當伺服器完成讀取要求資料流程，且用戶端已完成讀取回應串流時，雙向呼叫就會正常完成。</span><span class="sxs-lookup"><span data-stu-id="1267e-180">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="1267e-181">上述的範例呼叫是以正常結束的雙向呼叫範例之一。</span><span class="sxs-lookup"><span data-stu-id="1267e-181">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="1267e-182">在此呼叫中，用戶端：</span><span class="sxs-lookup"><span data-stu-id="1267e-182">In the call, the client:</span></span>

1. <span data-ttu-id="1267e-183">藉由呼叫來啟動新的雙向串流呼叫 `EchoClient.Echo` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-183">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="1267e-184">使用建立從服務讀取訊息的背景工作 `ResponseStream.ReadAllAsync()` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-184">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="1267e-185">使用將訊息傳送至伺服器 `RequestStream.WriteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-185">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="1267e-186">通知伺服器它已完成傳送訊息 `RequestStream.CompleteAsync()` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-186">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="1267e-187">等候背景工作讀取所有傳入的訊息。</span><span class="sxs-lookup"><span data-stu-id="1267e-187">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="1267e-188">在雙向串流呼叫期間，用戶端和服務可以隨時傳送訊息給彼此。</span><span class="sxs-lookup"><span data-stu-id="1267e-188">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="1267e-189">與雙向呼叫互動的最佳用戶端邏輯會根據服務邏輯而有所不同。</span><span class="sxs-lookup"><span data-stu-id="1267e-189">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="1267e-190">存取權 gRPC 尾端</span><span class="sxs-lookup"><span data-stu-id="1267e-190">Access gRPC trailers</span></span>

<span data-ttu-id="1267e-191">gRPC 呼叫可能會傳回 gRPC 尾端。</span><span class="sxs-lookup"><span data-stu-id="1267e-191">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="1267e-192">gRPC 尾端用來提供關於呼叫的名稱/值中繼資料。</span><span class="sxs-lookup"><span data-stu-id="1267e-192">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="1267e-193">尾端提供類似于 HTTP 標頭的功能，但會在通話結束時收到。</span><span class="sxs-lookup"><span data-stu-id="1267e-193">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="1267e-194">您可以使用來存取 gRPC `GetTrailers()` 結尾，後者會傳回中繼資料的集合。</span><span class="sxs-lookup"><span data-stu-id="1267e-194">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="1267e-195">當回應完成之後，就會傳回尾端，因此，您必須先等候所有回應訊息，才能存取尾端。</span><span class="sxs-lookup"><span data-stu-id="1267e-195">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="1267e-196">一元和用戶端串流呼叫必須等待， `ResponseAsync` 才能呼叫 `GetTrailers()` ：</span><span class="sxs-lookup"><span data-stu-id="1267e-196">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="1267e-197">伺服器和雙向串流呼叫必須在呼叫之前完成回應資料流程的等候 `GetTrailers()` ：</span><span class="sxs-lookup"><span data-stu-id="1267e-197">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="1267e-198">您也可以從存取 gRPC 尾端 `RpcException` 。</span><span class="sxs-lookup"><span data-stu-id="1267e-198">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="1267e-199">服務可能會傳回尾端的 gRPC 狀態，並將其設定為不確定。</span><span class="sxs-lookup"><span data-stu-id="1267e-199">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="1267e-200">在此情況下，會從 gRPC 用戶端擲回的例外狀況中取出尾端：</span><span class="sxs-lookup"><span data-stu-id="1267e-200">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="1267e-201">其他資源</span><span class="sxs-lookup"><span data-stu-id="1267e-201">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
