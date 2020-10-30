---
title: ASP.NET Core 簡介 SignalR
author: bradygaster
description: 瞭解 ASP.NET Core 連結 SignalR 庫如何簡化將即時功能新增至應用程式的程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 1810fef903362addcef4a6c9ec53264604f58d2b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051469"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a>ASP.NET Core 簡介 SignalR

## <a name="what-is-no-locsignalr"></a>什麼是 SignalR ？

ASP.NET Core SignalR 是一個開放原始碼程式庫，可簡化將即時 web 功能新增至應用程式的程式。 即時 web 功能可讓伺服器端程式碼立即將內容推送至用戶端。

適用于下列專案的絕佳候選項目 SignalR ：

* 需要經常從伺服器取得更新的應用程式。 例如遊戲、社交網路、投票、拍賣、地圖和 GPS 應用程式。
* 儀表板和監視應用程式。 範例包括公司儀表板、即時銷售更新或旅行警示。
* 共同作業應用程式。 共同作業應用程式的範例包括白板應用程式和小組會議軟體。
* 需要通知的應用程式。 社交網路、電子郵件、交談、遊戲、旅行警示和其他使用通知的應用程式。

SignalR 提供 API，可用於建立 (RPC) 的伺服器對用戶端 [遠端程序呼叫 ](https://wikipedia.org/wiki/Remote_procedure_call)。 這些 Rpc 會在用戶端上呼叫來自伺服器端 .NET Core 程式碼的 JavaScript 函式。

以下是 ASP.NET Core 的一些功能 SignalR ：

* 自動處理連接管理。
* 同時將訊息傳送給所有已連線的用戶端。 例如，聊天室。
* 將訊息傳送給特定用戶端或用戶端群組。
* 調整以處理增加的流量。

來源裝載于[ SignalR GitHub 上](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)的存放庫中。

## <a name="transports"></a>傳輸

SignalR 支援下列用來處理即時通訊 (的技巧，以順利回復) ：

* [WebSocket](https://tools.ietf.org/html/rfc7118)
* Server-Sent 事件
* 長時間輪詢

SignalR 自動選擇伺服器和用戶端功能內的最佳傳輸方法。

## <a name="hubs"></a>中樞

SignalR 使用 *中樞* 在用戶端與伺服器之間進行通訊。

中樞是一種高階管線，可讓用戶端和伺服器呼叫彼此的方法。 SignalR 會自動處理跨電腦界限的分派，讓用戶端可以在伺服器上呼叫方法，反之亦然。 您可以將強型別參數傳遞給可啟用模型系結的方法。 SignalR 提供兩種內建的中樞通訊協定：以 JSON 為基礎的文字通訊協定，以及以 [MessagePack](https://msgpack.org/)為基礎的二進位通訊協定。  相較于 JSON，MessagePack 通常會建立較小的訊息。 舊版瀏覽器必須支援 [XHR 層級 2](https://caniuse.com/#feat=xhr2) ，才能提供 MessagePack 通訊協定支援。

中樞會傳送包含用戶端方法名稱和參數的訊息，以呼叫用戶端程式代碼。 傳送為方法參數的物件會使用設定的通訊協定來還原序列化。 用戶端會嘗試比對名稱與用戶端程式代碼中的方法。 當用戶端找到相符的時，它會呼叫方法並將已還原序列化的參數資料傳遞給它。

## <a name="additional-resources"></a>其他資源

* [開始使用 SignalR ASP.NET Core](xref:tutorials/signalr)
* [支援的平臺](xref:signalr/supported-platforms)
* [中樞](xref:signalr/hubs)
* [JavaScript 用戶端](xref:signalr/javascript-client)
