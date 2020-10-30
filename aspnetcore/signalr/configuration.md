---
title: 'ASP.NET Core :::no-loc(SignalR)::: 設定'
author: bradygaster
description: '瞭解如何設定 ASP.NET Core :::no-loc(SignalR)::: 應用程式。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/configuration
ms.openlocfilehash: 7dac8c84683553a52e07ecc61c8bcf8616e77dc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061232"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="79d8a-103">ASP.NET Core :::no-loc(SignalR)::: 設定</span><span class="sxs-lookup"><span data-stu-id="79d8a-103">ASP.NET Core :::no-loc(SignalR)::: configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="79d8a-104">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-105">ASP.NET Core :::no-loc(SignalR)::: 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-105">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="79d8a-106">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="79d8a-107">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="79d8a-108">`AddJsonProtocol`可以在[加入 :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-108">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="79d8a-109">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="79d8a-110">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="79d8a-111">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="79d8a-112">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="79d8a-113">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="79d8a-114">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="79d8a-115">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="79d8a-116">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="79d8a-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="79d8a-117">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="79d8a-118">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-118">MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-119">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="79d8a-120">如需詳細資訊，請參閱[中 :::no-loc(SignalR)::: 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-120">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-121">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="79d8a-122">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-122">Configure server options</span></span>

<span data-ttu-id="79d8a-123">下表說明設定中樞的選項 :::no-loc(SignalR)::: ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-123">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="79d8a-124">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-124">Option</span></span> | <span data-ttu-id="79d8a-125">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-125">Default Value</span></span> | <span data-ttu-id="79d8a-126">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="79d8a-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-127">30 seconds</span></span> | <span data-ttu-id="79d8a-128">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="79d8a-129">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="79d8a-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="79d8a-130">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="79d8a-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-131">15 seconds</span></span> | <span data-ttu-id="79d8a-132">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="79d8a-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="79d8a-133">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-134">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-134">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-135">15 seconds</span></span> | <span data-ttu-id="79d8a-136">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="79d8a-137">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="79d8a-138">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="79d8a-139">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="79d8a-139">All installed protocols</span></span> | <span data-ttu-id="79d8a-140">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-140">Protocols supported by this hub.</span></span> <span data-ttu-id="79d8a-141">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="79d8a-142">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="79d8a-143">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="79d8a-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="79d8a-144">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="79d8a-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="79d8a-145">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="79d8a-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="79d8a-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-146">32 KB</span></span> | <span data-ttu-id="79d8a-147">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="79d8a-148">1</span><span class="sxs-lookup"><span data-stu-id="79d8a-148">1</span></span> | <span data-ttu-id="79d8a-149">每個用戶端在進行佇列之前可以平行呼叫的中樞方法數目上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="79d8a-150">您可以為所有中樞設定選項，方法是提供選項委派給 `Add:::no-loc(SignalR):::` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-150">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="79d8a-151">單一中樞的選項會覆寫中提供的全域選項 `Add:::no-loc(SignalR):::` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-151">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="79d8a-152">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="79d8a-153">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="79d8a-154">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="79d8a-155">下表說明設定 ASP.NET Core :::no-loc(SignalR)::: ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="79d8a-155">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="79d8a-156">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-156">Option</span></span> | <span data-ttu-id="79d8a-157">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-157">Default Value</span></span> | <span data-ttu-id="79d8a-158">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="79d8a-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-159">32 KB</span></span> | <span data-ttu-id="79d8a-160">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="79d8a-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="79d8a-161">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="79d8a-162">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="79d8a-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="79d8a-163">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="79d8a-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="79d8a-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-164">32 KB</span></span> | <span data-ttu-id="79d8a-165">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="79d8a-166">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="79d8a-167">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="79d8a-167">All Transports are enabled.</span></span> | <span data-ttu-id="79d8a-168">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="79d8a-169">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-169">See below.</span></span> | <span data-ttu-id="79d8a-170">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="79d8a-171">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-171">See below.</span></span> | <span data-ttu-id="79d8a-172">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="79d8a-173">0</span><span class="sxs-lookup"><span data-stu-id="79d8a-173">0</span></span> | <span data-ttu-id="79d8a-174">指定 negotiate 通訊協定的最小版本。</span><span class="sxs-lookup"><span data-stu-id="79d8a-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="79d8a-175">這是用來將用戶端限制在較新的版本。</span><span class="sxs-lookup"><span data-stu-id="79d8a-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="79d8a-176">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="79d8a-177">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-177">Option</span></span> | <span data-ttu-id="79d8a-178">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-178">Default Value</span></span> | <span data-ttu-id="79d8a-179">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="79d8a-180">90秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-180">90 seconds</span></span> | <span data-ttu-id="79d8a-181">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="79d8a-182">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="79d8a-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="79d8a-183">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="79d8a-184">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-184">Option</span></span> | <span data-ttu-id="79d8a-185">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-185">Default Value</span></span> | <span data-ttu-id="79d8a-186">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="79d8a-187">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-187">5 seconds</span></span> | <span data-ttu-id="79d8a-188">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="79d8a-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="79d8a-189">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="79d8a-190">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="79d8a-191">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-191">Configure client options</span></span>

<span data-ttu-id="79d8a-192">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="79d8a-193">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="79d8a-194">設定記錄</span><span class="sxs-lookup"><span data-stu-id="79d8a-194">Configure logging</span></span>

<span data-ttu-id="79d8a-195">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="79d8a-196">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="79d8a-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="79d8a-197">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-198">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="79d8a-199">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="79d8a-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="79d8a-200">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="79d8a-201">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="79d8a-202">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="79d8a-203">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="79d8a-204">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="79d8a-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="79d8a-205">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="79d8a-206">:::no-loc(SignalR):::在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-206">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="79d8a-207">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-207">The following table lists the available log levels.</span></span> <span data-ttu-id="79d8a-208">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="79d8a-209">記錄在此層級的訊息， **或是在資料表中所列的層級** ，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="79d8a-209">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="79d8a-210">String</span><span class="sxs-lookup"><span data-stu-id="79d8a-210">String</span></span>                      | <span data-ttu-id="79d8a-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="79d8a-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="79d8a-212">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="79d8a-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="79d8a-213">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="79d8a-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="79d8a-214">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="79d8a-215">如需有關記錄的詳細資訊，請參閱[ :::no-loc(SignalR)::: 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-215">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="79d8a-216">:::no-loc(SignalR):::JAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="79d8a-216">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="79d8a-217">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="79d8a-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="79d8a-218">下列程式碼片段說明如何搭配使用 `java.util.logging` :::no-loc(SignalR)::: JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-218">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="79d8a-219">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="79d8a-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="79d8a-220">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="79d8a-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="79d8a-221">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="79d8a-221">Configure allowed transports</span></span>

<span data-ttu-id="79d8a-222">使用的傳輸 :::no-loc(SignalR)::: 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-222">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="79d8a-223">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="79d8a-224">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-224">All transports are enabled by default.</span></span>

<span data-ttu-id="79d8a-225">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="79d8a-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="79d8a-226">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="79d8a-227">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="79d8a-228">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="79d8a-229">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="79d8a-230">:::no-loc(SignalR):::JAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="79d8a-230">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="79d8a-231">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="79d8a-231">Configure bearer authentication</span></span>

<span data-ttu-id="79d8a-232">若要提供驗證資料和 :::no-loc(SignalR)::: 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="79d8a-232">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="79d8a-233">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="79d8a-234">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="79d8a-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="79d8a-235">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="79d8a-236">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="79d8a-237">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="79d8a-238">在 :::no-loc(SignalR)::: JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-238">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="79d8a-239">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="79d8a-240">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="79d8a-241">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="79d8a-242">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="79d8a-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-243">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-244">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-244">Option</span></span> | <span data-ttu-id="79d8a-245">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-245">Default value</span></span> | <span data-ttu-id="79d8a-246">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="79d8a-247">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-248">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-248">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-249">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-250">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-251">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="79d8a-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-252">15 seconds</span></span> | <span data-ttu-id="79d8a-253">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-254">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-255">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-256">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-256">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-257">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-257">15 seconds</span></span> | <span data-ttu-id="79d8a-258">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-259">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-260">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="79d8a-261">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="79d8a-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-263">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-263">Option</span></span> | <span data-ttu-id="79d8a-264">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-264">Default value</span></span> | <span data-ttu-id="79d8a-265">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="79d8a-266">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-267">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-267">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-268">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="79d8a-269">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-270">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="79d8a-271">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-272">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-273">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-274">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-275">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-275">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-276">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-276">Option</span></span> | <span data-ttu-id="79d8a-277">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-277">Default value</span></span> | <span data-ttu-id="79d8a-278">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="79d8a-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="79d8a-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="79d8a-280">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-281">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-281">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-282">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-283">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-284">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="79d8a-285">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-285">15 seconds</span></span> | <span data-ttu-id="79d8a-286">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-287">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-288">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-289">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-289">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="79d8a-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="79d8a-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="79d8a-291">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-292">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-293">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-294">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="79d8a-295">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-295">Configure additional options</span></span>

<span data-ttu-id="79d8a-296">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-297">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-298">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-298">.NET Option</span></span> |  <span data-ttu-id="79d8a-299">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-299">Default value</span></span> | <span data-ttu-id="79d8a-300">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="79d8a-301">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="79d8a-302">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-303">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-303">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-304">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-304">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="79d8a-305">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-305">Empty</span></span> | <span data-ttu-id="79d8a-306">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="79d8a-307">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-307">Empty</span></span> | <span data-ttu-id="79d8a-308">:::no-loc(cookie):::要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-308">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="79d8a-309">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-309">Empty</span></span> | <span data-ttu-id="79d8a-310">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="79d8a-311">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-311">5 seconds</span></span> | <span data-ttu-id="79d8a-312">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="79d8a-312">WebSockets only.</span></span> <span data-ttu-id="79d8a-313">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="79d8a-314">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="79d8a-315">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-315">Empty</span></span> | <span data-ttu-id="79d8a-316">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="79d8a-317">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="79d8a-318">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="79d8a-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="79d8a-319">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="79d8a-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="79d8a-320">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="79d8a-321">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 :::no-loc(Cookie)::: s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="79d8a-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="79d8a-322">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="79d8a-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="79d8a-323">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="79d8a-324">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="79d8a-325">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="79d8a-326">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="79d8a-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-328">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-328">JavaScript Option</span></span> | <span data-ttu-id="79d8a-329">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-329">Default Value</span></span> | <span data-ttu-id="79d8a-330">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="79d8a-331">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="79d8a-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="79d8a-333">每個 HTTP 要求傳送的標頭字典。</span><span class="sxs-lookup"><span data-stu-id="79d8a-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="79d8a-334">在瀏覽器中傳送標頭不適用於 Websocket 或 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 串流。</span><span class="sxs-lookup"><span data-stu-id="79d8a-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="79d8a-335">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="79d8a-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="79d8a-336">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-337">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-337">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-338">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-338">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="79d8a-339">指定是否將使用 CORS 要求傳送認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="79d8a-340">Azure App Service 會使用 :::no-loc(cookie)::: 來進行粘滯話，且必須啟用此選項才能正確運作。</span><span class="sxs-lookup"><span data-stu-id="79d8a-340">Azure App Service uses :::no-loc(cookie):::s for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="79d8a-341">如需 CORS 與的詳細資訊 :::no-loc(SignalR)::: ，請參閱 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-341">For more info on CORS with :::no-loc(SignalR):::, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-342">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-342">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-343">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-343">Java Option</span></span> | <span data-ttu-id="79d8a-344">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-344">Default Value</span></span> | <span data-ttu-id="79d8a-345">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="79d8a-346">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="79d8a-347">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-348">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-348">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-349">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-349">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="79d8a-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="79d8a-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="79d8a-351">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-351">Empty</span></span> | <span data-ttu-id="79d8a-352">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="79d8a-353">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="79d8a-354">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="79d8a-355">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="79d8a-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="79d8a-356">其他資源</span><span class="sxs-lookup"><span data-stu-id="79d8a-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="79d8a-357">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-358">ASP.NET Core :::no-loc(SignalR)::: 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-358">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="79d8a-359">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="79d8a-360">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="79d8a-361">`AddJsonProtocol`可以在[加入 :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-361">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="79d8a-362">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="79d8a-363">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="79d8a-364">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="79d8a-365">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="79d8a-366">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="79d8a-367">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="79d8a-368">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="79d8a-369">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="79d8a-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="79d8a-370">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="79d8a-371">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-371">MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-372">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="79d8a-373">如需詳細資訊，請參閱[中 :::no-loc(SignalR)::: 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-373">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-374">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="79d8a-375">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-375">Configure server options</span></span>

<span data-ttu-id="79d8a-376">下表說明設定中樞的選項 :::no-loc(SignalR)::: ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-376">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="79d8a-377">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-377">Option</span></span> | <span data-ttu-id="79d8a-378">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-378">Default Value</span></span> | <span data-ttu-id="79d8a-379">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="79d8a-380">30 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-380">30 seconds</span></span> | <span data-ttu-id="79d8a-381">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="79d8a-382">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="79d8a-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="79d8a-383">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="79d8a-384">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-384">15 seconds</span></span> | <span data-ttu-id="79d8a-385">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="79d8a-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="79d8a-386">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-387">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-387">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-388">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-388">15 seconds</span></span> | <span data-ttu-id="79d8a-389">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="79d8a-390">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="79d8a-391">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="79d8a-392">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="79d8a-392">All installed protocols</span></span> | <span data-ttu-id="79d8a-393">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-393">Protocols supported by this hub.</span></span> <span data-ttu-id="79d8a-394">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="79d8a-395">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="79d8a-396">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="79d8a-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="79d8a-397">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="79d8a-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="79d8a-398">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="79d8a-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="79d8a-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-399">32 KB</span></span> | <span data-ttu-id="79d8a-400">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="79d8a-401">您可以為所有中樞設定選項，方法是提供選項委派給 `Add:::no-loc(SignalR):::` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-401">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="79d8a-402">單一中樞的選項會覆寫中提供的全域選項 `Add:::no-loc(SignalR):::` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-402">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="79d8a-403">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="79d8a-404">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="79d8a-405">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="79d8a-406">下表說明設定 ASP.NET Core :::no-loc(SignalR)::: ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="79d8a-406">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="79d8a-407">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-407">Option</span></span> | <span data-ttu-id="79d8a-408">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-408">Default Value</span></span> | <span data-ttu-id="79d8a-409">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="79d8a-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-410">32 KB</span></span> | <span data-ttu-id="79d8a-411">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="79d8a-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="79d8a-412">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="79d8a-413">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="79d8a-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="79d8a-414">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="79d8a-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="79d8a-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-415">32 KB</span></span> | <span data-ttu-id="79d8a-416">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="79d8a-417">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="79d8a-418">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="79d8a-418">All Transports are enabled.</span></span> | <span data-ttu-id="79d8a-419">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="79d8a-420">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-420">See below.</span></span> | <span data-ttu-id="79d8a-421">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="79d8a-422">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-422">See below.</span></span> | <span data-ttu-id="79d8a-423">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="79d8a-424">0</span><span class="sxs-lookup"><span data-stu-id="79d8a-424">0</span></span> | <span data-ttu-id="79d8a-425">指定 negotiate 通訊協定的最小版本。</span><span class="sxs-lookup"><span data-stu-id="79d8a-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="79d8a-426">這是用來將用戶端限制在較新的版本。</span><span class="sxs-lookup"><span data-stu-id="79d8a-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="79d8a-427">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="79d8a-428">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-428">Option</span></span> | <span data-ttu-id="79d8a-429">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-429">Default Value</span></span> | <span data-ttu-id="79d8a-430">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="79d8a-431">90秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-431">90 seconds</span></span> | <span data-ttu-id="79d8a-432">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="79d8a-433">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="79d8a-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="79d8a-434">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="79d8a-435">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-435">Option</span></span> | <span data-ttu-id="79d8a-436">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-436">Default Value</span></span> | <span data-ttu-id="79d8a-437">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="79d8a-438">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-438">5 seconds</span></span> | <span data-ttu-id="79d8a-439">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="79d8a-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="79d8a-440">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="79d8a-441">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="79d8a-442">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-442">Configure client options</span></span>

<span data-ttu-id="79d8a-443">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="79d8a-444">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="79d8a-445">設定記錄</span><span class="sxs-lookup"><span data-stu-id="79d8a-445">Configure logging</span></span>

<span data-ttu-id="79d8a-446">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="79d8a-447">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="79d8a-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="79d8a-448">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-449">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="79d8a-450">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="79d8a-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="79d8a-451">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="79d8a-452">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="79d8a-453">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="79d8a-454">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="79d8a-455">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="79d8a-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="79d8a-456">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="79d8a-457">:::no-loc(SignalR):::在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-457">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="79d8a-458">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-458">The following table lists the available log levels.</span></span> <span data-ttu-id="79d8a-459">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="79d8a-460">記錄在此層級的訊息， **或是在資料表中所列的層級** ，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="79d8a-460">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="79d8a-461">String</span><span class="sxs-lookup"><span data-stu-id="79d8a-461">String</span></span>                      | <span data-ttu-id="79d8a-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="79d8a-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="79d8a-463">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="79d8a-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="79d8a-464">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="79d8a-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="79d8a-465">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="79d8a-466">如需有關記錄的詳細資訊，請參閱[ :::no-loc(SignalR)::: 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-466">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="79d8a-467">:::no-loc(SignalR):::JAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="79d8a-467">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="79d8a-468">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="79d8a-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="79d8a-469">下列程式碼片段說明如何搭配使用 `java.util.logging` :::no-loc(SignalR)::: JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-469">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="79d8a-470">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="79d8a-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="79d8a-471">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="79d8a-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="79d8a-472">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="79d8a-472">Configure allowed transports</span></span>

<span data-ttu-id="79d8a-473">使用的傳輸 :::no-loc(SignalR)::: 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-473">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="79d8a-474">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="79d8a-475">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-475">All transports are enabled by default.</span></span>

<span data-ttu-id="79d8a-476">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="79d8a-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="79d8a-477">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="79d8a-478">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="79d8a-479">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="79d8a-480">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="79d8a-481">:::no-loc(SignalR):::JAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="79d8a-481">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="79d8a-482">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="79d8a-482">Configure bearer authentication</span></span>

<span data-ttu-id="79d8a-483">若要提供驗證資料和 :::no-loc(SignalR)::: 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="79d8a-483">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="79d8a-484">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="79d8a-485">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="79d8a-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="79d8a-486">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="79d8a-487">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="79d8a-488">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="79d8a-489">在 :::no-loc(SignalR)::: JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-489">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="79d8a-490">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="79d8a-491">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="79d8a-492">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="79d8a-493">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="79d8a-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-494">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-495">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-495">Option</span></span> | <span data-ttu-id="79d8a-496">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-496">Default value</span></span> | <span data-ttu-id="79d8a-497">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="79d8a-498">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-499">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-499">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-500">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-501">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-502">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="79d8a-503">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-503">15 seconds</span></span> | <span data-ttu-id="79d8a-504">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-505">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-506">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-507">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-507">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-508">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-508">15 seconds</span></span> | <span data-ttu-id="79d8a-509">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-510">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-511">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="79d8a-512">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="79d8a-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-514">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-514">Option</span></span> | <span data-ttu-id="79d8a-515">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-515">Default value</span></span> | <span data-ttu-id="79d8a-516">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="79d8a-517">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-518">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-518">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-519">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="79d8a-520">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-521">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="79d8a-522">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-523">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-524">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-525">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-526">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-526">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-527">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-527">Option</span></span> | <span data-ttu-id="79d8a-528">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-528">Default value</span></span> | <span data-ttu-id="79d8a-529">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="79d8a-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="79d8a-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="79d8a-531">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-532">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-532">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-533">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-534">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-535">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="79d8a-536">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-536">15 seconds</span></span> | <span data-ttu-id="79d8a-537">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-538">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-539">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-540">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-540">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="79d8a-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="79d8a-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="79d8a-542">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-543">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-544">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-545">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="79d8a-546">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-546">Configure additional options</span></span>

<span data-ttu-id="79d8a-547">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-548">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-549">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-549">.NET Option</span></span> |  <span data-ttu-id="79d8a-550">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-550">Default value</span></span> | <span data-ttu-id="79d8a-551">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="79d8a-552">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="79d8a-553">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-554">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-554">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-555">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-555">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="79d8a-556">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-556">Empty</span></span> | <span data-ttu-id="79d8a-557">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="79d8a-558">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-558">Empty</span></span> | <span data-ttu-id="79d8a-559">:::no-loc(cookie):::要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-559">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="79d8a-560">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-560">Empty</span></span> | <span data-ttu-id="79d8a-561">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="79d8a-562">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-562">5 seconds</span></span> | <span data-ttu-id="79d8a-563">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="79d8a-563">WebSockets only.</span></span> <span data-ttu-id="79d8a-564">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="79d8a-565">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="79d8a-566">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-566">Empty</span></span> | <span data-ttu-id="79d8a-567">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="79d8a-568">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="79d8a-569">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="79d8a-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="79d8a-570">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="79d8a-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="79d8a-571">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="79d8a-572">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 :::no-loc(Cookie)::: s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="79d8a-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="79d8a-573">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="79d8a-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="79d8a-574">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="79d8a-575">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="79d8a-576">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="79d8a-577">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="79d8a-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-579">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-579">JavaScript Option</span></span> | <span data-ttu-id="79d8a-580">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-580">Default Value</span></span> | <span data-ttu-id="79d8a-581">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="79d8a-582">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="79d8a-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="79d8a-584">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="79d8a-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="79d8a-585">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-586">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-586">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-587">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-587">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-588">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-588">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-589">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-589">Java Option</span></span> | <span data-ttu-id="79d8a-590">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-590">Default Value</span></span> | <span data-ttu-id="79d8a-591">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="79d8a-592">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="79d8a-593">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-594">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-594">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-595">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-595">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="79d8a-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="79d8a-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="79d8a-597">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-597">Empty</span></span> | <span data-ttu-id="79d8a-598">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="79d8a-599">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="79d8a-600">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="79d8a-601">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="79d8a-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="79d8a-602">其他資源</span><span class="sxs-lookup"><span data-stu-id="79d8a-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="79d8a-603">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-604">ASP.NET Core :::no-loc(SignalR)::: 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-604">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="79d8a-605">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="79d8a-606">您可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 擴充方法，在伺服器上設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="79d8a-607">`AddJsonProtocol`可以在[加入 :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之後加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-607">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="79d8a-608">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="79d8a-609">該物件的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 屬性是一個 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 物件，可以用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="79d8a-610">如需詳細資訊，請參閱 [ 檔上的System.Text.Js](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="79d8a-611">舉例來說，若要將序列化程式設定為不變更屬性名稱的大小寫，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="79d8a-612">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="79d8a-613">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="79d8a-614">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="79d8a-615">切換至 Newtonsoft.Js開啟</span><span class="sxs-lookup"><span data-stu-id="79d8a-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="79d8a-616">如果您需要 `Newtonsoft.Json` 中不支援的功能 `System.Text.Json` ，請參閱 [切換至 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="79d8a-617">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-617">MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-618">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="79d8a-619">如需詳細資訊，請參閱[中 :::no-loc(SignalR)::: 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-619">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-620">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="79d8a-621">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-621">Configure server options</span></span>

<span data-ttu-id="79d8a-622">下表說明設定中樞的選項 :::no-loc(SignalR)::: ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-622">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="79d8a-623">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-623">Option</span></span> | <span data-ttu-id="79d8a-624">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-624">Default Value</span></span> | <span data-ttu-id="79d8a-625">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="79d8a-626">30 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-626">30 seconds</span></span> | <span data-ttu-id="79d8a-627">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="79d8a-628">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="79d8a-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="79d8a-629">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="79d8a-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-630">15 seconds</span></span> | <span data-ttu-id="79d8a-631">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="79d8a-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="79d8a-632">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-633">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-633">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-634">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-634">15 seconds</span></span> | <span data-ttu-id="79d8a-635">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="79d8a-636">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="79d8a-637">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="79d8a-638">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="79d8a-638">All installed protocols</span></span> | <span data-ttu-id="79d8a-639">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-639">Protocols supported by this hub.</span></span> <span data-ttu-id="79d8a-640">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="79d8a-641">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="79d8a-642">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="79d8a-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="79d8a-643">可以針對用戶端上傳資料流程進行緩衝處理的最大專案數。</span><span class="sxs-lookup"><span data-stu-id="79d8a-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="79d8a-644">如果達到此限制，就會封鎖調用的處理，直到伺服器處理資料流程專案為止。</span><span class="sxs-lookup"><span data-stu-id="79d8a-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="79d8a-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-645">32 KB</span></span> | <span data-ttu-id="79d8a-646">單一傳入中樞訊息的大小上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="79d8a-647">您可以為所有中樞設定選項，方法是提供選項委派給 `Add:::no-loc(SignalR):::` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-647">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="79d8a-648">單一中樞的選項會覆寫中提供的全域選項 `Add:::no-loc(SignalR):::` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-648">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="79d8a-649">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="79d8a-650">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="79d8a-651">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="79d8a-652">下表說明設定 ASP.NET Core :::no-loc(SignalR)::: ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="79d8a-652">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="79d8a-653">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-653">Option</span></span> | <span data-ttu-id="79d8a-654">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-654">Default Value</span></span> | <span data-ttu-id="79d8a-655">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="79d8a-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-656">32 KB</span></span> | <span data-ttu-id="79d8a-657">在套用背壓之前，從用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="79d8a-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="79d8a-658">提高此值可讓伺服器在不套用背壓的情況下，更快接收更大的訊息，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="79d8a-659">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="79d8a-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="79d8a-660">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="79d8a-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="79d8a-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-661">32 KB</span></span> | <span data-ttu-id="79d8a-662">在觀察到背壓之前，伺服器所緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="79d8a-663">提高此值可讓伺服器更快速緩衝較大的訊息，而不需要等待背壓，但可能會增加記憶體耗用量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="79d8a-664">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="79d8a-664">All Transports are enabled.</span></span> | <span data-ttu-id="79d8a-665">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="79d8a-666">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-666">See below.</span></span> | <span data-ttu-id="79d8a-667">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="79d8a-668">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-668">See below.</span></span> | <span data-ttu-id="79d8a-669">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="79d8a-670">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="79d8a-671">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-671">Option</span></span> | <span data-ttu-id="79d8a-672">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-672">Default Value</span></span> | <span data-ttu-id="79d8a-673">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="79d8a-674">90秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-674">90 seconds</span></span> | <span data-ttu-id="79d8a-675">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="79d8a-676">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="79d8a-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="79d8a-677">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="79d8a-678">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-678">Option</span></span> | <span data-ttu-id="79d8a-679">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-679">Default Value</span></span> | <span data-ttu-id="79d8a-680">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="79d8a-681">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-681">5 seconds</span></span> | <span data-ttu-id="79d8a-682">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="79d8a-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="79d8a-683">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="79d8a-684">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="79d8a-685">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-685">Configure client options</span></span>

<span data-ttu-id="79d8a-686">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="79d8a-687">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="79d8a-688">設定記錄</span><span class="sxs-lookup"><span data-stu-id="79d8a-688">Configure logging</span></span>

<span data-ttu-id="79d8a-689">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="79d8a-690">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="79d8a-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="79d8a-691">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-692">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="79d8a-693">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="79d8a-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="79d8a-694">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="79d8a-695">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="79d8a-696">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="79d8a-697">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="79d8a-698">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="79d8a-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="79d8a-699">`LogLevel`您也可以提供 `string` 代表記錄層級名稱的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="79d8a-700">:::no-loc(SignalR):::在您沒有常數存取權的環境中設定記錄時，這會很有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-700">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="79d8a-701">下表列出可用的記錄層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-701">The following table lists the available log levels.</span></span> <span data-ttu-id="79d8a-702">您所提供的值， `configureLogging` 用來設定將記錄的 **最小** 記錄層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="79d8a-703">記錄在此層級的訊息， **或是在資料表中所列的層級** ，將會被記錄下來。</span><span class="sxs-lookup"><span data-stu-id="79d8a-703">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="79d8a-704">String</span><span class="sxs-lookup"><span data-stu-id="79d8a-704">String</span></span>                      | <span data-ttu-id="79d8a-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="79d8a-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="79d8a-706">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="79d8a-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="79d8a-707">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="79d8a-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="79d8a-708">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="79d8a-709">如需有關記錄的詳細資訊，請參閱[ :::no-loc(SignalR)::: 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-709">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="79d8a-710">:::no-loc(SignalR):::JAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="79d8a-710">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="79d8a-711">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="79d8a-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="79d8a-712">下列程式碼片段說明如何搭配使用 `java.util.logging` :::no-loc(SignalR)::: JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-712">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="79d8a-713">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="79d8a-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="79d8a-714">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="79d8a-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="79d8a-715">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="79d8a-715">Configure allowed transports</span></span>

<span data-ttu-id="79d8a-716">使用的傳輸 :::no-loc(SignalR)::: 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-716">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="79d8a-717">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="79d8a-718">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-718">All transports are enabled by default.</span></span>

<span data-ttu-id="79d8a-719">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="79d8a-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="79d8a-720">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="79d8a-721">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="79d8a-722">在 JAVA 用戶端中，會使用上的方法來選取傳輸 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="79d8a-723">JAVA 用戶端預設為使用 Websocket 傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="79d8a-724">:::no-loc(SignalR):::JAVA 用戶端尚不支援傳輸回復。</span><span class="sxs-lookup"><span data-stu-id="79d8a-724">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="79d8a-725">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="79d8a-725">Configure bearer authentication</span></span>

<span data-ttu-id="79d8a-726">若要提供驗證資料和 :::no-loc(SignalR)::: 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="79d8a-726">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="79d8a-727">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="79d8a-728">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="79d8a-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="79d8a-729">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="79d8a-730">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="79d8a-731">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="79d8a-732">在 :::no-loc(SignalR)::: JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-732">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="79d8a-733">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="79d8a-734">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="79d8a-735">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="79d8a-736">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="79d8a-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-737">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-738">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-738">Option</span></span> | <span data-ttu-id="79d8a-739">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-739">Default value</span></span> | <span data-ttu-id="79d8a-740">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="79d8a-741">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-742">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-742">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-743">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-744">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-745">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="79d8a-746">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-746">15 seconds</span></span> | <span data-ttu-id="79d8a-747">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-748">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-749">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-750">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-750">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-751">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-751">15 seconds</span></span> | <span data-ttu-id="79d8a-752">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-753">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-754">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="79d8a-755">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="79d8a-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-757">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-757">Option</span></span> | <span data-ttu-id="79d8a-758">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-758">Default value</span></span> | <span data-ttu-id="79d8a-759">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="79d8a-760">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-761">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-761">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-762">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="79d8a-763">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-764">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="79d8a-765">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-766">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-767">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-768">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-769">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-769">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-770">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-770">Option</span></span> | <span data-ttu-id="79d8a-771">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-771">Default value</span></span> | <span data-ttu-id="79d8a-772">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="79d8a-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="79d8a-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="79d8a-774">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-775">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-775">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-776">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-777">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-778">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="79d8a-779">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-779">15 seconds</span></span> | <span data-ttu-id="79d8a-780">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-781">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-782">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-783">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-783">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="79d8a-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="79d8a-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="79d8a-785">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-786">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-787">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-788">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="79d8a-789">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-789">Configure additional options</span></span>

<span data-ttu-id="79d8a-790">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-791">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-792">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-792">.NET Option</span></span> |  <span data-ttu-id="79d8a-793">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-793">Default value</span></span> | <span data-ttu-id="79d8a-794">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="79d8a-795">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="79d8a-796">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-797">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-797">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-798">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-798">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="79d8a-799">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-799">Empty</span></span> | <span data-ttu-id="79d8a-800">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="79d8a-801">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-801">Empty</span></span> | <span data-ttu-id="79d8a-802">:::no-loc(cookie):::要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-802">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="79d8a-803">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-803">Empty</span></span> | <span data-ttu-id="79d8a-804">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="79d8a-805">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-805">5 seconds</span></span> | <span data-ttu-id="79d8a-806">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="79d8a-806">WebSockets only.</span></span> <span data-ttu-id="79d8a-807">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="79d8a-808">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="79d8a-809">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-809">Empty</span></span> | <span data-ttu-id="79d8a-810">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="79d8a-811">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="79d8a-812">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="79d8a-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="79d8a-813">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="79d8a-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="79d8a-814">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="79d8a-815">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 :::no-loc(Cookie)::: s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="79d8a-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="79d8a-816">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="79d8a-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="79d8a-817">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="79d8a-818">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="79d8a-819">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="79d8a-820">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="79d8a-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-822">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-822">JavaScript Option</span></span> | <span data-ttu-id="79d8a-823">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-823">Default Value</span></span> | <span data-ttu-id="79d8a-824">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="79d8a-825">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="79d8a-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="79d8a-827">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="79d8a-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="79d8a-828">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-829">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-829">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-830">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-830">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-831">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-831">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-832">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-832">Java Option</span></span> | <span data-ttu-id="79d8a-833">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-833">Default Value</span></span> | <span data-ttu-id="79d8a-834">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="79d8a-835">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="79d8a-836">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-837">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-837">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-838">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-838">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="79d8a-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="79d8a-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="79d8a-840">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-840">Empty</span></span> | <span data-ttu-id="79d8a-841">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="79d8a-842">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="79d8a-843">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="79d8a-844">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="79d8a-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="79d8a-845">其他資源</span><span class="sxs-lookup"><span data-stu-id="79d8a-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="79d8a-846">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-847">ASP.NET Core :::no-loc(SignalR)::: 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-847">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="79d8a-848">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="79d8a-849">您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 :::no-loc(SignalR)::: 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="79d8a-850">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="79d8a-851">該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="79d8a-852">如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="79d8a-853">舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="79d8a-854">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="79d8a-855">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="79d8a-856">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="79d8a-857">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-857">MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-858">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="79d8a-859">如需詳細資訊，請參閱[中 :::no-loc(SignalR)::: 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-859">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-860">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="79d8a-861">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-861">Configure server options</span></span>

<span data-ttu-id="79d8a-862">下表說明設定中樞的選項 :::no-loc(SignalR)::: ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-862">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="79d8a-863">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-863">Option</span></span> | <span data-ttu-id="79d8a-864">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-864">Default Value</span></span> | <span data-ttu-id="79d8a-865">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="79d8a-866">30 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-866">30 seconds</span></span> | <span data-ttu-id="79d8a-867">如果用戶端未收到訊息 (（包括在此間隔內保持運作) ），伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="79d8a-868">這可能需要比此逾時間隔更長的時間，讓用戶端實際標示為已中斷連線，因為這是如何實行的。</span><span class="sxs-lookup"><span data-stu-id="79d8a-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="79d8a-869">建議的值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="79d8a-870">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-870">15 seconds</span></span> | <span data-ttu-id="79d8a-871">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="79d8a-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="79d8a-872">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-873">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-873">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-874">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-874">15 seconds</span></span> | <span data-ttu-id="79d8a-875">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="79d8a-876">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="79d8a-877">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="79d8a-878">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="79d8a-878">All installed protocols</span></span> | <span data-ttu-id="79d8a-879">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-879">Protocols supported by this hub.</span></span> <span data-ttu-id="79d8a-880">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="79d8a-881">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="79d8a-882">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="79d8a-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="79d8a-883">您可以為所有中樞設定選項，方法是提供選項委派給 `Add:::no-loc(SignalR):::` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-883">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="79d8a-884">單一中樞的選項會覆寫中提供的全域選項 `Add:::no-loc(SignalR):::` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-884">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="79d8a-885">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="79d8a-886">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="79d8a-887">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="79d8a-888">下表說明設定 ASP.NET Core :::no-loc(SignalR)::: ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="79d8a-888">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="79d8a-889">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-889">Option</span></span> | <span data-ttu-id="79d8a-890">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-890">Default Value</span></span> | <span data-ttu-id="79d8a-891">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="79d8a-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-892">32 KB</span></span> | <span data-ttu-id="79d8a-893">從伺服器緩衝的用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="79d8a-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="79d8a-894">提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="79d8a-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="79d8a-895">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="79d8a-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="79d8a-896">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="79d8a-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="79d8a-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-897">32 KB</span></span> | <span data-ttu-id="79d8a-898">由伺服器緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="79d8a-899">提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="79d8a-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="79d8a-900">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="79d8a-900">All Transports are enabled.</span></span> | <span data-ttu-id="79d8a-901">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="79d8a-902">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-902">See below.</span></span> | <span data-ttu-id="79d8a-903">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="79d8a-904">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-904">See below.</span></span> | <span data-ttu-id="79d8a-905">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="79d8a-906">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="79d8a-907">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-907">Option</span></span> | <span data-ttu-id="79d8a-908">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-908">Default Value</span></span> | <span data-ttu-id="79d8a-909">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="79d8a-910">90秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-910">90 seconds</span></span> | <span data-ttu-id="79d8a-911">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="79d8a-912">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="79d8a-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="79d8a-913">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="79d8a-914">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-914">Option</span></span> | <span data-ttu-id="79d8a-915">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-915">Default Value</span></span> | <span data-ttu-id="79d8a-916">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="79d8a-917">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-917">5 seconds</span></span> | <span data-ttu-id="79d8a-918">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="79d8a-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="79d8a-919">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="79d8a-920">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="79d8a-921">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-921">Configure client options</span></span>

<span data-ttu-id="79d8a-922">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="79d8a-923">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="79d8a-924">設定記錄</span><span class="sxs-lookup"><span data-stu-id="79d8a-924">Configure logging</span></span>

<span data-ttu-id="79d8a-925">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="79d8a-926">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="79d8a-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="79d8a-927">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-928">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="79d8a-929">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="79d8a-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="79d8a-930">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="79d8a-931">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="79d8a-932">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="79d8a-933">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="79d8a-934">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="79d8a-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="79d8a-935">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="79d8a-936">如需有關記錄的詳細資訊，請參閱[ :::no-loc(SignalR)::: 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-936">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="79d8a-937">:::no-loc(SignalR):::JAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="79d8a-937">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="79d8a-938">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="79d8a-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="79d8a-939">下列程式碼片段說明如何搭配使用 `java.util.logging` :::no-loc(SignalR)::: JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-939">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="79d8a-940">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="79d8a-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="79d8a-941">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="79d8a-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="79d8a-942">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="79d8a-942">Configure allowed transports</span></span>

<span data-ttu-id="79d8a-943">使用的傳輸 :::no-loc(SignalR)::: 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-943">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="79d8a-944">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="79d8a-945">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-945">All transports are enabled by default.</span></span>

<span data-ttu-id="79d8a-946">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="79d8a-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="79d8a-947">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="79d8a-948">這一版的 JAVA 用戶端 websocket 是唯一可用的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="79d8a-949">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="79d8a-949">Configure bearer authentication</span></span>

<span data-ttu-id="79d8a-950">若要提供驗證資料和 :::no-loc(SignalR)::: 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="79d8a-950">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="79d8a-951">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="79d8a-952">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="79d8a-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="79d8a-953">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="79d8a-954">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="79d8a-955">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="79d8a-956">在 :::no-loc(SignalR)::: JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-956">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="79d8a-957">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="79d8a-958">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="79d8a-959">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="79d8a-960">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="79d8a-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-961">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-962">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-962">Option</span></span> | <span data-ttu-id="79d8a-963">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-963">Default value</span></span> | <span data-ttu-id="79d8a-964">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="79d8a-965">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-966">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-966">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-967">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-968">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-969">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="79d8a-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-970">15 seconds</span></span> | <span data-ttu-id="79d8a-971">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-972">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-973">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-974">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-974">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-975">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-975">15 seconds</span></span> | <span data-ttu-id="79d8a-976">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-977">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-978">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="79d8a-979">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="79d8a-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-981">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-981">Option</span></span> | <span data-ttu-id="79d8a-982">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-982">Default value</span></span> | <span data-ttu-id="79d8a-983">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="79d8a-984">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-985">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-985">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-986">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="79d8a-987">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-988">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="79d8a-989">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-990">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-991">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-992">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-993">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-993">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-994">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-994">Option</span></span> | <span data-ttu-id="79d8a-995">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-995">Default value</span></span> | <span data-ttu-id="79d8a-996">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="79d8a-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="79d8a-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="79d8a-998">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-999">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-999">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-1000">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-1001">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-1002">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="79d8a-1003">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1003">15 seconds</span></span> | <span data-ttu-id="79d8a-1004">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-1005">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-1006">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-1007">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1007">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="79d8a-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="79d8a-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="79d8a-1009">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-1010">決定用戶端傳送 ping 訊息的間隔。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="79d8a-1011">從用戶端傳送任何訊息時，會將計時器重設為間隔的開始。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="79d8a-1012">如果用戶端未在伺服器上的集合中傳送訊息 `ClientTimeoutInterval` ，伺服器會將用戶端視為已中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="79d8a-1013">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1013">Configure additional options</span></span>

<span data-ttu-id="79d8a-1014">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-1016">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1016">.NET Option</span></span> |  <span data-ttu-id="79d8a-1017">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1017">Default value</span></span> | <span data-ttu-id="79d8a-1018">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="79d8a-1019">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="79d8a-1020">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-1021">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1021">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-1022">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1022">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="79d8a-1023">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1023">Empty</span></span> | <span data-ttu-id="79d8a-1024">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="79d8a-1025">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1025">Empty</span></span> | <span data-ttu-id="79d8a-1026">:::no-loc(cookie):::要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1026">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="79d8a-1027">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1027">Empty</span></span> | <span data-ttu-id="79d8a-1028">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="79d8a-1029">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1029">5 seconds</span></span> | <span data-ttu-id="79d8a-1030">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1030">WebSockets only.</span></span> <span data-ttu-id="79d8a-1031">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="79d8a-1032">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="79d8a-1033">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1033">Empty</span></span> | <span data-ttu-id="79d8a-1034">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="79d8a-1035">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="79d8a-1036">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="79d8a-1037">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="79d8a-1038">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="79d8a-1039">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 :::no-loc(Cookie)::: s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="79d8a-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="79d8a-1040">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="79d8a-1041">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="79d8a-1042">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="79d8a-1043">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="79d8a-1044">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="79d8a-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-1046">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1046">JavaScript Option</span></span> | <span data-ttu-id="79d8a-1047">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1047">Default Value</span></span> | <span data-ttu-id="79d8a-1048">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="79d8a-1049">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="79d8a-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="79d8a-1051">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="79d8a-1052">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-1053">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1053">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-1054">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1054">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-1055">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-1056">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1056">Java Option</span></span> | <span data-ttu-id="79d8a-1057">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1057">Default Value</span></span> | <span data-ttu-id="79d8a-1058">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="79d8a-1059">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="79d8a-1060">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-1061">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1061">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-1062">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1062">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="79d8a-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="79d8a-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="79d8a-1064">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1064">Empty</span></span> | <span data-ttu-id="79d8a-1065">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="79d8a-1066">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="79d8a-1067">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="79d8a-1068">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="79d8a-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="79d8a-1069">其他資源</span><span class="sxs-lookup"><span data-stu-id="79d8a-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="79d8a-1070">JSON/MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-1071">ASP.NET Core :::no-loc(SignalR)::: 支援兩種用來編碼訊息的通訊協定： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1071">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="79d8a-1072">每個通訊協定都有序列化設定選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="79d8a-1073">您可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)擴充方法在伺服器上設定 JSON 序列化，這可以在您的方法[中 :::no-loc(SignalR)::: 加入之後加入](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="79d8a-1074">`AddJsonProtocol`方法會使用接收物件的委派 `options` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="79d8a-1075">該物件的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 屬性是 JSON.NET `JsonSerializerSettings` 物件，可用來設定引數和傳回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="79d8a-1076">如需詳細資訊，請參閱 [JSON.NET 檔](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="79d8a-1077">舉例來說，若要將序列化程式設定為使用 "PascalCase" 屬性名稱，而不是預設的 "camelCase" 名稱，請在中使用下列程式碼 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="79d8a-1078">在 .NET 用戶端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="79d8a-1079">`Microsoft.Extensions.DependencyInjection`必須匯入命名空間來解析擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="79d8a-1080">目前無法在 JavaScript 用戶端中設定 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="79d8a-1081">MessagePack 序列化選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1081">MessagePack serialization options</span></span>

<span data-ttu-id="79d8a-1082">您可以藉由提供委派給 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼叫來設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="79d8a-1083">如需詳細資訊，請參閱[中 :::no-loc(SignalR)::: 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1083">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-1084">目前無法在 JavaScript 用戶端中設定 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="79d8a-1085">設定伺服器選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1085">Configure server options</span></span>

<span data-ttu-id="79d8a-1086">下表說明設定中樞的選項 :::no-loc(SignalR)::: ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1086">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="79d8a-1087">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1087">Option</span></span> | <span data-ttu-id="79d8a-1088">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1088">Default Value</span></span> | <span data-ttu-id="79d8a-1089">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="79d8a-1090">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1090">15 seconds</span></span> | <span data-ttu-id="79d8a-1091">如果用戶端未在此時間間隔內傳送初始交握訊息，則連接會關閉。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="79d8a-1092">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-1093">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1093">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="79d8a-1094">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1094">15 seconds</span></span> | <span data-ttu-id="79d8a-1095">如果伺服器未在此間隔內傳送訊息，則會自動傳送 ping 訊息以保持連接開啟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="79d8a-1096">變更時 `KeepAliveInterval` ，請變更 `ServerTimeout` / `serverTimeoutInMilliseconds` 用戶端上的設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="79d8a-1097">建議的 `ServerTimeout` / `serverTimeoutInMilliseconds` 值是值的兩倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="79d8a-1098">所有已安裝的通訊協定</span><span class="sxs-lookup"><span data-stu-id="79d8a-1098">All installed protocols</span></span> | <span data-ttu-id="79d8a-1099">此中樞所支援的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="79d8a-1100">預設會允許在伺服器上註冊的所有通訊協定，但是可以從此清單中移除通訊協定，以停用個別中樞的特定通訊協定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="79d8a-1101">如果為 `true` ，則在中樞方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="79d8a-1102">預設值為 `false` ，因為這些例外狀況訊息可能包含機密資訊。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="79d8a-1103">您可以為所有中樞設定選項，方法是提供選項委派給 `Add:::no-loc(SignalR):::` 中的呼叫 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1103">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="79d8a-1104">單一中樞的選項會覆寫中提供的全域選項 `Add:::no-loc(SignalR):::` ，可以使用下列方式進行設定 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1104">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="79d8a-1105">Advanced HTTP 設定選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="79d8a-1106">用 `HttpConnectionDispatcherOptions` 來設定與傳輸和記憶體緩衝區管理相關的 advanced 設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="79d8a-1107">這些選項的設定方式是將委派傳遞至中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="79d8a-1108">下表說明設定 ASP.NET Core :::no-loc(SignalR)::: ADVANCED HTTP 選項的選項：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1108">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="79d8a-1109">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1109">Option</span></span> | <span data-ttu-id="79d8a-1110">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1110">Default Value</span></span> | <span data-ttu-id="79d8a-1111">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="79d8a-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-1112">32 KB</span></span> | <span data-ttu-id="79d8a-1113">從伺服器緩衝的用戶端接收的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="79d8a-1114">提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="79d8a-1115">自動從套用 `Authorize` 至中樞類別的屬性收集的資料。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="79d8a-1116">用來判斷是否授權用戶端連線至中樞之 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 物件的清單。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="79d8a-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="79d8a-1117">32 KB</span></span> | <span data-ttu-id="79d8a-1118">由伺服器緩衝的應用程式所傳送的位元組數目上限。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="79d8a-1119">提高此值可讓伺服器傳送較大的訊息，但可能會對記憶體耗用量造成負面影響。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="79d8a-1120">所有傳輸都會啟用。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1120">All Transports are enabled.</span></span> | <span data-ttu-id="79d8a-1121">值的位旗標列舉 `HttpTransportType` ，可限制用戶端可以用來連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="79d8a-1122">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1122">See below.</span></span> | <span data-ttu-id="79d8a-1123">長輪詢傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="79d8a-1124">請參閱下文。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1124">See below.</span></span> | <span data-ttu-id="79d8a-1125">Websocket 傳輸特定的其他選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="79d8a-1126">長時間輪詢傳輸具有可使用屬性設定的其他選項 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="79d8a-1127">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1127">Option</span></span> | <span data-ttu-id="79d8a-1128">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1128">Default Value</span></span> | <span data-ttu-id="79d8a-1129">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="79d8a-1130">90秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1130">90 seconds</span></span> | <span data-ttu-id="79d8a-1131">在終止單一輪詢要求之前，伺服器等候訊息傳送至用戶端的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="79d8a-1132">減少此值會導致用戶端更頻繁地發出新的投票要求。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="79d8a-1133">WebSocket 傳輸具有可使用屬性設定的其他選項 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="79d8a-1134">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1134">Option</span></span> | <span data-ttu-id="79d8a-1135">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1135">Default Value</span></span> | <span data-ttu-id="79d8a-1136">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="79d8a-1137">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1137">5 seconds</span></span> | <span data-ttu-id="79d8a-1138">伺服器關閉之後，如果用戶端在此時間間隔內無法關閉，則連接會終止。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="79d8a-1139">可以用來將 `Sec-WebSocket-Protocol` 標頭設定為自訂值的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="79d8a-1140">委派會接收用戶端要求的值做為輸入，而且預期會傳回所需的值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="79d8a-1141">設定用戶端選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1141">Configure client options</span></span>

<span data-ttu-id="79d8a-1142">您可以在 `HubConnectionBuilder` .net 和 JavaScript 用戶端) 中提供的類型 (設定用戶端選項。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="79d8a-1143">它也可以在 JAVA 用戶端中使用，但子類別也是包含產生器設定選項以及本身的子 `HttpHubConnectionBuilder` 類別 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="79d8a-1144">設定記錄</span><span class="sxs-lookup"><span data-stu-id="79d8a-1144">Configure logging</span></span>

<span data-ttu-id="79d8a-1145">您可以使用方法，在 .NET 用戶端中設定記錄 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="79d8a-1146">記錄提供者和篩選器的註冊方式與在伺服器上的方式相同。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="79d8a-1147">如需詳細資訊，請參閱 [ASP.NET Core 檔中的記錄](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="79d8a-1148">為了註冊記錄提供者，您必須安裝必要的套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="79d8a-1149">如需完整清單，請參閱檔的 [內建記錄提供者](xref:fundamentals/logging/index#built-in-logging-providers) 一節。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="79d8a-1150">例如，若要啟用主控台記錄，請安裝 `Microsoft.Extensions.Logging.Console` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="79d8a-1151">呼叫 `AddConsole` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="79d8a-1152">在 JavaScript 用戶端中，有類似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="79d8a-1153">提供一個 `LogLevel` 值，指出要產生的記錄檔訊息的最小層級。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="79d8a-1154">記錄檔會寫入至瀏覽器主控台視窗。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="79d8a-1155">若要完全停用記錄，請 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="79d8a-1156">如需有關記錄的詳細資訊，請參閱[ :::no-loc(SignalR)::: 診斷檔](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1156">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="79d8a-1157">:::no-loc(SignalR):::JAVA 用戶端會使用[SLF4J](https://www.slf4j.org/)程式庫來進行記錄。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1157">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="79d8a-1158">它是一種高階記錄 API，可讓程式庫的使用者藉由帶入特定的記錄相依性，選擇自己的特定記錄執行。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="79d8a-1159">下列程式碼片段說明如何搭配使用 `java.util.logging` :::no-loc(SignalR)::: JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1159">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="79d8a-1160">如果您未設定相依性中的記錄，SLF4J 會載入預設的無作業記錄器，並顯示下列警告訊息：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="79d8a-1161">這可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="79d8a-1162">設定允許的傳輸</span><span class="sxs-lookup"><span data-stu-id="79d8a-1162">Configure allowed transports</span></span>

<span data-ttu-id="79d8a-1163">使用的傳輸 :::no-loc(SignalR)::: 可在 `WithUrl` JavaScript) 的呼叫 (中設定 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1163">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="79d8a-1164">值的位 OR `HttpTransportType` 可以用來限制用戶端只能使用指定的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="79d8a-1165">預設會啟用所有傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="79d8a-1166">例如，若要停用 Server-Sent 事件傳輸，但允許 Websocket 和長時間輪詢連接：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="79d8a-1167">在 JavaScript 用戶端中，您可以設定提供的 `transport` 選項物件上的欄位來設定傳輸 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="79d8a-1168">設定持有人驗證</span><span class="sxs-lookup"><span data-stu-id="79d8a-1168">Configure bearer authentication</span></span>

<span data-ttu-id="79d8a-1169">若要提供驗證資料和 :::no-loc(SignalR)::: 要求，請使用 `AccessTokenProvider` JavaScript) 的選項 (， `accessTokenFactory` 以指定傳回所需存取權杖的函式。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1169">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="79d8a-1170">在 .NET 用戶端中，此存取權杖會以 HTTP 「持有人驗證」權杖的形式傳入， (使用 `Authorization`) 類型的標頭 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="79d8a-1171">在 JavaScript 用戶端中，存取權杖會用來作為持有人權杖， **但** 在某些情況下，瀏覽器 api 會限制在) 中 Server-Sent 事件和 websocket 要求中套用標頭 (的能力。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="79d8a-1172">在這些情況下，存取權杖會以查詢字串值的形式提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="79d8a-1173">在 .NET 用戶端中，您 `AccessTokenProvider` 可以使用中的 options 委派來指定選項 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="79d8a-1174">在 JavaScript 用戶端中，您可以設定 `accessTokenFactory` 中選項物件上的欄位來設定存取權杖 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="79d8a-1175">在 :::no-loc(SignalR)::: JAVA 用戶端中，您可以提供存取權杖 factory 給 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)，以設定用於驗證的持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1175">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="79d8a-1176">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)來提供[RxJAVA](https://github.com/ReactiveX/RxJava) [單一 \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="79d8a-1177">只要呼叫 [Single. 延遲](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，您就可以撰寫邏輯來產生用戶端的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="79d8a-1178">設定 timeout 和 keep-alive 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="79d8a-1179">設定 timeout 和 keep-alive 行為的其他選項可在 `HubConnection` 物件本身取得：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-1181">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1181">Option</span></span> | <span data-ttu-id="79d8a-1182">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1182">Default value</span></span> | <span data-ttu-id="79d8a-1183">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="79d8a-1184">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-1185">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1185">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-1186">如果伺服器未在此間隔中傳送訊息，用戶端會將伺服器視為已中斷連線，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-1187">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-1188">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="79d8a-1189">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1189">15 seconds</span></span> | <span data-ttu-id="79d8a-1190">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-1191">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並 `Closed` `onclose` 在 JavaScript) 中觸發事件 (。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="79d8a-1192">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-1193">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1193">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="79d8a-1194">在 .NET 用戶端中，超時值會指定為 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="79d8a-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-1196">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1196">Option</span></span> | <span data-ttu-id="79d8a-1197">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1197">Default value</span></span> | <span data-ttu-id="79d8a-1198">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="79d8a-1199">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-1200">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1200">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-1201">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="79d8a-1202">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-1203">建議的值是至少兩倍伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-1204">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-1205">選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1205">Option</span></span> | <span data-ttu-id="79d8a-1206">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1206">Default value</span></span> | <span data-ttu-id="79d8a-1207">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="79d8a-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="79d8a-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="79d8a-1209">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="79d8a-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="79d8a-1210">伺服器活動的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1210">Timeout for server activity.</span></span> <span data-ttu-id="79d8a-1211">如果伺服器未在此間隔內傳送訊息，用戶端會將伺服器視為已中斷連線，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-1212">這個值必須夠大，才能從伺服器傳送 ping 訊息 **，並** 在逾時間隔內接收用戶端。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="79d8a-1213">建議的值是至少兩個伺服器值的數位 `KeepAliveInterval` ，以允許時間讓 ping 到達。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="79d8a-1214">15 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1214">15 seconds</span></span> | <span data-ttu-id="79d8a-1215">初始伺服器交握的超時時間。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="79d8a-1216">如果伺服器未在此間隔中傳送交握回應，用戶端會取消交握，並觸發 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="79d8a-1217">這是一項 advanced 設定，只有在因嚴重的網路延遲而發生信號交換逾時錯誤時，才應該修改這個設定。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="79d8a-1218">如需信號交換程式的詳細資訊，請參閱[ :::no-loc(SignalR)::: 中樞通訊協定規格](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1218">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="79d8a-1219">設定其他選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1219">Configure additional options</span></span>

<span data-ttu-id="79d8a-1220">您可以在 `WithUrl` `withUrl` JavaScript) 方法的 (中， `HubConnectionBuilder` 或在 JAVA 用戶端的不同設定 api 上設定其他選項 `HttpHubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="79d8a-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="79d8a-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="79d8a-1222">.NET 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1222">.NET Option</span></span> |  <span data-ttu-id="79d8a-1223">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1223">Default value</span></span> | <span data-ttu-id="79d8a-1224">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="79d8a-1225">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="79d8a-1226">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-1227">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1227">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-1228">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1228">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="79d8a-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1229">Empty</span></span> | <span data-ttu-id="79d8a-1230">要傳送以驗證要求的 TLS 憑證集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="79d8a-1231">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1231">Empty</span></span> | <span data-ttu-id="79d8a-1232">:::no-loc(cookie):::要與每個 HTTP 要求一起傳送的 HTTP 集合。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1232">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="79d8a-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1233">Empty</span></span> | <span data-ttu-id="79d8a-1234">每個 HTTP 要求所要傳送的認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="79d8a-1235">5 秒</span><span class="sxs-lookup"><span data-stu-id="79d8a-1235">5 seconds</span></span> | <span data-ttu-id="79d8a-1236">僅限 Websocket。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1236">WebSockets only.</span></span> <span data-ttu-id="79d8a-1237">用戶端在關閉以確認關閉要求後等待的最大時間量。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="79d8a-1238">如果伺服器未在這段時間內確認關閉，用戶端就會中斷連線。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="79d8a-1239">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1239">Empty</span></span> | <span data-ttu-id="79d8a-1240">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="79d8a-1241">可以用來設定或取代 `HttpMessageHandler` 用來傳送 HTTP 要求的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="79d8a-1242">不用於 WebSocket 連接。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="79d8a-1243">此委派必須傳回非 null 值，且會接收預設值做為參數。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="79d8a-1244">請修改該預設值的設定並傳回，或傳回新的 `HttpMessageHandler` 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="79d8a-1245">**取代處理常式時，請務必從提供的處理常式複製您想要保留的設定，否則設定的選項 (例如 :::no-loc(Cookie)::: s 和標頭) 不會套用至新的處理常式。**</span><span class="sxs-lookup"><span data-stu-id="79d8a-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="79d8a-1246">傳送 HTTP 要求時要使用的 HTTP proxy。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="79d8a-1247">設定此布林值，以傳送 HTTP 和 Websocket 要求的預設認證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="79d8a-1248">這樣就可以使用 Windows 驗證。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="79d8a-1249">可以用來設定其他 WebSocket 選項的委派。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="79d8a-1250">接收可以用來設定選項的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 實例。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="79d8a-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="79d8a-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="79d8a-1252">JavaScript 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1252">JavaScript Option</span></span> | <span data-ttu-id="79d8a-1253">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1253">Default Value</span></span> | <span data-ttu-id="79d8a-1254">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="79d8a-1255">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="79d8a-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>值，指定要用於連接的傳輸。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="79d8a-1257">設定為， `true` 以記錄用戶端所傳送和接收之訊息的位元組/字元。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="79d8a-1258">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-1259">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1259">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-1260">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1260">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="79d8a-1261">Java</span><span class="sxs-lookup"><span data-stu-id="79d8a-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="79d8a-1262">JAVA 選項</span><span class="sxs-lookup"><span data-stu-id="79d8a-1262">Java Option</span></span> | <span data-ttu-id="79d8a-1263">預設值</span><span class="sxs-lookup"><span data-stu-id="79d8a-1263">Default Value</span></span> | <span data-ttu-id="79d8a-1264">描述</span><span class="sxs-lookup"><span data-stu-id="79d8a-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="79d8a-1265">傳回字串的函式，在 HTTP 要求中以持有人驗證權杖的形式提供。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="79d8a-1266">將此設定為， `true` 以略過協商步驟。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="79d8a-1267">**只有當 websocket 傳輸是唯一啟用的傳輸時才支援** 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1267">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="79d8a-1268">使用 Azure 服務時，無法啟用此設定 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1268">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="79d8a-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="79d8a-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="79d8a-1270">Empty</span><span class="sxs-lookup"><span data-stu-id="79d8a-1270">Empty</span></span> | <span data-ttu-id="79d8a-1271">要與每個 HTTP 要求一起傳送的其他 HTTP 標頭對應。</span><span class="sxs-lookup"><span data-stu-id="79d8a-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="79d8a-1272">在 .NET 用戶端中，這些選項可以由提供給的 options 委派來修改 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="79d8a-1273">在 JavaScript 用戶端中，您可以在提供下列功能的 JavaScript 物件中提供這些選項 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="79d8a-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="79d8a-1274">在 JAVA 用戶端中，您可以在傳回的上使用方法來設定這些選項。 `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="79d8a-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="79d8a-1275">其他資源</span><span class="sxs-lookup"><span data-stu-id="79d8a-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
