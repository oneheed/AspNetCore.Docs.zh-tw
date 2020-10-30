---
title: ASP.NET Core 的取用者 Api 總覽
author: rick-anderson
description: 取得 ASP.NET Core 資料保護程式庫中可用的各種取用者 Api 的簡短總覽。
ms.author: riande
ms.date: 06/11/2019
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
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 485ea3f669b518f2979d04493b281bd116b05f65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051872"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET Core 的取用者 Api 總覽

`IDataProtectionProvider`和 `IDataProtector` 介面是取用者使用資料保護系統的基本介面。 它們位於 [AspNetCore. DataProtection 抽象](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 封裝中。

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

提供者介面代表資料保護系統的根。 它無法直接用來保護或解除保護資料。 相反地，取用者必須藉由呼叫來取得的參考 `IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)` ，其中目的是描述預期取用者使用案例的字串。 如需此參數的意圖以及如何選擇適當值的詳細資訊，請參閱 [目的字串](xref:security/data-protection/consumer-apis/purpose-strings) 。

## <a name="idataprotector"></a>>idataprotector

的呼叫會傳回保護裝置介面，這是取用 `CreateProtector` 者可用來執行保護和取消保護作業的介面。

若要保護資料片段，請將資料傳遞給 `Protect` 方法。 基本介面會定義轉換 byte []-> byte [] 的方法，但也有多載 (提供作為擴充方法，) 可轉換字串 > 字串。 這兩個方法所提供的安全性完全相同;開發人員應該選擇哪一種多載最方便使用。 不論選擇的多載，protected 方法所傳回的值現在都會受到保護， (enciphered 和校訂) ，而且應用程式可以將它傳送至不受信任的用戶端。

若要取消保護先前保護的資料片段，請將受保護的資料傳遞給 `Unprotect` 方法。  (有以 byte [] 為基礎和以字串為基礎的多載，可供開發人員使用。 ) 如果受保護的裝載是由先前的呼叫所產生 `Protect` `IDataProtector` ，則 `Unprotect` 方法會傳回原始未受保護的裝載。 如果受保護的承載已遭篡改或由不同的產生 `IDataProtector` ，則 `Unprotect` 方法會擲回 system.security.cryptography.cryptographicexception。

相同與不同的概念會與 `IDataProtector` 目的概念相同。 如果 `IDataProtector` 從相同的根產生兩個實例 `IDataProtectionProvider` ，但在呼叫中透過不同的目的字串來產生 `IDataProtectionProvider.CreateProtector` ，則會將它們視為 [不同的保護](xref:security/data-protection/consumer-apis/purpose-strings)裝置，而且無法取消保護另一個所產生的承載。

## <a name="consuming-these-interfaces"></a>使用這些介面

針對 DI 感知的元件，預期的用法是元件 `IDataProtectionProvider` 在其函式中採用參數，而 DI 系統會在元件具現化時，自動提供此服務。

> [!NOTE]
> 某些應用程式 (例如主控台應用程式或 ASP.NET 4.x 應用程式) 可能不是 DI 感知，所以無法使用此處所述的機制。 針對這些案例，請參閱 [非 Di 感知案例](xref:security/data-protection/configuration/non-di-scenarios) 檔，以取得取得提供者實例的詳細資訊， `IDataProtection` 而不需透過 DI。

下列範例示範三個概念：

1. [將資料保護系統新增](xref:security/data-protection/configuration/overview) 至服務容器，

2. 使用 DI 接收的實例 `IDataProtectionProvider` 、和

3. 從建立 `IDataProtector` `IDataProtectionProvider` ，然後使用它來保護和解除保護資料。

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

AspNetCore 的封裝包含擴充方法 `IServiceProvider.GetDataProtector` ，可做為開發人員的便利性。 它會封裝成單一作業 `IDataProtectionProvider` ，從服務提供者抓取，然後呼叫 `IDataProtectionProvider.CreateProtector` 。 下列範例會示範其使用方式。

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> 和的 `IDataProtectionProvider` 實例 `IDataProtector` 都是多個呼叫端的安全線程。 它的目的是只要元件透過呼叫取得的參考 `IDataProtector` `CreateProtector` ，它就會將該參考用於多個對和的呼叫 `Protect` `Unprotect` 。 `Unprotect`如果無法驗證或解密受保護的承載，的呼叫將會擲回 system.security.cryptography.cryptographicexception。 某些元件可能會想要忽略取消保護作業期間的錯誤;讀取驗證的元件 cookie 可能會處理此錯誤，並將要求視為完全沒有， cookie 而不是直接讓要求失敗。 需要此行為的元件應該明確地捕捉 System.security.cryptography.cryptographicexception，而不是抑制所有例外狀況。
