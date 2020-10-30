---
title: ASP.NET Core 中的金鑰管理
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護金鑰管理 Api 的執行詳細資料。
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: f7e0fa5e80208469188a11239a9453ecafdcf5f3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060192"
---
# <a name="key-management-in-aspnet-core"></a>ASP.NET Core 中的金鑰管理

<a name="data-protection-implementation-key-management"></a>

資料保護系統會自動管理用來保護和取消保護承載的主要金鑰存留期。 每個金鑰可以有四個階段的其中一個：

* 已建立-金鑰已存在於金鑰環中，但尚未啟用。 在經過足夠的時間之後，金鑰已經有機會傳播到所有使用此金鑰通道的電腦，因此不應將金鑰用於新的保護作業。

* 作用中-金鑰會存在於金鑰環中，並應用於所有新的保護作業。

* 已過期-金鑰已執行其自然存留期，不應再用於新的保護作業。

* 已撤銷-金鑰已遭入侵，不得用於新的保護作業。

建立、使用中和過期的金鑰可能全都用來取消保護傳入的裝載。 依預設，撤銷的金鑰可能不會用來取消保護承載，但應用程式開發人員可以在必要時覆 [寫此行為](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) 。

>[!WARNING]
> 開發人員可能會想要從金鑰環形刪除金鑰 (例如，從檔案系統) 中刪除對應的檔案。 屆時，受金鑰保護的所有資料都會永久 undecipherable，而且沒有任何緊急覆寫，就像是撤銷金鑰一樣。 刪除金鑰是真正的破壞性行為，因此，資料保護系統不會公開執行此作業的第一級 API。

## <a name="default-key-selection"></a>預設索引鍵選取專案

當資料保護系統從支援存放庫讀取金鑰環形時，它會嘗試從金鑰通道找出「預設」金鑰。 預設金鑰用於新的保護作業。

一般的啟發式是，資料保護系統會選擇以最新的啟用日期做為預設金鑰的金鑰。  (有一個小巧克力因數可允許伺服器對伺服器的時鐘誤差。 ) 如果金鑰已過期或已撤銷，而且應用程式未停用自動產生金鑰，則會產生新的金鑰，並根據以下的 [金鑰到期和](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) 輪流原則立即啟用。

如果資料保護系統立即產生新的金鑰，而不是改回不同的金鑰，則應該將新的金鑰產生視為在新金鑰之前啟用的所有金鑰的隱含到期時間。 一般的概念是，新的金鑰可能已使用不同的演算法或靜態加密機制來設定，而不是使用舊的金鑰，而系統應優先使用目前的設定。

發生例外狀況。 如果應用程式開發人員已 [停用自動產生金鑰](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)，則資料保護系統必須選擇做為預設金鑰。 在此回溯案例中，系統會選擇具有最新啟用日期的非撤銷金鑰，並提供指定給有時間可傳播至叢集中其他電腦的金鑰的喜好設定。 然後，回復系統最後可能會選擇過期的預設金鑰。 回退系統永遠不會選擇撤銷的金鑰做為預設金鑰，而且如果金鑰通道是空的或每個金鑰都已遭撤銷，則系統會在初始化時產生錯誤。

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>金鑰到期和輪流

建立金鑰時，系統會自動將啟用日期指定為 {now + 2 天} 且到期日為 {now + 90 天}。 啟用前的2天延遲可提供金鑰時間給整個系統。 也就是說，它可讓指向備份存放區的其他應用程式在下一次自動重新整理期間觀察金鑰，因此最大限度的是當金鑰環形變成作用中時，它會傳播至可能需要使用它的所有應用程式。

如果預設金鑰將在2天內到期，而且如果金鑰通道還沒有金鑰在預設金鑰到期時處於作用中狀態，則資料保護系統會自動將新的金鑰保存到金鑰通道。 此新金鑰的啟用日期為 {預設金鑰的到期日}，到期日為 {now + 90 天}。 如此一來，系統就會定期自動變換金鑰，而不會中斷服務。

在某些情況下，您可能會建立具有立即啟用的金鑰。 其中一個範例是應用程式未執行一段時間，且金鑰通道中的所有金鑰都已過期。 發生這種情況時，系統會將金鑰的啟用日期指定為 {now}，而不會有正常的2天啟用延遲。

預設的金鑰存留期為90天，不過這可以設定，如下列範例所示。

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

系統管理員也可以變更預設的全系統，不過明確的呼叫 `SetDefaultKeyLifetime` 將會覆寫任何全系統原則。 預設的金鑰存留期不能短于7天。

## <a name="automatic-key-ring-refresh"></a>自動金鑰響鈴更新

當資料保護系統初始化時，它會從基礎存放庫讀取金鑰信號，並將它快取在記憶體中。 此快取可讓您在不點擊備份存放區的情況下繼續進行保護和取消保護作業。 系統會每隔24小時或目前的預設金鑰到期（以先達到者為准）時，自動檢查備份存放區的變更。

>[!WARNING]
> 如果) 需要直接使用金鑰管理 Api，開發人員應該很少 (。 資料保護系統將會依照上述方式執行自動金鑰管理。

資料保護系統會公開介面 `IKeyManager` ，可用來檢查及變更金鑰環形。 提供實例的 DI 系統 `IDataProtectionProvider` 也可以 `IKeyManager` 為您的耗用量提供的實例。 或者，您可以 `IKeyManager` 直接從中提取， `IServiceProvider` 如下列範例所示。

修改金鑰環形 (明確地建立新金鑰或執行撤銷) 的任何作業，都會使記憶體中的快取失效。 下一次呼叫 `Protect` 或 `Unprotect` 時，將會導致資料保護系統重新讀取金鑰環形並重新建立快取。

下列範例示範 `IKeyManager` 如何使用介面檢查和操作金鑰通道，包括撤銷現有的金鑰，以及手動產生新的金鑰。

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>金鑰儲存體

資料保護系統具有啟發學習法，它會嘗試自動推算適當的金鑰儲存位置和靜態加密機制。 金鑰持續性機制也可透過應用程式開發人員進行設定。 下列檔討論這些機制的內建實施：

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
