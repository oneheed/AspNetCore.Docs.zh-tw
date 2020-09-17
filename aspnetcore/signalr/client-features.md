---
title: ASP.NET Core SignalR 用戶端
author: bradygaster
description: 瞭解各種 ASP.NET Core 用戶端支援哪些功能 SignalR 。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/client-features
ms.openlocfilehash: bb8352b11e78582c4a32b67da7dcb701d020b0d9
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721706"
---
# <a name="aspnet-core-no-locsignalr-clients"></a>ASP.NET Core SignalR 用戶端

## <a name="versioning-support-and-compatibility"></a>版本控制、支援和相容性

SignalR用戶端隨附于伺服器元件，並已建立版本以符合。 任何支援的用戶端都可以安全地連接到任何支援的伺服器，而任何相容性問題都會被視為要修正的 bug。 SignalR 與 .NET Core 的其餘部分相同的支援週期支援用戶端。 如需詳細資訊，請參閱 [.Net Core 支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) 。

許多功能都需要相容的用戶端 **和** 伺服器。 請參閱下方的表格，其中顯示各種功能的最小版本。

1.x 版的 SignalR 對應至2.1 和 2.2 .Net Core 版本，且存留期相同。 在3.x 版和更新版本中， SignalR 版本完全符合 .net 的其餘部分，且具有相同的支援週期。

| SignalR 版本 | .NET Core 版本 | 支援層級 | 結束支援 |
| - | - | - | - |
| 1.0. x | 2.1.x | 長期支援 | 2021年8月21日 |
| 1.1. x | 2.2. x | 生命週期結束 | 2019年12月23日 |
| 3.x 或更高版本 | *與 SignalR 版本相同* | 請參閱 [.Net Core 支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) |

**注意：** 在 ASP.NET Core 3.0 中，JavaScript 用戶端會 *移* 至 `@microsoft/signalr` npm 套件。

## <a name="feature-distribution"></a>功能散發

下表顯示提供即時支援之用戶端的功能與支援。 針對每項功能，會列出支援這項功能的 *最低* 版本。 如果未列出任何版本，則不支援此功能。

| 功能 | 伺服器 | .NET 用戶端 | JavaScript 用戶端 | Java 用戶端 |
| ---- | :-: | :-: | :-: | :-: |
| Azure SignalR 服務支援 |2.1.0|1.0.0|1.0.0|1.0.0|
| [伺服器對用戶端串流](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [用戶端對伺服器串流](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| 自動重新連接 ([.net](./dotnet-client.md?tabs=visual-studio&view=aspnetcore-3.0#handle-lost-connection)、 [JavaScript](./javascript-client.md?view=aspnetcore-3.0#reconnect-clients))           |3.0.0|3.0.0|3.0.0|❌|
| Websocket 傳輸 |2.1.0|1.0.0|1.0.0|1.0.0|
| 伺服器傳送的事件傳輸 |2.1.0|1.0.0|1.0.0|❌|
| 長時間輪詢傳輸 |2.1.0|1.0.0|1.0.0|3.0.0|
| JSON 中樞通訊協定 |2.1.0|1.0.0|1.0.0|1.0.0|
| MessagePack 中樞通訊協定 |2.1.0|1.0.0|1.0.0|❌|

[我們的問題追蹤](https://github.com/dotnet/AspNetCore/issues)程式會追蹤啟用其他用戶端功能的支援。

## <a name="additional-resources"></a>其他資源

* [開始使用 SignalR ASP.NET Core](xref:tutorials/signalr)
* [支援的平台](xref:signalr/supported-platforms)
* [中樞](xref:signalr/hubs)
* [JavaScript 用戶端](xref:signalr/javascript-client)