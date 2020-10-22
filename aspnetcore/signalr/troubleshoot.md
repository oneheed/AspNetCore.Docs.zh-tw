---
title: ASP.NET Core SignalR 連接疑難排解
author: bradygaster
description: ASP.NET Core SignalR 連接疑難排解。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: 2ffe8bcf4bef5dcb9fc74513c7507e5cb3a6b7cb
ms.sourcegitcommit: fad0cd264c9d07a48a8c6ba1690807e0f8728898
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379574"
---
# <a name="troubleshoot-connection-errors"></a>針對連線問題進行疑難排解

本節提供嘗試建立 ASP.NET Core 中樞連線時可能發生之錯誤的說明 SignalR 。

### <a name="response-code-404"></a>回應碼404

使用 Websocket 和 `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* 當您使用多部伺服器時，如果沒有任何「粘滯話」，連線可以在一部伺服器上啟動，然後切換到其他伺服器。 另一部伺服器並不知道先前的連接。
* 確認用戶端連接到正確的端點。 例如，伺服器裝載于 `http://127.0.0.1:5000/hub/myHub` ，而用戶端嘗試連接至 `http://127.0.0.1:5000/myHub` 。
* 如果連接使用此識別碼，而且在 negotiate 之後將要求傳送至伺服器的時間太長，伺服器會：

  * 刪除識別碼。
  * 傳回404。

### <a name="response-code-400-or-503"></a>回應碼400或503

針對下列錯誤：

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

此錯誤通常是因為用戶端只使用 Websocket 傳輸，但在伺服器上未啟用 Websocket 通訊協定。

### <a name="response-code-307"></a>回應碼307

使用 Websocket 和 `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

在 negotiate 要求期間，也會發生此錯誤。

常見原因：
* 應用程式已設定為在中呼叫以強制執行 HTTPS `UseHttpsRedirection` `Startup` ，或透過 URL 重寫規則強制使用 HTTPs。

可能的解決方案：
* 將用戶端上的 URL 從 "HTTP" 變更為 "HTTPs"。 `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>回應碼405

Http 狀態碼 405-不允許的方法

* 應用程式未啟用[CORS](xref:signalr/security#cross-origin-resource-sharing)

### <a name="response-code-0"></a>回應碼0

Http 狀態碼 0-通常是 [CORS](xref:signalr/security#cross-origin-resource-sharing) 問題，未提供狀態碼

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* 將預期的來源新增至 `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* 新增 `.AllowCredentials()` 至您的 CORS 原則。 無法使用 `.AllowAnyOrigin()` 或搭配 `.WithOrigins("*")` 此選項

### <a name="response-code-413"></a>回應碼413

Http 狀態碼 413-承載太大

這通常是因為具有超過4k 的存取權杖所造成。

* 如果使用 Azure SignalR 服務，請使用下列方法自訂透過服務傳送的宣告來減少權杖大小：
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>暫時性網路失敗

暫時性網路失敗可能會關閉 SignalR 連接。 伺服器可能會將關閉的連接視為正常的用戶端中斷連線。 若要取得用戶端中斷連線的原因詳細資訊，請 [從用戶端和伺服器收集記錄](xref:signalr/diagnostics)。