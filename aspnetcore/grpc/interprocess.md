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
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="fccbc-103">GRPC 間的處理序間通訊</span><span class="sxs-lookup"><span data-stu-id="fccbc-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="fccbc-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="fccbc-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="fccbc-105">用戶端與服務之間的 gRPC 呼叫通常會透過 TCP 通訊端來傳送。</span><span class="sxs-lookup"><span data-stu-id="fccbc-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="fccbc-106">TCP 很適合用來在網路上進行通訊，但是當用戶端與服務位於相同電腦上時， [ (IPC) 的處理序間通訊 ](https://wikipedia.org/wiki/Inter-process_communication) 會更有效率。</span><span class="sxs-lookup"><span data-stu-id="fccbc-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="fccbc-107">本檔說明如何搭配使用 gRPC 與 IPC 案例中的自訂傳輸。</span><span class="sxs-lookup"><span data-stu-id="fccbc-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="fccbc-108">伺服器組態</span><span class="sxs-lookup"><span data-stu-id="fccbc-108">Server configuration</span></span>

<span data-ttu-id="fccbc-109">Kestrel 支援自訂傳輸。</span><span class="sxs-lookup"><span data-stu-id="fccbc-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="fccbc-110">Kestrel 是在 *Program.cs*中設定：</span><span class="sxs-lookup"><span data-stu-id="fccbc-110">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="fccbc-111">上述範例：</span><span class="sxs-lookup"><span data-stu-id="fccbc-111">The preceding example:</span></span>

* <span data-ttu-id="fccbc-112">在中設定 Kestrel 的端點 `ConfigureKestrel` 。</span><span class="sxs-lookup"><span data-stu-id="fccbc-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="fccbc-113"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>使用指定路徑接聽[Unix 網域通訊端 (ud) ](https://en.wikipedia.org/wiki/Unix_domain_socket)的呼叫。</span><span class="sxs-lookup"><span data-stu-id="fccbc-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="fccbc-114">Kestrel 具有 ud 端點的內建支援。</span><span class="sxs-lookup"><span data-stu-id="fccbc-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="fccbc-115">在 Linux、macOS 和 [新式版本的 Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)上都支援 ud。</span><span class="sxs-lookup"><span data-stu-id="fccbc-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="fccbc-116">用戶端組態</span><span class="sxs-lookup"><span data-stu-id="fccbc-116">Client configuration</span></span>

<span data-ttu-id="fccbc-117">`GrpcChannel` 支援透過自訂傳輸進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="fccbc-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="fccbc-118">建立通道時，您可以使用 `SocketsHttpHandler` 具有自訂的來設定它 `ConnectionFactory` 。</span><span class="sxs-lookup"><span data-stu-id="fccbc-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="fccbc-119">Factory 可讓用戶端透過自訂傳輸進行連線，然後透過該傳輸來傳送 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="fccbc-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fccbc-120">`ConnectionFactory` 是 .NET 5 候選版1中的新 API。</span><span class="sxs-lookup"><span data-stu-id="fccbc-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="fccbc-121">Unix 網域通訊端連接 factory 範例：</span><span class="sxs-lookup"><span data-stu-id="fccbc-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="fccbc-122">使用自訂連接處理站建立通道：</span><span class="sxs-lookup"><span data-stu-id="fccbc-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="fccbc-123">使用上述程式碼透過 Unix 網域通訊端傳送 gRPC 呼叫所建立的通道。</span><span class="sxs-lookup"><span data-stu-id="fccbc-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
