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
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="01f08-103">針對連線問題進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="01f08-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="01f08-104">本節提供嘗試建立 ASP.NET Core 中樞連線時可能發生之錯誤的說明 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="01f08-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="01f08-105">回應碼404</span><span class="sxs-lookup"><span data-stu-id="01f08-105">Response code 404</span></span>

<span data-ttu-id="01f08-106">使用 Websocket 和 `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="01f08-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="01f08-107">當您使用多部伺服器時，如果沒有任何「粘滯話」，連線可以在一部伺服器上啟動，然後切換到其他伺服器。</span><span class="sxs-lookup"><span data-stu-id="01f08-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="01f08-108">另一部伺服器並不知道先前的連接。</span><span class="sxs-lookup"><span data-stu-id="01f08-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="01f08-109">確認用戶端連接到正確的端點。</span><span class="sxs-lookup"><span data-stu-id="01f08-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="01f08-110">例如，伺服器裝載于 `http://127.0.0.1:5000/hub/myHub` ，而用戶端嘗試連接至 `http://127.0.0.1:5000/myHub` 。</span><span class="sxs-lookup"><span data-stu-id="01f08-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="01f08-111">如果連接使用此識別碼，而且在 negotiate 之後將要求傳送至伺服器的時間太長，伺服器會：</span><span class="sxs-lookup"><span data-stu-id="01f08-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="01f08-112">刪除識別碼。</span><span class="sxs-lookup"><span data-stu-id="01f08-112">Deletes the ID.</span></span>
  * <span data-ttu-id="01f08-113">傳回404。</span><span class="sxs-lookup"><span data-stu-id="01f08-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="01f08-114">回應碼400或503</span><span class="sxs-lookup"><span data-stu-id="01f08-114">Response code 400 or 503</span></span>

<span data-ttu-id="01f08-115">針對下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="01f08-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="01f08-116">此錯誤通常是因為用戶端只使用 Websocket 傳輸，但在伺服器上未啟用 Websocket 通訊協定。</span><span class="sxs-lookup"><span data-stu-id="01f08-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="01f08-117">回應碼307</span><span class="sxs-lookup"><span data-stu-id="01f08-117">Response code 307</span></span>

<span data-ttu-id="01f08-118">使用 Websocket 和 `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="01f08-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="01f08-119">在 negotiate 要求期間，也會發生此錯誤。</span><span class="sxs-lookup"><span data-stu-id="01f08-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="01f08-120">常見原因：</span><span class="sxs-lookup"><span data-stu-id="01f08-120">Common cause:</span></span>
* <span data-ttu-id="01f08-121">應用程式已設定為在中呼叫以強制執行 HTTPS `UseHttpsRedirection` `Startup` ，或透過 URL 重寫規則強制使用 HTTPs。</span><span class="sxs-lookup"><span data-stu-id="01f08-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="01f08-122">可能的解決方案：</span><span class="sxs-lookup"><span data-stu-id="01f08-122">Possible solution:</span></span>
* <span data-ttu-id="01f08-123">將用戶端上的 URL 從 "HTTP" 變更為 "HTTPs"。</span><span class="sxs-lookup"><span data-stu-id="01f08-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="01f08-124">回應碼405</span><span class="sxs-lookup"><span data-stu-id="01f08-124">Response code 405</span></span>

<span data-ttu-id="01f08-125">Http 狀態碼 405-不允許的方法</span><span class="sxs-lookup"><span data-stu-id="01f08-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="01f08-126">應用程式未啟用[CORS](xref:signalr/security#cross-origin-resource-sharing)</span><span class="sxs-lookup"><span data-stu-id="01f08-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="01f08-127">回應碼0</span><span class="sxs-lookup"><span data-stu-id="01f08-127">Response code 0</span></span>

<span data-ttu-id="01f08-128">Http 狀態碼 0-通常是 [CORS](xref:signalr/security#cross-origin-resource-sharing) 問題，未提供狀態碼</span><span class="sxs-lookup"><span data-stu-id="01f08-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="01f08-129">將預期的來源新增至 `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="01f08-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="01f08-130">新增 `.AllowCredentials()` 至您的 CORS 原則。</span><span class="sxs-lookup"><span data-stu-id="01f08-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="01f08-131">無法使用 `.AllowAnyOrigin()` 或搭配 `.WithOrigins("*")` 此選項</span><span class="sxs-lookup"><span data-stu-id="01f08-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="01f08-132">回應碼413</span><span class="sxs-lookup"><span data-stu-id="01f08-132">Response code 413</span></span>

<span data-ttu-id="01f08-133">Http 狀態碼 413-承載太大</span><span class="sxs-lookup"><span data-stu-id="01f08-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="01f08-134">這通常是因為具有超過4k 的存取權杖所造成。</span><span class="sxs-lookup"><span data-stu-id="01f08-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="01f08-135">如果使用 Azure SignalR 服務，請使用下列方法自訂透過服務傳送的宣告來減少權杖大小：</span><span class="sxs-lookup"><span data-stu-id="01f08-135">If using the Azure SignalR Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="01f08-136">暫時性網路失敗</span><span class="sxs-lookup"><span data-stu-id="01f08-136">Transient network failures</span></span>

<span data-ttu-id="01f08-137">暫時性網路失敗可能會關閉 SignalR 連接。</span><span class="sxs-lookup"><span data-stu-id="01f08-137">Transient network failures may close the SignalR connection.</span></span> <span data-ttu-id="01f08-138">伺服器可能會將關閉的連接視為正常的用戶端中斷連線。</span><span class="sxs-lookup"><span data-stu-id="01f08-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="01f08-139">若要取得用戶端中斷連線的原因詳細資訊，請 [從用戶端和伺服器收集記錄](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="01f08-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>