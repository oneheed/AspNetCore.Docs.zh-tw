---
title: 其他 ASP.NET Core 資料保護 Api
author: rick-anderson
description: 瞭解 ASP.NET Core Data Protection ISecret 介面。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: bd772b11300cca8896ed512da1cd12c49e6f104b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060751"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="e78d5-103">其他 ASP.NET Core 資料保護 Api</span><span class="sxs-lookup"><span data-stu-id="e78d5-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="e78d5-104">實作為下列任何介面的型別，都應該是多個呼叫端的安全線程。</span><span class="sxs-lookup"><span data-stu-id="e78d5-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="e78d5-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="e78d5-105">ISecret</span></span>

<span data-ttu-id="e78d5-106">`ISecret`介面代表秘密值，例如密碼編譯金鑰內容。</span><span class="sxs-lookup"><span data-stu-id="e78d5-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="e78d5-107">它包含下列 API 介面：</span><span class="sxs-lookup"><span data-stu-id="e78d5-107">It contains the following API surface:</span></span>

* <span data-ttu-id="e78d5-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="e78d5-108">`Length`: `int`</span></span>

* <span data-ttu-id="e78d5-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="e78d5-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="e78d5-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="e78d5-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="e78d5-111">`WriteSecretIntoBuffer`方法會以原始密碼值填入提供的緩衝區。</span><span class="sxs-lookup"><span data-stu-id="e78d5-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="e78d5-112">此 API 採用緩衝區做為參數，而不是直接傳回，這 `byte[]` 會讓呼叫端有機會釘選緩衝區物件，以限制受管理垃圾收集行程的秘密暴露。</span><span class="sxs-lookup"><span data-stu-id="e78d5-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="e78d5-113">此 `Secret` 類型是將 `ISecret` 秘密值儲存在同進程記憶體中的具體實作為。</span><span class="sxs-lookup"><span data-stu-id="e78d5-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="e78d5-114">在 Windows 平臺上，秘密值會透過 [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)進行加密。</span><span class="sxs-lookup"><span data-stu-id="e78d5-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
