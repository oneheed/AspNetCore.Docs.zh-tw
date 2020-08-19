---
title: SignalR API 設計考慮
author: anurse
description: 瞭解如何設計 SignalR api，以在您的應用程式版本之間進行相容性。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
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
uid: signalr/api-design
ms.openlocfilehash: 4a838c3a051476bd3d281e133d08b643656ae3b7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632898"
---
# <a name="no-locsignalr-api-design-considerations"></a>SignalR API 設計考慮

[Andrew Stanton-護士](https://twitter.com/anurse)

本文提供建立 SignalR 型 api 的指引。

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>使用自訂物件參數以確保回溯相容性

將參數加入至 SignalR 中樞方法 (用戶端或伺服器上的) 是一項 *重大變更*。 這表示較舊的用戶端/伺服器在嘗試叫用方法時，如果沒有適當的參數數目，就會收到錯誤。 不過，將屬性新增至自訂物件參數 **不** 是中斷性變更。 這可以用來設計可在用戶端或伺服器上復原變更的相容 Api。

例如，請考慮伺服器端 API，如下所示：

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

JavaScript 用戶端會使用來呼叫這個方法 `invoke` ，如下所示：

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

如果您稍後將第二個參數加入至伺服器方法，較舊的用戶端將不會提供此參數值。 例如：

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

當舊用戶端嘗試叫用此方法時，將會收到如下的錯誤：

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

在伺服器上，您會看到如下的記錄訊息：

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

舊的用戶端只會傳送一個參數，但較新的伺服器 API 則需要兩個參數。 使用自訂物件做為參數可提供更大的彈性。 讓我們重新設計原始 API 以使用自訂物件：

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

現在，用戶端會使用物件來呼叫方法：

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

將屬性新增至物件，而不是加入參數 `TotalLengthRequest` ：

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

當舊用戶端傳送單一參數時， `Param2` 會保留額外的屬性 `null` 。 您可以藉由檢查 `Param2` 和套用預設值，來偵測舊版用戶端所傳送的訊息 `null` 。 新的用戶端可以傳送這兩個參數。

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

相同的技術適用于用戶端上定義的方法。 您可以從伺服器端傳送自訂物件：

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

在用戶端上，您會存取 `Message` 屬性，而不是使用參數：

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

如果您稍後決定要將訊息的寄件者新增至承載，請將屬性新增至物件：

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

較舊的用戶端將不會預期 `Sender` 值，所以會忽略它。 新用戶端可以藉由更新讀取新的屬性來接受它：

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

在此情況下，新的用戶端也可容忍未提供此值的舊伺服器 `Sender` 。 由於舊伺服器不會提供此 `Sender` 值，因此用戶端會先檢查是否存在，再進行存取。
