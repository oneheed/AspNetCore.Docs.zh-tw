---
title: 防止 ASP.NET Core 中的開啟重新導向攻擊
author: ardalis
description: 示範如何防止針對 ASP.NET Core 應用程式的開啟重新導向攻擊
ms.author: riande
ms.date: 07/07/2017
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
uid: security/preventing-open-redirects
ms.openlocfilehash: 5226e301960a56145b94b6128d0034c40b86bffd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633457"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="a652b-103">防止 ASP.NET Core 中的開啟重新導向攻擊</span><span class="sxs-lookup"><span data-stu-id="a652b-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="a652b-104">重新導向至透過要求（例如 querystring 或表單資料）所指定之 URL 的 web 應用程式可能會遭篡改，以將使用者重新導向至外部的惡意 URL。</span><span class="sxs-lookup"><span data-stu-id="a652b-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="a652b-105">這種篡改稱為開放式重新導向攻擊。</span><span class="sxs-lookup"><span data-stu-id="a652b-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="a652b-106">每當您的應用程式邏輯重新導向至指定的 URL 時，您必須確認重新導向 URL 尚未遭到篡改。</span><span class="sxs-lookup"><span data-stu-id="a652b-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="a652b-107">ASP.NET Core 有內建的功能，可協助保護應用程式免于開啟重新導向 (也稱為開放式重新導向) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="a652b-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="a652b-108">什麼是開放式重新導向攻擊？</span><span class="sxs-lookup"><span data-stu-id="a652b-108">What is an open redirect attack?</span></span>

<span data-ttu-id="a652b-109">Web 應用程式會在存取需要驗證的資源時，經常將使用者重新導向至登入頁面。</span><span class="sxs-lookup"><span data-stu-id="a652b-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="a652b-110">重新導向通常會包含 `returnUrl` querystring 參數，讓使用者可以在成功登入後，返回原始要求的 URL。</span><span class="sxs-lookup"><span data-stu-id="a652b-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="a652b-111">使用者通過驗證之後，系統會將他們重新導向至他們原先要求的 URL。</span><span class="sxs-lookup"><span data-stu-id="a652b-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="a652b-112">因為目的地 URL 是在要求的查詢字串中指定，惡意使用者可能會篡改查詢字串。</span><span class="sxs-lookup"><span data-stu-id="a652b-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="a652b-113">遭篡改的 querystring 可能會允許網站將使用者重新導向至外部的惡意網站。</span><span class="sxs-lookup"><span data-stu-id="a652b-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="a652b-114">這項技術稱為「開啟重新導向 (或重新導向) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="a652b-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="a652b-115">攻擊範例</span><span class="sxs-lookup"><span data-stu-id="a652b-115">An example attack</span></span>

<span data-ttu-id="a652b-116">惡意使用者可能會開發出一種攻擊，目的是要讓惡意使用者存取使用者的認證或機密資訊。</span><span class="sxs-lookup"><span data-stu-id="a652b-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="a652b-117">若要開始攻擊，惡意使用者說服使用者按一下網站登入頁面的連結，並將 `returnUrl` querystring 值新增至 URL。</span><span class="sxs-lookup"><span data-stu-id="a652b-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="a652b-118">例如，假設有一個 `contoso.com` 包含登入頁面的應用程式 `http://contoso.com/Account/LogOn?returnUrl=/Home/About` 。</span><span class="sxs-lookup"><span data-stu-id="a652b-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="a652b-119">攻擊會遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="a652b-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="a652b-120">使用者按一下惡意連結來 `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (第二個 URL 是 "contoso**1**.com"，而非 "contoso.com" ) 。</span><span class="sxs-lookup"><span data-stu-id="a652b-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="a652b-121">使用者成功登入。</span><span class="sxs-lookup"><span data-stu-id="a652b-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="a652b-122">網站) 將使用者重新導向 (，以 `http://contoso1.com/Account/LogOn` (看似真實網站) 的惡意網站。</span><span class="sxs-lookup"><span data-stu-id="a652b-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="a652b-123">使用者再次登入 (為惡意網站提供其認證) ，然後重新導向回到實際的網站。</span><span class="sxs-lookup"><span data-stu-id="a652b-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="a652b-124">使用者可能認為第一次嘗試登入失敗，而第二次嘗試成功。</span><span class="sxs-lookup"><span data-stu-id="a652b-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="a652b-125">使用者最可能仍不知道其認證已遭入侵。</span><span class="sxs-lookup"><span data-stu-id="a652b-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![開啟重新導向攻擊進程](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="a652b-127">除了登入頁面之外，某些網站還提供重新導向頁面或端點。</span><span class="sxs-lookup"><span data-stu-id="a652b-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="a652b-128">想像您的應用程式具有開啟重新導向的頁面 `/Home/Redirect` 。</span><span class="sxs-lookup"><span data-stu-id="a652b-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="a652b-129">例如，攻擊者可能會建立電子郵件中的連結 `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` 。</span><span class="sxs-lookup"><span data-stu-id="a652b-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="a652b-130">一般使用者會查看 URL，並以您的網站名稱開始查看。</span><span class="sxs-lookup"><span data-stu-id="a652b-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="a652b-131">信任這一點，他們會按一下該連結。</span><span class="sxs-lookup"><span data-stu-id="a652b-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="a652b-132">然後，開啟的重新導向會將使用者傳送到網路釣魚網站，看起來與您一樣，而且使用者可能會登入他們認為是您的網站。</span><span class="sxs-lookup"><span data-stu-id="a652b-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="a652b-133">防止開啟重新導向攻擊</span><span class="sxs-lookup"><span data-stu-id="a652b-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="a652b-134">開發 web 應用程式時，會將所有使用者提供的資料視為無法信任。</span><span class="sxs-lookup"><span data-stu-id="a652b-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="a652b-135">如果您的應用程式有可根據 URL 內容重新導向使用者的功能，請確定這類重新導向只會在您的應用程式 (或已知的 URL （而不是查詢字串) 中提供的任何 URL）中本機完成。</span><span class="sxs-lookup"><span data-stu-id="a652b-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="a652b-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="a652b-136">LocalRedirect</span></span>

<span data-ttu-id="a652b-137">`LocalRedirect`從基類使用 helper 方法 `Controller` ：</span><span class="sxs-lookup"><span data-stu-id="a652b-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="a652b-138">`LocalRedirect` 如果指定了非本機 URL，將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a652b-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="a652b-139">否則，它的行為就像 `Redirect` 方法一樣。</span><span class="sxs-lookup"><span data-stu-id="a652b-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="a652b-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="a652b-140">IsLocalUrl</span></span>

<span data-ttu-id="a652b-141">重新導向之前，請先使用 [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) 方法來測試 url：</span><span class="sxs-lookup"><span data-stu-id="a652b-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="a652b-142">下列範例顯示如何在重新導向之前檢查 URL 是否為本機。</span><span class="sxs-lookup"><span data-stu-id="a652b-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="a652b-143">`IsLocalUrl`方法會防止使用者不慎重新導向至惡意網站。</span><span class="sxs-lookup"><span data-stu-id="a652b-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="a652b-144">您可以記錄在預期本機 URL 的情況下提供非本機 URL 時所提供的 URL 詳細資料。</span><span class="sxs-lookup"><span data-stu-id="a652b-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="a652b-145">記錄重新導向 Url 可能有助於診斷重新導向的攻擊。</span><span class="sxs-lookup"><span data-stu-id="a652b-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
