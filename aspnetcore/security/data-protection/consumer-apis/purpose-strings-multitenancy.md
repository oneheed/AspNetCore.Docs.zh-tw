---
title: ASP.NET Core 中的用途階層和多租使用者
author: rick-anderson
description: 瞭解用途字串階層和多租使用者，因為它與 ASP.NET Core 資料保護 Api 相關。
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
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 84714cd852a3cbc7ff77d0fd0c88931536dead1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052782"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>ASP.NET Core 中的用途階層和多租使用者

因為 `IDataProtector` 也是隱含的 `IDataProtectionProvider` ，所以目的可以連結在一起。 在這 `provider.CreateProtector([ "purpose1", "purpose2" ])` 種情況下，相當於 `provider.CreateProtector("purpose1").CreateProtector("purpose2")` 。

這可讓您透過資料保護系統取得一些有趣的階層式關聯性。 在先前的 [SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)範例中，SecureMessage 元件可以 `provider.CreateProtector("Contoso.Messaging.SecureMessage")` 事先呼叫一次，並將結果快取至私用 `_myProvider` 欄位。 然後，您可以透過呼叫來建立未來的保護裝置 `_myProvider.CreateProtector("User: username")` ，並使用這些保護裝置來保護個別訊息。

這也可以反轉。 請考慮使用單一邏輯應用程式來裝載多個租使用者 (CMS 看似合理) ，而且每個租使用者都可以使用自己的驗證和狀態管理系統來設定。 傘應用程式具有單一主要提供者，它會呼叫 `provider.CreateProtector("Tenant 1")` 並 `provider.CreateProtector("Tenant 2")` 為每個租使用者提供自己的資料保護系統隔離配量。 然後，租使用者可以根據自己的需求來衍生自己的個別保護裝置，但無論他們有多難嘗試建立保護裝置，並與系統中的任何其他租使用者發生衝突。 以圖形方式呈現，如下所示。

![多租使用者用途](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> 這假設應用程式中的應用程式會控制可供個別租使用者使用的 Api，而且租使用者無法在伺服器上執行任意程式碼。 如果租使用者可以執行任意程式碼，則可以執行私用反映來中斷隔離的保證，或直接讀取主要金鑰內容，並衍生所需的任何子機碼。

資料保護系統實際上會在其預設的現成設定中使用一類多重租用。 依預設，主要金鑰存放區會儲存在背景工作進程帳戶的使用者設定檔資料夾 (或登錄中，) 的 IIS 應用程式集區身分識別。 但是，使用單一帳戶來執行多個應用程式其實相當常見，因此這些應用程式最後都會共用主要的金鑰資料。 為了解決此問題，資料保護系統會自動將唯一的每個應用程式識別碼插入為整體用途鏈中的第一個元素。 此隱含用途可將每個應用程式有效地視為系統內的唯一租使用者，以 [隔離個別的應用](xref:security/data-protection/configuration/overview#per-application-isolation) 程式，而保護裝置建立程式看起來與上圖相同。
