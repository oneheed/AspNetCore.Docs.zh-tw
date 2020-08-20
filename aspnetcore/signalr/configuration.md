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
ms.openlocfilehash: fc0e6398884bb5c3b806a587a8a361d7f279461f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625553"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="8c89b-103">ASP.NET Core SignalR 設定</span><span class="sxs-lookup"><span data-stu-id="8c89b-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="8c89b-104">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-105">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="8c89b-106">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="8c89b-107">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="8c89b-108">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8c89b-109">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="8c89b-110">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="8c89b-111">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="8c89b-112">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="8c89b-113">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="8c89b-114">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="8c89b-115">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="8c89b-116">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="8c89b-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="8c89b-117">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="8c89b-118">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-118">MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-119">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="8c89b-120">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-121">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="8c89b-122">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-122">Configure server options</span></span>

<span data-ttu-id="8c89b-123">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="8c89b-124">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-124">Option</span></span> | <span data-ttu-id="8c89b-125">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-125">Default Value</span></span> | <span data-ttu-id="8c89b-126">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="8c89b-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-127">30 seconds</span></span> | <span data-ttu-id="8c89b-128">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="8c89b-129">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="8c89b-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="8c89b-130">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="8c89b-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-131">15 seconds</span></span> | <span data-ttu-id="8c89b-132">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="8c89b-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8c89b-133">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-134">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-135">15 seconds</span></span> | <span data-ttu-id="8c89b-136">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8c89b-137">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8c89b-138">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8c89b-139">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="8c89b-139">All installed protocols</span></span> | <span data-ttu-id="8c89b-140">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-140">Protocols supported by this hub.</span></span> <span data-ttu-id="8c89b-141">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8c89b-142">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8c89b-143">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="8c89b-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="8c89b-144">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="8c89b-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="8c89b-145">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="8c89b-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="8c89b-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-146">32 KB</span></span> | <span data-ttu-id="8c89b-147">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="8c89b-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="8c89b-148">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8c89b-149">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="8c89b-150">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="8c89b-151">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="8c89b-152">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8c89b-153">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="8c89b-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="8c89b-154">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-154">Option</span></span> | <span data-ttu-id="8c89b-155">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-155">Default Value</span></span> | <span data-ttu-id="8c89b-156">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8c89b-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-157">32 KB</span></span> | <span data-ttu-id="8c89b-158">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="8c89b-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="8c89b-159">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8c89b-160">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="8c89b-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8c89b-161">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="8c89b-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8c89b-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-162">32 KB</span></span> | <span data-ttu-id="8c89b-163">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="8c89b-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="8c89b-164">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8c89b-165">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="8c89b-165">All Transports are enabled.</span></span> | <span data-ttu-id="8c89b-166">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8c89b-167">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-167">See below.</span></span> | <span data-ttu-id="8c89b-168">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8c89b-169">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-169">See below.</span></span> | <span data-ttu-id="8c89b-170">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="8c89b-171">0</span><span class="sxs-lookup"><span data-stu-id="8c89b-171">0</span></span> | <span data-ttu-id="8c89b-172">指定 negotiate 通訊協定的最小版本。</span><span class="sxs-lookup"><span data-stu-id="8c89b-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="8c89b-173">這是用來將用戶端限制在較新的版本。</span><span class="sxs-lookup"><span data-stu-id="8c89b-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="8c89b-174">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="8c89b-175">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-175">Option</span></span> | <span data-ttu-id="8c89b-176">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-176">Default Value</span></span> | <span data-ttu-id="8c89b-177">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="8c89b-178">90秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-178">90 seconds</span></span> | <span data-ttu-id="8c89b-179">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="8c89b-180">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="8c89b-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="8c89b-181">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="8c89b-182">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-182">Option</span></span> | <span data-ttu-id="8c89b-183">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-183">Default Value</span></span> | <span data-ttu-id="8c89b-184">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="8c89b-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-185">5 seconds</span></span> | <span data-ttu-id="8c89b-186">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="8c89b-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="8c89b-187">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="8c89b-188">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="8c89b-189">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-189">Configure client options</span></span>

<span data-ttu-id="8c89b-190">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="8c89b-191">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="8c89b-192">設定記錄</span><span class="sxs-lookup"><span data-stu-id="8c89b-192">Configure logging</span></span>

<span data-ttu-id="8c89b-193">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="8c89b-194">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="8c89b-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="8c89b-195">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-196">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="8c89b-197">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="8c89b-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="8c89b-198">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="8c89b-199">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="8c89b-200">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="8c89b-201">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="8c89b-202">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="8c89b-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="8c89b-203">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="8c89b-204">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="8c89b-205">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-205">The following table lists the available log levels.</span></span> <span data-ttu-id="8c89b-206">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="8c89b-207">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="8c89b-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="8c89b-208">String</span><span class="sxs-lookup"><span data-stu-id="8c89b-208">String</span></span>                      | <span data-ttu-id="8c89b-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="8c89b-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="8c89b-210">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="8c89b-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="8c89b-211">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="8c89b-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="8c89b-212">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="8c89b-213">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="8c89b-214">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="8c89b-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="8c89b-215">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="8c89b-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="8c89b-216">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="8c89b-217">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="8c89b-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="8c89b-218">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="8c89b-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="8c89b-219">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="8c89b-219">Configure allowed transports</span></span>

<span data-ttu-id="8c89b-220">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="8c89b-221">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="8c89b-222">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-222">All transports are enabled by default.</span></span>

<span data-ttu-id="8c89b-223">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="8c89b-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="8c89b-224">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="8c89b-225">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="8c89b-226">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="8c89b-227">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="8c89b-228">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="8c89b-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="8c89b-229">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="8c89b-229">Configure bearer authentication</span></span>

<span data-ttu-id="8c89b-230">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="8c89b-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="8c89b-231">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="8c89b-232">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="8c89b-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="8c89b-233">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="8c89b-234">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="8c89b-235">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="8c89b-236">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="8c89b-237">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="8c89b-238">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="8c89b-239">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="8c89b-240">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="8c89b-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-241">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-242">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-242">Option</span></span> | <span data-ttu-id="8c89b-243">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-243">Default value</span></span> | <span data-ttu-id="8c89b-244">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8c89b-245">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-246">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-246">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-247">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-248">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-249">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8c89b-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-250">15 seconds</span></span> | <span data-ttu-id="8c89b-251">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-252">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-253">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-254">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-255">15 seconds</span></span> | <span data-ttu-id="8c89b-256">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-257">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-258">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="8c89b-259">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="8c89b-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-261">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-261">Option</span></span> | <span data-ttu-id="8c89b-262">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-262">Default value</span></span> | <span data-ttu-id="8c89b-263">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8c89b-264">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-265">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-265">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-266">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8c89b-267">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-268">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="8c89b-269">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-270">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-271">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-272">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-273">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-273">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-274">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-274">Option</span></span> | <span data-ttu-id="8c89b-275">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-275">Default value</span></span> | <span data-ttu-id="8c89b-276">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8c89b-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8c89b-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8c89b-278">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-279">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-279">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-280">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-281">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-282">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8c89b-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-283">15 seconds</span></span> | <span data-ttu-id="8c89b-284">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-285">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-286">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-287">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="8c89b-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="8c89b-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="8c89b-289">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-290">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-291">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-292">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="8c89b-293">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-293">Configure additional options</span></span>

<span data-ttu-id="8c89b-294">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-295">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-296">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-296">.NET Option</span></span> |  <span data-ttu-id="8c89b-297">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-297">Default value</span></span> | <span data-ttu-id="8c89b-298">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="8c89b-299">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="8c89b-300">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-301">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-302">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="8c89b-303">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-303">Empty</span></span> | <span data-ttu-id="8c89b-304">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="8c89b-305">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-305">Empty</span></span> | <span data-ttu-id="8c89b-306">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="8c89b-307">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-307">Empty</span></span> | <span data-ttu-id="8c89b-308">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="8c89b-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-309">5 seconds</span></span> | <span data-ttu-id="8c89b-310">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="8c89b-310">WebSockets only.</span></span> <span data-ttu-id="8c89b-311">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="8c89b-312">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="8c89b-313">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-313">Empty</span></span> | <span data-ttu-id="8c89b-314">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="8c89b-315">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="8c89b-316">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="8c89b-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="8c89b-317">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="8c89b-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="8c89b-318">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="8c89b-319">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="8c89b-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="8c89b-320">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="8c89b-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="8c89b-321">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="8c89b-322">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="8c89b-323">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="8c89b-324">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="8c89b-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-326">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-326">JavaScript Option</span></span> | <span data-ttu-id="8c89b-327">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-327">Default Value</span></span> | <span data-ttu-id="8c89b-328">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="8c89b-329">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="8c89b-330">每個 HTTP 要求傳送的標頭字典。</span><span class="sxs-lookup"><span data-stu-id="8c89b-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="8c89b-331">在瀏覽器中傳送標頭不適用於 Websocket 或 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 串流。</span><span class="sxs-lookup"><span data-stu-id="8c89b-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="8c89b-332">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="8c89b-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="8c89b-333">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-334">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-335">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="8c89b-336">指定是否將使用 CORS 要求傳送認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="8c89b-337">Azure App Service 會使用 cookie 來進行粘滯話，且必須啟用此選項才能正確運作。</span><span class="sxs-lookup"><span data-stu-id="8c89b-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="8c89b-338">如需 CORS 與的詳細資訊 SignalR ，請參閱 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-339">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-339">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-340">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-340">Java Option</span></span> | <span data-ttu-id="8c89b-341">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-341">Default Value</span></span> | <span data-ttu-id="8c89b-342">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="8c89b-343">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="8c89b-344">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-345">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-346">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="8c89b-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="8c89b-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="8c89b-348">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-348">Empty</span></span> | <span data-ttu-id="8c89b-349">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="8c89b-350">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="8c89b-351">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="8c89b-352">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="8c89b-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="8c89b-353">其他資源</span><span class="sxs-lookup"><span data-stu-id="8c89b-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="8c89b-354">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-355">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="8c89b-356">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="8c89b-357">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="8c89b-358">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8c89b-359">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="8c89b-360">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="8c89b-361">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="8c89b-362">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="8c89b-363">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="8c89b-364">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="8c89b-365">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="8c89b-366">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="8c89b-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="8c89b-367">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="8c89b-368">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-368">MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-369">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="8c89b-370">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-371">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="8c89b-372">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-372">Configure server options</span></span>

<span data-ttu-id="8c89b-373">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="8c89b-374">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-374">Option</span></span> | <span data-ttu-id="8c89b-375">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-375">Default Value</span></span> | <span data-ttu-id="8c89b-376">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="8c89b-377">30 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-377">30 seconds</span></span> | <span data-ttu-id="8c89b-378">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="8c89b-379">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="8c89b-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="8c89b-380">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="8c89b-381">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-381">15 seconds</span></span> | <span data-ttu-id="8c89b-382">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="8c89b-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8c89b-383">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-384">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-385">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-385">15 seconds</span></span> | <span data-ttu-id="8c89b-386">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8c89b-387">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8c89b-388">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8c89b-389">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="8c89b-389">All installed protocols</span></span> | <span data-ttu-id="8c89b-390">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-390">Protocols supported by this hub.</span></span> <span data-ttu-id="8c89b-391">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8c89b-392">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8c89b-393">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="8c89b-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="8c89b-394">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="8c89b-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="8c89b-395">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="8c89b-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="8c89b-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-396">32 KB</span></span> | <span data-ttu-id="8c89b-397">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="8c89b-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="8c89b-398">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8c89b-399">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="8c89b-400">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="8c89b-401">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="8c89b-402">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8c89b-403">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="8c89b-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="8c89b-404">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-404">Option</span></span> | <span data-ttu-id="8c89b-405">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-405">Default Value</span></span> | <span data-ttu-id="8c89b-406">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8c89b-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-407">32 KB</span></span> | <span data-ttu-id="8c89b-408">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="8c89b-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="8c89b-409">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8c89b-410">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="8c89b-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8c89b-411">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="8c89b-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8c89b-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-412">32 KB</span></span> | <span data-ttu-id="8c89b-413">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="8c89b-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="8c89b-414">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8c89b-415">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="8c89b-415">All Transports are enabled.</span></span> | <span data-ttu-id="8c89b-416">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8c89b-417">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-417">See below.</span></span> | <span data-ttu-id="8c89b-418">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8c89b-419">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-419">See below.</span></span> | <span data-ttu-id="8c89b-420">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="8c89b-421">0</span><span class="sxs-lookup"><span data-stu-id="8c89b-421">0</span></span> | <span data-ttu-id="8c89b-422">指定 negotiate 通訊協定的最小版本。</span><span class="sxs-lookup"><span data-stu-id="8c89b-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="8c89b-423">這是用來將用戶端限制在較新的版本。</span><span class="sxs-lookup"><span data-stu-id="8c89b-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="8c89b-424">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="8c89b-425">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-425">Option</span></span> | <span data-ttu-id="8c89b-426">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-426">Default Value</span></span> | <span data-ttu-id="8c89b-427">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="8c89b-428">90秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-428">90 seconds</span></span> | <span data-ttu-id="8c89b-429">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="8c89b-430">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="8c89b-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="8c89b-431">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="8c89b-432">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-432">Option</span></span> | <span data-ttu-id="8c89b-433">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-433">Default Value</span></span> | <span data-ttu-id="8c89b-434">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="8c89b-435">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-435">5 seconds</span></span> | <span data-ttu-id="8c89b-436">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="8c89b-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="8c89b-437">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="8c89b-438">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="8c89b-439">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-439">Configure client options</span></span>

<span data-ttu-id="8c89b-440">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="8c89b-441">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="8c89b-442">設定記錄</span><span class="sxs-lookup"><span data-stu-id="8c89b-442">Configure logging</span></span>

<span data-ttu-id="8c89b-443">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="8c89b-444">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="8c89b-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="8c89b-445">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-446">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="8c89b-447">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="8c89b-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="8c89b-448">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="8c89b-449">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="8c89b-450">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="8c89b-451">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="8c89b-452">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="8c89b-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="8c89b-453">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="8c89b-454">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="8c89b-455">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-455">The following table lists the available log levels.</span></span> <span data-ttu-id="8c89b-456">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="8c89b-457">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="8c89b-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="8c89b-458">String</span><span class="sxs-lookup"><span data-stu-id="8c89b-458">String</span></span>                      | <span data-ttu-id="8c89b-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="8c89b-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="8c89b-460">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="8c89b-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="8c89b-461">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="8c89b-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="8c89b-462">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="8c89b-463">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="8c89b-464">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="8c89b-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="8c89b-465">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="8c89b-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="8c89b-466">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="8c89b-467">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="8c89b-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="8c89b-468">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="8c89b-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="8c89b-469">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="8c89b-469">Configure allowed transports</span></span>

<span data-ttu-id="8c89b-470">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="8c89b-471">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="8c89b-472">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-472">All transports are enabled by default.</span></span>

<span data-ttu-id="8c89b-473">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="8c89b-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="8c89b-474">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="8c89b-475">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="8c89b-476">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="8c89b-477">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="8c89b-478">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="8c89b-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="8c89b-479">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="8c89b-479">Configure bearer authentication</span></span>

<span data-ttu-id="8c89b-480">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="8c89b-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="8c89b-481">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="8c89b-482">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="8c89b-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="8c89b-483">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="8c89b-484">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="8c89b-485">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="8c89b-486">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="8c89b-487">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="8c89b-488">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="8c89b-489">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="8c89b-490">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="8c89b-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-491">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-492">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-492">Option</span></span> | <span data-ttu-id="8c89b-493">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-493">Default value</span></span> | <span data-ttu-id="8c89b-494">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8c89b-495">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-496">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-496">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-497">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-498">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-499">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8c89b-500">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-500">15 seconds</span></span> | <span data-ttu-id="8c89b-501">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-502">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-503">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-504">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-505">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-505">15 seconds</span></span> | <span data-ttu-id="8c89b-506">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-507">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-508">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="8c89b-509">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="8c89b-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-511">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-511">Option</span></span> | <span data-ttu-id="8c89b-512">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-512">Default value</span></span> | <span data-ttu-id="8c89b-513">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8c89b-514">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-515">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-515">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-516">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8c89b-517">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-518">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="8c89b-519">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-520">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-521">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-522">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-523">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-523">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-524">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-524">Option</span></span> | <span data-ttu-id="8c89b-525">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-525">Default value</span></span> | <span data-ttu-id="8c89b-526">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8c89b-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8c89b-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8c89b-528">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-529">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-529">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-530">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-531">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-532">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8c89b-533">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-533">15 seconds</span></span> | <span data-ttu-id="8c89b-534">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-535">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-536">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-537">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="8c89b-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="8c89b-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="8c89b-539">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-540">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-541">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-542">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="8c89b-543">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-543">Configure additional options</span></span>

<span data-ttu-id="8c89b-544">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-545">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-546">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-546">.NET Option</span></span> |  <span data-ttu-id="8c89b-547">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-547">Default value</span></span> | <span data-ttu-id="8c89b-548">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="8c89b-549">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="8c89b-550">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-551">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-552">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="8c89b-553">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-553">Empty</span></span> | <span data-ttu-id="8c89b-554">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="8c89b-555">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-555">Empty</span></span> | <span data-ttu-id="8c89b-556">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="8c89b-557">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-557">Empty</span></span> | <span data-ttu-id="8c89b-558">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="8c89b-559">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-559">5 seconds</span></span> | <span data-ttu-id="8c89b-560">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="8c89b-560">WebSockets only.</span></span> <span data-ttu-id="8c89b-561">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="8c89b-562">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="8c89b-563">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-563">Empty</span></span> | <span data-ttu-id="8c89b-564">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="8c89b-565">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="8c89b-566">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="8c89b-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="8c89b-567">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="8c89b-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="8c89b-568">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="8c89b-569">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="8c89b-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="8c89b-570">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="8c89b-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="8c89b-571">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="8c89b-572">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="8c89b-573">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="8c89b-574">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="8c89b-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-576">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-576">JavaScript Option</span></span> | <span data-ttu-id="8c89b-577">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-577">Default Value</span></span> | <span data-ttu-id="8c89b-578">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="8c89b-579">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="8c89b-580">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="8c89b-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="8c89b-581">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-582">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-583">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-584">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-584">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-585">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-585">Java Option</span></span> | <span data-ttu-id="8c89b-586">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-586">Default Value</span></span> | <span data-ttu-id="8c89b-587">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="8c89b-588">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="8c89b-589">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-590">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-591">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="8c89b-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="8c89b-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="8c89b-593">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-593">Empty</span></span> | <span data-ttu-id="8c89b-594">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="8c89b-595">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="8c89b-596">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="8c89b-597">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="8c89b-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="8c89b-598">其他資源</span><span class="sxs-lookup"><span data-stu-id="8c89b-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="8c89b-599">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-600">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="8c89b-601">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="8c89b-602">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="8c89b-603">`AddJsonProtocol`可以在[加入 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8c89b-604">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="8c89b-605">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="8c89b-606">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="8c89b-607">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="8c89b-608">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="8c89b-609">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="8c89b-610">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="8c89b-611">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="8c89b-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="8c89b-612">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="8c89b-613">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-613">MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-614">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="8c89b-615">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-616">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="8c89b-617">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-617">Configure server options</span></span>

<span data-ttu-id="8c89b-618">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="8c89b-619">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-619">Option</span></span> | <span data-ttu-id="8c89b-620">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-620">Default Value</span></span> | <span data-ttu-id="8c89b-621">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="8c89b-622">30 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-622">30 seconds</span></span> | <span data-ttu-id="8c89b-623">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="8c89b-624">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="8c89b-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="8c89b-625">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="8c89b-626">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-626">15 seconds</span></span> | <span data-ttu-id="8c89b-627">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="8c89b-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8c89b-628">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-629">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-630">15 seconds</span></span> | <span data-ttu-id="8c89b-631">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8c89b-632">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8c89b-633">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8c89b-634">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="8c89b-634">All installed protocols</span></span> | <span data-ttu-id="8c89b-635">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-635">Protocols supported by this hub.</span></span> <span data-ttu-id="8c89b-636">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8c89b-637">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8c89b-638">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="8c89b-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="8c89b-639">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="8c89b-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="8c89b-640">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="8c89b-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="8c89b-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-641">32 KB</span></span> | <span data-ttu-id="8c89b-642">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="8c89b-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="8c89b-643">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8c89b-644">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="8c89b-645">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="8c89b-646">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="8c89b-647">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8c89b-648">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="8c89b-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="8c89b-649">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-649">Option</span></span> | <span data-ttu-id="8c89b-650">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-650">Default Value</span></span> | <span data-ttu-id="8c89b-651">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8c89b-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-652">32 KB</span></span> | <span data-ttu-id="8c89b-653">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="8c89b-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="8c89b-654">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8c89b-655">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="8c89b-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8c89b-656">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="8c89b-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8c89b-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-657">32 KB</span></span> | <span data-ttu-id="8c89b-658">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="8c89b-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="8c89b-659">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8c89b-660">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="8c89b-660">All Transports are enabled.</span></span> | <span data-ttu-id="8c89b-661">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8c89b-662">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-662">See below.</span></span> | <span data-ttu-id="8c89b-663">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8c89b-664">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-664">See below.</span></span> | <span data-ttu-id="8c89b-665">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="8c89b-666">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="8c89b-667">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-667">Option</span></span> | <span data-ttu-id="8c89b-668">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-668">Default Value</span></span> | <span data-ttu-id="8c89b-669">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="8c89b-670">90秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-670">90 seconds</span></span> | <span data-ttu-id="8c89b-671">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="8c89b-672">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="8c89b-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="8c89b-673">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="8c89b-674">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-674">Option</span></span> | <span data-ttu-id="8c89b-675">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-675">Default Value</span></span> | <span data-ttu-id="8c89b-676">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="8c89b-677">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-677">5 seconds</span></span> | <span data-ttu-id="8c89b-678">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="8c89b-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="8c89b-679">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="8c89b-680">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="8c89b-681">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-681">Configure client options</span></span>

<span data-ttu-id="8c89b-682">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="8c89b-683">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="8c89b-684">設定記錄</span><span class="sxs-lookup"><span data-stu-id="8c89b-684">Configure logging</span></span>

<span data-ttu-id="8c89b-685">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="8c89b-686">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="8c89b-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="8c89b-687">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-688">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="8c89b-689">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="8c89b-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="8c89b-690">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="8c89b-691">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="8c89b-692">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="8c89b-693">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="8c89b-694">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="8c89b-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="8c89b-695">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="8c89b-696">SignalR在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="8c89b-697">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-697">The following table lists the available log levels.</span></span> <span data-ttu-id="8c89b-698">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="8c89b-699">記錄在此層級的訊息， **或是在資料表中所列的層級**，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="8c89b-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="8c89b-700">String</span><span class="sxs-lookup"><span data-stu-id="8c89b-700">String</span></span>                      | <span data-ttu-id="8c89b-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="8c89b-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="8c89b-702">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="8c89b-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="8c89b-703">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="8c89b-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="8c89b-704">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="8c89b-705">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="8c89b-706">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="8c89b-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="8c89b-707">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="8c89b-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="8c89b-708">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="8c89b-709">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="8c89b-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="8c89b-710">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="8c89b-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="8c89b-711">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="8c89b-711">Configure allowed transports</span></span>

<span data-ttu-id="8c89b-712">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="8c89b-713">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="8c89b-714">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-714">All transports are enabled by default.</span></span>

<span data-ttu-id="8c89b-715">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="8c89b-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="8c89b-716">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="8c89b-717">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="8c89b-718">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="8c89b-719">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="8c89b-720">SignalRJAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="8c89b-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="8c89b-721">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="8c89b-721">Configure bearer authentication</span></span>

<span data-ttu-id="8c89b-722">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="8c89b-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="8c89b-723">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="8c89b-724">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="8c89b-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="8c89b-725">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="8c89b-726">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="8c89b-727">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="8c89b-728">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="8c89b-729">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="8c89b-730">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="8c89b-731">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="8c89b-732">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="8c89b-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-733">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-734">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-734">Option</span></span> | <span data-ttu-id="8c89b-735">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-735">Default value</span></span> | <span data-ttu-id="8c89b-736">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8c89b-737">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-738">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-738">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-739">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-740">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-741">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8c89b-742">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-742">15 seconds</span></span> | <span data-ttu-id="8c89b-743">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-744">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-745">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-746">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-747">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-747">15 seconds</span></span> | <span data-ttu-id="8c89b-748">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-749">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-750">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="8c89b-751">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="8c89b-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-753">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-753">Option</span></span> | <span data-ttu-id="8c89b-754">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-754">Default value</span></span> | <span data-ttu-id="8c89b-755">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8c89b-756">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-757">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-757">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-758">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8c89b-759">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-760">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="8c89b-761">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-762">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-763">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-764">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-765">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-765">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-766">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-766">Option</span></span> | <span data-ttu-id="8c89b-767">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-767">Default value</span></span> | <span data-ttu-id="8c89b-768">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8c89b-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8c89b-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8c89b-770">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-771">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-771">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-772">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-773">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-774">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8c89b-775">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-775">15 seconds</span></span> | <span data-ttu-id="8c89b-776">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-777">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-778">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-779">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="8c89b-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="8c89b-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="8c89b-781">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-782">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-783">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-784">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="8c89b-785">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-785">Configure additional options</span></span>

<span data-ttu-id="8c89b-786">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-787">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-788">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-788">.NET Option</span></span> |  <span data-ttu-id="8c89b-789">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-789">Default value</span></span> | <span data-ttu-id="8c89b-790">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="8c89b-791">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="8c89b-792">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-793">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-794">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="8c89b-795">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-795">Empty</span></span> | <span data-ttu-id="8c89b-796">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="8c89b-797">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-797">Empty</span></span> | <span data-ttu-id="8c89b-798">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="8c89b-799">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-799">Empty</span></span> | <span data-ttu-id="8c89b-800">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="8c89b-801">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-801">5 seconds</span></span> | <span data-ttu-id="8c89b-802">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="8c89b-802">WebSockets only.</span></span> <span data-ttu-id="8c89b-803">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="8c89b-804">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="8c89b-805">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-805">Empty</span></span> | <span data-ttu-id="8c89b-806">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="8c89b-807">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="8c89b-808">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="8c89b-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="8c89b-809">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="8c89b-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="8c89b-810">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="8c89b-811">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="8c89b-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="8c89b-812">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="8c89b-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="8c89b-813">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="8c89b-814">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="8c89b-815">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="8c89b-816">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="8c89b-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-818">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-818">JavaScript Option</span></span> | <span data-ttu-id="8c89b-819">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-819">Default Value</span></span> | <span data-ttu-id="8c89b-820">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="8c89b-821">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="8c89b-822">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="8c89b-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="8c89b-823">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-824">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-825">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-826">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-826">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-827">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-827">Java Option</span></span> | <span data-ttu-id="8c89b-828">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-828">Default Value</span></span> | <span data-ttu-id="8c89b-829">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="8c89b-830">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="8c89b-831">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-832">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-833">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="8c89b-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="8c89b-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="8c89b-835">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-835">Empty</span></span> | <span data-ttu-id="8c89b-836">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="8c89b-837">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="8c89b-838">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="8c89b-839">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="8c89b-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="8c89b-840">其他資源</span><span class="sxs-lookup"><span data-stu-id="8c89b-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="8c89b-841">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-842">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="8c89b-843">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="8c89b-844">您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 SignalR 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8c89b-845">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="8c89b-846">該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="8c89b-847">如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="8c89b-848">舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="8c89b-849">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="8c89b-850">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="8c89b-851">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="8c89b-852">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-852">MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-853">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="8c89b-854">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-855">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="8c89b-856">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-856">Configure server options</span></span>

<span data-ttu-id="8c89b-857">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="8c89b-858">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-858">Option</span></span> | <span data-ttu-id="8c89b-859">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-859">Default Value</span></span> | <span data-ttu-id="8c89b-860">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="8c89b-861">30 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-861">30 seconds</span></span> | <span data-ttu-id="8c89b-862">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="8c89b-863">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="8c89b-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="8c89b-864">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="8c89b-865">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-865">15 seconds</span></span> | <span data-ttu-id="8c89b-866">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="8c89b-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8c89b-867">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-868">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-869">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-869">15 seconds</span></span> | <span data-ttu-id="8c89b-870">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8c89b-871">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8c89b-872">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8c89b-873">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="8c89b-873">All installed protocols</span></span> | <span data-ttu-id="8c89b-874">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-874">Protocols supported by this hub.</span></span> <span data-ttu-id="8c89b-875">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8c89b-876">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8c89b-877">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="8c89b-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="8c89b-878">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8c89b-879">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="8c89b-880">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="8c89b-881">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="8c89b-882">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8c89b-883">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="8c89b-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="8c89b-884">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-884">Option</span></span> | <span data-ttu-id="8c89b-885">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-885">Default Value</span></span> | <span data-ttu-id="8c89b-886">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8c89b-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-887">32 KB</span></span> | <span data-ttu-id="8c89b-888">從伺服器緩衝的用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="8c89b-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="8c89b-889">提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="8c89b-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8c89b-890">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="8c89b-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8c89b-891">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="8c89b-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8c89b-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-892">32 KB</span></span> | <span data-ttu-id="8c89b-893">由伺服器緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="8c89b-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="8c89b-894">提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="8c89b-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8c89b-895">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="8c89b-895">All Transports are enabled.</span></span> | <span data-ttu-id="8c89b-896">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8c89b-897">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-897">See below.</span></span> | <span data-ttu-id="8c89b-898">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8c89b-899">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-899">See below.</span></span> | <span data-ttu-id="8c89b-900">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="8c89b-901">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="8c89b-902">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-902">Option</span></span> | <span data-ttu-id="8c89b-903">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-903">Default Value</span></span> | <span data-ttu-id="8c89b-904">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="8c89b-905">90秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-905">90 seconds</span></span> | <span data-ttu-id="8c89b-906">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="8c89b-907">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="8c89b-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="8c89b-908">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="8c89b-909">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-909">Option</span></span> | <span data-ttu-id="8c89b-910">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-910">Default Value</span></span> | <span data-ttu-id="8c89b-911">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="8c89b-912">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-912">5 seconds</span></span> | <span data-ttu-id="8c89b-913">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="8c89b-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="8c89b-914">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="8c89b-915">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="8c89b-916">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-916">Configure client options</span></span>

<span data-ttu-id="8c89b-917">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="8c89b-918">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="8c89b-919">設定記錄</span><span class="sxs-lookup"><span data-stu-id="8c89b-919">Configure logging</span></span>

<span data-ttu-id="8c89b-920">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="8c89b-921">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="8c89b-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="8c89b-922">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-923">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="8c89b-924">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="8c89b-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="8c89b-925">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="8c89b-926">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="8c89b-927">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="8c89b-928">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="8c89b-929">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="8c89b-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="8c89b-930">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="8c89b-931">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="8c89b-932">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="8c89b-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="8c89b-933">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="8c89b-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="8c89b-934">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="8c89b-935">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="8c89b-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="8c89b-936">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="8c89b-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="8c89b-937">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="8c89b-937">Configure allowed transports</span></span>

<span data-ttu-id="8c89b-938">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="8c89b-939">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="8c89b-940">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-940">All transports are enabled by default.</span></span>

<span data-ttu-id="8c89b-941">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="8c89b-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="8c89b-942">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="8c89b-943">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="8c89b-944">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="8c89b-944">Configure bearer authentication</span></span>

<span data-ttu-id="8c89b-945">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="8c89b-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="8c89b-946">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="8c89b-947">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="8c89b-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="8c89b-948">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="8c89b-949">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="8c89b-950">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="8c89b-951">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="8c89b-952">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="8c89b-953">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="8c89b-954">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="8c89b-955">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="8c89b-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-956">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-957">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-957">Option</span></span> | <span data-ttu-id="8c89b-958">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-958">Default value</span></span> | <span data-ttu-id="8c89b-959">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8c89b-960">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-961">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-961">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-962">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-963">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-964">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8c89b-965">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-965">15 seconds</span></span> | <span data-ttu-id="8c89b-966">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-967">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-968">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-969">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-970">15 seconds</span></span> | <span data-ttu-id="8c89b-971">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-972">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-973">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="8c89b-974">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="8c89b-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-976">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-976">Option</span></span> | <span data-ttu-id="8c89b-977">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-977">Default value</span></span> | <span data-ttu-id="8c89b-978">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8c89b-979">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-980">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-980">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-981">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8c89b-982">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-983">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="8c89b-984">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-985">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-986">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-987">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-988">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-988">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-989">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-989">Option</span></span> | <span data-ttu-id="8c89b-990">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-990">Default value</span></span> | <span data-ttu-id="8c89b-991">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8c89b-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8c89b-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8c89b-993">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-994">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-994">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-995">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-996">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-997">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8c89b-998">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-998">15 seconds</span></span> | <span data-ttu-id="8c89b-999">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-1000">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-1001">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-1002">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="8c89b-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="8c89b-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="8c89b-1004">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-1005">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="8c89b-1006">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="8c89b-1007">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="8c89b-1008">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1008">Configure additional options</span></span>

<span data-ttu-id="8c89b-1009">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-1011">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1011">.NET Option</span></span> |  <span data-ttu-id="8c89b-1012">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1012">Default value</span></span> | <span data-ttu-id="8c89b-1013">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="8c89b-1014">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="8c89b-1015">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-1016">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-1017">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="8c89b-1018">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1018">Empty</span></span> | <span data-ttu-id="8c89b-1019">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="8c89b-1020">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1020">Empty</span></span> | <span data-ttu-id="8c89b-1021">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="8c89b-1022">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1022">Empty</span></span> | <span data-ttu-id="8c89b-1023">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="8c89b-1024">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-1024">5 seconds</span></span> | <span data-ttu-id="8c89b-1025">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1025">WebSockets only.</span></span> <span data-ttu-id="8c89b-1026">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="8c89b-1027">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="8c89b-1028">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1028">Empty</span></span> | <span data-ttu-id="8c89b-1029">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="8c89b-1030">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="8c89b-1031">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="8c89b-1032">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="8c89b-1033">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="8c89b-1034">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="8c89b-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="8c89b-1035">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="8c89b-1036">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="8c89b-1037">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="8c89b-1038">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="8c89b-1039">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="8c89b-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-1041">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1041">JavaScript Option</span></span> | <span data-ttu-id="8c89b-1042">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1042">Default Value</span></span> | <span data-ttu-id="8c89b-1043">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="8c89b-1044">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="8c89b-1045">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="8c89b-1046">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-1047">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-1048">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-1049">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-1050">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1050">Java Option</span></span> | <span data-ttu-id="8c89b-1051">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1051">Default Value</span></span> | <span data-ttu-id="8c89b-1052">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="8c89b-1053">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="8c89b-1054">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-1055">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-1056">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="8c89b-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="8c89b-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="8c89b-1058">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1058">Empty</span></span> | <span data-ttu-id="8c89b-1059">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="8c89b-1060">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="8c89b-1061">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="8c89b-1062">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="8c89b-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="8c89b-1063">其他資源</span><span class="sxs-lookup"><span data-stu-id="8c89b-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="8c89b-1064">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-1065">ASP.NET Core SignalR 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="8c89b-1066">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="8c89b-1067">您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 SignalR 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8c89b-1068">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="8c89b-1069">該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="8c89b-1070">如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="8c89b-1071">舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="8c89b-1072">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="8c89b-1073">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="8c89b-1074">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="8c89b-1075">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1075">MessagePack serialization options</span></span>

<span data-ttu-id="8c89b-1076">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="8c89b-1077">如需詳細資訊，請參閱[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-1078">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="8c89b-1079">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1079">Configure server options</span></span>

<span data-ttu-id="8c89b-1080">下表說明設定中樞的選項 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="8c89b-1081">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1081">Option</span></span> | <span data-ttu-id="8c89b-1082">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1082">Default Value</span></span> | <span data-ttu-id="8c89b-1083">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="8c89b-1084">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-1084">15 seconds</span></span> | <span data-ttu-id="8c89b-1085">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="8c89b-1086">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-1087">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="8c89b-1088">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-1088">15 seconds</span></span> | <span data-ttu-id="8c89b-1089">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="8c89b-1090">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="8c89b-1091">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="8c89b-1092">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="8c89b-1092">All installed protocols</span></span> | <span data-ttu-id="8c89b-1093">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="8c89b-1094">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="8c89b-1095">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="8c89b-1096">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="8c89b-1097">您可以為所有中樞設定選項，方法是提供選項委派給 `AddSignalR` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8c89b-1098">單一中樞的選項會覆寫中提供的全域選項 `AddSignalR` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="8c89b-1099">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="8c89b-1100">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="8c89b-1101">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8c89b-1102">下表說明設定 ASP.NET Core SignalR ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="8c89b-1103">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1103">Option</span></span> | <span data-ttu-id="8c89b-1104">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1104">Default Value</span></span> | <span data-ttu-id="8c89b-1105">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="8c89b-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-1106">32 KB</span></span> | <span data-ttu-id="8c89b-1107">從伺服器緩衝的用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="8c89b-1108">提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="8c89b-1109">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="8c89b-1110">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="8c89b-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="8c89b-1111">32 KB</span></span> | <span data-ttu-id="8c89b-1112">由伺服器緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="8c89b-1113">提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="8c89b-1114">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1114">All Transports are enabled.</span></span> | <span data-ttu-id="8c89b-1115">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="8c89b-1116">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1116">See below.</span></span> | <span data-ttu-id="8c89b-1117">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="8c89b-1118">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1118">See below.</span></span> | <span data-ttu-id="8c89b-1119">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="8c89b-1120">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="8c89b-1121">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1121">Option</span></span> | <span data-ttu-id="8c89b-1122">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1122">Default Value</span></span> | <span data-ttu-id="8c89b-1123">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="8c89b-1124">90秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-1124">90 seconds</span></span> | <span data-ttu-id="8c89b-1125">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="8c89b-1126">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="8c89b-1127">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="8c89b-1128">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1128">Option</span></span> | <span data-ttu-id="8c89b-1129">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1129">Default Value</span></span> | <span data-ttu-id="8c89b-1130">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="8c89b-1131">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-1131">5 seconds</span></span> | <span data-ttu-id="8c89b-1132">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="8c89b-1133">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="8c89b-1134">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="8c89b-1135">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1135">Configure client options</span></span>

<span data-ttu-id="8c89b-1136">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="8c89b-1137">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="8c89b-1138">設定記錄</span><span class="sxs-lookup"><span data-stu-id="8c89b-1138">Configure logging</span></span>

<span data-ttu-id="8c89b-1139">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="8c89b-1140">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="8c89b-1141">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="8c89b-1142">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="8c89b-1143">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="8c89b-1144">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="8c89b-1145">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="8c89b-1146">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="8c89b-1147">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="8c89b-1148">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="8c89b-1149">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="8c89b-1150">如需有關記錄的詳細資訊，請參閱[ SignalR 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="8c89b-1151">SignalRJAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="8c89b-1152">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="8c89b-1153">下列程式碼片段說明如何搭配使用 `java.util.logging` SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="8c89b-1154">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="8c89b-1155">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="8c89b-1156">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="8c89b-1156">Configure allowed transports</span></span>

<span data-ttu-id="8c89b-1157">使用的傳輸 SignalR 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="8c89b-1158">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="8c89b-1159">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="8c89b-1160">例如，若要停用伺服器傳送的事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="8c89b-1161">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="8c89b-1162">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="8c89b-1162">Configure bearer authentication</span></span>

<span data-ttu-id="8c89b-1163">若要提供驗證資料和 SignalR 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="8c89b-1164">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="8c89b-1165">在 JavaScript 用戶端中，存取權杖是用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在伺服器傳送的事件和 websocket 要求中) 套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="8c89b-1166">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="8c89b-1167">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="8c89b-1168">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="8c89b-1169">在 SignalR JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="8c89b-1170">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="8c89b-1171">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="8c89b-1172">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="8c89b-1173">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-1175">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1175">Option</span></span> | <span data-ttu-id="8c89b-1176">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1176">Default value</span></span> | <span data-ttu-id="8c89b-1177">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="8c89b-1178">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-1179">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1179">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-1180">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-1181">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-1182">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="8c89b-1183">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-1183">15 seconds</span></span> | <span data-ttu-id="8c89b-1184">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-1185">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="8c89b-1186">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-1187">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="8c89b-1188">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="8c89b-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-1190">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1190">Option</span></span> | <span data-ttu-id="8c89b-1191">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1191">Default value</span></span> | <span data-ttu-id="8c89b-1192">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="8c89b-1193">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-1194">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1194">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-1195">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="8c89b-1196">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-1197">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-1198">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-1199">選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1199">Option</span></span> | <span data-ttu-id="8c89b-1200">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1200">Default value</span></span> | <span data-ttu-id="8c89b-1201">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="8c89b-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="8c89b-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="8c89b-1203">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="8c89b-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="8c89b-1204">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1204">Timeout for server activity.</span></span> <span data-ttu-id="8c89b-1205">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-1206">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="8c89b-1207">建議的值是至少兩個伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="8c89b-1208">15 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-1208">15 seconds</span></span> | <span data-ttu-id="8c89b-1209">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="8c89b-1210">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="8c89b-1211">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="8c89b-1212">如需信號交換程式的詳細資訊，請參閱[ SignalR 中樞通訊協定規格](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="8c89b-1213">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1213">Configure additional options</span></span>

<span data-ttu-id="8c89b-1214">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="8c89b-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="8c89b-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="8c89b-1216">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1216">.NET Option</span></span> |  <span data-ttu-id="8c89b-1217">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1217">Default value</span></span> | <span data-ttu-id="8c89b-1218">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="8c89b-1219">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="8c89b-1220">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-1221">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-1222">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="8c89b-1223">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1223">Empty</span></span> | <span data-ttu-id="8c89b-1224">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="8c89b-1225">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1225">Empty</span></span> | <span data-ttu-id="8c89b-1226">cookie要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="8c89b-1227">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1227">Empty</span></span> | <span data-ttu-id="8c89b-1228">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="8c89b-1229">5 秒</span><span class="sxs-lookup"><span data-stu-id="8c89b-1229">5 seconds</span></span> | <span data-ttu-id="8c89b-1230">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1230">WebSockets only.</span></span> <span data-ttu-id="8c89b-1231">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="8c89b-1232">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="8c89b-1233">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1233">Empty</span></span> | <span data-ttu-id="8c89b-1234">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="8c89b-1235">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="8c89b-1236">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="8c89b-1237">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="8c89b-1238">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="8c89b-1239">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 Cookie s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="8c89b-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="8c89b-1240">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="8c89b-1241">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="8c89b-1242">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="8c89b-1243">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="8c89b-1244">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="8c89b-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c89b-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="8c89b-1246">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1246">JavaScript Option</span></span> | <span data-ttu-id="8c89b-1247">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1247">Default Value</span></span> | <span data-ttu-id="8c89b-1248">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="8c89b-1249">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="8c89b-1250">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="8c89b-1251">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-1252">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-1253">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="8c89b-1254">Java</span><span class="sxs-lookup"><span data-stu-id="8c89b-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="8c89b-1255">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="8c89b-1255">Java Option</span></span> | <span data-ttu-id="8c89b-1256">預設值</span><span class="sxs-lookup"><span data-stu-id="8c89b-1256">Default Value</span></span> | <span data-ttu-id="8c89b-1257">描述</span><span class="sxs-lookup"><span data-stu-id="8c89b-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="8c89b-1258">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="8c89b-1259">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="8c89b-1260">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援**。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="8c89b-1261">使用 Azure 服務時，無法啟用此設定 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="8c89b-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="8c89b-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="8c89b-1263">空白</span><span class="sxs-lookup"><span data-stu-id="8c89b-1263">Empty</span></span> | <span data-ttu-id="8c89b-1264">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="8c89b-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="8c89b-1265">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="8c89b-1266">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="8c89b-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="8c89b-1267">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="8c89b-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="8c89b-1268">其他資源</span><span class="sxs-lookup"><span data-stu-id="8c89b-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
