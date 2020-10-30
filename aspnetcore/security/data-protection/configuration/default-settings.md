---
title: ASP.NET Core 中的資料保護金鑰管理和存留期
author: rick-anderson
description: 瞭解 ASP.NET Core 中的資料保護金鑰管理和存留期。
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 1303c5c2c993f1d20383457666aebfa2a583e938
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053003"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>ASP.NET Core 中的資料保護金鑰管理和存留期

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>金鑰管理

應用程式會嘗試偵測其操作環境，並自行處理金鑰設定。

1. 如果應用程式裝載在 [Azure 應用程式](https://azure.microsoft.com/services/app-service/)中，金鑰會保存至 *%HOME%\ASP.NET\DataProtection-Keys* 資料夾。 此資料夾使用網路儲存體進行保存，並會在裝載應用程式的所有電腦上同步。
   * 金鑰待用時不受保護。
   * *DataProtection 金鑰* 資料夾會將金鑰通道提供給單一部署位置中應用程式的所有實例。
   * 各部署位置，例如預備和生產位置，不會共用金鑰環。 當您在部署位置（例如將預備環境移至生產環境或使用 A/B 測試）之間交換時，任何使用資料保護的應用程式將無法使用前一個插槽內的金鑰環來解密儲存的資料。 這會導致使用者登出使用標準 ASP.NET Core 驗證的應用程式 cookie ，因為它使用資料保護來保護它的 cookie 。 如果您想要與位置無關的按鍵環形，請使用外部金鑰環形提供者，例如 Azure Blob 儲存體、Azure Key Vault、SQL 存放區或 Redis 快取。

1. 如果有可用的使用者設定檔，則會將金鑰保存到 *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* 資料夾。 如果作業系統是 Windows，則會使用 DPAPI 將金鑰加密。

   應用程式集區的 [setProfileEnvironment 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)也必須啟用。 `setProfileEnvironment` 的預設值為 `true`。 在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。 如果金鑰並未如預期地儲存在使用者設定檔目錄中：

   1. 瀏覽至 *%windir%/system32/inetsrv/config* 資料夾。
   1. 開啟 *applicationHost.config* 檔案。
   1. 找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。
   1. 確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。

1. 如果應用程式是裝載在 IIS 中，則會將金鑰保存在僅碰到至背景工作進程帳戶的特殊登錄機碼中的 HKLM 登錄中。 在待用期間使用 DPAPI 加密金鑰。

1. 如果上述條件都不相符，則索引鍵不會在目前的進程之外保存。 當進程關機時，所有產生的金鑰都會遺失。

開發人員一律具有完整控制權，而且可以覆寫索引鍵的儲存方式和位置。 上述前三個選項應該針對大部分的應用程式提供良好的預設值，類似于 ASP.NET **\<machineKey>** 自動產生常式過去的運作方式。 最終的、回溯選項是唯一的案例，如果開發人員想要進行金鑰保存，則需要預先 [指定設定，](xref:security/data-protection/configuration/overview) 但此回退只會在罕見的情況下發生。

在 Docker 容器中裝載時，金鑰應保存在屬於 Docker 磁片區的資料夾中 (共用磁片區或裝載在容器存留期之外的主機掛接磁片區) 或外部提供者（例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 或 [Redis](https://redis.io/)）。 如果應用程式無法存取共用網路磁片區，則外部提供者也適用于 web 伺服陣列案例 (如需詳細資訊) ，請參閱 [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) 。

> [!WARNING]
> 如果開發人員覆寫上面所述的規則，並將資料保護系統指向特定的金鑰存放庫，則會停用待用金鑰的自動加密。 您可以透過設定來重新啟用靜止[保護。](xref:security/data-protection/configuration/overview)

## <a name="key-lifetime"></a>金鑰存留期

依預設，索引鍵的存留期為90天。 當金鑰過期時，應用程式會自動產生新的金鑰，並將新的金鑰設定為使用中的金鑰。 只要淘汰的金鑰保留在系統上，您的應用程式就可以解密任何受保護的資料。 如需詳細資訊，請參閱 [金鑰管理](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) 。

## <a name="default-algorithms"></a>預設演算法

使用的預設承載保護演算法是 AES-256-CBC，以提供機密性和 HMACSHA256 的真實性。 512位主要金鑰（每隔90天變更一次）是用來根據每個承載來衍生用於這些演算法的兩個子機碼。 如需詳細資訊，請參閱子機碼 [衍生](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) 。

## <a name="additional-resources"></a>其他資源

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
