---
title: 使用 gRPC 的效能最佳作法
author: jamesnk
description: 瞭解建立高效能 gRPC 服務的最佳做法。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 09d959b7fee0b431bb36d5449402a442b2f453ae
ms.sourcegitcommit: 68df2a4d236f9f3299622ed38c75bb51cbdb4856
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2021
ms.locfileid: "107225536"
---
# <a name="performance-best-practices-with-grpc"></a>使用 gRPC 的效能最佳作法

依 [James 牛頓](https://twitter.com/jamesnk)

gRPC 是專為高效能服務所設計。 本檔說明如何從 gRPC 獲得最佳效能。

## <a name="reuse-grpc-channels"></a>重複使用 gRPC 通道

進行 gRPC 呼叫時，應重複使用 gRPC 通道。 重複使用通道可讓您透過現有的 HTTP/2 連接來對呼叫進行多工處理。

如果為每個 gRPC 呼叫建立新的通道，則完成所需的時間可能會大幅增加。 每次呼叫都需要用戶端與伺服器之間的多個網路往返，才能建立新的 HTTP/2 連線：

1. 開啟通訊端
2. 正在建立 TCP 連接
3. 協商 TLS
4. 正在啟動 HTTP/2 連接
5. 進行 gRPC 呼叫

您可以安全地在 gRPC 呼叫之間共用和重複使用通道：

* gRPC 用戶端會使用通道建立。 gRPC 用戶端是輕量物件，不需要快取或重複使用。
* 您可以從通道建立多個 gRPC 用戶端，包括不同類型的用戶端。
* 通道和從通道建立的用戶端可以安全地由多個執行緒使用。
* 從通道建立的用戶端可能會進行多個同時呼叫。

gRPC 用戶端 factory 提供集中的方式來設定通道。 它會自動重複使用基礎通道。 如需詳細資訊，請參閱<xref:grpc/clientfactory>。

## <a name="connection-concurrency"></a>並行連接

HTTP/2 連線通常會限制連接上 (使用中 [HTTP 要求的最大並行資料流程 ](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) 數目，) 一次連線。 根據預設，大部分的伺服器會將此限制設定為100的並行資料流程。

GRPC 通道會使用單一 HTTP/2 連線，而且並行呼叫會在該連接上多工。 當使用中的呼叫數目達到連接資料流程限制時，其他呼叫會在用戶端中排入佇列。 排入佇列的呼叫會等待使用中的呼叫完成，然後才傳送。 負載很高或長時間執行的串流 gRPC 呼叫的應用程式，可能會看到因為這項限制而導致呼叫佇列所造成的效能問題。

::: moniker range=">= aspnetcore-5.0"

.NET 5 引進了 `SocketsHttpHandler.EnableMultipleHttp2Connections` 屬性。 當設定為時 `true` ，當達到並行資料流程限制時，通道會建立額外的 HTTP/2 連接。 當建立時，系統 `GrpcChannel` 會自動將其內部 `SocketsHttpHandler` 設定為建立額外的 HTTP/2 連接。 如果應用程式設定自己的處理常式，請考慮將設定 `EnableMultipleHttp2Connections` 為 `true` ：

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

有幾個適用于 .NET Core 3.1 應用程式的因應措施：

* 針對負載高的應用程式區域建立個別的 gRPC 通道。 例如， `Logger` gRPC 服務可能會有較高的負載。 使用不同的通道 `LoggerClient` 在應用程式中建立。
* 例如，使用 gRPC 通道的集區，建立 gRPC 通道的清單。 `Random` 是在每次需要 gRPC 通道時，用來挑選清單中的通道。 使用 `Random` 隨機分配多個連接的呼叫。

> [!IMPORTANT]
> 增加伺服器上的最大並行串流限制是解決此問題的另一種方式。 在 Kestrel 中，這是使用來設定 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> 。
>
> 不建議增加最大並行串流限制。 單一 HTTP/2 連接上的資料流程過多會導致新的效能問題：
>
> * 嘗試寫入連接的資料流程之間的執行緒爭用。
> * 連接封包遺失會導致 TCP 層封鎖所有呼叫。

## <a name="load-balancing"></a>負載平衡

某些負載平衡器不會有效地與 gRPC 搭配運作。 L4 (傳輸) 負載平衡器會在連接層級運作，方法是將 TCP 連線分散到各個端點。 這種方法很適合用來載入使用 HTTP/1.1 進行的平衡 API 呼叫。 使用 HTTP/1.1 進行的並行呼叫會在不同的連線上傳送，讓呼叫能夠跨端點進行負載平衡。

因為 L4 負載平衡器會在連接層級運作，所以它們無法與 gRPC 搭配運作。 gRPC 使用 HTTP/2，它會在單一 TCP 連接上將分離信號多個呼叫。 所有透過該連接的 gRPC 呼叫都會移至一個端點。

有兩個選項可有效 gRPC 負載平衡：

* 用戶端負載平衡
* L7 (應用程式) proxy 負載平衡

> [!NOTE]
> 只有 gRPC 呼叫可以在端點之間進行負載平衡。 一旦建立串流 gRPC 呼叫，透過資料流程傳送的所有訊息都會移至一個端點。

### <a name="client-side-load-balancing"></a>用戶端負載平衡

使用用戶端負載平衡時，用戶端會知道端點。 針對每個 gRPC 呼叫，它會選取不同的端點來傳送呼叫。 當延遲很重要時，用戶端負載平衡是不錯的選擇。 用戶端與服務之間沒有 proxy，因此呼叫會直接傳送至服務。 用戶端負載平衡的缺點在於，每個用戶端都必須追蹤其應使用的可用端點。

對應用戶端負載平衡是一種技術，可將負載平衡狀態儲存在中央位置。 用戶端會定期查詢中央位置，以取得負載平衡決策時要使用的資訊。

`Grpc.Net.Client` 目前不支援用戶端負載平衡。 如果 .NET 需要用戶端負載平衡，則 Grpc 是不錯的選擇[。](https://www.nuget.org/packages/Grpc.Core)

### <a name="proxy-load-balancing"></a>Proxy 負載平衡

L7 (應用程式) proxy 的運作層級高於 L4 (傳輸) proxy。 L7 proxy 瞭解 HTTP/2，而且能夠將 gRPC 的呼叫分散至多個端點的一個 HTTP/2 連接上的 proxy。 使用 proxy 比用戶端負載平衡更為簡單，但可能會增加額外的延遲給 gRPC 呼叫。

有許多 L7 proxy 可用。 部分選項如下：

* [Envoy](https://www.envoyproxy.io/) -常用的開放原始碼 proxy。
* 適用于 Kubernetes 的[Linkerd](https://linkerd.io/)服務網格。
* [YARP：反向 Proxy](https://microsoft.github.io/reverse-proxy/) -以 .Net 撰寫的預覽開放原始碼 Proxy。

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a>內含式通訊

用戶端與服務之間的 gRPC 呼叫通常會透過 TCP 通訊端來傳送。 TCP 很適合用來在網路上進行通訊，但是當用戶端與服務位於相同電腦上時， [ (IPC) 的處理序間通訊 ](https://wikipedia.org/wiki/Inter-process_communication) 會更有效率。

請考慮使用類似 Unix 網域通訊端或具名管道的傳輸，在同一部電腦上的進程之間進行 gRPC 呼叫。 如需詳細資訊，請參閱<xref:grpc/interprocess>。

## <a name="keep-alive-pings"></a>保持作用中的 ping

保持作用中的 ping 可用來讓 HTTP/2 連接在非使用狀態期間保持運作。 當應用程式繼續活動時，擁有現有的 HTTP/2 連線可讓您快速進行初始 gRPC 呼叫，而不會因為重新建立連接而造成延遲。

保持作用中 ping 的設定 <xref:System.Net.Http.SocketsHttpHandler> ：

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

上述程式碼會設定通道，在沒有活動的期間，每隔60秒將「保持運作」 ping 傳送至伺服器。 Ping 可確保伺服器和使用中的任何 proxy 都不會因為無活動而關閉連接。

::: moniker-end

## <a name="streaming"></a>串流

gRPC 雙向串流可以用來取代高效能案例中的一元 gRPC 呼叫。 一旦啟動雙向串流之後，來回串流訊息的速度會比傳送具有多個一元 gRPC 呼叫的訊息更快。 經過資料流程處理的訊息會以現有 HTTP/2 要求的資料形式傳送，並消除為每個一元呼叫建立新 HTTP/2 要求的額外負荷。

範例服務：

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

範例用戶端：

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

基於效能考慮，使用雙向串流取代一元呼叫是先進的技巧，而且在許多情況下都不適用。

在下列情況中，使用串流呼叫是不錯的選擇：

1. 需要高輸送量或低延遲。
2. gRPC 和 HTTP/2 會識別為效能瓶頸。
3. 用戶端中的工作者正在傳送或接收具有 gRPC 服務的一般訊息。

請留意使用串流呼叫而非一元的額外複雜性和限制：

1. 資料流程可能會因服務或連接錯誤而中斷。 如果發生錯誤，則需要邏輯才能重新開機資料流程。
2. `RequestStream.WriteAsync` 對多重執行緒而言不安全。 一次只能將一個訊息寫入資料流程。 從多個執行緒透過單一資料流程傳送訊息時，需要生產者/取用者佇列 <xref:System.Threading.Channels.Channel%601> ，例如整理訊息。
3. GRPC 串流方法僅限於接收一種訊息類型，以及傳送一種訊息類型。 例如， `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` 接收 `RequestMessage` 和傳送 `ResponseMessage` 。 Protobuf 支援使用和的未知或條件式訊息 `Any` ， `oneof` 可解決這項限制。

## <a name="binary-payloads"></a>二進位裝載

Protobuf 具有純量數值型別的支援二進位承載 `bytes` 。 C # 中產生的屬性會使用 `ByteString` 做為屬性類型。

```protobuf
syntax = "proto3";

message PayloadResponse {
    bytes data = 1;
}  
```

Protobuf 是一種二進位格式，可有效率地將大型二進位裝載序列化為極短的負擔。  以文字為基礎的格式（例如 JSON [）需要編碼位元組至 base64](https://en.wikipedia.org/wiki/Base64) ，並且將33% 新增至訊息大小。

使用大型承載時， `ByteString` 有一些最佳作法可避免不必要的複製和配置，如下所述。

### <a name="send-binary-payloads"></a>傳送二進位承載

`ByteString` 通常會使用建立實例 `ByteString.CopyFrom(byte[] data)` 。 這個方法會配置新的 `ByteString` 和新的 `byte[]` 。 資料會複製到新的位元組陣列。

使用建立實例可以避免額外的配置和複本 `UnsafeByteOperations.UnsafeWrap(ReadOnlyMemory<byte> bytes)` `ByteString` 。

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = UnsafeByteOperations.UnsafeWrap(data);
```

位元組不會隨一起複製， `UnsafeByteOperations.UnsafeWrap` 因此 `ByteString` 在使用中時不得修改它們。

`UnsafeByteOperations.UnsafeWrap` 需要 [Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 版本3.15.0 或更新版本。

### <a name="read-binary-payloads"></a>讀取二進位承載

您可以 `ByteString` 使用 `ByteString.Memory` 和屬性，有效率地從實例讀取資料 `ByteString.Span` 。

```csharp
var byteString = UnsafeByteOperations.UnsafeWrap(new byte[] { 0, 1, 2 });
var data = byteString.Span;

for (var i = 0; i < data.Length; i++)
{
    Console.WriteLine(data[i]);
}
```

這些屬性可讓程式碼直接從沒有配置或複本的中讀取資料 `ByteString` 。

大部分的 .NET Api 都有和多載 `ReadOnlyMemory<byte>` `byte[]` ，因此 `ByteString.Memory` 是使用基礎資料的建議方式。 不過，在某些情況下，應用程式可能需要以位元組陣列的形式取得資料。 如果需要位元組陣列，則 <xref:System.Runtime.InteropServices.MemoryMarshal.TryGetArray%2A?displayProperty=nameWithType> 可以使用方法從取得陣列， `ByteString` 而不需要配置新的資料複本。

```csharp
var byteString = GetByteString();

ByteArrayContent content;
if (MemoryMarshal.TryGetArray(byteString.Memory, out var segment))
{
    // Success. Use the ByteString's underlying array.
    content = new ByteArrayContent(segment.Array, segment.Offset, segment.Count);
}
else
{
    // TryGetArray didn't succeed. Fall back to creating a copy of the data with ToByteArray.
    content = new ByteArrayContent(byteString.ToByteArray());
}

var httpRequest = new HttpRequestMessage();
httpRequest.Content = content;
```

上述程式碼：

* 嘗試從取得陣列 `ByteString.Memory` <xref:System.Runtime.InteropServices.MemoryMarshal.TryGetArray%2A?displayProperty=nameWithType> 。
* 如果成功抓取，則使用 `ArraySegment<byte>` 。 區段具有陣列、位移和計數的參考。
* 否則，會切換回以配置新的陣列 `ByteString.ToByteArray()` 。
