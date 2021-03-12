---
title: SignalR在背景服務中裝載 ASP.NET Core
author: bradygaster
description: 瞭解如何 SignalR 從 .Net Core BackgroundService 類別將訊息傳送至用戶端。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/background-services
ms.openlocfilehash: 9d95d7c9a33bcf2f4a603d815269752124133ee6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589695"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="82583-103">SignalR在背景服務中裝載 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82583-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="82583-104">依 [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="82583-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="82583-105">本文提供下列指引：</span><span class="sxs-lookup"><span data-stu-id="82583-105">This article provides guidance for:</span></span>

* <span data-ttu-id="82583-106">SignalR使用裝載于 ASP.NET Core 的背景工作進程來裝載中樞。</span><span class="sxs-lookup"><span data-stu-id="82583-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="82583-107">從 .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)中，將訊息傳送至已連線的用戶端。</span><span class="sxs-lookup"><span data-stu-id="82583-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="82583-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/background-service/samples/3.x) [ (如何下載) ](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="82583-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="82583-109">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/background-service/samples/2.2) [ (如何下載) ](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="82583-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="82583-110">SignalR在啟動時啟用</span><span class="sxs-lookup"><span data-stu-id="82583-110">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="82583-111">SignalR在背景工作進程的環境中裝載 ASP.NET 核心中樞，與在 ASP.NET Core web 應用程式中裝載中樞相同。</span><span class="sxs-lookup"><span data-stu-id="82583-111">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="82583-112">在 `Startup.ConfigureServices` 方法中，呼叫會 `services.AddSignalR` 將必要的服務加入至 ASP.NET 核心相依性插入 (DI) 層以支援 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="82583-112">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="82583-113">在中 `Startup.Configure` ， `MapHub` 會在回呼中呼叫方法 `UseEndpoints` ，以連接 ASP.NET 核心要求管線中的中樞端點。</span><span class="sxs-lookup"><span data-stu-id="82583-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="82583-114">SignalR在背景工作進程的環境中裝載 ASP.NET 核心中樞，與在 ASP.NET Core web 應用程式中裝載中樞相同。</span><span class="sxs-lookup"><span data-stu-id="82583-114">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="82583-115">在 `Startup.ConfigureServices` 方法中，呼叫會 `services.AddSignalR` 將必要的服務加入至 ASP.NET 核心相依性插入 (DI) 層以支援 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="82583-115">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="82583-116">在中 `Startup.Configure` ， `UseSignalR` 會呼叫方法，以連接 ASP.NET 核心要求管線中的中樞端點 (s) 。</span><span class="sxs-lookup"><span data-stu-id="82583-116">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="82583-117">在上述範例中，類別會實 `ClockHub` 作為 `Hub<T>` 建立強型別中樞的類別。</span><span class="sxs-lookup"><span data-stu-id="82583-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="82583-118">已 `ClockHub` 在類別中設定， `Startup` 以回應端點的要求 `/hubs/clock` 。</span><span class="sxs-lookup"><span data-stu-id="82583-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="82583-119">如需強型別中樞的詳細資訊，請參閱 [使用 SignalR ASP.NET Core 中的中樞](xref:signalr/hubs#strongly-typed-hubs)。</span><span class="sxs-lookup"><span data-stu-id="82583-119">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="82583-120">此功能不限於[中樞 \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1)類別。</span><span class="sxs-lookup"><span data-stu-id="82583-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="82583-121">任何繼承自 [中樞](xref:Microsoft.AspNetCore.SignalR.Hub)的類別（例如 [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)）都可以運作。</span><span class="sxs-lookup"><span data-stu-id="82583-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="82583-122">強型別所使用的介面 `ClockHub` 是 `IClock` 介面。</span><span class="sxs-lookup"><span data-stu-id="82583-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="82583-123">SignalR從背景服務呼叫中樞</span><span class="sxs-lookup"><span data-stu-id="82583-123">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="82583-124">在啟動期間， `Worker` `BackgroundService` 會使用來啟用類別（） `AddHostedService` 。</span><span class="sxs-lookup"><span data-stu-id="82583-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="82583-125">因為 SignalR 也會在 `Startup` 階段啟用，在此階段中，每個中樞會附加至 ASP.NET 核心的 HTTP 要求管線中的個別端點，因此每個中樞都會以伺服器上的來表示 `IHubContext<T>` 。</span><span class="sxs-lookup"><span data-stu-id="82583-125">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="82583-126">使用 ASP.NET Core 的 DI 功能時，裝載層（例如 `BackgroundService` 類別、MVC 控制器類別或頁面模型）所具現化的其他類別， Razor 可以藉由接受 `IHubContext<ClockHub, IClock>` 在結構內的實例來取得伺服器端中樞的參考。</span><span class="sxs-lookup"><span data-stu-id="82583-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="82583-127">在 `ExecuteAsync` 背景服務中反復呼叫方法時，會使用將伺服器的目前日期和時間傳送到連接的用戶端 `ClockHub` 。</span><span class="sxs-lookup"><span data-stu-id="82583-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="82583-128">SignalR使用背景服務回應事件</span><span class="sxs-lookup"><span data-stu-id="82583-128">React to SignalR events with background services</span></span>

<span data-ttu-id="82583-129">如同使用的 JavaScript 用戶端的單一頁面應用程式 SignalR ，或使用的 .net 桌面應用程式 <xref:signalr/dotnet-client> ， `BackgroundService` 或 `IHostedService` 執行也可以用來連接到 SignalR 中樞並回應事件。</span><span class="sxs-lookup"><span data-stu-id="82583-129">Like a Single Page App using the JavaScript client for SignalR, or a .NET desktop app using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="82583-130">`ClockHubClient`類別會同時執行 `IClock` 介面和 `IHostedService` 介面。</span><span class="sxs-lookup"><span data-stu-id="82583-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="82583-131">如此一來，就可以在中 `Startup` 持續執行，並從伺服器回應中樞事件。</span><span class="sxs-lookup"><span data-stu-id="82583-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="82583-132">在初始化期間，會 `ClockHubClient` 建立的實例， `HubConnection` 並啟用該 `IClock.ShowTime` 方法做為中樞事件的處理常式 `ShowTime` 。</span><span class="sxs-lookup"><span data-stu-id="82583-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="82583-133">在 `IHostedService.StartAsync` 執行 `HubConnection` 時，會以非同步方式啟動。</span><span class="sxs-lookup"><span data-stu-id="82583-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="82583-134">在 `IHostedService.StopAsync` 方法期間， `HubConnection` 會以非同步方式處置。</span><span class="sxs-lookup"><span data-stu-id="82583-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="82583-135">在 `IHostedService.StartAsync` 執行 `HubConnection` 時，會以非同步方式啟動。</span><span class="sxs-lookup"><span data-stu-id="82583-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="82583-136">在 `IHostedService.StopAsync` 方法期間， `HubConnection` 會以非同步方式處置。</span><span class="sxs-lookup"><span data-stu-id="82583-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="82583-137">其他資源</span><span class="sxs-lookup"><span data-stu-id="82583-137">Additional resources</span></span>

* [<span data-ttu-id="82583-138">開始使用</span><span class="sxs-lookup"><span data-stu-id="82583-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="82583-139">中樞</span><span class="sxs-lookup"><span data-stu-id="82583-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="82583-140">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="82583-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="82583-141">強型別中樞</span><span class="sxs-lookup"><span data-stu-id="82583-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
