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
# <a name="host-aspnet-core-signalr-in-background-services"></a>SignalR在背景服務中裝載 ASP.NET Core

依 [Brady Gaster](https://twitter.com/bradygaster)

本文提供下列指引：

* SignalR使用裝載于 ASP.NET Core 的背景工作進程來裝載中樞。
* 從 .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)中，將訊息傳送至已連線的用戶端。

::: moniker range=">= aspnetcore-3.0"

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/background-service/samples/3.x) [ (如何下載) ](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/background-service/samples/2.2) [ (如何下載) ](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-signalr-in-startup"></a>SignalR在啟動時啟用

::: moniker range=">= aspnetcore-3.0"

SignalR在背景工作進程的環境中裝載 ASP.NET 核心中樞，與在 ASP.NET Core web 應用程式中裝載中樞相同。 在 `Startup.ConfigureServices` 方法中，呼叫會 `services.AddSignalR` 將必要的服務加入至 ASP.NET 核心相依性插入 (DI) 層以支援 SignalR 。 在中 `Startup.Configure` ， `MapHub` 會在回呼中呼叫方法 `UseEndpoints` ，以連接 ASP.NET 核心要求管線中的中樞端點。

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

SignalR在背景工作進程的環境中裝載 ASP.NET 核心中樞，與在 ASP.NET Core web 應用程式中裝載中樞相同。 在 `Startup.ConfigureServices` 方法中，呼叫會 `services.AddSignalR` 將必要的服務加入至 ASP.NET 核心相依性插入 (DI) 層以支援 SignalR 。 在中 `Startup.Configure` ， `UseSignalR` 會呼叫方法，以連接 ASP.NET 核心要求管線中的中樞端點 (s) 。

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

在上述範例中，類別會實 `ClockHub` 作為 `Hub<T>` 建立強型別中樞的類別。 已 `ClockHub` 在類別中設定， `Startup` 以回應端點的要求 `/hubs/clock` 。

如需強型別中樞的詳細資訊，請參閱 [使用 SignalR ASP.NET Core 中的中樞](xref:signalr/hubs#strongly-typed-hubs)。

> [!NOTE]
> 此功能不限於[中樞 \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1)類別。 任何繼承自 [中樞](xref:Microsoft.AspNetCore.SignalR.Hub)的類別（例如 [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)）都可以運作。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

強型別所使用的介面 `ClockHub` 是 `IClock` 介面。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a>SignalR從背景服務呼叫中樞

在啟動期間， `Worker` `BackgroundService` 會使用來啟用類別（） `AddHostedService` 。

```csharp
services.AddHostedService<Worker>();
```

因為 SignalR 也會在 `Startup` 階段啟用，在此階段中，每個中樞會附加至 ASP.NET 核心的 HTTP 要求管線中的個別端點，因此每個中樞都會以伺服器上的來表示 `IHubContext<T>` 。 使用 ASP.NET Core 的 DI 功能時，裝載層（例如 `BackgroundService` 類別、MVC 控制器類別或頁面模型）所具現化的其他類別， Razor 可以藉由接受 `IHubContext<ClockHub, IClock>` 在結構內的實例來取得伺服器端中樞的參考。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

在 `ExecuteAsync` 背景服務中反復呼叫方法時，會使用將伺服器的目前日期和時間傳送到連接的用戶端 `ClockHub` 。

## <a name="react-to-signalr-events-with-background-services"></a>SignalR使用背景服務回應事件

如同使用的 JavaScript 用戶端的單一頁面應用程式 SignalR ，或使用的 .net 桌面應用程式 <xref:signalr/dotnet-client> ， `BackgroundService` 或 `IHostedService` 執行也可以用來連接到 SignalR 中樞並回應事件。

`ClockHubClient`類別會同時執行 `IClock` 介面和 `IHostedService` 介面。 如此一來，就可以在中 `Startup` 持續執行，並從伺服器回應中樞事件。

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

在初始化期間，會 `ClockHubClient` 建立的實例， `HubConnection` 並啟用該 `IClock.ShowTime` 方法做為中樞事件的處理常式 `ShowTime` 。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

在 `IHostedService.StartAsync` 執行 `HubConnection` 時，會以非同步方式啟動。

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

在 `IHostedService.StopAsync` 方法期間， `HubConnection` 會以非同步方式處置。

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

在 `IHostedService.StartAsync` 執行 `HubConnection` 時，會以非同步方式啟動。

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

在 `IHostedService.StopAsync` 方法期間， `HubConnection` 會以非同步方式處置。

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>其他資源

* [開始使用](xref:tutorials/signalr)
* [中樞](xref:signalr/hubs)
* [發佈至 Azure](xref:signalr/publish-to-azure-web-app)
* [強型別中樞](xref:signalr/hubs#strongly-typed-hubs)
