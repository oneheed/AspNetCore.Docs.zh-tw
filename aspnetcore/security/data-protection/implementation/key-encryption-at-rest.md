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
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a>Windows 和 Azure 中使用 ASP.NET Core 的待用金鑰加密

根據預設，資料保護系統會 [採用探索機制](xref:security/data-protection/configuration/default-settings) 來判斷密碼編譯金鑰如何加密。 開發人員可以覆寫探索機制，並手動指定如何加密待用的金鑰。

> [!WARNING]
> 如果您指定明確的 [金鑰持續性位置](xref:security/data-protection/implementation/key-storage-providers)，資料保護系統會取消註冊靜態的預設金鑰加密機制。 因此，待用的金鑰不會再加密。 建議您在生產環境部署中 [指定明確的金鑰加密機制](xref:security/data-protection/implementation/key-encryption-at-rest) 。 本主題將說明靜態加密機制的選項。

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure 金鑰保存庫

若要將金鑰儲存在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)中，請在類別中使用 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 來設定系統 `Startup` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

如需詳細資訊，請參閱 [設定 ASP.NET Core 資料保護： ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)。

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**僅適用于 Windows 部署。**

使用 Windows DPAPI 時，會先使用 [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) 加密金鑰內容，再將其保存到儲存體。 DPAPI 是一種適當的加密機制，適用于絕對不會在目前的電腦上讀取的資料 (但您可以將這些金鑰備份至 Active Directory;請參閱 [DPAPI 和漫遊設定檔](https://support.microsoft.com/kb/309408/#6)) 。 若要設定 DPAPI 靜態金鑰加密，請呼叫其中一個 [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) 擴充方法：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

如果 `ProtectKeysWithDpapi` 呼叫時沒有參數，則只有目前的 Windows 使用者帳戶可以解密保存的金鑰環。 您可以選擇性地指定電腦上的任何使用者帳戶 (不只是目前的使用者帳戶，) 能夠解密金鑰環：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>X.509 憑證

如果應用程式分散在多部電腦上，將共用的 x.509 憑證散發到電腦上，並將託管的應用程式設定為使用憑證來加密待用金鑰，可能會比較方便：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

由於 .NET Framework 的限制，因此只支援具有 CAPI 私密金鑰的憑證。 請參閱以下內容，以瞭解這些限制的可能因應措施。

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**這項機制只適用于 Windows 8/Windows Server 2012 或更新版本。**

從 Windows 8 開始，Windows OS 支援 DPAPI-NG (也稱為 CNG DPAPI) 。 如需詳細資訊，請參閱 [關於 CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi)。

主體會編碼為保護描述項規則。 在呼叫 [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)的下列範例中，只有加入網域的使用者具有指定的 SID 才能解密金鑰環：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

也有的無參數多載 `ProtectKeysWithDpapiNG` 。 使用此便利方法來指定規則 "SID = {CURRENT_ACCOUNT_SID}"，其中 *CURRENT_ACCOUNT_SID* 是目前 Windows 使用者帳戶的 SID：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

在此案例中，AD 網域控制站負責散發 DPAPI-NG 作業所使用的加密金鑰。 目標使用者可以從任何已加入網域的電腦解密已加密的承載 (前提是該進程正在其身分識別) 下執行。

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>以憑證為基礎的 Windows DPAPI 加密-NG

如果應用程式是在 Windows 8.1/Windows Server 2012 R2 或更新版本上執行，您可以使用 Windows DPAPI-NG 來執行以憑證為基礎的加密。 使用規則描述元字串 "CERTIFICATE = HashId： THUMBPRINT"，其中 *指紋* 是憑證的十六進位編碼 SHA1 指紋：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

此存放庫中指向的任何應用程式都必須在 Windows 8.1/Windows Server 2012 R2 或更新版本上執行，才能解密金鑰。

## <a name="custom-key-encryption"></a>自訂金鑰加密

如果不適合使用內建機制，開發人員可以藉由提供自訂 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)來指定自己的金鑰加密機制。
