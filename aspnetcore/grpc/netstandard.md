---
title: 搭配使用 gRPC 用戶端與 .NET Standard 2。0
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
ms.openlocfilehash: a6b066979dcdcdf648b8b0326bef47fe0e466266
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2021
ms.locfileid: "103422496"
---
# <a name="use-grpc-client-with-net-standard-20"></a><span data-ttu-id="51359-103">搭配使用 gRPC 用戶端與 .NET Standard 2。0</span><span class="sxs-lookup"><span data-stu-id="51359-103">Use gRPC client with .NET Standard 2.0</span></span>

<span data-ttu-id="51359-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="51359-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="51359-105">本文討論如何使用 .NET gRPC 用戶端搭配支援 [.Net Standard 2.0](/dotnet/standard/net-standard)的 .net 執行。</span><span class="sxs-lookup"><span data-stu-id="51359-105">This article discusses how to use the .NET gRPC client with .NET implementations that support [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span>

## <a name="net-implementations"></a><span data-ttu-id="51359-106">.NET 實作</span><span class="sxs-lookup"><span data-stu-id="51359-106">.NET implementations</span></span>

<span data-ttu-id="51359-107">下列 .NET 執行 (或更新版本) 支援 [Grpc .net](https://www.nuget.org/packages/Grpc.Net.Client/) ，但沒有 HTTP/2 的完整支援：</span><span class="sxs-lookup"><span data-stu-id="51359-107">The following .NET implementations (or later) support [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client/) but don't have full support for HTTP/2:</span></span>

* <span data-ttu-id="51359-108">.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="51359-108">.NET Core 2.1</span></span>
* <span data-ttu-id="51359-109">.NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="51359-109">.NET Framework 4.6.1</span></span>
* <span data-ttu-id="51359-110">Mono 5.4</span><span class="sxs-lookup"><span data-stu-id="51359-110">Mono 5.4</span></span>
* <span data-ttu-id="51359-111">Xamarin.iOS 10.14</span><span class="sxs-lookup"><span data-stu-id="51359-111">Xamarin.iOS 10.14</span></span>
* <span data-ttu-id="51359-112">Xamarin.Android 8.0</span><span class="sxs-lookup"><span data-stu-id="51359-112">Xamarin.Android 8.0</span></span>
* <span data-ttu-id="51359-113">通用 Windows 平台 10.0.16299</span><span class="sxs-lookup"><span data-stu-id="51359-113">Universal Windows Platform 10.0.16299</span></span>
* <span data-ttu-id="51359-114">Unity 2018。1</span><span class="sxs-lookup"><span data-stu-id="51359-114">Unity 2018.1</span></span>

<span data-ttu-id="51359-115">.NET gRPC 用戶端可以使用一些額外的設定，從這些 .NET 執行中呼叫服務。</span><span class="sxs-lookup"><span data-stu-id="51359-115">The .NET gRPC client can call services from these .NET implementations with some additional configuration.</span></span>

## <a name="httphandler-configuration"></a><span data-ttu-id="51359-116">HttpHandler 設定</span><span class="sxs-lookup"><span data-stu-id="51359-116">HttpHandler configuration</span></span>

<span data-ttu-id="51359-117">HTTP 提供者必須使用來設定 `GrpcChannelOptions.HttpHandler` 。</span><span class="sxs-lookup"><span data-stu-id="51359-117">An HTTP provider must be configured using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="51359-118">如果未設定處理常式，則會擲回錯誤：</span><span class="sxs-lookup"><span data-stu-id="51359-118">If a handler isn't configured, an error is thrown:</span></span>

> <span data-ttu-id="51359-119">`System.PlatformNotSupportedException`： gRPC 需要額外的設定，才能成功對不支援透過 HTTP/2 gRPC 的 .NET 執行進行 RPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="51359-119">`System.PlatformNotSupportedException`: gRPC requires extra configuration to successfully make RPC calls on .NET implementations that don't have support for gRPC over HTTP/2.</span></span> <span data-ttu-id="51359-120">HTTP 提供者必須使用指定 `GrpcChannelOptions.HttpHandler` 。</span><span class="sxs-lookup"><span data-stu-id="51359-120">An HTTP provider must be specified using `GrpcChannelOptions.HttpHandler`.</span></span> <span data-ttu-id="51359-121">設定的 HTTP 提供者必須支援 HTTP/2，或設定為使用 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="51359-121">The configured HTTP provider must either support HTTP/2 or be configured to use gRPC-Web.</span></span>

<span data-ttu-id="51359-122">不支援 HTTP/2 的 .NET 執行（例如 UWP、Xamarin 和 Unity）可以使用 gRPC-Web 作為替代方案。</span><span class="sxs-lookup"><span data-stu-id="51359-122">.NET implementations that don't support HTTP/2, such as UWP, Xamarin, and Unity, can use gRPC-Web as an alternative.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new GrpcWebHandler(new HttpClientHandler())
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

<span data-ttu-id="51359-123">如需詳細資訊，請參閱 [使用 .Net gRPC 用戶端設定 GRPC Web](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client)。</span><span class="sxs-lookup"><span data-stu-id="51359-123">For more information, see [Configure gRPC-Web with the .NET gRPC client](xref:grpc/browser#configure-grpc-web-with-the-net-grpc-client).</span></span>

## <a name="net-framework"></a><span data-ttu-id="51359-124">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="51359-124">.NET Framework</span></span>

<span data-ttu-id="51359-125">.NET Framework 對 gRPC over HTTP/2 的支援有限。</span><span class="sxs-lookup"><span data-stu-id="51359-125">.NET Framework has limited support for gRPC over HTTP/2.</span></span> <span data-ttu-id="51359-126">若要在 .NET Framework 上啟用透過 HTTP/2 的 gRPC，請設定要使用的通道 <xref:System.Net.Http.WinHttpHandler> 。</span><span class="sxs-lookup"><span data-stu-id="51359-126">To enable gRPC over HTTP/2 on .NET Framework, configure the channel to use <xref:System.Net.Http.WinHttpHandler>.</span></span>

<span data-ttu-id="51359-127">使用的需求和限制 `WinHttpHandler` ：</span><span class="sxs-lookup"><span data-stu-id="51359-127">Requirements and restrictions to using `WinHttpHandler`:</span></span>

* <span data-ttu-id="51359-128">Windows 10 組建19622或更新版本。</span><span class="sxs-lookup"><span data-stu-id="51359-128">Windows 10 Build 19622 or later.</span></span>
* <span data-ttu-id="51359-129">[WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet 套件的參考。</span><span class="sxs-lookup"><span data-stu-id="51359-129">A reference to the [System.Net.Http.WinHttpHandler](https://www.nuget.org/packages/System.Net.Http.WinHttpHandler/) NuGet package.</span></span>
* <span data-ttu-id="51359-130">僅支援一元和伺服器串流 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="51359-130">Only unary and server streaming gRPC calls are supported.</span></span>
* <span data-ttu-id="51359-131">僅支援透過 TLS 的 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="51359-131">Only gRPC calls over TLS are supported.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
    {
        HttpHandler = new WinHttpHandler()
    });

var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = ".NET" });
```

> [!NOTE]
> <span data-ttu-id="51359-132">.NET Framework 支援處於早期階段，需要使用發行前版本軟體。</span><span class="sxs-lookup"><span data-stu-id="51359-132">.NET Framework support is in its early stages and requires using pre-release software.</span></span>
> * <span data-ttu-id="51359-133">Windows 10 組建19622或更新版本是以 [windows](https://insider.windows.com/) 測試人員組建的形式提供。</span><span class="sxs-lookup"><span data-stu-id="51359-133">Windows 10 Build 19622 or later is available as a [Windows Insiders](https://insider.windows.com/) build.</span></span>
> * <span data-ttu-id="51359-134">`System.Net.Http.WinHttpHandler`NuGet.org 上目前無法使用所需的版本。您應使用[此 NuGet](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json)摘要上提供的最新發行前版本。</span><span class="sxs-lookup"><span data-stu-id="51359-134">The required version of `System.Net.Http.WinHttpHandler` is not currently available on NuGet.org. The latest pre-release version is available on [this NuGet feed](https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet6/nuget/v3/index.json) should be used.</span></span>

## <a name="grpc-c-core-library"></a><span data-ttu-id="51359-135">gRPC c # 核心-程式庫</span><span class="sxs-lookup"><span data-stu-id="51359-135">gRPC C# core-library</span></span>

<span data-ttu-id="51359-136">.NET Framework 和 Xamarin 的替代選項是使用 [GRPC c # core 程式庫](https://grpc.io/docs/languages/csharp/quickstart/) 來進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="51359-136">An alternative option for .NET Framework and Xamarin is to use [gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) to make gRPC calls.</span></span> <span data-ttu-id="51359-137">它是協力廠商程式庫，可支援在 .NET Framework 和 Xamarin 上透過 HTTP/2 進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="51359-137">It's a third party library that supports making gRPC calls over HTTP/2 on .NET Framework and Xamarin.</span></span> <span data-ttu-id="51359-138">Microsoft 不支援 gRPC C-核心。</span><span class="sxs-lookup"><span data-stu-id="51359-138">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51359-139">其他資源</span><span class="sxs-lookup"><span data-stu-id="51359-139">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/browser>
* [<span data-ttu-id="51359-140">gRPC c # 核心-程式庫</span><span class="sxs-lookup"><span data-stu-id="51359-140">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
