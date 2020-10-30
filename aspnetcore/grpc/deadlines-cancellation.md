---
title: 具有期限和取消的可靠 gRPC 服務
author: jamesnk
description: 瞭解如何在 .NET 中使用期限和取消來建立可靠的 gRPC 服務。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059919"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>具有期限和取消的可靠 gRPC 服務

依 [James 牛頓](https://twitter.com/jamesnk)

期限和取消是 gRPC 用戶端用來中止進行中呼叫的功能。 本文討論為何期限和取消很重要，以及如何在 .NET gRPC 應用程式中使用它們。

## <a name="deadlines"></a>最後 期限

期限可讓 gRPC 用戶端指定等候完成呼叫所需的時間。 當超過期限時，即會取消通話。 設定期限很重要，因為它會提供呼叫可執行檔時間上限。 它會停止不正常的服務，使其無法執行永久和耗盡的伺服器資源。 期限是一個實用的工具，可用於建立可靠的應用程式，而且應該進行設定。

期限設定：

* 當進行呼叫時，會使用設定期限 `CallOptions.Deadline` 。
* 沒有預設期限值。 除非指定期限，否則 gRPC 呼叫不會有時間限制。
* 期限是超過期限的 UTC 時間。 例如，的 `DateTime.UtcNow.AddSeconds(5)` 期限是從現在開始的5秒。
* 如果使用過去或目前的時間，則呼叫會立即超過期限。
* 期限會透過 gRPC 呼叫傳送至服務，而且用戶端與服務會獨立追蹤此期限。 GRPC 呼叫可能會在一部電腦上完成，但回應傳回用戶端的時間已超過期限。

如果超過期限，用戶端和服務會有不同的行為：

* 用戶端會立即中止基礎 HTTP 要求，並擲回 `DeadlineExceeded` 錯誤。 用戶端應用程式可以選擇攔截錯誤，並向使用者顯示超時訊息。
* 在伺服器上，執行中的 HTTP 要求會中止，而且會引發 [ServerCallCoNtext。 CancellationToken](xref:System.Threading.CancellationToken) 。 雖然 HTTP 要求已中止，gRPC 呼叫仍會繼續在伺服器上執行，直到方法完成為止。 解除標記必須傳遞給非同步方法，如此一來，它們就會與呼叫一起取消。 例如，將解除標記傳遞給非同步資料庫查詢和 HTTP 要求。 傳遞取消權杖可讓取消的呼叫在伺服器上快速完成，並釋出資源供其他呼叫使用。

`CallOptions.Deadline`設定以設定 gRPC 呼叫的期限：

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

`ServerCallContext.CancellationToken`在 gRPC 服務中使用：

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>傳播期限

從執行中的 gRPC 服務進行 gRPC 呼叫時，應會傳播期限。 例如：

1. 具有期限的用戶端應用程式呼叫 `FrontendService.GetUser` 。
2. `FrontendService` 會呼叫 `UserService.GetUser`。 用戶端指定的期限應該使用新的 gRPC 呼叫來指定。
3. `UserService.GetUser` 接收期限。 如果超過用戶端應用程式的期限，則會正確地發生。

呼叫內容提供期限 `ServerCallContext.Deadline` ：

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

手動傳播期限可能很麻煩。 期限必須傳遞至每次呼叫，且很容易不小心遺漏。 GRPC client factory 提供自動解決方案。 指定 `EnableCallContextPropagation` ：

* 自動將期限和取消權杖傳播至子呼叫。
* 是確保複雜的嵌套 gRPC 案例一律會傳播期限和取消的絕佳方法。

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

如需詳細資訊，請參閱<xref:grpc/clientfactory#deadline-and-cancellation-propagation>。

## <a name="cancellation"></a>取消

取消可讓 gRPC 用戶端取消不再需要的長時間執行呼叫。 例如，當使用者造訪網站上的頁面時，會啟動串流即時更新的 gRPC 呼叫。 當使用者流覽離開頁面時，應取消資料流程。

GRPC 呼叫可以在用戶端中取消，方法是傳遞具有 [CallOptions](xref:System.Threading.CancellationToken) 的取消權杖，或 `Dispose` 在呼叫上呼叫。

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

gRPC 可取消的服務應：
* 傳遞 `ServerCallContext.CancellationToken` 至非同步方法。 取消非同步方法可讓伺服器上的呼叫快速完成。
* 將取消權杖傳播至子呼叫。 傳播取消權杖可確保子呼叫會以其父系取消。 [gRPC 用戶端 factory](xref:grpc/clientfactory) ，並 `EnableCallContextPropagation()` 自動傳播取消權杖。

## <a name="additional-resources"></a>其他資源

* <xref:grpc/client>
* <xref:grpc/clientfactory>
