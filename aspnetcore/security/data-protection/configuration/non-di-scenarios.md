---
title: ASP.NET Core 資料保護的非 DI 感知案例
author: rick-anderson
description: 瞭解如何支援不能或不想使用相依性插入所提供之服務的資料保護案例。
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
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 5b27d21b046333d7a01f2e81f25d7bd34253cf36
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629167"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a><span data-ttu-id="165f0-103">ASP.NET Core 資料保護的非 DI 感知案例</span><span class="sxs-lookup"><span data-stu-id="165f0-103">Non-DI aware scenarios for Data Protection in ASP.NET Core</span></span>

<span data-ttu-id="165f0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="165f0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="165f0-105">ASP.NET Core 的資料保護系統通常會 [新增至服務容器](xref:security/data-protection/consumer-apis/overview) ，並透過相依性插入 (DI) 來供相依元件取用。</span><span class="sxs-lookup"><span data-stu-id="165f0-105">The ASP.NET Core Data Protection system is normally [added to a service container](xref:security/data-protection/consumer-apis/overview) and consumed by dependent components via dependency injection (DI).</span></span> <span data-ttu-id="165f0-106">不過，在某些情況下，這並不可行或不需要，尤其是將系統匯入到現有的應用程式時。</span><span class="sxs-lookup"><span data-stu-id="165f0-106">However, there are cases where this isn't feasible or desired, especially when importing the system into an existing app.</span></span>

<span data-ttu-id="165f0-107">為了支援這些案例， [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 封裝提供具象類型 [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider)，可提供簡單的方式來使用資料保護，而不需要依賴 DI。</span><span class="sxs-lookup"><span data-stu-id="165f0-107">To support these scenarios, the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) package provides a concrete type, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), which offers a simple way to use Data Protection without relying on DI.</span></span> <span data-ttu-id="165f0-108">`DataProtectionProvider`類型會執行[IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider)。</span><span class="sxs-lookup"><span data-stu-id="165f0-108">The `DataProtectionProvider` type implements [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider).</span></span> <span data-ttu-id="165f0-109">您 `DataProtectionProvider` 只需要提供 [DirectoryInfo](/dotnet/api/system.io.directoryinfo) 實例來指出應儲存提供者之密碼編譯金鑰的位置，如下列程式碼範例所示：</span><span class="sxs-lookup"><span data-stu-id="165f0-109">Constructing `DataProtectionProvider` only requires providing a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) instance to indicate where the provider's cryptographic keys should be stored, as seen in the following code sample:</span></span>

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

<span data-ttu-id="165f0-110">依預設， `DataProtectionProvider` 實體類型不會在將原始金鑰內容保存到檔案系統之前加密。</span><span class="sxs-lookup"><span data-stu-id="165f0-110">By default, the `DataProtectionProvider` concrete type doesn't encrypt raw key material before persisting it to the file system.</span></span> <span data-ttu-id="165f0-111">這是為了支援開發人員指向網路共用的案例，而資料保護系統無法自動推算適當的靜止金鑰加密機制。</span><span class="sxs-lookup"><span data-stu-id="165f0-111">This is to support scenarios where the developer points to a network share and the Data Protection system can't automatically deduce an appropriate at-rest key encryption mechanism.</span></span>

<span data-ttu-id="165f0-112">此外， `DataProtectionProvider` 實體類型預設不會 [隔離應用程式](xref:security/data-protection/configuration/overview#per-application-isolation) 。</span><span class="sxs-lookup"><span data-stu-id="165f0-112">Additionally, the `DataProtectionProvider` concrete type doesn't [isolate apps](xref:security/data-protection/configuration/overview#per-application-isolation) by default.</span></span> <span data-ttu-id="165f0-113">使用相同金鑰目錄的所有應用程式可以共用承載，只要其 [用途參數](xref:security/data-protection/consumer-apis/purpose-strings) 相符即可。</span><span class="sxs-lookup"><span data-stu-id="165f0-113">All apps using the same key directory can share payloads as long as their [purpose parameters](xref:security/data-protection/consumer-apis/purpose-strings) match.</span></span>

<span data-ttu-id="165f0-114">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)函式會接受選擇性的設定回呼，可用來調整系統的行為。</span><span class="sxs-lookup"><span data-stu-id="165f0-114">The [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) constructor accepts an optional configuration callback that can be used to adjust the behaviors of the system.</span></span> <span data-ttu-id="165f0-115">下列範例示範如何使用 [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)的明確呼叫來還原隔離。</span><span class="sxs-lookup"><span data-stu-id="165f0-115">The sample below demonstrates restoring isolation with an explicit call to [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname).</span></span> <span data-ttu-id="165f0-116">此範例也會示範如何將系統設定為使用 Windows DPAPI 自動加密保存的金鑰。</span><span class="sxs-lookup"><span data-stu-id="165f0-116">The sample also demonstrates configuring the system to automatically encrypt persisted keys using Windows DPAPI.</span></span> <span data-ttu-id="165f0-117">如果目錄指向 UNC 共用，您可能會想要將共用憑證散發到所有相關的電腦上，並將系統設定為使用以憑證為基礎的加密與 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)的呼叫。</span><span class="sxs-lookup"><span data-stu-id="165f0-117">If the directory points to a UNC share, you may wish to distribute a shared certificate across all relevant machines and to configure the system to use certificate-based encryption with a call to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).</span></span>

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> <span data-ttu-id="165f0-118">實體類型的實例需要 `DataProtectionProvider` 建立很多資源。</span><span class="sxs-lookup"><span data-stu-id="165f0-118">Instances of the `DataProtectionProvider` concrete type are expensive to create.</span></span> <span data-ttu-id="165f0-119">如果應用程式維護多個此類型的實例，而且它們都是使用相同的金鑰儲存體目錄，應用程式效能可能會降低。</span><span class="sxs-lookup"><span data-stu-id="165f0-119">If an app maintains multiple instances of this type and if they're all using the same key storage directory, app performance might degrade.</span></span> <span data-ttu-id="165f0-120">如果您使用 `DataProtectionProvider` 類型，建議您建立此類型一次，並盡可能重複使用它。</span><span class="sxs-lookup"><span data-stu-id="165f0-120">If you use the `DataProtectionProvider` type, we recommend that you create this type once and reuse it as much as possible.</span></span> <span data-ttu-id="165f0-121">`DataProtectionProvider`從它建立的型別和所有[>idataprotector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)實例，都是多個呼叫端的安全線程。</span><span class="sxs-lookup"><span data-stu-id="165f0-121">The `DataProtectionProvider` type and all [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) instances created from it are thread-safe for multiple callers.</span></span>
