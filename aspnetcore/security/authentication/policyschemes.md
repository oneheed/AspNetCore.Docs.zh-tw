---
title: ASP.NET Core 中的原則架構
author: rick-anderson
description: 驗證原則配置可讓您更輕鬆地擁有單一邏輯驗證配置
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 60ac9914ef811a705c61ab3b2bec61643acc6ec0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634978"
---
# <a name="policy-schemes-in-aspnet-core"></a>ASP.NET Core 中的原則架構

驗證原則配置可讓單一邏輯驗證配置更容易使用多種方法。 例如，原則配置可能會針對挑戰使用 Google 驗證，並針對 cookie 其他所有專案使用驗證。 驗證原則配置可讓它：

* 輕鬆將任何驗證動作轉寄至另一個配置。
* 根據要求進行動態轉寄。

使用衍生的所有驗證配置 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> 以及相關聯[的 \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)：

* 會在 ASP.NET Core 2.1 和更新版本中自動設定原則。
* 可以透過設定配置的選項來啟用。

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>範例

下列範例會顯示結合較低層級配置的較高層級配置。 Google 驗證會用於挑戰，而 cookie 驗證則用於其他所有專案：

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

下列範例會針對每個要求啟用動態選取的配置。 也就是說，如何混合 cookie 和 API 驗證：

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
