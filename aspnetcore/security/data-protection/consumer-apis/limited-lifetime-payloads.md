---
title: 限制 ASP.NET Core 中受保護承載的存留期
author: rick-anderson
description: 瞭解如何使用 ASP.NET Core 資料保護 Api 來限制受保護承載的存留期。
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 74417d076399066a49271c27ff128d9de6c10f94
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060790"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>限制 ASP.NET Core 中受保護承載的存留期

在某些情況下，應用程式開發人員想要建立在一段時間後過期的受保護承載。 比方說，受保護的承載可能代表密碼重設權杖，此權杖的有效期應只為一小時。 開發人員當然可以建立自己的裝載格式，其中包含內嵌的到期日，而且資深開發人員可能會想要這麼做，但對於管理這些過期的大多數開發人員來說，可能會變得很繁瑣。

為了讓我們的開發人員更容易使用， [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 包含公用程式 api，可用於建立可在一段時間後自動到期的裝載。 這些 Api 會停止回應 `ITimeLimitedDataProtector` 類型。

## <a name="api-usage"></a>API 使用方式

`ITimeLimitedDataProtector`介面是用來保護和取消保護限時/自我過期承載的核心介面。 若要建立的實例 `ITimeLimitedDataProtector` ，您首先需要以特定用途所建立的一般 [>idataprotector](xref:security/data-protection/consumer-apis/overview) 實例。 `IDataProtector`實例可供使用之後，請呼叫 `IDataProtector.ToTimeLimitedDataProtector` 擴充方法以取回具有內建到期功能的保護裝置。

`ITimeLimitedDataProtector` 公開下列 API 介面和擴充方法：

* CreateProtector (字串用途) ： ITimeLimitedDataProtector-此 API 類似于現有的 `IDataProtectionProvider.CreateProtector` ，可用來從根時間限制的保護裝置建立 [目的鏈](xref:security/data-protection/consumer-apis/purpose-strings) 。

* 保護 (byte [] 純文字，DateTimeOffset 到期) ： byte []

* 保護 (byte [] 純文字，TimeSpan 存留期) ： byte []

* 保護 (byte [] 純文字) ： byte []

* 保護 (字串純文字，DateTimeOffset 到期) ：字串

* 保護 (字串純文字、TimeSpan 存留期) ：字串

* 保護 (字串純文字) ：字串

除了 `Protect` 只接受純文字的核心方法之外，還有新的多載，可讓您指定承載的到期日。 您可以透過 `DateTimeOffset`) 或從目前系統時間 (的相對時間) ，將到期日指定為絕對日期 (`TimeSpan` 。 如果呼叫未採用到期時間的多載，則會假設承載永不過期。

* 取消保護 (byte [] protectedData，out DateTimeOffset 到期) ： byte []

* 取消保護 (byte [] protectedData) ： byte []

* 取消保護 (字串 protectedData，輸出 DateTimeOffset 到期) ：字串

* 取消保護 (字串 protectedData) ：字串

方法會傳回 `Unprotect` 原始未受保護的資料。 如果承載尚未過期，則會以選擇性 out 參數傳回絕對到期，以及原始未受保護的資料。 如果承載已過期，則取消保護方法的所有多載都會擲回 System.security.cryptography.cryptographicexception。

>[!WARNING]
> 不建議使用這些 Api 來保護需要長期或無限持續性的承載。 「我可以承受受保護的承載在一個月後永久無法復原嗎？」 可以作為最佳的經驗法則;如果答案為否，則開發人員應該考慮替代 Api。

下列範例會使用 [非 DI 程式碼路徑](xref:security/data-protection/configuration/non-di-scenarios) 來具現化資料保護系統。 若要執行這個範例，請確定您已先加入 AspNetCore. DataProtection 副檔名套件的參考。

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
