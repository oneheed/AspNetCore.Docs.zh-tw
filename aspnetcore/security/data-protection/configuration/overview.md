---
title: 設定 ASP.NET Core 資料保護
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 中設定資料保護。
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 048f6d6d57d3cc5d64004e18b18a3347ee92e450
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234565"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="3b1ce-103">設定 ASP.NET Core 資料保護</span><span class="sxs-lookup"><span data-stu-id="3b1ce-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="3b1ce-104">當資料保護系統初始化時，它會根據操作環境套用 [預設設定](xref:security/data-protection/configuration/default-settings) 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="3b1ce-105">這些設定通常適用于在單一電腦上執行的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="3b1ce-106">在某些情況下，開發人員可能會想要變更預設設定：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="3b1ce-107">應用程式會散佈在多部電腦上。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="3b1ce-108">基於合規性因素。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-108">For compliance reasons.</span></span>

<span data-ttu-id="3b1ce-109">在這些情況下，資料保護系統會提供豐富的設定 API。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="3b1ce-110">類似于設定檔，應該使用適當的許可權來保護資料保護金鑰環形。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="3b1ce-111">您可以選擇加密待用的金鑰，但這並不會阻止攻擊者建立新的金鑰。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="3b1ce-112">因此，您應用程式的安全性會受到影響。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="3b1ce-113">使用資料保護設定的儲存位置應該將其存取限制為應用程式本身，類似于保護設定檔的方式。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="3b1ce-114">例如，如果您選擇將金鑰環形儲存在磁片上，請使用檔案系統許可權。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="3b1ce-115">確定您的 web 應用程式執行所用的身分識別，具有該目錄的讀取、寫入和建立存取權。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="3b1ce-116">如果您使用 Azure Blob 儲存體，則只有 web 應用程式應該能夠在 Blob 存放區中讀取、寫入或建立新的專案等等。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="3b1ce-117">擴充方法 [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) 會傳回 [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="3b1ce-118">`IDataProtectionBuilder` 公開擴充方法，您可以將它們連結在一起以設定資料保護選項。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3b1ce-119">本文中使用的資料保護延伸模組需要下列 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="3b1ce-120">AspNetCore. DataProtection Blob</span><span class="sxs-lookup"><span data-stu-id="3b1ce-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [<span data-ttu-id="3b1ce-121">AspNetCore. DataProtection 金鑰</span><span class="sxs-lookup"><span data-stu-id="3b1ce-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="3b1ce-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="3b1ce-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="3b1ce-123">使用 CLI 登入 Azure，例如：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-123">Log in to Azure using the CLI, for example:</span></span>

```azurecli
az login
``` 

<span data-ttu-id="3b1ce-124">若要將金鑰儲存在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)中，請在類別中使用 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 來設定系統 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-124">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class.</span></span> <span data-ttu-id="3b1ce-125">`blobUriWithSasToken` 這是應儲存金鑰檔的完整 URI。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-125">`blobUriWithSasToken` is the full URI where the key file should be stored.</span></span> <span data-ttu-id="3b1ce-126">URI 必須包含作為查詢字串參數的 SAS 權杖：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-126">The URI must contain the SAS token as a query string parameter:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
}
```

<span data-ttu-id="3b1ce-127">設定金鑰環形儲存位置 (例如， [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)) 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-127">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="3b1ce-128">必須設定位置，因為呼叫 `ProtectKeysWithAzureKeyVault` 會實 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) 來停用自動資料保護設定，包括金鑰環形儲存位置。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-128">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="3b1ce-129">上述範例會使用 Azure Blob 儲存體來保存金鑰環形。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-129">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="3b1ce-130">如需詳細資訊，請參閱 [金鑰儲存提供者： Azure 儲存體](xref:security/data-protection/implementation/key-storage-providers#azure-storage)。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-130">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="3b1ce-131">您也可以使用 [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system)在本機保存金鑰環形。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-131">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="3b1ce-132">`keyIdentifier`是金鑰加密所使用的金鑰保存庫金鑰識別碼。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-132">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="3b1ce-133">例如，在名為的金鑰保存庫中建立的金鑰 `dataprotection` `contosokeyvault` 具有金鑰識別碼 `https://contosokeyvault.vault.azure.net/keys/dataprotection/` 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-133">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="3b1ce-134">為應用程式提供金鑰保存庫的解除包裝 **金鑰** 和 **包裝金鑰** 許可權。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-134">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="3b1ce-135">`ProtectKeysWithAzureKeyVault` 重載：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-135">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="3b1ce-136">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder、Uri、TokenCredential) ](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) 允許使用 keyIdentifier Uri 和 TokenCredential，讓資料保護系統能夠使用金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-136">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier Uri and a tokenCredential to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="3b1ce-137">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder、string、IKeyEncryptionKeyResolver) ](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) 允許使用 keyIdentifier 字串和 IKeyEncryptionKeyResolver，讓資料保護系統使用金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-137">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, string, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier string and IKeyEncryptionKeyResolver to enable the data protection system to use the key vault.</span></span>

<span data-ttu-id="3b1ce-138">如果應用程式使用先前的 Azure 套件 ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) 和 [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) ，而且 Azure Key Vault 和 Azure 儲存體的組合會儲存並保護金鑰， <xref:System.UriFormatException?displayProperty=nameWithType> 則會在金鑰儲存的 blob 不存在時擲回。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-138">If the app uses the prior Azure packages ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) and [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) and a combination of Azure Key Vault and Azure Storage to store and protect keys, <xref:System.UriFormatException?displayProperty=nameWithType> is thrown if the blob for key storage doesn't exist.</span></span> <span data-ttu-id="3b1ce-139">在 Azure 入口網站中執行應用程式之前，可以手動建立 blob，或使用下列程式：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-139">The blob can be manually created ahead of running the app in the Azure portal, or use the following procedure:</span></span>

1. <span data-ttu-id="3b1ce-140">移除 `ProtectKeysWithAzureKeyVault` 第一次執行的呼叫，以就地建立 blob。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-140">Remove the call to `ProtectKeysWithAzureKeyVault` for the first run to create the blob in place.</span></span>
1. <span data-ttu-id="3b1ce-141">`ProtectKeysWithAzureKeyVault`針對後續執行加入的呼叫。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-141">Add the call to `ProtectKeysWithAzureKeyVault` for subsequent runs.</span></span>

<span data-ttu-id="3b1ce-142">建議您移除 `ProtectKeysWithAzureKeyVault` 第一次執行，因為它可確保檔案是以適當的架構和值建立。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-142">Removing `ProtectKeysWithAzureKeyVault` for the first run is advised, as it ensures that the file is created with the proper schema and values in place.</span></span> 

<span data-ttu-id="3b1ce-143">我們建議您升級至 [AspNetCore. DataProtection.](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) [AspNetCore. DataProtection. a... a..](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) 套件，因為提供的 API 會自動建立 blob （如果不存在的話）。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-143">We recommended upgrading to the [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) and [Azure.Extensions.AspNetCore.DataProtection.Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) packages because the API provided automatically creates the blob if it doesn't exist.</span></span>

```csharp
services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage("<storage account connection string", "<key store container name>", "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
```

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="3b1ce-144">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="3b1ce-144">PersistKeysToFileSystem</span></span>

<span data-ttu-id="3b1ce-145">若要將金鑰儲存在 UNC 共用，而不是 *% LOCALAPPDATA%* 的預設位置，請使用 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)設定系統：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-145">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="3b1ce-146">如果您變更金鑰持續性位置，系統就不會再自動加密待用的金鑰，因為它不知道 DPAPI 是否為適當的加密機制。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-146">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="3b1ce-147">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="3b1ce-147">ProtectKeysWith\*</span></span>

<span data-ttu-id="3b1ce-148">您可以藉由呼叫任何[ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)設定 api 來設定系統，以保護待用的金鑰。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-148">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="3b1ce-149">請考慮下列範例，此範例會將金鑰儲存在 UNC 共用上，並使用特定的 x.509 憑證來加密待用的金鑰：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-149">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3b1ce-150">在 ASP.NET Core 2.1 或更新版本中，您可以提供 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 給 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)，例如從檔案載入的憑證：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-150">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="3b1ce-151">如需有關內建金鑰加密機制的更多範例和討論，請參閱待用的 [金鑰加密](xref:security/data-protection/implementation/key-encryption-at-rest) 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-151">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="3b1ce-152">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="3b1ce-152">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="3b1ce-153">在 ASP.NET Core 2.1 或更新版本中，您可以使用 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 憑證的陣列搭配 [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)來輪替憑證和解密金鑰：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-153">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="3b1ce-154">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="3b1ce-154">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="3b1ce-155">若要將系統設定為使用14天的金鑰存留期，而不是預設的90天，請使用 [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-155">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="3b1ce-156">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="3b1ce-156">SetApplicationName</span></span>

<span data-ttu-id="3b1ce-157">根據預設，資料保護系統會根據其 [內容根](xref:fundamentals/index#content-root) 路徑隔離應用程式，即使它們共用相同的實體金鑰存放庫也是一樣。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-157">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="3b1ce-158">這可防止應用程式瞭解彼此受保護的承載。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-158">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="3b1ce-159">在應用程式之間共用受保護的承載：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-159">To share protected payloads among apps:</span></span>

* <span data-ttu-id="3b1ce-160"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>在每個具有相同值的應用程式中設定。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-160">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="3b1ce-161">在應用程式中使用相同版本的資料保護 API 堆疊。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-161">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="3b1ce-162">在應用程式的專案檔案中，執行下列 **其中一** 項：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-162">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="3b1ce-163">透過 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)參考相同的共用架構版本。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-163">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="3b1ce-164">參考相同的 [資料保護套件](xref:security/data-protection/introduction#package-layout) 版本。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-164">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="3b1ce-165">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="3b1ce-165">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="3b1ce-166">您可能會想要讓應用程式在接近到期時， (建立新的金鑰) ，自動變換金鑰。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-166">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="3b1ce-167">其中一個範例可能是在主要/次要關聯性中設定的應用程式，其中只有主要應用程式負責管理重要的問題，而次要應用程式只具有金鑰環的唯讀觀點。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-167">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="3b1ce-168">您可以使用下列方法設定系統，以將次要應用程式設定為將金鑰通道視為唯讀 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> ：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-168">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="3b1ce-169">每個應用程式隔離</span><span class="sxs-lookup"><span data-stu-id="3b1ce-169">Per-application isolation</span></span>

<span data-ttu-id="3b1ce-170">當 ASP.NET Core 的主機提供資料保護系統時，它會自動隔離彼此的應用程式，即使這些應用程式是在相同的背景工作進程帳戶下執行，而且使用相同的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-170">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="3b1ce-171">這有點類似于 System.object 的元素中的 IsolateApps 修飾詞 `<machineKey>` 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-171">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="3b1ce-172">隔離機制的運作方式是將本機電腦上的每個應用程式視為唯一的租使用者，如此一來， <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 任何指定應用程式的根目錄都會自動包含應用程式識別碼作為鑒別子。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-172">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="3b1ce-173">應用程式的唯一識別碼是應用程式的實體路徑：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-173">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="3b1ce-174">針對裝載于 IIS 中的應用程式，唯一識別碼是應用程式的 IIS 實體路徑。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-174">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="3b1ce-175">如果應用程式部署在 web 伺服陣列環境中，此值會穩定，假設 IIS 環境在 web 伺服陣列中的所有電腦上都有類似的設定。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-175">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="3b1ce-176">針對在 [Kestrel 伺服器](xref:fundamentals/servers/index#kestrel)上執行的自我裝載應用程式，唯一識別碼是應用程式在磁片上的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-176">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="3b1ce-177">唯一識別碼的設計目的是要同時重設 &mdash; 個別應用程式和電腦本身的。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-177">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="3b1ce-178">這種隔離機制假設應用程式不是惡意的。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-178">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="3b1ce-179">惡意的應用程式一律會影響在相同背景工作進程帳戶下執行的任何其他應用程式。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-179">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="3b1ce-180">在應用程式互相不受信任的共用裝載環境中，主機服務提供者應採取步驟來確保應用程式之間的作業系統層級隔離，包括將應用程式的基礎金鑰存放庫分開。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-180">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="3b1ce-181">如果 ASP.NET Core 主機未提供資料保護系統 (例如，如果您透過具象類型將它具現化， `DataProtectionProvider`) 應用程式隔離預設為停用。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-181">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="3b1ce-182">當應用程式隔離停用時，所有由相同金鑰處理內容支援的應用程式都可以共用承載，只要它們提供適當的 [用途](xref:security/data-protection/consumer-apis/purpose-strings)。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-182">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="3b1ce-183">若要在此環境中提供應用程式隔離，請在 configuration 物件上呼叫 [SetApplicationName](#setapplicationname) 方法，並為每個應用程式提供唯一的名稱。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-183">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="3b1ce-184">使用 UseCryptographicAlgorithms 變更演算法</span><span class="sxs-lookup"><span data-stu-id="3b1ce-184">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="3b1ce-185">「資料保護堆疊」可讓您變更新產生的索引鍵所使用的預設演算法。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-185">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="3b1ce-186">若要這樣做，最簡單的方法是從設定回呼呼叫 [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) ：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-186">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="3b1ce-187">預設 EncryptionAlgorithm 是 AES-256-CBC，預設 ValidationAlgorithm 為 HMACSHA256。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-187">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="3b1ce-188">系統管理員可以透過 [整個電腦的原則](xref:security/data-protection/configuration/machine-wide-policy)來設定預設原則，但會明確呼叫以 `UseCryptographicAlgorithms` 覆寫預設原則。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-188">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="3b1ce-189">呼叫可 `UseCryptographicAlgorithms` 讓您從預先定義的內建清單中指定所需的演算法。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-189">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="3b1ce-190">您不需要擔心演算法的執行。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-190">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="3b1ce-191">在上述案例中，如果在 Windows 上執行，資料保護系統會嘗試使用 AES 的 CNG 實行。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-191">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="3b1ce-192">否則，它會切換 [回受管理](/dotnet/api/system.security.cryptography.aes) 的 system.string 類別。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-192">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="3b1ce-193">您可以透過呼叫 [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)來手動指定執行。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-193">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="3b1ce-194">變更演算法並不會影響金鑰通道中的現有金鑰。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-194">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="3b1ce-195">它只會影響新產生的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-195">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="3b1ce-196">指定自訂受控演算法</span><span class="sxs-lookup"><span data-stu-id="3b1ce-196">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="3b1ce-197">若要指定自訂 managed 演算法，請建立指向實類型的 [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) 實例：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-197">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3b1ce-198">若要指定自訂 managed 演算法，請建立指向實類型的 [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) 實例：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-198">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="3b1ce-199">一般來說 \* ，型別屬性必須指向具體的可具現化 (透過公用無參數的 .ctor) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) 和 [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)的實值，但為了方便起見，系統特殊情況下有一些值 `typeof(Aes)` 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-199">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="3b1ce-200">SymmetricAlgorithm 必須具有≥128位的金鑰長度和≥64位的區塊大小，而且必須支援 PKCS #7 填補的 CBC 模式加密。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-200">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="3b1ce-201">KeyedHashAlgorithm 必須有 >= 128 位的摘要大小，而且必須支援長度等於雜湊演算法摘要長度的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-201">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="3b1ce-202">KeyedHashAlgorithm 不是絕對必要的為 HMAC。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-202">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="3b1ce-203">指定自訂的 Windows CNG 演算法</span><span class="sxs-lookup"><span data-stu-id="3b1ce-203">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="3b1ce-204">若要使用搭配 HMAC 驗證的 CBC 模式加密來指定自訂的 Windows CNG 演算法，請建立包含演算法資訊的 [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) 實例：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-204">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3b1ce-205">若要使用搭配 HMAC 驗證的 CBC 模式加密來指定自訂的 Windows CNG 演算法，請建立包含演算法資訊的 [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) 實例：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-205">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3b1ce-206">對稱封鎖密碼演算法的金鑰長度必須是 >= 128 位，>= 64 位的區塊大小，而且必須支援具有 PKCS #7 填補的 CBC 模式加密。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-206">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="3b1ce-207">雜湊演算法必須有 >= 128 位的摘要大小，而且必須支援使用 BCRYPT \_ ALG \_ 控制碼 \_ HMAC 旗標旗標來開啟 \_ 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-207">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="3b1ce-208">\*提供者屬性可以設定為 null，以針對指定的演算法使用預設提供者。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-208">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="3b1ce-209">如需詳細資訊，請參閱 [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) 檔。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-209">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="3b1ce-210">若要使用具有驗證的 Galois/計數器模式加密來指定自訂的 Windows CNG 演算法，請建立包含演算法資訊的 [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) 實例：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-210">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3b1ce-211">若要使用具有驗證的 Galois/計數器模式加密來指定自訂的 Windows CNG 演算法，請建立包含演算法資訊的 [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) 實例：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-211">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3b1ce-212">對稱式區塊加密演算法的金鑰長度必須是 >= 128 位、剛好是128位的區塊大小，而且必須支援 GCM 加密。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-212">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="3b1ce-213">您可以將 [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) 屬性設為 null，以針對指定的演算法使用預設提供者。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-213">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="3b1ce-214">如需詳細資訊，請參閱 [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) 檔。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-214">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="3b1ce-215">指定其他自訂演算法</span><span class="sxs-lookup"><span data-stu-id="3b1ce-215">Specifying other custom algorithms</span></span>

<span data-ttu-id="3b1ce-216">雖然不是公開為第一級的 API，但資料保護系統的擴充能力足以讓您指定幾乎任何類型的演算法。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-216">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="3b1ce-217">例如，您可以將硬體安全性模組中包含的所有金鑰都 (HSM) ，並提供核心加密和解密常式的自訂執行。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-217">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="3b1ce-218">如需詳細資訊，請參閱[核心密碼](xref:security/data-protection/extensibility/core-crypto)編譯擴充性中的[IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) 。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-218">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="3b1ce-219">在 Docker 容器中裝載時保存金鑰</span><span class="sxs-lookup"><span data-stu-id="3b1ce-219">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="3b1ce-220">在 [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) 容器中裝載時，您應該在下列其中一項維護金鑰：</span><span class="sxs-lookup"><span data-stu-id="3b1ce-220">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="3b1ce-221">此資料夾是保存在容器存留期之外的 Docker 磁片區，例如共用磁片區或裝載于主機的磁片區。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-221">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="3b1ce-222">外部提供者，例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 或 [Redis](https://redis.io/)。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-222">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="3b1ce-223">使用 Redis 來保存金鑰</span><span class="sxs-lookup"><span data-stu-id="3b1ce-223">Persisting keys with Redis</span></span>

<span data-ttu-id="3b1ce-224">只應使用支援 [Redis 資料持續](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) 性的 Redis 版本來儲存金鑰。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-224">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="3b1ce-225">[Azure Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction) 是持續性的，可以用來儲存金鑰。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-225">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="3b1ce-226">如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/13476)。</span><span class="sxs-lookup"><span data-stu-id="3b1ce-226">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b1ce-227">其他資源</span><span class="sxs-lookup"><span data-stu-id="3b1ce-227">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
