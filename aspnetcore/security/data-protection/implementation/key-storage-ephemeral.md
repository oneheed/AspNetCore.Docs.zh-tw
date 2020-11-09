---
title: ASP.NET Core 中的暫時資料保護提供者
author: rick-anderson
description: 瞭解 ASP.NET Core 暫時資料保護提供者的執行詳細資料。
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052694"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="42eac-103">ASP.NET Core 中的暫時資料保護提供者</span><span class="sxs-lookup"><span data-stu-id="42eac-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="42eac-104">在某些情況下，應用程式需要完即丟 `IDataProtectionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="42eac-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="42eac-105">例如，開發人員可能只是在一次性的主控台應用程式中進行實驗，或應用程式本身是暫時性的 (它的腳本或單元測試專案) 。</span><span class="sxs-lookup"><span data-stu-id="42eac-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="42eac-106">為了支援這些案例， [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) 套件包含類型 `EphemeralDataProtectionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="42eac-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="42eac-107">此類型提供基本的執行， `IDataProtectionProvider` 其金鑰存放庫只保留在記憶體中，且不會寫出至任何備份存放區。</span><span class="sxs-lookup"><span data-stu-id="42eac-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="42eac-108">的每個實例都會 `EphemeralDataProtectionProvider` 使用自己唯一的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="42eac-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="42eac-109">因此，如果 `IDataProtector` 根目錄為的 `EphemeralDataProtectionProvider` 會產生受保護的承載，則只有在相同的 `IDataProtector` [目的](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) 鏈) 根目錄于相同的實例時，該承載才會被同等的 (保護 `EphemeralDataProtectionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="42eac-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="42eac-110">下列範例會示範如何具現化 `EphemeralDataProtectionProvider` ，並使用它來保護和解除保護資料。</span><span class="sxs-lookup"><span data-stu-id="42eac-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
