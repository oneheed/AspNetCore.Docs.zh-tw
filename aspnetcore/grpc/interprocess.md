---
title: 與 gRPC 的處理程序間通訊
author: jamesnk
description: 瞭解如何使用 gRPC 進行進程間的通訊。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: 8c0f8fb1468e61d5aa2e7f42cb5da33c01819124
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217462"
---
# <a name="inter-process-communication-with-grpc"></a>與 gRPC 的處理程序間通訊

依 [James 牛頓](https://twitter.com/jamesnk)

用戶端與服務之間的 gRPC 呼叫通常會透過 TCP 通訊端來傳送。 TCP 的設計目的是要在網路上進行通訊。 當用戶端和服務位於同一部電腦時， [ (IPC) 的處理序間通訊](https://wikipedia.org/wiki/Inter-process_communication)比 TCP 更有效率。 本檔說明如何搭配使用 gRPC 與 IPC 案例中的自訂傳輸。

## <a name="server-configuration"></a>伺服器組態

[Kestrel](xref:fundamentals/servers/kestrel)支援自訂傳輸。 Kestrel 是在 *Program.cs* 中設定：

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
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>使用指定路徑接聽[Unix 網域通訊端 (ud) ](https://wikipedia.org/wiki/Unix_domain_socket)的呼叫。

Kestrel 具有 ud 端點的內建支援。 在 Linux、macOS 和 [新式版本的 Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)上都支援 ud。

## <a name="client-configuration"></a>用戶端組態

`GrpcChannel` 支援透過自訂傳輸進行 gRPC 呼叫。 建立通道時，您可以使用 `SocketsHttpHandler` 具有自訂的來設定它 `ConnectCallback` 。 回呼可讓用戶端透過自訂傳輸進行連接，然後透過該傳輸傳送 HTTP 要求。

Unix 網域通訊端連接 factory 範例：

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

使用自訂連接處理站建立通道：

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

使用上述程式碼透過 Unix 網域通訊端傳送 gRPC 呼叫所建立的通道。 您可以使用 Kestrel 和中的擴充性來執行其他 IPC 技術的支援 `SocketsHttpHandler` 。
