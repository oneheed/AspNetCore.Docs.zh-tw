---
title: ASP.NET Core 中的金鑰永久性和金鑰設定
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護金鑰永久性 Api 的執行詳細資料。
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061245"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>ASP.NET Core 中的金鑰永久性和金鑰設定

一旦物件保存到備份存放區後，就會永久修正其標記法。 您可以將新資料新增至備份存放區，但永遠不會變化現有的資料。 這種行為的主要目的是防止資料損毀。

這種行為的一個結果是，一旦金鑰寫入至備份存放區，它就是不可變的。 它的建立、啟動和到期日永遠不會變更，但可以使用撤銷 `IKeyManager` 。 此外，其基礎演算法資訊、主要金鑰材質和靜態加密屬性也是不可變的。

如果開發人員變更任何會影響金鑰持續性的設定，則在下一次產生金鑰之前，這些變更將不會生效，而是透過明確呼叫 `IKeyManager.CreateNewKey` 或透過資料保護系統本身的 [自動化金鑰產生](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 行為。 影響金鑰持續性的設定如下：

* [預設金鑰存留期](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [靜態金鑰加密機制](xref:security/data-protection/implementation/key-encryption-at-rest)

* [包含在索引鍵中的演算法資訊](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

如果您需要這些設定，才能在下一個自動金鑰滾動時間開始，請考慮明確呼叫以 `IKeyManager.CreateNewKey` 強制建立新的金鑰。 請記得提供明確的啟用日期 ( {now + 2 天} 是很好的經驗法則，可讓變更在通話中傳播) 和到期日。

>[!TIP]
> 所有觸及存放庫的應用程式都應該使用擴充方法來指定相同的設定 `IDataProtectionBuilder` 。 否則，保存金鑰的屬性將取決於叫用金鑰產生常式的特定應用程式。
