---
title: ASP.NET Core 中的資料保護全電腦原則支援
author: rick-anderson
description: 瞭解針對使用 ASP.NET Core 資料保護的所有應用程式，設定預設全電腦原則的支援。
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
ms.openlocfilehash: f4b8dc379c0219ff9fc363df55df1103ef40a5ce
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022403"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>ASP.NET Core 中的資料保護全電腦原則支援

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

在 Windows 上執行時，資料保護系統對於使用 ASP.NET Core 資料保護的所有應用程式，設定預設全電腦原則的支援有限。 一般的概念是，系統管理員可能想要變更預設設定（例如使用的演算法或金鑰存留期），而不需要手動更新電腦上的每個應用程式。

> [!WARNING]
> 系統管理員可以設定預設原則，但無法強制執行。 應用程式開發人員一律可以使用自己選擇的其中一個來覆寫任何值。 預設原則只會影響開發人員未指定設定之明確值的應用程式。

## <a name="setting-default-policy"></a>設定預設原則

若要設定預設原則，管理員可以在系統登錄的下列登錄機碼底下設定已知的值：

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

如果您是在64位的作業系統上，而且想要影響32位應用程式的行為，請記得設定上述金鑰的 Wow6432Node 對等用法。

支援的值如下所示。

| 值              | 類型   | 描述 |
| ------------------ | :----: | ----------- |
| EncryptionType     | string | 指定應該使用哪些演算法來保護資料。 此值必須為 CNG-CBC、CNG-GCM 或 Managed，並會在下面詳細說明。 |
| DefaultKeyLifetime | DWORD  | 指定新產生之金鑰的存留期。 此值以天為單位指定，且必須 >= 7。 |
| KeyEscrowSinks     | string | 指定用於金鑰委付的類型。 此值是以分號分隔的金鑰委付接收清單，其中清單中的每個元素都是實[IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)類型的元件限定名稱。 |

## <a name="encryption-types"></a>加密類型

如果 EncryptionType 是 CNG-CBC，系統會設定為使用 CBC 模式對稱區塊加密，以搭配 Windows CNG 所提供之服務的真實性和 HMAC (如需更多詳細資料，請參閱[指定自訂的 WINDOWS CNG 演算法](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)) 。 下列為支援的額外值，其中每一個都對應至 CngCbcAuthenticatedEncryptionSettings 類型上的屬性。

| 值                       | 類型   | 描述 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | CNG 瞭解的對稱式區塊密碼演算法的名稱。 此演算法會在 CBC 模式中開啟。 |
| EncryptionAlgorithmProvider | string | 可以產生演算法 EncryptionAlgorithm 的 CNG 提供者實作為名稱。 |
| EncryptionAlgorithmKeySize  | DWORD  | 要為對稱區塊加密演算法衍生的金鑰長度 (（以位) ）。 |
| HashAlgorithm               | string | CNG 所瞭解之雜湊演算法的名稱。 此演算法會在 HMAC 模式中開啟。 |
| HashAlgorithmProvider       | string | 可以產生演算法 HashAlgorithm 的 CNG 提供者實作為名稱。 |

如果 EncryptionType 是 CNG-GCM，系統會設定為使用 Galois/Counter 模式對稱區塊加密來搭配 Windows CNG 所提供的服務機密性和真實性 (如需詳細資訊，請參閱[指定自訂 WINDOWS CNG 演算法](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)) 。 下列為支援的額外值，其中每一個都對應至 CngGcmAuthenticatedEncryptionSettings 類型上的屬性。

| 值                       | 類型   | 描述 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | CNG 瞭解的對稱式區塊密碼演算法的名稱。 此演算法會在 Galois/Counter 模式中開啟。 |
| EncryptionAlgorithmProvider | string | 可以產生演算法 EncryptionAlgorithm 的 CNG 提供者實作為名稱。 |
| EncryptionAlgorithmKeySize  | DWORD  | 要為對稱區塊加密演算法衍生的金鑰長度 (（以位) ）。 |

如果 EncryptionType 是受管理的，系統會設定為使用受控 System.security.cryptography.symmetricalgorithm 的機密性和 KeyedHashAlgorithm 以取得真實性 (如需詳細資訊，請參閱[指定自訂的受控演算法](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms)) 。 下列為支援的額外值，其中每一個都對應至 ManagedAuthenticatedEncryptionSettings 類型上的屬性。

| 值                      | 類型   | 描述 |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | string | 實 System.security.cryptography.symmetricalgorithm 之型別的元件限定名稱。 |
| EncryptionAlgorithmKeySize | DWORD  | 要為對稱式加密演算法衍生的金鑰長度 (（以位) ）。 |
| ValidationAlgorithmType    | string | 實 KeyedHashAlgorithm 之型別的元件限定名稱。 |

如果 EncryptionType 具有 null 或空白以外的任何其他值，則資料保護系統會在啟動時擲回例外狀況。

> [!WARNING]
> 在設定包含類型名稱的預設原則設定時 (EncryptionAlgorithmType、ValidationAlgorithmType、KeyEscrowSinks) ，應用程式必須能夠使用這些類型。 這表示，對於在桌面 CLR 上執行的應用程式，包含這些類型的元件應該存在於全域組件快取中 (GAC) 。 針對在 .NET Core 上執行的 ASP.NET Core 應用程式，應該安裝包含這些類型的封裝。
