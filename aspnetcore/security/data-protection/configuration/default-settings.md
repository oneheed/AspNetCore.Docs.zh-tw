---
title: ASP.NET Core 中的資料保護金鑰管理和存留期
author: rick-anderson
description: 瞭解 ASP.NET Core 中的資料保護金鑰管理和存留期。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 1303c5c2c993f1d20383457666aebfa2a583e938
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053003"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a><span data-ttu-id="18739-103">ASP.NET Core 中的資料保護金鑰管理和存留期</span><span class="sxs-lookup"><span data-stu-id="18739-103">Data Protection key management and lifetime in ASP.NET Core</span></span>

<span data-ttu-id="18739-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18739-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="key-management"></a><span data-ttu-id="18739-105">金鑰管理</span><span class="sxs-lookup"><span data-stu-id="18739-105">Key management</span></span>

<span data-ttu-id="18739-106">應用程式會嘗試偵測其操作環境，並自行處理金鑰設定。</span><span class="sxs-lookup"><span data-stu-id="18739-106">The app attempts to detect its operational environment and handle key configuration on its own.</span></span>

1. <span data-ttu-id="18739-107">如果應用程式裝載在 [Azure 應用程式](https://azure.microsoft.com/services/app-service/)中，金鑰會保存至 *%HOME%\ASP.NET\DataProtection-Keys* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="18739-107">If the app is hosted in [Azure Apps](https://azure.microsoft.com/services/app-service/), keys are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="18739-108">此資料夾使用網路儲存體進行保存，並會在裝載應用程式的所有電腦上同步。</span><span class="sxs-lookup"><span data-stu-id="18739-108">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span>
   * <span data-ttu-id="18739-109">金鑰待用時不受保護。</span><span class="sxs-lookup"><span data-stu-id="18739-109">Keys aren't protected at rest.</span></span>
   * <span data-ttu-id="18739-110">*DataProtection 金鑰* 資料夾會將金鑰通道提供給單一部署位置中應用程式的所有實例。</span><span class="sxs-lookup"><span data-stu-id="18739-110">The *DataProtection-Keys* folder supplies the key ring to all instances of an app in a single deployment slot.</span></span>
   * <span data-ttu-id="18739-111">各部署位置，例如預備和生產位置，不會共用金鑰環。</span><span class="sxs-lookup"><span data-stu-id="18739-111">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span> <span data-ttu-id="18739-112">當您在部署位置（例如將預備環境移至生產環境或使用 A/B 測試）之間交換時，任何使用資料保護的應用程式將無法使用前一個插槽內的金鑰環來解密儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="18739-112">When you swap between deployment slots, for example swapping Staging to Production or using A/B testing, any app using Data Protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="18739-113">這會導致使用者登出使用標準 ASP.NET Core 驗證的應用程式 :::no-loc(cookie)::: ，因為它使用資料保護來保護它的 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="18739-113">This leads to users being logged out of an app that uses the standard ASP.NET Core :::no-loc(cookie)::: authentication, as it uses Data Protection to protect its :::no-loc(cookie):::s.</span></span> <span data-ttu-id="18739-114">如果您想要與位置無關的按鍵環形，請使用外部金鑰環形提供者，例如 Azure Blob 儲存體、Azure Key Vault、SQL 存放區或 Redis 快取。</span><span class="sxs-lookup"><span data-stu-id="18739-114">If you desire slot-independent key rings, use an external key ring provider, such as Azure Blob Storage, Azure Key Vault, a SQL store, or Redis cache.</span></span>

1. <span data-ttu-id="18739-115">如果有可用的使用者設定檔，則會將金鑰保存到 *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="18739-115">If the user profile is available, keys are persisted to the *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="18739-116">如果作業系統是 Windows，則會使用 DPAPI 將金鑰加密。</span><span class="sxs-lookup"><span data-stu-id="18739-116">If the operating system is Windows, the keys are encrypted at rest using DPAPI.</span></span>

   <span data-ttu-id="18739-117">應用程式集區的 [setProfileEnvironment 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)也必須啟用。</span><span class="sxs-lookup"><span data-stu-id="18739-117">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="18739-118">`setProfileEnvironment` 的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="18739-118">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="18739-119">在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="18739-119">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="18739-120">如果金鑰並未如預期地儲存在使用者設定檔目錄中：</span><span class="sxs-lookup"><span data-stu-id="18739-120">If keys aren't stored in the user profile directory as expected:</span></span>

   1. <span data-ttu-id="18739-121">瀏覽至 *%windir%/system32/inetsrv/config* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="18739-121">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
   1. <span data-ttu-id="18739-122">開啟 *applicationHost.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="18739-122">Open the *applicationHost.config* file.</span></span>
   1. <span data-ttu-id="18739-123">找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="18739-123">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
   1. <span data-ttu-id="18739-124">確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="18739-124">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

1. <span data-ttu-id="18739-125">如果應用程式是裝載在 IIS 中，則會將金鑰保存在僅碰到至背景工作進程帳戶的特殊登錄機碼中的 HKLM 登錄中。</span><span class="sxs-lookup"><span data-stu-id="18739-125">If the app is hosted in IIS, keys are persisted to the HKLM registry in a special registry key that's ACLed only to the worker process account.</span></span> <span data-ttu-id="18739-126">在待用期間使用 DPAPI 加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="18739-126">Keys are encrypted at rest using DPAPI.</span></span>

1. <span data-ttu-id="18739-127">如果上述條件都不相符，則索引鍵不會在目前的進程之外保存。</span><span class="sxs-lookup"><span data-stu-id="18739-127">If none of these conditions match, keys aren't persisted outside of the current process.</span></span> <span data-ttu-id="18739-128">當進程關機時，所有產生的金鑰都會遺失。</span><span class="sxs-lookup"><span data-stu-id="18739-128">When the process shuts down, all generated keys are lost.</span></span>

<span data-ttu-id="18739-129">開發人員一律具有完整控制權，而且可以覆寫索引鍵的儲存方式和位置。</span><span class="sxs-lookup"><span data-stu-id="18739-129">The developer is always in full control and can override how and where keys are stored.</span></span> <span data-ttu-id="18739-130">上述前三個選項應該針對大部分的應用程式提供良好的預設值，類似于 ASP.NET **\<machineKey>** 自動產生常式過去的運作方式。</span><span class="sxs-lookup"><span data-stu-id="18739-130">The first three options above should provide good defaults for most apps similar to how the ASP.NET **\<machineKey>** auto-generation routines worked in the past.</span></span> <span data-ttu-id="18739-131">最終的、回溯選項是唯一的案例，如果開發人員想要進行金鑰保存，則需要預先 [指定設定，](xref:security/data-protection/configuration/overview) 但此回退只會在罕見的情況下發生。</span><span class="sxs-lookup"><span data-stu-id="18739-131">The final, fallback option is the only scenario that requires the developer to specify [configuration](xref:security/data-protection/configuration/overview) upfront if they want key persistence, but this fallback only occurs in rare situations.</span></span>

<span data-ttu-id="18739-132">在 Docker 容器中裝載時，金鑰應保存在屬於 Docker 磁片區的資料夾中 (共用磁片區或裝載在容器存留期之外的主機掛接磁片區) 或外部提供者（例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 或 [Redis](https://redis.io/)）。</span><span class="sxs-lookup"><span data-stu-id="18739-132">When hosting in a Docker container, keys should be persisted in a folder that's a Docker volume (a shared volume or a host-mounted volume that persists beyond the container's lifetime) or in an external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span> <span data-ttu-id="18739-133">如果應用程式無法存取共用網路磁片區，則外部提供者也適用于 web 伺服陣列案例 (如需詳細資訊) ，請參閱 [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) 。</span><span class="sxs-lookup"><span data-stu-id="18739-133">An external provider is also useful in web farm scenarios if apps can't access a shared network volume (see [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) for more information).</span></span>

> [!WARNING]
> <span data-ttu-id="18739-134">如果開發人員覆寫上面所述的規則，並將資料保護系統指向特定的金鑰存放庫，則會停用待用金鑰的自動加密。</span><span class="sxs-lookup"><span data-stu-id="18739-134">If the developer overrides the rules outlined above and points the Data Protection system at a specific key repository, automatic encryption of keys at rest is disabled.</span></span> <span data-ttu-id="18739-135">您可以透過設定來重新啟用靜止[保護。](xref:security/data-protection/configuration/overview)</span><span class="sxs-lookup"><span data-stu-id="18739-135">At-rest protection can be re-enabled via [configuration](xref:security/data-protection/configuration/overview).</span></span>

## <a name="key-lifetime"></a><span data-ttu-id="18739-136">金鑰存留期</span><span class="sxs-lookup"><span data-stu-id="18739-136">Key lifetime</span></span>

<span data-ttu-id="18739-137">依預設，索引鍵的存留期為90天。</span><span class="sxs-lookup"><span data-stu-id="18739-137">Keys have a 90-day lifetime by default.</span></span> <span data-ttu-id="18739-138">當金鑰過期時，應用程式會自動產生新的金鑰，並將新的金鑰設定為使用中的金鑰。</span><span class="sxs-lookup"><span data-stu-id="18739-138">When a key expires, the app automatically generates a new key and sets the new key as the active key.</span></span> <span data-ttu-id="18739-139">只要淘汰的金鑰保留在系統上，您的應用程式就可以解密任何受保護的資料。</span><span class="sxs-lookup"><span data-stu-id="18739-139">As long as retired keys remain on the system, your app can decrypt any data protected with them.</span></span> <span data-ttu-id="18739-140">如需詳細資訊，請參閱 [金鑰管理](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) 。</span><span class="sxs-lookup"><span data-stu-id="18739-140">See [key management](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) for more information.</span></span>

## <a name="default-algorithms"></a><span data-ttu-id="18739-141">預設演算法</span><span class="sxs-lookup"><span data-stu-id="18739-141">Default algorithms</span></span>

<span data-ttu-id="18739-142">使用的預設承載保護演算法是 AES-256-CBC，以提供機密性和 HMACSHA256 的真實性。</span><span class="sxs-lookup"><span data-stu-id="18739-142">The default payload protection algorithm used is AES-256-CBC for confidentiality and HMACSHA256 for authenticity.</span></span> <span data-ttu-id="18739-143">512位主要金鑰（每隔90天變更一次）是用來根據每個承載來衍生用於這些演算法的兩個子機碼。</span><span class="sxs-lookup"><span data-stu-id="18739-143">A 512-bit master key, changed every 90 days, is used to derive the two sub-keys used for these algorithms on a per-payload basis.</span></span> <span data-ttu-id="18739-144">如需詳細資訊，請參閱子機碼 [衍生](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) 。</span><span class="sxs-lookup"><span data-stu-id="18739-144">See [subkey derivation](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18739-145">其他資源</span><span class="sxs-lookup"><span data-stu-id="18739-145">Additional resources</span></span>

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
