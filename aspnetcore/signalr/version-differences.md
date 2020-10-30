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
ms.openlocfilehash: c4c0ff83cb789e9aa35085496daa461404615726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061206"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a>ASP.NET SignalR 與 ASP.NET Core 之間的差異 SignalR

ASP.NET Core SignalR 與用戶端或伺服器的 ASP.NET 不相容 SignalR 。 本文詳細說明 ASP.NET Core 中已移除或變更的功能 SignalR 。

## <a name="how-to-identify-the-no-locsignalr-version"></a>如何識別 SignalR 版本

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **伺服器 NuGet 套件** | [Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | 無。 包含在 [AspNetCore 應用程式](xref:fundamentals/metapackage-app) 共用架構中。 |
| **用戶端 NuGet 套件** | [Microsoft SignalR AspNet.。客戶](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft SignalR AspNet.。Js](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore SignalR 。客戶](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **JavaScript 用戶端 npm 套件** | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| **Java 用戶端** | [GitHub 儲存](https://github.com/SignalR/java-client) 機制 (已淘汰)   | Maven package [.com. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **伺服器應用程式類型** | ASP.NET (System. Web) 或 OWIN Self-Host | ASP.NET Core |
| **支援的伺服器平臺** | .NET Framework 4.5 或更新版本 | .NET Core 3.0 或更新版本 |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **伺服器 NuGet 套件** | [Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) |  ( .NET Core) [的 AspNetCore 應用程式](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)<br>[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| **用戶端 NuGet 套件** | [Microsoft SignalR AspNet.。客戶](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft SignalR AspNet.。Js](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore SignalR 。客戶](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **JavaScript 用戶端 npm 套件** | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| **Java 用戶端** | [GitHub 儲存](https://github.com/SignalR/java-client) 機制 (已淘汰)   | Maven package [.com. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **伺服器應用程式類型** | ASP.NET (System. Web) 或 OWIN Self-Host | ASP.NET Core |
| **支援的伺服器平臺** | .NET Framework 4.5 或更新版本 | .NET Framework 4.6.1 或更新版本<br>.NET Core 2.1 或更新版本 |

::: moniker-end

## <a name="feature-differences"></a>功能差異

### <a name="automatic-reconnects"></a>自動重新連接

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET 中 SignalR ：

* 依預設， SignalR 如果中斷連接，則會嘗試重新連線到伺服器。 

在 ASP.NET Core SignalR ：

* 自動重新連接可加入 [.net 用戶端](xref:signalr/dotnet-client#automatically-reconnect) 和 [JavaScript 用戶端](xref:signalr/javascript-client#automatically-reconnect)：

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

在 ASP.NET Core 3.0 之前， SignalR 不支援自動重新連接。 如果用戶端已中斷連線，則使用者必須明確地啟動新的連線，才能重新連接。 在 ASP.NET 中 SignalR ， SignalR 如果中斷連接，則會嘗試重新連線到伺服器。

::: moniker-end

### <a name="protocol-support"></a>通訊協定支援

ASP.NET Core SignalR 支援 JSON，以及以 [MessagePack](xref:signalr/messagepackhubprotocol)為基礎的新二進位通訊協定。 此外，也可以建立自訂通訊協定。

### <a name="transports"></a>傳輸

ASP.NET Core 不支援永久框架傳輸 SignalR 。

## <a name="differences-on-the-server"></a>伺服器上的差異

ASP.NET Core 的 SignalR 伺服器端程式庫包含在 [AspNetCore](xref:fundamentals/metapackage-app)中，用於和 MVC 專案的 **ASP.NET Core Web 應用程式** 範本 Razor 。

ASP.NET Core SignalR 是 ASP.NET Core 中介軟體。 您必須在中呼叫來設定它 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices` 。

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

若要設定路由，請 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 在方法呼叫中的方法呼叫內將路由對應至中樞 `Startup.Configure` 。

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

若要設定路由，請 <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> 在方法呼叫中的方法呼叫內將路由對應至中樞 `Startup.Configure` 。

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>粘滯話

適用于 ASP.NET 的向外延展模型 SignalR 可讓用戶端重新連線，並將訊息傳送到伺服器陣列中的任何伺服器。 在 ASP.NET Core 中 SignalR ，用戶端必須在連接期間與相同的伺服器互動。 針對使用 Redis 的向外延展，這表示需要有粘滯話。 針對使用 [Azure SignalR 服務](/azure/azure-signalr/)的向外延展，由於服務會處理與用戶端的連線，因此不需要使用「粘滯話」。

### <a name="single-hub-per-connection"></a>每個連接的單一中樞

在 ASP.NET Core 中 SignalR ，連接模型已經過簡化。 連接會直接建立至單一中樞，而不是用來共用多個中樞存取權的單一連接。

### <a name="streaming"></a>串流

ASP.NET Core SignalR 現在支援從中樞將 [資料串流](xref:signalr/streaming) 至用戶端。

### <a name="state"></a>狀態

在用戶端與中樞 (之間傳遞任意狀態的能力通常稱為 `HubState`) 已移除，以及支援進度訊息。 目前沒有任何對應的中樞 proxy。

### <a name="persistentconnection-removal"></a>PersistentConnection 移除

在 ASP.NET Core 中 SignalR ，已移除 [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) 類別。

### <a name="globalhost"></a>GlobalHost

ASP.NET Core 在架構內建 (DI) 的相依性插入。 服務可以使用 DI 來存取 [HubCoNtext](xref:signalr/hubcontext)。 `GlobalHost`在 ASP.NET 中用 SignalR 來取得的物件 `HubContext` 不存在於 ASP.NET Core 中 SignalR 。

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR 不支援 `HubPipeline` 模組。

## <a name="differences-on-the-client"></a>用戶端上的差異

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalR 用戶端是以 [TypeScript](https://www.typescriptlang.org/)撰寫的。 使用 [javascript 用戶端](xref:signalr/javascript-client)時，您可以使用 JAVAscript 或 TypeScript 來撰寫。

### <a name="the-javascript-client-is-hosted-at-npm"></a>JavaScript 用戶端託管于 npm

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET 版本中，JavaScript 用戶端是透過 Visual Studio 中的 NuGet 套件取得的。 在 ASP.NET Core 版本中， [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 套件包含 JavaScript 程式庫。 此套件不包含在 **ASP.NET Core Web 應用程式** 範本中。 使用 npm 來取得和安裝 `@microsoft/signalr` npm 套件。

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

在 ASP.NET 版本中，JavaScript 用戶端是透過 Visual Studio 中的 NuGet 套件取得的。 在 ASP.NET Core 版本中， [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 套件包含 JavaScript 程式庫。 此套件不包含在 **ASP.NET Core Web 應用程式** 範本中。 使用 npm 來取得和安裝 `@aspnet/signalr` npm 套件。

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

已移除 jQuery 的相依性，不過專案仍可使用 jQuery。

### <a name="internet-explorer-support"></a>Internet Explorer 支援

ASP.NET Core SignalR 支援 microsoft Internet Explorer 11 或更新版本，而 ASP.NET SignalR 支援 microsoft Internet Explorer 8 或更新版本。
如需瀏覽器支援的詳細資訊，請參閱 [支援的平臺](xref:signalr/supported-platforms#javascript-client)。

### <a name="javascript-client-method-syntax"></a>JavaScript 用戶端方法語法

::: moniker range=">= aspnetcore-3.0"

JavaScript 語法已從的 ASP.NET 版本變更 SignalR 。 `$connection`使用[HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 來建立連線，而不是使用物件。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

使用 [on](/javascript/api/@microsoft/signalr/HubConnection#on) 方法來指定中樞可以呼叫的用戶端方法。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

JavaScript 語法已從的 ASP.NET 版本變更 SignalR 。 `$connection`使用[HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 來建立連線，而不是使用物件。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

使用 [on](/javascript/api/@aspnet/signalr/HubConnection#on) 方法來指定中樞可以呼叫的用戶端方法。

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

建立用戶端方法之後，啟動中樞連接。 連結 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 方法以記錄或處理錯誤。

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>中樞 proxy

::: moniker range=">= aspnetcore-3.0"

不再自動產生中樞 proxy。 相反地，方法名稱會以字串的形式傳遞至 [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

不再自動產生中樞 proxy。 相反地，方法名稱會以字串的形式傳遞至 [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API。

::: moniker-end

### <a name="net-and-other-clients"></a>.NET 和其他用戶端

[AspNetCore。 SignalR用戶端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet 套件包含 ASP.NET Core 的 .net 用戶端程式庫 SignalR 。

使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 來建立和建立與中樞的連接實例。

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>向外延展差異

ASP.NET SignalR 支援 SQL Server 和 Redis。 ASP.NET Core SignalR 支援 Azure SignalR 服務和 Redis。

### <a name="aspnet"></a>ASP.NET

* [SignalR Azure 服務匯流排的向外延展](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR 使用 Redis 的向外延展](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR SQL Server 的向外延展](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR 服務](/azure/azure-signalr/)
* [Redis 背板](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>其他資源

* [中樞](xref:signalr/hubs)
* [JavaScript 用戶端](xref:signalr/javascript-client)
* [.NET 用戶端](xref:signalr/dotnet-client)
* [支援的平台](xref:signalr/supported-platforms)
