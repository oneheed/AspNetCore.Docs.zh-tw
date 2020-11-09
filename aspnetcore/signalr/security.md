---
title: 'ASP.NET Core 中的安全性考慮 SignalR'
author: bradygaster
description: '瞭解如何在 ASP.NET Core 中使用驗證和授權 SignalR 。'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/security
ms.openlocfilehash: 5ecbf07b1527e9c68443870f7fce77adc29a5416
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050832"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="52ae6-103">ASP.NET Core 中的安全性考慮 SignalR</span><span class="sxs-lookup"><span data-stu-id="52ae6-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="52ae6-104">[Andrew Stanton-護士](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="52ae6-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="52ae6-105">本文提供有關保護的資訊 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="52ae6-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="52ae6-106">跨原始資源共用</span><span class="sxs-lookup"><span data-stu-id="52ae6-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="52ae6-107">[跨原始來源資源分享 (CORS) ](https://www.w3.org/TR/cors/) 可以用來允許瀏覽器中的跨原始來源連線 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="52ae6-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="52ae6-108">如果 JavaScript 程式碼裝載在與應用程式不同的網域上 SignalR ，則必須啟用 [CORS 中介軟體](xref:security/cors) ，才能讓 javascript 連接至 SignalR 應用程式。</span><span class="sxs-lookup"><span data-stu-id="52ae6-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="52ae6-109">只允許來自您信任或控制的網域的跨原始來源要求。</span><span class="sxs-lookup"><span data-stu-id="52ae6-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="52ae6-110">例如：</span><span class="sxs-lookup"><span data-stu-id="52ae6-110">For example:</span></span>

* <span data-ttu-id="52ae6-111">您的網站裝載于 `http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="52ae6-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="52ae6-112">您的 SignalR 應用程式裝載于 `http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="52ae6-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="52ae6-113">您應在應用程式中設定 CORS SignalR ，以只允許來源 `www.example.com` 。</span><span class="sxs-lookup"><span data-stu-id="52ae6-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="52ae6-114">如需設定 CORS 的詳細資訊，請參閱 [啟用跨原始來源要求 (CORS) ](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="52ae6-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="52ae6-115">SignalR**需要** 下列 CORS 原則：</span><span class="sxs-lookup"><span data-stu-id="52ae6-115">SignalR **requires** the following CORS policies:</span></span>

* <span data-ttu-id="52ae6-116">允許特定的預期來源。</span><span class="sxs-lookup"><span data-stu-id="52ae6-116">Allow the specific expected origins.</span></span> <span data-ttu-id="52ae6-117">允許任何來源都可以，但 **不** 是安全或建議的做法。</span><span class="sxs-lookup"><span data-stu-id="52ae6-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="52ae6-118">HTTP 方法 `GET` 和 `POST` 必須是允許的。</span><span class="sxs-lookup"><span data-stu-id="52ae6-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="52ae6-119">必須允許認證，才能讓以 cookie 正常運作的即時會話正常運作。</span><span class="sxs-lookup"><span data-stu-id="52ae6-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="52ae6-120">即使未使用驗證，也必須啟用它們。</span><span class="sxs-lookup"><span data-stu-id="52ae6-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="52ae6-121">不過，在5.0 中，我們在 TypeScript 用戶端中提供了不使用認證的選項。</span><span class="sxs-lookup"><span data-stu-id="52ae6-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="52ae6-122">不使用認證的選項只應在您100知道 Cookie 您的應用程式中使用 cookie 多部) 伺服器時，azure app service 所使用的認證（例如，您的應用程式中不需要的認證） (中使用。</span><span class="sxs-lookup"><span data-stu-id="52ae6-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="52ae6-123">例如，下列 CORS 原則可讓主控的 SignalR 瀏覽器用戶端 `https://example.com` 存取裝載 SignalR 于的應用程式 `https://signalr.example.com` ：</span><span class="sxs-lookup"><span data-stu-id="52ae6-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="52ae6-124">WebSocket 來源限制</span><span class="sxs-lookup"><span data-stu-id="52ae6-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="52ae6-125">CORS 所提供的保護不套用至 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="52ae6-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="52ae6-126">如需 Websocket 的原始限制，請參閱 [websocket 原始限制](xref:fundamentals/websockets#websocket-origin-restriction)。</span><span class="sxs-lookup"><span data-stu-id="52ae6-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="52ae6-127">CORS 所提供的保護不套用至 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="52ae6-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="52ae6-128">瀏覽器 **不** 會：</span><span class="sxs-lookup"><span data-stu-id="52ae6-128">Browsers do **not** :</span></span>

* <span data-ttu-id="52ae6-129">執行 CORS 的事前要求。</span><span class="sxs-lookup"><span data-stu-id="52ae6-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="52ae6-130">進行 WebSocket 要求時，採用 `Access-Control` 標頭中所指定的限制。</span><span class="sxs-lookup"><span data-stu-id="52ae6-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="52ae6-131">不過，瀏覽器會在發出 WebSocket 要求時，傳送 `Origin` 標頭。</span><span class="sxs-lookup"><span data-stu-id="52ae6-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="52ae6-132">應設定應用程式驗證這些標頭，以確保只允許來自預期來源的 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="52ae6-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="52ae6-133">在 ASP.NET Core 2.1 和更新版本中，可以使用先前放置的自訂中間 **件 `UseSignalR` ，以及中的驗證中介軟體** 來達成標頭驗證 `Configure` ：</span><span class="sxs-lookup"><span data-stu-id="52ae6-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="52ae6-134">因為 `Origin` 由用戶端控制，所以和 `Referer` 標頭一樣可能受到偽造。</span><span class="sxs-lookup"><span data-stu-id="52ae6-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="52ae6-135">這些標頭 **不** 應當做驗證機制使用。</span><span class="sxs-lookup"><span data-stu-id="52ae6-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="52ae6-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="52ae6-136">ConnectionId</span></span>

<span data-ttu-id="52ae6-137">`ConnectionId`如果 SignalR 伺服器或用戶端版本是 ASP.NET Core 2.2 或更早版本，則公開可能會導致惡意模擬。</span><span class="sxs-lookup"><span data-stu-id="52ae6-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="52ae6-138">如果 SignalR 伺服器和用戶端版本是 ASP.NET Core 3.0 或更新版本，則 `ConnectionToken` （而非） `ConnectionId` 必須保持秘密。</span><span class="sxs-lookup"><span data-stu-id="52ae6-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="52ae6-139">`ConnectionToken`刻意不會在任何 API 中公開。</span><span class="sxs-lookup"><span data-stu-id="52ae6-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="52ae6-140">可能很難確保較舊的 SignalR 用戶端不會連接到伺服器，因此即使您 SignalR 的伺服器版本是 ASP.NET Core 3.0 或更新版本，也不應該 `ConnectionId` 公開。</span><span class="sxs-lookup"><span data-stu-id="52ae6-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="52ae6-141">存取權杖記錄</span><span class="sxs-lookup"><span data-stu-id="52ae6-141">Access token logging</span></span>

<span data-ttu-id="52ae6-142">使用 Websocket 或 Server-Sent 事件時，瀏覽器用戶端會在查詢字串中傳送存取權杖。</span><span class="sxs-lookup"><span data-stu-id="52ae6-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="52ae6-143">透過查詢字串接收存取權杖通常是安全的，因為使用標準 `Authorization` 標頭。</span><span class="sxs-lookup"><span data-stu-id="52ae6-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="52ae6-144">請一律使用 HTTPS 來確保用戶端與伺服器之間有安全的端對端連接。</span><span class="sxs-lookup"><span data-stu-id="52ae6-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="52ae6-145">許多 web 伺服器會記錄每個要求的 URL，包括查詢字串。</span><span class="sxs-lookup"><span data-stu-id="52ae6-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="52ae6-146">記錄 Url 可能會記錄存取權杖。</span><span class="sxs-lookup"><span data-stu-id="52ae6-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="52ae6-147">ASP.NET Core 預設會記錄每個要求的 URL，其中包含查詢字串。</span><span class="sxs-lookup"><span data-stu-id="52ae6-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="52ae6-148">例如：</span><span class="sxs-lookup"><span data-stu-id="52ae6-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

<span data-ttu-id="52ae6-149">如果您對於使用伺服器記錄檔記錄這項資料有疑慮，可以將記錄器設定為層級或更新版本，以完全停用此記錄 `Microsoft.AspNetCore.Hosting` `Warning` (這些訊息會在 `Info` 層級) 寫入。</span><span class="sxs-lookup"><span data-stu-id="52ae6-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="52ae6-150">如需詳細資訊，請參閱 [記錄篩選](xref:fundamentals/logging/index#log-filtering) 以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="52ae6-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="52ae6-151">如果您仍想要記錄某些要求資訊，您可以 [撰寫中介軟體](xref:fundamentals/middleware/write) 來記錄所需的資料，並篩選出 `access_token` 查詢字串值 (如果有) 的話。</span><span class="sxs-lookup"><span data-stu-id="52ae6-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="52ae6-152">例外狀況</span><span class="sxs-lookup"><span data-stu-id="52ae6-152">Exceptions</span></span>

<span data-ttu-id="52ae6-153">例外狀況訊息通常會視為不應該向用戶端顯示的機密資料。</span><span class="sxs-lookup"><span data-stu-id="52ae6-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="52ae6-154">根據預設， SignalR 不會將中樞方法擲回之例外狀況的詳細資料傳送至用戶端。</span><span class="sxs-lookup"><span data-stu-id="52ae6-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="52ae6-155">相反地，用戶端會收到一般訊息，指出發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="52ae6-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="52ae6-156">您可以使用 [EnableDetailedErrors](xref:signalr/configuration#configure-server-options)來覆寫用戶端的例外狀況訊息傳遞 (例如開發或測試) 。</span><span class="sxs-lookup"><span data-stu-id="52ae6-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="52ae6-157">例外狀況訊息不應該在生產應用程式中公開給用戶端。</span><span class="sxs-lookup"><span data-stu-id="52ae6-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="52ae6-158">緩衝區管理</span><span class="sxs-lookup"><span data-stu-id="52ae6-158">Buffer management</span></span>

<span data-ttu-id="52ae6-159">SignalR 使用每一連接緩衝區來管理傳入和傳出訊息。</span><span class="sxs-lookup"><span data-stu-id="52ae6-159">SignalR uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="52ae6-160">依預設，會 SignalR 將這些緩衝區限制為 32 KB。</span><span class="sxs-lookup"><span data-stu-id="52ae6-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="52ae6-161">用戶端或伺服器可以傳送的最大訊息是 32 KB。</span><span class="sxs-lookup"><span data-stu-id="52ae6-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="52ae6-162">訊息的連接所耗用的記憶體上限為 32 KB。</span><span class="sxs-lookup"><span data-stu-id="52ae6-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="52ae6-163">如果您的訊息一律小於 32 KB，您可以減少限制，也就是：</span><span class="sxs-lookup"><span data-stu-id="52ae6-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="52ae6-164">防止用戶端能夠傳送較大的訊息。</span><span class="sxs-lookup"><span data-stu-id="52ae6-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="52ae6-165">伺服器永遠都不需要配置大型緩衝區來接受訊息。</span><span class="sxs-lookup"><span data-stu-id="52ae6-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="52ae6-166">如果您的訊息大於 32 KB，您可以增加限制。</span><span class="sxs-lookup"><span data-stu-id="52ae6-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="52ae6-167">增加此限制表示：</span><span class="sxs-lookup"><span data-stu-id="52ae6-167">Increasing this limit means:</span></span>

* <span data-ttu-id="52ae6-168">用戶端可能會導致伺服器配置大型的記憶體緩衝區。</span><span class="sxs-lookup"><span data-stu-id="52ae6-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="52ae6-169">大型緩衝區的伺服器配置可能會減少並行連接的數目。</span><span class="sxs-lookup"><span data-stu-id="52ae6-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="52ae6-170">傳入和傳出訊息有一些限制，可在中設定的 [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) 物件上設定 `MapHub` ：</span><span class="sxs-lookup"><span data-stu-id="52ae6-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="52ae6-171">`ApplicationMaxBufferSize` 代表用戶端伺服器緩衝的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="52ae6-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="52ae6-172">如果用戶端嘗試傳送大於此限制的訊息，則可能會關閉連接。</span><span class="sxs-lookup"><span data-stu-id="52ae6-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="52ae6-173">`TransportMaxBufferSize` 表示伺服器可以傳送的最大位元組數目。</span><span class="sxs-lookup"><span data-stu-id="52ae6-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="52ae6-174">如果伺服器嘗試傳送訊息 (包括中樞方法的傳回值) 大於此限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="52ae6-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="52ae6-175">將限制設定為 `0` 停用限制。</span><span class="sxs-lookup"><span data-stu-id="52ae6-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="52ae6-176">移除限制可讓用戶端傳送任何大小的訊息。</span><span class="sxs-lookup"><span data-stu-id="52ae6-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="52ae6-177">傳送大型訊息的惡意用戶端可能會導致配置過多的記憶體。</span><span class="sxs-lookup"><span data-stu-id="52ae6-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="52ae6-178">過多的記憶體使用量可能會大幅減少並行連接的數目。</span><span class="sxs-lookup"><span data-stu-id="52ae6-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
