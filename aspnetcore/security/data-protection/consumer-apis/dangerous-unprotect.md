---
title: 取消保護已在 ASP.NET Core 中撤銷金鑰的承載
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 應用程式中，解除保護資料，並以已撤銷的金鑰進行保護。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: a6ced7341967362ace9537ad0124ccca6fbe79b0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626697"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>取消保護已在 ASP.NET Core 中撤銷金鑰的承載

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

ASP.NET Core 的資料保護 Api 主要是供機密承載的無限持續性所用。 其他像是 [WINDOWS CNG DPAPI](/windows/win32/seccng/cng-dpapi) 和 [Azure Rights Management](/rights-management/) 的技術更適合不限數量的儲存體案例，而且它們也有增強的金鑰管理功能。 話雖如此，開發人員不會禁止開發人員使用 ASP.NET Core 資料保護 Api 來進行機密資料的長期保護。 金鑰絕不會從金鑰環中移除，因此 `IDataProtector.Unprotect` 只要金鑰可供使用且有效，一律可以復原現有的承載。

不過，當開發人員嘗試取消保護以撤銷金鑰保護的資料時，就會發生問題，因為 `IDataProtector.Unprotect` 在此情況下會擲回例外狀況。 這可能適用于短期或暫時性承載 (例如驗證權杖) ，因為這類承載可輕易地由系統重新建立，而在最糟的情況下，網站訪客可能需要再次登入。 但是針對保存的承載，具有 `Unprotect` throw 可能會導致無法接受的資料遺失。

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

為了支援即使在遭撤銷金鑰的情況下允許承載未受保護的案例，資料保護系統會包含 `IPersistedDataProtector` 類型。 若要取得的實例 `IPersistedDataProtector` ，只要以正常方式取得的實例， `IDataProtector` 並嘗試將轉換 `IDataProtector` 成 `IPersistedDataProtector` 。

> [!NOTE]
> 並非所有 `IDataProtector` 實例都可以轉換成 `IPersistedDataProtector` 。 開發人員應該使用 c # as 運算子或類似的，以避免因為轉換無效所造成的執行時間例外狀況，而且應該準備好適當地處理失敗的情況。

`IPersistedDataProtector` 公開下列 API 介面：

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

此 API 會將受保護的承載 (為位元組陣列) 並傳回未受保護的裝載。 沒有以字串為基礎的多載。 這兩個輸出參數如下所示。

* `requiresMigration`：如果用來保護此裝載的金鑰不再是使用中的預設金鑰（例如，用來保護此裝載的金鑰是舊的金鑰，且已從執行的金鑰），則會設定為 true。 呼叫端可能會想要根據其商務需求考慮重新保護承載。

* `wasRevoked`：如果用來保護此裝載的金鑰已遭撤銷，則會設定為 true。

>[!WARNING]
> 傳遞至方法時，請特別小心 `ignoreRevocationErrors: true` `DangerousUnprotect` 。 如果在呼叫這個方法之後，此 `wasRevoked` 值為 true，則會撤銷用來保護此承載的金鑰，而且應將承載的真實性視為有疑問。 在這種情況下，如果您有一些不同的保證可在未受信任的情況下運作，請只繼續在未受保護的裝載上操作，例如，它來自安全的資料庫，而不是由不受信任的 web 用戶端傳送。

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
