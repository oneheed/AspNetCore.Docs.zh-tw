---
title: 建立 gRPC 服務和方法
author: jamesnk
description: 瞭解如何建立 gRPC 服務和方法。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/services
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058736"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="0fda7-103">建立 gRPC 服務和方法</span><span class="sxs-lookup"><span data-stu-id="0fda7-103">Create gRPC services and methods</span></span>

<span data-ttu-id="0fda7-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0fda7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0fda7-105">本檔說明如何以 c # 建立 gRPC 服務和方法。</span><span class="sxs-lookup"><span data-stu-id="0fda7-105">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="0fda7-106">主題包括：</span><span class="sxs-lookup"><span data-stu-id="0fda7-106">Topics include:</span></span>

* <span data-ttu-id="0fda7-107">如何定義 *proto* 檔中的服務和方法。</span><span class="sxs-lookup"><span data-stu-id="0fda7-107">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="0fda7-108">使用 gRPC c # 工具產生的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0fda7-108">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="0fda7-109">執行 gRPC 服務和方法。</span><span class="sxs-lookup"><span data-stu-id="0fda7-109">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="0fda7-110">建立新的 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="0fda7-110">Create new gRPC services</span></span>

<span data-ttu-id="0fda7-111">[使用 c # 的 gRPC services 引進了](xref:grpc/basics) gRPC 的合約優先方法來開發 API。</span><span class="sxs-lookup"><span data-stu-id="0fda7-111">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="0fda7-112">服務和訊息是在 *proto* 檔案中定義。</span><span class="sxs-lookup"><span data-stu-id="0fda7-112">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="0fda7-113">然後，c # 工具會從 *proto* 檔產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="0fda7-113">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="0fda7-114">針對伺服器端資產，會針對每個服務產生一個抽象基底型別，以及任何訊息的類別。</span><span class="sxs-lookup"><span data-stu-id="0fda7-114">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="0fda7-115">下列的 *proto* 檔案：</span><span class="sxs-lookup"><span data-stu-id="0fda7-115">The following *.proto* file:</span></span>

* <span data-ttu-id="0fda7-116">定義 `Greeter` 服務。</span><span class="sxs-lookup"><span data-stu-id="0fda7-116">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="0fda7-117">`Greeter`服務會定義 `SayHello` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="0fda7-117">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="0fda7-118">`SayHello` 傳送 `HelloRequest` 訊息並接收 `HelloReply` 訊息</span><span class="sxs-lookup"><span data-stu-id="0fda7-118">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="0fda7-119">C # 工具會產生 c # `GreeterBase` 基底類型：</span><span class="sxs-lookup"><span data-stu-id="0fda7-119">C# tooling generates the C# `GreeterBase` base type:</span></span>

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="0fda7-120">根據預設，產生的不 `GreeterBase` 會進行任何動作。</span><span class="sxs-lookup"><span data-stu-id="0fda7-120">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="0fda7-121">它的虛擬 `SayHello` 方法會將 `UNIMPLEMENTED` 錯誤傳回給呼叫它的任何用戶端。</span><span class="sxs-lookup"><span data-stu-id="0fda7-121">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="0fda7-122">若要讓服務有用，應用程式必須建立的具體實作為 `GreeterBase` ：</span><span class="sxs-lookup"><span data-stu-id="0fda7-122">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="0fda7-123">服務執行會向應用程式註冊。</span><span class="sxs-lookup"><span data-stu-id="0fda7-123">The service implementation is registered with the app.</span></span> <span data-ttu-id="0fda7-124">如果服務是由 ASP.NET Core gRPC 所主控，則應該使用方法將它新增至路由管線 `MapGrpcService` 。</span><span class="sxs-lookup"><span data-stu-id="0fda7-124">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="0fda7-125">如需詳細資訊，請參閱 <xref:grpc/aspnetcore>。</span><span class="sxs-lookup"><span data-stu-id="0fda7-125">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="0fda7-126">執行 gRPC 方法</span><span class="sxs-lookup"><span data-stu-id="0fda7-126">Implement gRPC methods</span></span>

<span data-ttu-id="0fda7-127">GRPC 服務可以有不同類型的方法。</span><span class="sxs-lookup"><span data-stu-id="0fda7-127">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="0fda7-128">服務傳送和接收訊息的方式取決於定義的方法類型。</span><span class="sxs-lookup"><span data-stu-id="0fda7-128">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="0fda7-129">GRPC 方法類型為：</span><span class="sxs-lookup"><span data-stu-id="0fda7-129">The gRPC method types are:</span></span>

* <span data-ttu-id="0fda7-130">一元</span><span class="sxs-lookup"><span data-stu-id="0fda7-130">Unary</span></span>
* <span data-ttu-id="0fda7-131">伺服器串流</span><span class="sxs-lookup"><span data-stu-id="0fda7-131">Server streaming</span></span>
* <span data-ttu-id="0fda7-132">用戶端串流</span><span class="sxs-lookup"><span data-stu-id="0fda7-132">Client streaming</span></span>
* <span data-ttu-id="0fda7-133">雙向串流</span><span class="sxs-lookup"><span data-stu-id="0fda7-133">Bi-directional streaming</span></span>

<span data-ttu-id="0fda7-134">使用 proto 檔中的關鍵字來指定資料流程呼叫 `stream` 。 *.proto*</span><span class="sxs-lookup"><span data-stu-id="0fda7-134">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="0fda7-135">`stream` 可以放在呼叫的要求訊息、回應訊息或兩者上。</span><span class="sxs-lookup"><span data-stu-id="0fda7-135">`stream` can be placed on a call's request message, response message, or both.</span></span>

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

<span data-ttu-id="0fda7-136">每個呼叫類型都有不同的方法簽章。</span><span class="sxs-lookup"><span data-stu-id="0fda7-136">Each call type has a different method signature.</span></span> <span data-ttu-id="0fda7-137">從具體實作為抽象基底服務型別的覆寫產生的方法，可確保使用的是正確的引數和傳回型別。</span><span class="sxs-lookup"><span data-stu-id="0fda7-137">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="0fda7-138">一元方法</span><span class="sxs-lookup"><span data-stu-id="0fda7-138">Unary method</span></span>

<span data-ttu-id="0fda7-139">一元方法會取得要求訊息做為參數，並傳迴響應。</span><span class="sxs-lookup"><span data-stu-id="0fda7-139">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="0fda7-140">傳迴響應時，一元呼叫已完成。</span><span class="sxs-lookup"><span data-stu-id="0fda7-140">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="0fda7-141">一元呼叫最類似于 [WEB API 控制器上的動作](xref:web-api/index)。</span><span class="sxs-lookup"><span data-stu-id="0fda7-141">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="0fda7-142">GRPC 方法有一個重要的差異，就是 gRPC 方法無法將要求的部分系結至不同的方法引數。</span><span class="sxs-lookup"><span data-stu-id="0fda7-142">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="0fda7-143">gRPC 方法的傳入要求資料一律會有一個訊息引數。</span><span class="sxs-lookup"><span data-stu-id="0fda7-143">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="0fda7-144">您仍然可以將多個值的欄位放在要求訊息上，以傳送至 gRPC 服務：</span><span class="sxs-lookup"><span data-stu-id="0fda7-144">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="0fda7-145">伺服器串流方法</span><span class="sxs-lookup"><span data-stu-id="0fda7-145">Server streaming method</span></span>

<span data-ttu-id="0fda7-146">伺服器串流方法會以參數形式取得要求訊息。</span><span class="sxs-lookup"><span data-stu-id="0fda7-146">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="0fda7-147">因為多個訊息可以串流回給呼叫者，所以 `responseStream.WriteAsync` 會用來傳送回應訊息。</span><span class="sxs-lookup"><span data-stu-id="0fda7-147">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="0fda7-148">當方法傳回時，伺服器串流呼叫已完成。</span><span class="sxs-lookup"><span data-stu-id="0fda7-148">A server streaming call is complete when the method returns.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

<span data-ttu-id="0fda7-149">一旦伺服器串流方法啟動之後，用戶端就無法傳送額外的訊息或資料。</span><span class="sxs-lookup"><span data-stu-id="0fda7-149">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="0fda7-150">有些串流方法是設計成永遠執行。</span><span class="sxs-lookup"><span data-stu-id="0fda7-150">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="0fda7-151">針對連續串流方法，用戶端可以在不再需要時取消呼叫。</span><span class="sxs-lookup"><span data-stu-id="0fda7-151">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="0fda7-152">當發生取消時，用戶端會將信號傳送到伺服器，而 ServerCallCoNtext 會引發 [CancellationToken](xref:System.Threading.CancellationToken) 。</span><span class="sxs-lookup"><span data-stu-id="0fda7-152">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="0fda7-153">使用 `CancellationToken` 非同步方法時，您應該在伺服器上使用此權杖，以便：</span><span class="sxs-lookup"><span data-stu-id="0fda7-153">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="0fda7-154">任何非同步工作都會與串流呼叫一起取消。</span><span class="sxs-lookup"><span data-stu-id="0fda7-154">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="0fda7-155">方法很快就會結束。</span><span class="sxs-lookup"><span data-stu-id="0fda7-155">The method exits quickly.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a><span data-ttu-id="0fda7-156">用戶端串流方法</span><span class="sxs-lookup"><span data-stu-id="0fda7-156">Client streaming method</span></span>

<span data-ttu-id="0fda7-157">用戶端串流方法會在沒有接收訊息的方法的 *情況下* 啟動。</span><span class="sxs-lookup"><span data-stu-id="0fda7-157">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="0fda7-158">`requestStream`參數是用來從用戶端讀取訊息。</span><span class="sxs-lookup"><span data-stu-id="0fda7-158">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="0fda7-159">傳迴響應消息時，用戶端串流呼叫已完成：</span><span class="sxs-lookup"><span data-stu-id="0fda7-159">A client streaming call is complete when a response message is returned:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

<span data-ttu-id="0fda7-160">使用 c # 8 或更新版本時， `await foreach` 可以使用此語法來讀取訊息。</span><span class="sxs-lookup"><span data-stu-id="0fda7-160">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="0fda7-161">`IAsyncStreamReader<T>.ReadAllAsync()`擴充方法會讀取來自要求資料流程的所有訊息：</span><span class="sxs-lookup"><span data-stu-id="0fda7-161">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="0fda7-162">雙向串流方法</span><span class="sxs-lookup"><span data-stu-id="0fda7-162">Bi-directional streaming method</span></span>

<span data-ttu-id="0fda7-163">雙向串流方法會在沒有接收訊息的方法的 *情況下* 啟動。</span><span class="sxs-lookup"><span data-stu-id="0fda7-163">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="0fda7-164">`requestStream`參數是用來從用戶端讀取訊息。</span><span class="sxs-lookup"><span data-stu-id="0fda7-164">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="0fda7-165">方法可以選擇傳送訊息給 `responseStream.WriteAsync` 。</span><span class="sxs-lookup"><span data-stu-id="0fda7-165">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="0fda7-166">當方法傳回時，雙向串流呼叫已完成：</span><span class="sxs-lookup"><span data-stu-id="0fda7-166">A bi-directional streaming call is complete when the the method returns:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

<span data-ttu-id="0fda7-167">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="0fda7-167">The preceding code:</span></span>

* <span data-ttu-id="0fda7-168">傳送每個要求的回應。</span><span class="sxs-lookup"><span data-stu-id="0fda7-168">Sends a response for each request.</span></span>
* <span data-ttu-id="0fda7-169">是雙向串流的基本使用方式。</span><span class="sxs-lookup"><span data-stu-id="0fda7-169">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="0fda7-170">您可以支援更複雜的案例，例如同時讀取要求和傳送回應：</span><span class="sxs-lookup"><span data-stu-id="0fda7-170">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

<span data-ttu-id="0fda7-171">在雙向串流方法中，用戶端和服務可以隨時傳送訊息給彼此。</span><span class="sxs-lookup"><span data-stu-id="0fda7-171">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="0fda7-172">雙向方法的最佳執行會根據需求而有所不同。</span><span class="sxs-lookup"><span data-stu-id="0fda7-172">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="0fda7-173">存取權 gRPC 要求標頭</span><span class="sxs-lookup"><span data-stu-id="0fda7-173">Access gRPC request headers</span></span>

<span data-ttu-id="0fda7-174">要求訊息不是用戶端將資料傳送至 gRPC 服務的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="0fda7-174">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="0fda7-175">您可以使用中的服務來使用標頭值 `ServerCallContext.RequestHeaders` 。</span><span class="sxs-lookup"><span data-stu-id="0fda7-175">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="0fda7-176">其他資源</span><span class="sxs-lookup"><span data-stu-id="0fda7-176">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
