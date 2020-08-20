---
title: 其他 ASP.NET Core 資料保護 Api
author: rick-anderson
description: 瞭解 ASP.NET Core Data Protection ISecret 介面。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 947c6eb62550d325492365ece84d45a14845888f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630909"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>其他 ASP.NET Core 資料保護 Api

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 實作為下列任何介面的型別，都應該是多個呼叫端的安全線程。

## <a name="isecret"></a>ISecret

`ISecret`介面代表秘密值，例如密碼編譯金鑰內容。 它包含下列 API 介面：

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer`方法會以原始密碼值填入提供的緩衝區。 此 API 採用緩衝區做為參數，而不是直接傳回，這 `byte[]` 會讓呼叫端有機會釘選緩衝區物件，以限制受管理垃圾收集行程的秘密暴露。

此 `Secret` 類型是將 `ISecret` 秘密值儲存在同進程記憶體中的具體實作為。 在 Windows 平臺上，秘密值會透過 [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)進行加密。
