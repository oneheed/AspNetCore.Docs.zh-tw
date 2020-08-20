---
title: ASP.NET Core 中的目的字串
author: rick-anderson
description: 瞭解如何在 ASP.NET Core 資料保護 Api 中使用目的字串。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 1119c45570338f629a3ab7adbd43361529aa23e7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626918"
---
# <a name="purpose-strings-in-aspnet-core"></a>ASP.NET Core 中的目的字串

<a name="data-protection-consumer-apis-purposes"></a>

使用的元件 `IDataProtectionProvider` 必須將唯一的 *目的* 參數傳遞給 `CreateProtector` 方法。 目的參數是資料保護系統安全性的固有 *參數* ，因為它會在密碼編譯取用者之間提供隔離，即使根密碼編譯金鑰相同也是一樣。

當取用者指定目的時，目的字串會與根密碼編譯金鑰搭配使用，以衍生該取用者唯一的密碼編譯子機碼。 這會將取用者與應用程式中的所有其他密碼編譯取用者隔離：其他元件無法讀取其承載，而且無法讀取任何其他元件的裝載。 這項隔離也會針對元件呈現不可行的整個攻擊類別。

![目的圖範例](purpose-strings/_static/purposes.png)

在上圖中， `IDataProtector` 實例 A 和 B **無法** 讀取彼此本身的承載。

目的字串不必是秘密。 它應該是唯一的，因為沒有其他正常運作的元件也會提供相同的目的字串。

>[!TIP]
> 使用取用資料保護 Api 之元件的命名空間和類型名稱，是很好的經驗法則，如同實務，這項資訊永遠不會發生衝突。
>
>負責 minting 持有人權杖的 Contoso 撰寫元件可能會使用 >bearertoken 做為其目的字串。 或更棒的是，它可能會使用 >bearertoken 做為其目的字串。 附加版本號碼可讓未來的版本使用 >bearertoken 做為其目的，而不同的版本將會完全與承載進行隔離。

由於的目的參數 `CreateProtector` 是字串陣列，因此可以改為指定為 `[ "Contoso.Security.BearerToken", "v1" ]` 。 這可讓您建立用途階層，並開啟具有資料保護系統的多租使用者案例的可能性。

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> 元件不應允許不受信任的使用者輸入成為目的鏈的唯一輸入來源。
>
>例如，假設有一個 SecureMessage 的元件，負責儲存安全訊息。 如果要呼叫安全訊息元件 `CreateProtector([ username ])` ，惡意使用者可能會在嘗試取得要呼叫的元件時，建立具有使用者名稱 ">bearertoken" 的帳戶 `CreateProtector([ "Contoso.Security.BearerToken" ])` ，因此不小心導致安全訊息系統 mint 可能被視為驗證權杖的承載。
>
>訊息元件的更好用途是 `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` 提供適當的隔離。

提供的隔離和、和用途的行為如下所示 `IDataProtectionProvider` `IDataProtector` ：

* 針對指定的 `IDataProtectionProvider` 物件， `CreateProtector` 方法會建立唯一系結 `IDataProtector` 至 `IDataProtectionProvider` 建立它之物件的物件，以及傳遞至方法的目的參數。

* 目的參數不得為 null。  (如果目的是指定為數組，這表示陣列的長度不得為零，而且陣列的所有元素都必須為非 null ) 。在技術上，您可以使用空的字串目的，但不建議使用。

* 只有當兩個用途引數包含相同的字串時，才會使用相同的順序， (使用序數比較子) 。 單一用途引數相當於對應的單一元素目的陣列。

* 如果兩個 `IDataProtector` 物件是從 `IDataProtectionProvider` 具有對等用途參數的對等物件建立，則這兩個物件都是相等的。

* 針對指定的 `IDataProtector` 物件， `Unprotect(protectedData)` `unprotectedData` 如果 `protectedData := Protect(unprotectedData)` 對等物件，的呼叫將會傳回原始的 `IDataProtector` 。

> [!NOTE]
> 我們不考慮到某些元件刻意選擇與另一個元件衝突的目的字串。 這類元件基本上會被視為惡意的，而此系統不是為了在背景工作進程內執行惡意程式碼時提供安全性保證。
