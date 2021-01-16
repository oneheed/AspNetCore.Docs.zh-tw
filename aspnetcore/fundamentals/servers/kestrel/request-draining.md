---
title: ASP.NET Core Kestrel web 伺服器的要求清空
author: rick-anderson
description: 瞭解如何使用 Kestrel （適用于 ASP.NET Core 的跨平臺 web 伺服器）來清空要求。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253929"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel web 伺服器的要求清空

開啟 HTTP 連接相當耗時。 若是 HTTPS，也需要大量資源。 因此，Kestrel 會嘗試針對每個 HTTP/1.1 通訊協定重複使用連接。 要求主體必須完全取用，才能重複使用連線。 應用程式不一定會取用要求主體，例如伺服器傳回重新導向或404回應的 HTTP POST 要求。 在 HTTP POST 重新導向案例中：

* 用戶端可能已經傳送 POST 資料的一部分。
* 伺服器會寫入301回應。
* 此連接無法用於新的要求，直到來自前一個要求本文的 POST 資料完全讀取為止。
* Kestrel 會嘗試清空要求主體。 清空要求主體表示在不處理資料的情況下讀取和捨棄資料。

清空程式可讓您在允許連線重複使用，以及清空任何剩餘資料所需的時間之間進行取捨：

* 清空有五秒的時間，無法設定。
* 如果或標頭所指定的所有資料在 `Content-Length` `Transfer-Encoding` 此時間之前未被讀取，則連接會關閉。

有時您可能會想要在寫入回應之前或之後立即終止要求。 例如，用戶端可能會有限制性的資料上限。 限制上傳的資料可能是優先考慮。 在這種情況下，若要終止要求，請從控制器、頁面或中介軟體呼叫[HttpCoNtext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) 。 Razor

呼叫時有一些注意事項 `Abort` ：

* 建立新的連接可能很慢且昂貴。
* 在連接關閉之前，不保證用戶端已讀取回應。
* 呼叫 `Abort` 應該很罕見，而且會保留給嚴重的錯誤案例，而不是常見的錯誤。
  * 只有 `Abort` 在需要解決特定問題時才呼叫。 例如， `Abort` 如果惡意用戶端嘗試張貼資料，或在用戶端程式代碼中發生錯誤，而導致大型或數個要求，則呼叫。
  * 請勿呼叫 `Abort` 常見的錯誤狀況，例如 HTTP 404 (找不到) 。

在呼叫之前呼叫 [HttpResponse](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) ， `Abort` 可確保伺服器已完成寫入回應。 不過，用戶端行為不是可預測的，而且在中斷連接之前，可能不會讀取回應。

HTTP/2 的這個程式不同，因為通訊協定支援中止個別要求資料流程，而不需要關閉連接。 不適用五秒的清空超時時間。 如果在完成回應之後有任何未讀取的要求主體資料，則伺服器會傳送 HTTP/2 RST 框架。 系統會忽略其他要求主體資料框架。

可能的話，最好讓用戶端使用 [預期的： 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 要求標頭，並等待伺服器回應，然後再開始傳送要求本文。 這可讓用戶端有機會檢查回應，並在傳送不需要的資料之前中止。
