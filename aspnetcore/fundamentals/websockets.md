---
title: ASP.NET Core 中的 WebSockets 支援
author: rick-anderson
description: 了解如何在 ASP.NET Core 中開始使用 WebSocket。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 2864d98c1e3cff4474ce38d05c47f7cd0e8b3cc5
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395236"
---
# <a name="websockets-support-in-aspnet-core"></a>ASP.NET Core 中的 WebSockets 支援

作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Andrew Stanton-Nurse](https://github.com/anurse)

本文說明如何在 ASP.NET Core 中開始使用 WebSocket。 [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) 為通訊協定，其可在 TCP 連線下啟用雙向的持續性通訊通道。 它用於受益於快速且即時通訊的應用程式，例如聊天、儀表板和遊戲應用程式。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([如何下載](xref:index#how-to-download-a-sample))。 [如何執行](#sample-app)。

## SignalR

[ASP.NET 核心 SignalR ](xref:signalr/introduction)是可簡化將即時 web 功能新增至應用程式的程式庫。 它會盡可能使用 WebSockets。

針對大部分的應用程式，我們建議您不要透過 SignalR 原始 websocket。 SignalR 針對無法使用 Websocket 的環境提供傳輸回復。 它也會提供基本的遠端程序呼叫應用程式模型。 在大部分的情況下， SignalR 相較于使用原始 websocket，沒有顯著的效能缺點。

針對某些應用程式， [gRPC on .net](xref:grpc/index) 提供 websocket 的替代方案。

## <a name="prerequisites"></a>必要條件

* 支援 ASP.NET Core 的任何作業系統：  
  * Windows 7/Windows Server 2008 或更新版本
  * Linux
  * macOS  
* 如果應用程式在 Windows 上與 IIS 搭配執行：
  * Windows 8 / Windows Server 2012 或更新版本
  * IIS 8 / IIS 8 Express
  * 必須啟用 Websocket。 請參閱 [iis/Iis Express 支援](#iisiis-express-support) 區段。  
* 如果應用程式在 [HTTP.sys](xref:fundamentals/servers/httpsys) 上執行：
  * Windows 8 / Windows Server 2012 或更新版本
* 如需支援的瀏覽器，請請參閱 https://caniuse.com/#feat=websockets。

## <a name="configure-the-middleware"></a>設定中介軟體

在 `Startup` 類別的 `Configure` 方法中新增 WebSocket 中介軟體：

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

> [!NOTE]
> 如果您想要接受控制器中的 WebSocket 要求， `app.UseWebSockets` 必須先進行的呼叫 `app.UseEndpoints` 。

::: moniker range="< aspnetcore-2.2"

您可以設定下列設定：

* `KeepAliveInterval` - 要將 "ping" 框架傳送到用戶端，以確保 Proxy 保持連線開啟的頻率。 預設為兩分鐘。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

您可以設定下列設定：

* `KeepAliveInterval` - 要將 "ping" 框架傳送到用戶端，以確保 Proxy 保持連線開啟的頻率。 預設為兩分鐘。
* `AllowedOrigins` - WebSocket 要求之允許 Origin 標頭值的清單。 根據預設，會允許所有來源。 如需詳細資訊，請參閱下方的 "WebSocket origin restriction" (WebSocket 來源限制)。

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>接受 WebSocket 要求

在要求生命週期的後半部某處 (例如，在 `Configure` 方法或動作方法的後半部)，檢查它是否為 WebSocket 要求並接受 WebSocket 要求。

下列範例取自 `Configure` 方法的後半部：

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

WebSocket 要求可以傳入任何 URL，但此範例程式碼只接受 `/ws` 的要求。

使用 WebSocket 時，您 **必須** 確保中介軟體管線會在連線期間內持續執行。 如果您嘗試在中介軟體管線結束後傳送或接收 WebSocket 訊息，便可能會收到類似下列的例外狀況：

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

如果您是使用背景服務來將資料寫入 WebSocket，請務必使中介軟體管線持續執行。 請使用 <xref:System.Threading.Tasks.TaskCompletionSource%601> 來這麼做。 將 `TaskCompletionSource` 傳遞至您的背景服務，並讓它在您完成使用 WebSocket 時呼叫 <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A>。 然後，在要求期間，`await`<xref:System.Threading.Tasks.TaskCompletionSource%601.Task> 屬性，如下列範例所示：

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

從動作方法傳回太短時，也可能會發生 WebSocket 封閉式例外狀況。 在動作方法中接受通訊端時，請等候使用通訊端的程式碼完成後，再從動作方法返回。

絕不要使用 `Task.Wait`、`Task.Result` 或類似的封鎖呼叫等候通訊端完成，因為這會導致嚴重的執行緒處理問題。 一律使用 `await`。

## <a name="send-and-receive-messages"></a>傳送及接收訊息

`AcceptWebSocketAsync` 方法可將 TCP 連線升級為 WebSocket 連線，並提供 [WebSocket](/dotnet/core/api/system.net.websockets.websocket) 物件。 請使用 `WebSocket` 物件來傳送和接收訊息。

稍早所示接受 WebSocket 要求的程式碼會將 `WebSocket` 物件傳遞給 `Echo` 方法。 程式碼會收到一則訊息，並立即傳送回相同的訊息。 在用戶端關閉連線之前，訊息會在迴圈中傳送和接收：

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

如果在開始此迴圈之前接受 WebSocket 連線，中介軟體管線就會結束。 在關閉通訊端後，管線會回溯。 也就是說，在接受 WebSocket 後，要求就會在管線中停止向前移動。 當迴圈完成且通訊端關閉時，要求會繼續備份管線。

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>處理用戶端中斷連線問題

當用戶端由於失去連線而中斷連線時，不會自動通知伺服器。 只有當用戶端傳送通知時，伺服器才會收到連線中斷訊息，而在網際網路連線中斷的情況下無法傳送通知。 若想要在發生此情況時採取一些動作，請設定逾時，在指定的時間內未從用戶端收到通知之後觸發逾時。

若用戶端並非總是會傳送訊息，而且您不想讓系統只是因為連線閒置而觸發逾時，請讓用戶端使用計時器每隔 X 秒傳送一次 Ping 訊息。 在伺服器上，若訊息未在收到前一個訊息後的 2\*X 秒內抵達，則中止連線並回報用戶端已中斷連線。 等候預期時間間隔兩倍的時間，為網路延遲保留足夠的額外時間，看看能不能收到耽誤的 Ping 訊息。

## <a name="websocket-origin-restriction"></a>WebSocket 來源限制

CORS 所提供的保護不套用至 WebSocket。 瀏覽器 **不** 會：

* 執行 CORS 的事前要求。
* 進行 WebSocket 要求時，採用 `Access-Control` 標頭中所指定的限制。

不過，瀏覽器會在發出 WebSocket 要求時，傳送 `Origin` 標頭。 應設定應用程式驗證這些標頭，以確保只允許來自預期來源的 WebSocket。

若您在 "https://server.com" 上裝載伺服器，且在 "https://client.com" 上裝載用戶端，請將 "https://client.com" 新增至 `AllowedOrigins` 清單中，以讓 WebSockets 進行驗證。

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> 因為 `Origin` 由用戶端控制，所以和 `Referer` 標頭一樣可能受到偽造。 **請勿** 使用這些標頭作為驗證機制。

::: moniker-end

## <a name="iisiis-express-support"></a>IIS/IIS Express 支援

搭配 IIS/IIS Express 8 或更新版本的 Windows Server 2012 或更新版本和 Windows 8 或更新版本具有 WebSocket 通訊協定的支援。

> [!NOTE]
> 使用 IIS Express 時一律會啟用 WebSockets。

### <a name="enabling-websockets-on-iis"></a>在 IIS 上啟用 WebSockets

若要在 Windows Server 2012 或更新版本中啟用 WebSocket 通訊協定的支援：

> [!NOTE]
> 使用 IIS Express 時，不需要這些步驟

1. 使用來自 [管理] 功能表的 [新增角色及功能] 精靈，或是 [伺服器管理員] 中的連結。
1. 選取 [角色型或功能型安裝]。 選取 [下一步] 。
1. 選取適當的伺服器 (預設會選取本機伺服器)。 選取 [下一步] 。
1. 展開 [角色] 樹狀目錄中的 [網頁伺服器 (IIS)]，展開 [網頁伺服器]，然後展開 [應用程式開發]。
1. 選取 [WebSocket 通訊協定]。 選取 [下一步] 。
1. 如果不需要額外的功能，請選取 [下一步]。
1. 選取 [安裝]。
1. 當安裝完成時，選取 [關閉] 來結束精靈。

若要在 Windows 8 或更新版本中啟用 WebSocket 通訊協定的支援：

> [!NOTE]
> 使用 IIS Express 時，不需要這些步驟

1. 流覽至 [**控制台**] 的 [程式  >    >  **和功能]，**  >  **開啟或關閉** 畫面 (左側的 [Windows 功能]) 。
1. 開啟下列節點： **Internet information Services**  >  **World Wide Web 服務**  >  **應用程式開發功能**。
1. 選取 [WebSocket 通訊協定] 功能。 選取 [確定]。

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>在 Node.js 上使用 socket.io 時停用 WebSocket

如果在 [Node.js](https://nodejs.org/)上使用 [Socket.io](https://socket.io/)中的 WebSocket 支援，請使用 `webSocket` *web.config* 或 *applicationHost.config* 中的元素，停用預設的 IIS WebSocket 模組。如果未執行此步驟，IIS WebSocket 模組會嘗試處理 WebSocket 通訊，而不是 Node.js 和應用程式。

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>範例應用程式

本文附帶的[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples)是回應應用程式。 它有一個可進行 WebSocket 連線的網頁，而伺服器會將其接收的任何訊息重新傳送回用戶端。 範例應用程式未設定為從 Visual Studio 搭配 IIS Express 執行，因此請在的命令 shell 中執行應用程式， [`dotnet run`](/dotnet/core/tools/dotnet-run) 並在瀏覽器中流覽至 `http://localhost:5000` 。 網頁會顯示連接狀態：

![在 Websocket 連接之前網頁的初始狀態](websockets/_static/start.png)

選取 [連線] 將 WebSocket 要求傳送到顯示的 URL。 輸入測試訊息，然後選取 [傳送]。 完成後，請選取 [關閉通訊端]。 [通訊記錄檔] 區段會報告每次進行的開啟、傳送和關閉動作。

![傳送和接收 Websocket 連接和測試訊息之後的網頁最終狀態](websockets/_static/end.png)
