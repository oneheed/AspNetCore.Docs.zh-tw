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
# <a name="grpc-client-factory-integration-in-net"></a>.NET 中的 gRPC 用戶端工廠整合

依 [James 牛頓](https://twitter.com/jamesnk)

gRPC 整合 `HttpClientFactory` 可提供集中的方式來建立 gRPC 用戶端。 可以用來做為設定 [獨立 gRPC 用戶端實例](xref:grpc/client)的替代方案。 [Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 套件中提供 Factory 整合。

工廠提供下列優點：

* 提供設定邏輯 gRPC 用戶端實例的中心位置
* 管理基礎的存留期 `HttpClientMessageHandler`
* 在 ASP.NET Core gRPC 服務中自動傳播期限和取消

## <a name="register-grpc-clients"></a>註冊 gRPC 用戶端

若要註冊 gRPC 用戶端， `AddGrpcClient` 可以在中使用泛型擴充方法 `Startup.ConfigureServices` ，並指定 gRPC 具類型的用戶端類別和服務位址：

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

GRPC 用戶端類型會註冊為暫時性， (DI) 的相依性插入。 用戶端現在可以直接插入，也可以直接在 DI 所建立的類型中使用。 ASP.NET Core MVC 控制器、 SignalR 中樞和 gRPC 服務都是可自動插入 gRPC 用戶端的位置：

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

## <a name="configure-httphandler"></a>設定 HttpHandler

`HttpClientFactory` 建立 `HttpMessageHandler` gRPC 用戶端使用的。 標準 `HttpClientFactory` 方法可以用來新增外寄要求中介軟體或設定的基礎 `HttpClientHandler` `HttpClient` ：

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

如需詳細資訊，請參閱 [使用 IHttpClientFactory 提出 HTTP 要求](xref:fundamentals/http-requests)。

## <a name="configure-channel-and-interceptors"></a>設定通道和攔截器

gRPC 特有的方法可用於：

* 設定 gRPC 用戶端的基礎通道。
* 新增 `Interceptor` 用戶端在進行 gRPC 呼叫時將使用的實例。

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

## <a name="deadline-and-cancellation-propagation"></a>期限和取消傳播

gRPC 服務中的 factory 所建立的 gRPC 用戶端可以設定 `EnableCallContextPropagation()` 為，以自動將期限和取消權杖傳播至子呼叫。 `EnableCallContextPropagation()`擴充方法可在[Grpc. AspNetCore. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 套件中取得。

呼叫內容傳播的運作方式是從目前的 gRPC 要求內容中讀取期限和取消權杖，並自動將其傳播至 gRPC 用戶端所發出的撥出電話。 呼叫內容傳播是確保複雜的嵌套 gRPC 案例一律會傳播期限和取消的絕佳方法。

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

根據預設， `EnableCallContextPropagation` 如果用戶端是在 gRPC 呼叫的內容之外使用，則會引發錯誤。 此錯誤的設計目的是要警告您沒有要傳播的呼叫內容。 如果您想要在呼叫內容之外使用用戶端，請在設定用戶端時隱藏錯誤 `SuppressContextNotFoundErrors` ：

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

如需期限和 RPC 取消的詳細資訊，請參閱 <xref:grpc/deadlines-cancellation> 。

## <a name="additional-resources"></a>其他資源

* <xref:grpc/client>
* <xref:grpc/deadlines-cancellation>
* <xref:fundamentals/http-requests>
