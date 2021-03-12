---
title: 使用 ASP.NET Core 中的中樞 SignalR
author: bradygaster
description: 瞭解如何使用 ASP.NET Core 中的中樞 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 872b88cc3c87137365de8c50a37bf5dd5fd9fe10
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587862"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>使用中 SignalR 的中樞進行 ASP.NET 核心

[Rachel Appel](https://twitter.com/rachelappel)和[古柯 Griffin](https://twitter.com/1kevgriff)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ ) [ (如何下載) ](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>什麼是 SignalR 中樞

SignalR中樞 API 可讓您從伺服器呼叫連接用戶端上的方法。 在伺服器程式碼中，您可以定義用戶端所呼叫的方法。 在用戶端程式代碼中，您可以定義從伺服器呼叫的方法。 SignalR 會處理幕後的所有專案，讓用戶端對伺服器和伺服器對用戶端的通訊能夠正常運作。

## <a name="configure-signalr-hubs"></a>設定 SignalR 中樞

SignalR中介軟體需要一些透過呼叫所設定的服務 `services.AddSignalR` 。

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

將 SignalR 功能新增至 ASP.NET Core 應用程式時，會 SignalR `endpoint.MapHub` 在 `Startup.Configure` 方法的回呼中呼叫來設定路由 `app.UseEndpoints` 。

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

將 SignalR 功能新增至 ASP.NET Core 應用程式時，會 SignalR `app.UseSignalR` 在方法中呼叫來設定路由 `Startup.Configure` 。

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>建立和使用中樞

宣告繼承自的類別 `Hub` ，並在其中新增公用方法，以建立中樞。 用戶端可以呼叫定義為的方法 `public` 。

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

您可以指定傳回型別和參數，包括複雜類型和陣列，就像在任何 c # 方法中一樣。 SignalR 在您的參數和傳回值中處理複雜物件和陣列的序列化和還原序列化。

> [!NOTE]
> 中樞是暫時性的：
>
> * 請勿在中樞類別的屬性中儲存狀態。 每個中樞方法呼叫都會在新的中樞實例上執行。
> * `await`在呼叫相依于中樞保持運作的非同步方法時使用。 例如， `Clients.All.SendAsync(...)` 如果在未呼叫的情況下呼叫， `await` 且中樞方法在完成之前完成，則像這樣的方法可能會失敗 `SendAsync` 。

## <a name="the-context-object"></a>CoNtext 物件

`Hub`類別具有 `Context` 屬性，其中包含下列具有連接相關資訊的屬性：

| 屬性 | 描述 |
| ------ | ----------- |
| `ConnectionId` | 取得所指派之連接的唯一識別碼 SignalR 。 每個連接都有一個連接識別碼。|
| `UserIdentifier` | 取得 [使用者識別碼](xref:signalr/groups)。 根據預設，會 SignalR 使用 `ClaimTypes.NameIdentifier` `ClaimsPrincipal` 與連接相關聯的，做為使用者識別碼。 |
| `User` | 取得 `ClaimsPrincipal` 與目前使用者相關聯的。 |
| `Items` | 取得索引鍵/值集合，可用來在此連接的範圍內共用資料。 資料可以儲存在此集合中，而且會針對跨不同中樞方法調用的連接保存。 |
| `Features` | 取得連接上可用之功能的集合。 目前，在大部分情況下並不需要此集合，因此尚未詳細記載。 |
| `ConnectionAborted` | 取得，以在 `CancellationToken` 中斷連接時通知。 |

`Hub.Context` 也包含下列方法：

| 方法 | 描述 |
| ------ | ----------- |
| `GetHttpContext` | 傳回 `HttpContext` 連接的， `null` 如果連接與 HTTP 要求沒有關聯，則為。 若為 HTTP 連接，您可以使用這個方法來取得 HTTP 標頭和查詢字串等資訊。 |
| `Abort` | 中止連線。 |

## <a name="the-clients-object"></a>用戶端物件

`Hub`類別具有 `Clients` 屬性，其中包含伺服器與用戶端之間通訊的下列屬性：

| 屬性 | 描述 |
| ------ | ----------- |
| `All` | 在所有連線的用戶端上呼叫方法 |
| `Caller` | 在叫用中樞方法的用戶端上呼叫方法 |
| `Others` | 除了叫用方法的用戶端之外，在所有連線的用戶端上呼叫方法 |

`Hub.Clients` 也包含下列方法：

| 方法 | 描述 |
| ------ | ----------- |
| `AllExcept` | 在所有連線的用戶端上呼叫方法（指定的連接除外） |
| `Client` | 在特定連線用戶端上呼叫方法 |
| `Clients` | 在特定連線用戶端上呼叫方法 |
| `Group` | 在指定群組中的所有連接上呼叫方法  |
| `GroupExcept` | 在指定的群組中的所有連接上呼叫方法（指定的連接除外） |
| `Groups` | 在多個連接群組上呼叫方法  |
| `OthersInGroup` | 在一組連接上呼叫方法，但不包括叫用中樞方法的用戶端。  |
| `User` | 在所有與特定使用者相關聯的連接上呼叫方法 |
| `Users` | 在與指定使用者相關聯的所有連接上呼叫方法 |

上述資料表中的每個屬性或方法都會傳回具有方法的物件 `SendAsync` 。 `SendAsync`方法可讓您提供要呼叫的用戶端方法名稱和參數。

## <a name="send-messages-to-clients"></a>將訊息傳送給用戶端

若要對特定用戶端進行呼叫，請使用物件的屬性 `Clients` 。 在下列範例中，有三個中樞方法：

* `SendMessage` 使用將訊息傳送至所有連線的用戶端 `Clients.All` 。
* `SendMessageToCaller` 使用將訊息傳回給呼叫端 `Clients.Caller` 。
* `SendMessageToGroups` 將訊息傳送給群組中的所有用戶端 `SignalR Users` 。

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>強型別中樞

使用的缺點 `SendAsync` 是它依賴魔術字串來指定要呼叫的用戶端方法。 如果用戶端的方法名稱拼錯或遺失，則會將程式碼開放給執行階段錯誤。

使用的替代方法 `SendAsync` 是使用強型別 `Hub` <xref:Microsoft.AspNetCore.SignalR.Hub%601> 。 在下列範例中， `ChatHub` 已將用戶端方法解壓縮至名為的介面 `IChatClient` 。

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

這個介面可以用來重構上述 `ChatHub` 範例。

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

使用 `Hub<IChatClient>` 可啟用用戶端方法的編譯時間檢查。 這可防止使用魔術字串所造成的問題，因為只能 `Hub<T>` 提供介面中所定義方法的存取權。

使用強型別會 `Hub<T>` 停用使用的能力 `SendAsync` 。 在介面上定義的任何方法仍可以定義為非同步。 事實上，這些方法都應該傳回 `Task` 。 由於它是介面，因此請勿使用 `async` 關鍵字。 例如：

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async`不會從方法名稱中移除尾碼。 除非您的用戶端方法是使用來定義 `.on('MyMethodAsync')` ，否則您不應該使用 `MyMethodAsync` 做為名稱。

## <a name="change-the-name-of-a-hub-method"></a>變更中樞方法的名稱

根據預設，伺服器中樞方法名稱是 .NET 方法的名稱。 不過，您可以使用 [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) 屬性來變更這個預設值，並手動指定方法的名稱。 叫用方法時，用戶端應該使用這個名稱，而不是 .NET 方法名稱。

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>處理連接的事件

SignalR中樞 API 提供 `OnConnectedAsync` 和 `OnDisconnectedAsync` 虛擬方法來管理和追蹤連接。 覆寫 `OnConnectedAsync` 虛擬方法，以在用戶端連線至中樞時執行動作，例如將它新增至群組。

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

覆寫 `OnDisconnectedAsync` 虛擬方法，以在用戶端中斷連線時執行動作。 如果用戶端藉由呼叫來刻意中斷 (`connection.stop()` ，例如) ， `exception` 參數將會是 `null` 。 但是，如果用戶端因為錯誤而中斷連線 (例如網路失敗) ， `exception` 參數將會包含描述失敗的例外狀況。

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>處理錯誤

在您的中樞方法中擲回的例外狀況會傳送至叫用方法的用戶端。 在 JavaScript 用戶端上，此方法會傳回 `invoke` [javascript 承諾](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)。 當用戶端收到使用附加至承諾的處理常式時發生錯誤時 `catch` ，就會叫用該處理常式，並將其傳遞為 JavaScript `Error` 物件。

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

如果您的中樞擲回例外狀況，則不會關閉連接。 依預設，會將 SignalR 一般錯誤訊息傳回給用戶端。 例如：

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

非預期的例外狀況通常包含敏感性資訊，例如資料庫連接失敗時所觸發之例外狀況中的資料庫伺服器名稱。 SignalR 預設不會公開這些詳細的錯誤訊息做為安全性措施。 如需隱藏例外狀況詳細資料的詳細資訊，請參閱 [安全性考慮文章](xref:signalr/security#exceptions) 。

如果 *您有想要* 傳播至用戶端的例外狀況，您可以使用 `HubException` 類別。 如果您 `HubException` 從中樞方法擲回，則 SignalR **會將** 整個訊息傳送給用戶端（未經修改）。

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR 只會將 `Message` 例外狀況的屬性傳送至用戶端。 例外狀況的堆疊追蹤和其他屬性無法供用戶端使用。

## <a name="related-resources"></a>相關資源

* [ASP.NET Core 簡介 SignalR](xref:signalr/introduction)
* [JavaScript 用戶端](xref:signalr/javascript-client)
* [發佈至 Azure](xref:signalr/publish-to-azure-web-app)
