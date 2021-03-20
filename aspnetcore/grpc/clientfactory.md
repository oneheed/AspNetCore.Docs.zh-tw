---
title: .NET 中的 gRPC 用戶端工廠整合
author: jamesnk
description: 瞭解如何使用用戶端 factory 建立 gRPC 用戶端。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/19/2021
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
uid: grpc/clientfactory
ms.openlocfilehash: ea5181bd44a5deafdc6634b31b9efeda2884b58c
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711499"
---
# <a name="grpc-client-factory-integration-in-net"></a><span data-ttu-id="c3735-103">.NET 中的 gRPC 用戶端工廠整合</span><span class="sxs-lookup"><span data-stu-id="c3735-103">gRPC client factory integration in .NET</span></span>

<span data-ttu-id="c3735-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="c3735-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="c3735-105">gRPC 整合 `HttpClientFactory` 可提供集中的方式來建立 gRPC 用戶端。</span><span class="sxs-lookup"><span data-stu-id="c3735-105">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="c3735-106">可以用來做為設定 [獨立 gRPC 用戶端實例](xref:grpc/client)的替代方案。</span><span class="sxs-lookup"><span data-stu-id="c3735-106">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="c3735-107">[Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 套件中提供 Factory 整合。</span><span class="sxs-lookup"><span data-stu-id="c3735-107">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="c3735-108">工廠提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="c3735-108">The factory offers the following benefits:</span></span>

* <span data-ttu-id="c3735-109">提供設定邏輯 gRPC 用戶端實例的中心位置</span><span class="sxs-lookup"><span data-stu-id="c3735-109">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="c3735-110">管理基礎的存留期 `HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="c3735-110">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="c3735-111">在 ASP.NET Core gRPC 服務中自動傳播期限和取消</span><span class="sxs-lookup"><span data-stu-id="c3735-111">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="c3735-112">註冊 gRPC 用戶端</span><span class="sxs-lookup"><span data-stu-id="c3735-112">Register gRPC clients</span></span>

<span data-ttu-id="c3735-113">若要註冊 gRPC 用戶端， `AddGrpcClient` 可以在中使用泛型擴充方法 `Startup.ConfigureServices` ，並指定 gRPC 具類型的用戶端類別和服務位址：</span><span class="sxs-lookup"><span data-stu-id="c3735-113">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="c3735-114">GRPC 用戶端類型會註冊為暫時性， (DI) 的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="c3735-114">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="c3735-115">用戶端現在可以直接插入，也可以直接在 DI 所建立的類型中使用。</span><span class="sxs-lookup"><span data-stu-id="c3735-115">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="c3735-116">ASP.NET Core MVC 控制器、 SignalR 中樞和 gRPC 服務都是可自動插入 gRPC 用戶端的位置：</span><span class="sxs-lookup"><span data-stu-id="c3735-116">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httphandler"></a><span data-ttu-id="c3735-117">設定 HttpHandler</span><span class="sxs-lookup"><span data-stu-id="c3735-117">Configure HttpHandler</span></span>

<span data-ttu-id="c3735-118">`HttpClientFactory` 建立 `HttpMessageHandler` gRPC 用戶端使用的。</span><span class="sxs-lookup"><span data-stu-id="c3735-118">`HttpClientFactory` creates the `HttpMessageHandler` used by the gRPC client.</span></span> <span data-ttu-id="c3735-119">標準 `HttpClientFactory` 方法可以用來新增外寄要求中介軟體或設定的基礎 `HttpClientHandler` `HttpClient` ：</span><span class="sxs-lookup"><span data-stu-id="c3735-119">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="c3735-120">如需詳細資訊，請參閱 [使用 IHttpClientFactory 提出 HTTP 要求](xref:fundamentals/http-requests)。</span><span class="sxs-lookup"><span data-stu-id="c3735-120">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="c3735-121">設定通道和攔截器</span><span class="sxs-lookup"><span data-stu-id="c3735-121">Configure Channel and Interceptors</span></span>

<span data-ttu-id="c3735-122">gRPC 特有的方法可用於：</span><span class="sxs-lookup"><span data-stu-id="c3735-122">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="c3735-123">設定 gRPC 用戶端的基礎通道。</span><span class="sxs-lookup"><span data-stu-id="c3735-123">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="c3735-124">新增 `Interceptor` 用戶端在進行 gRPC 呼叫時將使用的實例。</span><span class="sxs-lookup"><span data-stu-id="c3735-124">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="c3735-125">期限和取消傳播</span><span class="sxs-lookup"><span data-stu-id="c3735-125">Deadline and cancellation propagation</span></span>

<span data-ttu-id="c3735-126">gRPC 服務中的 factory 所建立的 gRPC 用戶端可以設定 `EnableCallContextPropagation()` 為，以自動將期限和取消權杖傳播至子呼叫。</span><span class="sxs-lookup"><span data-stu-id="c3735-126">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="c3735-127">`EnableCallContextPropagation()`擴充方法可在[Grpc. AspNetCore. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 套件中取得。</span><span class="sxs-lookup"><span data-stu-id="c3735-127">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="c3735-128">呼叫內容傳播的運作方式是從目前的 gRPC 要求內容中讀取期限和取消權杖，並自動將其傳播至 gRPC 用戶端所發出的撥出電話。</span><span class="sxs-lookup"><span data-stu-id="c3735-128">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="c3735-129">呼叫內容傳播是確保複雜的嵌套 gRPC 案例一律會傳播期限和取消的絕佳方法。</span><span class="sxs-lookup"><span data-stu-id="c3735-129">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="c3735-130">根據預設， `EnableCallContextPropagation` 如果用戶端是在 gRPC 呼叫的內容之外使用，則會引發錯誤。</span><span class="sxs-lookup"><span data-stu-id="c3735-130">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="c3735-131">此錯誤的設計目的是要警告您沒有要傳播的呼叫內容。</span><span class="sxs-lookup"><span data-stu-id="c3735-131">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="c3735-132">如果您想要在呼叫內容之外使用用戶端，請在設定用戶端時隱藏錯誤 `SuppressContextNotFoundErrors` ：</span><span class="sxs-lookup"><span data-stu-id="c3735-132">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="c3735-133">如需期限和 RPC 取消的詳細資訊，請參閱 <xref:grpc/deadlines-cancellation> 。</span><span class="sxs-lookup"><span data-stu-id="c3735-133">For more information about deadlines and RPC cancellation, see <xref:grpc/deadlines-cancellation>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3735-134">其他資源</span><span class="sxs-lookup"><span data-stu-id="c3735-134">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/deadlines-cancellation>
* <xref:fundamentals/http-requests>
