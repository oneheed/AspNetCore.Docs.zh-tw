---
title: ASP.NET Core 的取用者 Api 總覽
author: rick-anderson
description: 取得 ASP.NET Core 資料保護程式庫中可用的各種取用者 Api 的簡短總覽。
ms.author: riande
ms.date: 06/11/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 485ea3f669b518f2979d04493b281bd116b05f65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051872"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a><span data-ttu-id="b43a7-103">ASP.NET Core 的取用者 Api 總覽</span><span class="sxs-lookup"><span data-stu-id="b43a7-103">Consumer APIs overview for ASP.NET Core</span></span>

<span data-ttu-id="b43a7-104">`IDataProtectionProvider`和 `IDataProtector` 介面是取用者使用資料保護系統的基本介面。</span><span class="sxs-lookup"><span data-stu-id="b43a7-104">The `IDataProtectionProvider` and `IDataProtector` interfaces are the basic interfaces through which consumers use the data protection system.</span></span> <span data-ttu-id="b43a7-105">它們位於 [AspNetCore. DataProtection 抽象](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 封裝中。</span><span class="sxs-lookup"><span data-stu-id="b43a7-105">They're located in the [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) package.</span></span>

## <a name="idataprotectionprovider"></a><span data-ttu-id="b43a7-106">IDataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="b43a7-106">IDataProtectionProvider</span></span>

<span data-ttu-id="b43a7-107">提供者介面代表資料保護系統的根。</span><span class="sxs-lookup"><span data-stu-id="b43a7-107">The provider interface represents the root of the data protection system.</span></span> <span data-ttu-id="b43a7-108">它無法直接用來保護或解除保護資料。</span><span class="sxs-lookup"><span data-stu-id="b43a7-108">It cannot directly be used to protect or unprotect data.</span></span> <span data-ttu-id="b43a7-109">相反地，取用者必須藉由呼叫來取得的參考 `IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)` ，其中目的是描述預期取用者使用案例的字串。</span><span class="sxs-lookup"><span data-stu-id="b43a7-109">Instead, the consumer must get a reference to an `IDataProtector` by calling `IDataProtectionProvider.CreateProtector(purpose)`, where purpose is a string that describes the intended consumer use case.</span></span> <span data-ttu-id="b43a7-110">如需此參數的意圖以及如何選擇適當值的詳細資訊，請參閱 [目的字串](xref:security/data-protection/consumer-apis/purpose-strings) 。</span><span class="sxs-lookup"><span data-stu-id="b43a7-110">See [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings) for much more information on the intent of this parameter and how to choose an appropriate value.</span></span>

## <a name="idataprotector"></a><span data-ttu-id="b43a7-111">>idataprotector</span><span class="sxs-lookup"><span data-stu-id="b43a7-111">IDataProtector</span></span>

<span data-ttu-id="b43a7-112">的呼叫會傳回保護裝置介面，這是取用 `CreateProtector` 者可用來執行保護和取消保護作業的介面。</span><span class="sxs-lookup"><span data-stu-id="b43a7-112">The protector interface is returned by a call to `CreateProtector`, and it's this interface which consumers can use to perform protect and unprotect operations.</span></span>

<span data-ttu-id="b43a7-113">若要保護資料片段，請將資料傳遞給 `Protect` 方法。</span><span class="sxs-lookup"><span data-stu-id="b43a7-113">To protect a piece of data, pass the data to the `Protect` method.</span></span> <span data-ttu-id="b43a7-114">基本介面會定義轉換 byte []-> byte [] 的方法，但也有多載 (提供作為擴充方法，) 可轉換字串 > 字串。</span><span class="sxs-lookup"><span data-stu-id="b43a7-114">The basic interface defines a method which converts byte[] -> byte[], but there's also an overload (provided as an extension method) which converts string -> string.</span></span> <span data-ttu-id="b43a7-115">這兩個方法所提供的安全性完全相同;開發人員應該選擇哪一種多載最方便使用。</span><span class="sxs-lookup"><span data-stu-id="b43a7-115">The security offered by the two methods is identical; the developer should choose whichever overload is most convenient for their use case.</span></span> <span data-ttu-id="b43a7-116">不論選擇的多載，protected 方法所傳回的值現在都會受到保護， (enciphered 和校訂) ，而且應用程式可以將它傳送至不受信任的用戶端。</span><span class="sxs-lookup"><span data-stu-id="b43a7-116">Irrespective of the overload chosen, the value returned by the Protect method is now protected (enciphered and tamper-proofed), and the application can send it to an untrusted client.</span></span>

<span data-ttu-id="b43a7-117">若要取消保護先前保護的資料片段，請將受保護的資料傳遞給 `Unprotect` 方法。</span><span class="sxs-lookup"><span data-stu-id="b43a7-117">To unprotect a previously-protected piece of data, pass the protected data to the `Unprotect` method.</span></span> <span data-ttu-id="b43a7-118"> (有以 byte [] 為基礎和以字串為基礎的多載，可供開發人員使用。 ) 如果受保護的裝載是由先前的呼叫所產生 `Protect` `IDataProtector` ，則 `Unprotect` 方法會傳回原始未受保護的裝載。</span><span class="sxs-lookup"><span data-stu-id="b43a7-118">(There are byte[]-based and string-based overloads for developer convenience.) If the protected payload was generated by an earlier call to `Protect` on this same `IDataProtector`, the `Unprotect` method will return the original unprotected payload.</span></span> <span data-ttu-id="b43a7-119">如果受保護的承載已遭篡改或由不同的產生 `IDataProtector` ，則 `Unprotect` 方法會擲回 system.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="b43a7-119">If the protected payload has been tampered with or was produced by a different `IDataProtector`, the `Unprotect` method will throw CryptographicException.</span></span>

<span data-ttu-id="b43a7-120">相同與不同的概念會與 `IDataProtector` 目的概念相同。</span><span class="sxs-lookup"><span data-stu-id="b43a7-120">The concept of same vs. different `IDataProtector` ties back to the concept of purpose.</span></span> <span data-ttu-id="b43a7-121">如果 `IDataProtector` 從相同的根產生兩個實例 `IDataProtectionProvider` ，但在呼叫中透過不同的目的字串來產生 `IDataProtectionProvider.CreateProtector` ，則會將它們視為 [不同的保護](xref:security/data-protection/consumer-apis/purpose-strings)裝置，而且無法取消保護另一個所產生的承載。</span><span class="sxs-lookup"><span data-stu-id="b43a7-121">If two `IDataProtector` instances were generated from the same root `IDataProtectionProvider` but via different purpose strings in the call to `IDataProtectionProvider.CreateProtector`, then they're considered [different protectors](xref:security/data-protection/consumer-apis/purpose-strings), and one won't be able to unprotect payloads generated by the other.</span></span>

## <a name="consuming-these-interfaces"></a><span data-ttu-id="b43a7-122">使用這些介面</span><span class="sxs-lookup"><span data-stu-id="b43a7-122">Consuming these interfaces</span></span>

<span data-ttu-id="b43a7-123">針對 DI 感知的元件，預期的用法是元件 `IDataProtectionProvider` 在其函式中採用參數，而 DI 系統會在元件具現化時，自動提供此服務。</span><span class="sxs-lookup"><span data-stu-id="b43a7-123">For a DI-aware component, the intended usage is that the component takes an `IDataProtectionProvider` parameter in its constructor and that the DI system automatically provides this service when the component is instantiated.</span></span>

> [!NOTE]
> <span data-ttu-id="b43a7-124">某些應用程式 (例如主控台應用程式或 ASP.NET 4.x 應用程式) 可能不是 DI 感知，所以無法使用此處所述的機制。</span><span class="sxs-lookup"><span data-stu-id="b43a7-124">Some applications (such as console applications or ASP.NET 4.x applications) might not be DI-aware so cannot use the mechanism described here.</span></span> <span data-ttu-id="b43a7-125">針對這些案例，請參閱 [非 Di 感知案例](xref:security/data-protection/configuration/non-di-scenarios) 檔，以取得取得提供者實例的詳細資訊， `IDataProtection` 而不需透過 DI。</span><span class="sxs-lookup"><span data-stu-id="b43a7-125">For these scenarios consult the [Non DI Aware Scenarios](xref:security/data-protection/configuration/non-di-scenarios) document for more information on getting an instance of an `IDataProtection` provider without going through DI.</span></span>

<span data-ttu-id="b43a7-126">下列範例示範三個概念：</span><span class="sxs-lookup"><span data-stu-id="b43a7-126">The following sample demonstrates three concepts:</span></span>

1. <span data-ttu-id="b43a7-127">[將資料保護系統新增](xref:security/data-protection/configuration/overview) 至服務容器，</span><span class="sxs-lookup"><span data-stu-id="b43a7-127">[Add the data protection system](xref:security/data-protection/configuration/overview) to the service container,</span></span>

2. <span data-ttu-id="b43a7-128">使用 DI 接收的實例 `IDataProtectionProvider` 、和</span><span class="sxs-lookup"><span data-stu-id="b43a7-128">Using DI to receive an instance of an `IDataProtectionProvider`, and</span></span>

3. <span data-ttu-id="b43a7-129">從建立 `IDataProtector` `IDataProtectionProvider` ，然後使用它來保護和解除保護資料。</span><span class="sxs-lookup"><span data-stu-id="b43a7-129">Creating an `IDataProtector` from an `IDataProtectionProvider` and using it to protect and unprotect data.</span></span>

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="b43a7-130">AspNetCore 的封裝包含擴充方法 `IServiceProvider.GetDataProtector` ，可做為開發人員的便利性。</span><span class="sxs-lookup"><span data-stu-id="b43a7-130">The package Microsoft.AspNetCore.DataProtection.Abstractions contains an extension method `IServiceProvider.GetDataProtector` as a developer convenience.</span></span> <span data-ttu-id="b43a7-131">它會封裝成單一作業 `IDataProtectionProvider` ，從服務提供者抓取，然後呼叫 `IDataProtectionProvider.CreateProtector` 。</span><span class="sxs-lookup"><span data-stu-id="b43a7-131">It encapsulates as a single operation both retrieving an `IDataProtectionProvider` from the service provider and calling `IDataProtectionProvider.CreateProtector`.</span></span> <span data-ttu-id="b43a7-132">下列範例會示範其使用方式。</span><span class="sxs-lookup"><span data-stu-id="b43a7-132">The following sample demonstrates its usage.</span></span>

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> <span data-ttu-id="b43a7-133">和的 `IDataProtectionProvider` 實例 `IDataProtector` 都是多個呼叫端的安全線程。</span><span class="sxs-lookup"><span data-stu-id="b43a7-133">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="b43a7-134">它的目的是只要元件透過呼叫取得的參考 `IDataProtector` `CreateProtector` ，它就會將該參考用於多個對和的呼叫 `Protect` `Unprotect` 。</span><span class="sxs-lookup"><span data-stu-id="b43a7-134">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span> <span data-ttu-id="b43a7-135">`Unprotect`如果無法驗證或解密受保護的承載，的呼叫將會擲回 system.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="b43a7-135">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="b43a7-136">某些元件可能會想要忽略取消保護作業期間的錯誤;讀取驗證的元件 :::no-loc(cookie)::: 可能會處理此錯誤，並將要求視為完全沒有， :::no-loc(cookie)::: 而不是直接讓要求失敗。</span><span class="sxs-lookup"><span data-stu-id="b43a7-136">Some components may wish to ignore errors during unprotect operations; a component which reads authentication :::no-loc(cookie):::s might handle this error and treat the request as if it had no :::no-loc(cookie)::: at all rather than fail the request outright.</span></span> <span data-ttu-id="b43a7-137">需要此行為的元件應該明確地捕捉 System.security.cryptography.cryptographicexception，而不是抑制所有例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b43a7-137">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
