---
title: 其他 ASP.NET Core 資料保護 Api
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護 ISecret 介面。
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
ms.openlocfilehash: 5ab8226779fb4209a7254b95eccac4be2d26b10d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019036"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="ff22a-103">其他 ASP.NET Core 資料保護 Api</span><span class="sxs-lookup"><span data-stu-id="ff22a-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="ff22a-104">實作為下列任何介面的型別應該是多個呼叫端的安全線程。</span><span class="sxs-lookup"><span data-stu-id="ff22a-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="ff22a-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="ff22a-105">ISecret</span></span>

<span data-ttu-id="ff22a-106">`ISecret`介面代表秘密值，例如密碼編譯金鑰內容。</span><span class="sxs-lookup"><span data-stu-id="ff22a-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="ff22a-107">其中包含下列 API 介面：</span><span class="sxs-lookup"><span data-stu-id="ff22a-107">It contains the following API surface:</span></span>

* <span data-ttu-id="ff22a-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="ff22a-108">`Length`: `int`</span></span>

* <span data-ttu-id="ff22a-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="ff22a-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="ff22a-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="ff22a-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="ff22a-111">方法會將 `WriteSecretIntoBuffer` 原始秘密值填入提供的緩衝區。</span><span class="sxs-lookup"><span data-stu-id="ff22a-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="ff22a-112">此 API 將緩衝區當做參數使用，而不是直接傳回的原因 `byte[]` 是，這讓呼叫者有機會釘選緩衝區物件，以限制受管理垃圾收集行程的秘密暴露。</span><span class="sxs-lookup"><span data-stu-id="ff22a-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="ff22a-113">`Secret`型別是的具體執行， `ISecret` 其中的秘密值會儲存在同進程記憶體中。</span><span class="sxs-lookup"><span data-stu-id="ff22a-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="ff22a-114">在 Windows 平臺上，密碼值是透過[CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)加密。</span><span class="sxs-lookup"><span data-stu-id="ff22a-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
