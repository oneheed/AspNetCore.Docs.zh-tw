---
title: ASP.NET Core 中的雜湊密碼
author: rick-anderson
description: 瞭解如何使用 ASP.NET Core 資料保護 Api 來雜湊密碼。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: b88abd076febae95f8990bcd730ce0d4517e92d2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587758"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="9e28b-103">ASP.NET Core 中的雜湊密碼</span><span class="sxs-lookup"><span data-stu-id="9e28b-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="9e28b-104">資料保護程式碼基底包含 *AspNetCore KeyDerivation* ，其中包含密碼編譯金鑰衍生函數。</span><span class="sxs-lookup"><span data-stu-id="9e28b-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="9e28b-105">此套件是獨立元件，與其余的資料保護系統沒有相依性。</span><span class="sxs-lookup"><span data-stu-id="9e28b-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="9e28b-106">可以完全獨立使用。</span><span class="sxs-lookup"><span data-stu-id="9e28b-106">It can be used completely independently.</span></span> <span data-ttu-id="9e28b-107">來源與資料保護程式碼基底並存，方便您使用。</span><span class="sxs-lookup"><span data-stu-id="9e28b-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="9e28b-108">封裝目前提供的方法可 `KeyDerivation.Pbkdf2` 允許使用 [PBKDF2 演算法](https://tools.ietf.org/html/rfc2898#section-5.2)來雜湊密碼。</span><span class="sxs-lookup"><span data-stu-id="9e28b-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="9e28b-109">此 API 與 .NET Framework 的現有 [Rfc2898DeriveBytes 類型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)非常類似，但有三個重要的差異：</span><span class="sxs-lookup"><span data-stu-id="9e28b-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="9e28b-110">`KeyDerivation.Pbkdf2`方法支援取用多個 PRFs (目前 `HMACSHA1` 、 `HMACSHA256` 和 `HMACSHA512`) ，而 `Rfc2898DeriveBytes` 類型只支援 `HMACSHA1` 。</span><span class="sxs-lookup"><span data-stu-id="9e28b-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="9e28b-111">`KeyDerivation.Pbkdf2`方法會偵測目前的作業系統，並嘗試選擇最優化的常式執行，在特定情況下提供更好的效能。</span><span class="sxs-lookup"><span data-stu-id="9e28b-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="9e28b-112"> (在 Windows 8 上，它提供大約10倍的輸送量 `Rfc2898DeriveBytes` 。 ) </span><span class="sxs-lookup"><span data-stu-id="9e28b-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="9e28b-113">`KeyDerivation.Pbkdf2`方法需要呼叫者指定所有參數 (salt、PRF 和反覆運算計數) 。</span><span class="sxs-lookup"><span data-stu-id="9e28b-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="9e28b-114">此 `Rfc2898DeriveBytes` 類型提供這些的預設值。</span><span class="sxs-lookup"><span data-stu-id="9e28b-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="9e28b-115">請參閱類型的 [原始程式碼](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) ， ASP.NET Core Identity `PasswordHasher` 以瞭解真實世界的使用案例。</span><span class="sxs-lookup"><span data-stu-id="9e28b-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
