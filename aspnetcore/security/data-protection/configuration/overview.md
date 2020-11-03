---
title: 設定 ASP.NET Core 資料保護
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 中設定資料保護。
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 048f6d6d57d3cc5d64004e18b18a3347ee92e450
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234565"
---
# <a name="configure-aspnet-core-data-protection"></a>設定 ASP.NET Core 資料保護

當資料保護系統初始化時，它會根據操作環境套用 [預設設定](xref:security/data-protection/configuration/default-settings) 。 這些設定通常適用于在單一電腦上執行的應用程式。 在某些情況下，開發人員可能會想要變更預設設定：

* 應用程式會散佈在多部電腦上。
* 基於合規性因素。

在這些情況下，資料保護系統會提供豐富的設定 API。

> [!WARNING]
> 類似于設定檔，應該使用適當的許可權來保護資料保護金鑰環形。 您可以選擇加密待用的金鑰，但這並不會阻止攻擊者建立新的金鑰。 因此，您應用程式的安全性會受到影響。 使用資料保護設定的儲存位置應該將其存取限制為應用程式本身，類似于保護設定檔的方式。 例如，如果您選擇將金鑰環形儲存在磁片上，請使用檔案系統許可權。 確定您的 web 應用程式執行所用的身分識別，具有該目錄的讀取、寫入和建立存取權。 如果您使用 Azure Blob 儲存體，則只有 web 應用程式應該能夠在 Blob 存放區中讀取、寫入或建立新的專案等等。
>
> 擴充方法 [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) 會傳回 [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)。 `IDataProtectionBuilder` 公開擴充方法，您可以將它們連結在一起以設定資料保護選項。

::: moniker range=">= aspnetcore-3.0"

本文中使用的資料保護延伸模組需要下列 NuGet 套件：

* [AspNetCore. DataProtection Blob](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [AspNetCore. DataProtection 金鑰](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

使用 CLI 登入 Azure，例如：

```azurecli
az login
``` 

若要將金鑰儲存在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)中，請在類別中使用 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 來設定系統 `Startup` 。 `blobUriWithSasToken` 這是應儲存金鑰檔的完整 URI。 URI 必須包含作為查詢字串參數的 SAS 權杖：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
}
```

設定金鑰環形儲存位置 (例如， [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)) 。 必須設定位置，因為呼叫 `ProtectKeysWithAzureKeyVault` 會實 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) 來停用自動資料保護設定，包括金鑰環形儲存位置。 上述範例會使用 Azure Blob 儲存體來保存金鑰環形。 如需詳細資訊，請參閱 [金鑰儲存提供者： Azure 儲存體](xref:security/data-protection/implementation/key-storage-providers#azure-storage)。 您也可以使用 [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system)在本機保存金鑰環形。

`keyIdentifier`是金鑰加密所使用的金鑰保存庫金鑰識別碼。 例如，在名為的金鑰保存庫中建立的金鑰 `dataprotection` `contosokeyvault` 具有金鑰識別碼 `https://contosokeyvault.vault.azure.net/keys/dataprotection/` 。 為應用程式提供金鑰保存庫的解除包裝 **金鑰** 和 **包裝金鑰** 許可權。

`ProtectKeysWithAzureKeyVault` 重載：

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder、Uri、TokenCredential) ](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) 允許使用 keyIdentifier Uri 和 TokenCredential，讓資料保護系統能夠使用金鑰保存庫。
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder、string、IKeyEncryptionKeyResolver) ](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) 允許使用 keyIdentifier 字串和 IKeyEncryptionKeyResolver，讓資料保護系統使用金鑰保存庫。

如果應用程式使用先前的 Azure 套件 ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) 和 [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) ，而且 Azure Key Vault 和 Azure 儲存體的組合會儲存並保護金鑰， <xref:System.UriFormatException?displayProperty=nameWithType> 則會在金鑰儲存的 blob 不存在時擲回。 在 Azure 入口網站中執行應用程式之前，可以手動建立 blob，或使用下列程式：

1. 移除 `ProtectKeysWithAzureKeyVault` 第一次執行的呼叫，以就地建立 blob。
1. `ProtectKeysWithAzureKeyVault`針對後續執行加入的呼叫。

建議您移除 `ProtectKeysWithAzureKeyVault` 第一次執行，因為它可確保檔案是以適當的架構和值建立。 

我們建議您升級至 [AspNetCore. DataProtection.](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) [AspNetCore. DataProtection. a... a..](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) 套件，因為提供的 API 會自動建立 blob （如果不存在的話）。

```csharp
services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage("<storage account connection string", "<key store container name>", "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
```

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

若要將金鑰儲存在 UNC 共用，而不是 *% LOCALAPPDATA%* 的預設位置，請使用 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)設定系統：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> 如果您變更金鑰持續性位置，系統就不會再自動加密待用的金鑰，因為它不知道 DPAPI 是否為適當的加密機制。

## <a name="protectkeyswith"></a>ProtectKeysWith\*

您可以藉由呼叫任何[ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)設定 api 來設定系統，以保護待用的金鑰。 請考慮下列範例，此範例會將金鑰儲存在 UNC 共用上，並使用特定的 x.509 憑證來加密待用的金鑰：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

在 ASP.NET Core 2.1 或更新版本中，您可以提供 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 給 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)，例如從檔案載入的憑證：

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

如需有關內建金鑰加密機制的更多範例和討論，請參閱待用的 [金鑰加密](xref:security/data-protection/implementation/key-encryption-at-rest) 。

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

在 ASP.NET Core 2.1 或更新版本中，您可以使用 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 憑證的陣列搭配 [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)來輪替憑證和解密金鑰：

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

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

若要將系統設定為使用14天的金鑰存留期，而不是預設的90天，請使用 [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

根據預設，資料保護系統會根據其 [內容根](xref:fundamentals/index#content-root) 路徑隔離應用程式，即使它們共用相同的實體金鑰存放庫也是一樣。 這可防止應用程式瞭解彼此受保護的承載。

在應用程式之間共用受保護的承載：

* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>在每個具有相同值的應用程式中設定。
* 在應用程式中使用相同版本的資料保護 API 堆疊。 在應用程式的專案檔案中，執行下列 **其中一** 項：
  * 透過 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)參考相同的共用架構版本。
  * 參考相同的 [資料保護套件](xref:security/data-protection/introduction#package-layout) 版本。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

您可能會想要讓應用程式在接近到期時， (建立新的金鑰) ，自動變換金鑰。 其中一個範例可能是在主要/次要關聯性中設定的應用程式，其中只有主要應用程式負責管理重要的問題，而次要應用程式只具有金鑰環的唯讀觀點。 您可以使用下列方法設定系統，以將次要應用程式設定為將金鑰通道視為唯讀 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>每個應用程式隔離

當 ASP.NET Core 的主機提供資料保護系統時，它會自動隔離彼此的應用程式，即使這些應用程式是在相同的背景工作進程帳戶下執行，而且使用相同的主要金鑰。 這有點類似于 System.object 的元素中的 IsolateApps 修飾詞 `<machineKey>` 。

隔離機制的運作方式是將本機電腦上的每個應用程式視為唯一的租使用者，如此一來， <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 任何指定應用程式的根目錄都會自動包含應用程式識別碼作為鑒別子。 應用程式的唯一識別碼是應用程式的實體路徑：

* 針對裝載于 IIS 中的應用程式，唯一識別碼是應用程式的 IIS 實體路徑。 如果應用程式部署在 web 伺服陣列環境中，此值會穩定，假設 IIS 環境在 web 伺服陣列中的所有電腦上都有類似的設定。
* 針對在 [Kestrel 伺服器](xref:fundamentals/servers/index#kestrel)上執行的自我裝載應用程式，唯一識別碼是應用程式在磁片上的實體路徑。

唯一識別碼的設計目的是要同時重設 &mdash; 個別應用程式和電腦本身的。

這種隔離機制假設應用程式不是惡意的。 惡意的應用程式一律會影響在相同背景工作進程帳戶下執行的任何其他應用程式。 在應用程式互相不受信任的共用裝載環境中，主機服務提供者應採取步驟來確保應用程式之間的作業系統層級隔離，包括將應用程式的基礎金鑰存放庫分開。

如果 ASP.NET Core 主機未提供資料保護系統 (例如，如果您透過具象類型將它具現化， `DataProtectionProvider`) 應用程式隔離預設為停用。 當應用程式隔離停用時，所有由相同金鑰處理內容支援的應用程式都可以共用承載，只要它們提供適當的 [用途](xref:security/data-protection/consumer-apis/purpose-strings)。 若要在此環境中提供應用程式隔離，請在 configuration 物件上呼叫 [SetApplicationName](#setapplicationname) 方法，並為每個應用程式提供唯一的名稱。

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>使用 UseCryptographicAlgorithms 變更演算法

「資料保護堆疊」可讓您變更新產生的索引鍵所使用的預設演算法。 若要這樣做，最簡單的方法是從設定回呼呼叫 [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) ：

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

預設 EncryptionAlgorithm 是 AES-256-CBC，預設 ValidationAlgorithm 為 HMACSHA256。 系統管理員可以透過 [整個電腦的原則](xref:security/data-protection/configuration/machine-wide-policy)來設定預設原則，但會明確呼叫以 `UseCryptographicAlgorithms` 覆寫預設原則。

呼叫可 `UseCryptographicAlgorithms` 讓您從預先定義的內建清單中指定所需的演算法。 您不需要擔心演算法的執行。 在上述案例中，如果在 Windows 上執行，資料保護系統會嘗試使用 AES 的 CNG 實行。 否則，它會切換 [回受管理](/dotnet/api/system.security.cryptography.aes) 的 system.string 類別。

您可以透過呼叫 [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)來手動指定執行。

> [!TIP]
> 變更演算法並不會影響金鑰通道中的現有金鑰。 它只會影響新產生的索引鍵。

### <a name="specifying-custom-managed-algorithms"></a>指定自訂受控演算法

::: moniker range=">= aspnetcore-2.0"

若要指定自訂 managed 演算法，請建立指向實類型的 [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) 實例：

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

若要指定自訂 managed 演算法，請建立指向實類型的 [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) 實例：

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

一般來說 \* ，型別屬性必須指向具體的可具現化 (透過公用無參數的 .ctor) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) 和 [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)的實值，但為了方便起見，系統特殊情況下有一些值 `typeof(Aes)` 。

> [!NOTE]
> SymmetricAlgorithm 必須具有≥128位的金鑰長度和≥64位的區塊大小，而且必須支援 PKCS #7 填補的 CBC 模式加密。 KeyedHashAlgorithm 必須有 >= 128 位的摘要大小，而且必須支援長度等於雜湊演算法摘要長度的索引鍵。 KeyedHashAlgorithm 不是絕對必要的為 HMAC。

### <a name="specifying-custom-windows-cng-algorithms"></a>指定自訂的 Windows CNG 演算法

::: moniker range=">= aspnetcore-2.0"

若要使用搭配 HMAC 驗證的 CBC 模式加密來指定自訂的 Windows CNG 演算法，請建立包含演算法資訊的 [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) 實例：

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

若要使用搭配 HMAC 驗證的 CBC 模式加密來指定自訂的 Windows CNG 演算法，請建立包含演算法資訊的 [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) 實例：

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
> 對稱封鎖密碼演算法的金鑰長度必須是 >= 128 位，>= 64 位的區塊大小，而且必須支援具有 PKCS #7 填補的 CBC 模式加密。 雜湊演算法必須有 >= 128 位的摘要大小，而且必須支援使用 BCRYPT \_ ALG \_ 控制碼 \_ HMAC 旗標旗標來開啟 \_ 。 \*提供者屬性可以設定為 null，以針對指定的演算法使用預設提供者。 如需詳細資訊，請參閱 [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) 檔。

::: moniker range=">= aspnetcore-2.0"

若要使用具有驗證的 Galois/計數器模式加密來指定自訂的 Windows CNG 演算法，請建立包含演算法資訊的 [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) 實例：

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

若要使用具有驗證的 Galois/計數器模式加密來指定自訂的 Windows CNG 演算法，請建立包含演算法資訊的 [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) 實例：

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
> 對稱式區塊加密演算法的金鑰長度必須是 >= 128 位、剛好是128位的區塊大小，而且必須支援 GCM 加密。 您可以將 [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) 屬性設為 null，以針對指定的演算法使用預設提供者。 如需詳細資訊，請參閱 [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) 檔。

### <a name="specifying-other-custom-algorithms"></a>指定其他自訂演算法

雖然不是公開為第一級的 API，但資料保護系統的擴充能力足以讓您指定幾乎任何類型的演算法。 例如，您可以將硬體安全性模組中包含的所有金鑰都 (HSM) ，並提供核心加密和解密常式的自訂執行。 如需詳細資訊，請參閱[核心密碼](xref:security/data-protection/extensibility/core-crypto)編譯擴充性中的[IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) 。

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>在 Docker 容器中裝載時保存金鑰

在 [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) 容器中裝載時，您應該在下列其中一項維護金鑰：

* 此資料夾是保存在容器存留期之外的 Docker 磁片區，例如共用磁片區或裝載于主機的磁片區。
* 外部提供者，例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 或 [Redis](https://redis.io/)。

## <a name="persisting-keys-with-redis"></a>使用 Redis 來保存金鑰

只應使用支援 [Redis 資料持續](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) 性的 Redis 版本來儲存金鑰。 [Azure Blob 儲存體](/azure/storage/blobs/storage-blobs-introduction) 是持續性的，可以用來儲存金鑰。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/13476)。

## <a name="additional-resources"></a>其他資源

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
