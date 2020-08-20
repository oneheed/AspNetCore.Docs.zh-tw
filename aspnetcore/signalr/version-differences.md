---
title: SignalR和 ASP.NET Core 之間的差異SignalR
author: bradygaster
description: SignalR和 ASP.NET Core 之間的差異SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: a8336a6c13c502f5a0fad150785cd9d484064618
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633002"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="f77b3-103">ASP.NET SignalR 與 ASP.NET Core 之間的差異 SignalR</span><span class="sxs-lookup"><span data-stu-id="f77b3-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="f77b3-104">ASP.NET Core SignalR 與用戶端或伺服器的 ASP.NET 不相容 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="f77b3-105">本文詳細說明 ASP.NET Core 中已移除或變更的功能 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="f77b3-106">如何識別 SignalR 版本</span><span class="sxs-lookup"><span data-stu-id="f77b3-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="f77b3-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="f77b3-107">ASP.NET SignalR</span></span> | <span data-ttu-id="f77b3-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="f77b3-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="f77b3-109">**伺服器 NuGet 套件**</span><span class="sxs-lookup"><span data-stu-id="f77b3-109">**Server NuGet package**</span></span> | <span data-ttu-id="f77b3-110">[Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="f77b3-111">無。</span><span class="sxs-lookup"><span data-stu-id="f77b3-111">None.</span></span> <span data-ttu-id="f77b3-112">包含在 [AspNetCore 應用程式](xref:fundamentals/metapackage-app) 共用架構中。</span><span class="sxs-lookup"><span data-stu-id="f77b3-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="f77b3-113">**用戶端 NuGet 套件**</span><span class="sxs-lookup"><span data-stu-id="f77b3-113">**Client NuGet packages**</span></span> | <span data-ttu-id="f77b3-114">[Microsoft SignalR AspNet.。客戶](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="f77b3-115">[Microsoft SignalR AspNet.。Js](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="f77b3-116">[AspNetCore SignalR 。客戶](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="f77b3-117">**JavaScript 用戶端 npm 套件**</span><span class="sxs-lookup"><span data-stu-id="f77b3-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="f77b3-118">signalr</span><span class="sxs-lookup"><span data-stu-id="f77b3-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="f77b3-119">**Java 用戶端**</span><span class="sxs-lookup"><span data-stu-id="f77b3-119">**Java client**</span></span> | <span data-ttu-id="f77b3-120">[GitHub 儲存](https://github.com/SignalR/java-client) 機制 (已淘汰) </span><span class="sxs-lookup"><span data-stu-id="f77b3-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="f77b3-121">Maven package [.com. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="f77b3-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="f77b3-122">**伺服器應用程式類型**</span><span class="sxs-lookup"><span data-stu-id="f77b3-122">**Server app type**</span></span> | <span data-ttu-id="f77b3-123">ASP.NET (System.object) 或 OWIN 自我裝載</span><span class="sxs-lookup"><span data-stu-id="f77b3-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="f77b3-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f77b3-124">ASP.NET Core</span></span> |
| <span data-ttu-id="f77b3-125">**支援的伺服器平臺**</span><span class="sxs-lookup"><span data-stu-id="f77b3-125">**Supported server platforms**</span></span> | <span data-ttu-id="f77b3-126">.NET Framework 4.5 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f77b3-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="f77b3-127">.NET Core 3.0 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f77b3-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="f77b3-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="f77b3-128">ASP.NET SignalR</span></span> | <span data-ttu-id="f77b3-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="f77b3-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="f77b3-130">**伺服器 NuGet 套件**</span><span class="sxs-lookup"><span data-stu-id="f77b3-130">**Server NuGet package**</span></span> | <span data-ttu-id="f77b3-131">[Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="f77b3-132"> ( .NET Core) [的 AspNetCore 應用程式](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="f77b3-133">[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="f77b3-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="f77b3-134">(.NET Framework)</span></span> |
| <span data-ttu-id="f77b3-135">**用戶端 NuGet 套件**</span><span class="sxs-lookup"><span data-stu-id="f77b3-135">**Client NuGet packages**</span></span> | <span data-ttu-id="f77b3-136">[Microsoft SignalR AspNet.。客戶](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="f77b3-137">[Microsoft SignalR AspNet.。Js](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="f77b3-138">[AspNetCore SignalR 。客戶](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="f77b3-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="f77b3-139">**JavaScript 用戶端 npm 套件**</span><span class="sxs-lookup"><span data-stu-id="f77b3-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="f77b3-140">signalr</span><span class="sxs-lookup"><span data-stu-id="f77b3-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="f77b3-141">**Java 用戶端**</span><span class="sxs-lookup"><span data-stu-id="f77b3-141">**Java client**</span></span> | <span data-ttu-id="f77b3-142">[GitHub 儲存](https://github.com/SignalR/java-client) 機制 (已淘汰) </span><span class="sxs-lookup"><span data-stu-id="f77b3-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="f77b3-143">Maven package [.com. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="f77b3-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="f77b3-144">**伺服器應用程式類型**</span><span class="sxs-lookup"><span data-stu-id="f77b3-144">**Server app type**</span></span> | <span data-ttu-id="f77b3-145">ASP.NET (System.object) 或 OWIN 自我裝載</span><span class="sxs-lookup"><span data-stu-id="f77b3-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="f77b3-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f77b3-146">ASP.NET Core</span></span> |
| <span data-ttu-id="f77b3-147">**支援的伺服器平臺**</span><span class="sxs-lookup"><span data-stu-id="f77b3-147">**Supported server platforms**</span></span> | <span data-ttu-id="f77b3-148">.NET Framework 4.5 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f77b3-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="f77b3-149">.NET Framework 4.6.1 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f77b3-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="f77b3-150">.NET Core 2.1 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f77b3-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="f77b3-151">功能差異</span><span class="sxs-lookup"><span data-stu-id="f77b3-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="f77b3-152">自動重新連接</span><span class="sxs-lookup"><span data-stu-id="f77b3-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f77b3-153">在 ASP.NET 中 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="f77b3-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="f77b3-154">依預設， SignalR 如果中斷連接，則會嘗試重新連線到伺服器。</span><span class="sxs-lookup"><span data-stu-id="f77b3-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="f77b3-155">在 ASP.NET Core SignalR ：</span><span class="sxs-lookup"><span data-stu-id="f77b3-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="f77b3-156">自動重新連接可加入 [.net 用戶端](xref:signalr/dotnet-client#automatically-reconnect) 和 [JavaScript 用戶端](xref:signalr/javascript-client#automatically-reconnect)：</span><span class="sxs-lookup"><span data-stu-id="f77b3-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f77b3-157">在 ASP.NET Core 3.0 之前， SignalR 不支援自動重新連接。</span><span class="sxs-lookup"><span data-stu-id="f77b3-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="f77b3-158">如果用戶端已中斷連線，則使用者必須明確地啟動新的連線，才能重新連接。</span><span class="sxs-lookup"><span data-stu-id="f77b3-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="f77b3-159">在 ASP.NET 中 SignalR ， SignalR 如果中斷連接，則會嘗試重新連線到伺服器。</span><span class="sxs-lookup"><span data-stu-id="f77b3-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="f77b3-160">通訊協定支援</span><span class="sxs-lookup"><span data-stu-id="f77b3-160">Protocol support</span></span>

<span data-ttu-id="f77b3-161">ASP.NET Core SignalR 支援 JSON，以及以 [MessagePack](xref:signalr/messagepackhubprotocol)為基礎的新二進位通訊協定。</span><span class="sxs-lookup"><span data-stu-id="f77b3-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="f77b3-162">此外，也可以建立自訂通訊協定。</span><span class="sxs-lookup"><span data-stu-id="f77b3-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="f77b3-163">傳輸</span><span class="sxs-lookup"><span data-stu-id="f77b3-163">Transports</span></span>

<span data-ttu-id="f77b3-164">ASP.NET Core 不支援永久框架傳輸 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="f77b3-165">伺服器上的差異</span><span class="sxs-lookup"><span data-stu-id="f77b3-165">Differences on the server</span></span>

<span data-ttu-id="f77b3-166">ASP.NET Core 的 SignalR 伺服器端程式庫包含在 [AspNetCore](xref:fundamentals/metapackage-app)中，用於和 MVC 專案的 **ASP.NET Core Web 應用程式** 範本 Razor 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="f77b3-167">ASP.NET Core SignalR 是 ASP.NET Core 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="f77b3-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="f77b3-168">您必須在中呼叫來設定它 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f77b3-169">若要設定路由，請 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 在方法呼叫中的方法呼叫內將路由對應至中樞 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f77b3-170">若要設定路由，請 <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> 在方法呼叫中的方法呼叫內將路由對應至中樞 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="f77b3-171">粘滯話</span><span class="sxs-lookup"><span data-stu-id="f77b3-171">Sticky sessions</span></span>

<span data-ttu-id="f77b3-172">適用于 ASP.NET 的向外延展模型 SignalR 可讓用戶端重新連線，並將訊息傳送到伺服器陣列中的任何伺服器。</span><span class="sxs-lookup"><span data-stu-id="f77b3-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="f77b3-173">在 ASP.NET Core 中 SignalR ，用戶端必須在連接期間與相同的伺服器互動。</span><span class="sxs-lookup"><span data-stu-id="f77b3-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="f77b3-174">針對使用 Redis 的向外延展，這表示需要有粘滯話。</span><span class="sxs-lookup"><span data-stu-id="f77b3-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="f77b3-175">針對使用 [Azure SignalR 服務](/azure/azure-signalr/)的向外延展，由於服務會處理與用戶端的連線，因此不需要使用「粘滯話」。</span><span class="sxs-lookup"><span data-stu-id="f77b3-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="f77b3-176">每個連接的單一中樞</span><span class="sxs-lookup"><span data-stu-id="f77b3-176">Single hub per connection</span></span>

<span data-ttu-id="f77b3-177">在 ASP.NET Core 中 SignalR ，連接模型已經過簡化。</span><span class="sxs-lookup"><span data-stu-id="f77b3-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="f77b3-178">連接會直接建立至單一中樞，而不是用來共用多個中樞存取權的單一連接。</span><span class="sxs-lookup"><span data-stu-id="f77b3-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="f77b3-179">資料流</span><span class="sxs-lookup"><span data-stu-id="f77b3-179">Streaming</span></span>

<span data-ttu-id="f77b3-180">ASP.NET Core SignalR 現在支援從中樞將 [資料串流](xref:signalr/streaming) 至用戶端。</span><span class="sxs-lookup"><span data-stu-id="f77b3-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="f77b3-181">狀態</span><span class="sxs-lookup"><span data-stu-id="f77b3-181">State</span></span>

<span data-ttu-id="f77b3-182">在用戶端與中樞 (之間傳遞任意狀態的能力通常稱為 `HubState`) 已移除，以及支援進度訊息。</span><span class="sxs-lookup"><span data-stu-id="f77b3-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="f77b3-183">目前沒有任何對應的中樞 proxy。</span><span class="sxs-lookup"><span data-stu-id="f77b3-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="f77b3-184">PersistentConnection 移除</span><span class="sxs-lookup"><span data-stu-id="f77b3-184">PersistentConnection removal</span></span>

<span data-ttu-id="f77b3-185">在 ASP.NET Core 中 SignalR ，已移除 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) 類別。</span><span class="sxs-lookup"><span data-stu-id="f77b3-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="f77b3-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="f77b3-186">GlobalHost</span></span>

<span data-ttu-id="f77b3-187">ASP.NET Core 在架構內建 (DI) 的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="f77b3-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="f77b3-188">服務可以使用 DI 來存取 [HubCoNtext](xref:signalr/hubcontext)。</span><span class="sxs-lookup"><span data-stu-id="f77b3-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="f77b3-189">`GlobalHost`在 ASP.NET 中用 SignalR 來取得的物件 `HubContext` 不存在於 ASP.NET Core 中 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="f77b3-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="f77b3-190">HubPipeline</span></span>

<span data-ttu-id="f77b3-191">ASP.NET Core SignalR 不支援 `HubPipeline` 模組。</span><span class="sxs-lookup"><span data-stu-id="f77b3-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="f77b3-192">用戶端上的差異</span><span class="sxs-lookup"><span data-stu-id="f77b3-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="f77b3-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="f77b3-193">TypeScript</span></span>

<span data-ttu-id="f77b3-194">ASP.NET Core SignalR 用戶端是以 [TypeScript](https://www.typescriptlang.org/)撰寫的。</span><span class="sxs-lookup"><span data-stu-id="f77b3-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="f77b3-195">使用 [javascript 用戶端](xref:signalr/javascript-client)時，您可以使用 JAVAscript 或 TypeScript 來撰寫。</span><span class="sxs-lookup"><span data-stu-id="f77b3-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="f77b3-196">JavaScript 用戶端託管于 npm</span><span class="sxs-lookup"><span data-stu-id="f77b3-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f77b3-197">在 ASP.NET 版本中，JavaScript 用戶端是透過 Visual Studio 中的 NuGet 套件取得的。</span><span class="sxs-lookup"><span data-stu-id="f77b3-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="f77b3-198">在 ASP.NET Core 版本中， [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 套件包含 JavaScript 程式庫。</span><span class="sxs-lookup"><span data-stu-id="f77b3-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="f77b3-199">此套件不包含在 **ASP.NET Core Web 應用程式** 範本中。</span><span class="sxs-lookup"><span data-stu-id="f77b3-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="f77b3-200">使用 npm 來取得和安裝 `@microsoft/signalr` npm 套件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f77b3-201">在 ASP.NET 版本中，JavaScript 用戶端是透過 Visual Studio 中的 NuGet 套件取得的。</span><span class="sxs-lookup"><span data-stu-id="f77b3-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="f77b3-202">在 ASP.NET Core 版本中， [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 套件包含 JavaScript 程式庫。</span><span class="sxs-lookup"><span data-stu-id="f77b3-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="f77b3-203">此套件不包含在 **ASP.NET Core Web 應用程式** 範本中。</span><span class="sxs-lookup"><span data-stu-id="f77b3-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="f77b3-204">使用 npm 來取得和安裝 `@aspnet/signalr` npm 套件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="f77b3-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="f77b3-205">jQuery</span></span>

<span data-ttu-id="f77b3-206">已移除 jQuery 的相依性，不過專案仍可使用 jQuery。</span><span class="sxs-lookup"><span data-stu-id="f77b3-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="f77b3-207">Internet Explorer 支援</span><span class="sxs-lookup"><span data-stu-id="f77b3-207">Internet Explorer support</span></span>

<span data-ttu-id="f77b3-208">ASP.NET Core SignalR 需要 microsoft Internet Explorer 11 或更新版本 (ASP.NET SignalR 支援 microsoft Internet Explorer 8 和更新版本) 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="f77b3-209">JavaScript 用戶端方法語法</span><span class="sxs-lookup"><span data-stu-id="f77b3-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f77b3-210">JavaScript 語法已從的 ASP.NET 版本變更 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="f77b3-211">`$connection`使用[HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 來建立連線，而不是使用物件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="f77b3-212">使用 [on](/javascript/api/@microsoft/signalr/HubConnection#on) 方法來指定中樞可以呼叫的用戶端方法。</span><span class="sxs-lookup"><span data-stu-id="f77b3-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f77b3-213">JavaScript 語法已從的 ASP.NET 版本變更 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="f77b3-214">`$connection`使用[HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 來建立連線，而不是使用物件。</span><span class="sxs-lookup"><span data-stu-id="f77b3-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="f77b3-215">使用 [on](/javascript/api/@aspnet/signalr/HubConnection#on) 方法來指定中樞可以呼叫的用戶端方法。</span><span class="sxs-lookup"><span data-stu-id="f77b3-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="f77b3-216">建立用戶端方法之後，啟動中樞連接。</span><span class="sxs-lookup"><span data-stu-id="f77b3-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="f77b3-217">連結 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 方法以記錄或處理錯誤。</span><span class="sxs-lookup"><span data-stu-id="f77b3-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="f77b3-218">中樞 proxy</span><span class="sxs-lookup"><span data-stu-id="f77b3-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f77b3-219">不再自動產生中樞 proxy。</span><span class="sxs-lookup"><span data-stu-id="f77b3-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="f77b3-220">相反地，方法名稱會以字串的形式傳遞至 [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API。</span><span class="sxs-lookup"><span data-stu-id="f77b3-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f77b3-221">不再自動產生中樞 proxy。</span><span class="sxs-lookup"><span data-stu-id="f77b3-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="f77b3-222">相反地，方法名稱會以字串的形式傳遞至 [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API。</span><span class="sxs-lookup"><span data-stu-id="f77b3-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="f77b3-223">.NET 和其他用戶端</span><span class="sxs-lookup"><span data-stu-id="f77b3-223">.NET and other clients</span></span>

<span data-ttu-id="f77b3-224">[AspNetCore。 SignalR用戶端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet 套件包含 ASP.NET Core 的 .net 用戶端程式庫 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f77b3-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="f77b3-225">使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 來建立和建立與中樞的連接實例。</span><span class="sxs-lookup"><span data-stu-id="f77b3-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="f77b3-226">向外延展差異</span><span class="sxs-lookup"><span data-stu-id="f77b3-226">Scaleout differences</span></span>

<span data-ttu-id="f77b3-227">ASP.NET SignalR 支援 SQL Server 和 Redis。</span><span class="sxs-lookup"><span data-stu-id="f77b3-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="f77b3-228">ASP.NET Core SignalR 支援 Azure SignalR 服務和 Redis。</span><span class="sxs-lookup"><span data-stu-id="f77b3-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="f77b3-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="f77b3-229">ASP.NET</span></span>

* [<span data-ttu-id="f77b3-230">SignalR Azure 服務匯流排的向外延展</span><span class="sxs-lookup"><span data-stu-id="f77b3-230">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="f77b3-231">SignalR 使用 Redis 的向外延展</span><span class="sxs-lookup"><span data-stu-id="f77b3-231">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="f77b3-232">SignalR SQL Server 的向外延展</span><span class="sxs-lookup"><span data-stu-id="f77b3-232">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="f77b3-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f77b3-233">ASP.NET Core</span></span>

* [<span data-ttu-id="f77b3-234">Azure SignalR 服務</span><span class="sxs-lookup"><span data-stu-id="f77b3-234">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="f77b3-235">Redis 背板</span><span class="sxs-lookup"><span data-stu-id="f77b3-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="f77b3-236">其他資源</span><span class="sxs-lookup"><span data-stu-id="f77b3-236">Additional resources</span></span>

* [<span data-ttu-id="f77b3-237">集線器</span><span class="sxs-lookup"><span data-stu-id="f77b3-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f77b3-238">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="f77b3-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="f77b3-239">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="f77b3-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="f77b3-240">支援的平台</span><span class="sxs-lookup"><span data-stu-id="f77b3-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
