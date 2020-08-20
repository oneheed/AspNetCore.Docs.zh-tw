---
title: 防止跨網站偽造要求 (XSRF/CSRF) 攻擊 ASP.NET Core
author: steve-smith
description: 探索如何防止惡意網站可能影響用戶端瀏覽器和應用程式之間互動的 web 應用程式遭受攻擊。
ms.author: riande
ms.custom: mvc
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
uid: security/anti-request-forgery
ms.openlocfilehash: d0cce4f48151ab56774ab28eb6d89a687b3747af
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635121"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="87154-103">防止跨網站偽造要求 (XSRF/CSRF) 攻擊 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87154-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="87154-104">依 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="87154-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="87154-105">跨網站要求偽造 (也稱為 XSRF 或 CSRF) 是針對 web 裝載的應用程式進行攻擊，而惡意的 web 應用程式可能會影響用戶端瀏覽器與信任該瀏覽器的 web 應用程式之間的互動。</span><span class="sxs-lookup"><span data-stu-id="87154-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="87154-106">這些攻擊是可能的，因為 web 瀏覽器會在每個網站要求時自動傳送某些類型的驗證權杖。</span><span class="sxs-lookup"><span data-stu-id="87154-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="87154-107">這種形式的惡意探索也稱為單鍵 *攻擊* 或 *會話騎* ，因為攻擊會利用使用者先前經過驗證的會話。</span><span class="sxs-lookup"><span data-stu-id="87154-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="87154-108">CSRF 攻擊的範例：</span><span class="sxs-lookup"><span data-stu-id="87154-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="87154-109">使用者 `www.good-banking-site.com` 使用表單驗證登入。</span><span class="sxs-lookup"><span data-stu-id="87154-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="87154-110">伺服器會驗證使用者，併發出包含驗證的回應 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="87154-111">網站很容易受到攻擊，因為它會信任透過有效驗證所收到的任何要求 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="87154-112">使用者造訪惡意網站 `www.bad-crook-site.com` 。</span><span class="sxs-lookup"><span data-stu-id="87154-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="87154-113">惡意網站 `www.bad-crook-site.com` 包含類似下面的 HTML 表單：</span><span class="sxs-lookup"><span data-stu-id="87154-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="87154-114">請注意，表單的 `action` 文章會張貼到易受攻擊的網站，而不是惡意網站。</span><span class="sxs-lookup"><span data-stu-id="87154-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="87154-115">這是 CSRF 的「跨網站」部分。</span><span class="sxs-lookup"><span data-stu-id="87154-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="87154-116">使用者選取 [提交] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="87154-116">The user selects the submit button.</span></span> <span data-ttu-id="87154-117">瀏覽器會提出要求，並自動包含要求之網域的驗證 cookie `www.good-banking-site.com` 。</span><span class="sxs-lookup"><span data-stu-id="87154-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="87154-118">要求會在伺服器上 `www.good-banking-site.com` 以使用者的驗證內容執行，而且可以執行已驗證的使用者可以執行的任何動作。</span><span class="sxs-lookup"><span data-stu-id="87154-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="87154-119">除了使用者選取按鈕來提交表單的情況之外，惡意網站也可能：</span><span class="sxs-lookup"><span data-stu-id="87154-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="87154-120">執行自動提交表單的腳本。</span><span class="sxs-lookup"><span data-stu-id="87154-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="87154-121">以 AJAX 要求形式傳送表單提交。</span><span class="sxs-lookup"><span data-stu-id="87154-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="87154-122">使用 CSS 隱藏表單。</span><span class="sxs-lookup"><span data-stu-id="87154-122">Hide the form using CSS.</span></span>

<span data-ttu-id="87154-123">除了一開始造訪惡意網站之外，這些替代案例不需要使用者採取任何動作或輸入。</span><span class="sxs-lookup"><span data-stu-id="87154-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="87154-124">使用 HTTPS 不會防止 CSRF 攻擊。</span><span class="sxs-lookup"><span data-stu-id="87154-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="87154-125">惡意網站可以傳送要求， `https://www.good-banking-site.com/` 就像傳送不安全的要求一樣簡單。</span><span class="sxs-lookup"><span data-stu-id="87154-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="87154-126">某些攻擊會以回應 GET 要求的端點為目標，在這種情況下，可以使用影像標記來執行動作。</span><span class="sxs-lookup"><span data-stu-id="87154-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="87154-127">這種形式的攻擊常見於允許影像但封鎖 JavaScript 的論壇網站。</span><span class="sxs-lookup"><span data-stu-id="87154-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="87154-128">變更 GET 要求狀態（變更變數或資源）的應用程式很容易受到惡意攻擊。</span><span class="sxs-lookup"><span data-stu-id="87154-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="87154-129">**取得變更狀態的要求不安全。最佳做法是永遠不要變更 GET 要求的狀態。**</span><span class="sxs-lookup"><span data-stu-id="87154-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="87154-130">使用進行驗證的 web 應用程式可能會 CSRF 攻擊， cookie 因為：</span><span class="sxs-lookup"><span data-stu-id="87154-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="87154-131">cookieWeb 應用程式所發行的瀏覽器存放區。</span><span class="sxs-lookup"><span data-stu-id="87154-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="87154-132">儲存 cookie cookie 的包含已驗證使用者的會話 s。</span><span class="sxs-lookup"><span data-stu-id="87154-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="87154-133">瀏覽器會 cookie 在每次要求時，將所有與網域相關聯的所有傳送至 web 應用程式，不論在瀏覽器中產生應用程式要求的方式為何。</span><span class="sxs-lookup"><span data-stu-id="87154-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="87154-134">不過，CSRF 攻擊並不限於利用 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="87154-135">例如，基本和摘要式驗證也很容易受到攻擊。</span><span class="sxs-lookup"><span data-stu-id="87154-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="87154-136">使用者使用基本或摘要式驗證登入之後，瀏覽器會自動傳送認證，直到會話 &dagger; 結束為止。</span><span class="sxs-lookup"><span data-stu-id="87154-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="87154-137">&dagger;在此內容中， *會話* 是指驗證使用者的用戶端會話。</span><span class="sxs-lookup"><span data-stu-id="87154-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="87154-138">它與伺服器端會話或 [ASP.NET Core 會話中介軟體](xref:fundamentals/app-state)無關。</span><span class="sxs-lookup"><span data-stu-id="87154-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="87154-139">使用者可以採取預防措施來防止 CSRF 弱點：</span><span class="sxs-lookup"><span data-stu-id="87154-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="87154-140">使用完畢後，請登出 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="87154-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="87154-141">定期清除瀏覽器 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="87154-142">不過，CSRF 弱點基本上是 web 應用程式的問題，而不是終端使用者。</span><span class="sxs-lookup"><span data-stu-id="87154-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="87154-143">驗證基本概念</span><span class="sxs-lookup"><span data-stu-id="87154-143">Authentication fundamentals</span></span>

<span data-ttu-id="87154-144">Cookie以驗證為基礎的驗證是一種常見的驗證形式。</span><span class="sxs-lookup"><span data-stu-id="87154-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="87154-145">以權杖為基礎的驗證系統越來越普及，特別是針對單一頁面應用程式 (Spa) 。</span><span class="sxs-lookup"><span data-stu-id="87154-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="87154-146">Cookie以驗證為基礎</span><span class="sxs-lookup"><span data-stu-id="87154-146">Cookie-based authentication</span></span>

<span data-ttu-id="87154-147">當使用者使用其使用者名稱和密碼進行驗證時，就會發出權杖，其中包含可用於驗證和授權的驗證票證。</span><span class="sxs-lookup"><span data-stu-id="87154-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="87154-148">權杖會儲存為 cookie 每個用戶端所發出的要求。</span><span class="sxs-lookup"><span data-stu-id="87154-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="87154-149">產生和驗證此程式 cookie 是由 Cookie 驗證中介軟體所執行。</span><span class="sxs-lookup"><span data-stu-id="87154-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="87154-150">[中介軟體](xref:fundamentals/middleware/index)會將使用者主體序列化為加密 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="87154-151">在後續的要求中，中介軟體會驗證、重新建立 cookie 主體，並將主體指派給[HttpCoNtext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)的[使用者](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)屬性。</span><span class="sxs-lookup"><span data-stu-id="87154-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="87154-152">權杖式驗證</span><span class="sxs-lookup"><span data-stu-id="87154-152">Token-based authentication</span></span>

<span data-ttu-id="87154-153">當使用者經過驗證時，就會發出權杖， (不是 antiforgery 權杖) 。</span><span class="sxs-lookup"><span data-stu-id="87154-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="87154-154">權杖包含 [宣告](/dotnet/framework/security/claims-based-identity-model) 形式的使用者資訊，或將應用程式指向應用程式中維護之使用者狀態的參考權杖。</span><span class="sxs-lookup"><span data-stu-id="87154-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="87154-155">當使用者嘗試存取需要驗證的資源時，權杖會以持有人權杖形式以其他授權標頭的形式傳送至應用程式。</span><span class="sxs-lookup"><span data-stu-id="87154-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="87154-156">這會讓應用程式成為無狀態。</span><span class="sxs-lookup"><span data-stu-id="87154-156">This makes the app stateless.</span></span> <span data-ttu-id="87154-157">在每個後續要求中，權杖會在伺服器端驗證的要求中傳遞。</span><span class="sxs-lookup"><span data-stu-id="87154-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="87154-158">此權杖未 *加密*;它經過 *編碼*。</span><span class="sxs-lookup"><span data-stu-id="87154-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="87154-159">在伺服器上，權杖會解碼以存取其資訊。</span><span class="sxs-lookup"><span data-stu-id="87154-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="87154-160">若要在後續要求時傳送權杖，請將權杖儲存在瀏覽器的本機儲存體中。</span><span class="sxs-lookup"><span data-stu-id="87154-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="87154-161">如果權杖儲存在瀏覽器的本機儲存體中，請不要擔心 CSRF 弱點。</span><span class="sxs-lookup"><span data-stu-id="87154-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="87154-162">當令牌儲存在中時，CSRF 是一項考慮 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="87154-163">如需詳細資訊，請參閱 GitHub 問題 [SPA 程式碼範例 cookie 會新增兩個](https://github.com/dotnet/AspNetCore.Docs/issues/13369)。</span><span class="sxs-lookup"><span data-stu-id="87154-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="87154-164">裝載于一個網域的多個應用程式</span><span class="sxs-lookup"><span data-stu-id="87154-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="87154-165">共用裝載環境容易遭受會話劫持、登入 CSRF 和其他攻擊。</span><span class="sxs-lookup"><span data-stu-id="87154-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="87154-166">雖然 `example1.contoso.net` 和 `example2.contoso.net` 是不同的主機，但是網域下的主機之間會有隱含的信任關係 `*.contoso.net` 。</span><span class="sxs-lookup"><span data-stu-id="87154-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="87154-167">此隱含信任關聯性可讓可能不受信任的主機影響彼此， cookie (管理 AJAX 要求的相同原始原則不一定適用于 HTTP cookie s) 。</span><span class="sxs-lookup"><span data-stu-id="87154-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="87154-168">在相同網域上裝載的應用程式之間惡意探索受信任的攻擊，並 cookie 不會共用網域來防止攻擊。</span><span class="sxs-lookup"><span data-stu-id="87154-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="87154-169">當每個應用程式裝載于自己的網域時，不會有隱含的 cookie 信任關係可進行攻擊。</span><span class="sxs-lookup"><span data-stu-id="87154-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="87154-170">ASP.NET Core antiforgery 設定</span><span class="sxs-lookup"><span data-stu-id="87154-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="87154-171">ASP.NET Core 使用 [ASP.NET Core 資料保護](xref:security/data-protection/introduction)來實行 antiforgery。</span><span class="sxs-lookup"><span data-stu-id="87154-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="87154-172">資料保護堆疊必須設定為可在伺服器陣列中運作。</span><span class="sxs-lookup"><span data-stu-id="87154-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="87154-173">如需詳細資訊，請參閱設定 [資料保護](xref:security/data-protection/configuration/overview) 。</span><span class="sxs-lookup"><span data-stu-id="87154-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="87154-174">在中呼叫下列其中一個 Api 時，會將 Antiforgery 中介軟體新增至相依性 [插入](xref:fundamentals/dependency-injection) 容器中 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="87154-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87154-175">當呼叫時，Antiforgery 中介軟體會新增至相依性[插入](xref:fundamentals/dependency-injection)容器 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="87154-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="87154-176">在 ASP.NET Core 2.0 或更新版本中， [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 會將 antiforgery token 插入至 HTML 表單元素。</span><span class="sxs-lookup"><span data-stu-id="87154-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="87154-177">檔案中的下列標記 Razor 會自動產生 antiforgery 權杖：</span><span class="sxs-lookup"><span data-stu-id="87154-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="87154-178">同樣地，如果表單的方法未取得，則 [IHtmlHelper](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) 預設會產生 antiforgery 權杖。</span><span class="sxs-lookup"><span data-stu-id="87154-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="87154-179">當 `<form>` 標籤包含 `method="post"` 屬性，且下列其中一個條件成立時，就會自動產生 HTML form 元素的 antiforgery token：</span><span class="sxs-lookup"><span data-stu-id="87154-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="87154-180">Action 屬性是空的 (`action=""`) 。</span><span class="sxs-lookup"><span data-stu-id="87154-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="87154-181"> () 未提供 action 屬性 `<form method="post">` 。</span><span class="sxs-lookup"><span data-stu-id="87154-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="87154-182">您可以停用 HTML 表單元素的自動產生 antiforgery token：</span><span class="sxs-lookup"><span data-stu-id="87154-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="87154-183">使用屬性明確停用 antiforgery 權杖 `asp-antiforgery` ：</span><span class="sxs-lookup"><span data-stu-id="87154-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="87154-184">您可以使用標籤協助程式 [！退出符號](xref:mvc/views/tag-helpers/intro#opt-out)，選擇不使用標記協助程式的表單元素：</span><span class="sxs-lookup"><span data-stu-id="87154-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="87154-185">`FormTagHelper`從 view 中移除。</span><span class="sxs-lookup"><span data-stu-id="87154-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="87154-186">您 `FormTagHelper` 可以藉由將下列指示詞新增至視圖，從視圖中移除 Razor ：</span><span class="sxs-lookup"><span data-stu-id="87154-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="87154-187">系統會自動保護[ Razor 頁面](xref:razor-pages/index)的 XSRF/CSRF。</span><span class="sxs-lookup"><span data-stu-id="87154-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="87154-188">如需詳細資訊，請參閱 [XSRF/CSRF 和 Razor Pages](xref:razor-pages/index#xsrf)。</span><span class="sxs-lookup"><span data-stu-id="87154-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="87154-189">防禦 CSRF 攻擊最常見的方法是使用 (STP) 的 *同步器權杖模式* 。</span><span class="sxs-lookup"><span data-stu-id="87154-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="87154-190">當使用者要求具有表單資料的頁面時，會使用 STP：</span><span class="sxs-lookup"><span data-stu-id="87154-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="87154-191">伺服器會將與目前使用者的身分識別相關聯的權杖傳送給用戶端。</span><span class="sxs-lookup"><span data-stu-id="87154-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="87154-192">用戶端會將權杖傳送回伺服器進行驗證。</span><span class="sxs-lookup"><span data-stu-id="87154-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="87154-193">如果伺服器收到的權杖不符合已驗證使用者的身分識別，則會拒絕該要求。</span><span class="sxs-lookup"><span data-stu-id="87154-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="87154-194">權杖是唯一且無法預期的。</span><span class="sxs-lookup"><span data-stu-id="87154-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="87154-195">此權杖也可以用來確保一連串要求的正確排序 (例如，確定要求順序：頁面 1 > 第2頁 > 第3頁) 。</span><span class="sxs-lookup"><span data-stu-id="87154-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="87154-196">ASP.NET Core MVC 和 Pages 範本中的所有表單都會 Razor 產生 antiforgery 權杖。</span><span class="sxs-lookup"><span data-stu-id="87154-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="87154-197">下列對等的 view 範例會產生 antiforgery 權杖：</span><span class="sxs-lookup"><span data-stu-id="87154-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="87154-198">明確地將 antiforgery token 新增至專案，而不使用標籤協助程式搭配 `<form>` HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) ：</span><span class="sxs-lookup"><span data-stu-id="87154-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="87154-199">在上述每個案例中，ASP.NET Core 加入隱藏的表單欄位，如下所示：</span><span class="sxs-lookup"><span data-stu-id="87154-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="87154-200">ASP.NET Core 包含使用 antiforgery 權杖的三個 [篩選](xref:mvc/controllers/filters) 條件：</span><span class="sxs-lookup"><span data-stu-id="87154-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="87154-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="87154-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="87154-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="87154-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="87154-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="87154-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="87154-204">Antiforgery 選項</span><span class="sxs-lookup"><span data-stu-id="87154-204">Antiforgery options</span></span>

<span data-ttu-id="87154-205">自訂中的 [antiforgery 選項](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="87154-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="87154-206">&dagger;`Cookie`使用[ Cookie Builder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder)類別的屬性來設定 antiforgery 屬性。</span><span class="sxs-lookup"><span data-stu-id="87154-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="87154-207">選項</span><span class="sxs-lookup"><span data-stu-id="87154-207">Option</span></span> | <span data-ttu-id="87154-208">描述</span><span class="sxs-lookup"><span data-stu-id="87154-208">Description</span></span> |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="87154-209">決定用來建立 antiforgery 的設定 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-209">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="87154-210">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="87154-210">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="87154-211">Antiforgery 系統用來在 views 中轉譯 antiforgery token 之隱藏表單欄位的名稱。</span><span class="sxs-lookup"><span data-stu-id="87154-211">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="87154-212">HeaderName</span><span class="sxs-lookup"><span data-stu-id="87154-212">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="87154-213">Antiforgery 系統所使用的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="87154-213">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="87154-214">如果 `null` 為，則系統只會考慮表單資料。</span><span class="sxs-lookup"><span data-stu-id="87154-214">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="87154-215">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="87154-215">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="87154-216">指定是否隱藏標頭的產生 `X-Frame-Options` 。</span><span class="sxs-lookup"><span data-stu-id="87154-216">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="87154-217">依預設，會產生值為 "SAMEORIGIN" 的標頭。</span><span class="sxs-lookup"><span data-stu-id="87154-217">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="87154-218">預設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="87154-218">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="87154-219">選項</span><span class="sxs-lookup"><span data-stu-id="87154-219">Option</span></span> | <span data-ttu-id="87154-220">描述</span><span class="sxs-lookup"><span data-stu-id="87154-220">Description</span></span> |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="87154-221">決定用來建立 antiforgery 的設定 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-221">Determines the settings used to create the antiforgery cookies.</span></span> |
| <span data-ttu-id="87154-222">[Cookie網域](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain)</span><span class="sxs-lookup"><span data-stu-id="87154-222">[CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain)</span></span> | <span data-ttu-id="87154-223">的網域 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-223">The domain of the cookie.</span></span> <span data-ttu-id="87154-224">預設為 `null`。</span><span class="sxs-lookup"><span data-stu-id="87154-224">Defaults to `null`.</span></span> <span data-ttu-id="87154-225">這個屬性已過時，將在未來的版本中移除。</span><span class="sxs-lookup"><span data-stu-id="87154-225">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="87154-226">建議的替代方式是 Cookie 。域。</span><span class="sxs-lookup"><span data-stu-id="87154-226">The recommended alternative is Cookie.Domain.</span></span> |
| <span data-ttu-id="87154-227">[Cookie名字](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename)</span><span class="sxs-lookup"><span data-stu-id="87154-227">[CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename)</span></span> | <span data-ttu-id="87154-228">cookie 的名稱。</span><span class="sxs-lookup"><span data-stu-id="87154-228">The name of the cookie.</span></span> <span data-ttu-id="87154-229">如果未設定，系統會產生以 [預設 Cookie 前置](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) 詞 ( "開頭的唯一名稱。AspNetCore. Antiforgery. ") 。</span><span class="sxs-lookup"><span data-stu-id="87154-229">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="87154-230">這個屬性已過時，將在未來的版本中移除。</span><span class="sxs-lookup"><span data-stu-id="87154-230">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="87154-231">建議的替代方式是 Cookie 。名字。</span><span class="sxs-lookup"><span data-stu-id="87154-231">The recommended alternative is Cookie.Name.</span></span> |
| <span data-ttu-id="87154-232">[Cookie路徑](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath)</span><span class="sxs-lookup"><span data-stu-id="87154-232">[CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath)</span></span> | <span data-ttu-id="87154-233">在上設定的路徑 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-233">The path set on the cookie.</span></span> <span data-ttu-id="87154-234">這個屬性已過時，將在未來的版本中移除。</span><span class="sxs-lookup"><span data-stu-id="87154-234">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="87154-235">建議的替代方式是 Cookie 。路徑。</span><span class="sxs-lookup"><span data-stu-id="87154-235">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="87154-236">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="87154-236">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="87154-237">Antiforgery 系統用來在 views 中轉譯 antiforgery token 之隱藏表單欄位的名稱。</span><span class="sxs-lookup"><span data-stu-id="87154-237">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="87154-238">HeaderName</span><span class="sxs-lookup"><span data-stu-id="87154-238">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="87154-239">Antiforgery 系統所使用的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="87154-239">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="87154-240">如果 `null` 為，則系統只會考慮表單資料。</span><span class="sxs-lookup"><span data-stu-id="87154-240">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="87154-241">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="87154-241">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="87154-242">指定 antiforgery 系統是否需要 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="87154-242">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="87154-243">如果 `true` 為，則非 HTTPS 要求會失敗。</span><span class="sxs-lookup"><span data-stu-id="87154-243">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="87154-244">預設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="87154-244">Defaults to `false`.</span></span> <span data-ttu-id="87154-245">這個屬性已過時，將在未來的版本中移除。</span><span class="sxs-lookup"><span data-stu-id="87154-245">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="87154-246">建議的替代方式是設定 Cookie 。SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="87154-246">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="87154-247">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="87154-247">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="87154-248">指定是否隱藏標頭的產生 `X-Frame-Options` 。</span><span class="sxs-lookup"><span data-stu-id="87154-248">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="87154-249">依預設，會產生值為 "SAMEORIGIN" 的標頭。</span><span class="sxs-lookup"><span data-stu-id="87154-249">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="87154-250">預設為 `false`。</span><span class="sxs-lookup"><span data-stu-id="87154-250">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="87154-251">如需詳細資訊，請參閱[ Cookie AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)。</span><span class="sxs-lookup"><span data-stu-id="87154-251">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="87154-252">使用 IAntiforgery 設定 antiforgery 功能</span><span class="sxs-lookup"><span data-stu-id="87154-252">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="87154-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 會提供 API 來設定 antiforgery 功能。</span><span class="sxs-lookup"><span data-stu-id="87154-253">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="87154-254">`IAntiforgery` 可以在類別的方法中要求 `Configure` `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="87154-254">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="87154-255">下列範例會從應用程式的首頁使用中介軟體來產生 antiforgery 權杖，並 cookie 使用本主題稍後所述的預設角度命名慣例，以 (的方式將它傳送至回應中) ：</span><span class="sxs-lookup"><span data-stu-id="87154-255">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="87154-256">需要 antiforgery 驗證</span><span class="sxs-lookup"><span data-stu-id="87154-256">Require antiforgery validation</span></span>

<span data-ttu-id="87154-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) 是可套用至個別動作、控制器或全域的動作篩選準則。</span><span class="sxs-lookup"><span data-stu-id="87154-257">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="87154-258">除非要求包含有效的 antiforgery token，否則會封鎖對套用此篩選的動作提出的要求。</span><span class="sxs-lookup"><span data-stu-id="87154-258">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="87154-259">`ValidateAntiForgeryToken`屬性需要權杖來要求它所標記的動作方法，包括 HTTP GET 要求。</span><span class="sxs-lookup"><span data-stu-id="87154-259">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="87154-260">如果屬性套用至 `ValidateAntiForgeryToken` 應用程式的控制器，則可以使用屬性來覆寫它 `IgnoreAntiforgeryToken` 。</span><span class="sxs-lookup"><span data-stu-id="87154-260">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="87154-261">ASP.NET Core 不支援新增 antiforgery 權杖來自動取得要求。</span><span class="sxs-lookup"><span data-stu-id="87154-261">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="87154-262">只自動驗證不安全 HTTP 方法的 antiforgery 權杖</span><span class="sxs-lookup"><span data-stu-id="87154-262">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="87154-263">ASP.NET Core 的應用程式不會產生安全 HTTP 方法的 antiforgery 權杖， (GET、HEAD、OPTIONS 和 TRACE) 。</span><span class="sxs-lookup"><span data-stu-id="87154-263">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="87154-264">您 `ValidateAntiForgeryToken` `IgnoreAntiforgeryToken` 可以使用 [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 屬性，而不是廣泛地套用屬性，然後以屬性覆寫它。</span><span class="sxs-lookup"><span data-stu-id="87154-264">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="87154-265">這個屬性與屬性的運作方式完全相同 `ValidateAntiForgeryToken` ，不同之處在于，它不需要權杖來處理使用下列 HTTP 方法所提出的要求：</span><span class="sxs-lookup"><span data-stu-id="87154-265">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="87154-266">GET</span><span class="sxs-lookup"><span data-stu-id="87154-266">GET</span></span>
* <span data-ttu-id="87154-267">HEAD</span><span class="sxs-lookup"><span data-stu-id="87154-267">HEAD</span></span>
* <span data-ttu-id="87154-268">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="87154-268">OPTIONS</span></span>
* <span data-ttu-id="87154-269">TRACE</span><span class="sxs-lookup"><span data-stu-id="87154-269">TRACE</span></span>

<span data-ttu-id="87154-270">我們建議您 `AutoValidateAntiforgeryToken` 廣泛使用非 API 案例。</span><span class="sxs-lookup"><span data-stu-id="87154-270">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="87154-271">這可確保預設會保護張貼動作。</span><span class="sxs-lookup"><span data-stu-id="87154-271">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="87154-272">替代方式是除非套用至個別的動作方法，否則預設會忽略 antiforgery 權杖 `ValidateAntiForgeryToken` 。</span><span class="sxs-lookup"><span data-stu-id="87154-272">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="87154-273">在此案例中，更有可能會將 POST 動作方法保持不受保護，讓應用程式容易遭受 CSRF 攻擊。</span><span class="sxs-lookup"><span data-stu-id="87154-273">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="87154-274">所有文章都應該傳送 antiforgery token。</span><span class="sxs-lookup"><span data-stu-id="87154-274">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="87154-275">Api 沒有自動傳送權杖的非部分的機制 cookie 。</span><span class="sxs-lookup"><span data-stu-id="87154-275">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="87154-276">執行可能取決於用戶端程式代碼的執行。</span><span class="sxs-lookup"><span data-stu-id="87154-276">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="87154-277">以下顯示一些範例：</span><span class="sxs-lookup"><span data-stu-id="87154-277">Some examples are shown below:</span></span>

<span data-ttu-id="87154-278">類別層級範例：</span><span class="sxs-lookup"><span data-stu-id="87154-278">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="87154-279">全域範例：</span><span class="sxs-lookup"><span data-stu-id="87154-279">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87154-280">服務。>addmvc (選項 => 選項。篩選。加入 (new AutoValidateAntiforgeryTokenAttribute ( # A3 # A4 # A5;</span><span class="sxs-lookup"><span data-stu-id="87154-280">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="87154-281">覆寫全域或控制器 antiforgery 屬性</span><span class="sxs-lookup"><span data-stu-id="87154-281">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="87154-282">[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)篩選準則可用來消除指定動作 (或控制器) 的 antiforgery token 需求。</span><span class="sxs-lookup"><span data-stu-id="87154-282">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="87154-283">套用時，此篩選準則會覆寫 `ValidateAntiForgeryToken` `AutoValidateAntiforgeryToken` 在較高層級指定， (全域或控制器) 上指定。</span><span class="sxs-lookup"><span data-stu-id="87154-283">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="87154-284">驗證後重新整理權杖</span><span class="sxs-lookup"><span data-stu-id="87154-284">Refresh tokens after authentication</span></span>

<span data-ttu-id="87154-285">藉由將使用者重新導向至 [view] 或 [Pages] 頁面，即可在使用者通過驗證之後重新整理權杖 Razor 。</span><span class="sxs-lookup"><span data-stu-id="87154-285">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="87154-286">JavaScript、AJAX 和 Spa</span><span class="sxs-lookup"><span data-stu-id="87154-286">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="87154-287">在傳統的 HTML 應用程式中，antiforgery 權杖會使用隱藏的表單欄位傳遞到伺服器。</span><span class="sxs-lookup"><span data-stu-id="87154-287">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="87154-288">在新式以 JavaScript 為基礎的應用程式和 Spa 中，許多要求都會以程式設計的方式進行。</span><span class="sxs-lookup"><span data-stu-id="87154-288">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="87154-289">這些 AJAX 要求可能會使用其他技術 (例如要求標頭或 cookie s) 傳送權杖。</span><span class="sxs-lookup"><span data-stu-id="87154-289">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="87154-290">如果 cookie 使用來儲存驗證權杖，以及驗證服務器上的 API 要求，CSRF 就是潛在的問題。</span><span class="sxs-lookup"><span data-stu-id="87154-290">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="87154-291">如果使用本機儲存體來儲存權杖，可能會緩和 CSRF 弱點，因為來自本機儲存體的值不會在每次要求時自動傳送至伺服器。</span><span class="sxs-lookup"><span data-stu-id="87154-291">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="87154-292">因此，使用本機儲存體將 antiforgery token 儲存在用戶端上，並以要求標頭的形式傳送權杖是建議的方法。</span><span class="sxs-lookup"><span data-stu-id="87154-292">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="87154-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="87154-293">JavaScript</span></span>

<span data-ttu-id="87154-294">使用 JavaScript 搭配 views，即可使用視圖內的服務來建立權杖。</span><span class="sxs-lookup"><span data-stu-id="87154-294">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="87154-295">將 [AspNetCore. Antiforgery. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 服務插入至 view 並呼叫 [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)：</span><span class="sxs-lookup"><span data-stu-id="87154-295">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="87154-296">這種方法不需要直接處理 cookie 來自伺服器的設定，或從用戶端讀取。</span><span class="sxs-lookup"><span data-stu-id="87154-296">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="87154-297">上述範例使用 JavaScript 來讀取 AJAX POST 標頭的隱藏欄位值。</span><span class="sxs-lookup"><span data-stu-id="87154-297">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="87154-298">JavaScript 也可以存取中的權杖 cookie ，並使用的 cookie 內容來建立具有權杖值的標頭。</span><span class="sxs-lookup"><span data-stu-id="87154-298">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="87154-299">假設腳本要求在名為的標頭中傳送權杖 `X-CSRF-TOKEN` ，請設定 antiforgery 服務以尋找 `X-CSRF-TOKEN` 標頭：</span><span class="sxs-lookup"><span data-stu-id="87154-299">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="87154-300">下列範例使用 JavaScript，以適當的標頭提出 AJAX 要求：</span><span class="sxs-lookup"><span data-stu-id="87154-300">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="87154-301">AngularJS</span><span class="sxs-lookup"><span data-stu-id="87154-301">AngularJS</span></span>

<span data-ttu-id="87154-302">AngularJS 使用慣例來處理 CSRF。</span><span class="sxs-lookup"><span data-stu-id="87154-302">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="87154-303">如果伺服器以名稱傳送 cookie `XSRF-TOKEN` ，AngularJS `$http` 服務 cookie 會在將要求傳送至伺服器時，將值加入至標頭。</span><span class="sxs-lookup"><span data-stu-id="87154-303">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="87154-304">此程式為自動。</span><span class="sxs-lookup"><span data-stu-id="87154-304">This process is automatic.</span></span> <span data-ttu-id="87154-305">不需要在用戶端明確設定標頭。</span><span class="sxs-lookup"><span data-stu-id="87154-305">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="87154-306">標頭名稱為 `X-XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="87154-306">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="87154-307">伺服器應該偵測到此標頭，並驗證其內容。</span><span class="sxs-lookup"><span data-stu-id="87154-307">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="87154-308">ASP.NET Core API 在應用程式啟動時使用此慣例：</span><span class="sxs-lookup"><span data-stu-id="87154-308">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="87154-309">設定您的應用程式，以在呼叫的中提供權杖 cookie `XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="87154-309">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="87154-310">設定 antiforgery 服務以尋找名為的標頭 `X-XSRF-TOKEN` 。</span><span class="sxs-lookup"><span data-stu-id="87154-310">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="87154-311">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="87154-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="87154-312">擴充 antiforgery</span><span class="sxs-lookup"><span data-stu-id="87154-312">Extend antiforgery</span></span>

<span data-ttu-id="87154-313">[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)型別可讓開發人員藉由在每個權杖中來回往返額外資料，來擴充反 CSRF 系統的行為。</span><span class="sxs-lookup"><span data-stu-id="87154-313">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="87154-314">每次產生欄位 token 時都會呼叫 [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) 方法，並將傳回值內嵌在產生的權杖內。</span><span class="sxs-lookup"><span data-stu-id="87154-314">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="87154-315">實施者可以傳回時間戳記、nonce 或任何其他值，然後在驗證權杖時呼叫 [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) 來驗證此資料。</span><span class="sxs-lookup"><span data-stu-id="87154-315">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="87154-316">用戶端的使用者名稱已內嵌在產生的權杖中，因此不需要包含這項資訊。</span><span class="sxs-lookup"><span data-stu-id="87154-316">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="87154-317">如果權杖包含補充資料但未 `IAntiForgeryAdditionalDataProvider` 設定，則不會驗證補充資料。</span><span class="sxs-lookup"><span data-stu-id="87154-317">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87154-318">其他資源</span><span class="sxs-lookup"><span data-stu-id="87154-318">Additional resources</span></span>

* <span data-ttu-id="87154-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 開啟的 [Web 應用程式安全性專案](https://www.owasp.org/index.php/Main_Page) (OWASP) 。</span><span class="sxs-lookup"><span data-stu-id="87154-319">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
