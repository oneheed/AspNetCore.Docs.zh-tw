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
# <a name="use-grpc-client-with-net-standard-20"></a><span data-ttu-id="91215-103">使用 gRPC 用戶端搭配 .NET Standard 2。0</span><span class="sxs-lookup"><span data-stu-id="91215-103">Use gRPC client with .NET Standard 2.0</span></span>

<span data-ttu-id="91215-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="91215-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="91215-105">本文討論如何使用 .NET gRPC 用戶端搭配支援 [.NET Standard 2.0](/dotnet/standard/net-standard)的 .net 執行。</span><span class="sxs-lookup"><span data-stu-id="91215-105">This article discusses how to use the .NET gRPC client with .NET implementations that support [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span>

## <a name="net-implementations"></a><span data-ttu-id="91215-106">.NET 實作</span><span class="sxs-lookup"><span data-stu-id="91215-106">.NET implementations</span></span>

<span data-ttu-id="91215-107">下列 .NET 執行 (或更新版本) 支援 [Grpc .net](https://www.nuget.org/packages/Grpc.Net.Client/) ，但沒有 HTTP/2 的完整支援：</span><span class="sxs-lookup"><span data-stu-id="91215-107">The following .NET implementations (or later) support [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) but don't have full support for HTTP/2:</span></span>

* <span data-ttu-id="91215-108">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="91215-108">.NET Core 2.1</span></span>
* <span data-ttu-id="91215-109">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="91215-109">.NET Framework 4.6.1</span></span>
* <span data-ttu-id="91215-110">Mono 5.4</span><span class="sxs-lookup"><span data-stu-id="91215-110">Mono 5.4</span></span>
* <span data-ttu-id="91215-111">Xamarin.iOS 10.14</span><span class="sxs-lookup"><span data-stu-id="91215-111">Xamarin.iOS 10.14</span></span>
* <span data-ttu-id="91215-112">Xamarin.Android 8.0</span><span class="sxs-lookup"><span data-stu-id="91215-112">Xamarin.Android 8.0</span></span>
* <span data-ttu-id="91215-113">通用 Windows 平台 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="91215-113">Universal Windows Platform 10.0.16299</span></span>
* <span data-ttu-id="91215-114">Unity 2018。1</span><span class="sxs-lookup"><span data-stu-id="91215-114">Unity 2018.1</span></span>

<span data-ttu-id="91215-115">.NET gRPC 用戶端可以使用一些額外的設定，從這些 .NET 執行中呼叫服務。</span><span class="sxs-lookup"><span data-stu-id="91215-115">The .NET gRPC client can call services from these .NET implementations with some additional configuration.</span></span>

## <a name="httphandler-configuration"></a><span data-ttu-id="91215-116">HttpHandler 設定</span><span class="sxs-lookup"><span data-stu-id="91215-116">HttpHandler configuration</span></span>

<span data-ttu-id="91215-117">HTTP 提供者必須使用來設定 `GrpcChannelOptions.HttpHandler` 。</span><span class="sxs-lookup"><span data-stu-id="91215-117">An HTTP provider must be configured using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="91215-118">如果未設定處理常式，則會擲回錯誤：</span><span class="sxs-lookup"><span data-stu-id="91215-118">If a handler isn't configured, an error is thrown:</span></span>

> <span data-ttu-id="91215-119">`System.PlatformNotSupportedException`： gRPC 需要額外的設定，才能成功對不支援透過 HTTP/2 gRPC 的 .NET 執行進行 RPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="91215-119">`System.PlatformNotSupportedException`: gRPC requires extra configuration to successfully make RPC calls on .NET implementations that don't have support for gRPC over HTTP/2.</span></span> <span data-ttu-id="91215-120">HTTP 提供者必須使用指定 `GrpcChannelOptions.HttpHandler` 。</span><span class="sxs-lookup"><span data-stu-id="91215-120">An HTTP provider must be specified using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="91215-121">設定的 HTTP 提供者必須支援 HTTP/2，或設定為使用 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="91215-121">The configured HTTP provider must either support HTTP/2 or be configured to use gRPC-Web.</span></span>

<span data-ttu-id="91215-122">不支援 HTTP/2 的 .NET 執行（例如 UWP、Xamarin 和 Unity）可以使用 gRPC-Web 作為替代方案。</span><span class="sxs-lookup"><span data-stu-id="91215-122">.NET implementations that don't support HTTP/2, such as UWP, Xamarin, and Unity, can use gRPC-Web as an alternative.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

<span data-ttu-id="91215-123">也可以使用 [gRPC 用戶端 factory](xref:grpc/clientfactory)來建立用戶端。</span><span class="sxs-lookup"><span data-stu-id="91215-123">Clients can also be created using the [gRPC client factory](xref:grpc/clientfactory).</span></span> <span data-ttu-id="91215-124">HTTP 提供者是使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 擴充方法來設定。</span><span class="sxs-lookup"><span data-stu-id="91215-124">An HTTP provider is configured using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>(options =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(new HttpClientHandler()));
```

<span data-ttu-id="91215-125">如需詳細資訊，請參閱 [使用 .Net gRPC 用戶端設定 GRPC Web](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client)。</span><span class="sxs-lookup"><span data-stu-id="91215-125">For more information, see [Configure gRPC-Web with the .NET gRPC client](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).</span></span>

## <a name="net-framework"></a><span data-ttu-id="91215-126">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="91215-126">.NET Framework</span></span>

<span data-ttu-id="91215-127">.NET Framework 對透過 HTTP/2 的 gRPC 有有限的支援。</span><span class="sxs-lookup"><span data-stu-id="91215-127">.NET Framework has limited support for gRPC over HTTP/2.</span></span> <span data-ttu-id="91215-128">若要在 .NET Framework 上啟用透過 HTTP/2 的 gRPC，請設定要使用的通道 <xref:System.Net.Http.WinHttpHandler> 。</span><span class="sxs-lookup"><span data-stu-id="91215-128">To enable gRPC over HTTP/2 on .NET Framework, configure the channel to use <xref:System.Net.Http.WinHttpHandler>.</span></span>

<span data-ttu-id="91215-129">使用的需求和限制 `WinHttpHandler` ：</span><span class="sxs-lookup"><span data-stu-id="91215-129">Requirements and restrictions to using `WinHttpHandler`:</span></span>

* <span data-ttu-id="91215-130">Windows 10 組建19622或更新版本。</span><span class="sxs-lookup"><span data-stu-id="91215-130">Windows 10 Build 19622 or later.</span></span>
* <span data-ttu-id="91215-131">[WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet 套件的參考。</span><span class="sxs-lookup"><span data-stu-id="91215-131">A reference to the [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet package.</span></span>
* <span data-ttu-id="91215-132">僅支援一元和伺服器串流 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="91215-132">Only unary and server streaming gRPC calls are supported.</span></span>
* <span data-ttu-id="91215-133">僅支援透過 TLS 的 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="91215-133">Only gRPC calls over TLS are supported.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> <span data-ttu-id="91215-134">.NET Framework 支援的初期階段，需要使用發行前版本的軟體。</span><span class="sxs-lookup"><span data-stu-id="91215-134">.NET Framework support is in its early stages and requires using pre-release software.</span></span>
> * <span data-ttu-id="91215-135">Windows 10 組建19622或更新版本是以 [Windows](https://insider.windows.com/) 測試人員組建的形式提供。</span><span class="sxs-lookup"><span data-stu-id="91215-135">Windows 10 Build 19622 or later is available as a [Windows Insiders](https://insider.windows.com/) build.</span></span>
> * <span data-ttu-id="91215-136">`System.Net.Http.WinHttpHandler`NuGet.org 上目前無法使用所需的版本。您應使用[此 NuGet](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json)摘要上提供的最新發行前版本。</span><span class="sxs-lookup"><span data-stu-id="91215-136">The required version of `System.Net.Http.WinHttpHandler` is not currently available on NuGet.org. The latest pre-release version is available on [this NuGet feed](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) should be used.</span></span>

## <a name="grpc-c-core-library"></a><span data-ttu-id="91215-137">gRPC c # 核心-程式庫</span><span class="sxs-lookup"><span data-stu-id="91215-137">gRPC C# core-library</span></span>

<span data-ttu-id="91215-138">.NET Framework 和 Xamarin 的替代選項是使用 [GRPC c # core 程式庫](https://grpc.io/docs/languages/csharp/quickstart/) 來進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="91215-138">An alternative option for .NET Framework and Xamarin is to use [gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) to make gRPC calls.</span></span> <span data-ttu-id="91215-139">它是協力廠商程式庫，可支援在 .NET Framework 和 Xamarin 上透過 HTTP/2 進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="91215-139">It's a third party library that supports making gRPC calls over HTTP/2 on .NET Framework and Xamarin.</span></span> <span data-ttu-id="91215-140">Microsoft 不支援 gRPC C-核心。</span><span class="sxs-lookup"><span data-stu-id="91215-140">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="91215-141">其他資源</span><span class="sxs-lookup"><span data-stu-id="91215-141">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/browser>
* [<span data-ttu-id="91215-142">gRPC c # 核心-程式庫</span><span class="sxs-lookup"><span data-stu-id="91215-142">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
