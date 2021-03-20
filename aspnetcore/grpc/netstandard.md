---
title: 使用 gRPC 用戶端搭配 .NET Standard 2。0
author: jamesnk
description: 瞭解如何在支援 .NET Standard 2.0 的應用程式和程式庫中使用 .NET gRPC 用戶端。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 3/11/2021
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
uid: grpc/netstandard
ms.openlocfilehash: b3df1d3b5565dc5c03988c29d2befbe1ea164f54
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711476"
---
# <a name="use-grpc-client-with-net-standard-20"></a>使用 gRPC 用戶端搭配 .NET Standard 2。0

依 [James 牛頓](https://twitter.com/jamesnk)

本文討論如何使用 .NET gRPC 用戶端搭配支援 [.NET Standard 2.0](/dotnet/standard/net-standard)的 .net 執行。

## <a name="net-implementations"></a>.NET 實作

下列 .NET 執行 (或更新版本) 支援 [Grpc .net](https://www.nuget.org/packages/Grpc.Net.Client/) ，但沒有 HTTP/2 的完整支援：

* .NET Core 2.1
* .NET Framework 4.6.1
* Mono 5.4
* Xamarin.iOS 10.14
* Xamarin.Android 8.0
* 通用 Windows 平台 10.0.16299
* Unity 2018。1

.NET gRPC 用戶端可以使用一些額外的設定，從這些 .NET 執行中呼叫服務。

## <a name="httphandler-configuration"></a>HttpHandler 設定

HTTP 提供者必須使用來設定 `GrpcChannelOptions.HttpHandler` 。 如果未設定處理常式，則會擲回錯誤：

> `System.PlatformNotSupportedException`： gRPC 需要額外的設定，才能成功對不支援透過 HTTP/2 gRPC 的 .NET 執行進行 RPC 呼叫。 HTTP 提供者必須使用指定 `GrpcChannelOptions.HttpHandler` 。 設定的 HTTP 提供者必須支援 HTTP/2，或設定為使用 gRPC Web。

不支援 HTTP/2 的 .NET 執行（例如 UWP、Xamarin 和 Unity）可以使用 gRPC-Web 作為替代方案。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

也可以使用 [gRPC 用戶端 factory](xref:grpc/clientfactory)來建立用戶端。 HTTP 提供者是使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 擴充方法來設定。

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>(options =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(new HttpClientHandler()));
```

如需詳細資訊，請參閱 [使用 .Net gRPC 用戶端設定 GRPC Web](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client)。

## <a name="net-framework"></a>.NET Framework

.NET Framework 對透過 HTTP/2 的 gRPC 有有限的支援。 若要在 .NET Framework 上啟用透過 HTTP/2 的 gRPC，請設定要使用的通道 <xref:System.Net.Http.WinHttpHandler> 。

使用的需求和限制 `WinHttpHandler` ：

* Windows 10 組建19622或更新版本。
* [WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet 套件的參考。
* 僅支援一元和伺服器串流 gRPC 呼叫。
* 僅支援透過 TLS 的 gRPC 呼叫。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> .NET Framework 支援的初期階段，需要使用發行前版本的軟體。
> * Windows 10 組建19622或更新版本是以 [Windows](https://insider.windows.com/) 測試人員組建的形式提供。
> * `System.Net.Http.WinHttpHandler`NuGet.org 上目前無法使用所需的版本。您應使用[此 NuGet](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json)摘要上提供的最新發行前版本。

## <a name="grpc-c-core-library"></a>gRPC c # 核心-程式庫

.NET Framework 和 Xamarin 的替代選項是使用 [GRPC c # core 程式庫](https://grpc.io/docs/languages/csharp/quickstart/) 來進行 gRPC 呼叫。 它是協力廠商程式庫，可支援在 .NET Framework 和 Xamarin 上透過 HTTP/2 進行 gRPC 呼叫。 Microsoft 不支援 gRPC C-核心。

## <a name="additional-resources"></a>其他資源

* <xref:grpc/client>
* <xref:grpc/browser>
* [gRPC c # 核心-程式庫](https://grpc.io/docs/languages/csharp/quickstart/)
