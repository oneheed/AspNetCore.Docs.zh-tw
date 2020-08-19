---
title: ASP.NET Core 的授權簡介
author: rick-anderson
description: 瞭解授權的基本概念，以及授權在 ASP.NET Core 應用程式中的運作方式。
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
uid: security/authorization/introduction
ms.openlocfilehash: 7d7570ead1365588fd582d9bea364685da29a576
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634432"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>ASP.NET Core 的授權簡介

<a name="security-authorization-introduction"></a>

授權是指決定使用者能夠做什麼的處理常式。 例如，系統管理使用者可以建立文件庫、新增檔、編輯檔，以及將它們刪除。 使用媒體櫃的非系統管理使用者只會獲得讀取檔的授權。

授權是彼此獨立的，而且與驗證無關。 不過，授權需要驗證機制。 驗證是查明使用者的處理常式。 驗證可為目前使用者建立一或多個身分識別。

如需 ASP.NET Core 中驗證的詳細資訊，請參閱 <xref:security/authentication/index> 。

## <a name="authorization-types"></a>授權類型

ASP.NET Core 授權提供簡單、宣告式的 [角色](xref:security/authorization/roles) 和以 [原則為基礎](xref:security/authorization/policies) 的豐富模型。 授權是以需求表示，而處理常式會根據需求來評估使用者的宣告。 命令式檢查可以根據簡單的原則或原則來評估使用者嘗試存取之資源的使用者識別和屬性。

## <a name="namespaces"></a>命名空間

授權元件（包括 `AuthorizeAttribute` 和 `AllowAnonymousAttribute` 屬性）可在 `Microsoft.AspNetCore.Authorization` 命名空間中找到。

請參閱有關 [簡單授權](xref:security/authorization/simple)的檔。
