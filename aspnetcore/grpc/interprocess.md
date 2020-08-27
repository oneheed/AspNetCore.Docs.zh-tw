---
title: GRPC 間的處理序間通訊
author: jamesnk
description: 瞭解如何使用 gRPC 進行進程間的通訊。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945667"
---
# <a name="inter-process-communication-with-grpc"></a>GRPC 間的處理序間通訊

依 [James 牛頓](https://twitter.com/jamesnk)

用戶端與服務之間的 gRPC 呼叫通常會透過 TCP 通訊端來傳送。 TCP 很適合用來在網路上進行通訊，但是當用戶端與服務位於相同電腦上時， [ (IPC) 的處理序間通訊 ](https://wikipedia.org/wiki/Inter-process_communication) 會更有效率。 本檔說明如何搭配使用 gRPC 與 IPC 案例中的自訂傳輸。

## <a name="server-configuration"></a>伺服器組態

Kestrel 支援自訂傳輸。 Kestrel 是在 *Program.cs*中設定：

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

上述範例：

* 在中設定 Kestrel 的端點 `ConfigureKestrel` 。
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>使用指定路徑接聽[Unix 網域通訊端 (ud) ](https://en.wikipedia.org/wiki/Unix_domain_socket)的呼叫。

Kestrel 具有 ud 端點的內建支援。 在 Linux、macOS 和 [新式版本的 Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)上都支援 ud。

## <a name="client-configuration"></a>用戶端組態

`GrpcChannel` 支援透過自訂傳輸進行 gRPC 呼叫。 建立通道時，您可以使用 `SocketsHttpHandler` 具有自訂的來設定它 `ConnectionFactory` 。 Factory 可讓用戶端透過自訂傳輸進行連線，然後透過該傳輸來傳送 HTTP 要求。

> [!IMPORTANT]
> `ConnectionFactory` 是 .NET 5 候選版1中的新 API。

Unix 網域通訊端連接 factory 範例：

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

使用自訂連接處理站建立通道：

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

使用上述程式碼透過 Unix 網域通訊端傳送 gRPC 呼叫所建立的通道。
