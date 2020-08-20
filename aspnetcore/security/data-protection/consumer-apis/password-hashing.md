---
title: ASP.NET Core 中的雜湊密碼
author: rick-anderson
description: 瞭解如何使用 ASP.NET Core 資料保護 Api 來雜湊密碼。
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 19263400397a9dfe2d9e6044109d6d063023f6f4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629856"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core 中的雜湊密碼

資料保護程式碼基底包含 *AspNetCore KeyDerivation* ，其中包含密碼編譯金鑰衍生函數。 此套件是獨立元件，與其余的資料保護系統沒有相依性。 可以完全獨立使用。 來源與資料保護程式碼基底並存，方便您使用。

封裝目前提供的方法可 `KeyDerivation.Pbkdf2` 允許使用 [PBKDF2 演算法](https://tools.ietf.org/html/rfc2898#section-5.2)來雜湊密碼。 此 API 與 .NET Framework 的現有 [Rfc2898DeriveBytes 類型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)非常類似，但有三個重要的差異：

1. `KeyDerivation.Pbkdf2`方法支援取用多個 PRFs (目前 `HMACSHA1` 、 `HMACSHA256` 和 `HMACSHA512`) ，而 `Rfc2898DeriveBytes` 類型只支援 `HMACSHA1` 。

2. `KeyDerivation.Pbkdf2`方法會偵測目前的作業系統，並嘗試選擇最優化的常式執行，在特定情況下提供更好的效能。  (在 Windows 8 上，它提供大約10倍的輸送量 `Rfc2898DeriveBytes` 。 ) 

3. `KeyDerivation.Pbkdf2`方法需要呼叫者指定所有參數 (salt、PRF 和反覆運算計數) 。 此 `Rfc2898DeriveBytes` 類型提供這些的預設值。

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

請參閱類型的 [原始程式碼](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) ， ASP.NET Core Identity `PasswordHasher` 以瞭解真實世界的使用案例。
