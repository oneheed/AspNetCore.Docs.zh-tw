---
title: ASP.NET Core 中的資料保護 Api 入門
author: rick-anderson
description: 瞭解如何使用 ASP.NET Core 資料保護 Api 來保護和取消保護應用程式中的資料。
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051651"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET Core 中的資料保護 Api 入門

<a name="security-data-protection-getting-started"></a>

保護資料最簡單的方式是由下列步驟組成：

1. 從資料保護提供者建立資料保護裝置。

2. `Protect`使用您想要保護的資料來呼叫方法。

3. `Unprotect`使用您想要轉換回純文字的資料來呼叫方法。

大部分的架構和應用程式模型（例如 ASP.NET Core 或 SignalR ）已設定資料保護系統，並將它新增至您透過相依性插入存取的服務容器。 下列範例示範如何設定服務容器以進行相依性插入，以及註冊資料保護堆疊、透過 DI 接收資料保護提供者、建立保護裝置，以及保護取消保護資料。

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

當您建立保護裝置時，您必須提供一或多個 [目的字串](xref:security/data-protection/consumer-apis/purpose-strings)。 目的字串可提供取用者之間的隔離。 例如，使用「綠色」目的字串所建立的保護裝置，將無法解除保護保護裝置所提供的資料，目的為「紫色」。

>[!TIP]
> 和的 `IDataProtectionProvider` 實例 `IDataProtector` 都是多個呼叫端的安全線程。 它的目的是只要元件透過呼叫取得的參考 `IDataProtector` `CreateProtector` ，它就會將該參考用於多個對和的呼叫 `Protect` `Unprotect` 。
>
>`Unprotect`如果無法驗證或解密受保護的承載，的呼叫將會擲回 system.security.cryptography.cryptographicexception。 某些元件可能會想要忽略取消保護作業期間的錯誤;讀取驗證的元件 cookie 可能會處理此錯誤，並將要求視為完全沒有， cookie 而不是直接讓要求失敗。 需要此行為的元件應該明確地捕捉 System.security.cryptography.cryptographicexception，而不是抑制所有例外狀況。
