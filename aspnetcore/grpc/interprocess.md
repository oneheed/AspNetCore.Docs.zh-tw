---
title: 與 gRPC 的處理程序間通訊
author: jamesnk
description: 瞭解如何使用 gRPC 進行進程間的通訊。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
ms.openlocfilehash: 34876f31cbc51ba66a91ae32ea6a5213dc34a369
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770151"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="67934-103">與 gRPC 的處理程序間通訊</span><span class="sxs-lookup"><span data-stu-id="67934-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="67934-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="67934-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="67934-105">用戶端與服務之間的 gRPC 呼叫通常會透過 TCP 通訊端來傳送。</span><span class="sxs-lookup"><span data-stu-id="67934-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="67934-106">TCP 的設計目的是要在網路上進行通訊。</span><span class="sxs-lookup"><span data-stu-id="67934-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="67934-107">當用戶端和服務位於同一部電腦時， [ (IPC) 的處理序間通訊](https://wikipedia.org/wiki/Inter-process_communication)比 TCP 更有效率。</span><span class="sxs-lookup"><span data-stu-id="67934-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="67934-108">本檔說明如何搭配使用 gRPC 與 IPC 案例中的自訂傳輸。</span><span class="sxs-lookup"><span data-stu-id="67934-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="67934-109">伺服器組態</span><span class="sxs-lookup"><span data-stu-id="67934-109">Server configuration</span></span>

<span data-ttu-id="67934-110">[Kestrel](xref:fundamentals/servers/kestrel)支援自訂傳輸。</span><span class="sxs-lookup"><span data-stu-id="67934-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="67934-111">Kestrel 是在 *Program.cs*中設定：</span><span class="sxs-lookup"><span data-stu-id="67934-111">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="67934-112">上述範例：</span><span class="sxs-lookup"><span data-stu-id="67934-112">The preceding example:</span></span>

* <span data-ttu-id="67934-113">在中設定 Kestrel 的端點 `ConfigureKestrel` 。</span><span class="sxs-lookup"><span data-stu-id="67934-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="67934-114"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>使用指定路徑接聽[Unix 網域通訊端 (ud) ](https://wikipedia.org/wiki/Unix_domain_socket)的呼叫。</span><span class="sxs-lookup"><span data-stu-id="67934-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="67934-115">Kestrel 具有 ud 端點的內建支援。</span><span class="sxs-lookup"><span data-stu-id="67934-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="67934-116">在 Linux、macOS 和 [新式版本的 Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)上都支援 ud。</span><span class="sxs-lookup"><span data-stu-id="67934-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="67934-117">用戶端組態</span><span class="sxs-lookup"><span data-stu-id="67934-117">Client configuration</span></span>

<span data-ttu-id="67934-118">`GrpcChannel` 支援透過自訂傳輸進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="67934-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="67934-119">建立通道時，您可以使用 `SocketsHttpHandler` 具有自訂的來設定它 `ConnectCallback` 。</span><span class="sxs-lookup"><span data-stu-id="67934-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="67934-120">回呼可讓用戶端透過自訂傳輸進行連接，然後透過該傳輸傳送 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="67934-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="67934-121">`SocketsHttpHandler.ConnectCallback` 是 .NET 5 候選版2中的新 API。</span><span class="sxs-lookup"><span data-stu-id="67934-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="67934-122">Unix 網域通訊端連接 factory 範例：</span><span class="sxs-lookup"><span data-stu-id="67934-122">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="67934-123">使用自訂連接處理站建立通道：</span><span class="sxs-lookup"><span data-stu-id="67934-123">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="67934-124">使用上述程式碼透過 Unix 網域通訊端傳送 gRPC 呼叫所建立的通道。</span><span class="sxs-lookup"><span data-stu-id="67934-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="67934-125">您可以使用 Kestrel 和中的擴充性來執行其他 IPC 技術的支援 `SocketsHttpHandler` 。</span><span class="sxs-lookup"><span data-stu-id="67934-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
