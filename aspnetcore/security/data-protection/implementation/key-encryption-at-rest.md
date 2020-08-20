---
title: Windows 和 Azure 中使用 ASP.NET Core 的待用金鑰加密
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護金鑰加密的執行詳細資料。
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 4ca2d998141639406a8283c4c756c05a93251928
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633676"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a><span data-ttu-id="6bcf9-103">Windows 和 Azure 中使用 ASP.NET Core 的待用金鑰加密</span><span class="sxs-lookup"><span data-stu-id="6bcf9-103">Key encryption at rest in Windows and Azure using ASP.NET Core</span></span>

<span data-ttu-id="6bcf9-104">根據預設，資料保護系統會 [採用探索機制](xref:security/data-protection/configuration/default-settings) 來判斷密碼編譯金鑰如何加密。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="6bcf9-105">開發人員可以覆寫探索機制，並手動指定如何加密待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="6bcf9-106">如果您指定明確的 [金鑰持續性位置](xref:security/data-protection/implementation/key-storage-providers)，資料保護系統會取消註冊靜態的預設金鑰加密機制。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="6bcf9-107">因此，待用的金鑰不會再加密。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="6bcf9-108">建議您在生產環境部署中 [指定明確的金鑰加密機制](xref:security/data-protection/implementation/key-encryption-at-rest) 。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="6bcf9-109">本主題將說明靜態加密機制的選項。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="6bcf9-110">Azure 金鑰保存庫</span><span class="sxs-lookup"><span data-stu-id="6bcf9-110">Azure Key Vault</span></span>

<span data-ttu-id="6bcf9-111">若要將金鑰儲存在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)中，請在類別中使用 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 來設定系統 `Startup` ：</span><span class="sxs-lookup"><span data-stu-id="6bcf9-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="6bcf9-112">如需詳細資訊，請參閱 [設定 ASP.NET Core 資料保護： ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="6bcf9-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="6bcf9-113">Windows DPAPI</span></span>

<span data-ttu-id="6bcf9-114">**僅適用于 Windows 部署。**</span><span class="sxs-lookup"><span data-stu-id="6bcf9-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="6bcf9-115">使用 Windows DPAPI 時，會先使用 [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) 加密金鑰內容，再將其保存到儲存體。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="6bcf9-116">DPAPI 是一種適當的加密機制，適用于絕對不會在目前的電腦上讀取的資料 (但您可以將這些金鑰備份至 Active Directory;請參閱 [DPAPI 和漫遊設定檔](https://support.microsoft.com/kb/309408/#6)) 。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="6bcf9-117">若要設定 DPAPI 靜態金鑰加密，請呼叫其中一個 [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="6bcf9-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="6bcf9-118">如果 `ProtectKeysWithDpapi` 呼叫時沒有參數，則只有目前的 Windows 使用者帳戶可以解密保存的金鑰環。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="6bcf9-119">您可以選擇性地指定電腦上的任何使用者帳戶 (不只是目前的使用者帳戶，) 能夠解密金鑰環：</span><span class="sxs-lookup"><span data-stu-id="6bcf9-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="6bcf9-120">X.509 憑證</span><span class="sxs-lookup"><span data-stu-id="6bcf9-120">X.509 certificate</span></span>

<span data-ttu-id="6bcf9-121">如果應用程式分散在多部電腦上，將共用的 x.509 憑證散發到電腦上，並將託管的應用程式設定為使用憑證來加密待用金鑰，可能會比較方便：</span><span class="sxs-lookup"><span data-stu-id="6bcf9-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="6bcf9-122">由於 .NET Framework 的限制，因此只支援具有 CAPI 私密金鑰的憑證。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="6bcf9-123">請參閱以下內容，以瞭解這些限制的可能因應措施。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="6bcf9-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="6bcf9-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="6bcf9-125">**這項機制只適用于 Windows 8/Windows Server 2012 或更新版本。**</span><span class="sxs-lookup"><span data-stu-id="6bcf9-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="6bcf9-126">從 Windows 8 開始，Windows OS 支援 DPAPI-NG (也稱為 CNG DPAPI) 。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="6bcf9-127">如需詳細資訊，請參閱 [關於 CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi)。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="6bcf9-128">主體會編碼為保護描述項規則。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="6bcf9-129">在呼叫 [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)的下列範例中，只有加入網域的使用者具有指定的 SID 才能解密金鑰環：</span><span class="sxs-lookup"><span data-stu-id="6bcf9-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="6bcf9-130">也有的無參數多載 `ProtectKeysWithDpapiNG` 。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="6bcf9-131">使用此便利方法來指定規則 "SID = {CURRENT_ACCOUNT_SID}"，其中 *CURRENT_ACCOUNT_SID* 是目前 Windows 使用者帳戶的 SID：</span><span class="sxs-lookup"><span data-stu-id="6bcf9-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="6bcf9-132">在此案例中，AD 網域控制站負責散發 DPAPI-NG 作業所使用的加密金鑰。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="6bcf9-133">目標使用者可以從任何已加入網域的電腦解密已加密的承載 (前提是該進程正在其身分識別) 下執行。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="6bcf9-134">以憑證為基礎的 Windows DPAPI 加密-NG</span><span class="sxs-lookup"><span data-stu-id="6bcf9-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="6bcf9-135">如果應用程式是在 Windows 8.1/Windows Server 2012 R2 或更新版本上執行，您可以使用 Windows DPAPI-NG 來執行以憑證為基礎的加密。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="6bcf9-136">使用規則描述元字串 "CERTIFICATE = HashId： THUMBPRINT"，其中 *指紋* 是憑證的十六進位編碼 SHA1 指紋：</span><span class="sxs-lookup"><span data-stu-id="6bcf9-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="6bcf9-137">此存放庫中指向的任何應用程式都必須在 Windows 8.1/Windows Server 2012 R2 或更新版本上執行，才能解密金鑰。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="6bcf9-138">自訂金鑰加密</span><span class="sxs-lookup"><span data-stu-id="6bcf9-138">Custom key encryption</span></span>

<span data-ttu-id="6bcf9-139">如果不適合使用內建機制，開發人員可以藉由提供自訂 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)來指定自己的金鑰加密機制。</span><span class="sxs-lookup"><span data-stu-id="6bcf9-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
