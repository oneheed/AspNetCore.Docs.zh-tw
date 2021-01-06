---
title: 建立 gRPC 服務和方法
author: jamesnk
description: 瞭解如何建立 gRPC 服務和方法。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058736"
---
# <a name="create-grpc-services-and-methods"></a>建立 gRPC 服務和方法

依 [James 牛頓](https://twitter.com/jamesnk)

本檔說明如何以 c # 建立 gRPC 服務和方法。 主題包括：

* 如何定義 *proto* 檔中的服務和方法。
* 使用 gRPC c # 工具產生的程式碼。
* 執行 gRPC 服務和方法。

## <a name="create-new-grpc-services"></a>建立新的 gRPC 服務

[使用 c # 的 gRPC services 引進了](xref:grpc/basics) gRPC 的合約優先方法來開發 API。 服務和訊息是在 *proto* 檔案中定義。 然後，c # 工具會從 *proto* 檔產生程式碼。 針對伺服器端資產，會針對每個服務產生一個抽象基底型別，以及任何訊息的類別。

下列的 *proto* 檔案：

* 定義 `Greeter` 服務。
* `Greeter`服務會定義 `SayHello` 呼叫。
* `SayHello` 傳送 `HelloRequest` 訊息並接收 `HelloReply` 訊息

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

C # 工具會產生 c # `GreeterBase` 基底類型：

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

根據預設，產生的不 `GreeterBase` 會進行任何動作。 它的虛擬 `SayHello` 方法會將 `UNIMPLEMENTED` 錯誤傳回給呼叫它的任何用戶端。 若要讓服務有用，應用程式必須建立的具體實作為 `GreeterBase` ：

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

服務執行會向應用程式註冊。 如果服務是由 ASP.NET Core gRPC 所主控，則應該使用方法將它新增至路由管線 `MapGrpcService` 。

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

如需相關資訊，請參閱 <xref:grpc/aspnetcore> 。

## <a name="implement-grpc-methods"></a>執行 gRPC 方法

GRPC 服務可以有不同類型的方法。 服務傳送和接收訊息的方式取決於定義的方法類型。 GRPC 方法類型為：

* 一元 (Unary)
* 伺服器串流
* 用戶端串流
* 雙向串流

使用 proto 檔中的關鍵字來指定資料流程呼叫 `stream` 。  `stream` 可以放在呼叫的要求訊息、回應訊息或兩者上。

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

每個呼叫類型都有不同的方法簽章。 從具體實作為抽象基底服務型別的覆寫產生的方法，可確保使用的是正確的引數和傳回型別。

### <a name="unary-method"></a>一元方法

一元方法會取得要求訊息做為參數，並傳迴響應。 傳迴響應時，一元呼叫已完成。

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

一元呼叫最類似于 [WEB API 控制器上的動作](xref:web-api/index)。 GRPC 方法有一個重要的差異，就是 gRPC 方法無法將要求的部分系結至不同的方法引數。 gRPC 方法的傳入要求資料一律會有一個訊息引數。 您仍然可以將多個值的欄位放在要求訊息上，以傳送至 gRPC 服務：

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>伺服器串流方法

伺服器串流方法會以參數形式取得要求訊息。 因為多個訊息可以串流回給呼叫者，所以 `responseStream.WriteAsync` 會用來傳送回應訊息。 當方法傳回時，伺服器串流呼叫已完成。

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

一旦伺服器串流方法啟動之後，用戶端就無法傳送額外的訊息或資料。 有些串流方法是設計成永遠執行。 針對連續串流方法，用戶端可以在不再需要時取消呼叫。 當發生取消時，用戶端會將信號傳送到伺服器，而 ServerCallCoNtext 會引發 [CancellationToken](xref:System.Threading.CancellationToken) 。 使用 `CancellationToken` 非同步方法時，您應該在伺服器上使用此權杖，以便：

* 任何非同步工作都會與串流呼叫一起取消。
* 方法很快就會結束。

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

### <a name="client-streaming-method"></a>用戶端串流方法

用戶端串流方法會在沒有接收訊息的方法的 *情況下* 啟動。 `requestStream`參數是用來從用戶端讀取訊息。 傳迴響應消息時，用戶端串流呼叫已完成：

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

使用 c # 8 或更新版本時， `await foreach` 可以使用此語法來讀取訊息。 `IAsyncStreamReader<T>.ReadAllAsync()`擴充方法會讀取來自要求資料流程的所有訊息：

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

### <a name="bi-directional-streaming-method"></a>雙向串流方法

雙向串流方法會在沒有接收訊息的方法的 *情況下* 啟動。 `requestStream`參數是用來從用戶端讀取訊息。 方法可以選擇傳送訊息給 `responseStream.WriteAsync` 。 當方法傳回時，雙向串流呼叫已完成：

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

上述程式碼：

* 傳送每個要求的回應。
* 是雙向串流的基本使用方式。

您可以支援更複雜的案例，例如同時讀取要求和傳送回應：

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

在雙向串流方法中，用戶端和服務可以隨時傳送訊息給彼此。 雙向方法的最佳執行會根據需求而有所不同。

## <a name="access-grpc-request-headers"></a>存取權 gRPC 要求標頭

要求訊息不是用戶端將資料傳送至 gRPC 服務的唯一方法。 您可以使用中的服務來使用標頭值 `ServerCallContext.RequestHeaders` 。

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>其他資源

* <xref:grpc/basics>
* <xref:grpc/client>
