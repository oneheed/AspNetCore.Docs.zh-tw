---
title: 利用 .NET 用戶端呼叫 gRPC 服務
author: jamesnk
description: 瞭解如何使用 .NET gRPC 用戶端呼叫 gRPC 服務。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/18/2020
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
uid: grpc/client
ms.openlocfilehash: 39f9b3fde19e31ca970668552e5829308705f513
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97699130"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="6c965-103">利用 .NET 用戶端呼叫 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="6c965-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="6c965-104">.NET gRPC 用戶端程式庫可在 [gRPC .net. 用戶端](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 套件中取得。</span><span class="sxs-lookup"><span data-stu-id="6c965-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="6c965-105">本檔說明如何：</span><span class="sxs-lookup"><span data-stu-id="6c965-105">This document explains how to:</span></span>

* <span data-ttu-id="6c965-106">設定 gRPC 用戶端以呼叫 gRPC services。</span><span class="sxs-lookup"><span data-stu-id="6c965-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="6c965-107">對一元、伺服器串流、用戶端串流和雙向串流方法進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="6c965-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="6c965-108">設定 gRPC 用戶端</span><span class="sxs-lookup"><span data-stu-id="6c965-108">Configure gRPC client</span></span>

<span data-ttu-id="6c965-109">gRPC 用戶端是 [從 *\* proto* 檔案產生](xref:grpc/basics#generated-c-assets)的具體用戶端類型。</span><span class="sxs-lookup"><span data-stu-id="6c965-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="6c965-110">具體 gRPC 用戶端的方法會轉譯為 *\* proto* 檔案中的 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="6c965-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="6c965-111">例如，名為的服務會 `Greeter` 產生 `GreeterClient` 具有方法的型別來呼叫服務。</span><span class="sxs-lookup"><span data-stu-id="6c965-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="6c965-112">GRPC 用戶端是從通道建立。</span><span class="sxs-lookup"><span data-stu-id="6c965-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="6c965-113">首先，使用 `GrpcChannel.ForAddress` 建立通道，然後使用通道來建立 gRPC 用戶端：</span><span class="sxs-lookup"><span data-stu-id="6c965-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="6c965-114">通道代表 gRPC 服務的長時間連接。</span><span class="sxs-lookup"><span data-stu-id="6c965-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="6c965-115">建立通道時，會使用與呼叫服務相關的選項進行設定。</span><span class="sxs-lookup"><span data-stu-id="6c965-115">When a channel is created, it's configured with options related to calling a service.</span></span> <span data-ttu-id="6c965-116">例如， `HttpClient` 用來進行呼叫、最大傳送和接收訊息大小，以及記錄可以在上指定 `GrpcChannelOptions` 和使用 `GrpcChannel.ForAddress` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="6c965-117">如需完整的選項清單，請參閱 [用戶端設定選項](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="6c965-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="6c965-118">設定 TLS</span><span class="sxs-lookup"><span data-stu-id="6c965-118">Configure TLS</span></span>

<span data-ttu-id="6c965-119">GRPC 用戶端必須使用與所呼叫服務相同的連接層級安全性。</span><span class="sxs-lookup"><span data-stu-id="6c965-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="6c965-120">建立 gRPC 通道時，會設定 (TLS) 的 gRPC 用戶端傳輸層安全性。</span><span class="sxs-lookup"><span data-stu-id="6c965-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="6c965-121">GRPC 用戶端在呼叫服務且通道與服務的連接層級安全性不相符時，會擲回錯誤。</span><span class="sxs-lookup"><span data-stu-id="6c965-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="6c965-122">若要將 gRPC 通道設定為使用 TLS，請確定伺服器位址的開頭為 `https` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="6c965-123">例如，會 `GrpcChannel.ForAddress("https://localhost:5001")` 使用 HTTPS 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="6c965-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="6c965-124">GRPC 通道會自動協調受 TLS 保護的連線，並使用安全連線來進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="6c965-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="6c965-125">gRPC 支援透過 TLS 的用戶端憑證驗證。</span><span class="sxs-lookup"><span data-stu-id="6c965-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="6c965-126">如需使用 gRPC 通道設定用戶端憑證的詳細資訊，請參閱 <xref:grpc/authn-and-authz#client-certificate-authentication> 。</span><span class="sxs-lookup"><span data-stu-id="6c965-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="6c965-127">若要呼叫不安全的 gRPC 服務，請確定伺服器位址的開頭為 `http` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="6c965-128">例如，會 `GrpcChannel.ForAddress("http://localhost:5000")` 使用 HTTP 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="6c965-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="6c965-129">在 .NET Core 3.1 或更新版本中，需要額外的設定，才能 [使用 .net 用戶端呼叫不安全的 gRPC 服務](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)。</span><span class="sxs-lookup"><span data-stu-id="6c965-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="6c965-130">用戶端效能</span><span class="sxs-lookup"><span data-stu-id="6c965-130">Client performance</span></span>

<span data-ttu-id="6c965-131">通道和用戶端效能和使用方式：</span><span class="sxs-lookup"><span data-stu-id="6c965-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="6c965-132">建立通道可能是昂貴的作業。</span><span class="sxs-lookup"><span data-stu-id="6c965-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="6c965-133">重複使用 gRPC 呼叫的通道可提供效能優勢。</span><span class="sxs-lookup"><span data-stu-id="6c965-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="6c965-134">gRPC 用戶端會使用通道建立。</span><span class="sxs-lookup"><span data-stu-id="6c965-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="6c965-135">gRPC 用戶端是輕量物件，不需要快取或重複使用。</span><span class="sxs-lookup"><span data-stu-id="6c965-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="6c965-136">您可以從通道建立多個 gRPC 用戶端，包括不同類型的用戶端。</span><span class="sxs-lookup"><span data-stu-id="6c965-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="6c965-137">通道和從通道建立的用戶端可以安全地由多個執行緒使用。</span><span class="sxs-lookup"><span data-stu-id="6c965-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="6c965-138">從通道建立的用戶端可能會進行多個同時呼叫。</span><span class="sxs-lookup"><span data-stu-id="6c965-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="6c965-139">`GrpcChannel.ForAddress` 不是建立 gRPC 用戶端的唯一選項。</span><span class="sxs-lookup"><span data-stu-id="6c965-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="6c965-140">如果從 ASP.NET Core 應用程式呼叫 gRPC 服務，請考慮 [gRPC 用戶端工廠整合](xref:grpc/clientfactory)。</span><span class="sxs-lookup"><span data-stu-id="6c965-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="6c965-141">gRPC 整合 `HttpClientFactory` 提供了建立 gRPC 用戶端的集中式替代方案。</span><span class="sxs-lookup"><span data-stu-id="6c965-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="6c965-142">`Grpc.Net.Client`在 Xamarin 上，目前不支援透過 HTTP/2 呼叫 gRPC。</span><span class="sxs-lookup"><span data-stu-id="6c965-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="6c965-143">我們正在努力改善未來 Xamarin 版本中的 HTTP/2 支援。</span><span class="sxs-lookup"><span data-stu-id="6c965-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="6c965-144">[Grpc Core](https://www.nuget.org/packages/Grpc.Core) 和 [Grpc-Web 是一](xref:grpc/browser) 種可行的替代方案，可立即運作。</span><span class="sxs-lookup"><span data-stu-id="6c965-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="6c965-145">進行 gRPC 呼叫</span><span class="sxs-lookup"><span data-stu-id="6c965-145">Make gRPC calls</span></span>

<span data-ttu-id="6c965-146">藉由呼叫用戶端上的方法來起始 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="6c965-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="6c965-147">GRPC 用戶端會處理訊息序列化，並將 gRPC 呼叫定址至正確的服務。</span><span class="sxs-lookup"><span data-stu-id="6c965-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="6c965-148">gRPC 具有不同類型的方法。</span><span class="sxs-lookup"><span data-stu-id="6c965-148">gRPC has different types of methods.</span></span> <span data-ttu-id="6c965-149">用戶端如何用來進行 gRPC 呼叫，取決於呼叫的方法類型。</span><span class="sxs-lookup"><span data-stu-id="6c965-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="6c965-150">GRPC 方法類型為：</span><span class="sxs-lookup"><span data-stu-id="6c965-150">The gRPC method types are:</span></span>

* <span data-ttu-id="6c965-151">一元 (Unary)</span><span class="sxs-lookup"><span data-stu-id="6c965-151">Unary</span></span>
* <span data-ttu-id="6c965-152">伺服器串流</span><span class="sxs-lookup"><span data-stu-id="6c965-152">Server streaming</span></span>
* <span data-ttu-id="6c965-153">用戶端串流</span><span class="sxs-lookup"><span data-stu-id="6c965-153">Client streaming</span></span>
* <span data-ttu-id="6c965-154">雙向串流</span><span class="sxs-lookup"><span data-stu-id="6c965-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="6c965-155">一元呼叫</span><span class="sxs-lookup"><span data-stu-id="6c965-155">Unary call</span></span>

<span data-ttu-id="6c965-156">一元呼叫會從傳送要求訊息的用戶端開始。</span><span class="sxs-lookup"><span data-stu-id="6c965-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="6c965-157">當服務完成時，會傳迴響應消息。</span><span class="sxs-lookup"><span data-stu-id="6c965-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="6c965-158">在該 *\* proto* 檔中，每個一元服務方法都會在實體 gRPC 用戶端類型上產生兩個 .net 方法，以呼叫方法：非同步方法和封鎖方法。</span><span class="sxs-lookup"><span data-stu-id="6c965-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="6c965-159">例如， `GreeterClient` 有兩種方式可呼叫 `SayHello` ：</span><span class="sxs-lookup"><span data-stu-id="6c965-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="6c965-160">`GreeterClient.SayHelloAsync` - `Greeter.SayHello` 以非同步方式呼叫服務。</span><span class="sxs-lookup"><span data-stu-id="6c965-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="6c965-161">可以等候。</span><span class="sxs-lookup"><span data-stu-id="6c965-161">Can be awaited.</span></span>
* <span data-ttu-id="6c965-162">`GreeterClient.SayHello` -呼叫 `Greeter.SayHello` 服務並封鎖直到完成為止。</span><span class="sxs-lookup"><span data-stu-id="6c965-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="6c965-163">請勿在非同步程式碼中使用。</span><span class="sxs-lookup"><span data-stu-id="6c965-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="6c965-164">伺服器串流呼叫</span><span class="sxs-lookup"><span data-stu-id="6c965-164">Server streaming call</span></span>

<span data-ttu-id="6c965-165">伺服器串流呼叫會從傳送要求訊息的用戶端開始。</span><span class="sxs-lookup"><span data-stu-id="6c965-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="6c965-166">`ResponseStream.MoveNext()` 讀取從服務資料流程處理的訊息。</span><span class="sxs-lookup"><span data-stu-id="6c965-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="6c965-167">傳回時，伺服器串流呼叫已 `ResponseStream.MoveNext()` 完成 `false` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="6c965-168">使用 c # 8 或更新版本時， `await foreach` 可以使用此語法來讀取訊息。</span><span class="sxs-lookup"><span data-stu-id="6c965-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="6c965-169">`IAsyncStreamReader<T>.ReadAllAsync()`擴充方法會讀取來自回應資料流程的所有訊息：</span><span class="sxs-lookup"><span data-stu-id="6c965-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="6c965-170">用戶端串流呼叫</span><span class="sxs-lookup"><span data-stu-id="6c965-170">Client streaming call</span></span>

<span data-ttu-id="6c965-171">用戶端串流呼叫會在沒有用戶端傳送訊息的 *情況下* 啟動。</span><span class="sxs-lookup"><span data-stu-id="6c965-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="6c965-172">用戶端可以選擇傳送訊息給 `RequestStream.WriteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="6c965-173">當用戶端完成傳送訊息時， `RequestStream.CompleteAsync()` 應呼叫以通知服務。</span><span class="sxs-lookup"><span data-stu-id="6c965-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="6c965-174">當服務傳迴響應消息時，就會完成呼叫。</span><span class="sxs-lookup"><span data-stu-id="6c965-174">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="6c965-175">雙向串流呼叫</span><span class="sxs-lookup"><span data-stu-id="6c965-175">Bi-directional streaming call</span></span>

<span data-ttu-id="6c965-176">雙向串流呼叫會在沒有用戶端傳送訊息的 *情況下* 啟動。</span><span class="sxs-lookup"><span data-stu-id="6c965-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="6c965-177">用戶端可以選擇傳送訊息給 `RequestStream.WriteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="6c965-178">從服務串流處理的訊息可使用 `ResponseStream.MoveNext()` 或存取 `ResponseStream.ReadAllAsync()` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="6c965-179">當沒有其他訊息時，雙向串流呼叫就會完成 `ResponseStream` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="6c965-180">為了獲得最佳效能，並避免用戶端與服務發生不必要的錯誤，請嘗試正常完成雙向串流呼叫。</span><span class="sxs-lookup"><span data-stu-id="6c965-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="6c965-181">當伺服器完成讀取要求資料流程，且用戶端已完成讀取回應串流時，雙向呼叫就會正常完成。</span><span class="sxs-lookup"><span data-stu-id="6c965-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="6c965-182">上述的範例呼叫是以正常結束的雙向呼叫範例之一。</span><span class="sxs-lookup"><span data-stu-id="6c965-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="6c965-183">在此呼叫中，用戶端：</span><span class="sxs-lookup"><span data-stu-id="6c965-183">In the call, the client:</span></span>

1. <span data-ttu-id="6c965-184">藉由呼叫來啟動新的雙向串流呼叫 `EchoClient.Echo` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="6c965-185">使用建立從服務讀取訊息的背景工作 `ResponseStream.ReadAllAsync()` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="6c965-186">使用將訊息傳送至伺服器 `RequestStream.WriteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="6c965-187">通知伺服器它已完成傳送訊息 `RequestStream.CompleteAsync()` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="6c965-188">等候背景工作讀取所有傳入的訊息。</span><span class="sxs-lookup"><span data-stu-id="6c965-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="6c965-189">在雙向串流呼叫期間，用戶端和服務可以隨時傳送訊息給彼此。</span><span class="sxs-lookup"><span data-stu-id="6c965-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="6c965-190">與雙向呼叫互動的最佳用戶端邏輯會根據服務邏輯而有所不同。</span><span class="sxs-lookup"><span data-stu-id="6c965-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-headers"></a><span data-ttu-id="6c965-191">存取權 gRPC 標頭</span><span class="sxs-lookup"><span data-stu-id="6c965-191">Access gRPC headers</span></span>

<span data-ttu-id="6c965-192">gRPC 呼叫會傳迴響應標頭。</span><span class="sxs-lookup"><span data-stu-id="6c965-192">gRPC calls return response headers.</span></span> <span data-ttu-id="6c965-193">HTTP 回應標頭傳遞有關呼叫的名稱/值中繼資料，而該呼叫與傳回的訊息無關。</span><span class="sxs-lookup"><span data-stu-id="6c965-193">HTTP response headers pass name/value metadata about a call that isn't related the returned message.</span></span>

<span data-ttu-id="6c965-194">您可以使用來存取標頭 `ResponseHeadersAsync` ，這會傳回中繼資料的集合。</span><span class="sxs-lookup"><span data-stu-id="6c965-194">Headers are accessible using `ResponseHeadersAsync`, which returns a collection of metadata.</span></span> <span data-ttu-id="6c965-195">標頭通常會隨回應訊息傳回;因此，您必須等待它們。</span><span class="sxs-lookup"><span data-stu-id="6c965-195">Headers are typically returned with the response message; therefore, you must await them.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });

var headers = await call.ResponseHeadersAsync;
var myValue = headers.GetValue("my-trailer-name");

var response = await call.ResponseAsync;
```

<span data-ttu-id="6c965-196">`ResponseHeadersAsync` 使用：</span><span class="sxs-lookup"><span data-stu-id="6c965-196">`ResponseHeadersAsync` usage:</span></span>

* <span data-ttu-id="6c965-197">必須等待的結果 `ResponseHeadersAsync` 才能取得標頭集合。</span><span class="sxs-lookup"><span data-stu-id="6c965-197">Must await the result of `ResponseHeadersAsync` to get the headers collection.</span></span>
* <span data-ttu-id="6c965-198">在串流處理) 時，不需要先存取 `ResponseAsync` (或回應資料流程。</span><span class="sxs-lookup"><span data-stu-id="6c965-198">Doesn't have to be accessed before `ResponseAsync` (or the response stream when streaming).</span></span> <span data-ttu-id="6c965-199">如果已傳迴響應，則會 `ResponseHeadersAsync` 立即傳回標頭。</span><span class="sxs-lookup"><span data-stu-id="6c965-199">If a response has been returned, then `ResponseHeadersAsync` returns headers instantly.</span></span>
* <span data-ttu-id="6c965-200">如果發生連接或伺服器錯誤，而且沒有針對 gRPC 呼叫傳回標頭，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="6c965-200">Will throw an exception if there was a connection or server error and headers weren't returned for the gRPC call.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="6c965-201">存取權 gRPC 尾端</span><span class="sxs-lookup"><span data-stu-id="6c965-201">Access gRPC trailers</span></span>

<span data-ttu-id="6c965-202">gRPC 呼叫可能會傳迴響應尾端。</span><span class="sxs-lookup"><span data-stu-id="6c965-202">gRPC calls may return response trailers.</span></span> <span data-ttu-id="6c965-203">尾端用來提供關於呼叫的名稱/值中繼資料。</span><span class="sxs-lookup"><span data-stu-id="6c965-203">Trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="6c965-204">尾端提供類似于 HTTP 標頭的功能，但會在通話結束時收到。</span><span class="sxs-lookup"><span data-stu-id="6c965-204">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="6c965-205">您可以使用來存取尾端 `GetTrailers()` ，以傳回中繼資料的集合。</span><span class="sxs-lookup"><span data-stu-id="6c965-205">Trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="6c965-206">完成回應後會傳回尾端。</span><span class="sxs-lookup"><span data-stu-id="6c965-206">Trailers are returned after the response is complete.</span></span> <span data-ttu-id="6c965-207">因此，您必須先等候所有回應訊息，才能存取尾端。</span><span class="sxs-lookup"><span data-stu-id="6c965-207">Therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="6c965-208">一元和用戶端串流呼叫必須等待， `ResponseAsync` 才能呼叫 `GetTrailers()` ：</span><span class="sxs-lookup"><span data-stu-id="6c965-208">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="6c965-209">伺服器和雙向串流呼叫必須在呼叫之前完成回應資料流程的等候 `GetTrailers()` ：</span><span class="sxs-lookup"><span data-stu-id="6c965-209">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

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

<span data-ttu-id="6c965-210">也可以從存取尾端 `RpcException` 。</span><span class="sxs-lookup"><span data-stu-id="6c965-210">Trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="6c965-211">服務可能會傳回尾端的 gRPC 狀態，並將其設定為不確定。</span><span class="sxs-lookup"><span data-stu-id="6c965-211">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="6c965-212">在此情況下，會從 gRPC 用戶端擲回的例外狀況中取出尾端：</span><span class="sxs-lookup"><span data-stu-id="6c965-212">In this situation, the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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

## <a name="configure-deadline"></a><span data-ttu-id="6c965-213">設定期限</span><span class="sxs-lookup"><span data-stu-id="6c965-213">Configure deadline</span></span>

<span data-ttu-id="6c965-214">建議您設定 gRPC 呼叫期限，因為它會提供呼叫可執行檔時間上限。</span><span class="sxs-lookup"><span data-stu-id="6c965-214">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="6c965-215">它會停止不正常的服務，使其無法執行永久和耗盡的伺服器資源。</span><span class="sxs-lookup"><span data-stu-id="6c965-215">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="6c965-216">期限是建立可靠應用程式的有用工具。</span><span class="sxs-lookup"><span data-stu-id="6c965-216">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="6c965-217">`CallOptions.Deadline`設定以設定 gRPC 呼叫的期限：</span><span class="sxs-lookup"><span data-stu-id="6c965-217">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="6c965-218">如需詳細資訊，請參閱 <xref:grpc/deadlines-cancellation#deadlines> 。</span><span class="sxs-lookup"><span data-stu-id="6c965-218">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c965-219">其他資源</span><span class="sxs-lookup"><span data-stu-id="6c965-219">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
