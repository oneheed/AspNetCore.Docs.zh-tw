---
title: ASP.NET Core 中的安全性考慮 SignalR
author: bradygaster
description: 瞭解如何在 ASP.NET Core 中使用驗證和授權 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
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
uid: signalr/security
ms.openlocfilehash: 5ecbf07b1527e9c68443870f7fce77adc29a5416
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050832"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a>ASP.NET Core 中的安全性考慮 SignalR

[Andrew Stanton-護士](https://twitter.com/anurse)

本文提供有關保護的資訊 SignalR 。

## <a name="cross-origin-resource-sharing"></a>跨原始資源共用

[跨原始來源資源分享 (CORS) ](https://www.w3.org/TR/cors/) 可以用來允許瀏覽器中的跨原始來源連線 SignalR 。 如果 JavaScript 程式碼裝載在與應用程式不同的網域上 SignalR ，則必須啟用 [CORS 中介軟體](xref:security/cors) ，才能讓 javascript 連接至 SignalR 應用程式。 只允許來自您信任或控制的網域的跨原始來源要求。 例如：

* 您的網站裝載于 `http://www.example.com`
* 您的 SignalR 應用程式裝載于 `http://signalr.example.com`

您應在應用程式中設定 CORS SignalR ，以只允許來源 `www.example.com` 。

如需設定 CORS 的詳細資訊，請參閱 [啟用跨原始來源要求 (CORS) ](xref:security/cors)。 SignalR**需要** 下列 CORS 原則：

* 允許特定的預期來源。 允許任何來源都可以，但 **不** 是安全或建議的做法。
* HTTP 方法 `GET` 和 `POST` 必須是允許的。
* 必須允許認證，才能讓以 cookie 正常運作的即時會話正常運作。 即使未使用驗證，也必須啟用它們。

::: moniker range=">= aspnetcore-5.0"

不過，在5.0 中，我們在 TypeScript 用戶端中提供了不使用認證的選項。
不使用認證的選項只應在您100知道 Cookie 您的應用程式中使用 cookie 多部) 伺服器時，azure app service 所使用的認證（例如，您的應用程式中不需要的認證） (中使用。

::: moniker-end

例如，下列 CORS 原則可讓主控的 SignalR 瀏覽器用戶端 `https://example.com` 存取裝載 SignalR 于的應用程式 `https://signalr.example.com` ：

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

## <a name="websocket-origin-restriction"></a>WebSocket 來源限制

::: moniker range=">= aspnetcore-2.2"

CORS 所提供的保護不套用至 WebSocket。 如需 Websocket 的原始限制，請參閱 [websocket 原始限制](xref:fundamentals/websockets#websocket-origin-restriction)。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS 所提供的保護不套用至 WebSocket。 瀏覽器 **不** 會：

* 執行 CORS 的事前要求。
* 進行 WebSocket 要求時，採用 `Access-Control` 標頭中所指定的限制。

不過，瀏覽器會在發出 WebSocket 要求時，傳送 `Origin` 標頭。 應設定應用程式驗證這些標頭，以確保只允許來自預期來源的 WebSocket。

在 ASP.NET Core 2.1 和更新版本中，可以使用先前放置的自訂中間 **件 `UseSignalR` ，以及中的驗證中介軟體** 來達成標頭驗證 `Configure` ：

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> 因為 `Origin` 由用戶端控制，所以和 `Referer` 標頭一樣可能受到偽造。 這些標頭 **不** 應當做驗證機制使用。

::: moniker-end

## <a name="connectionid"></a>ConnectionId

`ConnectionId`如果 SignalR 伺服器或用戶端版本是 ASP.NET Core 2.2 或更早版本，則公開可能會導致惡意模擬。 如果 SignalR 伺服器和用戶端版本是 ASP.NET Core 3.0 或更新版本，則 `ConnectionToken` （而非） `ConnectionId` 必須保持秘密。 `ConnectionToken`刻意不會在任何 API 中公開。  可能很難確保較舊的 SignalR 用戶端不會連接到伺服器，因此即使您 SignalR 的伺服器版本是 ASP.NET Core 3.0 或更新版本，也不應該 `ConnectionId` 公開。

## <a name="access-token-logging"></a>存取權杖記錄

使用 Websocket 或 Server-Sent 事件時，瀏覽器用戶端會在查詢字串中傳送存取權杖。 透過查詢字串接收存取權杖通常是安全的，因為使用標準 `Authorization` 標頭。 請一律使用 HTTPS 來確保用戶端與伺服器之間有安全的端對端連接。 許多 web 伺服器會記錄每個要求的 URL，包括查詢字串。 記錄 Url 可能會記錄存取權杖。 ASP.NET Core 預設會記錄每個要求的 URL，其中包含查詢字串。 例如：

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

如果您對於使用伺服器記錄檔記錄這項資料有疑慮，可以將記錄器設定為層級或更新版本，以完全停用此記錄 `Microsoft.AspNetCore.Hosting` `Warning` (這些訊息會在 `Info` 層級) 寫入。 如需詳細資訊，請參閱 [記錄篩選](xref:fundamentals/logging/index#log-filtering) 以取得詳細資訊。 如果您仍想要記錄某些要求資訊，您可以 [撰寫中介軟體](xref:fundamentals/middleware/write) 來記錄所需的資料，並篩選出 `access_token` 查詢字串值 (如果有) 的話。

## <a name="exceptions"></a>例外狀況

例外狀況訊息通常會視為不應該向用戶端顯示的機密資料。 根據預設， SignalR 不會將中樞方法擲回之例外狀況的詳細資料傳送至用戶端。 相反地，用戶端會收到一般訊息，指出發生錯誤。 您可以使用 [EnableDetailedErrors](xref:signalr/configuration#configure-server-options)來覆寫用戶端的例外狀況訊息傳遞 (例如開發或測試) 。 例外狀況訊息不應該在生產應用程式中公開給用戶端。

## <a name="buffer-management"></a>緩衝區管理

SignalR 使用每一連接緩衝區來管理傳入和傳出訊息。 依預設，會 SignalR 將這些緩衝區限制為 32 KB。 用戶端或伺服器可以傳送的最大訊息是 32 KB。 訊息的連接所耗用的記憶體上限為 32 KB。 如果您的訊息一律小於 32 KB，您可以減少限制，也就是：

* 防止用戶端能夠傳送較大的訊息。
* 伺服器永遠都不需要配置大型緩衝區來接受訊息。

如果您的訊息大於 32 KB，您可以增加限制。 增加此限制表示：

* 用戶端可能會導致伺服器配置大型的記憶體緩衝區。
* 大型緩衝區的伺服器配置可能會減少並行連接的數目。

傳入和傳出訊息有一些限制，可在中設定的 [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) 物件上設定 `MapHub` ：

* `ApplicationMaxBufferSize` 代表用戶端伺服器緩衝的最大位元組數目。 如果用戶端嘗試傳送大於此限制的訊息，則可能會關閉連接。
* `TransportMaxBufferSize` 表示伺服器可以傳送的最大位元組數目。 如果伺服器嘗試傳送訊息 (包括中樞方法的傳回值) 大於此限制，則會擲回例外狀況。

將限制設定為 `0` 停用限制。 移除限制可讓用戶端傳送任何大小的訊息。 傳送大型訊息的惡意用戶端可能會導致配置過多的記憶體。 過多的記憶體使用量可能會大幅減少並行連接的數目。
