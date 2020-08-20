---
title: ASP.NET Core 資料保護
author: rick-anderson
description: 瞭解資料保護的概念，以及 ASP.NET Core 資料保護 Api 的設計原則。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/introduction
ms.openlocfilehash: 4f578e30a972b0d4ce5db08b2ec844e270c11406
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630077"
---
# <a name="aspnet-core-data-protection"></a>ASP.NET Core 資料保護

Web 應用程式通常需要儲存安全性敏感的資料。 Windows 為桌面應用程式提供 DPAPI，但這不適用於 web 應用程式。 ASP.NET Core 資料保護堆疊提供簡單、容易使用的密碼編譯 API，可供開發人員用來保護資料，包括金鑰管理和輪替。

ASP.NET Core 資料保護堆疊的設計目的是要做為 &lt; ASP.NET 1.x-4.x 中 machineKey 元素的長期取代 &gt; 。 它是設計用來解決舊密碼編譯堆疊的許多缺點，同時提供現成可用的解決方案，讓新式應用程式可能會遇到的大部分使用案例。

## <a name="problem-statement"></a>問題陳述

整體問題陳述可在單一句子中簡潔陳述：我需要保存受信任的資訊以供稍後抓取，但我不信任持續性機制。 在 web 詞彙中，這可能會撰寫為「我需要透過不受信任的用戶端來回存取受信任的狀態」。

這是驗證或持有人權杖的標準範例 cookie 。 伺服器會產生「我 Groot 並擁有 xyz 許可權」權杖，並將它交給用戶端。 在未來的某個日期，用戶端會將該權杖呈現回伺服器，但是伺服器需要某種程度的保證，用戶端尚未偽造權杖。 因此，第一項需求：真品 (也稱為 完整性，防篡改的) 。

因為保存的狀態是伺服器所信任的狀態，所以我們預期此狀態可能會包含操作環境的特定資訊。 這可能是檔案路徑的形式、許可權、控制碼或其他間接參考，或是其他部分的伺服器特定資料。 這類資訊通常不會公開給不受信任的用戶端。 因此，第二個需求：機密性。

最後，由於現代化的應用程式已元件化，我們看到的是，不論系統中的其他元件為何，個別的元件都不會使用這個系統。 比方說，如果持有人權杖元件使用此堆疊，則應該在不幹擾可能也使用相同堆疊的反 CSRF 機制的情況下運作。 因此，最終需求：隔離。

我們可以提供進一步的條件約束，以便縮小需求的範圍。 我們假設在 cryptosystem 中運作的所有服務都同樣受信任，而且不需要在直接控制下的服務外部產生或使用資料。 此外，我們還要求作業的速度越快，因為對 web 服務提出的每個要求可能會經歷 cryptosystem 一次或多次。 這使得對稱式密碼編譯適用于我們的案例，我們可以在需要的時間之前，對非對稱式加密進行折扣。

## <a name="design-philosophy"></a>設計原理

我們從找出現有堆疊的問題開始。 一旦這麼做之後，我們就能對現有解決方案的發展進行調查，並結束現有的解決方案，使其無法獲得我們所追求的功能。 接著，我們會根據數個指導準則來設計解決方案。

* 系統應該提供簡單的設定。 在理想的情況下，系統會是零設定，而開發人員可能會碰到執行的基礎。 在開發人員需要設定特定層面 (例如金鑰存放庫) 的情況下，應該提供考慮以簡化這些特定的設定。

* 提供簡單的取用者面向 API。 Api 應該很容易使用，而且很難正確使用。

* 開發人員不應學習金鑰管理原則。 系統應代表開發人員處理演算法選取和金鑰存留期。 在理想的情況下，開發人員甚至絕對不能存取原始金鑰資料。

* 如果可能的話，應該盡可能保護金鑰。 系統應找出適當的預設保護機制，並自動套用。

考慮到這些原則，我們開發了簡單、 [容易使用](xref:security/data-protection/using-data-protection) 的資料保護堆疊。

ASP.NET Core 的資料保護 Api 主要是供機密承載的無限持續性所用。 其他像是 [WINDOWS CNG DPAPI](/windows/win32/seccng/cng-dpapi) 和 [Azure Rights Management](/rights-management/) 的技術更適合不限數量的儲存體案例，而且它們也有增強的金鑰管理功能。 話雖如此，開發人員不會禁止開發人員使用 ASP.NET Core 資料保護 Api 來進行機密資料的長期保護。

## <a name="audience"></a>對象

資料保護系統分為五個主要套件。 這些 Api 的各個層面都以三個主要物件為目標;

1. 取用 [者 Api 概述](xref:security/data-protection/consumer-apis/overview) 目標應用程式和架構開發人員。

   「我不想要瞭解堆疊的運作方式或其設定方式。 我只想要盡可能簡單地以成功使用 Api 的機率來執行某些作業。」

2. 設定 [api](xref:security/data-protection/configuration/overview) 將目標設為應用程式開發人員和系統管理員。

   「我需要告訴資料保護系統，我的環境需要非預設的路徑或設定」。

3. 擴充性 Api 的目標是開發人員負責執行自訂原則。 這些 Api 的使用會限制在罕見的情況下，以及有經驗的安全感知開發人員。

   「我需要取代系統內的整個元件，因為我有真正獨特的行為需求。 我願意學習覆使用的 API 介面部分，以建立可滿足我需求的外掛程式。」

## <a name="package-layout"></a>封裝版面配置

資料保護堆疊由五個套件組成。

* [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 包含用 <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 來建立資料保護服務的和介面。 它也包含使用這些類型的實用擴充方法 (例如， [>idataprotector。保護](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)) 。 如果資料保護系統在其他地方具現化，而且您正在使用 API，請參考 `Microsoft.AspNetCore.DataProtection.Abstractions` 。

* [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) 包含資料保護系統的核心執行，包括核心密碼編譯作業、金鑰管理、設定和擴充性。 若要具現化資料保護系統 (例如，將它新增至 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>) 或修改或擴充其行為，請參考 `Microsoft.AspNetCore.DataProtection` 。

* [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 包含開發人員可能會覺得有用但不屬於核心套件的其他 api。 例如，此套件包含可具現化資料保護系統的 factory 方法，以將金鑰儲存在檔案系統上的位置，而不需要相依性插入 (請參閱 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>) 。 它也包含限制受保護承載之存留期的擴充方法 (請參閱 <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>) 。

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) 可以安裝到現有的 ASP.NET 4.x 應用程式，以將其作業重新導向至 `<machineKey>` 使用新的 ASP.NET Core 資料保護堆疊。 如需詳細資訊，請參閱<xref:security/data-protection/compatibility/replacing-machinekey>。

* [AspNetCore KeyDerivation](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) 提供 PBKDF2 密碼雜湊常式的執行，而且必須安全地處理使用者密碼的系統才能使用。 如需詳細資訊，請參閱<xref:security/data-protection/consumer-apis/password-hashing>。

## <a name="additional-resources"></a>其他資源

<xref:host-and-deploy/web-farm>
