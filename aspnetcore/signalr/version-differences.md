---
title: SignalR和 ASP.NET Core 之間的差異SignalR
author: bradygaster
description: SignalR和 ASP.NET Core 之間的差異SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: c37f0a3c528b6285fbf35f41eed781fb76c68de4
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854596"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="f08a5-103">ASP.NET SignalR 與 ASP.NET Core 之間的差異 SignalR</span><span class="sxs-lookup"><span data-stu-id="f08a5-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="f08a5-104">ASP.NET Core SignalR 與用戶端或伺服器的 ASP.NET 不相容 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="f08a5-105">本文詳細說明 ASP.NET Core 中已移除或變更的功能 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="f08a5-106">如何識別 SignalR 版本</span><span class="sxs-lookup"><span data-stu-id="f08a5-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="f08a5-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="f08a5-107">ASP.NET SignalR</span></span> | <span data-ttu-id="f08a5-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="f08a5-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="f08a5-109">**伺服器 NuGet 套件**</span><span class="sxs-lookup"><span data-stu-id="f08a5-109">**Server NuGet package**</span></span> | <span data-ttu-id="f08a5-110">[Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="f08a5-111">無。</span><span class="sxs-lookup"><span data-stu-id="f08a5-111">None.</span></span> <span data-ttu-id="f08a5-112">包含在 [AspNetCore 應用程式](xref:fundamentals/metapackage-app) 共用架構中。</span><span class="sxs-lookup"><span data-stu-id="f08a5-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="f08a5-113">**用戶端 NuGet 套件**</span><span class="sxs-lookup"><span data-stu-id="f08a5-113">**Client NuGet packages**</span></span> | <span data-ttu-id="f08a5-114">[Microsoft SignalR AspNet.。客戶](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="f08a5-115">[Microsoft SignalR AspNet.。Js](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="f08a5-116">[AspNetCore SignalR 。客戶](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="f08a5-117">**JavaScript 用戶端 npm 套件**</span><span class="sxs-lookup"><span data-stu-id="f08a5-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="f08a5-118">signalr</span><span class="sxs-lookup"><span data-stu-id="f08a5-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="f08a5-119">**Java 用戶端**</span><span class="sxs-lookup"><span data-stu-id="f08a5-119">**Java client**</span></span> | <span data-ttu-id="f08a5-120">[GitHub 儲存](https://github.com/SignalR/java-client) 機制 (已淘汰) </span><span class="sxs-lookup"><span data-stu-id="f08a5-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="f08a5-121">Maven package [.com. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="f08a5-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="f08a5-122">**伺服器應用程式類型**</span><span class="sxs-lookup"><span data-stu-id="f08a5-122">**Server app type**</span></span> | <span data-ttu-id="f08a5-123">ASP.NET (System. Web) 或 OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="f08a5-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="f08a5-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f08a5-124">ASP.NET Core</span></span> |
| <span data-ttu-id="f08a5-125">**支援的伺服器平臺**</span><span class="sxs-lookup"><span data-stu-id="f08a5-125">**Supported server platforms**</span></span> | <span data-ttu-id="f08a5-126">.NET Framework 4.5 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f08a5-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="f08a5-127">.NET Core 3.0 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f08a5-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="f08a5-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="f08a5-128">ASP.NET SignalR</span></span> | <span data-ttu-id="f08a5-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="f08a5-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="f08a5-130">**伺服器 NuGet 套件**</span><span class="sxs-lookup"><span data-stu-id="f08a5-130">**Server NuGet package**</span></span> | <span data-ttu-id="f08a5-131">[Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="f08a5-132"> ( .NET Core) [的 AspNetCore 應用程式](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="f08a5-133">[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="f08a5-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="f08a5-134">(.NET Framework)</span></span> |
| <span data-ttu-id="f08a5-135">**用戶端 NuGet 套件**</span><span class="sxs-lookup"><span data-stu-id="f08a5-135">**Client NuGet packages**</span></span> | <span data-ttu-id="f08a5-136">[Microsoft SignalR AspNet.。客戶](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="f08a5-137">[Microsoft SignalR AspNet.。Js](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="f08a5-138">[AspNetCore SignalR 。客戶](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="f08a5-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="f08a5-139">**JavaScript 用戶端 npm 套件**</span><span class="sxs-lookup"><span data-stu-id="f08a5-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="f08a5-140">signalr</span><span class="sxs-lookup"><span data-stu-id="f08a5-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="f08a5-141">**Java 用戶端**</span><span class="sxs-lookup"><span data-stu-id="f08a5-141">**Java client**</span></span> | <span data-ttu-id="f08a5-142">[GitHub 儲存](https://github.com/SignalR/java-client) 機制 (已淘汰) </span><span class="sxs-lookup"><span data-stu-id="f08a5-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="f08a5-143">Maven package [.com. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="f08a5-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="f08a5-144">**伺服器應用程式類型**</span><span class="sxs-lookup"><span data-stu-id="f08a5-144">**Server app type**</span></span> | <span data-ttu-id="f08a5-145">ASP.NET (System. Web) 或 OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="f08a5-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="f08a5-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f08a5-146">ASP.NET Core</span></span> |
| <span data-ttu-id="f08a5-147">**支援的伺服器平臺**</span><span class="sxs-lookup"><span data-stu-id="f08a5-147">**Supported server platforms**</span></span> | <span data-ttu-id="f08a5-148">.NET Framework 4.5 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f08a5-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="f08a5-149">.NET Framework 4.6.1 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f08a5-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="f08a5-150">.NET Core 2.1 或更新版本</span><span class="sxs-lookup"><span data-stu-id="f08a5-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="f08a5-151">功能差異</span><span class="sxs-lookup"><span data-stu-id="f08a5-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="f08a5-152">自動重新連接</span><span class="sxs-lookup"><span data-stu-id="f08a5-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f08a5-153">在 ASP.NET 中 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="f08a5-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="f08a5-154">依預設， SignalR 如果中斷連接，則會嘗試重新連線到伺服器。</span><span class="sxs-lookup"><span data-stu-id="f08a5-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="f08a5-155">在 ASP.NET Core SignalR ：</span><span class="sxs-lookup"><span data-stu-id="f08a5-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="f08a5-156">自動重新連接可加入 [.net 用戶端](xref:signalr/dotnet-client#automatically-reconnect) 和 [JavaScript 用戶端](xref:signalr/javascript-client#automatically-reconnect)：</span><span class="sxs-lookup"><span data-stu-id="f08a5-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

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

<span data-ttu-id="f08a5-157">在 ASP.NET Core 3.0 之前， SignalR 不支援自動重新連接。</span><span class="sxs-lookup"><span data-stu-id="f08a5-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="f08a5-158">如果用戶端已中斷連線，則使用者必須明確地啟動新的連線，才能重新連接。</span><span class="sxs-lookup"><span data-stu-id="f08a5-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="f08a5-159">在 ASP.NET 中 SignalR ， SignalR 如果中斷連接，則會嘗試重新連線到伺服器。</span><span class="sxs-lookup"><span data-stu-id="f08a5-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="f08a5-160">通訊協定支援</span><span class="sxs-lookup"><span data-stu-id="f08a5-160">Protocol support</span></span>

<span data-ttu-id="f08a5-161">ASP.NET Core SignalR 支援 JSON，以及以 [MessagePack](xref:signalr/messagepackhubprotocol)為基礎的新二進位通訊協定。</span><span class="sxs-lookup"><span data-stu-id="f08a5-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="f08a5-162">此外，也可以建立自訂通訊協定。</span><span class="sxs-lookup"><span data-stu-id="f08a5-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="f08a5-163">傳輸</span><span class="sxs-lookup"><span data-stu-id="f08a5-163">Transports</span></span>

<span data-ttu-id="f08a5-164">ASP.NET Core 不支援永久框架傳輸 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="f08a5-165">伺服器上的差異</span><span class="sxs-lookup"><span data-stu-id="f08a5-165">Differences on the server</span></span>

<span data-ttu-id="f08a5-166">ASP.NET Core 的 SignalR 伺服器端程式庫包含在 [AspNetCore](xref:fundamentals/metapackage-app)中，用於和 MVC 專案的 **ASP.NET Core Web 應用程式** 範本 Razor 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="f08a5-167">ASP.NET Core SignalR 是 ASP.NET Core 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="f08a5-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="f08a5-168">您必須在中呼叫來設定它 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f08a5-169">若要設定路由，請 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 在方法呼叫中的方法呼叫內將路由對應至中樞 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f08a5-170">若要設定路由，請 <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> 在方法呼叫中的方法呼叫內將路由對應至中樞 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="f08a5-171">粘滯話</span><span class="sxs-lookup"><span data-stu-id="f08a5-171">Sticky sessions</span></span>

<span data-ttu-id="f08a5-172">適用于 ASP.NET 的向外延展模型 SignalR 可讓用戶端重新連線，並將訊息傳送到伺服器陣列中的任何伺服器。</span><span class="sxs-lookup"><span data-stu-id="f08a5-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="f08a5-173">在 ASP.NET Core 中 SignalR ，用戶端必須在連接期間與相同的伺服器互動。</span><span class="sxs-lookup"><span data-stu-id="f08a5-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="f08a5-174">針對使用 Redis 的向外延展，這表示需要有粘滯話。</span><span class="sxs-lookup"><span data-stu-id="f08a5-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="f08a5-175">針對使用 [Azure SignalR 服務](/azure/azure-signalr/)的向外延展，由於服務會處理與用戶端的連線，因此不需要使用「粘滯話」。</span><span class="sxs-lookup"><span data-stu-id="f08a5-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="f08a5-176">每個連接的單一中樞</span><span class="sxs-lookup"><span data-stu-id="f08a5-176">Single hub per connection</span></span>

<span data-ttu-id="f08a5-177">在 ASP.NET Core 中 SignalR ，連接模型已經過簡化。</span><span class="sxs-lookup"><span data-stu-id="f08a5-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="f08a5-178">連接會直接建立至單一中樞，而不是用來共用多個中樞存取權的單一連接。</span><span class="sxs-lookup"><span data-stu-id="f08a5-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="f08a5-179">串流</span><span class="sxs-lookup"><span data-stu-id="f08a5-179">Streaming</span></span>

<span data-ttu-id="f08a5-180">ASP.NET Core SignalR 現在支援從中樞將 [資料串流](xref:signalr/streaming) 至用戶端。</span><span class="sxs-lookup"><span data-stu-id="f08a5-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="f08a5-181">州</span><span class="sxs-lookup"><span data-stu-id="f08a5-181">State</span></span>

<span data-ttu-id="f08a5-182">在用戶端與中樞 (之間傳遞任意狀態的能力通常稱為 `HubState`) 已移除，以及支援進度訊息。</span><span class="sxs-lookup"><span data-stu-id="f08a5-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="f08a5-183">目前沒有任何對應的中樞 proxy。</span><span class="sxs-lookup"><span data-stu-id="f08a5-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="f08a5-184">PersistentConnection 移除</span><span class="sxs-lookup"><span data-stu-id="f08a5-184">PersistentConnection removal</span></span>

<span data-ttu-id="f08a5-185">在 ASP.NET Core 中 SignalR ，已移除 [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) 類別。</span><span class="sxs-lookup"><span data-stu-id="f08a5-185">In ASP.NET Core SignalR, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="f08a5-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="f08a5-186">GlobalHost</span></span>

<span data-ttu-id="f08a5-187">ASP.NET Core 在架構內建 (DI) 的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="f08a5-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="f08a5-188">服務可以使用 DI 來存取 [HubCoNtext](xref:signalr/hubcontext)。</span><span class="sxs-lookup"><span data-stu-id="f08a5-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="f08a5-189">`GlobalHost`在 ASP.NET 中用 SignalR 來取得的物件 `HubContext` 不存在於 ASP.NET Core 中 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="f08a5-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="f08a5-190">HubPipeline</span></span>

<span data-ttu-id="f08a5-191">ASP.NET Core SignalR 不支援 `HubPipeline` 模組。</span><span class="sxs-lookup"><span data-stu-id="f08a5-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="f08a5-192">用戶端上的差異</span><span class="sxs-lookup"><span data-stu-id="f08a5-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="f08a5-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="f08a5-193">TypeScript</span></span>

<span data-ttu-id="f08a5-194">ASP.NET Core SignalR 用戶端是以 [TypeScript](https://www.typescriptlang.org/)撰寫的。</span><span class="sxs-lookup"><span data-stu-id="f08a5-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="f08a5-195">使用 [javascript 用戶端](xref:signalr/javascript-client)時，您可以使用 JAVAscript 或 TypeScript 來撰寫。</span><span class="sxs-lookup"><span data-stu-id="f08a5-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="f08a5-196">JavaScript 用戶端託管于 npm</span><span class="sxs-lookup"><span data-stu-id="f08a5-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f08a5-197">在 ASP.NET 版本中，JavaScript 用戶端是透過 Visual Studio 中的 NuGet 套件取得的。</span><span class="sxs-lookup"><span data-stu-id="f08a5-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="f08a5-198">在 ASP.NET Core 版本中， [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 套件包含 JavaScript 程式庫。</span><span class="sxs-lookup"><span data-stu-id="f08a5-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="f08a5-199">此套件不包含在 **ASP.NET Core Web 應用程式** 範本中。</span><span class="sxs-lookup"><span data-stu-id="f08a5-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="f08a5-200">使用 npm 來取得和安裝 `@microsoft/signalr` npm 套件。</span><span class="sxs-lookup"><span data-stu-id="f08a5-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f08a5-201">在 ASP.NET 版本中，JavaScript 用戶端是透過 Visual Studio 中的 NuGet 套件取得的。</span><span class="sxs-lookup"><span data-stu-id="f08a5-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="f08a5-202">在 ASP.NET Core 版本中， [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 套件包含 JavaScript 程式庫。</span><span class="sxs-lookup"><span data-stu-id="f08a5-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="f08a5-203">此套件不包含在 **ASP.NET Core Web 應用程式** 範本中。</span><span class="sxs-lookup"><span data-stu-id="f08a5-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="f08a5-204">使用 npm 來取得和安裝 `@aspnet/signalr` npm 套件。</span><span class="sxs-lookup"><span data-stu-id="f08a5-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="f08a5-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="f08a5-205">jQuery</span></span>

<span data-ttu-id="f08a5-206">已移除 jQuery 的相依性，不過專案仍可使用 jQuery。</span><span class="sxs-lookup"><span data-stu-id="f08a5-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="f08a5-207">Internet Explorer 支援</span><span class="sxs-lookup"><span data-stu-id="f08a5-207">Internet Explorer support</span></span>

<span data-ttu-id="f08a5-208">ASP.NET Core SignalR 不支援 microsoft Internet Explorer，而 ASP.NET SignalR 支援 microsoft Internet Explorer 8 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="f08a5-208">ASP.NET Core SignalR doesn't support Microsoft Internet Explorer, whereas ASP.NET SignalR supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="f08a5-209">如需詳細資訊，請參閱 <xref:signalr/supported-platforms#javascript-client> 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-209">For more information, see <xref:signalr/supported-platforms#javascript-client>.</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="f08a5-210">JavaScript 用戶端方法語法</span><span class="sxs-lookup"><span data-stu-id="f08a5-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f08a5-211">JavaScript 語法已從的 ASP.NET 版本變更 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-211">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="f08a5-212">`$connection`使用[HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 來建立連線，而不是使用物件。</span><span class="sxs-lookup"><span data-stu-id="f08a5-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="f08a5-213">使用 [on](/javascript/api/@microsoft/signalr/HubConnection#on) 方法來指定中樞可以呼叫的用戶端方法。</span><span class="sxs-lookup"><span data-stu-id="f08a5-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f08a5-214">JavaScript 語法已從的 ASP.NET 版本變更 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-214">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="f08a5-215">`$connection`使用[HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 來建立連線，而不是使用物件。</span><span class="sxs-lookup"><span data-stu-id="f08a5-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="f08a5-216">使用 [on](/javascript/api/@aspnet/signalr/HubConnection#on) 方法來指定中樞可以呼叫的用戶端方法。</span><span class="sxs-lookup"><span data-stu-id="f08a5-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="f08a5-217">建立用戶端方法之後，啟動中樞連接。</span><span class="sxs-lookup"><span data-stu-id="f08a5-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="f08a5-218">連結 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 方法以記錄或處理錯誤。</span><span class="sxs-lookup"><span data-stu-id="f08a5-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="f08a5-219">中樞 proxy</span><span class="sxs-lookup"><span data-stu-id="f08a5-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f08a5-220">不再自動產生中樞 proxy。</span><span class="sxs-lookup"><span data-stu-id="f08a5-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="f08a5-221">相反地，方法名稱會以字串的形式傳遞至 [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API。</span><span class="sxs-lookup"><span data-stu-id="f08a5-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f08a5-222">不再自動產生中樞 proxy。</span><span class="sxs-lookup"><span data-stu-id="f08a5-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="f08a5-223">相反地，方法名稱會以字串的形式傳遞至 [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API。</span><span class="sxs-lookup"><span data-stu-id="f08a5-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="f08a5-224">.NET 和其他用戶端</span><span class="sxs-lookup"><span data-stu-id="f08a5-224">.NET and other clients</span></span>

<span data-ttu-id="f08a5-225">[AspNetCore。 SignalR用戶端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet 套件包含 ASP.NET Core 的 .net 用戶端程式庫 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f08a5-225">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="f08a5-226">使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 來建立和建立與中樞的連接實例。</span><span class="sxs-lookup"><span data-stu-id="f08a5-226">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="f08a5-227">向外延展差異</span><span class="sxs-lookup"><span data-stu-id="f08a5-227">Scaleout differences</span></span>

<span data-ttu-id="f08a5-228">ASP.NET SignalR 支援 SQL Server 和 Redis。</span><span class="sxs-lookup"><span data-stu-id="f08a5-228">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="f08a5-229">ASP.NET Core SignalR 支援 Azure SignalR 服務和 Redis。</span><span class="sxs-lookup"><span data-stu-id="f08a5-229">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="f08a5-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="f08a5-230">ASP.NET</span></span>

* [<span data-ttu-id="f08a5-231">SignalR Azure 服務匯流排的向外延展</span><span class="sxs-lookup"><span data-stu-id="f08a5-231">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="f08a5-232">SignalR 使用 Redis 的向外延展</span><span class="sxs-lookup"><span data-stu-id="f08a5-232">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="f08a5-233">SignalR SQL Server 的向外延展</span><span class="sxs-lookup"><span data-stu-id="f08a5-233">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="f08a5-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f08a5-234">ASP.NET Core</span></span>

* [<span data-ttu-id="f08a5-235">Azure SignalR 服務</span><span class="sxs-lookup"><span data-stu-id="f08a5-235">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="f08a5-236">Redis 背板</span><span class="sxs-lookup"><span data-stu-id="f08a5-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="f08a5-237">其他資源</span><span class="sxs-lookup"><span data-stu-id="f08a5-237">Additional resources</span></span>

* [<span data-ttu-id="f08a5-238">集線器</span><span class="sxs-lookup"><span data-stu-id="f08a5-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f08a5-239">JavaScript 用戶端</span><span class="sxs-lookup"><span data-stu-id="f08a5-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="f08a5-240">.NET 用戶端</span><span class="sxs-lookup"><span data-stu-id="f08a5-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="f08a5-241">支援的平台</span><span class="sxs-lookup"><span data-stu-id="f08a5-241">Supported platforms</span></span>](xref:signalr/supported-platforms)
