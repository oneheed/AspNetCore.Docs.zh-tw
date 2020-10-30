---
title: ASP.NET Core 中的資料保護全電腦原則支援
author: rick-anderson
description: 瞭解如何針對使用 ASP.NET Core 資料保護的所有應用程式，設定預設的全電腦原則。
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
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: f6f258e193bf964cb9b4cbd24da2740c5ed89a91
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052990"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>ASP.NET Core 中的資料保護全電腦原則支援

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

在 Windows 上執行時，對於使用 ASP.NET Core 資料保護的所有應用程式而言，資料保護系統的支援有限，可設定預設的全電腦原則。 一般的概念是，系統管理員可能想要變更預設設定，例如使用的演算法或金鑰存留期，而不需要手動更新電腦上的每個應用程式。

> [!WARNING]
> 系統管理員可以設定預設原則，但無法強制執行。 應用程式開發人員一律可以使用自己選擇的其中一個值來覆寫任何值。 預設原則只會影響開發人員未針對設定指定明確值的應用程式。

## <a name="setting-default-policy"></a>設定預設原則

若要設定預設原則，系統管理員可以在系統登錄中的下列登錄機碼底下設定已知的值：

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

如果您使用的是64位的作業系統，而且想要影響32位應用程式的行為，請記得設定與上述金鑰相等的 Wow6432Node。

支援的值如下所示。

| 值              | 類型   | 描述 |
| ------------------ | :----: | ----------- |
| EncryptionType     | 字串 | 指定用於資料保護的演算法。 此值必須是 CNG-CBC、CNG、GCM 或受控，並且會在下面更詳細地說明。 |
| DefaultKeyLifetime | DWORD  | 指定新產生之索引鍵的存留期。 值是以天為單位指定，而且必須是 >= 7。 |
| KeyEscrowSinks     | 字串 | 指定用於金鑰委付的類型。 值是以分號分隔的索引鍵，清單中的每個專案都是實 [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)型別的元件限定名稱。 |

## <a name="encryption-types"></a>加密類型

如果 EncryptionType 是 CNG-CBC，系統就會將系統設定為使用 CBC 模式對稱封鎖加密，以提供 Windows CNG 所提供之服務的機密性和 HMAC 的真實性 (如需詳細資料，請參閱 [指定自訂的 WINDOWS cng 演算法](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)) 。 以下是支援的其他值，每一個都對應至 CngCbcAuthenticatedEncryptionSettings 類型上的屬性。

| 值                       | 類型   | 描述 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | 字串 | CNG 所瞭解的對稱區塊加密演算法名稱。 此演算法會在 CBC 模式中開啟。 |
| EncryptionAlgorithmProvider | 字串 | 可以產生演算法 EncryptionAlgorithm 的 CNG 提供者執行名稱。 |
| EncryptionAlgorithmKeySize  | DWORD  |  (的長度，以位) 為對稱區塊加密演算法所衍生的金鑰。 |
| HashAlgorithm               | 字串 | CNG 所瞭解的雜湊演算法名稱。 此演算法會在 HMAC 模式中開啟。 |
| HashAlgorithmProvider       | 字串 | 可以產生演算法 HashAlgorithm 的 CNG 提供者執行名稱。 |

如果 EncryptionType 是 CNG-GCM，系統就會將系統設定為使用 Galois/Counter 模式對稱區塊加密，以提供 Windows CNG 所提供的服務機密性和真實性 (如需詳細資料，請參閱 [指定自訂的 WINDOWS cng 演算法](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)) 。 以下是支援的其他值，每一個都對應至 CngGcmAuthenticatedEncryptionSettings 類型上的屬性。

| 值                       | 類型   | 描述 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | 字串 | CNG 所瞭解的對稱區塊加密演算法名稱。 此演算法會以 Galois/Counter 模式開啟。 |
| EncryptionAlgorithmProvider | 字串 | 可以產生演算法 EncryptionAlgorithm 的 CNG 提供者執行名稱。 |
| EncryptionAlgorithmKeySize  | DWORD  |  (的長度，以位) 為對稱區塊加密演算法所衍生的金鑰。 |

如果 EncryptionType 是受管理的，系統就會將系統設定為使用受控 SymmetricAlgorithm 來取得機密性和 KeyedHashAlgorithm 的真實性 (如需詳細資料，請參閱 [指定自訂受控演算法](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms)) 。 以下是支援的其他值，每一個都對應至 ManagedAuthenticatedEncryptionSettings 類型上的屬性。

| 值                      | 類型   | 描述 |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | 字串 | 實 SymmetricAlgorithm 之型別的元件限定名稱。 |
| EncryptionAlgorithmKeySize | DWORD  |  (的長度，以位) 為對稱加密演算法所衍生的金鑰。 |
| ValidationAlgorithmType    | 字串 | 實 KeyedHashAlgorithm 之型別的元件限定名稱。 |

如果 EncryptionType 具有 null 或空白以外的任何其他值，資料保護系統就會在啟動時擲回例外狀況。

> [!WARNING]
> 設定包含類型名稱 (EncryptionAlgorithmType、ValidationAlgorithmType、KeyEscrowSinks) 的預設原則設定時，這些類型必須可供應用程式使用。 這表示，對於在 Desktop CLR 上執行的應用程式，包含這些類型的元件應該存在於全域組件快取 (GAC) 中。 如果是在 .NET Core 上執行的 ASP.NET Core 應用程式，則應該安裝包含這些類型的封裝。
