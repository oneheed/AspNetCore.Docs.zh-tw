---
title: 防止 ASP.NET Core 中的開啟重新導向攻擊
author: ardalis
description: 示範如何防止針對 ASP.NET Core 應用程式的開啟重新導向攻擊
ms.author: riande
ms.date: 07/07/2017
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
uid: security/preventing-open-redirects
ms.openlocfilehash: e546cd852367921c7c694db3639f7a233f606e75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058385"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>防止 ASP.NET Core 中的開啟重新導向攻擊

重新導向至透過要求（例如 querystring 或表單資料）所指定之 URL 的 web 應用程式可能會遭篡改，以將使用者重新導向至外部的惡意 URL。 這種篡改稱為開放式重新導向攻擊。

每當您的應用程式邏輯重新導向至指定的 URL 時，您必須確認重新導向 URL 尚未遭到篡改。 ASP.NET Core 有內建的功能，可協助保護應用程式免于開啟重新導向 (也稱為開放式重新導向) 攻擊。

## <a name="what-is-an-open-redirect-attack"></a>什麼是開放式重新導向攻擊？

Web 應用程式會在存取需要驗證的資源時，經常將使用者重新導向至登入頁面。 重新導向通常會包含 `returnUrl` querystring 參數，讓使用者可以在成功登入後，返回原始要求的 URL。 使用者通過驗證之後，系統會將他們重新導向至他們原先要求的 URL。

因為目的地 URL 是在要求的查詢字串中指定，惡意使用者可能會篡改查詢字串。 遭篡改的 querystring 可能會允許網站將使用者重新導向至外部的惡意網站。 這項技術稱為「開啟重新導向 (或重新導向) 攻擊。

### <a name="an-example-attack"></a>攻擊範例

惡意使用者可能會開發出一種攻擊，目的是要讓惡意使用者存取使用者的認證或機密資訊。 若要開始攻擊，惡意使用者說服使用者按一下網站登入頁面的連結，並將 `returnUrl` querystring 值新增至 URL。 例如，假設有一個 `contoso.com` 包含登入頁面的應用程式 `http://contoso.com/Account/LogOn?returnUrl=/Home/About` 。 攻擊會遵循下列步驟：

1. 使用者按一下惡意連結來 `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (第二個 URL 是 "contoso **1** .com"，而非 "contoso.com" ) 。
2. 使用者成功登入。
3. 網站) 將使用者重新導向 (，以 `http://contoso1.com/Account/LogOn` (看似真實網站) 的惡意網站。
4. 使用者再次登入 (為惡意網站提供其認證) ，然後重新導向回到實際的網站。

使用者可能認為第一次嘗試登入失敗，而第二次嘗試成功。 使用者最可能仍不知道其認證已遭入侵。

![開啟重新導向攻擊進程](preventing-open-redirects/_static/open-redirection-attack-process.png)

除了登入頁面之外，某些網站還提供重新導向頁面或端點。 想像您的應用程式具有開啟重新導向的頁面 `/Home/Redirect` 。 例如，攻擊者可能會建立電子郵件中的連結 `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` 。 一般使用者會查看 URL，並以您的網站名稱開始查看。 信任這一點，他們會按一下該連結。 然後，開啟的重新導向會將使用者傳送到網路釣魚網站，看起來與您一樣，而且使用者可能會登入他們認為是您的網站。

## <a name="protecting-against-open-redirect-attacks"></a>防止開啟重新導向攻擊

開發 web 應用程式時，會將所有使用者提供的資料視為無法信任。 如果您的應用程式有可根據 URL 內容重新導向使用者的功能，請確定這類重新導向只會在您的應用程式 (或已知的 URL （而不是查詢字串) 中提供的任何 URL）中本機完成。

### <a name="localredirect"></a>LocalRedirect

`LocalRedirect`從基類使用 helper 方法 `Controller` ：

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` 如果指定了非本機 URL，將會擲回例外狀況。 否則，它的行為就像 `Redirect` 方法一樣。

### <a name="islocalurl"></a>IsLocalUrl

重新導向之前，請先使用 [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) 方法來測試 url：

下列範例顯示如何在重新導向之前檢查 URL 是否為本機。

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

`IsLocalUrl`方法會防止使用者不慎重新導向至惡意網站。 您可以記錄在預期本機 URL 的情況下提供非本機 URL 時所提供的 URL 詳細資料。 記錄重新導向 Url 可能有助於診斷重新導向的攻擊。
