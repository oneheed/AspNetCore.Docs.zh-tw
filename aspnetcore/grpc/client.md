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
ms.openlocfilehash: 6515e87845cc5aa101532c18711d175a73581bee
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722705"
---
# <a name="call-grpc-services-with-the-net-client"></a>利用 .NET 用戶端呼叫 gRPC 服務

.NET gRPC 用戶端程式庫可在 [gRPC .net. 用戶端](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 套件中取得。 本檔說明如何：

* 設定 gRPC 用戶端以呼叫 gRPC services。
* 對一元、伺服器串流、用戶端串流和雙向串流方法進行 gRPC 呼叫。

## <a name="configure-grpc-client"></a>設定 gRPC 用戶端

gRPC 用戶端是[從* \* proto*檔案產生](xref:grpc/basics#generated-c-assets)的具體用戶端類型。 具體 gRPC 用戶端的方法會轉譯為* \* proto*檔案中的 gRPC 服務。 例如，名為的服務會 `Greeter` 產生 `GreeterClient` 具有方法的型別來呼叫服務。

GRPC 用戶端是從通道建立。 首先，使用 `GrpcChannel.ForAddress` 建立通道，然後使用通道來建立 gRPC 用戶端：

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

通道代表 gRPC 服務的長時間連接。 建立通道時，會使用與呼叫服務相關的選項進行設定。 例如， `HttpClient` 用來進行呼叫、最大傳送和接收訊息大小，以及記錄可以在上指定 `GrpcChannelOptions` 和使用 `GrpcChannel.ForAddress` 。 如需完整的選項清單，請參閱 [用戶端設定選項](xref:grpc/configuration#configure-client-options)。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a>設定 TLS

GRPC 用戶端必須使用與所呼叫服務相同的連接層級安全性。 建立 gRPC 通道時，會設定 (TLS) 的 gRPC 用戶端傳輸層安全性。 GRPC 用戶端在呼叫服務且通道與服務的連接層級安全性不相符時，會擲回錯誤。

若要將 gRPC 通道設定為使用 TLS，請確定伺服器位址的開頭為 `https` 。 例如，會 `GrpcChannel.ForAddress("https://localhost:5001")` 使用 HTTPS 通訊協定。 GRPC 通道會自動協調受 TLS 保護的連線，並使用安全連線來進行 gRPC 呼叫。

> [!TIP]
> gRPC 支援透過 TLS 的用戶端憑證驗證。 如需使用 gRPC 通道設定用戶端憑證的詳細資訊，請參閱 <xref:grpc/authn-and-authz#client-certificate-authentication> 。

若要呼叫不安全的 gRPC 服務，請確定伺服器位址的開頭為 `http` 。 例如，會 `GrpcChannel.ForAddress("http://localhost:5000")` 使用 HTTP 通訊協定。 在 .NET Core 3.1 或更新版本中，需要額外的設定，才能 [使用 .net 用戶端呼叫不安全的 gRPC 服務](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)。

### <a name="client-performance"></a>用戶端效能

通道和用戶端效能和使用方式：

* 建立通道可能是昂貴的作業。 重複使用 gRPC 呼叫的通道可提供效能優勢。
* gRPC 用戶端會使用通道建立。 gRPC 用戶端是輕量物件，不需要快取或重複使用。
* 您可以從通道建立多個 gRPC 用戶端，包括不同類型的用戶端。
* 通道和從通道建立的用戶端可以安全地由多個執行緒使用。
* 從通道建立的用戶端可能會進行多個同時呼叫。

`GrpcChannel.ForAddress` 不是建立 gRPC 用戶端的唯一選項。 如果從 ASP.NET Core 應用程式呼叫 gRPC 服務，請考慮 [gRPC 用戶端工廠整合](xref:grpc/clientfactory)。 gRPC 整合 `HttpClientFactory` 提供了建立 gRPC 用戶端的集中式替代方案。

> [!NOTE]
> `Grpc.Net.Client`在 Xamarin 上，目前不支援透過 HTTP/2 呼叫 gRPC。 我們正在努力改善未來 Xamarin 版本中的 HTTP/2 支援。 [Grpc Core](https://www.nuget.org/packages/Grpc.Core) 和 [Grpc-Web 是一](xref:grpc/browser) 種可行的替代方案，可立即運作。

## <a name="make-grpc-calls"></a>進行 gRPC 呼叫

藉由呼叫用戶端上的方法來起始 gRPC 呼叫。 GRPC 用戶端會處理訊息序列化，並將 gRPC 呼叫定址至正確的服務。

gRPC 具有不同類型的方法。 用戶端如何用來進行 gRPC 呼叫，取決於呼叫的方法類型。 GRPC 方法類型為：

* 一元
* 伺服器串流
* 用戶端串流
* 雙向串流

### <a name="unary-call"></a>一元呼叫

一元呼叫會從傳送要求訊息的用戶端開始。 當服務完成時，會傳迴響應消息。

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

在該* \* proto*檔中，每個一元服務方法都會在實體 gRPC 用戶端類型上產生兩個 .net 方法，以呼叫方法：非同步方法和封鎖方法。 例如， `GreeterClient` 有兩種方式可呼叫 `SayHello` ：

* `GreeterClient.SayHelloAsync` - `Greeter.SayHello` 以非同步方式呼叫服務。 可以等候。
* `GreeterClient.SayHello` -呼叫 `Greeter.SayHello` 服務並封鎖直到完成為止。 請勿在非同步程式碼中使用。

### <a name="server-streaming-call"></a>伺服器串流呼叫

伺服器串流呼叫會從傳送要求訊息的用戶端開始。 `ResponseStream.MoveNext()` 讀取從服務資料流程處理的訊息。 傳回時，伺服器串流呼叫已 `ResponseStream.MoveNext()` 完成 `false` 。

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

使用 c # 8 或更新版本時， `await foreach` 可以使用此語法來讀取訊息。 `IAsyncStreamReader<T>.ReadAllAsync()`擴充方法會讀取來自回應資料流程的所有訊息：

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>用戶端串流呼叫

用戶端串流呼叫會在沒有用戶端傳送訊息的 *情況下* 啟動。 用戶端可以選擇傳送訊息給 `RequestStream.WriteAsync` 。 當用戶端完成傳送訊息時， `RequestStream.CompleteAsync()` 應呼叫以通知服務。 當服務傳迴響應消息時，就會完成呼叫。

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

### <a name="bi-directional-streaming-call"></a>雙向串流呼叫

雙向串流呼叫會在沒有用戶端傳送訊息的 *情況下* 啟動。 用戶端可以選擇傳送訊息給 `RequestStream.WriteAsync` 。 從服務串流處理的訊息可使用 `ResponseStream.MoveNext()` 或存取 `ResponseStream.ReadAllAsync()` 。 當沒有其他訊息時，雙向串流呼叫就會完成 `ResponseStream` 。

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

為了獲得最佳效能，並避免用戶端與服務發生不必要的錯誤，請嘗試正常完成雙向串流呼叫。 當伺服器完成讀取要求資料流程，且用戶端已完成讀取回應串流時，雙向呼叫就會正常完成。 上述的範例呼叫是以正常結束的雙向呼叫範例之一。 在此呼叫中，用戶端：

1. 藉由呼叫來啟動新的雙向串流呼叫 `EchoClient.Echo` 。
2. 使用建立從服務讀取訊息的背景工作 `ResponseStream.ReadAllAsync()` 。
3. 使用將訊息傳送至伺服器 `RequestStream.WriteAsync` 。
4. 通知伺服器它已完成傳送訊息 `RequestStream.CompleteAsync()` 。
5. 等候背景工作讀取所有傳入的訊息。

在雙向串流呼叫期間，用戶端和服務可以隨時傳送訊息給彼此。 與雙向呼叫互動的最佳用戶端邏輯會根據服務邏輯而有所不同。

## <a name="access-grpc-trailers"></a>存取權 gRPC 尾端

gRPC 呼叫可能會傳回 gRPC 尾端。 gRPC 尾端用來提供關於呼叫的名稱/值中繼資料。 尾端提供類似于 HTTP 標頭的功能，但會在通話結束時收到。

您可以使用來存取 gRPC `GetTrailers()` 結尾，後者會傳回中繼資料的集合。 當回應完成之後，就會傳回尾端，因此，您必須先等候所有回應訊息，才能存取尾端。

一元和用戶端串流呼叫必須等待， `ResponseAsync` 才能呼叫 `GetTrailers()` ：

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

伺服器和雙向串流呼叫必須在呼叫之前完成回應資料流程的等候 `GetTrailers()` ：

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

您也可以從存取 gRPC 尾端 `RpcException` 。 服務可能會傳回尾端的 gRPC 狀態，並將其設定為不確定。 在此情況下，會從 gRPC 用戶端擲回的例外狀況中取出尾端：

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

## <a name="configure-deadline"></a>設定期限

建議您設定 gRPC 呼叫期限，因為它會提供呼叫可執行檔時間上限。 它會停止不正常的服務，使其無法執行永久和耗盡的伺服器資源。 期限是建立可靠應用程式的有用工具。

`CallOptions.Deadline`設定以設定 gRPC 呼叫的期限：

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

如需詳細資訊，請參閱<xref:grpc/deadlines-cancellation#deadlines>。

## <a name="additional-resources"></a>其他資源

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
