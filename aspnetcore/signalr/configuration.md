---
title: ASP.NET Core SignalR 設定
author: bradygaster
description: 瞭解如何設定 ASP.NET Core SignalR 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393869"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="965b3-103">ASP.NET Core SignalR 設定</span><span class="sxs-lookup"><span data-stu-id="965b3-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="965b3-104">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="965b3-105">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="965b3-106">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="965b3-107">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="965b3-108">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="965b3-109">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="965b3-110">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="965b3-111">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="965b3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="965b3-112">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="965b3-113">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="965b3-114">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="965b3-115">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="965b3-116">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="965b3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="965b3-117">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="965b3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="965b3-118">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-118">MessagePack serialization options</span></span>

<span data-ttu-id="965b3-119">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="965b3-120">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-121">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="965b3-122">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="965b3-122">Configure server options</span></span>

<span data-ttu-id="965b3-123">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="965b3-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="965b3-124">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-124">Option</span></span> | <span data-ttu-id="965b3-125">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-125">Default Value</span></span> | <span data-ttu-id="965b3-126">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="965b3-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-127">30 seconds</span></span> | <span data-ttu-id="965b3-128">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="965b3-129">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="965b3-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="965b3-130">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="965b3-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-131">15 seconds</span></span> | <span data-ttu-id="965b3-132">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="965b3-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="965b3-133">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-134">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-135">15 seconds</span></span> | <span data-ttu-id="965b3-136">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="965b3-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="965b3-137">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="965b3-138">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="965b3-139">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="965b3-139">All installed protocols</span></span> | <span data-ttu-id="965b3-140">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-140">Protocols supported by this hub.</span></span> <span data-ttu-id="965b3-141">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="965b3-142">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="965b3-143">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="965b3-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="965b3-144">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="965b3-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="965b3-145">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="965b3-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="965b3-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-146">32 KB</span></span> | <span data-ttu-id="965b3-147">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="965b3-148">1</span><span class="sxs-lookup"><span data-stu-id="965b3-148">1</span></span> | <span data-ttu-id="965b3-149">每個用戶端在進行佇列之前可以平行呼叫的中樞方法數目上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="965b3-150">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="965b3-151">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="965b3-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="965b3-152">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="965b3-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="965b3-153">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="965b3-154">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="965b3-155">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="965b3-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="965b3-156">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-156">Option</span></span> | <span data-ttu-id="965b3-157">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-157">Default Value</span></span> | <span data-ttu-id="965b3-158">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="965b3-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-159">32 KB</span></span> | <span data-ttu-id="965b3-160">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="965b3-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="965b3-161">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="965b3-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="965b3-162">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="965b3-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="965b3-163">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="965b3-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="965b3-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-164">32 KB</span></span> | <span data-ttu-id="965b3-165">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="965b3-166">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="965b3-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="965b3-167">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="965b3-167">All Transports are enabled.</span></span> | <span data-ttu-id="965b3-168">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="965b3-169">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-169">See below.</span></span> | <span data-ttu-id="965b3-170">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="965b3-171">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-171">See below.</span></span> | <span data-ttu-id="965b3-172">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="965b3-173">0</span><span class="sxs-lookup"><span data-stu-id="965b3-173">0</span></span> | <span data-ttu-id="965b3-174">指定 negotiate 通訊協定的最小版本。</span><span class="sxs-lookup"><span data-stu-id="965b3-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="965b3-175">這是用來將用戶端限制在較新的版本。</span><span class="sxs-lookup"><span data-stu-id="965b3-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="965b3-176">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="965b3-177">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-177">Option</span></span> | <span data-ttu-id="965b3-178">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-178">Default Value</span></span> | <span data-ttu-id="965b3-179">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="965b3-180">90秒</span><span class="sxs-lookup"><span data-stu-id="965b3-180">90 seconds</span></span> | <span data-ttu-id="965b3-181">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="965b3-182">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="965b3-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="965b3-183">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="965b3-184">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-184">Option</span></span> | <span data-ttu-id="965b3-185">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-185">Default Value</span></span> | <span data-ttu-id="965b3-186">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="965b3-187">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-187">5 seconds</span></span> | <span data-ttu-id="965b3-188">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="965b3-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="965b3-189">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="965b3-190">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="965b3-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="965b3-191">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="965b3-191">Configure client options</span></span>

<span data-ttu-id="965b3-192">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="965b3-193">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="965b3-194">設定記錄</span><span class="sxs-lookup"><span data-stu-id="965b3-194">Configure logging</span></span>

<span data-ttu-id="965b3-195">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="965b3-196">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="965b3-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="965b3-197">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-198">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="965b3-199">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="965b3-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="965b3-200">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="965b3-201">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="965b3-202">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="965b3-203">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="965b3-204">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="965b3-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="965b3-205">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="965b3-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="965b3-206">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="965b3-207">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-207">The following table lists the available log levels.</span></span> <span data-ttu-id="965b3-208">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="965b3-209">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="965b3-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="965b3-210">String</span><span class="sxs-lookup"><span data-stu-id="965b3-210">String</span></span>                      | <span data-ttu-id="965b3-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="965b3-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="965b3-212">`info` **或** `information`</span><span class="sxs-lookup"><span data-stu-id="965b3-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="965b3-213">`warn` **或** `warning`</span><span class="sxs-lookup"><span data-stu-id="965b3-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="965b3-214">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="965b3-215">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="965b3-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="965b3-216">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="965b3-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="965b3-217">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="965b3-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="965b3-218">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="965b3-219">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="965b3-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="965b3-220">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="965b3-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="965b3-221">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="965b3-221">Configure allowed transports</span></span>

<span data-ttu-id="965b3-222">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="965b3-223">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="965b3-224">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-224">All transports are enabled by default.</span></span>

<span data-ttu-id="965b3-225">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="965b3-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="965b3-226">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="965b3-227">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="965b3-228">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="965b3-229">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="965b3-230">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="965b3-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="965b3-231">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="965b3-231">Configure bearer authentication</span></span>

<span data-ttu-id="965b3-232">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="965b3-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="965b3-233">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="965b3-234">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="965b3-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="965b3-235">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="965b3-236">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="965b3-237">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="965b3-238">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="965b3-239">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="965b3-240">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="965b3-241">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="965b3-242">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="965b3-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-243">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-244">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-244">Option</span></span> | <span data-ttu-id="965b3-245">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-245">Default value</span></span> | <span data-ttu-id="965b3-246">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="965b3-247">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-248">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-248">Timeout for server activity.</span></span> <span data-ttu-id="965b3-249">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-250">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-251">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="965b3-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-252">15 seconds</span></span> | <span data-ttu-id="965b3-253">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-254">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-255">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-256">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-257">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-257">15 seconds</span></span> | <span data-ttu-id="965b3-258">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-259">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-260">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="965b3-261">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="965b3-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="965b3-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-263">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-263">Option</span></span> | <span data-ttu-id="965b3-264">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-264">Default value</span></span> | <span data-ttu-id="965b3-265">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="965b3-266">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-267">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-267">Timeout for server activity.</span></span> <span data-ttu-id="965b3-268">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="965b3-269">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-270">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="965b3-271">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="965b3-272">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-273">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-274">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-275">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-275">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-276">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-276">Option</span></span> | <span data-ttu-id="965b3-277">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-277">Default value</span></span> | <span data-ttu-id="965b3-278">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="965b3-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="965b3-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="965b3-280">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-281">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-281">Timeout for server activity.</span></span> <span data-ttu-id="965b3-282">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-283">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-284">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="965b3-285">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-285">15 seconds</span></span> | <span data-ttu-id="965b3-286">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-287">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-288">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-289">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="965b3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="965b3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="965b3-291">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="965b3-292">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-293">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-294">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="965b3-295">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="965b3-295">Configure additional options</span></span>

<span data-ttu-id="965b3-296">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-297">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-298">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-298">.NET Option</span></span> |  <span data-ttu-id="965b3-299">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-299">Default value</span></span> | <span data-ttu-id="965b3-300">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="965b3-301">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="965b3-302">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-303">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-304">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="965b3-305">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-305">Empty</span></span> | <span data-ttu-id="965b3-306">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="965b3-307">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-307">Empty</span></span> | <span data-ttu-id="965b3-308">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="965b3-309">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-309">Empty</span></span> | <span data-ttu-id="965b3-310">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="965b3-311">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-311">5 seconds</span></span> | <span data-ttu-id="965b3-312">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="965b3-312">WebSockets only.</span></span> <span data-ttu-id="965b3-313">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="965b3-314">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="965b3-315">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-315">Empty</span></span> | <span data-ttu-id="965b3-316">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="965b3-317">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="965b3-318">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="965b3-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="965b3-319">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="965b3-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="965b3-320">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="965b3-321">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="965b3-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="965b3-322">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="965b3-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="965b3-323">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="965b3-324">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="965b3-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="965b3-325">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="965b3-326">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="965b3-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-328">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-328">JavaScript Option</span></span> | <span data-ttu-id="965b3-329">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-329">Default Value</span></span> | <span data-ttu-id="965b3-330">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="965b3-331">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="965b3-332">每個 HTTP 要求傳送的標頭字典。</span><span class="sxs-lookup"><span data-stu-id="965b3-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="965b3-333">在瀏覽器中傳送標頭不適用於 Websocket 或 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 串流。</span><span class="sxs-lookup"><span data-stu-id="965b3-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="965b3-334">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="965b3-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="965b3-335">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-336">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-337">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="965b3-338">指定是否將使用 CORS 要求傳送認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="965b3-339">Azure App Service 會使用 cookie 來進行粘滯話，且必須啟用此選項才能正確運作。</span><span class="sxs-lookup"><span data-stu-id="965b3-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="965b3-340">如需 CORS 與的詳細資訊 SignalR ，請參閱 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="965b3-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-341">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-341">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-342">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-342">Java Option</span></span> | <span data-ttu-id="965b3-343">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-343">Default Value</span></span> | <span data-ttu-id="965b3-344">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="965b3-345">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="965b3-346">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-347">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-348">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="965b3-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="965b3-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="965b3-350">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-350">Empty</span></span> | <span data-ttu-id="965b3-351">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="965b3-352">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="965b3-353">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="965b3-354">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="965b3-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="965b3-355">其他資源</span><span class="sxs-lookup"><span data-stu-id="965b3-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="965b3-356">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="965b3-357">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="965b3-358">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="965b3-359">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="965b3-360">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="965b3-361">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="965b3-362">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="965b3-363">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="965b3-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="965b3-364">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="965b3-365">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="965b3-366">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="965b3-367">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="965b3-368">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="965b3-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="965b3-369">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="965b3-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="965b3-370">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-370">MessagePack serialization options</span></span>

<span data-ttu-id="965b3-371">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="965b3-372">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-373">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="965b3-374">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="965b3-374">Configure server options</span></span>

<span data-ttu-id="965b3-375">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="965b3-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="965b3-376">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-376">Option</span></span> | <span data-ttu-id="965b3-377">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-377">Default Value</span></span> | <span data-ttu-id="965b3-378">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="965b3-379">30 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-379">30 seconds</span></span> | <span data-ttu-id="965b3-380">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="965b3-381">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="965b3-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="965b3-382">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="965b3-383">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-383">15 seconds</span></span> | <span data-ttu-id="965b3-384">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="965b3-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="965b3-385">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-386">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-387">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-387">15 seconds</span></span> | <span data-ttu-id="965b3-388">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="965b3-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="965b3-389">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="965b3-390">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="965b3-391">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="965b3-391">All installed protocols</span></span> | <span data-ttu-id="965b3-392">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-392">Protocols supported by this hub.</span></span> <span data-ttu-id="965b3-393">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="965b3-394">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="965b3-395">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="965b3-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="965b3-396">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="965b3-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="965b3-397">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="965b3-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="965b3-398">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-398">32 KB</span></span> | <span data-ttu-id="965b3-399">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="965b3-400">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="965b3-401">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="965b3-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="965b3-402">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="965b3-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="965b3-403">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="965b3-404">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="965b3-405">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="965b3-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="965b3-406">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-406">Option</span></span> | <span data-ttu-id="965b3-407">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-407">Default Value</span></span> | <span data-ttu-id="965b3-408">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="965b3-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-409">32 KB</span></span> | <span data-ttu-id="965b3-410">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="965b3-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="965b3-411">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="965b3-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="965b3-412">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="965b3-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="965b3-413">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="965b3-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="965b3-414">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-414">32 KB</span></span> | <span data-ttu-id="965b3-415">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="965b3-416">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="965b3-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="965b3-417">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="965b3-417">All Transports are enabled.</span></span> | <span data-ttu-id="965b3-418">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="965b3-419">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-419">See below.</span></span> | <span data-ttu-id="965b3-420">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="965b3-421">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-421">See below.</span></span> | <span data-ttu-id="965b3-422">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="965b3-423">0</span><span class="sxs-lookup"><span data-stu-id="965b3-423">0</span></span> | <span data-ttu-id="965b3-424">指定 negotiate 通訊協定的最小版本。</span><span class="sxs-lookup"><span data-stu-id="965b3-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="965b3-425">這是用來將用戶端限制在較新的版本。</span><span class="sxs-lookup"><span data-stu-id="965b3-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="965b3-426">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="965b3-427">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-427">Option</span></span> | <span data-ttu-id="965b3-428">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-428">Default Value</span></span> | <span data-ttu-id="965b3-429">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="965b3-430">90秒</span><span class="sxs-lookup"><span data-stu-id="965b3-430">90 seconds</span></span> | <span data-ttu-id="965b3-431">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="965b3-432">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="965b3-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="965b3-433">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="965b3-434">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-434">Option</span></span> | <span data-ttu-id="965b3-435">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-435">Default Value</span></span> | <span data-ttu-id="965b3-436">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="965b3-437">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-437">5 seconds</span></span> | <span data-ttu-id="965b3-438">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="965b3-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="965b3-439">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="965b3-440">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="965b3-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="965b3-441">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="965b3-441">Configure client options</span></span>

<span data-ttu-id="965b3-442">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="965b3-443">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="965b3-444">設定記錄</span><span class="sxs-lookup"><span data-stu-id="965b3-444">Configure logging</span></span>

<span data-ttu-id="965b3-445">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="965b3-446">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="965b3-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="965b3-447">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-448">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="965b3-449">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="965b3-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="965b3-450">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="965b3-451">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="965b3-452">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="965b3-453">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="965b3-454">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="965b3-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="965b3-455">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="965b3-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="965b3-456">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="965b3-457">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-457">The following table lists the available log levels.</span></span> <span data-ttu-id="965b3-458">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="965b3-459">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="965b3-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="965b3-460">String</span><span class="sxs-lookup"><span data-stu-id="965b3-460">String</span></span>                      | <span data-ttu-id="965b3-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="965b3-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="965b3-462">`info` **或** `information`</span><span class="sxs-lookup"><span data-stu-id="965b3-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="965b3-463">`warn` **或** `warning`</span><span class="sxs-lookup"><span data-stu-id="965b3-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="965b3-464">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="965b3-465">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="965b3-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="965b3-466">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="965b3-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="965b3-467">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="965b3-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="965b3-468">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="965b3-469">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="965b3-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="965b3-470">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="965b3-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="965b3-471">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="965b3-471">Configure allowed transports</span></span>

<span data-ttu-id="965b3-472">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="965b3-473">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="965b3-474">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-474">All transports are enabled by default.</span></span>

<span data-ttu-id="965b3-475">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="965b3-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="965b3-476">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="965b3-477">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="965b3-478">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="965b3-479">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="965b3-480">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="965b3-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="965b3-481">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="965b3-481">Configure bearer authentication</span></span>

<span data-ttu-id="965b3-482">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="965b3-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="965b3-483">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="965b3-484">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="965b3-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="965b3-485">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="965b3-486">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="965b3-487">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="965b3-488">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="965b3-489">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="965b3-490">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="965b3-491">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="965b3-492">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="965b3-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-493">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-494">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-494">Option</span></span> | <span data-ttu-id="965b3-495">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-495">Default value</span></span> | <span data-ttu-id="965b3-496">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="965b3-497">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-498">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-498">Timeout for server activity.</span></span> <span data-ttu-id="965b3-499">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-500">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-501">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="965b3-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-502">15 seconds</span></span> | <span data-ttu-id="965b3-503">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-504">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-505">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-506">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-507">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-507">15 seconds</span></span> | <span data-ttu-id="965b3-508">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-509">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-510">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="965b3-511">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="965b3-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="965b3-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-513">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-513">Option</span></span> | <span data-ttu-id="965b3-514">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-514">Default value</span></span> | <span data-ttu-id="965b3-515">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="965b3-516">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-517">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-517">Timeout for server activity.</span></span> <span data-ttu-id="965b3-518">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="965b3-519">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-520">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="965b3-521">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="965b3-522">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-523">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-524">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-525">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-525">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-526">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-526">Option</span></span> | <span data-ttu-id="965b3-527">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-527">Default value</span></span> | <span data-ttu-id="965b3-528">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="965b3-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="965b3-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="965b3-530">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-531">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-531">Timeout for server activity.</span></span> <span data-ttu-id="965b3-532">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-533">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-534">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="965b3-535">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-535">15 seconds</span></span> | <span data-ttu-id="965b3-536">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-537">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-538">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-539">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="965b3-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="965b3-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="965b3-541">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="965b3-542">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-543">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-544">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="965b3-545">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="965b3-545">Configure additional options</span></span>

<span data-ttu-id="965b3-546">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-547">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-548">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-548">.NET Option</span></span> |  <span data-ttu-id="965b3-549">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-549">Default value</span></span> | <span data-ttu-id="965b3-550">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="965b3-551">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="965b3-552">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-553">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-554">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="965b3-555">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-555">Empty</span></span> | <span data-ttu-id="965b3-556">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="965b3-557">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-557">Empty</span></span> | <span data-ttu-id="965b3-558">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="965b3-559">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-559">Empty</span></span> | <span data-ttu-id="965b3-560">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="965b3-561">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-561">5 seconds</span></span> | <span data-ttu-id="965b3-562">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="965b3-562">WebSockets only.</span></span> <span data-ttu-id="965b3-563">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="965b3-564">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="965b3-565">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-565">Empty</span></span> | <span data-ttu-id="965b3-566">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="965b3-567">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="965b3-568">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="965b3-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="965b3-569">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="965b3-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="965b3-570">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="965b3-571">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="965b3-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="965b3-572">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="965b3-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="965b3-573">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="965b3-574">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="965b3-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="965b3-575">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="965b3-576">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="965b3-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-578">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-578">JavaScript Option</span></span> | <span data-ttu-id="965b3-579">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-579">Default Value</span></span> | <span data-ttu-id="965b3-580">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="965b3-581">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="965b3-582">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="965b3-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="965b3-583">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-584">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-585">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-586">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-586">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-587">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-587">Java Option</span></span> | <span data-ttu-id="965b3-588">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-588">Default Value</span></span> | <span data-ttu-id="965b3-589">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="965b3-590">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="965b3-591">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-592">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-593">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="965b3-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="965b3-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="965b3-595">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-595">Empty</span></span> | <span data-ttu-id="965b3-596">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="965b3-597">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="965b3-598">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="965b3-599">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="965b3-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="965b3-600">其他資源</span><span class="sxs-lookup"><span data-stu-id="965b3-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="965b3-601">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="965b3-602">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="965b3-603">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="965b3-604">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="965b3-605">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="965b3-606">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="965b3-607">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="965b3-608">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="965b3-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="965b3-609">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="965b3-610">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="965b3-611">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="965b3-612">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="965b3-613">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="965b3-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="965b3-614">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="965b3-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="965b3-615">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-615">MessagePack serialization options</span></span>

<span data-ttu-id="965b3-616">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="965b3-617">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-618">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="965b3-619">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="965b3-619">Configure server options</span></span>

<span data-ttu-id="965b3-620">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="965b3-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="965b3-621">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-621">Option</span></span> | <span data-ttu-id="965b3-622">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-622">Default Value</span></span> | <span data-ttu-id="965b3-623">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="965b3-624">30 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-624">30 seconds</span></span> | <span data-ttu-id="965b3-625">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="965b3-626">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="965b3-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="965b3-627">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="965b3-628">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-628">15 seconds</span></span> | <span data-ttu-id="965b3-629">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="965b3-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="965b3-630">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-631">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-632">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-632">15 seconds</span></span> | <span data-ttu-id="965b3-633">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="965b3-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="965b3-634">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="965b3-635">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="965b3-636">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="965b3-636">All installed protocols</span></span> | <span data-ttu-id="965b3-637">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-637">Protocols supported by this hub.</span></span> <span data-ttu-id="965b3-638">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="965b3-639">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="965b3-640">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="965b3-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="965b3-641">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="965b3-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="965b3-642">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="965b3-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="965b3-643">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-643">32 KB</span></span> | <span data-ttu-id="965b3-644">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="965b3-645">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="965b3-646">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="965b3-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="965b3-647">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="965b3-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="965b3-648">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="965b3-649">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="965b3-650">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="965b3-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="965b3-651">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-651">Option</span></span> | <span data-ttu-id="965b3-652">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-652">Default Value</span></span> | <span data-ttu-id="965b3-653">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="965b3-654">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-654">32 KB</span></span> | <span data-ttu-id="965b3-655">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="965b3-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="965b3-656">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="965b3-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="965b3-657">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="965b3-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="965b3-658">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="965b3-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="965b3-659">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-659">32 KB</span></span> | <span data-ttu-id="965b3-660">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="965b3-661">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="965b3-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="965b3-662">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="965b3-662">All Transports are enabled.</span></span> | <span data-ttu-id="965b3-663">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="965b3-664">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-664">See below.</span></span> | <span data-ttu-id="965b3-665">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="965b3-666">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-666">See below.</span></span> | <span data-ttu-id="965b3-667">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="965b3-668">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="965b3-669">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-669">Option</span></span> | <span data-ttu-id="965b3-670">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-670">Default Value</span></span> | <span data-ttu-id="965b3-671">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="965b3-672">90秒</span><span class="sxs-lookup"><span data-stu-id="965b3-672">90 seconds</span></span> | <span data-ttu-id="965b3-673">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="965b3-674">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="965b3-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="965b3-675">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="965b3-676">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-676">Option</span></span> | <span data-ttu-id="965b3-677">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-677">Default Value</span></span> | <span data-ttu-id="965b3-678">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="965b3-679">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-679">5 seconds</span></span> | <span data-ttu-id="965b3-680">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="965b3-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="965b3-681">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="965b3-682">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="965b3-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="965b3-683">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="965b3-683">Configure client options</span></span>

<span data-ttu-id="965b3-684">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="965b3-685">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="965b3-686">設定記錄</span><span class="sxs-lookup"><span data-stu-id="965b3-686">Configure logging</span></span>

<span data-ttu-id="965b3-687">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="965b3-688">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="965b3-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="965b3-689">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-690">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="965b3-691">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="965b3-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="965b3-692">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="965b3-693">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="965b3-694">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="965b3-695">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="965b3-696">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="965b3-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="965b3-697">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="965b3-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="965b3-698">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="965b3-699">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-699">The following table lists the available log levels.</span></span> <span data-ttu-id="965b3-700">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="965b3-701">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="965b3-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="965b3-702">String</span><span class="sxs-lookup"><span data-stu-id="965b3-702">String</span></span>                      | <span data-ttu-id="965b3-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="965b3-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="965b3-704">`info` **或** `information`</span><span class="sxs-lookup"><span data-stu-id="965b3-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="965b3-705">`warn` **或** `warning`</span><span class="sxs-lookup"><span data-stu-id="965b3-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="965b3-706">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="965b3-707">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="965b3-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="965b3-708">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="965b3-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="965b3-709">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="965b3-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="965b3-710">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="965b3-711">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="965b3-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="965b3-712">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="965b3-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="965b3-713">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="965b3-713">Configure allowed transports</span></span>

<span data-ttu-id="965b3-714">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="965b3-715">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="965b3-716">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-716">All transports are enabled by default.</span></span>

<span data-ttu-id="965b3-717">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="965b3-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="965b3-718">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="965b3-719">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="965b3-720">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="965b3-721">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="965b3-722">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="965b3-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="965b3-723">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="965b3-723">Configure bearer authentication</span></span>

<span data-ttu-id="965b3-724">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="965b3-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="965b3-725">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="965b3-726">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="965b3-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="965b3-727">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="965b3-728">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="965b3-729">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="965b3-730">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="965b3-731">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="965b3-732">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="965b3-733">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="965b3-734">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="965b3-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-735">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-736">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-736">Option</span></span> | <span data-ttu-id="965b3-737">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-737">Default value</span></span> | <span data-ttu-id="965b3-738">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="965b3-739">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-740">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-740">Timeout for server activity.</span></span> <span data-ttu-id="965b3-741">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-742">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-743">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="965b3-744">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-744">15 seconds</span></span> | <span data-ttu-id="965b3-745">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-746">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-747">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-748">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-749">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-749">15 seconds</span></span> | <span data-ttu-id="965b3-750">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-751">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-752">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="965b3-753">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="965b3-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="965b3-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-755">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-755">Option</span></span> | <span data-ttu-id="965b3-756">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-756">Default value</span></span> | <span data-ttu-id="965b3-757">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="965b3-758">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-759">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-759">Timeout for server activity.</span></span> <span data-ttu-id="965b3-760">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="965b3-761">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-762">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="965b3-763">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="965b3-764">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-765">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-766">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-767">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-767">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-768">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-768">Option</span></span> | <span data-ttu-id="965b3-769">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-769">Default value</span></span> | <span data-ttu-id="965b3-770">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="965b3-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="965b3-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="965b3-772">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-773">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-773">Timeout for server activity.</span></span> <span data-ttu-id="965b3-774">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-775">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-776">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="965b3-777">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-777">15 seconds</span></span> | <span data-ttu-id="965b3-778">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-779">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-780">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-781">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="965b3-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="965b3-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="965b3-783">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="965b3-784">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-785">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-786">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="965b3-787">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="965b3-787">Configure additional options</span></span>

<span data-ttu-id="965b3-788">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-789">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-790">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-790">.NET Option</span></span> |  <span data-ttu-id="965b3-791">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-791">Default value</span></span> | <span data-ttu-id="965b3-792">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="965b3-793">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="965b3-794">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-795">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-796">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="965b3-797">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-797">Empty</span></span> | <span data-ttu-id="965b3-798">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="965b3-799">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-799">Empty</span></span> | <span data-ttu-id="965b3-800">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="965b3-801">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-801">Empty</span></span> | <span data-ttu-id="965b3-802">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="965b3-803">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-803">5 seconds</span></span> | <span data-ttu-id="965b3-804">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="965b3-804">WebSockets only.</span></span> <span data-ttu-id="965b3-805">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="965b3-806">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="965b3-807">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-807">Empty</span></span> | <span data-ttu-id="965b3-808">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="965b3-809">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="965b3-810">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="965b3-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="965b3-811">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="965b3-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="965b3-812">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="965b3-813">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="965b3-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="965b3-814">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="965b3-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="965b3-815">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="965b3-816">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="965b3-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="965b3-817">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="965b3-818">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="965b3-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-820">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-820">JavaScript Option</span></span> | <span data-ttu-id="965b3-821">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-821">Default Value</span></span> | <span data-ttu-id="965b3-822">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="965b3-823">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="965b3-824">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="965b3-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="965b3-825">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-826">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-827">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-828">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-828">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-829">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-829">Java Option</span></span> | <span data-ttu-id="965b3-830">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-830">Default Value</span></span> | <span data-ttu-id="965b3-831">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="965b3-832">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="965b3-833">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-834">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-835">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="965b3-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="965b3-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="965b3-837">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-837">Empty</span></span> | <span data-ttu-id="965b3-838">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="965b3-839">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="965b3-840">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="965b3-841">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="965b3-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="965b3-842">其他資源</span><span class="sxs-lookup"><span data-stu-id="965b3-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="965b3-843">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="965b3-844">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="965b3-845">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="965b3-846">您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 SignalR 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="965b3-847">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="965b3-848">該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="965b3-849">如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="965b3-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="965b3-850">舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="965b3-851">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="965b3-852">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="965b3-853">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="965b3-854">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-854">MessagePack serialization options</span></span>

<span data-ttu-id="965b3-855">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="965b3-856">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-857">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="965b3-858">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="965b3-858">Configure server options</span></span>

<span data-ttu-id="965b3-859">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="965b3-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="965b3-860">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-860">Option</span></span> | <span data-ttu-id="965b3-861">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-861">Default Value</span></span> | <span data-ttu-id="965b3-862">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="965b3-863">30 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-863">30 seconds</span></span> | <span data-ttu-id="965b3-864">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="965b3-865">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="965b3-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="965b3-866">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="965b3-867">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-867">15 seconds</span></span> | <span data-ttu-id="965b3-868">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="965b3-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="965b3-869">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-870">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-871">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-871">15 seconds</span></span> | <span data-ttu-id="965b3-872">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="965b3-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="965b3-873">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="965b3-874">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="965b3-875">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="965b3-875">All installed protocols</span></span> | <span data-ttu-id="965b3-876">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-876">Protocols supported by this hub.</span></span> <span data-ttu-id="965b3-877">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="965b3-878">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="965b3-879">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="965b3-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="965b3-880">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="965b3-881">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="965b3-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="965b3-882">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="965b3-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="965b3-883">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="965b3-884">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="965b3-885">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="965b3-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="965b3-886">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-886">Option</span></span> | <span data-ttu-id="965b3-887">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-887">Default Value</span></span> | <span data-ttu-id="965b3-888">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="965b3-889">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-889">32 KB</span></span> | <span data-ttu-id="965b3-890">從伺服器緩衝的用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="965b3-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="965b3-891">提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="965b3-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="965b3-892">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="965b3-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="965b3-893">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="965b3-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="965b3-894">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-894">32 KB</span></span> | <span data-ttu-id="965b3-895">由伺服器緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="965b3-896">提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="965b3-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="965b3-897">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="965b3-897">All Transports are enabled.</span></span> | <span data-ttu-id="965b3-898">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="965b3-899">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-899">See below.</span></span> | <span data-ttu-id="965b3-900">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="965b3-901">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-901">See below.</span></span> | <span data-ttu-id="965b3-902">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="965b3-903">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="965b3-904">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-904">Option</span></span> | <span data-ttu-id="965b3-905">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-905">Default Value</span></span> | <span data-ttu-id="965b3-906">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="965b3-907">90秒</span><span class="sxs-lookup"><span data-stu-id="965b3-907">90 seconds</span></span> | <span data-ttu-id="965b3-908">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="965b3-909">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="965b3-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="965b3-910">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="965b3-911">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-911">Option</span></span> | <span data-ttu-id="965b3-912">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-912">Default Value</span></span> | <span data-ttu-id="965b3-913">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="965b3-914">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-914">5 seconds</span></span> | <span data-ttu-id="965b3-915">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="965b3-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="965b3-916">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="965b3-917">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="965b3-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="965b3-918">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="965b3-918">Configure client options</span></span>

<span data-ttu-id="965b3-919">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="965b3-920">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="965b3-921">設定記錄</span><span class="sxs-lookup"><span data-stu-id="965b3-921">Configure logging</span></span>

<span data-ttu-id="965b3-922">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="965b3-923">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="965b3-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="965b3-924">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-925">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="965b3-926">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="965b3-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="965b3-927">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="965b3-928">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="965b3-929">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="965b3-930">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="965b3-931">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="965b3-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="965b3-932">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="965b3-933">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="965b3-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="965b3-934">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="965b3-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="965b3-935">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="965b3-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="965b3-936">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="965b3-937">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="965b3-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="965b3-938">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="965b3-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="965b3-939">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="965b3-939">Configure allowed transports</span></span>

<span data-ttu-id="965b3-940">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="965b3-941">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="965b3-942">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-942">All transports are enabled by default.</span></span>

<span data-ttu-id="965b3-943">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="965b3-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="965b3-944">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="965b3-945">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="965b3-946">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="965b3-946">Configure bearer authentication</span></span>

<span data-ttu-id="965b3-947">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="965b3-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="965b3-948">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="965b3-949">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="965b3-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="965b3-950">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="965b3-951">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="965b3-952">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="965b3-953">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="965b3-954">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="965b3-955">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="965b3-956">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="965b3-957">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="965b3-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-958">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-959">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-959">Option</span></span> | <span data-ttu-id="965b3-960">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-960">Default value</span></span> | <span data-ttu-id="965b3-961">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="965b3-962">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-963">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-963">Timeout for server activity.</span></span> <span data-ttu-id="965b3-964">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-965">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-966">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="965b3-967">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-967">15 seconds</span></span> | <span data-ttu-id="965b3-968">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-969">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-970">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-971">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-972">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-972">15 seconds</span></span> | <span data-ttu-id="965b3-973">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-974">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-975">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="965b3-976">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="965b3-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="965b3-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-978">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-978">Option</span></span> | <span data-ttu-id="965b3-979">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-979">Default value</span></span> | <span data-ttu-id="965b3-980">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="965b3-981">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-982">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-982">Timeout for server activity.</span></span> <span data-ttu-id="965b3-983">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="965b3-984">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-985">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="965b3-986">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="965b3-987">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-988">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-989">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-990">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-990">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-991">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-991">Option</span></span> | <span data-ttu-id="965b3-992">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-992">Default value</span></span> | <span data-ttu-id="965b3-993">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="965b3-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="965b3-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="965b3-995">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-996">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-996">Timeout for server activity.</span></span> <span data-ttu-id="965b3-997">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-998">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-999">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="965b3-1000">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1000">15 seconds</span></span> | <span data-ttu-id="965b3-1001">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-1002">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-1003">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-1004">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="965b3-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="965b3-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="965b3-1006">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="965b3-1007">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="965b3-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="965b3-1008">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="965b3-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="965b3-1009">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="965b3-1010">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1010">Configure additional options</span></span>

<span data-ttu-id="965b3-1011">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-1013">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1013">.NET Option</span></span> |  <span data-ttu-id="965b3-1014">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1014">Default value</span></span> | <span data-ttu-id="965b3-1015">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="965b3-1016">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="965b3-1017">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-1018">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-1019">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="965b3-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1020">Empty</span></span> | <span data-ttu-id="965b3-1021">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="965b3-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1022">Empty</span></span> | <span data-ttu-id="965b3-1023">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="965b3-1024">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1024">Empty</span></span> | <span data-ttu-id="965b3-1025">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="965b3-1026">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1026">5 seconds</span></span> | <span data-ttu-id="965b3-1027">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="965b3-1027">WebSockets only.</span></span> <span data-ttu-id="965b3-1028">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="965b3-1029">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="965b3-1030">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1030">Empty</span></span> | <span data-ttu-id="965b3-1031">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="965b3-1032">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="965b3-1033">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="965b3-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="965b3-1034">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="965b3-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="965b3-1035">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="965b3-1036">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="965b3-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="965b3-1037">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="965b3-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="965b3-1038">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="965b3-1039">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="965b3-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="965b3-1040">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="965b3-1041">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="965b3-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-1043">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1043">JavaScript Option</span></span> | <span data-ttu-id="965b3-1044">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1044">Default Value</span></span> | <span data-ttu-id="965b3-1045">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="965b3-1046">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="965b3-1047">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="965b3-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="965b3-1048">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-1049">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-1050">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-1051">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-1052">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1052">Java Option</span></span> | <span data-ttu-id="965b3-1053">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1053">Default Value</span></span> | <span data-ttu-id="965b3-1054">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="965b3-1055">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="965b3-1056">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-1057">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-1058">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="965b3-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="965b3-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="965b3-1060">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1060">Empty</span></span> | <span data-ttu-id="965b3-1061">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="965b3-1062">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="965b3-1063">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="965b3-1064">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="965b3-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="965b3-1065">其他資源</span><span class="sxs-lookup"><span data-stu-id="965b3-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="965b3-1066">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="965b3-1067">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="965b3-1068">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="965b3-1069">您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 SignalR 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="965b3-1070">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="965b3-1071">該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="965b3-1072">如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="965b3-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="965b3-1073">舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="965b3-1074">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="965b3-1075">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="965b3-1076">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="965b3-1077">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1077">MessagePack serialization options</span></span>

<span data-ttu-id="965b3-1078">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="965b3-1079">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-1080">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="965b3-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="965b3-1081">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1081">Configure server options</span></span>

<span data-ttu-id="965b3-1082">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="965b3-1083">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1083">Option</span></span> | <span data-ttu-id="965b3-1084">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1084">Default Value</span></span> | <span data-ttu-id="965b3-1085">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="965b3-1086">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1086">15 seconds</span></span> | <span data-ttu-id="965b3-1087">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="965b3-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="965b3-1088">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-1089">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="965b3-1090">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1090">15 seconds</span></span> | <span data-ttu-id="965b3-1091">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="965b3-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="965b3-1092">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="965b3-1093">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="965b3-1094">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="965b3-1094">All installed protocols</span></span> | <span data-ttu-id="965b3-1095">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="965b3-1096">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="965b3-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="965b3-1097">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="965b3-1098">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="965b3-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="965b3-1099">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="965b3-1100">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="965b3-1101">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="965b3-1102">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="965b3-1103">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="965b3-1104">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="965b3-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="965b3-1105">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1105">Option</span></span> | <span data-ttu-id="965b3-1106">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1106">Default Value</span></span> | <span data-ttu-id="965b3-1107">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="965b3-1108">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-1108">32 KB</span></span> | <span data-ttu-id="965b3-1109">從伺服器緩衝的用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="965b3-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="965b3-1110">提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="965b3-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="965b3-1111">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="965b3-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="965b3-1112">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="965b3-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="965b3-1113">32 KB</span><span class="sxs-lookup"><span data-stu-id="965b3-1113">32 KB</span></span> | <span data-ttu-id="965b3-1114">由伺服器緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="965b3-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="965b3-1115">提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="965b3-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="965b3-1116">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="965b3-1116">All Transports are enabled.</span></span> | <span data-ttu-id="965b3-1117">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="965b3-1118">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-1118">See below.</span></span> | <span data-ttu-id="965b3-1119">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="965b3-1120">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="965b3-1120">See below.</span></span> | <span data-ttu-id="965b3-1121">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="965b3-1122">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="965b3-1123">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1123">Option</span></span> | <span data-ttu-id="965b3-1124">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1124">Default Value</span></span> | <span data-ttu-id="965b3-1125">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="965b3-1126">90秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1126">90 seconds</span></span> | <span data-ttu-id="965b3-1127">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="965b3-1128">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="965b3-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="965b3-1129">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="965b3-1130">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1130">Option</span></span> | <span data-ttu-id="965b3-1131">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1131">Default Value</span></span> | <span data-ttu-id="965b3-1132">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="965b3-1133">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1133">5 seconds</span></span> | <span data-ttu-id="965b3-1134">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="965b3-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="965b3-1135">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="965b3-1136">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="965b3-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="965b3-1137">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1137">Configure client options</span></span>

<span data-ttu-id="965b3-1138">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="965b3-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="965b3-1139">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="965b3-1140">設定記錄</span><span class="sxs-lookup"><span data-stu-id="965b3-1140">Configure logging</span></span>

<span data-ttu-id="965b3-1141">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="965b3-1142">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="965b3-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="965b3-1143">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="965b3-1144">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="965b3-1145">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="965b3-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="965b3-1146">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="965b3-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="965b3-1147">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="965b3-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="965b3-1148">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="965b3-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="965b3-1149">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="965b3-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="965b3-1150">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="965b3-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="965b3-1151">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="965b3-1152">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="965b3-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="965b3-1153">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="965b3-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="965b3-1154">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="965b3-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="965b3-1155">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="965b3-1156">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="965b3-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="965b3-1157">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="965b3-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="965b3-1158">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="965b3-1158">Configure allowed transports</span></span>

<span data-ttu-id="965b3-1159">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="965b3-1160">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="965b3-1161">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="965b3-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="965b3-1162">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="965b3-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="965b3-1163">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="965b3-1164">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="965b3-1164">Configure bearer authentication</span></span>

<span data-ttu-id="965b3-1165">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="965b3-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="965b3-1166">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="965b3-1167">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="965b3-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="965b3-1168">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="965b3-1169">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="965b3-1170">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="965b3-1171">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="965b3-1172">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="965b3-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="965b3-1173">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="965b3-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="965b3-1174">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="965b3-1175">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="965b3-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-1177">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1177">Option</span></span> | <span data-ttu-id="965b3-1178">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1178">Default value</span></span> | <span data-ttu-id="965b3-1179">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="965b3-1180">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-1181">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-1181">Timeout for server activity.</span></span> <span data-ttu-id="965b3-1182">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-1183">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-1184">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="965b3-1185">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1185">15 seconds</span></span> | <span data-ttu-id="965b3-1186">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-1187">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="965b3-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="965b3-1188">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-1189">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="965b3-1190">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="965b3-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="965b3-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-1192">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1192">Option</span></span> | <span data-ttu-id="965b3-1193">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1193">Default value</span></span> | <span data-ttu-id="965b3-1194">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="965b3-1195">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-1196">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-1196">Timeout for server activity.</span></span> <span data-ttu-id="965b3-1197">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="965b3-1198">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-1199">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-1200">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-1201">選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1201">Option</span></span> | <span data-ttu-id="965b3-1202">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1202">Default value</span></span> | <span data-ttu-id="965b3-1203">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="965b3-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="965b3-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="965b3-1205">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="965b3-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="965b3-1206">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-1206">Timeout for server activity.</span></span> <span data-ttu-id="965b3-1207">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-1208">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="965b3-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="965b3-1209">建議的值是至少兩個伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="965b3-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="965b3-1210">15 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1210">15 seconds</span></span> | <span data-ttu-id="965b3-1211">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="965b3-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="965b3-1212">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="965b3-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="965b3-1213">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="965b3-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="965b3-1214">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="965b3-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="965b3-1215">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1215">Configure additional options</span></span>

<span data-ttu-id="965b3-1216">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="965b3-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="965b3-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="965b3-1218">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1218">.NET Option</span></span> |  <span data-ttu-id="965b3-1219">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1219">Default value</span></span> | <span data-ttu-id="965b3-1220">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="965b3-1221">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="965b3-1222">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-1223">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-1224">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="965b3-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1225">Empty</span></span> | <span data-ttu-id="965b3-1226">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="965b3-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1227">Empty</span></span> | <span data-ttu-id="965b3-1228">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="965b3-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="965b3-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1229">Empty</span></span> | <span data-ttu-id="965b3-1230">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="965b3-1231">5 秒</span><span class="sxs-lookup"><span data-stu-id="965b3-1231">5 seconds</span></span> | <span data-ttu-id="965b3-1232">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="965b3-1232">WebSockets only.</span></span> <span data-ttu-id="965b3-1233">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="965b3-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="965b3-1234">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="965b3-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="965b3-1235">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1235">Empty</span></span> | <span data-ttu-id="965b3-1236">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="965b3-1237">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="965b3-1238">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="965b3-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="965b3-1239">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="965b3-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="965b3-1240">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="965b3-1241">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="965b3-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="965b3-1242">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="965b3-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="965b3-1243">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="965b3-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="965b3-1244">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="965b3-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="965b3-1245">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="965b3-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="965b3-1246">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="965b3-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="965b3-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="965b3-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="965b3-1248">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1248">JavaScript Option</span></span> | <span data-ttu-id="965b3-1249">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1249">Default Value</span></span> | <span data-ttu-id="965b3-1250">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="965b3-1251">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="965b3-1252">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="965b3-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="965b3-1253">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-1254">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-1255">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="965b3-1256">Java</span><span class="sxs-lookup"><span data-stu-id="965b3-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="965b3-1257">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="965b3-1257">Java Option</span></span> | <span data-ttu-id="965b3-1258">預設值</span><span class="sxs-lookup"><span data-stu-id="965b3-1258">Default Value</span></span> | <span data-ttu-id="965b3-1259">描述</span><span class="sxs-lookup"><span data-stu-id="965b3-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="965b3-1260">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="965b3-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="965b3-1261">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="965b3-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="965b3-1262">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="965b3-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="965b3-1263">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="965b3-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="965b3-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="965b3-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="965b3-1265">Empty</span><span class="sxs-lookup"><span data-stu-id="965b3-1265">Empty</span></span> | <span data-ttu-id="965b3-1266">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="965b3-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="965b3-1267">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="965b3-1268">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="965b3-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="965b3-1269">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="965b3-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="965b3-1270">其他資源</span><span class="sxs-lookup"><span data-stu-id="965b3-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
