---
title: 取消保護已在 ASP.NET Core 中撤銷金鑰的承載
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 應用程式中，解除保護資料，並以已撤銷的金鑰進行保護。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051092"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="b55ce-103">取消保護已在 ASP.NET Core 中撤銷金鑰的承載</span><span class="sxs-lookup"><span data-stu-id="b55ce-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="b55ce-104">ASP.NET Core 的資料保護 Api 主要是供機密承載的無限持續性所用。</span><span class="sxs-lookup"><span data-stu-id="b55ce-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="b55ce-105">其他像是 [WINDOWS CNG DPAPI](/windows/win32/seccng/cng-dpapi) 和 [Azure Rights Management](/rights-management/) 的技術更適合不限數量的儲存體案例，而且它們也有增強的金鑰管理功能。</span><span class="sxs-lookup"><span data-stu-id="b55ce-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="b55ce-106">話雖如此，開發人員不會禁止開發人員使用 ASP.NET Core 資料保護 Api 來進行機密資料的長期保護。</span><span class="sxs-lookup"><span data-stu-id="b55ce-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="b55ce-107">金鑰絕不會從金鑰環中移除，因此 `IDataProtector.Unprotect` 只要金鑰可供使用且有效，一律可以復原現有的承載。</span><span class="sxs-lookup"><span data-stu-id="b55ce-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="b55ce-108">不過，當開發人員嘗試取消保護以撤銷金鑰保護的資料時，就會發生問題，因為 `IDataProtector.Unprotect` 在此情況下會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b55ce-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="b55ce-109">這可能適用于短期或暫時性承載 (例如驗證權杖) ，因為這類承載可輕易地由系統重新建立，而在最糟的情況下，網站訪客可能需要再次登入。</span><span class="sxs-lookup"><span data-stu-id="b55ce-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="b55ce-110">但是針對保存的承載，具有 `Unprotect` throw 可能會導致無法接受的資料遺失。</span><span class="sxs-lookup"><span data-stu-id="b55ce-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="b55ce-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="b55ce-111">IPersistedDataProtector</span></span>

<span data-ttu-id="b55ce-112">為了支援即使在遭撤銷金鑰的情況下允許承載未受保護的案例，資料保護系統會包含 `IPersistedDataProtector` 類型。</span><span class="sxs-lookup"><span data-stu-id="b55ce-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="b55ce-113">若要取得的實例 `IPersistedDataProtector` ，只要以正常方式取得的實例， `IDataProtector` 並嘗試將轉換 `IDataProtector` 成 `IPersistedDataProtector` 。</span><span class="sxs-lookup"><span data-stu-id="b55ce-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="b55ce-114">並非所有 `IDataProtector` 實例都可以轉換成 `IPersistedDataProtector` 。</span><span class="sxs-lookup"><span data-stu-id="b55ce-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="b55ce-115">開發人員應該使用 c # as 運算子或類似的，以避免因為轉換無效所造成的執行時間例外狀況，而且應該準備好適當地處理失敗的情況。</span><span class="sxs-lookup"><span data-stu-id="b55ce-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="b55ce-116">`IPersistedDataProtector` 公開下列 API 介面：</span><span class="sxs-lookup"><span data-stu-id="b55ce-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="b55ce-117">此 API 會將受保護的承載 (為位元組陣列) 並傳回未受保護的裝載。</span><span class="sxs-lookup"><span data-stu-id="b55ce-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="b55ce-118">沒有以字串為基礎的多載。</span><span class="sxs-lookup"><span data-stu-id="b55ce-118">There's no string-based overload.</span></span> <span data-ttu-id="b55ce-119">這兩個輸出參數如下所示。</span><span class="sxs-lookup"><span data-stu-id="b55ce-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="b55ce-120">`requiresMigration`：如果用來保護此裝載的金鑰不再是使用中的預設金鑰（例如，用來保護此裝載的金鑰是舊的金鑰，且已從執行的金鑰），則會設定為 true。</span><span class="sxs-lookup"><span data-stu-id="b55ce-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="b55ce-121">呼叫端可能會想要根據其商務需求考慮重新保護承載。</span><span class="sxs-lookup"><span data-stu-id="b55ce-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="b55ce-122">`wasRevoked`：如果用來保護此裝載的金鑰已遭撤銷，則會設定為 true。</span><span class="sxs-lookup"><span data-stu-id="b55ce-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="b55ce-123">傳遞至方法時，請特別小心 `ignoreRevocationErrors: true` `DangerousUnprotect` 。</span><span class="sxs-lookup"><span data-stu-id="b55ce-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="b55ce-124">如果在呼叫這個方法之後，此 `wasRevoked` 值為 true，則會撤銷用來保護此承載的金鑰，而且應將承載的真實性視為有疑問。</span><span class="sxs-lookup"><span data-stu-id="b55ce-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="b55ce-125">在這種情況下，如果您有一些不同的保證可在未受信任的情況下運作，請只繼續在未受保護的裝載上操作，例如，它來自安全的資料庫，而不是由不受信任的 web 用戶端傳送。</span><span class="sxs-lookup"><span data-stu-id="b55ce-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
