---
title: ASP.NET Core SignalR 設定
author: bradygaster
description: 瞭解如何設定 ASP.NET Core SignalR 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: cef05b32731e0930d7a3cfc6fe4502236b07a236
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972063"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="31b57-103">ASP.NET Core SignalR 設定</span><span class="sxs-lookup"><span data-stu-id="31b57-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="31b57-104">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="31b57-105">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="31b57-106">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="31b57-107">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="31b57-108">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31b57-109">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="31b57-110">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="31b57-111">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="31b57-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="31b57-112">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="31b57-113">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="31b57-114">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="31b57-115">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="31b57-116">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="31b57-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="31b57-117">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="31b57-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="31b57-118">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-118">MessagePack serialization options</span></span>

<span data-ttu-id="31b57-119">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="31b57-120">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-121">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="31b57-122">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="31b57-122">Configure server options</span></span>

<span data-ttu-id="31b57-123">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="31b57-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="31b57-124">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-124">Option</span></span> | <span data-ttu-id="31b57-125">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-125">Default Value</span></span> | <span data-ttu-id="31b57-126">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="31b57-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-127">30 seconds</span></span> | <span data-ttu-id="31b57-128">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="31b57-129">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="31b57-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="31b57-130">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="31b57-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-131">15 seconds</span></span> | <span data-ttu-id="31b57-132">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="31b57-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="31b57-133">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-134">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-135">15 seconds</span></span> | <span data-ttu-id="31b57-136">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="31b57-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="31b57-137">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="31b57-138">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="31b57-139">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="31b57-139">All installed protocols</span></span> | <span data-ttu-id="31b57-140">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-140">Protocols supported by this hub.</span></span> <span data-ttu-id="31b57-141">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="31b57-142">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="31b57-143">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="31b57-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="31b57-144">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="31b57-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="31b57-145">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="31b57-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="31b57-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-146">32 KB</span></span> | <span data-ttu-id="31b57-147">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="31b57-148">1</span><span class="sxs-lookup"><span data-stu-id="31b57-148">1</span></span> | <span data-ttu-id="31b57-149">每個用戶端在進行佇列之前可以平行呼叫的中樞方法數目上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="31b57-150">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="31b57-151">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="31b57-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="31b57-152">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="31b57-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="31b57-153">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="31b57-154">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="31b57-155">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="31b57-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="31b57-156">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-156">Option</span></span> | <span data-ttu-id="31b57-157">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-157">Default Value</span></span> | <span data-ttu-id="31b57-158">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="31b57-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-159">32 KB</span></span> | <span data-ttu-id="31b57-160">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="31b57-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="31b57-161">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="31b57-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="31b57-162">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="31b57-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="31b57-163">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="31b57-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="31b57-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-164">32 KB</span></span> | <span data-ttu-id="31b57-165">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="31b57-166">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="31b57-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="31b57-167">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="31b57-167">All Transports are enabled.</span></span> | <span data-ttu-id="31b57-168">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="31b57-169">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-169">See below.</span></span> | <span data-ttu-id="31b57-170">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="31b57-171">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-171">See below.</span></span> | <span data-ttu-id="31b57-172">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="31b57-173">0</span><span class="sxs-lookup"><span data-stu-id="31b57-173">0</span></span> | <span data-ttu-id="31b57-174">指定 negotiate 通訊協定的最小版本。</span><span class="sxs-lookup"><span data-stu-id="31b57-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="31b57-175">這是用來將用戶端限制在較新的版本。</span><span class="sxs-lookup"><span data-stu-id="31b57-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="31b57-176">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="31b57-177">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-177">Option</span></span> | <span data-ttu-id="31b57-178">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-178">Default Value</span></span> | <span data-ttu-id="31b57-179">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="31b57-180">90秒</span><span class="sxs-lookup"><span data-stu-id="31b57-180">90 seconds</span></span> | <span data-ttu-id="31b57-181">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="31b57-182">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="31b57-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="31b57-183">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="31b57-184">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-184">Option</span></span> | <span data-ttu-id="31b57-185">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-185">Default Value</span></span> | <span data-ttu-id="31b57-186">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="31b57-187">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-187">5 seconds</span></span> | <span data-ttu-id="31b57-188">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="31b57-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="31b57-189">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="31b57-190">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="31b57-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="31b57-191">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="31b57-191">Configure client options</span></span>

<span data-ttu-id="31b57-192">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="31b57-193">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="31b57-194">設定記錄</span><span class="sxs-lookup"><span data-stu-id="31b57-194">Configure logging</span></span>

<span data-ttu-id="31b57-195">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="31b57-196">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="31b57-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="31b57-197">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-198">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="31b57-199">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="31b57-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="31b57-200">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="31b57-201">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="31b57-202">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="31b57-203">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="31b57-204">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="31b57-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="31b57-205">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="31b57-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="31b57-206">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="31b57-207">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-207">The following table lists the available log levels.</span></span> <span data-ttu-id="31b57-208">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="31b57-209">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="31b57-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="31b57-210">String</span><span class="sxs-lookup"><span data-stu-id="31b57-210">String</span></span>                      | <span data-ttu-id="31b57-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="31b57-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="31b57-212">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="31b57-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="31b57-213">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="31b57-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="31b57-214">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="31b57-215">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="31b57-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="31b57-216">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="31b57-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="31b57-217">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="31b57-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="31b57-218">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="31b57-219">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="31b57-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="31b57-220">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="31b57-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="31b57-221">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="31b57-221">Configure allowed transports</span></span>

<span data-ttu-id="31b57-222">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="31b57-223">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="31b57-224">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-224">All transports are enabled by default.</span></span>

<span data-ttu-id="31b57-225">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="31b57-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="31b57-226">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="31b57-227">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="31b57-228">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="31b57-229">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="31b57-230">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="31b57-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="31b57-231">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="31b57-231">Configure bearer authentication</span></span>

<span data-ttu-id="31b57-232">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="31b57-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="31b57-233">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="31b57-234">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="31b57-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="31b57-235">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="31b57-236">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="31b57-237">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="31b57-238">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="31b57-239">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="31b57-240">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="31b57-241">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="31b57-242">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="31b57-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-243">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-244">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-244">Option</span></span> | <span data-ttu-id="31b57-245">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-245">Default value</span></span> | <span data-ttu-id="31b57-246">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="31b57-247">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-248">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-248">Timeout for server activity.</span></span> <span data-ttu-id="31b57-249">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-250">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-251">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="31b57-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-252">15 seconds</span></span> | <span data-ttu-id="31b57-253">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-254">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-255">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-256">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-257">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-257">15 seconds</span></span> | <span data-ttu-id="31b57-258">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-259">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-260">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="31b57-261">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="31b57-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="31b57-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-263">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-263">Option</span></span> | <span data-ttu-id="31b57-264">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-264">Default value</span></span> | <span data-ttu-id="31b57-265">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="31b57-266">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-267">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-267">Timeout for server activity.</span></span> <span data-ttu-id="31b57-268">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="31b57-269">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-270">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="31b57-271">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="31b57-272">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-273">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-274">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-275">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-275">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-276">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-276">Option</span></span> | <span data-ttu-id="31b57-277">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-277">Default value</span></span> | <span data-ttu-id="31b57-278">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="31b57-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="31b57-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="31b57-280">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-281">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-281">Timeout for server activity.</span></span> <span data-ttu-id="31b57-282">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-283">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-284">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="31b57-285">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-285">15 seconds</span></span> | <span data-ttu-id="31b57-286">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-287">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-288">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-289">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="31b57-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="31b57-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="31b57-291">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="31b57-292">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-293">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-294">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="31b57-295">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="31b57-295">Configure additional options</span></span>

<span data-ttu-id="31b57-296">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-297">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-298">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-298">.NET Option</span></span> |  <span data-ttu-id="31b57-299">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-299">Default value</span></span> | <span data-ttu-id="31b57-300">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="31b57-301">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="31b57-302">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-303">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-304">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="31b57-305">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-305">Empty</span></span> | <span data-ttu-id="31b57-306">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="31b57-307">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-307">Empty</span></span> | <span data-ttu-id="31b57-308">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="31b57-309">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-309">Empty</span></span> | <span data-ttu-id="31b57-310">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="31b57-311">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-311">5 seconds</span></span> | <span data-ttu-id="31b57-312">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="31b57-312">WebSockets only.</span></span> <span data-ttu-id="31b57-313">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="31b57-314">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="31b57-315">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-315">Empty</span></span> | <span data-ttu-id="31b57-316">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="31b57-317">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="31b57-318">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="31b57-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="31b57-319">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="31b57-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="31b57-320">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="31b57-321">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="31b57-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="31b57-322">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="31b57-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="31b57-323">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="31b57-324">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="31b57-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="31b57-325">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="31b57-326">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="31b57-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-328">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-328">JavaScript Option</span></span> | <span data-ttu-id="31b57-329">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-329">Default Value</span></span> | <span data-ttu-id="31b57-330">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="31b57-331">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="31b57-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="31b57-333">每個 HTTP 要求傳送的標頭字典。</span><span class="sxs-lookup"><span data-stu-id="31b57-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="31b57-334">在瀏覽器中傳送標頭不適用於 Websocket 或 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 串流。</span><span class="sxs-lookup"><span data-stu-id="31b57-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="31b57-335">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="31b57-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="31b57-336">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-337">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-338">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="31b57-339">指定是否將使用 CORS 要求傳送認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="31b57-340">Azure App Service 會使用 cookie 來進行粘滯話，且必須啟用此選項才能正確運作。</span><span class="sxs-lookup"><span data-stu-id="31b57-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="31b57-341">如需 CORS 與的詳細資訊 SignalR ，請參閱 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="31b57-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-342">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-342">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-343">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-343">Java Option</span></span> | <span data-ttu-id="31b57-344">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-344">Default Value</span></span> | <span data-ttu-id="31b57-345">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="31b57-346">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="31b57-347">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-348">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-349">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="31b57-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="31b57-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="31b57-351">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-351">Empty</span></span> | <span data-ttu-id="31b57-352">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="31b57-353">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="31b57-354">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="31b57-355">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="31b57-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="31b57-356">其他資源</span><span class="sxs-lookup"><span data-stu-id="31b57-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="31b57-357">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="31b57-358">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="31b57-359">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="31b57-360">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="31b57-361">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31b57-362">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="31b57-363">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="31b57-364">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="31b57-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="31b57-365">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="31b57-366">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="31b57-367">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="31b57-368">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="31b57-369">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="31b57-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="31b57-370">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="31b57-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="31b57-371">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-371">MessagePack serialization options</span></span>

<span data-ttu-id="31b57-372">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="31b57-373">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-374">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="31b57-375">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="31b57-375">Configure server options</span></span>

<span data-ttu-id="31b57-376">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="31b57-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="31b57-377">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-377">Option</span></span> | <span data-ttu-id="31b57-378">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-378">Default Value</span></span> | <span data-ttu-id="31b57-379">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="31b57-380">30 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-380">30 seconds</span></span> | <span data-ttu-id="31b57-381">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="31b57-382">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="31b57-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="31b57-383">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="31b57-384">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-384">15 seconds</span></span> | <span data-ttu-id="31b57-385">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="31b57-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="31b57-386">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-387">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-388">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-388">15 seconds</span></span> | <span data-ttu-id="31b57-389">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="31b57-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="31b57-390">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="31b57-391">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="31b57-392">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="31b57-392">All installed protocols</span></span> | <span data-ttu-id="31b57-393">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-393">Protocols supported by this hub.</span></span> <span data-ttu-id="31b57-394">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="31b57-395">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="31b57-396">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="31b57-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="31b57-397">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="31b57-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="31b57-398">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="31b57-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="31b57-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-399">32 KB</span></span> | <span data-ttu-id="31b57-400">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="31b57-401">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="31b57-402">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="31b57-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="31b57-403">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="31b57-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="31b57-404">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="31b57-405">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="31b57-406">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="31b57-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="31b57-407">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-407">Option</span></span> | <span data-ttu-id="31b57-408">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-408">Default Value</span></span> | <span data-ttu-id="31b57-409">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="31b57-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-410">32 KB</span></span> | <span data-ttu-id="31b57-411">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="31b57-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="31b57-412">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="31b57-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="31b57-413">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="31b57-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="31b57-414">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="31b57-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="31b57-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-415">32 KB</span></span> | <span data-ttu-id="31b57-416">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="31b57-417">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="31b57-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="31b57-418">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="31b57-418">All Transports are enabled.</span></span> | <span data-ttu-id="31b57-419">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="31b57-420">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-420">See below.</span></span> | <span data-ttu-id="31b57-421">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="31b57-422">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-422">See below.</span></span> | <span data-ttu-id="31b57-423">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="31b57-424">0</span><span class="sxs-lookup"><span data-stu-id="31b57-424">0</span></span> | <span data-ttu-id="31b57-425">指定 negotiate 通訊協定的最小版本。</span><span class="sxs-lookup"><span data-stu-id="31b57-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="31b57-426">這是用來將用戶端限制在較新的版本。</span><span class="sxs-lookup"><span data-stu-id="31b57-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="31b57-427">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="31b57-428">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-428">Option</span></span> | <span data-ttu-id="31b57-429">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-429">Default Value</span></span> | <span data-ttu-id="31b57-430">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="31b57-431">90秒</span><span class="sxs-lookup"><span data-stu-id="31b57-431">90 seconds</span></span> | <span data-ttu-id="31b57-432">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="31b57-433">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="31b57-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="31b57-434">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="31b57-435">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-435">Option</span></span> | <span data-ttu-id="31b57-436">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-436">Default Value</span></span> | <span data-ttu-id="31b57-437">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="31b57-438">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-438">5 seconds</span></span> | <span data-ttu-id="31b57-439">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="31b57-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="31b57-440">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="31b57-441">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="31b57-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="31b57-442">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="31b57-442">Configure client options</span></span>

<span data-ttu-id="31b57-443">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="31b57-444">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="31b57-445">設定記錄</span><span class="sxs-lookup"><span data-stu-id="31b57-445">Configure logging</span></span>

<span data-ttu-id="31b57-446">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="31b57-447">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="31b57-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="31b57-448">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-449">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="31b57-450">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="31b57-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="31b57-451">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="31b57-452">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="31b57-453">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="31b57-454">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="31b57-455">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="31b57-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="31b57-456">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="31b57-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="31b57-457">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="31b57-458">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-458">The following table lists the available log levels.</span></span> <span data-ttu-id="31b57-459">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="31b57-460">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="31b57-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="31b57-461">String</span><span class="sxs-lookup"><span data-stu-id="31b57-461">String</span></span>                      | <span data-ttu-id="31b57-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="31b57-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="31b57-463">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="31b57-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="31b57-464">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="31b57-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="31b57-465">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="31b57-466">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="31b57-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="31b57-467">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="31b57-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="31b57-468">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="31b57-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="31b57-469">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="31b57-470">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="31b57-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="31b57-471">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="31b57-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="31b57-472">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="31b57-472">Configure allowed transports</span></span>

<span data-ttu-id="31b57-473">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="31b57-474">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="31b57-475">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-475">All transports are enabled by default.</span></span>

<span data-ttu-id="31b57-476">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="31b57-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="31b57-477">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="31b57-478">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="31b57-479">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="31b57-480">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="31b57-481">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="31b57-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="31b57-482">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="31b57-482">Configure bearer authentication</span></span>

<span data-ttu-id="31b57-483">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="31b57-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="31b57-484">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="31b57-485">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="31b57-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="31b57-486">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="31b57-487">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="31b57-488">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="31b57-489">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="31b57-490">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="31b57-491">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="31b57-492">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="31b57-493">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="31b57-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-494">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-495">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-495">Option</span></span> | <span data-ttu-id="31b57-496">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-496">Default value</span></span> | <span data-ttu-id="31b57-497">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="31b57-498">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-499">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-499">Timeout for server activity.</span></span> <span data-ttu-id="31b57-500">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-501">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-502">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="31b57-503">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-503">15 seconds</span></span> | <span data-ttu-id="31b57-504">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-505">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-506">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-507">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-508">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-508">15 seconds</span></span> | <span data-ttu-id="31b57-509">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-510">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-511">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="31b57-512">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="31b57-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="31b57-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-514">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-514">Option</span></span> | <span data-ttu-id="31b57-515">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-515">Default value</span></span> | <span data-ttu-id="31b57-516">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="31b57-517">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-518">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-518">Timeout for server activity.</span></span> <span data-ttu-id="31b57-519">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="31b57-520">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-521">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="31b57-522">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="31b57-523">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-524">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-525">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-526">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-526">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-527">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-527">Option</span></span> | <span data-ttu-id="31b57-528">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-528">Default value</span></span> | <span data-ttu-id="31b57-529">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="31b57-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="31b57-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="31b57-531">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-532">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-532">Timeout for server activity.</span></span> <span data-ttu-id="31b57-533">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-534">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-535">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="31b57-536">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-536">15 seconds</span></span> | <span data-ttu-id="31b57-537">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-538">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-539">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-540">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="31b57-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="31b57-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="31b57-542">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="31b57-543">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-544">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-545">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="31b57-546">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="31b57-546">Configure additional options</span></span>

<span data-ttu-id="31b57-547">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-548">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-549">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-549">.NET Option</span></span> |  <span data-ttu-id="31b57-550">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-550">Default value</span></span> | <span data-ttu-id="31b57-551">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="31b57-552">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="31b57-553">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-554">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-555">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="31b57-556">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-556">Empty</span></span> | <span data-ttu-id="31b57-557">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="31b57-558">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-558">Empty</span></span> | <span data-ttu-id="31b57-559">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="31b57-560">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-560">Empty</span></span> | <span data-ttu-id="31b57-561">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="31b57-562">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-562">5 seconds</span></span> | <span data-ttu-id="31b57-563">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="31b57-563">WebSockets only.</span></span> <span data-ttu-id="31b57-564">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="31b57-565">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="31b57-566">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-566">Empty</span></span> | <span data-ttu-id="31b57-567">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="31b57-568">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="31b57-569">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="31b57-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="31b57-570">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="31b57-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="31b57-571">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="31b57-572">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="31b57-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="31b57-573">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="31b57-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="31b57-574">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="31b57-575">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="31b57-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="31b57-576">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="31b57-577">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="31b57-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-579">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-579">JavaScript Option</span></span> | <span data-ttu-id="31b57-580">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-580">Default Value</span></span> | <span data-ttu-id="31b57-581">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="31b57-582">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="31b57-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="31b57-584">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="31b57-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="31b57-585">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-586">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-587">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-588">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-588">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-589">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-589">Java Option</span></span> | <span data-ttu-id="31b57-590">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-590">Default Value</span></span> | <span data-ttu-id="31b57-591">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="31b57-592">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="31b57-593">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-594">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-595">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="31b57-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="31b57-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="31b57-597">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-597">Empty</span></span> | <span data-ttu-id="31b57-598">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="31b57-599">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="31b57-600">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="31b57-601">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="31b57-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="31b57-602">其他資源</span><span class="sxs-lookup"><span data-stu-id="31b57-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="31b57-603">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="31b57-604">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="31b57-605">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="31b57-606">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="31b57-607">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="31b57-608">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="31b57-609">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="31b57-610">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="31b57-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="31b57-611">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="31b57-612">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="31b57-613">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="31b57-614">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="31b57-615">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="31b57-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="31b57-616">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="31b57-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="31b57-617">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-617">MessagePack serialization options</span></span>

<span data-ttu-id="31b57-618">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="31b57-619">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-620">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="31b57-621">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="31b57-621">Configure server options</span></span>

<span data-ttu-id="31b57-622">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="31b57-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="31b57-623">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-623">Option</span></span> | <span data-ttu-id="31b57-624">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-624">Default Value</span></span> | <span data-ttu-id="31b57-625">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="31b57-626">30 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-626">30 seconds</span></span> | <span data-ttu-id="31b57-627">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="31b57-628">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="31b57-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="31b57-629">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="31b57-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-630">15 seconds</span></span> | <span data-ttu-id="31b57-631">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="31b57-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="31b57-632">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-633">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-634">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-634">15 seconds</span></span> | <span data-ttu-id="31b57-635">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="31b57-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="31b57-636">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="31b57-637">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="31b57-638">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="31b57-638">All installed protocols</span></span> | <span data-ttu-id="31b57-639">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-639">Protocols supported by this hub.</span></span> <span data-ttu-id="31b57-640">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="31b57-641">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="31b57-642">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="31b57-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="31b57-643">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="31b57-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="31b57-644">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="31b57-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="31b57-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-645">32 KB</span></span> | <span data-ttu-id="31b57-646">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="31b57-647">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="31b57-648">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="31b57-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="31b57-649">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="31b57-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="31b57-650">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="31b57-651">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="31b57-652">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="31b57-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="31b57-653">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-653">Option</span></span> | <span data-ttu-id="31b57-654">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-654">Default Value</span></span> | <span data-ttu-id="31b57-655">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="31b57-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-656">32 KB</span></span> | <span data-ttu-id="31b57-657">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="31b57-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="31b57-658">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="31b57-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="31b57-659">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="31b57-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="31b57-660">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="31b57-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="31b57-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-661">32 KB</span></span> | <span data-ttu-id="31b57-662">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="31b57-663">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="31b57-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="31b57-664">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="31b57-664">All Transports are enabled.</span></span> | <span data-ttu-id="31b57-665">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="31b57-666">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-666">See below.</span></span> | <span data-ttu-id="31b57-667">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="31b57-668">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-668">See below.</span></span> | <span data-ttu-id="31b57-669">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="31b57-670">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="31b57-671">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-671">Option</span></span> | <span data-ttu-id="31b57-672">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-672">Default Value</span></span> | <span data-ttu-id="31b57-673">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="31b57-674">90秒</span><span class="sxs-lookup"><span data-stu-id="31b57-674">90 seconds</span></span> | <span data-ttu-id="31b57-675">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="31b57-676">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="31b57-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="31b57-677">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="31b57-678">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-678">Option</span></span> | <span data-ttu-id="31b57-679">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-679">Default Value</span></span> | <span data-ttu-id="31b57-680">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="31b57-681">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-681">5 seconds</span></span> | <span data-ttu-id="31b57-682">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="31b57-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="31b57-683">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="31b57-684">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="31b57-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="31b57-685">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="31b57-685">Configure client options</span></span>

<span data-ttu-id="31b57-686">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="31b57-687">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="31b57-688">設定記錄</span><span class="sxs-lookup"><span data-stu-id="31b57-688">Configure logging</span></span>

<span data-ttu-id="31b57-689">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="31b57-690">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="31b57-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="31b57-691">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-692">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="31b57-693">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="31b57-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="31b57-694">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="31b57-695">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="31b57-696">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="31b57-697">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="31b57-698">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="31b57-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="31b57-699">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="31b57-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="31b57-700">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="31b57-701">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-701">The following table lists the available log levels.</span></span> <span data-ttu-id="31b57-702">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="31b57-703">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="31b57-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="31b57-704">String</span><span class="sxs-lookup"><span data-stu-id="31b57-704">String</span></span>                      | <span data-ttu-id="31b57-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="31b57-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="31b57-706">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="31b57-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="31b57-707">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="31b57-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="31b57-708">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="31b57-709">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="31b57-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="31b57-710">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="31b57-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="31b57-711">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="31b57-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="31b57-712">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="31b57-713">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="31b57-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="31b57-714">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="31b57-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="31b57-715">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="31b57-715">Configure allowed transports</span></span>

<span data-ttu-id="31b57-716">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="31b57-717">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="31b57-718">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-718">All transports are enabled by default.</span></span>

<span data-ttu-id="31b57-719">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="31b57-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="31b57-720">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="31b57-721">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="31b57-722">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="31b57-723">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="31b57-724">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="31b57-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="31b57-725">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="31b57-725">Configure bearer authentication</span></span>

<span data-ttu-id="31b57-726">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="31b57-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="31b57-727">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="31b57-728">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="31b57-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="31b57-729">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="31b57-730">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="31b57-731">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="31b57-732">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="31b57-733">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="31b57-734">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="31b57-735">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="31b57-736">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="31b57-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-737">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-738">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-738">Option</span></span> | <span data-ttu-id="31b57-739">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-739">Default value</span></span> | <span data-ttu-id="31b57-740">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="31b57-741">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-742">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-742">Timeout for server activity.</span></span> <span data-ttu-id="31b57-743">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-744">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-745">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="31b57-746">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-746">15 seconds</span></span> | <span data-ttu-id="31b57-747">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-748">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-749">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-750">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-751">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-751">15 seconds</span></span> | <span data-ttu-id="31b57-752">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-753">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-754">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="31b57-755">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="31b57-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="31b57-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-757">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-757">Option</span></span> | <span data-ttu-id="31b57-758">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-758">Default value</span></span> | <span data-ttu-id="31b57-759">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="31b57-760">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-761">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-761">Timeout for server activity.</span></span> <span data-ttu-id="31b57-762">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="31b57-763">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-764">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="31b57-765">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="31b57-766">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-767">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-768">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-769">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-769">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-770">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-770">Option</span></span> | <span data-ttu-id="31b57-771">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-771">Default value</span></span> | <span data-ttu-id="31b57-772">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="31b57-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="31b57-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="31b57-774">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-775">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-775">Timeout for server activity.</span></span> <span data-ttu-id="31b57-776">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-777">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-778">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="31b57-779">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-779">15 seconds</span></span> | <span data-ttu-id="31b57-780">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-781">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-782">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-783">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="31b57-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="31b57-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="31b57-785">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="31b57-786">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-787">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-788">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="31b57-789">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="31b57-789">Configure additional options</span></span>

<span data-ttu-id="31b57-790">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-791">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-792">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-792">.NET Option</span></span> |  <span data-ttu-id="31b57-793">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-793">Default value</span></span> | <span data-ttu-id="31b57-794">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="31b57-795">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="31b57-796">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-797">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-798">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="31b57-799">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-799">Empty</span></span> | <span data-ttu-id="31b57-800">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="31b57-801">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-801">Empty</span></span> | <span data-ttu-id="31b57-802">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="31b57-803">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-803">Empty</span></span> | <span data-ttu-id="31b57-804">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="31b57-805">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-805">5 seconds</span></span> | <span data-ttu-id="31b57-806">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="31b57-806">WebSockets only.</span></span> <span data-ttu-id="31b57-807">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="31b57-808">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="31b57-809">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-809">Empty</span></span> | <span data-ttu-id="31b57-810">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="31b57-811">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="31b57-812">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="31b57-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="31b57-813">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="31b57-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="31b57-814">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="31b57-815">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="31b57-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="31b57-816">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="31b57-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="31b57-817">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="31b57-818">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="31b57-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="31b57-819">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="31b57-820">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="31b57-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-822">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-822">JavaScript Option</span></span> | <span data-ttu-id="31b57-823">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-823">Default Value</span></span> | <span data-ttu-id="31b57-824">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="31b57-825">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="31b57-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="31b57-827">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="31b57-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="31b57-828">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-829">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-830">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-831">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-831">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-832">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-832">Java Option</span></span> | <span data-ttu-id="31b57-833">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-833">Default Value</span></span> | <span data-ttu-id="31b57-834">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="31b57-835">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="31b57-836">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-837">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-838">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="31b57-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="31b57-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="31b57-840">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-840">Empty</span></span> | <span data-ttu-id="31b57-841">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="31b57-842">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="31b57-843">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="31b57-844">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="31b57-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="31b57-845">其他資源</span><span class="sxs-lookup"><span data-stu-id="31b57-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="31b57-846">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="31b57-847">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="31b57-848">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="31b57-849">您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 SignalR 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="31b57-850">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="31b57-851">該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="31b57-852">如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="31b57-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="31b57-853">舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="31b57-854">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="31b57-855">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="31b57-856">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="31b57-857">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-857">MessagePack serialization options</span></span>

<span data-ttu-id="31b57-858">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="31b57-859">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-860">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="31b57-861">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="31b57-861">Configure server options</span></span>

<span data-ttu-id="31b57-862">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="31b57-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="31b57-863">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-863">Option</span></span> | <span data-ttu-id="31b57-864">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-864">Default Value</span></span> | <span data-ttu-id="31b57-865">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="31b57-866">30 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-866">30 seconds</span></span> | <span data-ttu-id="31b57-867">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="31b57-868">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="31b57-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="31b57-869">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="31b57-870">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-870">15 seconds</span></span> | <span data-ttu-id="31b57-871">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="31b57-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="31b57-872">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-873">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-874">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-874">15 seconds</span></span> | <span data-ttu-id="31b57-875">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="31b57-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="31b57-876">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="31b57-877">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="31b57-878">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="31b57-878">All installed protocols</span></span> | <span data-ttu-id="31b57-879">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-879">Protocols supported by this hub.</span></span> <span data-ttu-id="31b57-880">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="31b57-881">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="31b57-882">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="31b57-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="31b57-883">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="31b57-884">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="31b57-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="31b57-885">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="31b57-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="31b57-886">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="31b57-887">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="31b57-888">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="31b57-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="31b57-889">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-889">Option</span></span> | <span data-ttu-id="31b57-890">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-890">Default Value</span></span> | <span data-ttu-id="31b57-891">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="31b57-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-892">32 KB</span></span> | <span data-ttu-id="31b57-893">從伺服器緩衝的用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="31b57-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="31b57-894">提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="31b57-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="31b57-895">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="31b57-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="31b57-896">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="31b57-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="31b57-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-897">32 KB</span></span> | <span data-ttu-id="31b57-898">由伺服器緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="31b57-899">提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="31b57-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="31b57-900">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="31b57-900">All Transports are enabled.</span></span> | <span data-ttu-id="31b57-901">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="31b57-902">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-902">See below.</span></span> | <span data-ttu-id="31b57-903">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="31b57-904">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-904">See below.</span></span> | <span data-ttu-id="31b57-905">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="31b57-906">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="31b57-907">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-907">Option</span></span> | <span data-ttu-id="31b57-908">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-908">Default Value</span></span> | <span data-ttu-id="31b57-909">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="31b57-910">90秒</span><span class="sxs-lookup"><span data-stu-id="31b57-910">90 seconds</span></span> | <span data-ttu-id="31b57-911">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="31b57-912">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="31b57-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="31b57-913">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="31b57-914">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-914">Option</span></span> | <span data-ttu-id="31b57-915">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-915">Default Value</span></span> | <span data-ttu-id="31b57-916">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="31b57-917">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-917">5 seconds</span></span> | <span data-ttu-id="31b57-918">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="31b57-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="31b57-919">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="31b57-920">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="31b57-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="31b57-921">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="31b57-921">Configure client options</span></span>

<span data-ttu-id="31b57-922">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="31b57-923">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="31b57-924">設定記錄</span><span class="sxs-lookup"><span data-stu-id="31b57-924">Configure logging</span></span>

<span data-ttu-id="31b57-925">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="31b57-926">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="31b57-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="31b57-927">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-928">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="31b57-929">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="31b57-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="31b57-930">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="31b57-931">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="31b57-932">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="31b57-933">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="31b57-934">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="31b57-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="31b57-935">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="31b57-936">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="31b57-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="31b57-937">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="31b57-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="31b57-938">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="31b57-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="31b57-939">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="31b57-940">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="31b57-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="31b57-941">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="31b57-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="31b57-942">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="31b57-942">Configure allowed transports</span></span>

<span data-ttu-id="31b57-943">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="31b57-944">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="31b57-945">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-945">All transports are enabled by default.</span></span>

<span data-ttu-id="31b57-946">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="31b57-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="31b57-947">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="31b57-948">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="31b57-949">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="31b57-949">Configure bearer authentication</span></span>

<span data-ttu-id="31b57-950">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="31b57-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="31b57-951">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="31b57-952">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="31b57-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="31b57-953">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="31b57-954">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="31b57-955">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="31b57-956">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="31b57-957">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="31b57-958">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="31b57-959">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="31b57-960">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="31b57-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-961">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-962">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-962">Option</span></span> | <span data-ttu-id="31b57-963">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-963">Default value</span></span> | <span data-ttu-id="31b57-964">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="31b57-965">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-966">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-966">Timeout for server activity.</span></span> <span data-ttu-id="31b57-967">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-968">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-969">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="31b57-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-970">15 seconds</span></span> | <span data-ttu-id="31b57-971">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-972">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-973">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-974">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-975">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-975">15 seconds</span></span> | <span data-ttu-id="31b57-976">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-977">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-978">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="31b57-979">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="31b57-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="31b57-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-981">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-981">Option</span></span> | <span data-ttu-id="31b57-982">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-982">Default value</span></span> | <span data-ttu-id="31b57-983">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="31b57-984">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-985">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-985">Timeout for server activity.</span></span> <span data-ttu-id="31b57-986">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="31b57-987">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-988">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="31b57-989">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="31b57-990">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-991">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-992">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-993">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-993">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-994">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-994">Option</span></span> | <span data-ttu-id="31b57-995">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-995">Default value</span></span> | <span data-ttu-id="31b57-996">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="31b57-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="31b57-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="31b57-998">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-999">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-999">Timeout for server activity.</span></span> <span data-ttu-id="31b57-1000">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-1001">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-1002">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="31b57-1003">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1003">15 seconds</span></span> | <span data-ttu-id="31b57-1004">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-1005">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-1006">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-1007">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="31b57-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="31b57-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="31b57-1009">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="31b57-1010">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="31b57-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="31b57-1011">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="31b57-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="31b57-1012">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="31b57-1013">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1013">Configure additional options</span></span>

<span data-ttu-id="31b57-1014">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-1016">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1016">.NET Option</span></span> |  <span data-ttu-id="31b57-1017">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1017">Default value</span></span> | <span data-ttu-id="31b57-1018">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="31b57-1019">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="31b57-1020">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-1021">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-1022">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="31b57-1023">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1023">Empty</span></span> | <span data-ttu-id="31b57-1024">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="31b57-1025">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1025">Empty</span></span> | <span data-ttu-id="31b57-1026">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="31b57-1027">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1027">Empty</span></span> | <span data-ttu-id="31b57-1028">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="31b57-1029">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1029">5 seconds</span></span> | <span data-ttu-id="31b57-1030">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="31b57-1030">WebSockets only.</span></span> <span data-ttu-id="31b57-1031">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="31b57-1032">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="31b57-1033">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1033">Empty</span></span> | <span data-ttu-id="31b57-1034">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="31b57-1035">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="31b57-1036">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="31b57-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="31b57-1037">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="31b57-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="31b57-1038">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="31b57-1039">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="31b57-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="31b57-1040">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="31b57-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="31b57-1041">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="31b57-1042">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="31b57-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="31b57-1043">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="31b57-1044">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="31b57-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-1046">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1046">JavaScript Option</span></span> | <span data-ttu-id="31b57-1047">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1047">Default Value</span></span> | <span data-ttu-id="31b57-1048">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="31b57-1049">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="31b57-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="31b57-1051">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="31b57-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="31b57-1052">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-1053">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-1054">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-1055">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-1056">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1056">Java Option</span></span> | <span data-ttu-id="31b57-1057">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1057">Default Value</span></span> | <span data-ttu-id="31b57-1058">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="31b57-1059">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="31b57-1060">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-1061">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-1062">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="31b57-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="31b57-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="31b57-1064">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1064">Empty</span></span> | <span data-ttu-id="31b57-1065">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="31b57-1066">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="31b57-1067">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="31b57-1068">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="31b57-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="31b57-1069">其他資源</span><span class="sxs-lookup"><span data-stu-id="31b57-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="31b57-1070">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="31b57-1071">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="31b57-1072">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="31b57-1073">您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 SignalR 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="31b57-1074">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="31b57-1075">該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="31b57-1076">如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="31b57-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="31b57-1077">舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="31b57-1078">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="31b57-1079">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="31b57-1080">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="31b57-1081">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1081">MessagePack serialization options</span></span>

<span data-ttu-id="31b57-1082">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="31b57-1083">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-1084">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="31b57-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="31b57-1085">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1085">Configure server options</span></span>

<span data-ttu-id="31b57-1086">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="31b57-1087">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1087">Option</span></span> | <span data-ttu-id="31b57-1088">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1088">Default Value</span></span> | <span data-ttu-id="31b57-1089">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="31b57-1090">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1090">15 seconds</span></span> | <span data-ttu-id="31b57-1091">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="31b57-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="31b57-1092">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-1093">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="31b57-1094">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1094">15 seconds</span></span> | <span data-ttu-id="31b57-1095">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="31b57-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="31b57-1096">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="31b57-1097">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="31b57-1098">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="31b57-1098">All installed protocols</span></span> | <span data-ttu-id="31b57-1099">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="31b57-1100">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="31b57-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="31b57-1101">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="31b57-1102">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="31b57-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="31b57-1103">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="31b57-1104">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="31b57-1105">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="31b57-1106">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="31b57-1107">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="31b57-1108">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="31b57-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="31b57-1109">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1109">Option</span></span> | <span data-ttu-id="31b57-1110">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1110">Default Value</span></span> | <span data-ttu-id="31b57-1111">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="31b57-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-1112">32 KB</span></span> | <span data-ttu-id="31b57-1113">從伺服器緩衝的用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="31b57-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="31b57-1114">提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="31b57-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="31b57-1115">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="31b57-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="31b57-1116">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="31b57-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="31b57-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="31b57-1117">32 KB</span></span> | <span data-ttu-id="31b57-1118">由伺服器緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="31b57-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="31b57-1119">提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="31b57-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="31b57-1120">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="31b57-1120">All Transports are enabled.</span></span> | <span data-ttu-id="31b57-1121">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="31b57-1122">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-1122">See below.</span></span> | <span data-ttu-id="31b57-1123">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="31b57-1124">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="31b57-1124">See below.</span></span> | <span data-ttu-id="31b57-1125">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="31b57-1126">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="31b57-1127">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1127">Option</span></span> | <span data-ttu-id="31b57-1128">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1128">Default Value</span></span> | <span data-ttu-id="31b57-1129">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="31b57-1130">90秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1130">90 seconds</span></span> | <span data-ttu-id="31b57-1131">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="31b57-1132">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="31b57-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="31b57-1133">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="31b57-1134">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1134">Option</span></span> | <span data-ttu-id="31b57-1135">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1135">Default Value</span></span> | <span data-ttu-id="31b57-1136">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="31b57-1137">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1137">5 seconds</span></span> | <span data-ttu-id="31b57-1138">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="31b57-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="31b57-1139">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="31b57-1140">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="31b57-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="31b57-1141">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1141">Configure client options</span></span>

<span data-ttu-id="31b57-1142">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="31b57-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="31b57-1143">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="31b57-1144">設定記錄</span><span class="sxs-lookup"><span data-stu-id="31b57-1144">Configure logging</span></span>

<span data-ttu-id="31b57-1145">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="31b57-1146">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="31b57-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="31b57-1147">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="31b57-1148">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="31b57-1149">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="31b57-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="31b57-1150">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="31b57-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="31b57-1151">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="31b57-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="31b57-1152">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="31b57-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="31b57-1153">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="31b57-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="31b57-1154">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="31b57-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="31b57-1155">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="31b57-1156">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="31b57-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="31b57-1157">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="31b57-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="31b57-1158">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="31b57-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="31b57-1159">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="31b57-1160">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="31b57-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="31b57-1161">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="31b57-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="31b57-1162">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="31b57-1162">Configure allowed transports</span></span>

<span data-ttu-id="31b57-1163">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="31b57-1164">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="31b57-1165">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="31b57-1166">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="31b57-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="31b57-1167">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="31b57-1168">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="31b57-1168">Configure bearer authentication</span></span>

<span data-ttu-id="31b57-1169">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="31b57-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="31b57-1170">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="31b57-1171">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="31b57-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="31b57-1172">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="31b57-1173">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="31b57-1174">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="31b57-1175">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="31b57-1176">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="31b57-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="31b57-1177">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="31b57-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="31b57-1178">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="31b57-1179">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="31b57-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-1181">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1181">Option</span></span> | <span data-ttu-id="31b57-1182">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1182">Default value</span></span> | <span data-ttu-id="31b57-1183">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="31b57-1184">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-1185">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-1185">Timeout for server activity.</span></span> <span data-ttu-id="31b57-1186">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-1187">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-1188">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="31b57-1189">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1189">15 seconds</span></span> | <span data-ttu-id="31b57-1190">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-1191">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="31b57-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="31b57-1192">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-1193">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="31b57-1194">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="31b57-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="31b57-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-1196">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1196">Option</span></span> | <span data-ttu-id="31b57-1197">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1197">Default value</span></span> | <span data-ttu-id="31b57-1198">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="31b57-1199">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-1200">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-1200">Timeout for server activity.</span></span> <span data-ttu-id="31b57-1201">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="31b57-1202">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-1203">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-1204">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-1205">選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1205">Option</span></span> | <span data-ttu-id="31b57-1206">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1206">Default value</span></span> | <span data-ttu-id="31b57-1207">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="31b57-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="31b57-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="31b57-1209">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="31b57-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="31b57-1210">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-1210">Timeout for server activity.</span></span> <span data-ttu-id="31b57-1211">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-1212">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="31b57-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="31b57-1213">建議的值是至少兩個伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="31b57-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="31b57-1214">15 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1214">15 seconds</span></span> | <span data-ttu-id="31b57-1215">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="31b57-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="31b57-1216">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="31b57-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="31b57-1217">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="31b57-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="31b57-1218">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="31b57-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="31b57-1219">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1219">Configure additional options</span></span>

<span data-ttu-id="31b57-1220">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="31b57-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="31b57-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="31b57-1222">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1222">.NET Option</span></span> |  <span data-ttu-id="31b57-1223">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1223">Default value</span></span> | <span data-ttu-id="31b57-1224">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="31b57-1225">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="31b57-1226">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-1227">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-1228">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="31b57-1229">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1229">Empty</span></span> | <span data-ttu-id="31b57-1230">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="31b57-1231">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1231">Empty</span></span> | <span data-ttu-id="31b57-1232">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="31b57-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="31b57-1233">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1233">Empty</span></span> | <span data-ttu-id="31b57-1234">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="31b57-1235">5 秒</span><span class="sxs-lookup"><span data-stu-id="31b57-1235">5 seconds</span></span> | <span data-ttu-id="31b57-1236">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="31b57-1236">WebSockets only.</span></span> <span data-ttu-id="31b57-1237">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="31b57-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="31b57-1238">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="31b57-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="31b57-1239">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1239">Empty</span></span> | <span data-ttu-id="31b57-1240">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="31b57-1241">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="31b57-1242">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="31b57-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="31b57-1243">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="31b57-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="31b57-1244">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="31b57-1245">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="31b57-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="31b57-1246">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="31b57-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="31b57-1247">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="31b57-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="31b57-1248">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="31b57-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="31b57-1249">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="31b57-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="31b57-1250">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="31b57-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="31b57-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="31b57-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="31b57-1252">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1252">JavaScript Option</span></span> | <span data-ttu-id="31b57-1253">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1253">Default Value</span></span> | <span data-ttu-id="31b57-1254">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="31b57-1255">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="31b57-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="31b57-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="31b57-1257">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="31b57-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="31b57-1258">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-1259">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-1260">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="31b57-1261">Java</span><span class="sxs-lookup"><span data-stu-id="31b57-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="31b57-1262">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="31b57-1262">Java Option</span></span> | <span data-ttu-id="31b57-1263">預設值</span><span class="sxs-lookup"><span data-stu-id="31b57-1263">Default Value</span></span> | <span data-ttu-id="31b57-1264">描述</span><span class="sxs-lookup"><span data-stu-id="31b57-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="31b57-1265">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="31b57-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="31b57-1266">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="31b57-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="31b57-1267">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="31b57-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="31b57-1268">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="31b57-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="31b57-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="31b57-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="31b57-1270">空白</span><span class="sxs-lookup"><span data-stu-id="31b57-1270">Empty</span></span> | <span data-ttu-id="31b57-1271">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="31b57-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="31b57-1272">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="31b57-1273">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="31b57-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="31b57-1274">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="31b57-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="31b57-1275">其他資源</span><span class="sxs-lookup"><span data-stu-id="31b57-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
